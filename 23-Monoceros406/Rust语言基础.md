---
title: Rust语言基础
date: 2023-11-11 13:34:01
tags: Rust
mathjax: true
---

# Rust语言基础

## 命令行

### 输出

```rust
fn main(){
    let a=12;
    println!("a is {0}, a again is {0}",a);
    println!("{{}}");//输出大括号
}
```

### 详细输出

```rust
#[derive(Debug)] //打印详细信息
struct Rectangle{
    width:u32,
    height:u32,
}
fn main(){
    let rect1=Rectangle{width:30,height:50};
    println!("rect1 is {:?}",rect1);//替换为{:#?}时输出带格式的输出
}
```

### 命令行输入

```rust
use std::io::stdin;
fn main(){
    let mut str_buf=String::new();
    stdin().read_line(&mut str_buff).unwrap();
    printfln!("{}",str_buf);
}
```

示例：

```rust
use std::io::stdin;
fn main(){
    let mut str_buf=String::new();
    stdin().read_line(&mut str_buf).unwrap();
    let sp:Vec<&str>=str_buf.as_str().split(' ').collect();
    let a=sp[0].trim().parse::<i32>().unwrap();
    let b=sp[1].trim().parse::<i32>().unwrap();
    println!("{}+{}={}",a,b,a+b);
}

```

### 命令行传参

法一：

```rust
fn main(){
    let args=std::env::args();
    for arg in args{
        println!("{}",arg)
    }
}
```

法二：

```rust
fn main(){
    let mut args=std::env::args();
    args.next();
    let a=args.next().unwrap().trim().parse<i32>().unwrap();
    let b=args.next().unwrap().trim().parse<i32>().unwrap();
    println!("{}+{}={}",a,b,a+b);
}
```

## 基础语法

### 变量

```rust
let a=123;//不可变变量
let a:i32=123;
let mut a=123;//可变变量

//可以声明后赋初始值
let a;
a=456;

//重影机制
let x=5;
let x=x+1;
let x=x*2;
```

### 静态变量

```rust
static a:i32=123;
fn main(){
    println!("{}",a);
}
```

直接修改静态变量的值：

```rust
static mut VAR:i32=123;
fn main(){
    unsafe{
        VAR=456;
        println!("{}",VAR);
    }
}
```

## 数据类型

### 整数型 浮点型 布尔型 字符型

```rust
i8 u8
i16 u16
i32 u32
i64 u64
i128 u128
isize usize

f64 f32

bool

char
```

### 字符串

String

```rust
let string=String::from("Some string");
//追加
let mut string=String::from("");
string.push('A');
string.push_str("QWER");
//长度
String::from("Hello你好").len();//11 字符编码UTF-8 一个中文字符3
//比较
let a=String::from("...");
let b=String::from("...");
let result=a.eq(&b);//true
String::from("...").eq("...");
String::from("...").eq(String::from("...").as_str());
//截取
let s:String=String::from("RUNOOB");
let ch:char=s.chars().nth(2).unwrap();//N
let sub:&str=&s[0..3];//RUN
```

其实&str比String好使：

```rust
fn main(){
    let s:&str="RUNOOB";
    println!("{}{}{}{}",s.len(),s.eq("RUNOOB"),s.chars().nth(2).unwarp(),&s[0..3]);
}
```

### 元组

```rust
let tup=(500,6.4,1);
let tup:(i32,f64,i32)=(500,6.4,1);

fn main(){
    let tup:(i32,f64,i32)=(500,6.4,1);
    println!("{}",tup.0);
    let (x,y,z)=tup;
    println!("{}",x);
}
```

### 数组

```rust
let a=[1,2,3,4,5];
let length=a.len();
let b=["January","Febrary","March"];
let c:[i32;5]=[1,2,3,4,5];
let d=[3;5];//d=[3,3,3,3,3];
let first=a[0];
let mut a=[1,2,3,4,5]
a[0]=0;
```

## 函数

### 声明

