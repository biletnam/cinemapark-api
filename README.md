# Cinema Park API
PHP библиотека для интеграции с информационными системами Синема Парк.

[![Latest Stable Version](https://poser.pugx.org/axp-dev/cinemapark-api/v/stable)](https://packagist.org/packages/axp-dev/cinemapark-api)
[![Latest Unstable Version](https://poser.pugx.org/axp-dev/cinemapark-api/v/unstable)](https://packagist.org/packages/axp-dev/cinemapark-api)
[![License](https://poser.pugx.org/axp-dev/cinemapark-api/license)](https://packagist.org/packages/axp-dev/cinemapark-api)

## Оглавление
1. [Старт](#Старт)
    + [Composer](#Установка-через-composer)
    + [Инициализация](#Инициализация)
2. [Использование](#Использование)
    + Получение информации по кинотеатрам, фильмам и сеансам
        + [Получение списка мультиплексов и городов](#Получение-списка-мультиплексов-и-городов)
        + [Получение списка фильмов](#Получение-списка-фильмов)
        + [Привязка фильмов к мультиплексам](###Привязка-фильмов-к-мультиплексам)
        + [Получение расписания фильма](#Получение-расписания-фильма)
        + [Получение расписания мультиплекса](#Получение-расписания-мультиплекса)
        + [Получение дополнительной информации по фильму](#Получение-дополнительной-информации-по-фильму)
        + [Получение списка залов по всем мультиплексам](#Получение-списка-залов-по-всем-мультиплексам)
        + [Получение списка форматов показа фильмов](#Получение-списка-форматов-показа-фильмов)
        + [Комплексная выгрузка текущего расписания мультиплекса](#Комплексная-выгрузка-текущего-расписания-мультиплекса)
    + [В разработке] Организация интернет-бронирования и продаж
        + [Проверка возможности начать сессию выбора мест для бронирования или покупки мест](#Проверка-возможности-начать-сессию-выбора-мест-для-бронирования-или-покупки-мест)
        + [Инициализация сессии выбора мест для бронирования или покупки](#Инициализация-сессии-выбора-мест-для-бронирования-или-покупки)
        + [Получение геометрической схемы зала](#Получение-геометрической-схемы-зала)
        + [Получение состояния мест](#Получение-состояния-мест)
3. [Вспомогательная информация](#Вспомогательная-информация)
    + [Код возрастных ограничений](#Код-возрастных-ограничений)
    + [Состояние сеанса](#Состояние-сеанса)
    + [Режим открытия сессии выбора мест](#Режим-открытия-сессии-выбора-мест)
    + [Код доступности инициализации сессии](#Код-доступности-инициализации-сессии)
    + [Возможность работы с сеансом](#Возможность-работы-с-сеансом)
    + [Статус сессии выбора мест](#Статус-сессии-выбора-мест)
    + [Состояние места](#Состояние-места)
4. [Автор](#Автор)
5. [Лицензия](#Лицензия)

## Старт
### Установка через composer
```
$ composer require axp-dev/cinemapark-api
```
### Инициализация
```php
$CinemaPark = new AXP\CinemaPark\CinemaPark();

// Получаем информаицю по фильму "Гадкий я 3"
$film = $CinemaPark->getFilmInfo(3679);
```

## Использование
### Получение списка мультиплексов и городов
```php
public function getMultiplexes() : array
```
#### Результат ответа
Поле | Тип | Описание
-----|-----|---------
id | integer | Идентификатор мультиплекса
short_name | string | Короткое название мультиплекса
full_name | string | Полное название мультиплекса
description | string | Описание кинотеатра
phone | string | Телефон кинотеатра
formats | array | Список форматов показа фильмов
city_id | integer | Идентификатор города мультиплекса
city_name | string | Наименование города мультиплекса
address | string | Адес мультиплекса
multiplex_geo | string | Географические координаты мультиплекса

### Получение списка фильмов
В список могут попадать фильмы, не значащиеся в расписании мультиплексов (к примеру, поставленные в прокат на будущее, но без конкретного расписания).
```php
public function getFilms() : array
```
#### Результат ответа
Поле | Тип | Описание
-----|-----|---------
has_subtitles | bool | Если фильм идёт с субтитрами (скорее всего, с оригинальной звуковой дорожкой)
age_id | integer | Код возрастных ограничений. [Подробнее](#Код-возрастных-ограничений)
startdate | string | Дата старта проката в нашей сети (без учёта возможных премьерных показов)
genre | string | Текстовое описание жанра фильма
original_title | string | Оригинальное название фильма (для иностранных фильмов)
timing | integer | Продолжительность фильма в минутах
age_limit | integer | Возрастное ограничение. В будущем будет произведён полный переход от age_id к age_limit
category | string | Slug категории 
title | string | Русскоязычное название фильма с учётом формата
film_id | integer | Идентификатор фильма
youtubeid | string | Список трейлеров с Youtube (через запятую) 

### Привязка фильмов к мультиплексам
В список могут попадать фильмы, не значащиеся в расписании мультиплексов (к примеру, поставленные в прокат на будущее, но без конкретного расписания).
```php
public function getFilmsMultiplexes() : array
```
#### Результат ответа
Поле | Тип | Описание
-----|-----|---------
id | integer | Идентификатор фильма
multiplex | array | Идентификатор мультиплекса, к которому привязан фильм

### Получение расписания фильма
Выводится всё известное расписание, в т.ч. и прошедшие сеансы.
```php
public function getRepertoir($id) : array
```
#### Параметры метода
Аргумент | Тип | Описание
-----|-----|---------
id | integer | Идентификатор фильма
#### Результат ответа
Поле | Тип | Описание
-----|-----|---------
format_id | integer | Идентификатор формата показа, соответствующий выгрузке formats
hall | integer | Идентификатор зала (уникален для всей сети)
base_price | integer | Цена билета на сеанс без учёта скидок (в российских рублях)
id | integer | Идентификатор сеанса
state | bool | Состояние сеанса (открыт, либо фильмокопия не поступила / произошёл срыв сеанса / сеанс отменён). [Подробнее](#Состояние-сеанса)
datetime | string | Дата/время сеанса (местное время соответствующего мультиплекса)
multiplex | integer | Идентификатор мультиплекса
glasses_price | integer | Дополнительная стоимость, взимаемая на кассе за 3D-очки

### Получение расписания мультиплекса
```php
public function getMultiplexRepertoir($id) : array
```
#### Параметры метода
Аргумент | Тип | Описание
-----|-----|---------
id | integer | Идентификатор мультиплекса
#### Результат ответа
Поле | Тип | Описание
-----|-----|---------
format_id | integer | Идентификатор формата показа, соответствующий выгрузке formats
hall | integer | Идентификатор зала (уникален для всей сети)
base_price | integer | Цена билета на сеанс без учёта скидок (в российских рублях)
id | integer | Идентификатор сеанса
state | bool | Состояние сеанса (открыт, либо фильмокопия не поступила / произошёл срыв сеанса / сеанс отменён). [Подробнее](#Состояние-сеанса)
datetime | string | Дата/время сеанса (местное время соответствующего мультиплекса)
multiplex | integer | Идентификатор мультиплекса
glasses_price | integer | Дополнительная стоимость, взимаемая на кассе за 3D-очки

### Получение дополнительной информации по фильму
```php
public function getFilmInfo($id) : array
```
#### Параметры метода
Аргумент | Тип | Описание
-----|-----|---------
id | integer | Идентификатор фильма

#### Результат ответа
Поле | Тип | Описание
-----|-----|---------
hit | bool | Присвоен ли фильму статус «Хит»
description | string | Описание фильма
addinfo | array | Дополнительная информация, тип которой указан в атрибуте «title» (режиссёр, актёры, озвучка)
year | integer | Год выпуска фильма
country | string | Страна фильма

### Получение списка залов по всем мультиплексам
```php
public function getHalls() : array
```
#### Результат ответа
Поле | Тип | Описание
-----|-----|---------
multiplex_id | integer | Идентификатор мультиплекса
title | string | Идентификатор зала внутри мультиплекса
id | integer | Идентификатор зала внутри мультиплекса

### Получение списка форматов показа фильмов
```php
public function getFormats() : array
```
#### Результат ответа
Поле | Тип | Описание
-----|-----|---------
id | integer | Идентификатор формата (показываемый в выгрузке repertoir как format_id)
short_name | string | Наименование формата
title_suffix | sting | Текстовая строка, которую нужно добавить к названию фильма, чтобы получить «название фильма с учётом формата»
priority | integer | Очерёдность показа формата в списке форматов

### Комплексная выгрузка текущего расписания мультиплекса
```php
public function getTimeTable($id) : array
```
#### Параметры метода
Аргумент | Тип | Описание
-----|-----|---------
id | integer | Идентификатор мультиплекса
#### Результат ответа
Поле | Тип | Описание
-----|-----|---------
hall | integer | Идентификатор зала (уникален для всей сети)
hall_title | string | Маркетинговое/коммерческое наименование зала
datetime | string | Дата/время сеанса (местное время соответствующего мультиплекса)
base_price | integer | Цена билета на сеанс без учёта скидок (в российских рублях)
age_limit | integer | Возрастное ограничение на фильм
title | string | Название фильма с учётом формата
hall_website_id | integer | Идентификатор зала на сайте СИНЕМА ПАРК
has_subtitles | bool | Наличие субтитров на сеансе

### Проверка возможности начать сессию выбора мест для бронирования или покупки мест
```php
public function checkBSession($multiplex_id, $repertoir_id, $mode) : array
```
#### Параметры метода
Аргумент | Тип | Описание
-----|-----|---------
multiplex_id | integer | Идентификатор мультиплекса
repertoir_id | integer | Идентификатор сеанса (уникален в пределах мультиплекса)
mode | integer | Режим открытия сессии выбора мест. [Подробнее](#Режим-открытия-сессии-выбора-мест)

#### Результат ответа
Поле | Тип | Описание
-----|-----|---------
status | integer | Можно ли начать сессию выбора мест. 0 - Нельзя, 1- Можно.
mode_available | integer/array | Доступность режимов открытия сессии выбора мест. 0 - Нельзя, 1- Можно.
b_session_availability | integer | Код доступности инициализации сессии. [Подробнее](#Код-доступности-инициализации-сессии)
repertoir_status | integer | Возможность работы с сеансом. [Подробнее](#Возможность-работы-с-сеансом)

### Инициализация сессии выбора мест для бронирования или покупки
```php
public function initBSession($multiplex_id, $repertoir_id, $mode) : array
```
#### Параметры метода
Аргумент | Тип | Описание
-----|-----|---------
multiplex_id | integer | Идентификатор мультиплекса
repertoir_id | integer | Идентификатор сеанса (уникален в пределах мультиплекса)
mode | integer | Режим открытия сессии выбора мест. [Подробнее](#Режим-открытия-сессии-выбора-мест)

#### Результат ответа
Поле | Тип | Описание
-----|-----|---------
status | integer | Можно ли начать сессию выбора мест. 0 - Нельзя, 1- Можно.
b_session_id | string | Идентификатор сессии выбора мест, использующийся в дальнейшем при выборе мест и отмене/фиксации сессии

### Получение геометрической схемы зала
Схему зала можно кешировать, рассчитывая на то, что в данном конкретном сеансе она не изменится.
```php
public function seatsLayout($multiplex_id, $repertoir_id) : array
```
#### Параметры метода
Аргумент | Тип | Описание
-----|-----|---------
multiplex_id | integer | Идентификатор мультиплекса
repertoir_id | integer | Идентификатор сеанса (уникален в пределах мультиплекса)

#### Результат ответа
Поле | Тип | Описание
-----|-----|---------
id | integer | ID места. Используется для выбора или для отмены выбора места
number | integer | Номер зрительского кресла (расположен на спинке кресла)
row | integer | Ряд зрительских кресел
xpos | integer | X-координата кресла на схеме зала
ypos | integer | Y-координата кресла на схеме зала
block_No | integer | Номер блока для групповых мест (диванов). Присутствует, если место является частью группового.

### Получение состояния мест
```php
public function seatStates($multiplex_id, $repertoir_id, $b_session_id, $timestamp_tz = 0) : array
```
#### Параметры метода
Аргумент | Тип | Описание
-----|-----|---------
multiplex_id | integer | Идентификатор мультиплекса
repertoir_id | integer | Идентификатор сеанса (уникален в пределах мультиплекса)
b_session_id | string | Идентификатор сессии выбора мест
timestamp_tz | integer | Дата/время в формате Unix Timestamp

#### Результат ответа
Поле | Тип | Описание
-----|-----|---------
sql_date | array | Время на стороне нашей системы, соответствующее выводимым данным
b_session_data | array | Данные сессии выбора мест (при наличии)
seat_states | array | Данные по месту (при наличии)

##### Данные в `b_session_data`
Поле | Тип | Описание
-----|-----|---------
ts_start_tz | integer | Время начала сессии в формате Unix Timestamp
status | integer | Текущий статус сессии выбора мест. [Подробнее](#Статус-сессии-выбора-мест)

##### Данные в `seat_states`
Поле | Тип | Описание
-----|-----|---------
is_mine | bool | Принадлежит ли место текущей сессии выбора мест
seat_id | integer | ID места, соответствующее выгрузке `seatsLayout()`.
state | integer | Состояние места. [Подробнее](#Состояние-места)

## Вспомогательная информация
### Код возрастных ограничений
Значение | Описание 
-----|-----
1 | без ограничений по возрасту
2 | до 12 лет в сопровождении родителей
3 | достигшим 14 лет
4 | достигшим 16 лет
5 | достигшим 18 лет
6 | для детей старше 6 лет

### Состояние сеанса
Значение | Описание 
-----|-----
1 |Сеанс открыт, в нём можно бронировать и покупать билеты
не 1 | Сеанс не открыт, в нём нельзя бронировать и покупать билеты

Вопрос «показывать ли пользователю сеансы, где state != 1» остаётся на ваше усмотрение.

### Режим открытия сессии выбора мест
Значение | Описание 
-----|-----
0 | Проверка режимов бронирования и покупки
1 | Бронирование
2 | Покупка

### Код доступности инициализации сессии
Значение | Описание
-----|-----
0 | Ошибка получения статуса
1 | Можно инициализировать сессию
2 | Превышено общее количество активных сессий
3 | Превышено количество активных сессий в мультиплексе
4 | Превышено количество активных сессий в сеансе
5 | Превышено количество активных сессий по IP-адресу
6 | Превышено общее количество сессий по IP-адресу
7 | Превышено количество бронирований по IP-адресу
8 | Система недоступна, ведутся технические работы

Для IP, внесённых в «белый список», всегда должно возвращаться 1.

### Возможность работы с сеансом
Значение | Описание
-----|-----
0 | Различные нарушения целостности базы данных
1 | Нет соединения с базой данных
2 | Отсутствует сеанс
3 | Сеанс не открыт
4 | В зале нет ни одного свободного места, бронь и продажи закрыты
5 | Всё OK, сеанс открыт – можно бронировать и продавать
6 | Слишком мало времени до сеанса, продажа запрещена
7 | Слишком мало времени до сеанса, бронирование запрещено
8 | Сеанс прошёл
9 | В зале осталось мало свободных мест — разрешена только продажа
10 | В данном сеансе разрешено только бронирование

### Статус сессии выбора мест
Значение | Описание
-----|-----
1 | Идёт выбор мест
2 | Выбор мест отменён (пользователь отказался от бронирования/покупки на стадии выбора мест)
3 | Таймаут выбора (сессия закрыта по тайм-ауту, пользователь не отказался от выбора мест, но и не подтвердил его)
4 | Места забронированы
5 | Бронь снята (после подтверждения брони пользователь отказался от неё)
6 | Таймаут оплаты (после фиксации выбора мест оплата не поступила в установленное время)
7 | Покупка совершена, места оплачены

### Состояние места
Значение | Описание
-----|-----
0 | Место свободно
1 | Место блокировано (в настоящий момент выбрано для бронирования или продажи)
2 | Место забронировано
3 | Место продано (билет выдан на руки клиенту)

## Автор
[Alexander Pushkarev](https://github.com/axp-dev), e-mail: [axp-dev@yandex.com](mailto:axp-dev@yandex.com)

## Лицензия
Основой Cinema Park API являет открытый исходный код, в соответствии [MIT license](https://opensource.org/licenses/MIT)