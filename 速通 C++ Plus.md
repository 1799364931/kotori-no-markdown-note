# 速通 C++ Plus (只记录怪怪的知识)

## 1.OOP

### 1. 重载输出流

可以通过重载 ``operator << () `` 对输出流进行重载 

返回值用引用是为了避免拷贝(引用确实有这个好处喵) 

```cpp
ostream & operator << (ostream & os , const c_name & obj){
    os << " ";
    return os;
}
```

### 2. 类的类型转换

#### 1. 转换构造函数

```cpp
class Test1 {
    public :
    	Test(double d);
};

class Test2 {
    public :
    	Test2(double d);
    	Test2(long l);
};

class Test2 {
    public :
    	explicit Test3(double d);
};

int main(){
  Test1 t1 = 123.4; // 这是合法的
  Test2 t2 = 123; //这是不合法的 因为存在二义性 123可以被转换为两种情况
  Test2 t2_ = 123.4; //这是合法的 不存在二义性
  Test2 t2__ = (double) 123 ; //这也是合法的
  Test3 t3 = 123; //这是不合法的 因为该类的构造函数不能被隐式转换
  Test3 t3_ = Test3(123.4); //合法 显式构造
}

```

#### 2. 转换函数

如果希望实现 类到其他类型的转换，需要实现转换函数`` operator typeName()``

```cpp
class Test1 {
    public :
    double data ;
    Test1 (double d){
        Test1 data = d;
    }
    
    opeartor double(){
        return data;
    }; 
    
    opeartor int(){
        return (int)data;
    }; 
    
    friend const opeeator + (const Test1 & t1 , const Test1 &t2);
};

Test1 opeeator + (const Test1 & t1 , const Test1 &t2){
    return Test1(t1.data,t2.data);
}



int main(){
    Test1 t1(123.4);
    double d1 = t1; //这是合法的 可以自动推断出是double
    long l1 = t1; //这是不合法的 因为double和int都可以进一步转换long 具有二义性
    auto t2 = t1+123.4 ;
    //这是不合法的 ， 因为不知道是将t1转换为double还是将123.4转换为Test1做运算，所以必须将
    //转换构造函数和转换函数其中一个声明为explicit才能避免二义性
    //或者不用友元函数 ， 使用成员函数重载
    
   	//见P343
}

```

