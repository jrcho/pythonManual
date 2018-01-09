# Chapter 6. How-To

## 6.1 같은 라인 출력하기

print()는 기본적으로 실행될 때 마다 줄을 바꾸게 된다. 같은 줄에 쓰려면 ‘\r’을 적용하고, 플러싱을 하면 된다. 저수준의 stdout.write()를 사용하면 다음과 같다. 

```python
from sys import stdout
from time import sleep

for i in range(10):
    stdout.write('\r%d' % i)
    stdout.flush()
    sleep(1)
stdout.write('\n')
```

print() 문을 사용할 때는 다음과 같다. 

```python
from time import sleep

for i in range(10):
    print('\r%d'%i,end='',flush=True)
    sleep(1)

print('\n')
```

위 코드는 cmd에서 사용거나 spyder에서는 정상작동하나, Visual Studio 2017에서는 줄바꿈을 하게 된다(버그로 보임)

다음은 keras에서 사용하고 있는 실제 코드이다. 

```python
import sys
import time

for i in range(10):
    s = '{:03d} / 100'.format(i)
    sys.stdout.write(s)
    time.sleep(1)
    sys.stdout.write('\b' * len(s))
    sys.stdout.write('\r')
```

## 6.2 텍스트 파일 읽고 쓰기

Python에서 파일(file) 객체는 `open()` 내장함수를 통해 파일을 열면서 생성할 수 있다. 텍스트 파일이라면 `readline()`, `readlines()`, `write()` 등의 메쏘드로 읽고 쓰기를 수행할 수 있다. 다 쓴 파일은 `close()` 메쏘드로 닫는다. 

```python
# SomeWord.txt
# Hello Python!!!
# Python is the most powerful language.

f = open('SomeWord.txt','rt')   # Open file
while True:
    line = f.readline()        # read line-by-line using f.readline()
    if not line: break
    print(line)   # line has '\n'
f.close()   # Close file

f = open('SomeWord.txt','rt')
lines = f.readlines()     # read line-by-line using f.readline()
print(lines)
f.close()   # Close file


f = open('SomeWordOutput.txt','wt')
for line in lines:
    f.write(line)   # Use f.write(line) instead of f.writeline(line)
f.close()   # Close file
```

CSV 파일인 경우 csv 패키지를 사용하면 편리하다.

```python
filename = './text.csv';

f = open(filename,'rt')
reader = csv.reader(f,delimiter=',')
next(reader)    # 헤더라인 skip… 필요한 경우 사용한다. 
for line in reader:
    print(line)
f.close()
```

위에서 line은 line = [‘first’, ‘second’,’third’] 등과 같이 문자열으로 분리해서 읽히게 된다. 만약 숫자만을 포함한 CSV 파일이라면 다음과 같이 float()를 통해 변환하면 된다. 

```python
filename = './text.csv';

f = open(filename,'rt')
reader = csv.reader(f,delimiter=',')
next(reader)    # 헤더라인 skip… 필요한 경우 사용한다. 
for line in reader:
for word in line:
    print(float(word))
f.close()
```

숫자만 있을 경우에는 NumPy의 loadtxt()나 savetxt()를 사용하면 편리하다. 이 방법은 모든 데이터가 수치데이터 여야 한다. 

```python
import numpy as np

data = np.loadtxt('ttt.txt')
…
np.savetxt('ttt.out',data,"# test")
```

## 6.3 Excel 다루기

