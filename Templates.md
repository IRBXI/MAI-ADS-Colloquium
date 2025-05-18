1. Что такое template и зачем он нужен?
template - с++ сущность, которая обозначает:
- семейство классов
- семейство функций
- семейство переменных(с c++14)
- псевдоним семейству типов(using)
Он нужен для того чтобы один и тот же код работал для нескольких типов или констант(в случае, если шаблонным параметром является переменная, известная на этапе компиляции).
Пример:
```cpp
#include <iostream>
template<typename T>
T max(T a, T b){
    if(a > b){
        return a;
    }
    return b;
}
int main(){
    std::cout << max(1, 10) << '\n';
    return 0;
}
```
2. Чем `Typename T` отличается от `Class T`?
В перечислении шаблонных параметров ничем. [ссылка](https://en.cppreference.com/w/cpp/language/template_parameters)
>type-parameter-key|-|either `typename` or `class`. There is no difference between these keywords in a type template parameter declaration

3. Как работает перегрузка шаблонных функций?
Основное правило, которое там действует это "явное лучше, чем неявное". И еще один важный нюанс: в начале выборки специализации не учитываются.
4. Как явно вызвать шаблонную функцию?
Подставить шаблонные параметры.
```cpp
#include <iostream>
template<class T>
T max(T a, T b){
    std::cout << 1 << '\n';
    if(a > b){
        return a;
    }
    return b;
}
int max(int a, int b){
    std::cout << 2 << '\n';
    if(a > b){
        return a;
    }
    return b;
}
int main(){
    std::cout << max<int>(1, 10) << '\n'; //1
    return 0;
}
```
5. Что такое специализация шаблона?
Специализация шаблона - инструмент, позволяющий явно изменить шаблонный код в зависимости от переданных параметров(так написано в специализации).
Пример полной специализации:
```cpp
#include <iostream>
template<class T>
T max(T a, T b){
    std::cout << 1 << '\n';
    if(a > b){
        return a;
    }
    return b;
}
template<> //ВАЖНО
int max<int>(int a, int b){
    std::cout << 2 << '\n';
    if(a > b){
        return a;
    }
    return b;
}
int main(){
    std::cout << max(1, 10) << '\n'; //2
    return 0;
}
```
6. Что такое частичная специализация?
Частичная специализация - это инструмент, позволяющий изменять шаблонный код в зависимости от **части** шаблонных параметров. Функции не могут быть частично специализированы, а классы могут.
Пример с cppreference:
```cpp
template<class T1, class T2, int I>
class A {};             // primary template
 
template<class T, int I>
class A<T, T*, I> {};   // #1: partial specialization where T2 is a pointer to T1
 
template<class T, class T2, int I>
class A<T*, T2, I> {};  // #2: partial specialization where T1 is a pointer
 
template<class T>
class A<int, T*, 5> {}; // #3: partial specialization where
                        //     T1 is int, I is 5, and T2 is a pointer
 
template<class X, class T, int I>
class A<X, T*, I> {};   // #4: partial specialization where T2 is a pointer
```
7. Шаблоны можно создавать только от типов?
Нет, их еще можно создавать от констант. Пример:
```cpp
// Шаблонный класс с non-type параметром (целочисленное значение)
template <typename T, int Size>
class FixedArray {
private:
    T data[Size];  // Массив фиксированного размера

public:
    // Конструктор по умолчанию
    FixedArray() = default;

    // Метод для доступа к элементам
    T& operator[](int index) {
        return data[index];
    }

    // Константная версия
    const T& operator[](int index) const {
        return data[index];
    }

    // Получение размера
    constexpr int size() const { return Size; }
};
```
8. - Можно ли в такие типы подставлять не const значения?
Нельзя
9. - Как убрать от объекта всё лишнее - `*`, `&`, `const` и т.д?
```cpp
#include <iostream>
template <typename T> struct remove_ref {
  using type = T;
};

template <typename T> struct remove_ref<T &> {
  using type = T;
};

template <typename T> struct remove_ptr {
  using type = T;
};

template <typename T> struct remove_ptr<T *> {
  using type = T;
};

template <typename T> struct remove_const {
  using type = T;
};

template <typename T> struct remove_const<const T> {
  using type = T;
};

int main() {
  typename remove_const<const int>::type a = 5;
  typename remove_ptr<int*>::type b = 5;
  typename remove_ref<int&>::type c = 5;
  std::cout << a + b + c << '\n';
}
```
Либо можно использовать функции из библиотеки `type_traits`.
`std::remove_reference<typename Tp>
`std::remove_const<typename Tp>`
`std::remove_pointer<typename Tp>`
10. Как работать с шаблонами от множества аргументов?
Нужно использовать `parameter pack`.
`parameter pack` принимает 0 и более шаблонных параметров.
Пример:
```cpp
#include <iostream>
void print() {} //конец рекурсии
template<typename T, typename... Args> //обозначает пак шаблонных параметров
void print(const T& first, const Args&... args){ //распаковка
    std::cout << first << '\n';
    print(args...); //распаковка в аргументы
}
```

