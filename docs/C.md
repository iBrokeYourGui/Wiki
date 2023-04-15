# Python или Ruby? Госпади спаси!

## Makefile  

Для компиляции любого приложения с более чем одним файлом с исхожным кодом, нужно описать сценарий сборки.  
Библиотеки, объекты, пути и прочее.  
Для этого нужно составлять так называемый Makefile.  
При наличии данного файла в директории, для сборки приложения достаточно набрать команду `make` и прилодение соберется самостоятельно по описаннуму сценарию сборки.  

```make
MyProject : main.o my_lib.o
    gcc main.o my_lib.o -o MyProject

main.o : main.c
    gcc -c main.c -o main.o

my_lib.o : my_lib.c
    gcc -c my_lib.c -o my_lib.o

clean : 
    rm MyProject *.o
```

__MyProject__ - исполняемый файл приложения. Для его сборки трубуются скомпилированные объекты main.o и my_lib.o.  
__main.o__ - правило компиляции для файла main.c  
__my_lib.o__ - правило компиляции для файла my_lib.c  
__clean__ - доп правило которое удаляем исполняемый файл и ранее созданные объекты. Она требуется для полной пересборки проекта. Без нее будут пересобиратся только те файлы, в которые ранее были внесены изменения.

Соотвественно `make clean` - очистит проект, а следом `make` соберет проект с нуля. 

Далее начинаем упрощать данный файл.  

Используя переменные можно упростить дальнейшую коректировку правил, меня только один раз значение переменной. 

```make
TARGET = MyProject
CC = gcc

$(TARGET) : main.o my_lib.o
    $(CC) main.o my_lib.o -o $(TARGET)

main.o : main.c
    $(CC) -c main.c -o main.o

my_lib.o : my_lib.c
    $(CC) -c my_lib.c -o my_lib.o

clean : 
    rm $(TARGET) *.o
```

Дале определим группы файлов в иде переменных 

`SRC = $(wildcard *.c)` - все исходники  
`OBJ = $(patsubst %.c, %.o, $(SRC))` - все объекты. Так как объекты появляются только после компиляции, мы возьмем все измена исходников и составим из них именя будущийх объетов. 


```make
TARGET = MyProject
CC = gcc

SRC = $(wildcard *.c)
OBJ = $(patsubst %.c, %.o, $(SRC))

${TARGET} : $(OBJ)
    $(CC) $(OBJ) -o $(TARGET)

%.o : %.c
    $(CC) -c $< $@

clean : 
    rm (TARGET) *.o
```

И для порлной красоты разнесем все по папкам.  
Исходники все будут лежать в тапке ./src/  
Объекты в папке ./obj/  
Исполняемые файлы в папке ./build/

```make
TARGET = MyProject
CC = gcc

PREF_SRC = ./src/
PREF_OBJ = ./obj/
PREF_TARGET = ./build/

SRC = $(wildcard $(PREF_SRC)*.c)
OBJ = $(patsubst $(PREF_SRC)%.c, $(PREF_OBJ)%.o, $(SRC))

$(PREF_TARGET)$(TARGET) : $(OBJ)
    $(CC) $(OBJ) -o $(TARGET)

$(PREF_OBJ)%.o : $(PREF_SRC)%.c
    $(CC) -c $< $@

clean : 
    rm $(TARGET) $(PREF_OBJ)*.o
```

