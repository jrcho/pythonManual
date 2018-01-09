# Chapter 3. Numpy

NumPy는 과학 컴퓨팅을 위한 기본 패키지이다. 다차원 배열 객체, 이로부터 유도한 마스크된 배열 및 행렬 등과 같은 객체, 논리, 배열 형태 조작, 정렬, 선택, I/O를 비롯한 배열에 대한 빠른 작업을 위한 다양한 루틴을 제공한다. 이외에도 이산 푸리에 변환, 기본 선형 대수학, 기본 통계 연산, 무작위 시뮬레이션 등등 다양한 기능을 가지고 있다.
NumPy와 패키지의 핵심은 `ndarray` 객체이다. ndarray는 fixed-size homogeneous multidimensional array 정도로 이해할 수 있으며, 기본적으로 vectorization과 broadcasting을 지원한다. Python에서 제공하는 list, tuple 등의 시퀀스는 서로 다른 데이터타입을 저장할 수 있고(heterogeneous sequence) 크기가 자동으로 커질 수 있는 반면에 `ndarray`는 성능향상을 위해 같은 데이터타입만을 요소로 가질 수 있고, 크기 역시 고정되어 있다. 만약 크기를 변경하면 새로 메모리에 할당되고 이전 값은 삭제된다.

## 3.1 생성

2차원 배열은 다음과 같이 만들 수 있다.

```python
>>> X = np.array(((1,2,3),(4,5,6)))   # 또는 np.array([(1,2,3),(4,5,6)]) 도 가능.
>>> X
array([[1, 2, 3],
        [4, 5, 6]])
```

초기화와 관련된 몇몇 편의함수를 제공한다. `zeros(..)`는 모두 0으로, `ones(...)`는 모든 성분을 1로, `empty(...)`는 값을 초기화하지 않고(메모리 상태에 따라 다른 값이 들어감) 생성한다. 인자로 생성할 배열의 차원을 `list`나 `tuple`로 지정합니다. `dtype`은 디폴트가 `float64`이고, 변경을 원하면 지정해야 한다.

```python
>>> Y = np.zeros((3,3))
>>> Y
array([[ 0.,  0.,  0.],
        [ 0.,  0.,  0.],
        [ 0.,  0.,  0.]])
>>> Y = np.ones((3,3),dtype='int32')
>>> Z = np.empty((3,3))
```

미리 크기를 지정하지 않고 순차적으로 만들어야 할 경우가 있다. 이때는 크기 0인 배열을 생성하고 `append()`를 수행하면 된다.

```python
import numpy as np

A = np.array([])
for i in range(3):
    A = np.append(A,[1,2,3])

>>> A
array([ 1.,  2.,  3.,  1.,  2.,  3.,  1.,  2.,  3.])
```

단순한 시퀀스는 `range()` 함수의 실수버전인 `arange(from,to,step)`이나 `linspace(from,to,npoints)`를 사용하면 편리하다. 또한 단위행렬을 위한 `eye(n)` 함수를 제공한다.

```python
>>> np.arange(1,2,0.1)
array([ 1. ,  1.1,  1.2,  1.3,  1.4,  1.5,  1.6,  1.7,  1.8,  1.9])
>>> np.arange(10)   # start, step 생략가능. 정수로 생성
array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
>>> np.arange(10.)  # start, step 생략가능. 실수로 생성
array([ 0.,  1.,  2.,  3.,  4.,  5.,  6.,  7.,  8.,  9.])
>>> np.linspace(0.,20.,11)
array([  0.,   2.,   4., ...,  16.,  18.,  20.])
>>> np.eye(3)
array([[ 1.,  0.,  0.],
       [ 0.,  1.,  0.],
       [ 0.,  0.,  1.]])
```

`ndarray`는 고정된 크기를 유지하면서 shape을 변경할 수 있다. 예를 들어 크기 9의 1차원 배열을 3*3 2차원 배열로 바꿀 수 있다. 이때 사용하는 함수는 `reshape()` 인데 함수 또는 메쏘드 형태로 제공한다.

