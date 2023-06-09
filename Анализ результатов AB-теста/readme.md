# Анализ результатов A/B-теста в интернет-магазине

**Постановка задачи:**
- Оцените корректность проведения теста и проанализируйте его результаты.
- Чтобы оценить корректность проведения теста:
    - удостоверьтесь, что нет пересечений с конкурирующим тестом и нет пользователей, участвующих в двух группах теста одновременно;
    - проверьте равномерность распределения пользователей по тестовым группам и правильность их формирования.

**Техническое задание:**
- Название теста: `recommender_system_test`;
- группы: `А` — контрольная, `B` — новая платёжная воронка;
- дата запуска: 2020-12-07;
- дата остановки набора новых пользователей: 2020-12-21;
- дата остановки: 2021-01-04;
- аудитория: в тест должно быть отобрано 15% новых пользователей из региона EU;
- назначение теста: тестирование изменений, связанных с внедрением улучшенной рекомендательной системы;
- ожидаемое количество участников теста: 6000;
- ожидаемый эффект: за 14 дней с момента регистрации пользователи покажут улучшение каждой метрики не менее, чем на 10%:
    - конверсии в просмотр карточек товаров — событие `product_page`,
    - просмотры корзины — `product_cart`,
    - покупки — `purchase`.


## Использованные библиотеки
```
pandas
seaborn
numpy
matplotlib
scipy
math
datetime
```
**Вывод:**

**Предобработка:**
- в промежуток времени, для набора пользователей для теста *recommender_system_test* никакие акции не проводились, после набора было 2 акции, навредить нашему тесту они не должны;
- нарушений ТЗ по датам запуска/сбора/остановки не обнаружено;    
- в наш тест попало 6701 пользователь;
- 1602 пользователя(~24%) попали и ещё в *interface_eu_test*, они могут исказить результаты;
- ~5% пользователей из других регионов;
- после очистки осталось 6351 пользователь, что соответствует 15% от всего трафика региона *EU* в указанный период набора;
- распределение в группы неравномерное, ~ 57/43;
- 2870 пользователей после регистрации не совершили никаких событий, их распределние ~ 64/36;
- убрав события старше 14 дней, а так же пользователей, которые не совершали события, у нас осталось 3481 пользователей с распределением ~ 75/25, что является грубым нарушением ТЗ и А/В-теста (6000 пользователей и распределение 50/50).

**Исследовательский анализ данных:**
- в группе *В* пользователь в среднем совершал ~4 события, в группе *А* ~6 событий, что на 50% больше;
- с 14 по 22 декабря 2020 г. видим скачок количества событий, маркетинговых событий не было, до 14 декабря количество событий в группе *В* было больше, во время и после скачка ситуация поменялась;
- всплеск регистраций 14 декабря, возможно, что-то изменили в рекламной кампании;
- по устройствам аномалий не обнаружено;
- по общей воронке:
    - всего в эксперементе 3481 пользователей;
    - 99.97% авторизовались;
    - 62.57% увидели страницу с товаром;
    - 29.47% перешли в корзину с товарами;
    - 31.08% успешно оплатили;
    - в `product_cart` попало меньше пользователей, чем `purchase`, скорее всего, потому что на сайте есть функция быстрой покупки, при помощи которой, которая минуя `product_cart`, можно купить товар;
    - на переходе от события `login` к событию `product_page` теряется больше всего пользователей, что составляет 37.4%.
- по воронкам групп:
    - конверсия просмотра карточек товаров упала на 13.1%;
    - конверсия просмотра корзины упала на 11.3%;
    - конверсия в покупки упала на 7.4%.
- средняя выручка на пользователя по группам отличается незначительно, в контрольной группе 23.1 у.е. на пользователя, в эксперементальной 22.9 у.е..

**Итог:** учитывая все выводы, можно сказать, что эксперемент с внедрением улучшенной рекомендательной системы - провалился, многие после регистрации не совершли никаких событий, и все метрики упали на 7-13%, когда ожидали улучшения не менее, чем на 10%.

*P.S. В связи нарушениями, результаты А/В-теста могут быть неправильными и недостоверными.*
