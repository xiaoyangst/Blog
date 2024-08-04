在做一道测试题，需要和位运算打交道，C++提供有容器是std::bitset，特此学习一番

## 常用的构造函数

```c++
std::bitset<N> b;		// 默认构造函数

std::bitset<N> b(unsigned long val);	// 整数构造函数

std::bitset<N> b(const std::string& str, std::string::size_type pos = 0, std::string::size_type n = std::string::npos);		// 字符串构造函数
```

下面对几个构造函数进行演示，看看效果是怎么样的

```c++
#include <iostream>
#include <bitset>

int main() {
  // 默认构造函数
  std::bitset<8> bit;

  // 整数构造函数
  int num = 10;
  std::bitset<4> bitNum(num);

  // 字符串构造函数
  std::string str = "11001001";
  std::bitset<16> bitStr(str);

  std::cout<<"默认构造函数 = "<<bit<<std::endl;
  std::cout<<"整数构造函数 = "<<bitNum<<std::endl;
  std::cout<<"字符串构造函数 = "<<bitStr<<std::endl;

  return 0;
}

/*

默认构造函数 = 00000000
整数构造函数 = 1010
字符串构造函数 = 0000000011001001

*/
```

整数构造函数传递整数，会被自动转换成二进制数

字符串构造函数传递字符串类型的二进制数，会被自动转换成二进制数。同样，还支持读取字符串的起始地址（pos）和读取长度（n）

我们还要看看两种情况：

- 位数不够：从右往左截取指定位数长度
- 位数超过可以容纳的长度：左边用 0 补齐

```c++
#include <iostream>
#include <bitset>

int main() {

  // 整数构造函数
  int num = 9;
  std::bitset<8> bitNum1(num);
  std::bitset<2> bitNum2(num);


  std::cout<<"超过 = "<<bitNum1<<std::endl;
  std::cout<<"不足 = "<<bitNum2<<std::endl;

  return 0;
}

/*

超过 = 00001001
不足 = 01

*/
```

## 其它方法

set：将所有位设置为 `1`，或者将特定位置的位设置为 `1`

```c++
void set(); // 将所有位设置为1
void set(std::size_t pos, bool value = true); // 将位置pos的位设置为value
```

reset：将所有位设置为 `0`，或者将特定位置的位设置为 `0`

```c++
void reset(); // 将所有位设置为0
void reset(std::size_t pos); // 将位置pos的位设置为0
```

flip：反转所有位的值（`0`变`1`，`1`变`0`），或者反转特定位置的位值

```c++
void flip(); // 反转所有位
void flip(std::size_t pos); // 反转位置pos的位
```

test：检查特定位置的位是否为 `1`，返回 `true` 或 `false`

```c++
bool test(std::size_t pos) const; // 检查位置pos的位是否为1
```

any：检查是否有任何位被设置为 `1`，如果有则返回 `true`

```c++
bool any() const; // 是否有任何位为1
```

none：检查是否所有位都为 `0`，如果是则返回 `true`

```c++
bool none() const; // 是否所有位为0
```

all：检查是否所有位都为 `1`，如果是则返回 `true`

```c++
bool all() const; // 是否所有位为1
```

count：返回 `bitset` 的大小（位数）

```c++
std::size_t size() const; // bitset的大小
```

to_string：将 `bitset` 转换为包含 `0` 和 `1` 的字符串

```c++
std::string to_string() const; // bitset转换为字符串
```

:pushpin: to_ulong：将 `bitset` 转换为 `unsigned long`，即把给定的二进制转换为整数

```c++
unsigned long to_ulong() const; // bitset转换为unsigned long
```

位运算符：可以使用位运算符 `&`、`|`、`^`、`~`、`<<` 和 `>>` 进行位操作

## 解题

有一个 16 位的整数，每 4 位为一个数，写函数求他们的和。比如：整数 1101010110110111（十进制为 54711），和 1101+0101+1011+0111（十进制为 36）

```c++
#include <iostream>
#include <bitset>

unsigned long getSum(const std::string& str){

  unsigned long sum = 0;
  for (int i = 0; i < 16; i += 4) {
    std::string tmp = str.substr(i,4);
    std::bitset<16> bitStr(tmp);
    sum += bitStr.to_ulong();
  }

  return sum;
}

int main() {

  std::string bitStr;
  std::cout<<"输入16 位的整数（由0或1组成）:";
  std::getline(std::cin,bitStr);

  std::cout<<getSum(bitStr)<<std::endl;

  return 0;
}

/*

输入16 位的整数（由0或1组成）:1101010110110111
36

*/
```