```python
>>> X = np.arange(0,9,1.)
>>> X
array([ 0.,  1.,  2.,  3.,  4.,  5.,  6.,  7.,  8.])
>>> Y = np.reshape(X,(3,3))    # 또는 Y=X.reshape((3,3))
>>> Y
array([[ 0.,  1.,  2.],
       [ 3.,  4.,  5.],
       [ 6.,  7.,  8.]])
```

만약 자기 자신을 대상을 변경하면 shape 속성을 강제로 변경하면 된다.

```python
>>> X.shape = (3,3)
>>> X
array([[ 0.,  1.,  2.],
       [ 3.,  4.,  5.],
       [ 6.,  7.,  8.]])
```

## 3.2 형변환

`astype()` 메쏘드를 사용하면 배열에서 `dtype`을 바꿀 수 있다. 

```python
>>> a = np.arange(3);
>>> a.astype(int)  # a.astype('int34') 와 동일
>>> a.astype('int34') 
>>> a.astype('int64')
>>> a.astype(float)    # a.astype('float64')
>>> a.astype('float32')
>>> a.astype('float64')
```

인자로는 문자열로 정확한 형과 크기를 지정하거나 `int`, `float` 등과 같이 대표상수가 들어갈 수 있다. 위 예에서 32 bit 정수와 64bit 실수형이 디폴트임을 확인할 수 있다. 

## 3.3 인덱싱과 부분배열

`ndarray`에서 인덱싱하는 것은 `A[2]`, `A[2,3]` 등과 같은 [] 연산자를 사용한다. `A[1:3,1:2]` 등과 같이 : 를 이용한 범위 지정을 통해 부분 배열을 얻어낼 수 있다. `A[-1]` 등과 같이 음의 정수를 사용하면 뒤에서부터 인덱싱 된다. 

```python
>>> a = np.array([1.2,-1.3,2.2,5.3,3.7])
>>> a
array([ 1.2, -1.3,  2.2,  5.3,  3.7])
>>> a[0]
1.2
>>> a[0:3]
array([ 1.2, -1.3,  2.2])
>>> a[-1]
3.7000000000000002
>>> a[-2]
5.2999999999999998
>>> a[0:-1]
array([ 1.2, -1.3,  2.2,  5.3])
```

인덱싱에서 정수로 이루어진 index array, boolean array 등을 사용할 수 있다. 

```python
>>> a = np.array([1.2,-1.3,2.2,5.3,3.7])
>>> idx = [0,3]
>>> a[idx]
array([ 1.2,  5.3])
>>> idx = a > 2
>>> idx
array([False, False,  True,  True,  True], dtype=bool)
>>> a[idx]
array([ 2.2,  5.3,  3.7])
```

특히 이떤 조건을 만족하는 요소로 이루어진 배열을 구해낼 때 boolean array를 이용한 연산이 매우 유용하다. 만약 그 위치(index)를 구하고 싶다면, `np.nonzero()` 또는 `np.where()`, `np.flatnonzero()` 를 사용한다. 

```python
>>> np.nonzero(a>2)   # tuple로 리턴.  Np.nonzero(a>2)[0][0]으로 써야 첫번째 index
(array([2, 3, 4], dtype=int64),)

>>> np.flatnonzero(a>2)   # array로 리턴, np.flatnonzero(a>2)[0]이면 첫번째 index
(array([2, 3, 4], dtype=int64),)
```

`concatenate()`, `hstack()`, `vstack()` 를 통해 배열을 합칠 수 있다. 

```python
>>> a = np.array([[1, 2], [3, 4]])
>>> b = np.array([[5, 6]])
>>> np.concatenate((a, b), axis=0)
array([[1, 2],
       [3, 4],
       [5, 6]])
>>> np.concatenate((a, b.T), axis=1)
array([[1, 2, 5],
        [3, 4, 6]])
```

`concatenate()`를 사용할 때는 배열의 차원이 서로 동일해야 한다. 위에서 b가 (1,2) 2-d 배열이다. 만약 b가 (2,)의 1-d 배열이면 에러가 발생한다. 만약 2차원 행렬을 대상으로 한다면 `hstack()`과 `vstack()`이 보다 편리하다. 이 들 함수는 (n,) 형태의 1D 배열을 (1,n) 배열로 취급한다. 

