# Chapter 2. Python 기초

## 2.1 기본 자료형과 문자열

Python은 불린(`bool`), 정수(`int`), 실수(`float`), 문자열(`str`) 등의 **기본 자료형**과 리스트(`list`), 튜플(`tuple`), 딕셔너리(`dictionary`), 집합(`set`) 등의 **컨테이너 자료형** 등을 제공한다. 다음은 기본자료형을 생성하고 그 자료형을 `type(x)`를 통해 알아낸 예이다.

```python
>>> isCheck = True    # True or False
>>> type(isCheck)
<class 'bool'>
>>> a = 12
>>> type(a)
<class 'int'>
>>> f = 12.3
>>> type(f)
<class 'float'>
>>> s = 'Hello'
>>> type(s)
<class 'str'>
```

bool 형은 미리정의된 상수 `True`, `False` 만을 가질수 있다. 수치자료형인 `int`와 `float`는 사칙연산이 가능하다.

```python
>>> a = 1
>>> b = 3
>>> c=a+b
```

문자열(`str`)은 작은 따옴표나 큰 따옴표로 둘러싸면 되는데 다음과 같은 4가지 방법으로 정의한다. 

```python
>>> movie = 'World War Z'
>>> movie = "World War Z"
>>> movie = '''World War Z'''
>>> movie = """World War Z"""
```

만약 문자열내에 ‘ 또는 “ 가 있을 다른 종률의 따옴표로 둘러싸면 된다. 

```python
>>> sentense = "It's fine"
>>> sentese = '"I like the word "good"'
```

따옴표 세개인 것은 multiline으로 문자열을 정의할 때 주로 사용된다. 

```python
>>> sentense = """Jane is good.
Paul is bad"""
```

물론 C/C++ 처럼 이스케이프 코드로 정의할 수도 있다.

```python
>>> sentese = 'It\'s fine'
```

Python에서는 C/C++의 문자(character)라는 자료형이 존재하지 않으며 문자열로만 취급하는 점에 주의해야 한다. 문자열에는 +와 *연산자를 적용할 수 있으며, []로 인덱싱과 슬라이싱이가능하다. 또한 문자열 조작함수가 존재한다. + 연산은 concatenation이다.

```python
>>> head = "Python"
>>> tail = " is fun!"
>>> head + tail
'Python is fun!'
```

* 연산은 반복이다. 다음은 흔히 사용하는 응용 예이다.

```python
# script.py
print("=" * 50)
print("My Program")
print("=" * 50)
```

```cmd
> python script.py
==================================================
My Program
==================================================
```

[] 연산자로 인덱싱이 가능하다. 이때 인덱스는 0부터 시작하고 –는 제일 뒤쪽에서의 인덱스로 처리한다. 즉, -1은 제일 뒤, -2는 뒤에서 두번재 요소를 가르킨다. :기호로 범위를 지정할 수도 있다.

```python
>>> s = 'Python uses zero-based index'
>>> s[0]
'P'
>>> s[1]
'y'
>>> s[-1]
'x'
>>> s[-2]
'e'
>>> s[7:11]
'uses'
>>> s[7:-1]
'uses zero-based inde'
>>> s[7:]
'uses zero-based index'
```

주의할 점은 인덱싱이나 슬라이싱 된 부분을 L-value로 사용하여 값을 넣을 수 없다는 점이다.

```python
>>> s[1] = 'T'         # Error
>>> s[1:3] = 'As'     # Error
```

문자열의 포맷팅는 % 기호를 사용하고, C언어의 printf() 함수의 %s, %f, %d 등의 포맷팅 지시자를 사용한다.

```python
>>> "I have %d apples" % 3
'I have 3 apples'
```

만약 여러 개를 포맷팅할 때는 () 기호롤 튜플로 만들어 주면 된다. 

```python
>>> 'X = %d, y = %f\n' %(1.2,3.1)
'X = 1, y = 3.100000\n'
```

보통 문자열 포맷팅은 `print()` 문을 사용할 때 같이 사용한다. 

```python
print('X = %d, y = %f\n' %(1.2,3.1))
```

문자열 조작함수로는 `count()`, `find()`, `rfind()`, `index()`, `rindex()`, `replace()`, `join()`, `split()`, `lstrip()`, `rstrip()`, `strip()`, `upper()`, `lower()` 등이 있다. 이들 함수는 모두 일반 함수가 아닌 문자열의 메쏘드이다. `count(s)`는 주어진 문자열의 개수를, `find(s)`는 최초 찾은 문자열의 위치를 리턴한다. `rfind(s)`는 뒤에서부터 찾는다. `index(s)`, `rindex(s)`는 `find(s)`와 `rfind(s)`와 동일하지만 찾지 못할 때는 ValueError를 이르킨다. 

```python
>>> names = 'John Elis Python Elis Mike'
>>> names.count('Elis')
2
>>> names.find('Elis')
5
>>> names.rfind('Elis')
17
```

