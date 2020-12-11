---
layout: post
title: Критика Protobuf и кто же вместо него
tags: [Scala, Protobuf, protocols]
published: false
---

На днях попробовал реализовать [MySQL X Protocol](https://github.com/mysql/mysql-server/tree/8.0/plugin/x/protocol/protobuf). по идее должно быть очень легко сделать, ведь он полностью описан на proto2. Но нет - получите багу в [ScalaPB](https://github.com/scalapb/ScalaPB/issues/998), распишитесь.

Попутно снова смотрел в код который генерирует эта либа. Страх и ненависть. И всё равно неправильно. Для джавы есть [JavaPoet](https://github.com/square/javapoet), для Ktolin они же сделали [KotlinPoet](https://github.com/square/kotlinpoet). Где такой же поет для Скалы? Не хочу я в синтаксическом дереве разбираться. И [Scalameta](https://scalameta.org/) вообще ни разу не понятная(

Но это пол беды. Прочитал интересную статью, о критике протобафа: https://reasonablypolymorphic.com/blog/protos-are-wrong/. Прото на подъеме, а критика не сказать что беспочвенная. Короче, есть о чем разобраться. Хочу проверить всё что там сказано, и сформулировать своё мнение по этому поводу.

В комментах попробовал вычленить что есть на замену:

- [IdeaLingua RPC/DML](https://izumi.7mind.io/latest/release/doc/idealingua/index.html) от Изуми. Слышал, не использовал, ниразу не видел, что бы применялось где-то
-  [Microsoft Bond](http://microsoft.github.io/bond/) Передал в опенсорс пару лет назад. Статус непонятен. Убрали часть фукнционала в пользу gRPC
- [Amazon Ion](http://amzn.github.io/ion-docs/) вообще впервые слышу

Так что же вместо всего этого? А пока ничего. Автор обмолвился про рекурсивные схемы, о которых рассказывала Олли и Бартош. Скорее всего это и есть будущие.

[gRPC: Up and Running](https://www.oreilly.com/library/view/grpc-up-and/9781492058328/) - продолжаю читать.