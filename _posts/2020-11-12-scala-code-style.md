---
layout: post
title: Какой код стайл выбрать?
tags: [Scala, Codestyle]
published: false
---

Давайте раберемся, какой кодстайл выбрать, и какая логика того или иного решения.

https://gist.github.com/thom-nic/2c74ed4075569da0f80b
HasThisTypePatternTriedToSneakInSomeGenericOrParameterizedTypePatternMatchingStuffAnywhereVisitor: 97
AbstractAnnotationConfigDispatcherServletInitializer: 52
ClassFilterAwareUnionIntroductionAwareMethodMatcher: 51
AbstractInterruptibleBatchPreparedStatementSetter: 49
AbstractInterceptorDrivenBeanDefinitionDecorator: 48

https://www.youtube.com/watch?v=ZsHMHukIlJY&ab_channel=IstanbulTechTalks

Что бы иметь критерии выбора, нужно начать с идеи зачем, и чего мы хотим получить вконце.
Я часто работал с кодовой базой которой >10 лет, в основном на java. Чаще всего это были проекты в глбуоком легаси. Если я лез в гит историю, то меня интересовала история файла. Как, почему, и когда он менялся. А так же кто сделал изменение, и вдруг он еще работает в компании и помнит почему написал именно так.

# Длинна строки
Остановитесь на длинне 80 символов. Это кажется очень странной рекомендацией в 2020-м году, когда у каждого есть 27 дюймовый монитор с 4к матрицей. Зачем себя ограничивать?
Ответ состоит из нескольких частей.
1. Не все работают с отдельным монитором. У кого-то 13 дюймовый макбук. Кто-то(как я) увеличивают шрифты, что бы не щуриться. Я открываю пополоам од и юнит тест к файлу. Всё это приводит к тому, что удобно, когда у вас длинна строки <80 символов.

Как контр аргумент может быть Так есть же перенос строки во всех редакторах! Не во всех - БитБакет не дает вам такоой возможности. Ревьювить код в браезере становиться практически невозможным.

Есть еще довод в пользу короткой строки - Так легче читать. Это субъективно. По той же причине удобно читать газеты в колонках, и все современные сайты так же верстаются полоконочно. На просторах интернета говорится о не более 8 слов в строчке. Найти научное подтверждение этому мне не удалось.

```scala
// bad
case class CaseClassWithLongJavaStyleName(javaStyleParameterLongStringTitle: String, oneMoreJavaStyleLongParam: Int)

// better
case class CaseClassWithLongJavaStyleName(javaStyleParameterLongStringTitle: String,
                                          oneMoreJavaStyleLongParam: Int)
```

Теперь проведем эксперемент от Кэвлина Хея. Заменем все не пробелы квадратом, и попробуем понять где же находится информация:
```scala
#### ##### ################################################################# #######
                                          ########################## ####
```

Зачем нужно туда-сюда бегать глазами? мы еще и сбедаем драгоценнок место в начале строки. Поэтому писать будем так:

```scala
// so-so 
case class CaseClassWithLongJavaStyleName(javaStyleParameterLongStringTitle: String,
                                          oneMoreJavaStyleLongParam: Int)
//better
case class CaseClassWithLongJavaStyleName(
  javaStyleParameterLongStringTitle: String,
  oneMoreJavaStyleLongParam: Int)
```

Следующий момент вернет нас в прошлое к языку SQL. Представьте, что вы пишете тестовый запрос, и постоянно то удаляете то добавляете колонки в select:

```sql
select name,
       age,
       phone,
--     manager_id
from employees
where id = 1;
```
Из-за последней запятой, вы постоянно промахиваетесь с запросом. Вы возвращаетесь на третью строчку, убираете запятую, а когда раскомментируете строку 4, снова добавляете. DBA из-за этого придумали запись с запятой перед параметром:
```sql
select name
     , age
     , phone
--   , manager_id
from employees
where id = 1;
```
Всё, запятая теперь не страшна. Такой же код конвеншен принят в Хаскеле(код из XMonad):
```haskell
data XState = XState
    { windowset        :: !WindowSet                     -- ^ workspace list
    , mapped           :: !(S.Set Window)                -- ^ the Set of mapped windows
    , waitingUnmap     :: !(M.Map Window Int)            -- ^ the number of expected UnmapEvents
    , dragging         :: !(Maybe (Position -> Position -> X (), X ()))
    , numberlockMask   :: !KeyMask                       -- ^ The numlock modifier
    , extensibleState  :: !(M.Map String (Either String StateExtension))
    }
```
К такому конвеншену можно привыкнуть, хотя он и используется очень редко.
В джаве на этом можно было бы и остановиться. Но новые языки, как Скала или Котлин, показывают намного более изящную альтернативу:
```scala
//better
case class CaseClassWithLongJavaStyleName(
  javaStyleParameterLongStringTitle: String,
  oneMoreJavaStyleLongParam: Int,
)
```

Эта запятая успешно работает со Скалы 2.12 и Котлина Н. Теперь диффы в гите, как и добавление новых филдов значительно изящнее.

Имплиситы
#########

Добавлять будем так же, как и в предыдущем случаи:
```scala

case class A(
  name: String,
  age: Int,
) (implicit
  ec: ExucutionContext,
) {

  def smth(param: Int): Unit = {
    println("Woohoo!")
  }
}
```
