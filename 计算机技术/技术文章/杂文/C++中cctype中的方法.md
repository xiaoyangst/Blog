:pushpin:**`std::isalpha`**: 判断是否为字母

:pushpin:**`std::isdigit`**: 判断是否为数字

:pushpin:**`std::isalnum`**: 判断是否为字母或数字

**`std::iscntrl`**: 判断是否为控制字符

**`std::isgraph`**: 判断是否为除空格外的可打印字符

:pushpin:**`std::islower`**: 判断是否为小写字母

:pushpin:**`std::isupper`**: 判断是否为大写字母

**`std::isprint`**: 判断是否为可打印字符（包括空格）

**`std::ispunct`**: 判断是否为标点符号

**`std::isspace`**: 判断是否为空白字符（如空格、换行、制表符等）

**`std::isxdigit`**: 判断是否为十六进制数字

如上这些方法是用来判断是否为某种字符的，如果是返回true，如果不是返回false

通常我们在处理哪种需要判断给定的字符串中有多少数字、多少字母等会非常好用

下面再介绍此头文件中的另外两个方法，即大小写字母转换

**`std::tolower`**：转换为小写

**`std::toupper`**：转换为大写