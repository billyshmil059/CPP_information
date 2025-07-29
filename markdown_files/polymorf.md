
# Перегрузка функций

В отличие от С в С++ можно определить несколько функций с одинаковым именем, но разными параметрами.
```c++
double square(double d) { return d* d; }

int square(int i) { return i * i; }
```

При вызове функции по имени будет произведен поиск наиболее подходящей функции:
```c++
int a = square(4); // square(int)
double b = square (3.14); // square (double)
double c = square(5); // square (int)
int d = square(b); // square (double)
float e = square (2.71f); // square (double)
```


## Перегрузка методов


Аналогично перегрузке функций в С++ существует перегрузка методов.

```c++
struct Vector2D {
    Vector2D(double x, double y) : x(x), y(y) {}

    Vector2D mult (double d) const { return Vector2D (x*d, y * d); }

    double mult (Vector2D const& p) const { return x * p.x + y * p.y; }

    double x, y;
};

int main() {
    Vector2D p(1, 2);
    Vector2D q = p.mult(10); // (10, 20)
    double r = q.mult(p) // 50
}

```

# Переопределение методов (overriding)

```c++
struct Person {
    string name() const { return name_; }
    ...
}


struct Professor : Person {
    string name() const {
        return "Prof." + Person::name();
    }
};

int main() {
    Professor pr("Stroustrup");
    cout << pr.name () << endl; // Prof. Stroustrup
    Person *p = &pr;
    cout << p->name() << endl; // Stroustrup
}
```

Заметим, что при вызове метода name() через указатель на базовый класс будет вызван метод name() с базового класса.

Если нас это не устраивает, то в С++ есть специальный механизм виртуальных методов.

Если мы определим метод name() базового класса со словом virtual, то есть так:
```c++
virtual string name() const {return name_}
```
То следующий код начнет работать уже иначе:

```c++
int main() {
    Professor pr("Stroustrup");
    cout << pr.name () << endl; // Prof. Stroustrup
    Person *p = &pr;
    cout << p->name() << endl; //  Prof. Stroustrup
}
```


# Чистые виртуальные(абстрактные методы)

Рассмотрим следующий код

```c++
struct Person {
    virtual string occupation() const = 0;
    ...
};

struct Student : Person {
    string occupation () const { return "student";}
    ...
};

struct Professor : Person {
    string occupation() const { return "professor";}
    ...
};

```
Здесь метод
```c++
virtual string occupation() const = 0;
```

является абстрактным. `... = 0` означает, что данный метод не имеет реализации.

То есть определение абстрактного метода - виртуальные метод не имеющий реализации.
В С++ нельзя создавать объекты классов, которые имеют абстрактные методы.

Классы с абстрактными методами называются абстрактными.

Также можно создавать деструкторы с virtual в начале, то есть виртуальные деструкторы.



# Интерфейсы

Интерфейс — это абстрактный класс, в котором все методы чисто виртуальные и нет состояния (нет полей-членов, кроме разве что static).

Он задаёт контракт, то есть что должен уметь объект, но не определяет, как именно это делается.

Пример:
```c++
class IShape {
public:
    virtual double area() const = 0;
    virtual void draw() const = 0;

    virtual ~IShape() = default; // всегда нужен виртуальный деструктор
};
```

Этот класс нельзя создать напрямую (он абстрактный), но можно наследоваться:

```c++
class Circle : public IShape {
public:
    Circle(double r) : radius(r) {}

    double area() const override {
        return 3.14 * radius * radius;
    }

    void draw() const override {
        std::cout << "Drawing circle\n";
    }

private:
    double radius;
};
```


# Полиморфизм

Полиморфизм - возможность единообразно обрабатывать разные типы данных.
- Перегрузка функций. Выбор функции происходит в момент компиляции на основе типов аргументов функции, статический полиморфизм.
- Виртуальные методы. Выбор метода происходит в момент выполнения на основе типа объекта, у которого вызывается виртуальный метод, динамический полиморфизм.


# Виртуальное наследование

Представим такую иерархию классов:


```c++
#include <string>


struct A          {
    std::string a = "0";
};

struct B : A  {
    B() {
        a="1";
    }
};
struct C : A  {
    C() {
        a="2";
    }
};


struct D : C, B   {

};

#include <iostream>
int main()
{
    D d;
    std::cout << d.a << '\n';
}
```


Структуры `C` и `B` наследуются от `A`, каждый экземпляр `C` и `B` будут иметь свое поле `a`.

Когда мы наследуем `D` от `C` и `B`, то класс `A` наследуется в `D` два раза: один раз от `B` и второй раз от `C`.

Из-за чего возникает два поля `а` один раз от `B` и второй раз от `C` и появляется неопределенность какое именно поле `a` нам нужно.

Чтобы такого не было ввели виртуальное наследование.
Для этого нужно дописать ключевое слово `virtual` вот так:
```c++
struct C : virtual A  {
``` 
и 

```c++
struct B : virtual A  {
```
