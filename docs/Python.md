# Python

### Косяк с таймзоной

```Python
import datetime
import pytz

MSK = pytz.timezone('Europe/Moscow')
date = datetime.datetime.combine(datetime.date.today(), datetime.time.min, tzinfo=MSK)
date
```
Out[6]: datetime.datetime(2023, 3, 28, 0, 0, tzinfo=<DstTzInfo 'Europe/Moscow' LMT+2:30:00 STD>)  <---- WTF! 2.30! 

Не забиваем себе голову почему да как тупо делаем вот так:
```Python
date = datetime.datetime.combine(datetime.date.today(), datetime.time.min)
data = MSK.localize(begin_datetime)
data
```
Out[11]: datetime.datetime(2023, 3, 28, 0, 0, tzinfo=<DstTzInfo 'Europe/Moscow' MSK+3:00:00 STD>)  <---- Вот теперь все нормально. 

