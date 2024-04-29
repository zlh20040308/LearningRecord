# Makefile_learning  

## 入门

### 一份简单的Makefile  

假设现在有一份`hello.c`源文件，我们接下来编写一份简单的Makefile来编译它：  

假设在当前目录下有两份文件：  

```bash  
feng@Feng:~/LearningRecord$ ls
Makefile  hello.c
```  

我们在`Makefile`中填入以下代码

```Makefile
hello: hello.c  
    gcc -o hello hello.c
```  

运行`make`：  

```bash  
feng@Feng:~/LearningRecord$ make
gcc -o hello hello.c
```  

可以看到make帮我们生成了目标文件：  

```bash  
feng@Feng:~/LearningRecord$ ls
Makefile  hello  hello.c
```  

注意事项：  

- `:`号左边是要生成的目标，右边是生成这个目标所需要的依赖  
- 下面一行是生成目标所需要运行的命令要注意这个命令前面要输入一个`TAB`键  

### 分层编译  

接下来修改一下这份Makefile：  

```Makefile
hello: hello.o  
	gcc -o hello hello.o

hello.o: hello.c
	gcc -c hello.c -o hello.o
```  

make的执行逻辑是这样的：  

- 默认从文件的开头执行  
- 先去查看在该文件夹下是否存在该目标文件，如果  
    - 存在，则将依赖文件的生成时间逐个与目标文件的生成时间相对比，如果  
        - 存在依赖某些依赖晚于该目标文件生成，则重新编译生成目标文件
        - 如果没有依赖晚于已存在的目标生成，则不更新任何东西 
    - 不存在，先去查看在该文件夹下是否存在该目标的依赖，如果
 
        - 存在，则比较该依赖最近一次修改的时间，逻辑同上  
        - 不存在，则向下查找是否存在相关命令生成了该依赖，如果  
            - 存在，重复上面的逻辑  
            - 不存在，报错退出  

### 添加指定的目标

修改一下这份Makefile：  

```Makefile
hello:hello.o  
	gcc -o hello hello.o

hello.o: hello.c
	gcc -c hello.c -o hello.o

clean: 
	rm -f hello
	rm -f hello.o
```  

运行`make clean`：  

```bash  
feng@Feng:~/LearningRecord$ make clean
rm -f hello
rm -f hello.o
```  

## 伪目标  

现在假设在我们的当前目录下有一份`clean`文件  

```bash  
feng@Feng:~/LearningRecord$ touch clean
feng@Feng:~/LearningRecord$ ls
Makefile  clean  hello.c
```  

然后在执行`clean`：  

```bash  
feng@Feng:~/LearningRecord$ make clean
make: 'clean' is up to date.
```  

这是因为在我们当前的文件夹下已经有了目标文件，并且这个目标文件没有任何依赖，所以make工具认为不需要执行命令生成新的目标  

要加上伪目标关键字`.PHONY`来告诉make这不是一个真正要生成的目标：  


```Makefile  
hello:hello.o  
	gcc -o hello hello.o

hello.o: hello.c
	gcc -c hello.c -o hello.o

.PHONY: clean
clean: 
	rm -f hello
	rm -f hello.o  
```  

## 默认目标  

make会默认生成Makefile中最上方的目标，如果要修改默认目标，则需要加上`.DEFAULT_GOAL`:  

```Makefile
.DEFAULT_GOAL=hello.o
hello:hello.o  
	gcc -o hello hello.o

hello.o: hello.c
	gcc -c hello.c -o hello.o

.PHONY: clean
clean: 
	rm -f hello
	rm -f hello.o
```  

这样，默认的目标就是`hello.o`了：  

```bash  
feng@Feng:~/LearningRecord$ make
gcc -c hello.c -o hello.o
```
## 定义变量  

```Makefile
OBJ = hello.o 

hello: $(OBJ)  
	gcc -o hello $(OBJ)

$(OBJ): hello.c
	gcc -c hello.c -o $(OBJ)

.PHONY: clean
clean: 
	rm -f hello
	rm -f $(OBJ) 
```  

## 包含其他Makefile  

```Makefile  
# ./Makefile
include ./inc/makefile
OBJ = hello.o 

hello: $(OBJ)  
	gcc -o hello $(OBJ)

$(OBJ): hello.c
	gcc -c hello.c -o $(OBJ)

.PHONY: clean
clean: 
	rm -f hello
	rm -f hello.o

print:
	echo $(subMakefile)
```  

```Makefile  
# ./inc/makefile
subMakefile = ./inc/subMakefile
```