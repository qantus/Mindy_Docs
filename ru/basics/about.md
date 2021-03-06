# История

В один прекрасный момент [мы](http://studio107.ru/) поняли, что разрабатывать "каждый новый проект" на новой, более подходящей для этого системе - плохо. Было принято решение перейти на какой либо framework. Выбор пал на yii (php) и django (python).

Достаточно долгое время мы вели разработку на Yii. Разработали первую версию системы с автоматическими формами, автоматической админ панелью и назвали ее Mindy. Это было не более чем надстройка над Yii. Но с каждым новым проектом и доработкой мы упирались в ограничения `active record` или в громоздкий синтаксис Criteria, или в ужасно неудобную реализацию `layout` и `view` и невозможности полноценной интеграции сторонних шаблонизаторов, так как контроллер был насквозь пронизан кусками логики за которую должен отвечать шаблонизатор, а не контроллер.

> Название Mindy изначально предполагалось как Mind(yii) - умный (разумный) yii.

Изучили все плюсы и минусы этого прекрасного продукта и приняли решение о самостоятельном развитии его форка. У yii много как достоинств так и недостатков. Первой причиной форка была абсолютная монолитность Yii. С выходом Yii2 правильность нашего решения только подтвердилась.

# О проекте

Мы попытались создать нечто сочетающее в себе удобство разработки из мира django (не взирая на язык программирования python).

Проект представлял из себя форк yii в первоначальном виде.

## Абстракция работы с базами данных (ORM)

В дальнейшем были убраны `active record` и `query` из yii1. Добавлен `query` из yii2 и на его основе был создан [Orm](https://github.com/studio107/Mindy_Orm) по синтаксису напоминающий [Django orm](https://docs.djangoproject.com/en/1.7/#the-model-layer)

Сравните:

```python
Page.objects().filter(name__icontains="foo")
```

```php
Page::objects()->filter(['name__icontains' => 'foo']);
```

На текущий момент поддерживаются: mysql, postgresql, mssql, sqlite

Orm и Query теперь не зависят от Mindy / Yii. Можно использовать их для вашего проекта. Например с wordpress или drupal, или как вам больше нравится.

## Шаблонизатор

Были удалены стандартные для yii понятия как `layout` и `view`. На замену им пришел полноценный шаблонизатор [Flow](https://github.com/nramenta/flow) (который к слову является форком twig), который так же был форкнут и переписан для совместимости как с [django template engine](https://docs.djangoproject.com/en/1.7/topics/templates/) так и с [twig](http://twig.sensiolabs.org/)

Пример:

```twig
{% for i in data %}
    {{ loop.index }}
    {{ forloop.counter }}
{% endfor %}
```

Таким образом контроллер больше не отвечает за работу представлений как это было в Yii, ведь логично, это и не его обязанность вовсе.

## Система событий (Events)

На замену системе событий Yii пришла [Aura.PHP Signal](https://github.com/auraphp/Aura.Signal). События так же не зависят от самой Mindy.

## Роуты и обработка url (Routes)

Так как мы одновременно разрабатывали проекта на Yii и django, а особенно после django, роуты в yii выглядели как минимум странно. К примеру обработка `/`.

Чтобы заставить работать `wildcard` роуты необходимо было перекрыть стандартный роутер и добавить обработку %2f. Странно, согласитесь? Чтобы создать более менее гибкий роут, судя по документации yii нужно было определять свой класс который отвечал за обработку и построение роутов. К слову, в django мы не столкнулись с этим ни разу.

Мы использовали `fastroute` [phroute](https://github.com/mrjgreen/phroute).

Теперь построение роутов и их обработка выглядит так:

```php
$url->reverse('pages:view', $model->url);
// или
$url->reverse('pages:view', ['url' => $model->url]);
```

в шаблонизаторе:

```twig
{% url "pages:view" model.url %}
{# или #}
{% url "pages:view" ['url' => model.url] as pageUrl %}
{{ pageUrl }}
```