`count(s)`, `find(s)`, `rfind(s)`, `index(s)`, `rindex(s)` 등의 함수는 시작과 끝단의 범위를 지정할 수도 있다. 

```python
>>> names.find('Elis',10)
17
>>> names.find('Elis',10,24)
17
```

`replace(old,new)`는 찾아바꾸기를 수행한다. 

```python
>>> names = 'John Elis Python Elis Mike'
>>> names.replace('Elis','Jane')
'John Jane Python Jane Mike'
```

`replace(old,new,count)`로 바꾸기 회수 count를 지정할 수 있다. 

```python
>>> names = 'John Elis Python Elis Mike'
>>> names.replace('Elis','Jane',1)
'John Jane Python Elis Mike'
```

`split()`는 white space로 문자열을 분리하여 string list로 변경한다. `split(sep)`를 사용하면 sep 로 구분하여 분리한다.

```python
>>> name = 'John Python Elis Mike'
>>> name.split()
['John', 'Python', 'Elis', 'Mike']
``` 

```python
>>> name = 'John\tPython\n Elis   Mike'
>>> name.split()
['John', 'Python', 'Elis', 'Mike']
```

위의 예처럼 `split()`으로 사용할 때 공백문자는 ‘ ‘, ‘\t’, ‘\n’, ‘\r’ 중 하나이면 된다. 반면에 `split(sep)`으로 호출할 때는 주어진 sep과 일치하는 것으로만 분리가능하다. 

```python
>>> name = 'John Python, Elis, Mike'
>>> name.split(',')
['John Python', ' Elis', ' Mike']
```

`join(iterable)`은 인자로 주어진 iteration이 가능한 객체(문자열, 리스트, 튜플 등)를 자신으로 이어서 새로운 문자열을 만들어 준다. 

```python
>>> a = ['Mike','John']
>>> '-'.join(a)
'Mike-John'
>>> b = 'Mike'
>>> '-'.join(b)
'M-i-k-e'
```

iterable이라는 것은 인덱스를 통한 연산이 가능하는 의미이다. 위에서 문자열로 이루어진 리스트, 또는 문자열 그 자체를 이어줌을 알 수 있다. 다음은 ,로 구분되는 숫자데이터를 읽은 전형적이 코드이다.
이외에 주로 사용하는 함수로는 왼쪽공백을 제거해 주는 `lstrip()`, 오른쪽 공백을 제거하는 `rstrip()`, 양쪽 공백을 제거나는 `strip()`, 대문자 또는 소문자로 변경해주는 `upper()`, `lower()` 등이 있다. 

```python
>>> line = '  This is test  '
>>> line.rstrip()
'  This is test'
>>> s = "What's this?"
>>> s.upper()
"WHAT'S THIS?"
>>> s.lower()
"what's this?"
```

기본 자료형들은 자신들의 이름인 `bool`, `int`, `float`, `str`으로 형변환이 가능하다.

```python
>>> bool('True')
True
>>> bool('True')
True
>>> bool(1)
True
>>> bool(0)
False
>>> bool(-1)
True
>>> str(12)
'12'
>>> float('12.3')
12.3
```

## 2.2 컨테이너

Python에서 제공하는 컨테이너로는 `list`, `tuple`, `dict`, `set` 등이 있다. 이중 `list`와 `tuple`은 순차컨테이너(또는 sequence)이고, `dict`와 `set`은 연관컨테이너이다.

* `list`는 C++ STL의 vector나 list와 동등하다. Python에는 배열이 없는데 `list`가 그 역할을 대신한다. C++ STL의 vector나 list과 다른 점은 저장하는 요소의 자료형이 동일한 자료형일 필요가 없다는 점이다.

```python
>>> x = [1,'ones', 1.2]
>>> type(x)
<class 'list'>
>>> x[0]
1
>>> x[1]
'ones'
```

* `tuple`은 읽기전용 list이다. 

```python
>>> y = (1,2,'one')
>>> y[0]
1
>>> y[2]
'one'
>>> type(y)
<class 'tuple'>
```

* `dict`는 C++ STL의 map과 동등하다. 즉, <Key, Value>의 쌍으로 구성된 자료형이다. C++ STL의 map과 다른 점은 Key나 Value의 자료형이 동일자료형일 필요가 없다는 것이다. 

```python
>>> dic = {'name':'pey', 'phone':'0119993323', 'birth': '1118'}
>>> dic[‘name’]

>>> dic = {1:'pey',1.2:'good', 'test':5}
```

* `set`은 각각 C++ STL의 set과 동일하다. 즉, 순서없이 저장하는 배열이다. 이 또한 C++ STL set과 달리 동일한 자료형일 필요가 없다.

---
***Shallow copy***
Python에서 기본자료형은 deep copy가 적용되지만, 컨테이너 전체가 아닌 요소를 변경할 때는 shallow copy를 적용한다는 점에 주의해야 한다. 

