# Логические ошибки и исключительные ситуации
1. Логические ошибки.
   - Ошибки в логике работы программы, которые происходят из-за неправильно написанного кода, т.е. это ошибки программиста:
   - выход за границу массива,
   - попытка деления на ноль,
   - обращение по нулевому указателю,
2. Исключительные ситуации.
   - Ситуации, которые требуют особой обработки.
   - Возникновение таких ситуаций поведение программы. -это „нормальное"
   - ошибка записи на диск,
   - недоступность сервера,
   - неправильный формат файла,
   ...

## Выявление логических ошибок на этапе разработки
- Оператор static_assert.
Пример:
```c++
#include <type_traits>
template<class T>
void countdown(T start) {
    static_assert(std::is_integral<T>::value && std::is_signed<T>::value, "Requires signed integral type");
    while (start >= 0) {
        std::cout << start-- << std::endl;
    }
}

```
`std::is_integral<T>::value` и `std::is_signed<T>::value` это булевые функции из <type_traits>, которые 
проверяют класс на целочисленность(с плавающими числами также возвращает true) и знаковость соответственно.


Если это не выполняется, то при запуске программы выбросится ошибка "Requires signed integral type".

`static_assert` работает на этапе компиляции.

Если нужно выбрасывать ошибку во время выполнения(в каком-то случае), то можно использовать `assert()` из 
заголовочного файла "cassert".


## Исключения

В C++ мы можем бросать и ловить исключения их. 

Изучим тему на примерах

Ловля исключений:

```c++
#include <iostream>
#include <stdexcept>

int main()
{
    std::string txt;
    std::cin >> txt;                     // вводим строку

    try {
        int n = std::stoi(txt);          // может бросить
        std::cout << n << "² = " << n*n << '\n';
    }
    catch (const std::invalid_argument&) {   // строка — не число
        std::cerr << "Не число: " << txt << '\n';
    }
    catch (const std::out_of_range&) {       // число не помещается в int
        std::cerr << "Слишком большое число: " << txt << '\n';
    }
}
```


В данной программе мы преобразуем string в int и могут возникнуть некоторые ошибки:
1. Символы в txt включают в себя не только цифры.
2. Размер преобразуемого числа слишком большой и не умещается в int.

Слово `try` по смыслу означает попробовать.
Если что-то в нашем коде в фигурных скобках после `try` выбрасывает ошибку, то далее в `catch` мы ее ловим.

По смыслу catch как if: `если ошибка == (то что в круглых скобках после catch), то выполнить:(то что в фигурных скобках после catch`.

`std::cerr` - это стандартный поток вывода(Как `std::cout`), только для ошибок.

А теперь разберем как бросать ошибки и создавать свои классы ошибок из уже имеющихся:


```c++
#include <iostream>
#include <stdexcept>
#include <limits>

//--------------------------------------------------------------------
// 1. Собственная иерархия исключений
//--------------------------------------------------------------------
class calc_error : public std::runtime_error {        // базовый класс
public:
    using std::runtime_error::runtime_error;          // наследуем конструкторы
};

class divide_by_zero_error : public calc_error {      // деление на ноль
public:
    divide_by_zero_error() : calc_error("divide by zero") {}
};

class overflow_error : public calc_error {            // переполнение int
public:
    overflow_error() : calc_error("integer overflow") {}
};

//--------------------------------------------------------------------
// 2. «Безопасные» арифметические функции
//--------------------------------------------------------------------
int safe_divide(int a, int b)
{
    if (b == 0)
        throw divide_by_zero_error{};
    return a / b;
}

int safe_multiply(int a, int b)
{
    long long tmp = static_cast<long long>(a) * b;     // считаем в 64-битах
    if (tmp > std::numeric_limits<int>::max() ||
        tmp < std::numeric_limits<int>::min())
        throw overflow_error{};
    return static_cast<int>(tmp);
}

//--------------------------------------------------------------------
// 3. Точка входа
//--------------------------------------------------------------------
int main()
{
    try {
        int x = safe_divide(10, 0);                    // бросит divide_by_zero_error
        std::cout << x << '\n';

        int y = safe_multiply(1'000'000, 10'000);      // бросит overflow_error
        std::cout << y << '\n';
    }
    catch (const divide_by_zero_error& e) {            // ловим «узкоспециализированные»
        std::cerr << "Math error: " << e.what() << '\n';
    }
    catch (const overflow_error& e) {
        std::cerr << "Overflow: " << e.what() << '\n';
    }
    catch (const calc_error& e) {                      // общий предок — страховка
        std::cerr << "Calculation error: " << e.what() << '\n';
    }
    catch (const std::exception& e) {                  // «парашют» на всё остальное
        std::cerr << "Unhandled: " << e.what() << '\n';
    }
}

```