```rust
fn addition(a:i32,b:i32)->i32{
    return a+b;
}
fn main(){
    let sum=addition(100,23);
    println!("{}",sum);
}
```

### 函数表达式

```rust
fn main(){
    let x=4;
    let y={
        let a=x*x*x;
        let b=2*x*x;
        a+b+3
    };
    println!("{}",y);
}
```

### 函数对象

```rust
fn function_one(){
    println!("Funtion1");
}
fn function_two(){
    println!("Function2");
}
fn main(){
    let mut fun:fn();
    fun=function_one;
    fun();
    fun=function_two;
    fun();
}
```

### 闭包/Lambda表达式/匿名函数

```rust
fn main(){
    let fun=|x:i32|->i32{
        return x+1;
    };
    println!("{}",fun(1));
}
```

## 条件语句

### if-else

```rust
fn main(){
    let number=3;
    if number<5 {//必须为bool型，否则违法
        println!("{}",number);
    }else{
        println!("{}",number);
    }
}

fn main(){
    let score=100;
    if score>90 {
        println!("...");
    }else if score>60{
        println!("...");
    }
}
```

### 三元运算符

```rust
fn main(){
    let a=3;
    let number=if a>0 {1} else {-1};
}

fn main(){
    let score=86;
    let branch=if score>90{
        "..."
    }else if score>80{
        ",,,"
    }else{
        "..."
    };
}
```

### match

```rust
fn main(){
    let op=1;
    match op{
        0=>{
            println!("op=0");
        },
        1|2|3|4|5=>{
            println!("...");
        },
        _=>{
            println!("default");
        }
    }
}
```

## 循环结构

### while

```rust
fn main(){
    let mut number=1;
    while number<4{
        number+=1;
    }
}
```

### for

```rust
fn main(){
    for i in 1..5{
        println!("{}",i);
    }
}

fn main(){
    let a=[10,20,30,40,50];
    for i in a.iter(){
        println!("{}",i);
    }
}

fn main(){
    let a=[10,20,30,40,50];
    for i in 0..a.len(){
        println!("a[{}]={}",i,a[i]);
    }
}
```

### loop

```rust
fn main(){
    let s=['R','U','N','O','O','B'];
    let mut i=0;
    loop{
        let ch=s[i];
        if ch=='O'{
            break;//loop内没有break时不会通过编译
        }
        print!("{}",ch);
        i+=1;
    }
}
fn main(){
    let s=['R','U','N','O','O','B'];
    let mut i=0;
    let location=loop{//唯一支持函数表达式的循环
        let ch=s[i];
        if ch=='O'{
            break i;//返回下标
        }
        i+=1;
    };
    println!("{}",location);
}
```

## 所有权

### 参数所有权

所有权转移与复制：

```rust
fn main(){
    let s=String::from("Hello");
    takes_ownership(s);
    let x=5;
    makes_copy(x);
}
fn takes_ownership(some_string:String){//其他类型所有权转移
    println!("{}",some_string);
}
fn makes_copy(some_integer:i32){//原子类型所有权复制
    println!("{}",some_integer);
}
```

原子类型所有权借用：

```rust
fn main(){
    let s=String::from("hello");
    reference(&s);
    println!("主函数{}",s);
}
fn reference(some_string:&String){
    println!("子函数{}",some_string);//只有读取权，无修改权
}
```

### 返回值所有权

```rust
fn main(){
    let s1=gives_ownership();
    let s2=String::from("hello");
    let s3=takes_and_gives_back(s2);
}//s3被释放，s2被转移，s1被释放
fn gives_ownership()->String{
    let some_string=String::from("hello");//some_string生效
    return some_string;
}//some_string所有权被转移出函数
fn takes_and_gives_back(a_string:String)->String{//获a_string所有权
    return a_string;
}//a_string所有权被转移出函数
```

### 引用

获取修改权：

