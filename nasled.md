# Наследование классов.


Наследование — это механизм, позволяющий создавать производные классы, расширяя уже существующие.

```c++
struct Person {
    string name() const { return name_; }
    int age() const { return age_; }
private:
    string name_;
    int age_;
};

struct Student : Person {
    string university() const { return uni_; }
private:
    string uni_;
};
```


Создание/удаление объекта производного класса


```c++
struct Person {
    Person(string name, int age)
    : name_(name), age_(age)
    {}
    ...
};

struct Student : Person {
    Student (string name, int age, string uni)
    : Person(name, age), uni_(uni)
    {}
    ...
};

```



## Модификатор доступа protected

Класс-наследник не имеет доступа к private-членам родительского класса.
Для определения закрытых членов класса доступных наследникам используется модификатор protected.

```c++
struct Person {
...
protected:
    string name;
    int age_;
};

struct Student : Person {
... // можно менять поля name_ и age_
};

```


# Агрегирование VS Наследование

Агрегирование — это включение объекта одного класса в качестве поля в другой.
Наследование устанавливает более сильные связи между классами, нежели агрегирование:
приведение между объектами,
доступ к protected членам.
Если наследование можно заменить легко на агрегирование, то это нужно сделать.

# Модификаторы доступа при наследовании

| Модификатор наследования | public-члены базового класса становятся | protected-члены становятся | private-члены становятся |
| ------------------------ | --------------------------------------- | -------------------------- | ------------------------ |
| `public`                 | `public` в производном                  | `protected`                | недоступны               |
| `protected`              | `protected`                             | `protected`                | недоступны               |
| `private`                | `private`                               | `private`                  | недоступны               |



Общий вид:
```c++
class Derived : [модификатор] Base {};
```
Пример:

```c++
class Base {
public:
    int a;
protected:
    int b;
private:
    int c;
};

class PublicDerived : public Base {
    // a — public
    // b — protected
    // c — недоступен
};

class ProtectedDerived : protected Base {
    // a — protected
    // b — protected
    // c — недоступен
};

class PrivateDerived : private Base {
    // a — private
    // b — private
    // c — недоступен
};

```


