# API для Yatube

## Описание

**Yatube** — социальная сеть для публикации личных дневников. Этот проект — REST API для неё: через него можно получать, создавать, редактировать и удалять посты, оставлять комментарии, просматривать сообщества (группы) и подписываться на других авторов.

API позволяет подключить к Yatube любой клиент: мобильное приложение, фронтенд на JavaScript, бота и т. д.

Основные возможности:

- просмотр постов, групп и комментариев доступен всем, в том числе анонимным пользователям;
- создавать посты и комментарии могут только аутентифицированные пользователи;
- изменять и удалять контент может только его автор;
- подписки (`/follow/`) доступны только аутентифицированным пользователям, подписаться на самого себя или дважды на одного автора нельзя;
- аутентификация по JWT-токенам.

### Технологии

- Python 3.10
- Django 3.2
- Django REST Framework 3.12
- Simple JWT

## Установка

Клонировать репозиторий и перейти в него в командной строке:

```bash
git clone git@github.com:TolikKnv/api-final-yatube-ad.git
cd api-final-yatube-ad
```

Создать и активировать виртуальное окружение:

```bash
python -m venv venv
```

- Windows:

  ```bash
  source venv/Scripts/activate
  ```

- Linux / macOS:

  ```bash
  source venv/bin/activate
  ```

Установить зависимости из файла `requirements.txt`:

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Выполнить миграции:

```bash
cd yatube_api
python manage.py migrate
```

Создать суперпользователя (пользователи через API не регистрируются):

```bash
python manage.py createsuperuser
```

Запустить проект:

```bash
python manage.py runserver
```

Полная документация API будет доступна по адресу <http://127.0.0.1:8000/redoc/>.

## Примеры запросов

### Получение JWT-токена

`POST /api/v1/jwt/create/`

```json
{
    "username": "string",
    "password": "string"
}
```

Ответ:

```json
{
    "refresh": "string",
    "access": "string"
}
```

Полученный `access`-токен передаётся в заголовке каждого запроса:

```text
Authorization: Bearer <access-токен>
```

### Получение списка постов

`GET /api/v1/posts/`

Поддерживается пагинация через параметры `limit` и `offset`, например `GET /api/v1/posts/?limit=10&offset=0`.

```json
{
    "count": 123,
    "next": "http://127.0.0.1:8000/api/v1/posts/?offset=20&limit=10",
    "previous": "http://127.0.0.1:8000/api/v1/posts/?offset=0&limit=10",
    "results": [
        {
            "id": 0,
            "author": "string",
            "text": "string",
            "pub_date": "2021-10-14T20:41:29.648Z",
            "image": "string",
            "group": 0
        }
    ]
}
```

### Создание поста

`POST /api/v1/posts/`

```json
{
    "text": "string",
    "image": "string",
    "group": 0
}
```

### Добавление комментария к посту

`POST /api/v1/posts/{post_id}/comments/`

```json
{
    "text": "string"
}
```

Ответ:

```json
{
    "id": 0,
    "author": "string",
    "text": "string",
    "created": "2019-08-24T14:15:22Z",
    "post": 0
}
```

### Подписка на автора

`POST /api/v1/follow/`

```json
{
    "following": "string"
}
```

Ответ:

```json
{
    "user": "string",
    "following": "string"
}
```

Получить свои подписки можно запросом `GET /api/v1/follow/`. Поиск по подпискам: `GET /api/v1/follow/?search=username`.

## Автор

[TolikKnv](https://github.com/TolikKnv)