```rust
fn main(){
    let mut s1=String::from("String;");
    add_suffix(&mut s1);
    println!("{}",s1);
}
fn add_suffix(s:&mut String){
    s.push_str("SUFFIX");
}
```

可变变量被不可变借用后，该引用的生命周期结束前不能存在任何借用：

```rust
fn main(){
    let mut s1=String::from("1");
    let r=&s1;
    s1.push_str("2");//错误
}
```

变量被可变借用，在引用的生命周期结束前不能存在任何其他借用：

```rust
fn main(){
    let mut s1=String::from("1");
    let r1=&mut s1;
    let r2=&s1;//错误
}
```

### 解引用

```rust
fn swap(a:&mut i32,b:&mut i32){
    let t=*a;
    *a=*b;
    *b=t;
}
fn main(){
    let mut a=0;
    let mut b=1;
    swap(&mut a,&mut b);+
    println!("a={}b={}",a,b);
}
```

## 切片类型

### 字符串切片

```rust
fn main(){
    let s:String=String::from("broadcast");//切片内容不可改
    let part1:&str=&s[0..5];//合法语法：[5..] [..4] [..]
    let part2:&str=&s[5..9];
    println!("{}={}+{}",s,part1,part2);
}
```

### 数组切片

```rust
fn main(){
    let arr:[i32;5]=[0,1,2,3,4];
    let part:&[i32]=&arr[1..3];
    for i in part.iter(){
        println!("{}",i);
    }
}
```

## 复合类型

### 结构体

```rust
struct Site{
    domain:String,
    name:String,
    natin:String
}
let runoob=Site{
    domain:String::from("aaa"),
    name:String::from("bbb"),
    nation:String::from("ccc")
};
let domain=String::from("xxx");
let bing=Site{
    domain,//使用后所有权被转移，不能对domain进行任何操作
    name:String::from("aaa"),
    ..runoob //其他成员与runoob保持一致
}
```

生命周期显性注释：

```rust
fn longer<'a>(s1:&'a str,s2:&'a str)->&'a str{//声明s1 s2 返回值 的生命周期一样长
    if s2.len()>s1.len(){
        return s2; //返回值不确定
    }else{
        return s1;
    }
}
```

### 结构体方法

```rust
struct Rectangle{
    width:u32,
    height:u32,
}
impl Rectangle{
    fn wider(&self,rect:&Rectangle)->bool{
        self.width>rect.width
    }
}
fn main(){
    let rect1=Rectangle{width:30,height:50};
    let rect2=Rectangle{width:40,height:20};
    println!("{}",rect.wider(rect2));//false
}
```

### 元组结构体

```rust
fn main(){
    struct Color(u8.u8,u8);
    let black=Color(0,0,0);
    println!("{},{},{}",black.0,black.1,black.2);
}
```

### 单元结构体

```rust
struct UnitStruct;//无成员
```

### 枚举类

```rust
enum Book{
    Papery(u32),
    Electronic{url:String},
}
fn main(){
    let book=Book::Papery{index:1001};
    match book{
        Book::Papery(index)=>{
            println!("Papery book {}",index);
        },
        Book::Electronic{url}=>{
            println!("E-book {}",url);
        }
    }
}
```

### if-let语法

```rust
enum Book{
    Papery(u32),
    Electronic{url:String},
}
fn main(){
    let book=Book::Electronic(String::from("url"));
    if let Book::Papery(index)=book{
        println!("Papery {}",index);
    }else{
        println!("No");
    }
}
```

### 枚举类方法

```rust
enum Singnal{
    Red,
    Yellow,
    Green
}
impl Signal{
    fn yellow(&mut self){
        *self=Signal::Yellow;
    }
}
fn main(){
    let mut signal=Signal::Red;
    signal.yellow();
}
```

## 泛型

### 泛型函数

```rust
fn get_last<T>(array:&[T])->&T{
    &array[array.len()-1]
}
fn main(){
    let a=["aaa","bbb","ccc"];
    println!("{}",get_last::<&str>(&a));
}
```

