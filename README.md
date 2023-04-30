# api_final_yatube - REST API для проекта Yatube

YaTube API представляет собой проект социальной сети, в котором реализован функционал позволяющий:
* Подписываться на других авторов.
* Просматривать, создавать новые, удалять и изменять сообщения.
* Просматривать и создавать группы.
* Комментировать, смотреть, удалять и обновлять комментарии.
* Фильтровать выбор по полям.

## Описание проекта
API доступен только аутентифицированным пользователям. В проекте аутентификация осуществляется по токену TokenAuthentication (реализована аутентификация по JWT-токену).

Когда вы запустите проект, по адресу http://127.0.0.1:8000/redoc/ будет доступна документация для API Yatube. В документации описано как работает API. Документация представлена в формате Redoc.

В ответ на запросы POST, PUT и PATCH ваш API возвращает объект, который был добавлен или изменён.

Работа с моделью Post осуществляется через через ModelViewSet.

В проекте описана модель Follow, в ней два поля — user (кто подписан) и following (на кого подписан). Для этой модели в документации уже описан эндпоинт /follow/ и два метода:

* GET — возвращает все подписки пользователя, сделавшего запрос. Возможен поиск по подпискам по параметру search
* POST — подписать пользователя, сделавшего запрос на пользователя, переданного в теле запроса. При попытке подписаться на самого себя, пользователь получит информативное сообщение об ошибке. Проверка должна осуществляться на уровне API.

## Примеры работы с API для всех пользователей

Для неавторизованных пользователей работа с API доступна в режиме чтения, что-либо изменить или создать не получится.

```
GET api/v1/posts/ - получить список всех публикаций.
GET api/v1/posts/{id}/ - получение публикации по id
GET api/v1/groups/ - получение списка доступных сообществ
GET api/v1/groups/{id}/ - получение информации о сообществе по id
GET api/v1/{post_id}/comments/ - получение всех комментариев к публикации
GET api/v1/{post_id}/comments/{id}/ - Получение комментария к публикации по id
```
## Примеры работы с API для авторизованных пользователей

Для создания публикации используем:
```
POST /api/v1/posts/
```

Обновление публикации:
```
PUT /api/v1/posts/{id}/
```

Частичное обновление публикации:
```
PATCH /api/v1/posts/{id}/
```

Частичное обновление публикации:
```
DEL /api/v1/posts/{id}/
```

Получение доступа к эндпоинту /api/v1/follow/ (подписки) доступен только для авторизованных пользователей.
```
GET /api/v1/follow/ - подписка пользователя от имени которого сделан запрос
на пользователя переданного в теле запроса. Анонимные запросы запрещены.
```

* Авторизованные пользователи могут создавать посты, комментировать их и подписываться на других пользователей.
* Пользователи могут изменять или удалять контент, если они являются авторами этого контента.

### Добавить группу в проект нужно через админ панель Django:
```
admin/ - после авторизации, переходим в раздел Groups и создаем группы
```
Доступ авторизованным пользователем доступен по JWT-токену (Joser), который можно получить выполнив POST запрос по адресу:

POST /api/v1/jwt/create/
Передав в body данные пользователя (например в postman):
```
{
"username": "string",
"password": "string"
}
```
Полученный токен добавляем в headers (postman), после чего буду доступны все функции проекта:
```
Authorization: Bearer {your_token}
```
Обновить JWT-токен:
```
POST /api/v1/jwt/refresh/ - обновление JWT-токена
```
Проверить JWT-токен:
```
POST /api/v1/jwt/verify/ - проверка JWT-токена
```
Так же в проекте API реализована пагинация (LimitOffsetPagination):
```
GET /api/v1/posts/?limit=5&offset=0 - пагинация на 5 постов, начиная с первого
```

## Примеры запросов

Пример POST-запроса с токеном Льва Толстого: добавление нового поста. POST .../api/v1/posts/
```
{ "text": "Утром читал и писал немного. Вечером побольше, но все не только без увлечения, но с какою-то непреодолимой ленью" }
```
Пример ответа:
```
{ "id": 20, "text": "Утром читал и писал немного. Вечером побольше, но все не только без увлечения, но с какою-то непреодолимой ленью.", "author": "leo", "image": null, "group": 1, "pub_date": "2022-07-01T08:47:11.084589Z" }
```
Пример POST-запроса с токеном Льва Толстого: отправляем новый комментарий к посту с id=20. POST .../api/v1/posts/20/comments/
```
{ "text": "тестовый комментарий", }
```
Пример ответа:
```
{ "id": 3, "author": "leo", "post": 20, "text": "тест тест", "created": "2022-07-01T10:14:51.388932Z" }
```
Пример GET-запроса с токеном Льва Толстого: получаем информацию о группе. GET .../api/v1/groups/2/

Пример ответа:
```
{ "id": 2, "title": "История", "slug": "history", "description": "Посты на тему истории" }
```

## Как запустить проект:

Клонировать репозиторий и перейти в него в командной строке:
```
git clone git@github.com:EvKutyashov/api_final_yatube.git
```

Cоздать и активировать виртуальное окружение:
```
python3 -m venv env
```
```
source env/bin/activate
```

Установить зависимости из файла requirements.txt:
```
python3 -m pip install --upgrade pip
```
```
pip install -r requirements.txt
```
Выполнить миграции:
```
python3 manage.py migrate
```
Запустить проект:
```
python3 manage.py runserver
```


## Использованные технологии
* [Python](https://www.python.org/)
* [Django](https://www.djangoproject.com/)
* [Django REST framework](https://www.django-rest-framework.org/)
* [DRF Simple JWT](https://django-rest-framework-simplejwt.readthedocs.io/en/latest/)