```python
>>> a = np.array([1,2,3])
>>> b = np.array([4,5,6])
>>> np.vstack((a,b))
array([[1, 2, 3],
       [4, 5, 6]])
>>> np.hstack((a,b))
array([1, 2, 3, 4, 5, 6])
```

## 3.4 기본 연산

`ndarray` 객체에 대한 연산은 기본적으로 elementwise로 진행된다.

```python
>>> a = np.array([1,2,3,4])
>>> b = np.array([4,5,6,7])
>>> a+b
array([ 5,  7,  9, 11])
>>> a*b
array([ 4, 10, 18, 28])
>>> a**2
array([ 1,  4,  9, 16])
>>> a+2   # 상수 2는 같은 크기의 배열로 인식
array([3, 4, 5, 6])
>>> 10*np.sin(a)    # NumPy의 universal 함수 sin() 적용
array([ 8.41470985,  9.09297427,  1.41120008, -7.56802495])
>>> a<3
array([ True,  True, False, False], dtype=bool)
>>> a *= b    # a = a*b와 동일
>>> a
array([ 4, 10, 18, 28])
```

위와 같이 배열의 개별 요소가 아닌 배열에 대해 연산을 적용하는 것을 vectorization이라 하며 크기가 다를 경우에도 작동하도록 한 것(예를 들어 a=a+3)을 broadcasting이라고 한다. 

배열의 요소를 인덱싱하는 것은 [] 기호를 사용할 수 있으며 :를 이용해 범위를 지정하는 것이 가능하다.

```python
>>> A[1,2]
>>> A[1:3,:]  # 또는 A[1:3,]
```

만약 행렬 연산이 필요할 경우 `dot()` , `transpose()` 등과 같은 함수를 사용해야 합니다. 

```python
>>> A =np.arange(9).reshape(3,3)
>>> A
array([[0, 1, 2],
       [3, 4, 5],
       [6, 7, 8]])
>>> B = np.arange(11,11+9).reshape(3,3)
>>> B
array([[11, 12, 13],
       [14, 15, 16],
       [17, 18, 19]])
>>> np.dot(A,B)   # 또는 메소드 A.dot(B)를 적용해도 됨. 
                    # matmul()로 2차원 배열에서 동일한 역할을 함.
array([[ 48,  51,  54],
       [174, 186, 198],
       [300, 321, 342]])
>>> np.transpose(A)    # 또는 속성 A.T 를 적용해도 됨.
array([[0, 3, 6],
       [1, 4, 7],
       [2, 5, 8]])
```

배열에서 찾는 것은 최소값 또는 최대값은 `np.amin(x)`, `np.amax(x)`를 이용하면 된다. 만약 최소값 또는 최대값이 있는 위치를 구하려면 `np.argmin(x)`, `np.argmax(x)`를 사용한다. 정렬은 `np.sort(x)`를, 정렬했을 때의 인덱스를 구하려면 `np.argsort(x)`를 사용하면 된다. 

```python
import numpy as np
>>> x = np.array([9.1,8.2,2.3,3,3,7.6,5.2])
>>> np.amin(x)
2.2999999999999998
>>> np.argmin(x)
2
>>> np.sort(x)
array([ 2.3,  3. ,  3. ,  5.2,  7.6,  8.2,  9.1])
>>> np.argsort(x)
array([2, 3, 4, 6, 5, 1, 0], dtype=int64)
```

만약 두번째로 큰 값의 위치를 구한다면 다음과 같다. 

```python
>>> imax2 = np.argsort(x)[-2]
```

Numpy를 사용할 때 True, False를 이용하면 어떤 조건을 맞는 

## 3.5 Broadcasting

NumPy에서 차원이 맞지 않은 객체끼리 연산되도록 하는 것을 broadcasting이라 한다. 대표적인 예가 다음과 같이 2차원 배열에 스칼라나 1차원 배열을 연산하는 것이다. 다음과 같이 (3,3)의 2차원 배열 A, (3,)인 1차원 배열 x, (1,3)과 (3,1)인 2차원 배열 y,z가 있다고 가정하기로 한다. 