### 泛型结构体

```rust
struct Point<T>{
    x:T,
    y:T
}
let point=Point::<i32>{
    x:1,
    y:2
}
let p1=Point(x:1,y:2);//支持自动判断类型
```

### 泛型枚举类

```rust
enum Shape<T>{
    Rectangle(T,T);
    Cube(T,T,T);
}
let s1=Shape::Rectangle(1,2);//支持自动判断类型
let s3:Shape<i32> =Shape::Rectangle(1,2);
```

### impl泛型

#### 对泛型类实现方法

```rust
struct Point<T>{
    x:T,
    y:T
}
impl<T> Point<T>{
    fn get_x(&self)->&T{
        &self.x
    }
    fn get_y(&self)->&T{
        &self.y
    }
}
fn main(){
    let point=Point(x:3.0,y:4.0);
    println!("{},{}",point.get0_x(),point.get_y());
}
```

#### 对具体类实现方法

```rust
struct Point<T>{
    x:T,
    y:T
}
impl Point<f64>{
    fn get_x(&self)->f64{
        self.x
    }
    fn get_y(&self)->f64{
        self.y
    }
}
```

#### 泛型方法

```rust
struct Data<A,B>{
    x:A,
    y:B
}
impl<A,B> Data<A,B>{
    fn mix<C,D>(self,other:Data<C,D>)->Data<A,D>{
        Data{
            x:self.x,
            y:other.y,
        }
    }
}
fn main(){
    let a=Data{
        x:123.45,
        y:"67890"
    };
    let b=Data{
        x:9876,
        y:String::from("54321")
    };
    println!("{:?}",a.mix(b));//x:123.45 y:"54321"
}
```

## 错误处理

### 不可恢复错误

```rust
fn main(){
    panic!("error occurred");
}
```

### Result枚举类

```rust
fn divide(a:f64,b:f64)->Result<f64,&'static str>{
    if b!=0.0{
        Result::Ok(a/b)
    }else{
        Result::Err("divided by zero")//输出
    }
}
fn main(){
    let result=divide(1.0,0.0);
    match result{
        Ok(value)=>{
            println!("{}",value);
        },
        Err(err)=>{
            println!("{}",err);
        }
    }
}
```

unwrap方法：

```rust
let result=divide(1.0,0.0).unwrap();//发生错误时直接结束进程
```

expect方法：

```rust
let result=divide(1.0,0.0).expect("出错!");//发生错误时输出并结束进程
```

### 可恢复错误的传递

```rust
fn sqrt(x:f64)->Result<f64,&'static str>{
    if x>=0.0{
        Result::Ok(x.sqrt());
    }else{
        Result::Err("...");
    }
}
fn is_prime(x:u32)->Result<f64,&'static str>{
    let result=sqrt(x as f64)?;//?运算符：出现错误时is_prime函数直接返回sqrt函数返回的Result::Err对象
    let t=(result+1.0).ceil() as u32;
    for i in 2..t{
        if i==x{
            continue;
        }
        if x%i==0{
            return Result::Ok(0.0);
        }
    }
    return Result::Ok(1.0);
}
```

### Error类型

```rust
use std::io;
use std::io::Read;
use std::fs::File;
fn read_text_from_file(path:&str)->Result<String,io::Error>{
    let mut f=File::open(path)?;
    let mut s=String::new();
    f.read_to_string(&mut s)?;
    Ok(s);
}
fn main(){
    let str_file=read_text_from_file("hello.txt");
    match str_file{
        Ok(s)=>println!("{}",s);
        Err(e)=>{
            match e.kind(){
                io::ErrorKind::NotFound=>{
                    println!("没有这个文件");
                },
                io::ErrorKind::PermissionDenied=>{
                    println!("权限不够");
                },
                _=>{
                    println!("其他错误");
                }
            }
        }
    }
}
```

## 空引用

### Option枚举类

