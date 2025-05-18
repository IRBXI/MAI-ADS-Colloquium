[[c++]]
# Глобальная перегрузка
Логирующий оператор new:
```cpp
#include <iostream>
void* operator new(size_t n){
    std::cout << "Allocated " << n << " bytes\n";
    return malloc(n);
}
void operator delete(void* ptr, size_t bytes){
    std::cout << "Deallocated " << bytes << " bytes\n";
    free(ptr);
}
int main(){
    int* x = new int(5);
    delete x;
    return 0;
}
```
# Как работает оператор new
Оператор new:
1. Выдели память для объекта
2. Вызови конструктор на этой памяти
Оператор delete:
- Вызови деструктор для объекта
- Очисти память
Перегруженный оператор new занимается только выделением памяти.
# Оператор new для класса
Если определили оператор new, то должны определить и оператор delete.
```cpp
#include <iostream>
struct S{
    int x;
    int y;
    S(int x, int y): x(x), y(y){}
    void* operator new(size_t n){
        std::cout << "Allocated " << n << " bytes\n";
        return malloc(n);
    }
    void operator delete(void* ptr, size_t n){
        std::cout << "Deallocated " << n << " bytes\n";
    }
};
int main(){
    S* x = new S(2,3);
    delete x;
    return 0;
}
```
# placement new
placement new получает указатель на выделенную память и вызывает на ней конструктор.
Как перегрузить:
```cpp
void* operator new(size_t n, void* ptr){
	return ptr;
}
```