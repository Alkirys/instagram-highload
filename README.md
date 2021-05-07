# instagram-highload
ДЗ-1 по курсу Highload. Проектирование веб-сервиса Instagram

### 1. Тема
Instagram - приложение для обмена фотографиями с элементами социальной сети

* функционал по публикации фото, их комментированию, просмотру ленты
* 70% аудитории моложе 30 лет
* наиболее популярен в США, России и Индии

### 2. Планируемая нагрузка

* аудитория - 1 млрд. человек ([источник](https://tass.ru/ekonomika/5309458))
* около 500 млн. пользователей ежедневно ([источник](https://lpgenerator.ru/blog/2019/02/28/instagram-v-cifrah-statistika-na-2019-god/))
* среднее время, проведенное в Instagram - 28 мин./день ([источник](https://www.likeni.ru/analytics/statistika-po-instagram-kotoruyu-nuzhno-znat-k-2020-godu/))

Обычно, пользователь заходит на сайт по несколько раз в день.
Зная, что среднее время, проведенное в Instagram - 28 мин./день, предположим,
что пользователь заходит 7 раз в день, проводя на сайте по 4 минуты.

Теперь оценим трафик среднестатистического пользователя за одну сессию.

За 4 минуты пользования сайтом(просмотр ленты), я сделал 315 запросов и загрузил 33.8 мб данных.
Их них:
* на динамику - 75 запросов и 995 кб (~ 0.97 мб)
* на картинки - 211 запросов и 28.5 мб
* на остальную статику - 29 запросов и 4.3 мб

Тогда за сутки среднестатистический пользователь:
* для динамики <br/>
  * загрузит ```7 * 0.97 = 6.8 мб``` данных <br/>
  * сделает ```7 * 75 = 525``` запросов

* для картинок <br/>
  * загрузит ```7 * 28.5 = 199.5 мб``` данных <br/>
  * сделает ```7 * 211 = 1477``` запросов

* для остальной статики <br/>
  * загрузит ```7 * 4.3 = 30.1 мб``` данных <br/>
  * сделает ```7 * 29 = 203``` запроса

Расcчитаем средний трафик:
- динамика:```(6.8 мб * 500 000 000) * (8/1024) / (24 * 60 * 60) = 307.44 Гбит/с```
- картинки: ```(199.5 мб * 500 000 000) * (8/1024) / (24 * 60 * 60) = 9019.64 Гбит/c```
- остальная статика:```(30.1 мб * 500 000 000) * (8/1024) / (24 * 60 * 60) = 1360.86 Гбит/c```

Расcчитаем средний RPS:
- динамика: ```(525 * 500 000 000) / (24 * 60 * 60) = 3 038 195 RPS```
- картинки: ```(1477 * 500 000 000) / (24 * 60 * 60) = 8 547 454 RPS```
- остальная статика: ```(203 * 500 000 000) / (24 * 60 * 60) = 1 174 769 RPS```

Также, помимо просмотра ленты, сервис предоставляет функционал создания постов и комментариев лайков и 
авторизации/регистрации, нагрузки на сервисы которых также необходимо подсчитать.

Согласно [источнику](https://www.likeni.ru/analytics/statistika-po-instagram-kotoruyu-nuzhno-znat-k-2020-godu/), 
прирост аудитории Instagram на 2019 год составил 7,3%. Это значит, что за год произойдет 73 000 000
запросов на регистрацию, т.е.<br>
```73 000 000 / (365 * 24 * 60 * 60) = 2.4 RPS```. <br>
Предположив, что один такой запрос передает ~400 байт информации, получим<br>
```(400 байт * 73 000 000) * (8/1024) / (365 * 24 * 60 * 60) = 7.2 Кбит/с```<br>
Так как аудитория проекта составляет 500 млн. человек и мы приняли, что в среднем каждый их них заходит на сайт по 4 раза в день,
получаем 2 000 000 000 запросов на авторизацию в день, т.е. примерно <br>
```2 000 000 000 / (24 * 60 * 60) = 23 148 RPS```<br>
Запрос на авторизацию в Instagram передает 154 байта по сети, тогда нагрузка составит около<br>
```(154 байт * 2 000 000 000) * (8/1024/1024) / (24 * 60 * 60) = 27 Мбит/с```<br>

Тогда общая нагрузка на сервис авторизации/регистрации будет:
```7.2 Кбит/с + 27 Мбит/с = 28 Мбит/с```<br>
```2.4 + 23 148 = 23 151 RPS```

Примем, что в день публикуется 100 млн. постов ([источник](https://lpgenerator.ru/blog/2019/02/28/instagram-v-cifrah-statistika-na-2019-god/)),
т.е. ~1158 постов в секунду.
А также, ежесекундно ставится 8500 лайков и 1000 комментариев ([источник](https://www.likeni.ru/analytics/25-faktov-ob-instagram-kotorye-dolzhen-znat-kazhdyy/))
Примем, что в каждом посте публикуется по 1 фото, тогда объем переданных данных равен ~4 мб. 
При лайке поста передается 75 байт, а при публикации комментария ~ 450 - 600 байт. Тогда, можем рассчитать нагрузку:
* на посты ```(4 мб * 1158) * (8/1024) = 36.2 Гбит/с```
* на комментарии ```(600 байт * 1000) * (8/1024/1024) = 4.6 Мбит/с```
* на лайки ```(75 байт * 1000) * (8/1024/1024) = 0.6 Мбит/с```

Итого:<br>
```1158 + 8500 + 1000 = 10 658 RPS```<br>
```36.2 Гбит/с + 4.6 Мбит/с + 0.6 Мбит/с = 37 Гбит/с```

### 3. Логическая схема БД

![Схема бд](https://github.com/Alkirys/instagram-highload/blob/main/images/BD_logic.png "Схема бд")

### 4. Физические системы хранения

Для хранения данных о пользователях, постах, комментариях и лайках, я выбрал
СУБД PostgreSQL, как наиболее функциональную и надежную базу данных, имеющую хорошую поддержку
и инструменты шардинга.

Шардинг таблиц пользователей, и комментариев может производиться по полю id. Для таблицы постов, шардинг по id не 
подойдет, так как такое решение может сильно замедлить запрос на получение пользователем ленты, в данном случае, имеет 
смысл шардить посты по геопозиции.

Активные сессии пользователей для обеспечения скорейшего доступа будут храниться в Redis.
Также, Redis имеет возможность master-slave репликацию и встроенное API для работы с
Memcached.

Все фотографии пользователей будут храниться в Amazon S3 хранилище, которое автоматически шардит данные.<br>

Примерный расчет объема фото:

Согласно источникам([1](https://tjournal.ru/flood/48865-instagram-thanksgiving), [2](https://ru.epicstars.com/full_history_of_instagam/)) 
при аудитории в 100 млн. пользователей, в Instagram было опубликовано 16 млрд. фото.
Рассчитаем примерное количество фото для аудитории в 1 млрд. человек<br>
```(1 млрд. * 16 млрд.) / 100 млн. = 160 млрд.```
<br>
Тогда, приняв средний размер фото равный 3 мб, для их хранения понадобится<br>
```3 * 1024 * 160000000000 / 1024^3 = 457 764 Гб```

Примерный расчет объема таблиц:

* Пользователи:<br>
Аудитория сервиса - 1 млрд. человек. Тогда одна запись в таблице займет:<br>
  ```4(user_id) + 128(username) + 128(password) + 128(email) + 128(avatar) + 4 * 150(subscribe) = 1116 байт```
  <br><br>
  А всего необходимо<br>
  ```1116 * 1000000000 / 1024^3  = 1040 Гб```<br><br>
  
* Посты:<br>
  Одна запись в таблице займет:<br>
  ```4(post_id) + 4(user_id) + 256(description) + 128(images_url) + 128(location) + 4(likes) = 524 байта```
  <br><br>
  Предположим, что каждый пост содержит одно фото, тогда всего таблица займет <br>
  ```524 * 160000000000 / 1024^3  = 78 083 Гб```<br><br>

* Комментарии:<br>
  Одна запись в таблице займет:<br>
  ```4(comment_id) + 4(user_id) + 4(post_id) + 256(message) + 8(time) = 276 байт```
  <br><br>
  Согласно [источнику](https://blog.cybermarketing.ru/vovlechennost-instagram/), медиана числа комментариев к посту равна 4, тогда таблица займет:<br>
  ```276 * 160000000000 * 4 / 1024^3  = 164 509 Гб```
  <br><br>

* Лайки:<br>
  Одна запись в таблице займет:<br>
  ```4(user_id) + 4(post_id) = 8 байт```
  <br><br>
  Согласно [источнику](https://blog.cybermarketing.ru/vovlechennost-instagram/), медиана количества лайков к посту равна 100, тогда таблица займет:<br>
  ```8 * 160000000000 * 100 / 1024^3  = 119 210 Гб```
  <br><br>
  
Таким образом, для хранения таблиц потребуется<br>
```1040 + 78083 + 164509 + 119210 = 362 842 Гб```

### 5. Выбор прочих технологий

#### Frontend
Фронтенд будет написан с использованием технологий HTML, CSS, Typescript.
В качестве библиотеки для работы с интерфейсаими будет использоваться React, так как он обеспечивает модульность кода 
и высокую производительность, а также, прост в освоении. Для увеличения уровня абстракции при работе с css, 
воспользуемся Scss(Sass) - метаязыком, основанным на css. В качестве сборщика модулей будем использовать один из наиболее
распространенных инструментов - Webpack

#### Backend
Бэкенд будет иметь микросервисную архитектуру, что позволит легко масштабировать конкретный сервис, а не все приложение,
а также, повысит отказоустойчивость системы. В качестве основного языка будет использоваться Golang, так как он 
обладает строгой типизацией, низким порогом вхождения, многопоточностью и кроссплатформенностью "из коробки", а также, 
высокой производительностью.

#### Протоколы взаимодействия
Для общения между клиентом и сервером выберем протокол HTTP2, так как он бинарный, а также, поддеожиывет одновременную
загрузку большого количества файлов. Сами данные будут передаваться в формате json, так как он прост и поддерживается 
многими языками программирования.

На бэкенде для общения между микросервисами будет использоваться протокол gRPC, а данные будут передаваться в формате 
protobuf, что позволит увеличить скорость общения, так как структуры в protobuf быстрее парсятся и имеют меньший 
размер, в сравнении с другими форматами.

#### Балансировка нагрузки и раздача статики
Для раздачи статики м балансировки нагрузки будет использоваться Nginx, как надежный и высокопроизводительный веб-сервер,
поддерживающий возможность написания собственных модулей, а также, обладающий крупным сообществом.