```rust
fn index_of(arr:&[i32],em:i32)->Option<usize>{
    let mut i:usize=0;
    while i<arr.len(){
        if arr[i]==em{
            return Option::Some(i);
        }
        i+=1;
    }
    return Option::None;
}
fn main(){
    let arr=[1,2,3,4,5];
    let index=index_of(&arr,3);
    if let Some(i)=index{
        println!("{}",i);
    }else{
        println!("元素没找到");
    }
}
```

支持unwrap方法：

```rust
fn main(){
    let arr=[1,2,3,4,5];
    let index=index_of(&arr,3).unwrap();
    println!("{}",index);
}
```

支持expect方法：

```rust
fn main(){
    let arr=[1,2,3,4,5];
    let index=index_of(&arr,3).expect("没找到");
    println!("{}",index);
}
```

## 工程组织

### 模块

```rust
mod nation{
    pub mod government{
        pub fn govern(){}
    }
    mod congress{
        pub fn legislate(){}
    }
    mod court{
        fn judicial(){
            super::congress::legislate();//super表示当前模块上一级
        }
    }
}
fn main(){
    nation::government::govern();
}
```

### 结构体

```rust
mod house{
    pub struct Breakfast{
        pub toast:String,
        fruit:String,
    }
    impl Breakfast{
        pub fn summer(toast:&str)=>Breakfast{
            Breakfast{
                toast:String::from(toast),
                fruit:String::from("苹果"),
            }
        }
    }
}
fn main(){
    let mut meal=house::Breakfast::summer("黑麦");
    meal.toast=String::from("小麦");
    println!("{}",meal.toast);
}
```

### 枚举类

```rust
mod a_module{
    pub enum Person{
        King{
            name:String
        },
        Queue
    }
}
fn main(){
    let person=a_module::Person::King{
        name:String::from("Blue");
    };
    if let a_module::Person::King{name}=person{
        println!("{}",name);
    }
}
```

### use

```rust
mod nation{
    pub mod government{
        pub fn govern1(){}
    }
    pub fn govern2(){}
    pub use government::govern1;
}
use crate::nation::government::govern1;
use crate::nation::govern2 as nation_govern;
fn main(){
    nation_govern();
    govern1();
    nation::govern1();
}
```

### 标准库

https://doc.rust-lang.org/stable/std/all.html

```rust
use std::f64::consts::PI;
fn main(){
    println!("{}",PI);
}
```

### 多源文件工程

second_module.rs:

```rust
pub fn output(){
    println!("aaa");
}
```

main.rs:

```rust
mod second_module;
fn main(){
    println!("bbb");
    second_module::output();
}
```

### Cargo

```bash
cargo new xxx #新建工程
cargo init xxx #在当前目录新建工程，不新建文件夹
cargo new xxx --lib #新建库工程，不生成main.rs，主文件为lib.rs
cargo build #构建
cargo run #构建并执行
cargo doc #生成电子文档
```

### 导入外部包

例如在https://docs.rs/上搜索rand，在Cargo.toml中添加rand库：

```toml
[package]
name="hello"
version="0.1.0"
authors=["Ulyan Sobin <ulyansobin@yeah.net>"]
edition="2018"

[dependdencies]
rand="0.8.4"
```

源程序中：

```rust
extern crate rand;
fn main(){
    for _ in 0..8{
        let i:i32=rand::random();
        println!("{}",i);
    }
}
```

## 特性

### 定义特性

```rust
trait Comparable{
    fn greater(&self,b:&Self)->bool;
    fn less(&self,b:&Self)->bool;
    fn equals(&self,b:&Self)->bool;
}
struct Circle{
    radius:f64,
    center:(f64,f64)
}
impl Comparable for Circle{
    fn greater(&self,b:&Circle)->bool{
        self.radius>b.radius
    }
    fn less(&self,b:&Circle)->bool{
        self.radius<b.radius
    }
    fn equals(&self,b:&Circle)->bool{
        self.radius==b.radius
    }
}
fn main(){
    let c1=Circle{
        radius:10.0,
        center:(0.0,0.0)
    };
    let c2=Circle{
        radius:5.0,
        center:(3.0,4.0)
    };
    println!("{}",c1.greater(&c2));
    println!("{}",c1.less(&c2));
    println!("{}",c1.equals(&c2));
}
```

