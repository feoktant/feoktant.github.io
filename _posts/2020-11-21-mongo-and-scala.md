---
layout: post
title: ТФ и Монго - всё плохо
tags: [Scala, Mongo]
published: false
---

Пощупал Монго. Крайне печальная история со скаловым драйвером. Что у нас есть:

1. Официальный [скала драйвер](https://github.com/mongodb/mongo-scala-driver) как обертка вокруг [джава протокола](https://github.com/mongodb/mongo-java-driver/tree/master/bson-scala). В драйвер висят несколько пул риквестов, которые убирают бойлерплейт. Код очень завязан на джаву, и свою собственную имплементацию Observable.
2. [Reactivemongo](https://github.com/ReactiveMongo/ReactiveMongo). Живой, полностью скаловый драйвер. Возвращает скала Future. В качестве протокола - Netty.
3. [scodec-bson](https://gitlab.com/lJoublanc/scodec-bson). Абсолютно дохлая имплементация бсон на scodec. Артефакта нет в mvncentral. Несколько жирных todo в коде.
4. [circe-bson](https://github.com/circe/circe-bson). Конвертер бсон из ReactiveMongo в circe-json.

Итого, для Монги нет ничего под Тайплевел стек, или хотя бы с ТФ.

