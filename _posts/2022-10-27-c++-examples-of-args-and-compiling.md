---
layout: post
title: "C++ 的启动, 编译, 和实用工具"
subtitle: "Args , compiling, and ssh example of C++"
author: "Eric"
header-style: text
tags:
  - Linux
  - Makefile
  - C/C++
  - 编译
---




主要包括了几个重要的模板, 涉及启动参数, makefile, 和ssh 等. 在实际开发中效率提升非常明显.



## 1. 完美参数实例 rdp

参数任意位置和个数

```c++
#include <cstdlib>
#include <string.h>
#include <stdlib.h>
#include <unistd.h>
#include <iostream>

using namespace std;

int main(int argc, char ** argv){

    string ip;
    string user = "firefly";
    bool flag_fullscreen = false;
    
    for(int i=1;i<argc;i++)
    {       
        if(strcmp(argv[i],"f")==0)
        {
            flag_fullscreen = true;
        }
        else if(strcmp(argv[i],"-f")==0){
            flag_fullscreen = true;
        }
        else if(strcmp(argv[i],"r")==0){
            user = "root";
        }
        else if(strcmp(argv[i],"-r")==0){
            user = "root";
        }
        else{
            ip = argv[i];
        }
            
    }   

    string str = "xfreerdp /v:192.168.57."+ip+" /u:"+user+" /p:firefly";
    if (flag_fullscreen)
    {
        str.append(" -f");
    }

    system(str.c_str());

    return 0;
}
```

## 2. makefile批量生成.out模板

```shell
# makefile

CC = g++
CPPFLAG = -Wall -g -std=c++11

TARGET = $(patsubst %.cpp, %.out, $(wildcard *.cpp))
HEADER = $(wildcard headers/*.h)

%.out : %.cpp $(HEADER)
        $(CC) $(CPPFLAG) $< -o $@

.PHONY : all clean

all : $(TARGET)

clean : 
        rm -f *.out
```

## 3. No pass 

```shell
# vim /etc/ssh/ssh_config   
StrictHostKeyChecking no
```

```c++
#include <cstdlib>
#include <string.h>
#include <stdlib.h>
#include <unistd.h>
#include <iostream>

using namespace std;

int main(int argc, char ** argv){

    string ip = argv[1];
     
    string str = "sshpass -p firefly ssh firefly@192.168.57."+ip;

    system(str.c_str());

    return 0;
}
```
