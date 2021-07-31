# **有关C++语言的一些问题**

1. c++源码的编译过程
2. 内存管理、堆和栈的区别、内存对齐、类对象的大小
3. static关键字作用、变量的种类和区别
4. 内存泄漏是什么？如何防止内存泄漏
5. 智能指针的类型，各类型的特点
6. shared_ptr的原理，简单代码实现
```
class shared_ptr{
private:
    int* _ptr;
    size_t * _count;

public:
    shared_ptr(int * ptr=nullptr ):_ptr(ptr){
        if (_ptr)
            _count = new size_t(1);
    }

    ~shared_ptr(){
        *_count--;
        if (*_count==0){
            delete _ptr;
            delete _count;
        }
    }

    shared_ptr(const shared_ptr& rhs){
        if (this != &rhs){
            this->_ptr = rhs._ptr;
            this->_count = rhs._count;
            (*this->_count)++;
        }
    }

    shared_ptr& operator=(const shared_ptr& rhs){
        if (this->_ptr == rhs._ptr)
            return *this;
        if (_ptr){
            (*_count)--;
            if ((*_count)==0){
                delete this->_ptr;
                delete this->_count;
            }
        }
        this->_ptr = rhs._ptr;
        this->_count = rhs._count;
        (*this->_count)++;
        return *this;
    }

    size_t print_count(){
        return *this->_count;
    }
};
```

7. shared_ptr存在循环引用的问题
```
class B;
class A
{
public:
　　shared_ptr<B> m_b;
};
 
class B
{
public:
　　shared_ptr<A> m_a;
};
 
int main()
{
　　while (true)
　　{
　　　　shared_ptr<A> a(new A); //new出来的A的引用计数此时为1
　　　　shared_ptr<B> b(new B); //new出来的B的引用计数此时为1
　　　　a->m_b = b; //B的引用计数增加为2
　　　　b->m_a = a; //A的引用计数增加为2
　　}
 
　　//b先出作用域，B的引用计数减少为1，不为0，所以堆上的B空间没有被释放
　　//且B持有的A也没有机会被析构，A的引用计数也完全没减少
 
　　//a后出作用域，同理A的引用计数减少为1，不为0，所以堆上A的空间也没有被释放
}
```

8 . C++11的新特性
* auto自动类型推导：编译器在编译阶段通过初始值来推导变量的类型，并且aotu变量必须要有初始值
* decltype类型推导，
* lambda表达式是一个可调用对象：[捕获列表](参数列表)->返回值类型{ 函数体； }
* 范围for的使用
* 右值引用：绑定到右值的引用，右值指的是即将销毁的对象，而常规引用则称为左值引用（左值和右值，以及左值引用和右值引用的语义）
* 智能指针
* delete函数和default函数
* move()函数：可以获得绑定到左值上的右值引用
