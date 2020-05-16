---
layout: post
title: Status field in JSON responses
tags: [Scala, Rest, Circe]
---

Sometimes I see pattern in REST JSON API responses with status field:

|field|type|description|
|:-----|:-----|:-----|
|**success**|boolean|True or false. **If false then only the two last parameters will be appended.** Default value: true|
|userId|string|Created user id. For example: "00000000-0000-0000-0000-000000000000"|
|errCode|number|**If success false**: Custom error code. For example: 210003|
|errMsg|string|**If success false**: Custom error message. For example: "Transaction failed"|

And such `success` field appears in all responses, forming what will be in response.
Let's implement this pattern in Scala.

## Step one: modeling response

First of all, lets think about responses. They all share the same status field. Error should have `false` and successful responses be `true`:
```scala
trait Response {
  val success: Boolean
}

trait ErrorMixin extends Response {
  val success: Boolean = false 
}

trait SuccessMixin extends Response {
  val success: Boolean = true 
}
```

If we return error, we do not need to write other fields except `errCode` and `errMsg`.
And by other side, no need to pass nulls on success in `errCode` and `errMsg`.
Such two case classes will look like this, without our status code.

```scala
case class Error(
  errCode: Int,
  errMsg: String
) extends ErrorMixin

case class UserIdResponse(
  userId: UUID
) extends SuccessMixin
```

Status code will be mapped in type by `Either`.
Since our app can have lots of endpoints and we want to have consistent error responses, I'll add type to show this explicitly:

```scala
type Result[S <: SuccessMixin] = Either[Error, S]

def findUser(req: RequestDto): Result[UserIdResponse] = ???
```

## Step two: encode it to JSON

I'll use [Circe](https://circe.github.io/circe/) lib to show how to encode these DTO's, but it should not be hard to adopt it for [PlayJson](http://kailuowang.blogspot.com/2013/11/addremove-fields-to-plays-default-case.html) or other lib.

Circe will give us `Either` encoder implicitly, so we need to add only status field beforehand:

```scala
def withSuccess[R <: Response](enc: Encoder[R]): Encoder[R] = (r: R) =>
  enc.apply(r).mapObject(_.+:("success", r.success.asJson))

import io.circe._, io.circe.syntax._

implicit val userCreatedEncoder: Encoder[UserIdResponse] =
  withSuccess(deriveEncoder[UserIdResponse])

implicit val errorEncoder: Encoder[Error] =
  withSuccess(deriveEncoder[Error])
```

And that's all! Status field will be always on it's place.
Now it is easy to map status codes for left part depending on error happened.
