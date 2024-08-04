> 之前浅学这块内容，C++11封装的如此冗长，于是“重学”加深影响，方便后续查阅

在`std::chrono`命名空间下定义

## 时间段duration



## 时间点time_point

用于表示时间点的类模板，和下面要介绍的clocks结合使用

创建对象不填写参数，代表构造一个以纪元为值的对象

```c++
#include <iostream>
#include <chrono>

int main(){

  std::chrono::system_clock::time_point time_point1;
  time_t  tt1 = std::chrono::system_clock::to_time_t(time_point1);
  std::cout<<ctime(&tt1);

  return 0;
}

/*
	Thu Jan  1 08:00:00 1970
*/
```

有两个成员方法：min和max代表time_point的最小值和最大值

测试最小值为空，最大值为Sat Apr 12 07:47:16 2262

前面介绍无参构造函数，还有两个构造函数没有介绍

```c++
// 使用一个时间段 d 来初始化时间点，从时钟的时间零点开始计算
explicit time_point(const duration& d);

/*

    // 创建一个时间段
    std::chrono::seconds duration(10);
    
    // 使用时间段构造 time_point
    std::chrono::system_clock::time_point tp_with_duration(duration);
    
    auto duration_since_epoch = tp_with_duration.time_since_epoch();
    
    std::cout << "Constructed time_point with duration: "
              << duration_since_epoch.count() << " seconds since epoch\n";
             
     输出结果：
     Constructed time_point with duration: 10000000000 seconds since epoch

*/

```

```c++
// 使用另一个时间点来拷贝构造新的时间点
time_point(const time_point& other) = default;

/*

    // 获取当前时间点
    std::chrono::system_clock::time_point tp_now = std::chrono::system_clock::now();
    
    // 使用拷贝构造函数
    std::chrono::system_clock::time_point tp_copy(tp_now);
    
    auto duration_since_epoch_now = tp_now.time_since_epoch();
    auto duration_since_epoch_copy = tp_copy.time_since_epoch();
    
    std::cout << "Original time_point: "
              << duration_since_epoch_now.count() << " ticks since epoch\n";
    std::cout << "Copied time_point: "
              << duration_since_epoch_copy.count() << " ticks since epoch\n";
              
     输出结果：
     Original time_point: 1719716264027922800 ticks since epoch
     Copied time_point: 1719716264027922800 ticks since epoch
*/
```

## 系统时钟clocks

（一）std::chrono::system_clock

now：可以用于获取当前时间

to_time_t：转换为 time_t 

from_time_t：从time_t 转换

```c++
#include <iostream>
#include <chrono>

int main(){

  std::chrono::system_clock::time_point curTime = std::chrono::system_clock::now(); // 获取当前时间
  time_t showTime = std::chrono::system_clock::to_time_t(curTime);  // 转换为 time_t 类型
  std::cout<<"当前时间: "<<std::ctime(&showTime)<<std::endl;

  return 0;
}

/*
	当前时间: Sun Jun 30 10:29:03 2024
*/
```

time_t 类型是C语言的时间类型，这就是实现C++和C语言时间类型的转换的

（二）std::chrono::steady_clock

不会被系统时间的调整所影响，且它的滴答频率是稳定的，适合用于**测量时间间隔**

只提供 now方法用于获取当前时间，测量时间间隔只需要调用两次now方法求差即可

（三）std::chrono::high_resolution_clock

提供了最高可能的时间分辨率。通常，它是 `system_clock` 或 `steady_clock` 的别名，但具体实现依赖于系统和标准库实现

同样只提供 now 方法用于获取当前时间

---

看来，测试时间间隔采用std::chrono::steady_clock，希望将时间转换为日历显示效果或者转换为C语言风格的时间类当采用std::chrono::system_clock

##  表示不同的时间单位

`std::chrono::hours`：表示小时的持续时间

`std::chrono::minutes`：表示分钟的持续时间

`std::chrono::seconds`：表示秒的持续时间

`std::chrono::milliseconds`：表示毫秒的持续时间

`std::chrono::microseconds`：表示微秒的持续时间

`std::chrono::nanoseconds`：表示纳秒的持续时间

## 转换为duration或time_point

（一）duration_cast

用于将时间段从一种单位转换为另一种单

```c++
template <class ToDuration, class Rep, class Period>
constexpr ToDuration duration_cast(const duration<Rep, Period>& d);
```

把 秒 转换为 分钟，把 秒 转换为毫秒 

```c++
#include <iostream>
#include <chrono>

int main() {
    std::chrono::seconds sec(120); // 120 seconds

    // 将秒转换为分钟
    std::chrono::minutes min = std::chrono::duration_cast<std::chrono::minutes>(sec);
    std::cout << sec.count() << " seconds is " << min.count() << " minutes." << std::endl;

    // 将秒转换为毫秒
    std::chrono::milliseconds millisec = std::chrono::duration_cast<std::chrono::milliseconds>(sec);
    std::cout << sec.count() << " seconds is " << millisec.count() << " milliseconds." << std::endl;

    return 0;
}
```

（二）time_point_cast

用于将时间点从一种单位转换为另一种单位

```c++
template <class ToDuration, class Clock, class Duration>
constexpr time_point<Clock, ToDuration> time_point_cast(const time_point<Clock, Duration>& tp);
```

将当前时间点 转换为 毫秒精度，将当前时间点转换为秒精度

```c++
#include <iostream>
#include <chrono>

int main() {
    auto now = std::chrono::system_clock::now(); // 获取当前时间点

    // 将当前时间点转换为毫秒精度
    auto now_ms = std::chrono::time_point_cast<std::chrono::milliseconds>(now);
    auto epoch = now_ms.time_since_epoch();
    auto value = std::chrono::duration_cast<std::chrono::milliseconds>(epoch);

    std::cout << "Current time in milliseconds since epoch: " << value.count() << "ms" << std::endl;

    // 将当前时间点转换为秒精度
    auto now_sec = std::chrono::time_point_cast<std::chrono::seconds>(now);
    epoch = now_sec.time_since_epoch();
    auto value_sec = std::chrono::duration_cast<std::chrono::seconds>(epoch);

    std::cout << "Current time in seconds since epoch: " << value_sec.count() << "s" << std::endl;

    return 0;
}
```

