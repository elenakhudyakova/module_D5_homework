# Проект News Portal. Итоговое задание модуля D2

## 0. Запуск shell
```
python manage.py shell
```

## 1. Создать двух пользователей (с помощью метода User.objects.create_user).
### импортируем модель

```
from NewsPaper.models import User

user1 = User.objects.create_user('Bill Totonto')
user2 = User.objects.create_user('Guy Richy')
```

## 2. Создать два объекта модели Author, связанные с пользователями.

### импортируем модель

```
from NewsPaper.models import Author

Author.objects.create(author = user1)
Author.objects.create(author = user2)
```

## 3. Добавить 4 категории в модель Category.

### импортируем модель

```
from NewsPaper.models import Category
Category.objects.create(article_category = 'sport') # (id=1)
Category.objects.create(article_category = 'politics')
Category.objects.create(article_category = 'education')
Category.objects.create(article_category = 'culture')# (id=4)
```

## 4. Добавить 2 статьи и 1 новость.

### импортируем модель

```
from NewsPaper.models import Post
author = Author.objects.get(id=1)

# <Post: Post object (1)> - т.е. этот пост имеет id=1
Post.objects.create(
post_author = author, 
category = 'A', 
title = 'The Record', 
content = 'Long text'
)

# <Post: Post object (2)>
Post.objects.create(
post_author = author, 
category = 'A', 
title = 'The Magic', 
content = 'Not very long story'
)

# <Post: Post object (3)>
      Post.objects.create(
post_author = author, 
category = 'N', 
title = 'Some news', 
content = 'We have launched a new project'
)
```

## 5. Присвоить им категории (как минимум в одной статье/новости должно быть не меньше 2 категорий).

### статье под id=1 присваиваем категорию 1 - ‘sports’

```
Post.objects.get(id=1).post_category.add(Category.objects.get(id=1))
```

### статье под id=1 присваиваем категорию 4 - 'culture'

```
Post.objects.get(id=1).post_category.add(Category.objects.get(id=4))
```

## 6. Создать как минимум 4 комментария к разным объектам модели Post (в каждом объекте должен быть как минимум один комментарий).

```
from NewsPaper.models import *
Comment.objects.create(
                      comment_post=Post.objects.get(id=1),
                      comment_user=Author.objects.get(id=1).author, 
                      feedback_text = 'Very interesting article'
                      )

Comment.objects.create(
                      comment_post=Post.objects.get(id=2), 
                      comment_user = Author.objects.get(id=1).author,
                      feedback_text = 'It is interesting'
                      )

Comment.objects.create(
                      comment_post = Post.objects.get(id=3), 
                      comment_user = Author.objects.get(id=2).author, feedback_text = "Wow, that's awesome"
                      )

Comment.objects.create(
                      comment_post=Post.objects.get(id=1), 
                      comment_user = Author.objects.get(id=2).author, feedback_text = 'It is okay'
                      )
```

## 7. Применяя функции like() и dislike() к статьям/новостям и комментариям, скорректировать рейтинги этих объектов.

```
Comment.objects.get(id=1).like()
Post.objects.get(id=1).dislike()
Post.objects.get(id=3).like()
```

### проверка рейтинга пользователя

```
Comment.objects.get(id=1).comment_rate
```

### проверка рейтинга поста

```
Post.objects.get(id=1).post_rate
```

## 8. Обновить рейтинги пользователей.

```
u1 = Author.objects.get(id=1)
u1.update_rating()
u1.user_rate

>>> u2 = Author.objects.get(id=2)
```

### чтобы посмотреть суммарный рейтинг комментариев пользователя, можно обратиться к нему напрямую

```
>>> u2.author.comment_set.aggregate(comment_rating=Sum('comment_rate'))
>>> u2.update_rating()
>>> u2.user_rate
```

## 9. Вывести username и рейтинг лучшего пользователя (применяя сортировку и возвращая поля первого объекта).

```
>>> s = Author.objects.order_by('user_rate')
>>> for i in s:
...     i.user_rate
...     i.author.username
1
'Guy Richy'
60
'Bill Totonto'
```

## 10. Вывести дату добавления, username автора, рейтинг, заголовок и превью лучшей статьи, основываясь на лайках/дислайках к этой статье.

```
>>> p = Post.objects.order_by('-post_rate')
for i in p[:1]:
...     i.date_created
...     i.post_author.author
...     i.post_rate
...     i.title
...     i.preview()
```

## 11. Вывести все комментарии (дата, пользователь, рейтинг, текст) к этой статье.

```
>>> Post.objects.all().order_by('-post_rate')[0].comment_set.values(
'comment_date_created', 
'comment_user', 
'comment_rate', 'feedback_text'
)
```

### В консоли выведутся комментарии:

```
<QuerySet [
{
'comment_date_created': datetime.date(2021, 5, 11), 
'comment_user': 3, 
'comment_rate': 1, 
'feedback_text': 'Very interesting article'
}, 

{
'comment_date_created': datetime.date(2021, 5, 11), 
'comment_user': 4, 
'comment_rate': 0, 
'feedback_text': 'It is okay'
}, 

{
'comment_date_created': datetime.date(2021, 5, 12), 
'comment_user': 3, 
'comment_rate': 8, 
'feedback_text': 'Very interesting article'
}, 

{
'comment_date_created': datetime.date(2021, 5, 12), 'comment_user': 4, 
'comment_rate': 8, 
'feedback_text': 'It is okay'
}
]
```

