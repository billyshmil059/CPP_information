# Шаблоны

Для чего вообще нужны шаблоны? Предположим мы хотим создать класс динамического массива и хотим использовать его 
с разными типами данных.
Тогда бы нам пришлось сначала создать класс ArrayInt для элементов типа int, потом ArrayFlt для flout и так далее для всех нужных
в программе типов данных.

В таком случае бы у нас получилось много ПОЧТИ одинаковых классов.

Примеры классов ArrayInt и ArrayFlt:

```c++
struct ArrayInt {
    explicit ArrayInt(size_t size) : data_(new int [size]), size_(size) {}
    
    ~ArrayInt() { delete [] data_;}
    
    size_t size() const { return size; }
    int operator[](size_t i) const { return data_[i]; }
    int& operator[](size_t i) { return data_[i]; }
private:
    int* data_;
    size_t size_;
};
```

```c++
struct ArrayFlt {
    explicit ArrayFlt(size_t size) : data_(new float[size]), size_(size) {}
    
    ~ArrayFlt() { delete [] data_;}
    
    size_t size() const { return size; }
    float operator[](size_t i) const { return data_[i]; }
    float& operator[](size_t i) { return data_[i]; }
private:
    float* data_;
    size_t size_;
};
```


Это можно заменить на такое благодаря шаблонам:

```c++
template <class Type>
struct Array {
    explicit Array(size_t size) : data_(new Type[size]), size_(size) {}
    
    ~Array()
    { delete() data; }
    
    size_t size() const 
    { return size_; }
    
    Type operator[](size_t i) const
    { return data_[i]; }
    
    Type& operator[](size_t i)
    { return data_[i]; }
    ...
private:
    Type* data_;
    size_t size_;
};
```

Также шаблоны можно применять к функциям.

Пример применения:

```c++
template <typename Num>
Num square(Num x) { return x*x; }
```


