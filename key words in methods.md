1. Зачем нужны const методы? Какие у них свойства?

const методы нужны, чтобы явно указать, что метод не меняет состояние обьекта.

От const обьектов можно вызывать только const методы.

Внутри const методов нельзя вызывать не const методы, а также нельзя изменять обьект. 

2. Что такое ключевое слово mutable? Когда можно использовать?

Ключевое слово mutable позволяет изменять поле обьекта даже если сам обьект является константным, это также позволяет изменять это поле внутри const методов. 

Один из примеров импользования mutable это кеширование результатов какого-то ресурсозатратного метода, сам метод может быть const, но тем не менее внутри него мы хотим сохранять результат каких-то вычислений, то есть изменять поле обьекта, без ключевого слова mutable это не получится. 

Еще один пример это использование mutable с мьютексами, даже есть правило "M&M" - mutable and mutex go together

3. Что такое static поле?

Аналогично с другими переменными помечеными static это переменная, которая инициализируется один раз в начале программы 

(static поля существуют независимо от того есть у нас действительно обьекты такого типа или нет!!!)

Чтобы получить доступ к этой переменной можно использовать такой синтаксис: 

```cpp
class S {
    static int n = 3;
};

int x = S::n + 3; // 6
```

4. Что такое static метод?

Статический метод это функция, которая ассоциируется с обьектов, но не вызывается от какого-то его инстанса и не имеет доступа к его полям, так как не имеет внутри себя указатель this. 

Статические методы так же можно вызывать даже если у нас ни одного обьекта этого типа, синтаксис аналогичен получению статических полей (::)

5. Что будет при delete nullptr? 

Ничего не произойдет. Вызов оператора delete от nullptr полностью безопасен.  

6. Ключевое слово explicit - зачем и когда использовать?

Ключевое слово explicit используется для того, чтобы сказать, что мы хотим, чтобы обьект данного типа можно было получить только при помощи явного каста или вызова соответствубщего конструктора. 

Code style говорит, что в большинстве случаев конструктуры с одним аргументов должны быть explicit

Думаю, что этот пример с cppreference поможет понять, что имеется ввиду: 
```cpp
struct A
{
    A(int) {}      // converting constructor
    A(int, int) {} // converting constructor (C++11)
    operator bool() const { return true; }
};
 
struct B
{
    explicit B(int) {}
    explicit B(int, int) {}
    explicit operator bool() const { return true; }
};
 
int main()
{
    A a1 = 1;      // OK: copy-initialization selects A::A(int)
    A a2(2);       // OK: direct-initialization selects A::A(int)
    A a3 {4, 5};   // OK: direct-list-initialization selects A::A(int, int)
    A a4 = {4, 5}; // OK: copy-list-initialization selects A::A(int, int)
    A a5 = (A)1;   // OK: explicit cast performs static_cast
    if (a1) { }    // OK: A::operator bool()
    bool na1 = a1; // OK: copy-initialization selects A::operator bool()
    bool na2 = static_cast<bool>(a1); // OK: static_cast performs direct-initialization
 
//  B b1 = 1;      // error: copy-initialization does not consider B::B(int)
    B b2(2);       // OK: direct-initialization selects B::B(int)
    B b3 {4, 5};   // OK: direct-list-initialization selects B::B(int, int)
//  B b4 = {4, 5}; // error: copy-list-initialization does not consider B::B(int, int)
    B b5 = (B)1;   // OK: explicit cast performs static_cast
    if (b2) { }    // OK: B::operator bool()
//  bool nb1 = b2; // error: copy-initialization does not consider B::operator bool()
    bool nb2 = static_cast<bool>(b2); // OK: static_cast performs direct-initialization
 
    [](...){}(a4, a5, na1, na2, b5, nb2); // suppresses “unused variable” warnings
}
```