```python
>>> import numpy as np
>>> A = np.arange(9.).reshape(3,3)   # 2d array : (3,3)
>>> x = np.array([1.,0,0])   # 1d array : (3,)
>>> y = x.reshape(1,3)       # 2d array : (1,3)
>>> z = x.reshape(3,1)       # 2d array : (3,1)
>>> A
array([[ 0.,  1.,  2.],
       [ 3.,  4.,  5.],
       [ 6.,  7.,  8.]])
>>> x
array([ 1.,  0.,  0.])
>>> y
array([[ 1.,  0.,  0.]])
>>> z
array([[ 1.],
       [ 0.],
       [ 0.]])
```

2차원 배열과 스칼라의 연산은 다음과 같다. 내부적으로 스칼라 1의 차원을 (3,3)으로 확장하고 그 성분의 값은 스칼라 값을 그대로 쓴다.

```python
>>> A+1   # (3,3) + scalar ==> (3,3) + scalar*I
array([[ 1.,  2.,  3.],
       [ 4.,  5.,  6.],
       [ 7.,  8.,  9.]])
```

2차원 배열과 1차원 배열의 연산에서는 1차원 배열 (3,)을 2차원 배열 (3,3)으로 확장하게 된다. 

```python
# (3,3) + (3,) ==> (3,3)
# x = [1,0,0] --> expand with (3,3) 1 0 0
#                                       1 0 0
#                                       1 0 0
>>> A+x
array([[ 1.,  1.,  2.],
        [ 4.,  4.,  5.],
        [ 7.,  7.,  8.]])
```

차원이 (1,3)인 배열을 적용할 때는 위와 통일하다. 

```python
# (3,3) + (1,3) ==> (3,3)
# y = [[1,0,0]] --> expand with (3,3) 1 0 0
#                                         1 0 0
#                                         1 0 0
>>> A+y
array([[ 1.,  1.,  2.],
        [ 4.,  4.,  5.],
        [ 7.,  7.,  8.]])
```

차원이 (3,1)일 경우에는 다음과 같이 확장된다. 

```python
# (3,3) + (3,1)
# z = 1 --> expand with (3,3) 1 1 1
#      0                         0 0 0
#      0                         0 0 0
>>> A+z 
array([[ 1.,  2.,  3.],
       [ 3.,  4.,  5.],
       [ 6.,  7.,  8.]])
```

마지막으로 (1,3)와 (3,1) 배열이 연산결과는 다음과 같이 (3,3) 배열이 된다. 

```python
# (1,3) + (3,1)  --> each dimensions are expanded
#
# y = [1,0,0] --> (3,3) 1 0 0     z = 1     1 1 1
#                       1 0 0         0     0 0 0
#                       1 0 0         0     0 0 0
>>> y+z
array([[ 2.,  1.,  1.],
       [ 1.,  0.,  0.],
       [ 1.,  0.,  0.]])
```

## 3.6 행렬 연산의 주의점

### 3.6.1 Shallow copy

부분행렬은 shallow copy 된다.

```python
>>> a = np.array([1,2,3])
>>> b = a[1:2]
>>> b[0] = 10
>>> print(a)
>>> b = a[1:2].copy()
```

### 3.6.2 행렬의 차원

`matmul(A,b)`, `dot(A,B)`, `solve(A,b)` 등의 함수로 선형대수를 수행할 때 배열의 차원과 관련해서 주의해야 할 점이 있다. 다음과 같은 배열을 선언하였다고 가정하기로 한다.

```python
>>> A = np.arange(9.).reshape(3,3)   # (3,3) 2d array
>>> x = np.array([2,0,1])             # (3,)  1d array
>>> y = x.reshape(1,3)                # (1,3) 2d array
>>> z = x.reshape(3,1)                # (3,1) 2d array
>>> A
array([[ 0.,  1.,  2.],
       [ 3.,  4.,  5.],
       [ 6.,  7.,  8.]])
>>> x
array([2, 0, 1])
>>> y
array([[2, 0, 1]])
>>> z
array([[2],
       [0],
       [1]])
```

Ay 연산이나 Az 연산은 예상한대로 오류를 출력하거나 정상적이로 결과가 출력된다. 

