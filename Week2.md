#### 1. Направете копие на файла /etc/passwd във вашата home директория под името my_passwd.

```shell
$ cp /etc/passwd my_passwd
```
 
 #### 2. Направете директория practice-test в home директорията ви. Вътре направете директория test1. Можете ли да направите тези две неща наведнъж? Разгледайте нужната man страница. След това създайте празен файл вътре, който да се казва test.txt, преместете го в practice-test чрез релативни пътища.


```shell
$ mkdir -p practice-test/test1
$ touch practice-test/test1/test.txt
$ mv practice-test/test1/test.txt practice-te      st

```

#### 3. Създайте директорията practice/01 във вашата home директория.
Създайте 3 файла в нея - f1, f2, f3.
Копирайте файловете f1, f2, f3 от директорията practice/01/ в директория dir1, намираща се във вашата home директория. Ако нямате такава, създайте я.

```shell
$ mkdir -p practice/01
$ touch practice/01/{f1,f2,f3}
$ mkdir dir1
$ cp practice/01/{f1,f2,f3} dir1

```

#### 4. Нека файлът f2 бъде преместен в директория dir2, намираща се във вашата home директория и бъде преименуван на numbers.

```shell
$ mv practice/01/f2 dir2/numbers
```

#### 5. Отпечатайте имената на всички директории в директорията /home.
```shell
ls -d ./* */

```



```shell
```
