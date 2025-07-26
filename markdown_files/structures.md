Это типо определение структуры.
В C++ (а также в C) ключевое слово struct служит для объявления пользовательского агрегатного типа данных, то есть для объединения в одном объекте нескольких полей (членов данных), обычно тесно связанных между собой. 
## Объявление и определение структуры:
```c++
struct Point {
    int x;      // поле данных
    int y;

    void move(int dx, int dy) {  // метод структуры
        x += dx;
        y += dy;
    }
};
```

- Ключевое слово struct указывает компилятору, что мы объявляем структуру.

- Внутри тела структуры можно определять:

    1. поля данных (члены данных) любого типа;

    2. функции-члены (методы), включая конструкторы, деструкторы, операторы и т.п.



## Конструкторы и деструкторы

Конструкторы - это методы для инициализации структур.
```c++
struct Point {
    Point() {
        x = y = 0 ; // 1-ый способ инициализации
    }
    Point (double x, double y) { // 2-ой способ инициализации
        this->x = x;
        this->y = y;
    }
    double x;
    double y;
};
```
```c++
Point akk; 1-ый способ
Point akk(2, 3); 2-ой способ
```

## Cписок инициализации

```c++
struct Foo {
    int   x;
    const int y;
    std::string s;
    
    Foo(int _x, int _y, const std::string& _s)
      : x(_x)         // инициализируем x значением _x
      , y(_y)         // инициализируем const-поле y
      , s(_s)         // инициализируем строку через её конструктор копирования
    {
        // тело конструктора — пока пустое
    }
};

```
Зачем может быть нужен список инициализации?
Затем, что константы и переданные ссылки мы не можем инициализировать в теле конструктора.
Также список инициализации быстрее чем инициализация в теле конструктора.


## Значения по умолчанию

```c++
struct Point {
    Point (double x = 0 , double y = 0 )
        : x(x), y(y)
    {}
    double x;
    double y;
};

Point p1; // x = 0 y = 0
Point p2(2); x = 2, y = 0
Point p3(3,4); x = 3 y = 4
```

## Конструктор от одного параметра и explicit
```c++
struct Point {
    explicit Point (double x = 0, double y = 0 )
    : x(x), y(y)
    {}
    double x;
    double y;
};
```

```
Point p1; //  (0, 0)
Point p2(2); // (2, 0)
Point p3 (3,4); // (3, 4)
Point p4 = 5 // error c explicit и (5, 0) без explicit 
```

explicit это запрет на работу конструктора через "=";


## Деструкторы

Деструктор - это метод, который вызывается при удалении структуры, генерируется компилятором.


```c++
struct IntArray {
    explicit IntArray (size_t size)
        : size (size)
        data (new int [size])
    { }
    ~IntArray() {
        delete [] data;
    }
    size_t size;
    int * data;
};
```
## Терминология
- Cтруктуру с методами, конструкторами и деструктором называют классом.
- Экземпляр(значение) класса называют объектом.

## Модификаторы доступа

По умолчанию мы можем обращаться ко всем методам и полям структуры из любой части программы. 

Однако благодаря модификаторам доступа мы можем запретить обращаться к полям и методам из внешних функций.

```c++
struct IntArray {
    explicit IntArray (size_t size)
        : size_(size), data_(new int[size])
    {}
    ~IntArray() { delete [] data_; }
    
    int & get(size_t i) { return data[i]; }
    
    size_t size () { return size; }
private:
    size_t size_;
    int * data_;
};
```

## Ключевое слово class

Ключевое слово struct можно заменить на class, тогда поля и методы по умолчанию будут private.

```c++
class IntArray {
public:
    explicit IntArray (size_t size)
        : size_(size), data_(new int [size])
    {}
    ~IntArray() { delete () data_; }
    
    int & get (size_t i) { return data_ [i]; }
    size_t size() { return size; }
private:
    size_t size;
    int * data_;
```


# Копирование объектов

Вот разница между присваиванием и копированием.

```c++
struct IntArray {
    ...
private:
    size_t size;
    int * data_;
};
int main() {
    IntArray a1 (10);
    IntArray a2 (20);
    IntArray a3 = a1; // копирование
    a2 = a1; // присваивание
    return 0;
}
```


Компилятор генерирует по умолчанию 4 метода:
1. конструктор по умолчанию
2. конструктор копирования
3. оператор присваивания
4. деструктор

Если потребовалось переопределить конструктор копирования,
оператор присваивания или деструктор, то нужно переопределить и остальные методы из этого списка.

# Примеры конструктора копирования и оператора присваивания

Конструктор копирования
```c++
struct IntArray {
    IntArray (IntArray const& a)
    : size_(a.size), data_(new int [size])
    {
        for (size_t i = 0 ; i != size; ++i) {
            data_[i] = a.data[i];
        }
    }
    ...
private:
    size_t size;
    int * data_;
};
```

Оператор присваивания

```c++
struct IntArray {
    IntArray& operator=(IntArray const& a)
    {
        if (this != &a) {
            delete [] data_;
            size_ = a.size_;
            data_ = new int [size_];
            for (size_t i = 0; i!= size_; ++i)
                data_[i] = a.data_[i];
        }
        return *this;
    }
    ...
}
```

Традиционно оператор присваивания возвращает ссылку.