### 默认特性

```rust
trait Printable{
    fn print(&self);
    fn println(&self){
        self.print();
        println!("[END]");
    }
}
struct Text{
    content:String
}
impl Printable for Text{
    fn print(&self){
        print!("{}",self.content);
    }
}
fn main(){
    let text=Text{
        content:String::from("aaa");
    };
    text.println();
}
```

### 常规特性参数

```rust
trait Comparable{
    fn greater(&self,b:&Self)->bool;
    fn less(&self,b:&Self)->bool;
    fn equals(&self,b:&Self)->bool;
}
impl Comparable for f64{
    fn greater(&self,b:&Self)->bool{
        *self>*b
    }
    fn less(&self,b:&Self)->bool{
        *self><*b
    }
    fn equals(&self,b:&Self)->bool{
        *self==*b
    }
}
fn select_sort(array:&mut[&impl Comparable]){
    for i in 0..array.len(){
        let mut k=i;
        for j in (i+1)..array.len(){
            if array[j].less(&array[k]){
                k=j;
            }
        }
        if k!=i{
            let t=array[k];
            array[k]=array[i];
            array[i]=t;
        }
    }
}
fn main(){
    let fa=[1.0,4.0,5.0,2.0,3.0];
    let mut ra=[&fa[0],&fa[1],&fa[2],&fa[3],&fa[4]];
    select_sort(&mut ra);
    for f in ra{
        println!("{}",f);
    }
}
```

### 泛型特性参数

```rust
fn select_sort<T:Comparable>(array:&mut[&T]){
    //...
}
fn function(a:impl SomeTrait,b:SomeTrait,c:impl SomeTrait){
    //...
}
fn function<T:SomeTrait>(a:T,b:T,c:T){
    //...
}
```

### 特性叠加

```rust
trait Stringable{
    fn stringify(&self)->String;
}
trait Printable{
    fn print(&self);
}
impl Stringable for i32{
    fn stringify(&self)->String{
        self.to_string()
    }
}
impl Printable for i32{
    fn print(&self){
        println!("{}",self);
    }
}
fn print_by_two_ways(a:impl Stringable+Printable){
    println!("{}",a.stringify());
    a.print();
}
fn main(){
    let a:i32=-123;
    print_by_two_ways(a);
}
```

使用where关键字优化：

```rust
fn some_function<T:Display+Clone,U:Clone+Debug>(t:T,u:T)
//优化为：
fn some_function<T,U>(t:T,u:U)->i32
	where T:Display+Clone,
		  U:Clone+Debug
```

### 特性作返回值

```rust
trait Printable{
    fn print(&self);
}
impl Printable for f64{
    fn print(&self){
        println!("{}",self);
    }
}
impl Printable for i32{
    fn print(&self){
        println!("{}",self);
    }
}
fn get_number(condition:bool)->Box<dyn Printable>{
    if condition{
        return Box::new(3.1415926_f64);
    }else{
        return Box::new(10000_i32);
    }
}
```

## 文件与I/O

### 读取整个文件（只读）

字符串读取：

```rust
use std::fs;
fn main(){
    let text=fs::read_to_string("hello.txt").unwrap();
    println!("{}",text);
}
```

二进制读取：

```rust
use std::fs;
fn main(){
    let binary=fs::read("hello.txt").unwrap();
    let text=String::from_utf8(binary).unwrap();
    println!("{}",text);
}
```

### 打开文件并读取（只读）

字符串读取：

```rust
use std::fs::File;
use std::io::Read;
fn main(){
    let mut file=File::open("hello.txt").unwrap();
    let mut text=String::new();
    file.read_to_string(&mut text);
    println!("{}",text);
}
```

