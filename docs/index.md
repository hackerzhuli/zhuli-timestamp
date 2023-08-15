为什么要创建一个新的时间戳：
Unix时间戳可以使用简单的数据类型（int）来表示一个时间点，可以用来简单的比较日期，以及记录日期，以及在不同的系统之间传递日期信息。

但是，Unix时间戳的范围有限，只能表示x-x范围内的日期。

这是因为int类型的数值范围太小了。

所以，需要一个新的标准。使用long来表示一种新的时间戳，并且，将epoch从1970年改为1年。将秒改为毫秒。

大概能表示的范围是+-2.92亿年。

说明：
int：是指32位整数
long：是指64位整数

特别说明：
这个日期和MongoDB的Date类型是一致的。实际上，MongoDB的日期内部也是这样存储的。

精度选择说明：
时间戳的主要作用，是记录和传输日期。通常来讲，微秒级的精度在大多数应用中都没有作用。这样做，也可以减少在json中表示大的数字的长度。

python 参考实现：
``` Python3
from datetime import datetime, timezone, timedelta

epoch = datetime(1, 1, 1, tzinfo=timezone.utc)


def datetime_to_zhuli_timestamp(date:datetime) -> int:
    """
    convert datetime to zhuli timestamp

    Returns
    -------
    an integer represents zhuli timestamp
    """
    return round((date - epoch).total_seconds() * 1000)


def datetime_from_zhuli_timestamp(timestamp: int) -> datetime:
    """
    convert zhuli timestamp to zhuli datetime

    Returns
    -------
    a datetime object
    """
    return epoch + timedelta(milliseconds=timestamp)


def zhuli_timestamp_now() -> int:
    """
    get zhuli time stamp of now

    Returns
    -------
    zhuli timestamp that represents now
    """
    return datetime_to_zhuli_timestamp(datetime.now(tz=timezone.utc))

```

C# 参考实现
``` C#
using System;

namespace Zhuli
{
    public static class ZhuliTimestamp
    {
        public static readonly DateTime Epoch = new DateTime(1, 1, 1, 0, 0, 0, 0, DateTimeKind.Utc);
        
        public static long FromDateTime(DateTime dateTime)
        {
            return (int)Math.Round((dateTime - Epoch).TotalSeconds * 1000);
        }

        public static DateTime ToDateTime(long timestamp)
        {
            return Epoch + new TimeSpan(timestamp * (TimeSpan.TicksPerSecond / 1000));
        }

        public static long Now()
        {
            return FromDateTime(DateTime.UtcNow);
        }
    }
}
```
