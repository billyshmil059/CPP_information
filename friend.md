
# Про ключевое слово friend

Ключевое слово friend позволяет определять для класса дружественные классы, дружественные методы и дружественные функции

Начнем с классов.

```c++
struct String {
    ...
    friend struct StringBuffer;
private:
    char *data_;
    size_t len_;
};

struct StringBuffer {
    void append(String const & s) {
        append(s.data_);
    }

    void append (char const* s) {...}
    ...
};
```

Теперь благодаря `friend struct StringBuffer`
класс StringBuffer может работать с полями и методами класса String будто со своими.

### Дружественные функции
Дружественные функции можно определять прямо внутри описания класса (они становятся inline).

```c++
struct String {
    ...
    friend std:: ostream& operator<<(std::ostream & os, String const & s) {
        return os << s.data_;
    }
private:
    char * data_;
    size_t len_;
};
```


### Дружественные методы

```c++
struct String;

struct StringBuffer {
    void append(String const & s);
    
    void append(char const* s) {...}
    ...
};
struct String {
    ...
    friend void StringBuffer::append(String const& s);
};

void StringBuffer::append(String const & s) {
    append(s.data_);
}
```