二进制读取：

```rust
use std::fs::File;
use std::io::Read;
fn main(){
    let mut file=File::open("hello.txt").unwrap();
    let mut binary=Vec::<u8>::new();
    file.read_to_end(&mut binary).unwrap();
    let text=String::from_utf8(binary).unwrap();
    println!("{}",text);
}
```

流读取，缓冲区1字节：

```rust
use std::io::Read;
fn main(){
    let mut file=File::open("hello.txt").unwrap();
    let mut buffer=[0_u8];
    let mut binary=Vec::<u8>::new();
    loop{
        let count=file.read(&mut buffer).unwrap();
        if count==0{
            break;
        }
        binary.push(buffer[0]);
    }
    let text=String::from+_utf8(binary).unwrap();
    println!("{}",text);
}
```

### 新建文件

新建文件或覆盖写：

```rust
use std::fs::File;
use std::io::Write;
fn main(){
    let mut file=File::create("output.txt").unwrap();
    file.write(b"aaa").unwrap();
}
```

### 追加写

```rust
use std::fs::OpenOptions;
use std::io::Write;
fn mian(){
    let mut file=OpenOptions::new().append(true).open("hello.txt").unwrap();
    file.write(b"\n[SUFFIX]").unwrap();
}
```

### OpenOptions对象

#### 基本使用

```rust
//法一
let mut options=OpenOptions::new();
options.read(true);
options.write(true);
let file=options.open("hello.txt").unwrap();

//法二
let file=OpenOptions::new().read(true).write(true).open("hello.txt").unwrap();
```

#### 示例

```rust
use std::fs::OpenOptions;
use std::io::{Seek,Write,SeekFrom,Read};
fn main(){
    let mut file=OpenOptions::new().read(true).write(true).create(true).open("output.txt").unwrap();
    file.write(b"aaa").unwrap();
    file.seek(SeekFrom::Start(0)).unwrap();
    let mut buffer=String::new();
    file.read_to_string(&mut buffer).unwrap();
    println!("{}",buffer);
    let mut file=Openoptions::new().read(true).append(true).open("output.txt").unwrap();
    file.write(b"[END]").unwrap();
    file.seek(SeekFrom::Start(0)).unwrap();
    let mut buffer=String::new();
    file.read_to_string(&mut buffer).unwrap();
    println!("{}",buffer);
}
```

### 二进制读写

写：

```rust
use std::fs::File;
use std::io::Write;
fn main(){
    const PI:f64=3.141592653589793;
    let mut file=File::create("PI.bin").unwrap();
    file.write(&PI.to_ne_bytes()).unwrap();//大端序或小端序取决于CPU
    //其他：大端序to_le_bytes 小端序to_be_bytes
}
```

读：

```rust
use std::fs::File;
use std::io::Read;
fn main(){
    let mut file=File::open("PI.bin").unwrap();
    let mut buffer=[0_u8;8];
    file.read(&mut buffer).unwrap();
    let data=f64::from_ne_bytes(buffer);//同to_ne_bytes
    println!("{}",data);
}
```

### 列出目录

```rust
use std::fs;
fn main(){
    let dir=fs::read_dir("./").unwrap();
    for item in dir{
        let entry=item.unwrap();
        println!("{}",entry.file_name().to_str().unwrap());
    }
}
```

### 创建目录

```rust
use std::fs;
fn main(){
    fs::create_dir("./data").unwrap();
}

//递归地创建：
use std::fs;
fn main(){
    fs::create_dir_all("./data/1/2").unwrap();
}
```

### 删除文件或目录

```rust
use std::fs;
fn main(){
    fs::Fiie::create("./data/1/2/test").unwrap();
    fs::remove_file("./data/1/2/test").unwrap();
    fs::remove_dir("./data/1/2").unwrap();
}

//递归地删除：
use std::fs;
fn main(){
    fs::remove_dir_all("./data").unwrap();
}
```