```python
a = 1
b = a
a = 2
print(a,b)

a = [1,2,3]
b = a
a = [10, 11]
print(a,b)

# 아래는 shallow copy 적용
a = [1,2,3]
b = a
b[1] = 10
print(a,b)
```

---

## 2.3 제어문

타 프로그래밍 언어처럼 `if`, `for`, `while` 등의 제어구문이 있다.

```python
if a>0:
    print(a)
else:
    print(a)
```


## 2.4 함수

Python에서 함수는 `def function(a,…):` 형태로 정의한다. 

```python
def add(a,b):
    return a+b

def printSomething(a,b,c):
    print(a,b,c)

def printLogo():
    print('logo text ...')

c=add(a,b)
printSomething(1,2,3)
printLogo()
```

위에서 add(a,b)는 리턴값이 있는 함수이고, printSomething()과 printLogo()는 없다. printLogo() 처럼 인자가 없을 수도 있다. 
여러 값을 리턴할 때는 튜플로 리턴한다. 값을 받을 때는 한 개의 변수로 받을 수도 있고 unpack 기능을 이용해 리턴값으로 튜플을 모아도 된다. 

```python
def computeProps(n,p,q):
    ...
    return (a,b,c)

r = computeProps(...)
r[0]
(a,b,c) = computeProps(...)   # upack 이용
```

디폴트 인자의 지정역시 간단하게 할 수 있다. 

```python
def computeIgIcr(n,b,h,Ast,dt,Asc,dc,opt='exact'):
   …
Ig = computeIg(n,b,h,h,Ast,dt,Asc,dt)

또는 
Ig = computeIg(n,b,h,h,Ast,dt,Asc,dt,’appr’)
```

## 2.5 클래스

Python의 클래스는 class 키워드를 이용해 선언할 수 있다. 다음은 가장 간단하게 정의한 예이다. 
다음과 같은 형식을 지닌다.

```python
class Simple:
    pass
```

위와 같이 정의한 후 객체를 생성한 후 멤버변수를 추가할 수도 있다. 

```python
>>> a = Simple()
>>> a.name = 'Jane'
>>> a.phone = '123-456-7890'
```

다음은 보다 일반적인 사용법을 예시한 것이다. 

```python
class Account:
    numOfAccount = 0
    def __init__(self,name):
        self.name = name;
        self.balances = 0
        Account.numOfAccount += 1 
    def withdraw(self,value):
        self.balances -= value
        return self.balances
    def deposit(self,value):
        self.balances += value        
        return self.balances
    def __del__(self):
        Account.numOfAccount -= 0
```

```python
>>> a1 = Account('John')
>>> a1.deposit(10)
10
>>> a1.withdraw(2)
8
>>> print(a1.balances)
8
>>> a2 = Account('Jane')
>>> print('no of Account : ',Account.numOfAccount)
2
```

Account 클래스는 클래스 단위로 정의한 변수(클래스 변수, C++의 static variable과 동일)인 numOfAccount와 name, balances라는 인스턴스 변수(C++의 멤버변수)와 생성자 __init__(), 소멸자 __del__(), 일반 메쏘드 withdraw(), deposit() 등을 정의하고 있다. self라는 인자는 C++ 클래스 정의시 생략되는 this 포인터와 같은 역할을 한다. 또한 Python에서는 기본적으로 멤버와 메쏘드가 public 속성을 지닌다. 만약 private으로 하고싶을 때는 두개의 밑줄 _ _로 시작하도록 이름을 정의하면 된다. 
메쏘드에 self 인자가 없는 경우는 C++의 static member와 동일하다. 이 함수에서는 클래스 멤버 변수를 조작하거나 단순히 namespace를 사용하는 함수처럼 사용해야 한다. 

```python
class Account:
    numOfAccount = 0
	...
    def makeZero(number):
        Account.numOfAccount = number
```

Python의 클래스에서도 상속(inheritance)과 가상함수 등을 지원한다. 다음은 간단한 예이다. 

```python
class Element:
    def __init__(self,id):
        self.id = id
        self.nodeIds = []
    def computeStiffness(self):
        print('Element::computeStiffness')
    def printElement(self):
        print('id : %d'%self.id)        

class Q4Element(Element):
    def __init__(self,id,nodeIds):
        super().__init__(id)  #   or  Element.__init__(self,id)
        self.nodeIds = nodeIds

    def computeStiffness(self):
        print('Q4Element::computeStiffness')
```

```python
>>> e = Q4Element(1,[1,2,3])
>>> e.printElement()
id : 1
>>> e.computeStiffness()
Q4Element::computeStiffness
```

위에서 부모클래스의 생성자인 `__init__()`를 호출하는 방법을 두가지이다. `super().__init__(id)` 등과 같이 `self`를 사용하는 방법과 `Element.__init__(self,id)`와 같이 클래스 이름을 사용하고 메쏘드에 `self`를 사용하는 방법이 있다. 이러한 방법을 일반 메쏘드에서도 성립한다. 또한 이름이 같은 메쏘드가 가상함수가 된다.