Python에서 Excel을 다루는 방법은 크게 COM을 이용하는 방법과 COM 무관한 독립 팩키지를 이용하는 방법이 있다. 독립 패키지로는 xlwt, xlsxwriter, openpyxl이 있으며, [OpenPyXL](https://openpyxl.readthedocs.io/)이 가장 많이 사용되고 있다. OpenPyXL은 Anaconda 배포본에 포함되어 있으므로 별도의 인스톨 없이 사용가능하다.

OpenPyXL은 xlsx 파일을 메모리에 로딩한 후 읽거나 수정 따위의 작업을 수행한하다. 이후 저장하는 방식으로 작업을 수행한다. 원본파일을 변경하는 것이 아니라 메모리에 상주하는 파일을 새로운 이름으로 저장하는 방식임에 주의해야 한다. 다음은 기존 파일에서 읽어서 작업후 다시 저장하는 샘플이다.

```python
# Tips : ascii to int, and back
# >>> ord('a')
# 97
# >>> chr(97)
# 'a'

from openpyxl import Workbook
from openpyxl import load_workbook
import numpy as np

filename = 'SW-Top.xlsx'
sheetName = 'S'
startCol = 'F'
# .... startCol = 'F'
# ....  F    G    H   I   J   K   
# ....  Sxx Syy, Szz Sxy Syz Szx

outputFileName = filename[0:filename.rfind('.')] + '-new.xlsx'

ns = ord('F')-ord('A')  

wb = load_workbook(filename)   # load to memory
# wb.get_sheet_names()

ws = wb[sheetName]   # selet sheet

max_row  = ws.max_row
ws[chr(ord('F')+6)+str(1)] = 'P1'
ws[chr(ord('F')+7)+str(1)] = 'P2'
ws[chr(ord('F')+8)+str(1)] = 'P3(Zero)'

for i in range(2,max_row+1):
    Sxx = ws[chr(ord('F')+0)+str(i)].value
    Syy = ws[chr(ord('F')+1)+str(i)].value
    Szz = ws[chr(ord('F')+2)+str(i)].value
    Sxy = ws[chr(ord('F')+3)+str(i)].value
    Syz = ws[chr(ord('F')+4)+str(i)].value
    Szx = ws[chr(ord('F')+5)+str(i)].value
    S = np.array([ [Sxx, Sxy, Szx],
                   [Sxy, Syy, Syz],
                   [Szx, Syz, Szz]])
    (P,V) = np.linalg.eig(S)    # allway (-neg, +pos, 0)
    ws[chr(ord('F')+6)+str(i)] = P[1]
    ws[chr(ord('F')+7)+str(i)] = P[0]
    ws[chr(ord('F')+8)+str(i)] = P[2]

wb.save(outputFileName)
```

## 6.4 Excel 데이터를 clipboard를 복사한후 사용하기

```python
import numpy as np
import pandas as pd
df=  pd.read_clipboard()            # If you have selected the headers
# df = pd.read_clipboard(header=None) # If you haven't selected the headers

df[‘Tag1’]  # Pandas Series로 들어오는 데 numpy 1d arry와 동일
x = df[‘Tag1’].as_matrix()  # 강재로 numpy로 바꿀 경우
```

## 6.5 Findpeak

데이터 분석시 첨두값을 구해야할 경우가 많다. [여기](https://blog.ytotech.com/2015/11/01/findpeaks-in-python/)를 참조하면 Python에서 사용된 다양한 방법을 제시하고 있다. 여기에서는 peakdectect라는 matlab을 흉내낸 코드가 가장 효율적이라고 제시한다. 

```python
import numpy as np
import matplotlib.pyplot as plt
from peakdetect import *


from scipy import signal
x = np.arange(0, 10*np.pi, 0.05)
y = np.sin(x)+0.5*np.cos(2*x)
#ipeak = signal.find_peaks_cwt(y, np.arange(1,2))
#ipeak, x[ipeak], y[ipeak]
#([32], array([ 1.6]), array([ 0.9995736]))

ipeaks = peakdetect(y,lookahead=1)

ipeaksmax = ipeaks[0];
ipeaksmin = ipeaks[1];
                 
ipeakmax = [ipeaksmax[i][0] for i in range(len(ipeaksmax)) ]
ipeakmin = [ipeaksmin[i][0] for i in range(len(ipeaksmin)) ]

plt.plot(x,y,x[ipeakmax],y[ipeakmax],'o',x[ipeakmin],y[ipeakmin],'*')
```

 

## 6.6 시간 다루기

```python
import time
import datetime

strDate = '2016-04-25 13:03:17'

date = time.strptime(strDate,'%Y-%m-%d %H:%M:%S') 
seconds = datetime.timedelta(days=t.tm_mday,hours=t.tm_hour,minutes=t.tm_min,seconds=t.tm_sec).total_seconds()
```

## 6.7 파이썬 스크립트 실행하기
Python 2에서 execfile(‘script.py’) 형태로 다른 스크립트를 실행하였다. Python 3에서는 더 이상 지원하지않는다. 대신 파일을 읽은 다음 exec(commandLines)와 같이 라인단위로 실행하는 명령어를 이용하면 된다. 

```python
f = open('myRCPile.py')
exec(f.read())
f.close()
```

## 6.8 운영체계 환경
Python의 기본 모듈 중 os 모듈은 운영체계와 관련된 각종 기능을 제공한다.

```python
import os

# 환경변수
env = os.environ
env['PATH'])   # PATH 환경변수

os.listdir()
os.listdir('c:')

os.mkdir('NewDir')
os.rmdir('NewDir')
os.unlink('ttt.txt')
os.rename('from.txt','to.txt')

import glob
files = glob.glob('*.*')

os.system('notepad')
os.system('notepad')
```

다음은 subprocess 모듈을 이용한 예이다. 

```python
import subprocess

subprocess.run('notepad')
Out[75]: CompletedProcess(args='notepad', returncode=0)

subprocess.run(['notepad','ttt.out'])
Out[76]: CompletedProcess(args=['notepad', 'ttt.out'], returncode=0)

subprocess.run(['notepad','SomeWord.txt'])
```

다음은 도스용 프로그램을 사용한 예이다. 이때 wait()를 통해 프로그램이 종료될 때 까지 기다린다. 

* 방법 1 : 직접 실행화일 위치를 지정. r에 주의

```python
import subprocess
subprocess.SW_HIDE = 1
r = subprocess.Popen(r'D:\DevProg\HFC3.0\resource\Distrib-x64-2017-08-21\hfAnalyzer -o'+inputFileName, shell=True).wait()
if r == 1: 
    print('running error')
```

* 방법 2 : 실행 파일의 위치를 os의 PATH로 지정. r에 주의

```python
import os
env = os.environ
newpath = r'D:\DevProg\HFC3.0\resource\Distrib-x64-2017-08-21;'+env['PATH']
env['PATH'] = newpath

import subprocess
subprocess.SW_HIDE = 1
r = subprocess.Popen('hfAnalyzer -o '+inputFileName, shell=True).wait()
if r == 1: 
    print('running error')
```

* 방법 3 : 실행 파일의 위치를 Popen의 PATH로 지정. r에 주의

```python

import os
env = os.environ
newpath = r'D:\DevProg\HFC3.0\resource\Distrib-x64-2017-08-21;'+env['PATH']

import subprocess
subprocess.SW_HIDE = 1
r = subprocess.Popen('hfAnalyzer -o '+inputFileName, shell=True,env={'PATH':newpath}).wait()
if r == 1: 
    print('running error')
```


참고사항 : wait() 메쏘드는 자식 프로세스가 종료되기를 기다리고, 그 리턴 코드를 받아온다. Wait() 를 사용하지 않으면 return 코드를 사용할 이유가 없다. 

## 6.9 사용자 모듈

Python은 working directory와 python 인스톨 디렉토리, 그 하위의 python\lib\site-packages 등에서 모듈을 찾게 된다. 이중 working directory를 제외한 모듈을 찾는 위치를 조회하려면 sys.path를 통해 접근할 수 있다. 다음은 anaconda에서의 디폴트 sys이다. 

```python
>>> import sys
>>> sys.path
['',
 'C:\\Program Files\\Anaconda3\\python36.zip',
 'C:\\Program Files\\Anaconda3\\DLLs',
 'C:\\Program Files\\Anaconda3\\lib',
 'C:\\Program Files\\Anaconda3',
 'C:\\Program Files\\Anaconda3\\lib\\site-packages',
 'C:\\Program Files\\Anaconda3\\lib\\site-packages\\Sphinx-1.5.6-py3.6.egg',
 'C:\\Program Files\\Anaconda3\\lib\\site-packages\\win32',
 'C:\\Program Files\\Anaconda3\\lib\\site-packages\\win32\\lib',
 'C:\\Program Files\\Anaconda3\\lib\\site-packages\\Pythonwin',
 'C:\\Program Files\\Anaconda3\\lib\\site-packages\\setuptools-27.2.0-py3.6.egg',
 'C:\\Program Files\\Anaconda3\\lib\\site-packages\\IPython\\extensions',
 'C:\\Users\\jrcho\\.ipython']
]
```

만약 자신만의 모듈을 작성한 경우 sys.path.append(path)로 추가해 주거나, PYTHONPATH 환경변수를 설정해 주어야 한다. 여러버전의 Python이 인스톨되어 있는 경우 PYTHONPATH 환경변수를 설정하는 것보다는 sys.path.append(path)를 활용하는 것이 좋다. 

```python
D:\DevProg\Python
   - mymodule1 (여기에 init.py가 있어야 함)
   - mymodule2 (여기에 init.py가 있어야 함)
```

```python
import sys
sys.path.append(r'D:\DevProg\Python')
import mymodule1

…
```

**참고 1**
Visual Studio에서는 현재 IPython 쉘에서 PYTHONPATH를 제대로 지원하지 않는다. Start Debugging나 Start without Debuggin으로 실행할 때는 설정해둔 PYTHONPATH 가 구동되지만 IPython에서 실행할 때는 상기와 같이 sys.path.append(path)를 추가해야 한다. 또한 IPython은 working directory를 자동으로 파일위치로 잡지 않기 때문에 실행파일이 존재하는 경우에는 working directory를 설정해야 한다.

```python
import os
os.chdir(r'D:\DaelimCS\Report\Analysis\Now')

import sys
sys.path.append(r'D:\DevProg\Python')

import my.util as util
import myRCPile as rcPile
```

**참고 2** 
Spyder는 자체에서 PYTHONPATH를 설정할 수 있으므로 편리하다 

