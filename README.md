# ARPACK-linking-fortran-to-cpp
ARPACK : Large scale eigenvalue problem들을 계산하기 위해 만든 fortran77기반 라이브러리  
시스템 : window10, VS2017  
## Reference
1) [Teng-Yok Lee's WWW : Build ARPACK on Windows for Visual Studio](http://www.recheliu.org/memo/buildarpackonwindowsforvisualstudio) 중 cygwin을 이용한 방법  
2) [ARPACK 공식 배포](https://www.caam.rice.edu/software/ARPACK/)
3) [Cygwin 공식 배포](https://cygwin.com/install.html)
  
## 1. Cygwin 설치
- Cygwin 설치 진행  
![image](https://user-images.githubusercontent.com/33209778/60559651-0e261280-9d88-11e9-8bc3-bf4f4b2b7193.png)  
![image](https://user-images.githubusercontent.com/33209778/60559670-20a04c00-9d88-11e9-9f70-659d2eabd4fd.png)  
- **이 자료는 Cygwin 경로를 D:\cygwin64 기준으로 작성**
![image](https://user-images.githubusercontent.com/33209778/60559679-2bf37780-9d88-11e9-8046-5da79f298912.png)  
![image](https://user-images.githubusercontent.com/33209778/60559697-3f9ede00-9d88-11e9-8157-1767914e5d79.png)  
![image](https://user-images.githubusercontent.com/33209778/60559718-58a78f00-9d88-11e9-81d7-8fd92e0ebaea.png)  
![image](https://user-images.githubusercontent.com/33209778/60559747-6f4de600-9d88-11e9-918d-903a9e373cd2.png)  
![image](https://user-images.githubusercontent.com/33209778/60559772-8096f280-9d88-11e9-97e3-03bdb6005d45.png)  
![image](https://user-images.githubusercontent.com/33209778/60559794-8f7da500-9d88-11e9-9c64-22dba9f4a46a.png)  
  
- mingw64-x86_64-gcc와 make 선택하여 설치  
![image](https://user-images.githubusercontent.com/33209778/60559928-06b33900-9d89-11e9-9613-f2d89c54444d.png)  
![image](https://user-images.githubusercontent.com/33209778/60559963-2f3b3300-9d89-11e9-8b4a-cf2ec187def7.png)  
  
- Cygwin 설치 완료  
![image](https://user-images.githubusercontent.com/33209778/60560060-63aeef00-9d89-11e9-939b-180ff514d7a1.png) 
  
  
## 2. ARPACK 다운로드 및 코드 수정
- ARPACK 다운로드
![image](https://user-images.githubusercontent.com/33209778/60560448-cf458c00-9d8a-11e9-84fa-4c90ed4deeb0.png)  
![image](https://user-images.githubusercontent.com/33209778/60560471-ed12f100-9d8a-11e9-847c-555b538b9efa.png)  

- 압축풀고 메모장에서 ARmake.inc를 열고, 다음 변수들을 수정  
( **이 자료는 ARPACK 경로를 D:\src\ARPACK 기준으로 작성** )   
```
home = /cygdrive/d/src/ARPACK
PLAT = x64
FC = /usr/bin/x86_64-w64-mingw32-gfortran.exe
FFLAGS    = -O
AR = /bin/x86_64-w64-mingw32-ar.exe 
RANLIB = /usr/bin/x86_64-w64-mingw32-ranlib.exe
```
  
- 메모장에서 D:\src\ARPACK\UTIL\second.f를 열고 아래 코드를 제거
```
EXTERNAL           ETIME.
```
  
## 3. ARPACK build
- Cygwin terminal 실행 후 다음 명령어 실행  
![image](https://user-images.githubusercontent.com/33209778/60560320-3e6eb080-9d8a-11e9-9630-4df2e4e24310.png)  
```
cd /cygdrive/d/src/ARPACK
make lib
/usr/bin/x86_64-w64-mingw32-dllwrap.exe --export-all-symbols BLAS/*.o LAPACK/*.o SRC/*.o UTIL/*.o -lm -lgfortran --output-def arpack_x64.def -o arpack_x64.dll
```
  
- VS2017 Command prompt 실행 후 다음 명령어 실행  
![image](https://user-images.githubusercontent.com/33209778/60563964-d1164c00-9d98-11e9-8b49-1499b0cb30af.png)  
```
d:
cd d:\src\ARPACK
lib.exe /machine:X64 /def:arpack_x64.def
```
  
## 4. VS에서 ARPACK link
- VS 실행 후 아래와 같이 진행  
![image](https://user-images.githubusercontent.com/33209778/60564220-d0ca8080-9d99-11e9-9e05-b53b96f24089.png)  
  
- 디버깅->환경 항목에서 다음 코드 추가 ( build 과정에서 이용한 cygwin의 .dll 파일들 path 추가)  
![image](https://user-images.githubusercontent.com/33209778/60564313-2141de00-9d9a-11e9-9d0a-8a3978802980.png)  
```
PATH=D:\src\ARPACK;%PATH%
PATH=D:\cygwin64\usr\x86_64-w64-mingw32\sys-root\mingw\bin;%PATH%
```
  
- 링커->일반->추가 라이브러리 디렉터리 항목에서 다음 코드 추가 ( build 된 .lib 파일 path 추가 )  
![image](https://user-images.githubusercontent.com/33209778/60564815-14be8500-9d9c-11e9-9eee-c59c4f9a8785.png)  
```
D:\src\ARPACK
```
  
- 링커->입력->추가 종속성 항목에서 기존 코드 앞에 다음 코드 추가 ( build 된 .lib 파일 추가 )
![image](https://user-images.githubusercontent.com/33209778/60564948-90203680-9d9c-11e9-9f56-b898536c6e42.png)  
```
arpack_x64.lib;
```

