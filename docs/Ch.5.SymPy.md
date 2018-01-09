# Chapter 5. SymPy

## 5.1 설치하기 

SymPy 패키지는 Matlab의  Symbolic Math Toolbox 처럼 Symbolic math를 지원한다. Anaconda 배포본에 포함되어 있으므로 별도의 인스톨이 필요없다. 다만 수식을 보기 좋게 보기 위해서는 Tex를 설치할 필요가 있다. 

    * MikeText 인스톨
    * basic-miktex-2.9.6236-x64.exe

주의할 점은 init_printing()을 호출해야 한다. 이 함수는 수식을 출력할 가장 좋은 backend를 검색하여 구동하게 한다. 만약 Tex가 인스톨되어 있다면 이를 사용하고, 없다면 matplotlib을 사용한다. Matplotlib은 행렬을 보기 좋게 출력못하기 때문에 MikeTex등 tex를 인스톨하는 것이 좋다.

## 5.2 기본 사용법

![SymPy](images/5-1.png)
