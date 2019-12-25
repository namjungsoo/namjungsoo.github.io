---
layout: post
title: CLion에서 사용하는 CMakeLists.txt 간단 강좌
---
리눅스에서 프로그래밍할때 IntelliJ 기반의 CLion이라는 툴을 사용해보았다. CLion은 Makefile이 아닌 범용 maketool인 CMake를 사용한다. 그래서 Makefile 대신에 CMakeLists.txt를 만들어줘야 빌드가 된다.

### 1. 아래는 long string에 대한 에러가 날때 사용한다. CFLAGS옵션 이다.
#### Makefile
FLAGS = -g -O3 -fpermissive -w

#### CMakeLists.txt
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fpermissive")

### 2. 아래는 include directory에 대해서 설정이다. I옵션이다.
#### Makefile
INCS = -I/usr/include -I/usr/local/mysql/include -I/usr/local/include

#### CMakeLists.txt
include_directories(/usr/include)
include_directories(/usr/local/mysql/include)
include_directories(/usr/local/include)

### 3. 아래는 AnsiC에 대한 옵션이다.
set(CMAKE_C_FLAGS "-std=c99")

### 4. 아래는 define 정의이다. FLAGS+= -D와 같다.
add_definitions(-DREGION_KR=1)  
add_definitions(-DMODE_REAL=1)  
add_definitions(-DDB_REAL=1)  

### 5. 아래는 static library에 대한 옵션이다.
#### Makefile
LIBS = -lc -lssl -lpthread -L'/usr/local/mysql/lib/mysql' -lmysqlclient -lz -lcrypt -lnsl -lm -ldl -lrt

#### CMakeLists.txt
add_executable(dark_server ${SOURCE_FILES})

#link must be after add_executable  
target_link_libraries(dark_server c)  
target_link_libraries(dark_server ssl)  
target_link_libraries(dark_server pthread)  
target_link_libraries(dark_server mysqlclient)  