```python
>>> np.matmul(A,y)   # (3,3)*(1,3)  Dimension Error

>>> np.matmul(A,z)   # (3,3)*(3,1)  (3,1)

array([[  2.],
       [ 11.],
       [ 20.]])
```

문제는 Ax와 같이 2차원 배열과 1차원 배열에 연산을 적용할 때이다. 

```python
>>> np.matmul(A,x)   # (3,3)*(3,)  (3,)
array([  2.,  11.,  20.])       
```

위 결과에서 Ax와 같은 연산은 x를 (3,1) 배열로 취급한 연산결과와 동일하지만 출력될 때는 다시 (3,) 행태로 변환해 줌을 알 수 있다. 이는 앞서의 broadcasting과 비교할 때 상당히 혼돈될 수 있으므로 주의해야 한다.

## 3.7 선형대수

주요 선형대수함수로는 `linalg.eig(A)`, `linalg.norm(x)`, `linalg.cond(x)`, `linalg.det(a)`, `linalg.solve(A,b)`, `linalg.inv(a)` 등이 있다.

```python
>>> A = np.array([[1,2,-1],[3,7,0],[0,4,-1]])
>>> [D,V] = np.linalg.eig(A)   # D는 eigenvalues, V는 vector
>>> D
array([ 7.69041576,  1.        , -1.69041576])
>>> V
array([[-0.20463281,  0.66666667,  0.44197763],
       [-0.88917212, -0.33333333, -0.15257416],
       [-0.40926563, -0.66666667,  0.88395526]])
```

에서는 `sin()`, `cos()`다양한 수학함수를 제공합니다. 스칼라값을 입력으로 하는 math 패키지와 달리 배열에 대해 elementwise로 적용됩니다. 

```python
>>> x = np.arange(0.,2*np.pi,0.1)
>>> y = np.sin(x)
>>> y
array([ 0.        ,  0.09983342,  0.19866933, ..., -0.2794155 ,
       -0.1821625 , -0.0830894 ])
```

## 3.8 FFT

NumPy에서는 numpy.fft에서 DFT를 지원한다. Matlab과 거의 동일한 형식으로 사용된다. 

* Standard FFTs

```python
fft(a), ifft(a)
```

* Real FFTs

* Hermitian FFTs

* Helper routines : `fftfreq()`

가장 많이 사용되는 것은 `fft(x)`, `ifft(x)`, `fftfreq(n)`, `fftshift(x)` 등이다. FFT의 정의는 Matlab과 동일하다. 다만 인덱스가 0부터라는 점에 주의해야 한다. 다음은 $x_{m} \leftrightarrow X_{n}$이고 데이터 개수가 N일 때 FFT와 IFFT의 정의이다.

* FFT

$$
X_{n} = \sum_{m = 0}^{N - 1}{x_{m}exp( - j\frac{2\pi mn}{N})},\ \ where\ n = 0,1,\ldots,N - 1

$$

* Inverse FFT

$$
x_{m} = \frac{1}{N}\sum_{n = 0}^{N - 1}{X_{n}exp(j\frac{2\pi mn}{N})},\ \ \text{where\ }m = 0,1,\ldots,N - 1
$$


FFT의 결과를 표현할 때 stem plot을사용할 경우가 많은데 matplotlib을 통해 그릴 수 있다.

## 3.9 How-to

### 3.9.1 Nan, Inf 활용법

`nan`을 이용하면 데이터를 처리하는 데 편리할 수 있다. 그림을 그릴 때 `nan`이 있으면 그 포인트는 그리지 않게 된다. 예를 들어

```python
x = [1,2,3,4]
y1 = [4, 7,,8, 9]
y2 = [2,3,1, np.nan]

plt.plot(x,y1)
plt.plot(x,y2)
```

`Inf`는 비교시 초기값으로 활용하면 편리하다. 

### 3.9.2 행렬데이터 저장과 복원

`tofile()`과 `fromfile()`로 matlabl에서 행렬데이터를 저장하고 로드하는 것과 같은 효과를 낼 수 있다.

```python
A.tofile(‘backup’)
Anew = np.fromfile(‘backup’)
```
