## 可调用对象的类型和调用形式

在 C++ 中函数、函数指针、指向类的成员函数指针、函数对象（重载了调用运算符的类）、lambda 表达式都属于**可调用对象**。可调用对象都是有类型的，比方说函数对象，它本质就是一个重载了调用运算符的类，不同的类当然类型也不相同。还有函数和函数指针，它们的类型是有其返回值类型和实参类型决定，`int func(int a,int b)` 和 `void func()` 就不是同一个类型。它们对应的指针也是不同的类型，不能相互赋值。

虽然它们的类型不尽相同，但是它们的调用形式却有相同之处。调用形式指明了调用返回的类型和传递给调用的实参类型。如下面的代码所示，尽管 函数 和 lambda表达式 类型不同（不能相互赋值），但是调用形式一样。

```c++
#include <iostream>
using namespace std;

int addA(int num1,int num2){  // 函数
  return num1 + num2;
}

auto addB = [](int num1,int num2)->int {  // lambda表达式
  return num1 + num2;
};

int main() {
  
  // 调用形式一样
  addA(1,2);
  addB(1,2);

  return 0;
}
```

## function的用法

同一种调用形式的可调用对象的类型可能不同，可以通过 function 将其**统一**起来，function 是一个模板，当创建一个具体的 function 类型时必须提供该 function 类型能够表示的对象的调用信息。

```c++
function<int(int,int)>
```

声明了一个 function 类型，它表示接受两个 int 实参，返回一个 int 结果的可调用对象。

注：当你知道 function 是一个模板，就理解为什么外层是一对尖括号<>

---

既然 function 是把不同的可调用对象的类型统一起来，那就把之前的例子拿过来统一看看。

```c++
#include <iostream>
#include <vector>
#include <functional>
using namespace std;

int addA(int num1,int num2){  // 函数
  return num1 + num2;
}

auto addB = [](int num1,int num2)->int {  // lambda表达式
  return num1 + num2;
};

int main() {

  using  funCallback = function<int(int,int)>;

  funCallback f1 = addA;
  funCallback f2 = addB;

  f1(1,2);
  f2(1,2);
  return 0;
}
```

我们可以看到在赋值给 function 类型时，我们没有传递任何参数（实际上也不支持），而是把可调用对象的名称传递过去。还有 function 把不同的可调用对象的类型统一起来，这在上面这个例子中已经证明，即f1、f2接受不同类型的可调用对象了。

function 类型的对象（如上面的f1、f2）只是把可调用对象存储起来，需要的时候调用，就像调用普通函数那样，传递所需的实参即可。如果声明的 function 类型的对象没有存储可调用对象，我们去执行是没有任何效果的，为了避免这种情况，可以在调用之前检查是否为空。

```c++
if(!f1){	
	std::cout<<"f1 is nullptr"<<std::endl;
}
```

难道我们的 function 就没有不足吗？有个可调用对象 function 无法存储，即类成员（函数）指针。这是因为成员函数指针和普通函数指针有一些重要的区别。成员函数指针需要一个对象实例来调用，而普通函数指针不需要。

```c++
class Test{
 public:
  Test() = default;
  ~Test() = default;
  int AddC(int num1,int num2){
    return num1 + num2;
  }
};

int main() {

  using  funCallback = function<int(int,int)>;
  funCallback f1 = &Test::AddC;	// 报错，这是不允许的

  return 0;
}
```

## bind的用法

接受一个可调用对象，生成一个新的可调用对象来适应原对象的参数列表。即用来将可调用对象与其参数一起进行绑定，绑定后的结果可以使用 function 进行保存。

bind 必须把函数所需的参数进行绑定，如果不填写默认参数进行绑定，就用占位符placeholders::_N（1、2、3...N）填充，等后续调用时再自行填写。

```c++
// 绑定非类成员函数/变量
auto f = std::bind(可调用对象地址, 绑定的参数/占位符);
// 绑定类成员函/变量
auto f = std::bind(类函数/成员地址, 类实例对象地址, 绑定的参数/占位符);
```

前面讲 function的时候，讲到两个问题。首先 function 存储可调用对象，只有在实际调用的情况下才允许传递实参。其次，function 无法存储 类成员（函数）指针。这两个问题 bind 都可以解决。

为什么这里通常用 auto 来推断类型？因为 bind 绑定占位符之后，类型就会发生改变，如果手动去指定会比较麻烦，看下面这个例子：

```c++
using  funCallback = function<int(int,int)>;
Test t1;
function<int(int)> f1 = bind(&Test::AddC,t1,placeholders::_1,2);
f1(1);
```

我们明显可以看到填写具体的参数之后，类型就从 `function<int(int,int)>` 变成 `function<int(int)>`。所以，当我们想对 function 对象的参数列表进行操作（填写默认参数，以减少后续调用所需填写的参数）可以想到用 bind来辅助。但是 bind 是不可以对 function 对象的返回值进行操作的，除非是隐式转换，比方说原 function 对象 是 int 返回值，你这边的新 function 对象返回值是 float 是可以编译通过的。

```c++
using  funCallback = function<int(int,int)>;
Test t1;
function<float(int)> f1 = bind(&Test::AddC,t1,placeholders::_1,2);  // 返回值存在隐式转换可以编译通过
function<int*(int)> f1 = bind(&Test::AddC,t1,placeholders::_1,2);   // 返回值不存在隐式转换无法编译通过
```

## 回调机制的实现

function 可以存储任意类型的可调用对象，bind 可以将一个可调用对象转换成新的可调用对象，那么我们可以通过 bind 将一个可调用对象转换成指定的 funtcion 类型。function 和 bind 是 C++11 中实现回调机制的常用组合。

从网上借鉴一句话来阐述：**回调函数是你写一个函数，让预先写好的系统来调用。你调用系统的函数，是直调。让系统调用你的函数，就是回调**。比方说网络库中，你传递一个回调函数，这个回调函数的功能是处理连接上客户端发送的消息，那么这个回调函数究竟什么时候调用并不用你管，这是由系统（网络库）来选择合适的时机进行调用的，毕竟网络库要管理很多的连接。

```c++
#include <iostream>
#include <functional>
#include <vector>

// 定义回调类型
using Callback = std::function<void(int)>;

class EventSystem {
public:
    // 注册回调函数
    void registerCallback(Callback cb) {
        callbacks.push_back(cb);
    }

    // 触发事件，调用所有注册的回调函数
    void triggerEvent(int value) {
        for (const auto& cb : callbacks) {
            cb(value);
        }
    }

private:
    std::vector<Callback> callbacks;
};
```

这个系统可以注册回调函数（即存储回调函数），还支持执行回调函数。作为用户如果想要系统执行某个功能，你只需要把功能函数（可调用对象）传递给系统即可，系统会自行安排调用时机（即调用 triggerEvent 方法）。

```c++
void myCallbackFunction(int value) {
    std::cout << "Callback function called with value: " << value << std::endl;
}

int main() {
    EventSystem eventSystem;

    // 注册普通函数回调
    eventSystem.registerCallback(myCallbackFunction);

    // 触发事件（系统执行回调）
    eventSystem.triggerEvent(42);

    return 0;
}
```

