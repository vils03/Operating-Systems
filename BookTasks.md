#### 2016-SE-01. 
Даден е текстов файл с име philip-j-fry.txt. Напишете shell script и/или серия от
команди, които извеждат броя редове, съдържащи поне една четна цифра и несъдържащи малка
латинска буква от a до w.
Примерно съдържание на файла:
123abv123
123zz123
MMU_2.4
Примерен изход:
Броят на търсените редовете е 2

```shell
  echo "Броят на редовете е: " | egrep -c  "^[^a-w]*[2,4,6,8,0]*$" philip-j-fry.txt
```
#### 2017-IN-01. 
Напишете серия от команди, извеждащи на екрана само броя на всички обекти във
файловата система, чиито собственик е текущият потребител.
Забележка: Във файловата система със сигурност съществуват директории, до които нямате
достъп.

```shell
 find / -user $(whoami) 2>/dev/null | wc -l
```
#### 2017-IN-02.
Напишете серия от команди, които изтриват:
а) всички файлове в текущата директория и нейните поддиректории, които са с нулева дължина.
```shell
find . -mindepth 1 -type f -size 0 -exec rm {} \;
```
б) 5-е най-големи файла в home директорията на текущия потребител и нейните поддиректории.
```shell
find . -mindepth 1 -type f -printf "%p %s"|sort -nr -k2 | head -n5 | xargs -I {} rm {}
```

#### 2017-IN-03. 
Напишете серия от команди, които от файла /etc/passwd да вземат под-низ, състоящ
се от втора и трета цифра на факултетния номер на студентите от специалност Информатика,
чиито фамилии завършват на “а”. Изведете коя комбинация от цифри се среща най-често и коя е
тя.
Примерно съдържание на файла:
s45194:x:1255:502:Elizabet Mihaylova, Inf, k3, g1:/home/Inf/s45194:/bin/bash
s45139:x:1261:502:Vasilena Peycheva:/home/Inf/s45139:/bin/bash
s81257:x:1079:503:Vasilena Nikolova, KN, 2kurs, 5gr:/home/KN/s81257:/bin/bash
s81374:x:1117:503:Ivan Kamburov, KN, 2kurs, 7gr:/home/KN/s81374:/bin/bash
kiril:x:508:500:Kiril Varadinov:/home/kiril:/bin/bash
s61812:x:1128:504:Vladimir Genchev:/home/SI/s61812:/bin/bash
user:x:1000:99:Inactive user just to start UID from 1000:/home/user:/sbin/nologin
s81254:x:1077:503:Mariela Tihova, KN, 2kurs, 5gr:/home/KN/s81254:/bin/bash
s81386:x:1121:503:Daniela Ruseva, KN, 2kurs, 7gr:/home/KN/s81386:/bin/bash
s45216:x:1235:502:Aleksandar Yavashev, Inf, k3, g3:/home/Inf/s45216:/bin/bash
Примерен изход:
2 51

```shell
 cat /etc/passwd | awk -F':' '$4=502' | cut -d',' -f1 | egrep "[[:alpha:]]*a$"|cut     -c 2,3 | sort|uniq -c|sort -nr -k1|head -n 1

```

#### 2017-SE-01. 
Намерете имената на топ 5 файловете в текущата директория с най-много hardlinks
```shell
find . -mindepth 1 -type f -printf "%p %n\n"|sort -nr -k2|head -n5|cut -d ' ' -f1
```


```shell

```
