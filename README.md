# API YaMDb
***
<details>
    <summary style="font-size: 16pt; font-weight: bold">Описание</summary>

Проект YaMDb собирает отзывы пользователей на произведения. Произведения делятся на категории: «Книги», «Фильмы», «Музыка». Список категорий может быть расширен администратором.
Сами произведения в YaMDb не хранятся, здесь нельзя посмотреть фильм или послушать музыку.
Произведению может быть присвоен жанр из списка предустановленных. Новые жанры может создавать только администратор.
Благодарные или возмущённые пользователи оставляют к произведениям текстовые отзывы и ставят произведению оценку. Из пользовательских оценок формируется рейтинг.
</details>

***
<details>
    <summary style="font-size: 16pt; font-weight: bold">Технологии</summary>

* Python 3.8.9
* Django 2.2.16
* djangorestframework 3.12.4

С полным списком технологий можно ознакомиться в файле requirements.txt
</details>

***
<details>
     <summary style="font-size: 16pt; font-weight: bold">Документация</summary>

С документацией проекта можно ознакомиться по [ссылке](http://127.0.0.1:8000/redoc/) после запуска проекта.
</details>

***
<details>
    <summary style="font-size: 16pt; font-weight: bold">Запуск проекта</summary>

Клонировать репозиторий и перейти в него в командной строке:

```
git clone https://github.com/GhoulNEC/api_yambd.git
```

```
cd api_yamdb
```

Создать и активировать виртуальное окружение:

```
python3 -m venv venv
```

```
source venv/bin/activate
```

```
python3 -m pip install --upgrade pip
```

Установить зависимости из файла requirements.txt:

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
</details>

***
<details>
    <summary style="font-size: 16pt; font-weight: bold">Пример получения API</summary>

В API YaMDb существует несколько уровней доступа в зависимости от присвоенной пользовательской роли.

### Неавторизованный пользователь
Неавторизированным пользователям доступен ограниченный функционал сервиса
Yamdb. Клиент может получить только разрешенные запросы такие, как GET, HEAD и OPTIONS.

#### Регистрация нового пользователя

Получить код подтверждения на переданный `email`.
Использовать имя 'me' В качестве `username` запрещено.
Поля `email` и `username` должны быть уникальными.

`POST api/v1/auth/signup/`

```json
{
  "email": "string",
  "username": "string"
}
```

`POST api/v1/auth/token/` - Получение JWT-токена в обмен на username и confirmation code.

```json
{
  "username": "string",
  "confirmation_code": "string"
}
```

#### Управление API

`GET api/v1/categories/` - Получение списка всех категорий. 

`GET api/v1/titles/` - Получение списка всех произведений. 
При указании параметров limit и offset выдача должна работать 
с пагинацией

```json
[
    {
        "count": 0,
        "next": "string",
        "previous": "string",
        "results": [
            {
                "id": 0,
                "name": "string",
                "year": 0,
                "rating": 0,
                "description": "string",
                "genre": [
                  {
                    "name": "string",
                    "slug": "string"
                  }
                ],
                "category": {
                    "name": "string",
                    "slug": "string"
                }
            }
        ]
    }
]
```

`GET api/v1/titles/{title_id}/` - Получение произведения по id

`GET api/v1/titles/{title_id}/reviews/` - Получение списка всех отзывов произведения.

`GET api/v1/titles/{title_id}/reviews/{reviews_id}/` - Получение отзыва по id для указанного произведения.

`GET api/v1/titles/{title_id}/reviews/{reviews_id}/comments/` - Получение списка всех комментариев к отзыву по id. 

`GET api/v1/titles/{title_id}/reviews/{reviews_id}/comments/{comment_id}/` - Получение комментария для отзыва по id.

`GET api/v1/categories/` - Получение список категорий произведений. Так же для категорий
доступные только методы - GET, POST, DEL. Методы POST и DEL разрешены только администратору.

```json
[
    {
        "count": 0,
        "next": "string",
        "previous": "string",
        "results": [
            {
                "name": "string",
                "slug": "string"
            }
        ]
    }
]
```
`POST api/v1/genres/` - Добавление жанра. На жанры накладываются те же ограничения,
что и для категорий. GET - доступен всем.

```json
{
    "name": "string",
    "slug": "string"
}
```

### Авторизированный пользователь
Авторизированный пользователь может читать всё, как и неавторизированный, может публиковать отзывы и ставить оценки произведениям, может комментировать отзывы; 
может редактировать и удалять свои отзывы и комментарии, редактировать свои оценки произведений. 
Эта роль присваивается по умолчанию каждому новому пользователю.

`POST api/v1/titles/{title_id}/reviews/` - Добавление нового отзыва. Пользователь может оставить только один отзыв на произведение.

```json
{
  "text": "string",
  "score": 1
}
```

`PATCH api/vi/titles/{title_id}/reviews/{review_id}/` - Частичное обновление отзыва. 
Права доступа: Автор комментария, модератор или администратор.

```json
{
  "text": "string",
  "score": 1
}
```

`DELETE api/vi/titles/{title_id}/reviews/{review_id}` - Удаление отзыва. 
Права доступа: Автор комментария, модератор или администратор.

`POST api/v1/titles/{title_id}/reviews/{reviews_id}/comments/` - Добавление комментария к отзыву.

```json
{
  "text": "string"
}
```

`PATCH api/v1/titles/{title_id}/reviews/{review_id}/comments/{comment_id}/` - Частичное обновление комментария.
Права доступа: Автор комментария, модератор или администратор.

```json
{
  "text": "string"
}
```

`DELETE api/v1/titles/{title_id}/reviews/{review_id}/comments/{comment_id}/` - Удаление комментария к отзыву по id.
Права доступа: Автор комментария, модератор или администратор.

`GET api/v1/users/me/` - Получение данных своей учетной записи.

`PATCH api/v1/users/me/` - Изменение данных своей учетной записи.
Поля `email` и `username` должны быть уникальными.

```json
{
  "username": "string",
  "email": "user@example.com",
  "first_name": "string",
  "last_name": "string",
  "bio": "string"
}
```

### Администратор

`POST api/v1/categories/` - Добавление новой категории.

Поле `slug` для каждой категории должно быть уникальным.

```json
{
  "name": "string",
  "slug": "string"
}
```

`DELETE api/v1/categories/{slig}/` - Удаление категории.

`POST api/v1/genres/` - Добавление жанра.

Поле `slug` для каждого жанра должно быть уникальным.

```json
{
  "name": "string",
  "slug": "string"
}
```

`DELETE api/v1/genres/{slug}/` - Удаление жанра.

`POST api/v1/titles/` - Добавление нового произведения.

Нельзя добавлять произведения, которые еще не вышли (год выпуска не может быть больше текущего).
При добавлении нового произведения требуется указать уже существующие категорию и жанр.

```json
{
  "name": "string",
  "year": 0,
  "description": "string",
  "genre": [
    "string"
  ],
  "category": "string"
}
```

`PATCH api/v1/titles/{title_id}/` - Частичное обновление информации о произведении.

```json
{
  "name": "string",
  "year": 0,
  "description": "string",
  "genre": [
    "string"
  ],
  "category": "string"
}
```

`DELETE api/v1/titles/{title_id}/` - удаление произведения.

#### Управление пользователями

`GET api/v1/users/` - Получение списка всех пользователей.

`GET api/v1/users/{username}/` - Получение пользователя по username.

`POST api/v1/users/` - Добавление нового пользователя.
Поля `email` и `username` должны быть уникальными.

```json
{
  "username": "string",
  "email": "user@example.com",
  "first_name": "string",
  "last_name": "string",
  "bio": "string",
  "role": "user"
}
```

`PATCH api/v1/users/{username}/` - Изменение данных пользователя по username.
Поля `email` и `username` должны быть уникальными.

```json
{
  "username": "string",
  "email": "user@example.com",
  "first_name": "string",
  "last_name": "string",
  "bio": "string",
  "role": "user"
}
```

`DELETE api/v1/users/{username}/` - Удаление пользователя по username.
</details>

***
<details>
    <summary style="font-size: 16pt; font-weight: bold">Management-command</summary>

Если у Вас есть чем наполнить базу данных, а именно у Вас имеется *.csv файл,
то management-command fill_db Вам облегчит жизнь. Для того чтобы
воспользоваться ею, необходимо прописать в командной строке вашего проекта следующее:
```
python manage.py fill_db -m [Model] -f [file]
```
Важно отметить, что название модели нужно вводить строго
с заглавной буквы. Так же для заполнения БД необходимо по следующем критериям:
1. Первым делом заполнить модель User;
2. Заполнить модели Category / Genre;
3. Заполнить модель Title;
4. Заполнить модель GenreTitle;
5. Последующие модели.
Если не учесть данную последовательность, то возникнет ошибка,
т.к. все модели с полями ForeignKey / ManyToManyField ожидают
экземпляр класса связующей ею моделью.

Просьба - учесть данный факт!

### Пример команды
```
python manage.py fill_db -m Category -f category
```
Для подробной информации используйте:
```
python manage.py fill_db -h
```
</details>

***
<details>
    <summary style="font-size: 16pt; font-weight: bold">Авторы</summary>

* [Вячеслав Наприенко](https://github.com/Hellon048)
* [Максим Игнатов](https://github.com/Maxon57)
* [Роман Евстафьев](https://github.com/GhoulNEC)
</details>

***
