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
 cat /etc/passwd | awk -F':' '$4=502' | cut -d',' -f1 | egrep "[[:alpha:]]*a$"|cut -c 2,3 | sort|uniq -c|sort -nr -k1|head -n 1

```

#### 2017-SE-01. 
Намерете имената на топ 5 файловете в текущата директория с най-много hardlinks
```shell
find . -mindepth 1 -type f -printf "%p %n\n"|sort -nr -k2|head -n5|cut -d ' ' -f1
```

#### 2018-SE-01.
Променете правата на всички директории, намиращи се някъде във вашата home
директория, така че да станат такива, каквито биха се получили, ако ги бяхте създали с маска
0022.
```shell
find . -mindepth 1 -maxdepth 1 -type d -exec chmod 644 {} \;
```

#### 2018-SE-02.
Напишете серия от команди, извеждащи на екрана само inode-а на най-скоро променения (по съдържание) файл, намиращ се в home директорията на потребител pesho (или нейните под-директории), който има повече от едно име //ne e testvana
```shell
find . -mindepth 1 -type f -printf "%AT %i %n\n" 2>/dev/null | sort -n -k1,3|head -n1|cut -d ' ' -f2
```
#### 2018-SE-03.
При подреждане в нарастващ ред на числовите потребителски идентификатори (UID)
на акаунтите, дефинирани в системата, 201-ят акаунт е от групата, запазена за акаунти от специалност СИ.
Изведете списък с имената (име и фамилия) и home директориите на всички акаунти от специалност
СИ, подреден по факултетен номер.
За справка:
s61988:x:1219:504:Stoian Genchev,SI,2,5:/home/SI/s61988:/bin/bash
s81430:x:1234:503:Iordan Petkov, KN, k2, g7:/home/KN/s81430:/bin/bash
s61807:x:1248:504:Elica Venchova:/home/SI/s61807:/bin/bash
s62009:x:1254:504:Denitsa Dobreva, 2, 6:/home/SI/s62009:/bin/bash
s61756:x:1258:504:Katrin Kartuleva, SI, 4, 1:/home/SI/s61756:/bin/bash
s855287:x:1195:504:Vaska Kichukova,SI,2,5:/home/SI/s855287:/bin/bash
Примерен изход:
Katrin Kartuleva:/home/SI/s61756
Elica Venchova:/home/SI/s61807
Stoian Genchev:/home/SI/s61988
Denitsa Dobreva:/home/SI/s62009
Vaska Kichukova:/home/SI/s855287

```shell
$ gr=$(cat /etc/passwd |sort -n -t':' -k3| head -n 201|tail -n 1|cut -d ':' -f4)
$ cat /etc/passwd | awk -F':' -v ch=$gr '$4==ch {print $0}'
```
#### 2019-SE-01.
Даден е текстовият файл planets.txt, който съдържа информация за гравитационно
закръглените обекти в дадена слънчева система. На всеки ред има информация за точно един обект
в следните колони, разделени с ’;’:
• име на обекта
• тип на обекта (един знак)
– Т - земен тип
– G - газов гигант
– I - леден гигант
• средно разстояние на обекта до локалната звезда
• маса на обекта (относителна величина)
• обем на обекта (относителна величина)
• плътност (g/cm3)
• средна орбитална скорост (km/s)
Първият ред във файла e header, който описва имената на колоните.
Данните за обектите не са сортирани.
Намерете и изведете разделени с таб името и масата на обекта, който е едновременно:
• най-близкият до локалната звезда
• от същия тип като типа на най-далечният до локалната звезда обект
Примерен входен файл:
name;type;distance;mass;volume;density;speed
earth;T;1.00000011;1;1;5.52;29.7859
mars;T;1.52366231;0.107;0.151;3.94;24.1309
saturn;G;9.53707032;95;763.62;0.7;9.6724
mercury;T;0.38709893;0.055;0.056;5.43;47.8725
venus;T;0.72333199;0.815;0.857;5.24;35.0214
jupiter;G;5.20336301;318;1321.3;1.33;13.0697
neptune;I;30.06896348;17;57.747;1.76;5.4778
uranus;I;19.19126393;14.5;63.102;1.3;6.8352
```shell
$ type=$(cat planets.txt | tail -n+2 | sort -n -t';' -k3|tail -n1|cut -d';' -f2)
$cat planets.txt | sort -rn -t';' -k3|awk -F';' -v t=$type '{if($2==t) print $1"\t"$4;exit}'
```

#### 2019-SE-02.
Вие сте асистент по ОС. На първото упражнение казвате на студентите да си напишат
данните на лист, взимате го и им правите акаунти. След упражнението обаче, забравяте да вземете
листа със себе си - сещате се половин час по-късно, когато трябва да въведете имената на студентите
в таблица, но за зла беда в стаята вече няма ни помен от листа (вероятно иззет от спешния отряд
на GDPR-полицията)
Сещате се, че в началото на упражнението UNIX-часовникът е показвал 1551168000, а в края
1551176100.
Напишете команда, която изкарва разделени с таб факултетните номера и имената на потребителите от специалност СИ, чиито home директории са променили статуса си (status change time) в
зададения времеви интервал.
Приемете, че всички потребители от СИ имат home директории под /home/SI.
Примерен изход:
62198 Ivaylo Georgiev
62126 Victoria Georgieva
62009 Denitsa Dobreva
62208 Trayana Nedelcheva
Няколко реда от /etc/passwd за справка:
s62136:x:1302:503:Alexander Ignatov, SI, 2, 2:/home/KN/s62136:/bin/bash
s62171:x:1031:504:Deivid Metanov:/home/SI/s62171:/bin/bash
s62126:x:1016:504:Victoria Georgieva:/home/SI/s62126:/bin/bash
s62009:x:1170:504:Denitsa Dobreva,SI,3,3:/home/SI/s62009:/bin/bash
s62196:x:1221:504:Elena Tuparova,SI,2,1:/home/SI/s62196:/bin/bash
```shell
$ find . -mindepth 1 -maxdepth 1 -type d | xargs -I {} stat -c "%Y %u" {}|awk '{if(($1>1551168000) && ($1<1681977353)) {print $2}}'|xargs -I {} grep '{}' /etc/passwd|awk -F':' '{print $1"\t"$5}'| sed 's/,//g'|sed 's/^s//g'
```

#### 2019-SE-03.
От всички файлове в home директорията на потребителя velin, изведете дълбочината
на файл, който:
• има същия inode като този на най-скоро променения файл сред тях
• има минимална дълбочина
Пояснение Под "дълбочина"да се разбира дълбочина в дървото на файловата система: например
файлът /foo/bar/baz има дълбочина 3.
```shell
$ inodeeLast=$(find /home/velin -mindepth 1 -type f|xargs -I {} stat -c "%Y %i" {}|sort -rn -k1|head -n1|cut -d' ' -f2)
$ find /home/velin -mindepth 1 -type f -printf "%d %i\n"|awk -v node=$inodeeLast '{if($2==node) {print $1;exit}}' 
```

#### 2020-SE-01.
За всички файлове, които (едновременно):
• се намират някъде във вашата home директория;
• имат права, каквито биха имали, ако ги бяхте създали с маска 0022
променете правата им така, че group owner-а на файла да има право да пише в него.
```shell
$ find . -type f -perm 644 -exec chmod 666 {} \; 
```

#### 2020-SE-02.
Даден е текстовият файл spacex.txt, който съдържа информация за полети на
ракети на SpaceX. На всеки ред има информация за един такъв полет в следните колони, разделени
с вертикална черта ’|’:
• дата на полета в UNIX формат
• космодрум
• успешност на полета (две възможни стойности)
– Success - успешен полет
– Failure - неуспешен полет
• полезен товар
Първият ред във файла e header, който описва имената на колоните. Данните във файла не са
сортирани.
Намерете и изведете разделени с двоеточие (’:’) успешността и информацията за полезния товар
на най-скорощния полет, който е изстрелян от космодрума с най-много неуспешни полети.
Примерен входен файл:
date|launch site|outcome|payload
1291759200|CCAFS|Success|Dragon demo flight and cheese
41435438800|CCAFS|Failure|SpaceX CRS-7
1275666300|CCAFS|Success|Dragon Spacecraft Qualification Unit
1452981600|VAFB|Success|Jason-3
1498165200|KSC|Success|BulgariaSat-1
1473454800|CCAFS|Failure|Amos-6
1517868000|KSC|Success|Elon Musk’s Tesla
1405285200|CCAFS|Success|Orbcomm
```shell
$ cosm=$(cat spacex.txt | tail -n+2|grep 'Failure'|cut -d'|' -f2|sort -rn|head -n1 |uniq -c|tr -s ' '|sed 's/^ //'|cut -d' ' -f2)
$ cat spacex.txt | grep $cosm|sort -nr -t'|' -k1 |head -n1|cut -d'|' -f3,4|sed 's/|/:/'
```
#### 2022-CE-01.
Напишете серия от команди, които намират всички обикновени файлове, отговарящи едновременно
на следните условия:
• намират се в home директорията на текущия потребител, но не и в нейните под-директории;
• собственик им е текущият потребител.
Серията от команди трябва да променя правата на намерените файлове така, че да станат такива,
каквито биха се получили, ако при създаването на файловете ефективната маска е била 0002.
Гарантирайте, че серията от команди няма изход
```shell
$ find ~ -mindepth 1 -maxdepth 1 -type f -uid $(id -u) -exec chmod 664 {} \; 2>/dev/null
```
#### 2016-SE-01.
Напишете shell скрипт, който по подаден един позиционен параметър, ако този
параметър е директория, намира всички symlink-ове в нея и под-директориите и с несъществуващ
destination.
```shell
 #!/bin/bash
  
   if [ $# -ne 1 ]; then
       echo "Invalid input"
       exit 1
   fi
  
   if [ ! -d $1 ]; then
       echo "Invalid argument"
      exit 1
  fi
 
  find $1 -mindepth 1 -type l -exec [ ! -e {} ] \; -printf "%p\n"
```


#### 2016-SE-03.
В текущата директория има само обикновени файлове (без директории). Да се
напише bash script, който приема 2 позиционни параметъра – числа, който мести файловете от
текущата директория към нови директории (a, b и c, които трябва да бъдат създадени), като
определен файл се мести към директория ’a’, само ако той има по-малко редове от първи позиционен
параметър, мести към директория ’b’, ако редове са между първи и втори позиционен параметър
и в ’c’ в останалите случаи.
```shell
#!/bin/bash

if [ $# -ne 2 ]; then
        echo "Invalid input"
        exit 1
fi
regex="^[0-9]+$"
if [[ ! $1=~$regex || ! $2=~$regex ]]; then
        echo "Invalid input"
        exit 1
fi
mkdir a b c
for file in $(find . -type f); do
        lines=$(cat $file | wc -l)
        if [ $lines -lt $1 ];then
                mv $file a
        elif [[ $lines -gt $1 && $lines -lt $2 ]];then
                mv $file b
        else
                mv $file c
        fi
done
```
#### 2016-SE-04.
Файловете във вашата home директория съдържат информация за музикални албуми и имат специфична структура. Началото на всеки ред е годината на издаване на албума, а
непосредствено, след началото на всеки ред следва името на изпълителя на песента. Имената на
файловете се състоят от една дума, която съвпада с името на изпълнителя.
Примерно съдържание на файл с име "Bonnie":
52005г. Bonnie - "God Was in the Water" (Randall Bramblett, Davis Causey) – 5:17
2005г. Bonnie - "Love on One Condition" (Jon Cleary) – 3:43
2005г. Bonnie - "So Close" (Tony Arata, George Marinelli, Pete Wasner) – 3:22
2005г. Bonnie - "Trinkets" (Emory Joseph) – 5:02
2005г. Bonnie - "Crooked Crown" (David Batteau, Maia Sharp) – 3:49
2005г. Bonnie - "Unnecessarily Mercenary" (Jon Cleary) – 3:51
2005г. Bonnie - "I Will Not Be Broken" - "Deep Water" (John Capek, Marc Jordan) – 3:58
Да се напише shell скрипт приемащ два параметъра, които са имена на файлове от вашата home директория. Скриптът сравнява, кой от двата файла има повече на брой редове, съдържащи неговото
име (на файла). За файлът победител изпълнете следните действия:
• извлечете съдържанието му, без годината на издаване на албума и без името на изпълнителя
• сортирайте лексикографски извлеченото съдържание и го запишете във файл с име ’изпълнител.songs’
Примерен изходен файл (с име Bonnie.songs):
"Crooked Crown" (David Batteau, Maia Sharp) – 3:49
"God Was in the Water" (Randall Bramblett, Davis Causey) – 5:17
"I Will Not Be Broken" - "Deep Water" (John Capek, Marc Jordan) – 3:58
"Love on One Condition" (Jon Cleary) – 3:43
"So Close" (Tony Arata, George Marinelli, Pete Wasner) – 3:22
"Trinkets" (Emory Joseph) – 5:02
"Unnecessarily Mercenary" (Jon Cleary) – 3:51
```shell
#!/bin/bash

if [ $# -ne 2 ]; then
        echo "Invalid input"
        exit 1
fi

lines1=$(cat $1 | grep "$1" | wc -l)
lines2=$(cat $2 | grep "$2" | wc -l)

if [ $lines1 -gt $lines2 ]; then
        cat $1 | cut -d'-' -f2-|sed 's/^ //'|sort >> "$1.songs"
else
        cat $2 | cut -d'-' -f2-|sed 's/^ //'|sort >> "$2.songs"
fi
```
#### 2016-SE-06.
Имате текстов файл със следното съдържание (всяка книга е на един ред):
1979 г. - „Синият тайфун“ (сборник съветски научнофантастични разкази за морето)
1979 г. - „Двойната звезда“ - Любен Дилов
1979 г. - „Завръщане от звездите“ - Станислав Лем (Превод: Веселин Маринов)
1979 г. - „Среща с Рама“ - Артър Кларк (Превод: Александър Бояджиев)
1979 г. - „Алиби“ - Димитър Пеев (криминален роман)
1979 г. - „Тайнственият триъгълник“ (сборник НФ разкази за морето)
1979 г. - „Второто нашествие на марсианците“ - Аркадий и Борис Стругацки
1979 г. - „Гробищен свят“ - Клифърд Саймък (Превод: Михаил Грънчаров)
1979 г. - „Чоки“ - Джон Уиндъм (Превод: Теодора Давидова)
1979 г. - „Спускане в Маелстрьом“ - Едгар Алан По (Превод: Александър Бояджиев)
1980 г. - „Допълнителна примамка“ - Робърт Ф. Йънг (Превод: Искра Иванова, ...)
1980 г. - „Кристалното яйце“ - Хърбърт Уелс (Превод: Борис Миндов, ...)
1980 г. - „Онирофилм“ (сборник италиански НФ разкази) (Превод: Никола Иванов, ...)
Напишете shell script (приемащ аргумент име на файл) който извежда:
• всеки ред от файла с добавен пореден номер във формат "1. ", "2. ", ... "11. " ...
• махат данните за годината на издаване
• сортират изхода по заглавие (лексикографски, възходящо)
Примерен изход (показани са само първите 4 реда):
5. „Алиби“ - Димитър Пеев (криминален роман)
7. „Второто нашествие на марсианците“ - Аркадий и Борис Стругацки
8. „Гробищен свят“ - Клифърд Саймък (Превод: Михаил Грънчаров)
2. „Двойната звезда“ - Любен Дилов
```shell
 #!/bin/bash
  
   if [ $# -ne 1 ]; then
       echo "Invalid input"
       exit 1
   fi
  
   if [ ! -f $1 ]; then
       echo "Invalid argument"
      exit 1
  fi
 
  cat $1 | cut -d'-' -f2-|sed 's/^ //'|awk '{print NR". "$0}'|sed 's/„/@/'|    sed 's/“/@/' | sort -t'@' -k2|sed 's/@/„/1'|sed 's/@/“/1'
```
#### 2017-IN-01.
Напишете скрипт, който приема три задължителни позицонни аргумента:
• име на фаил
• низ1
• низ2
Файлът е текстови, и съдържа редове във формат:
6ключ=стойност
където стойност може да бъде:
• празен низ, т.е. редът е ключ=
• низ, състоящ се от един или повече термове, разделени с интервали, т.е., редът е ключ=t1 t2
t3
Някъде във файла: 
• се съдържа един ред с ключ първия подаден низ (низ1);
• и може да се съдържа един ред с ключ втория подаден низ (низ2).
Скриптът трябва да променя реда с ключ низ2 така, че обединението на термовете на редовете с
ключове низ1 и низ2 да включва всеки терм еднократно.
Примерен входен файл:
$ cat z1.txt
FOO=73
BAR=42
BAZ=
ENABLED_OPTIONS=a b c d
ENABLED_OPTIONS_EXTRA=c e f
Примерно извикване:
$ ./a.sh z1.txt ENABLED_OPTIONS ENABLED_OPTIONS_EXTRA
Изходен файл:
$ cat z1.txt
FOO=73
BAR=42
BAZ=
ENABLED_OPTIONS=a b c d
ENABLED_OPTIONS_EXTRA=e f 
```shell
#!/bin/bash

if [ $# -ne 3 ]; then
        echo "Invalid input"
        exit 1
fi

if [ ! -f $1 ]; then
        echo "First argument is not file"
        exit 1
fi
line=""
flag=""
for char in $(cat $1 | egrep "^$3=" | cut -d'=' -f2|cut -d' ' -f1-)
do
        for ch in $(cat $1 | egrep "^$2=" | cut -d'=' -f2|cut -d' ' -f1-);do
                if [ $char == $ch ]; then
                        flag=1
                        break
                fi
        done
        if [[ $flag -ne 1 ]]; then
                line+="${char} "
        else
                flag=""
        fi
done
toRep=$(cat $1 |egrep "^$3=.*")
sed -i "s/$toRep/$3=$line/g" $1
```
#### 2017-IN-03.
Напишете скрипт, който извежда името на потребителския акаунт, в чиято home
директория има най-скоро променен обикновен файл и кой е този файл. Напишете скрипта с подходящите проверки, така че да бъде валиден инструмент.
```shell
#!/bin/bash

if [ $# -ne 0 ]; then
        echo "Wrong input"
        exit 1
fi

cat /etc/passwd | cut -d':' -f6| grep "home"|xargs -I {} find {} -type f -printf "%AT %f %u\n" 2>/dev/null|sort -nr -k1|head -n1|cut -d' ' -f2,3

```
#### 2017-SE-01.
Напишете скрипт, който получава задължителен първи позиционен параметър – директория и незадължителен втори – число. Скриптът трябва да проверява подадената директория
и нейните под-директории и да извежда имената на:
7а) при подаден на скрипта втори параметър – всички файлове с брой hardlink-ове поне равен на
параметъра;
б) при липса на втори параметър – всички symlink-ове с несъществуващ destination (счупени
symlink-ове).
Забележка:За удобство приемаме, че ако има подаден втори параметър, то той е число.
```shell
#!/bin/bash

if [ $# -gt 2 ]; then
        echo "More than two arguments"
        exit 1
fi
DIR=$1
if [ $# -eq 2 ]; then
        find $DIR -type f -printf "%n %f\n" 2>/dev/null |awk -v num=$2 '$1>=num {print $2}'
fi
if [ $# -eq 1 ]; then
        find $DIR -mindepth 1 -type l 2>/dev/null -exec [ ! -e {} ] \; -printf "%f\n"
fi
```

#### 2017-SE-02.
Напишете скрипт, който приема три задължителни позиционни параметра - директория SRC, директория DST (която не трябва да съдържа файлове) и низ АBC. Ако скриптът се
изпълнява от root потребителя, то той трябва да намира всички файлове в директорията SRC и
нейните под-директории, които имат в името си като под-низ АBC, и да ги мести в директорията
DST, запазвайки директорийната структура (но без да запазва мета-данни като собственик и права,
т.е. не ни интересуват тези параметри на новите директории, които скриптът би генерирал в DST).
Пример:
• в SRC (/src) има следните файлове:
/src/foof.txt
/src/1/bar.txt
/src/1/foo.txt
/src/2/1/foobar.txt
/src/2/3/barf.txt
• DST (/dst) е празна директория
• зададения низ е foo
Резултат:
• в SRC има следните файлове:
/src/1/bar.txt
/src/2/3/barf.txt
• в DST има следните файлове:
/dst/foof.txt
/dst/1/foo.txt
/dst/2/1/foobar.txt
```shell
#!/bin/bash

if [ $# -ne 3 ]; then
        echo "Invalid input"
        exit 1
fi

if [[ "$(whoami)" != "s0600007" ]]; then
        echo "Script must be run by root"
        exit 1
fi

if [[ ! -d $1 || ! -d $2 ]]; then
        echo "Invalid arguments"
        exit 1
fi

if [ $(find $2 -mindepth 1 -maxdepth 2 | wc -l) -ne 0 ]; then
        echo "Destination dir not empty"
        exit 1
fi

find $1 -mindepth 1 -type f -name "*$3*" -printf "%p\n"|cut -d'/' -f2 | xargs -I {} mv $1/{} $2
```

#### 2017-SE-03.
Напишете скрипт, който ако се изпълнява от root потребителя:
а) извежда обобщена информация за броя и общото количество активна памет (RSS - resident set
size, non-swaped physical memory that a task has used) на текущите процеси на всеки потребител;
б) ако процесът с най-голяма активна памет на даден потребител използва два пъти повече памет
от средното за потребителя, то скриптът да прекратява изпълнението му по подходящ начин.
За справка:
$ ps aux | head -5
USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND
root 1 0.0 0.0 15820 1052 ? Ss Apr21 0:06 init [2]
root 2 0.0 0.0 0 0 ? S Apr21 0:00 [kthreadd]
root 3 0.0 0.0 0 0 ? S Apr21 0:02 [ksoftirqd/0]
root 5 0.0 0.0 0 0 ? S< Apr21 0:00 [kworker/0:0H]
Алтернативно, може да ползвате изхода от ps -e -o uid,pid,rss
```shell
#!/bin/bash

if [[ "$(whoami)" != "s0600007" ]]; then
        echo "This script must be rum by root"
        exit 1
fi

for user in $(ps -e -o uid=|sort -d|uniq); do
        ps -e -u $user -o rss=,user=|awk -v sum=0 '{sum+=$1} END {print sum" "$2}'
done

for user in $(ps -e -o uid=|sort -d|uniq); do
        all=$(ps -e -u $user -o rss=|awk -v sum=0 '{sum+=$1} END {print sum}')
        count=$(ps -e -u $user -o rss=|wc -l)
        av=$(( all / count ))
        maxAndPid=$(ps -e -u $user -o rss=,pid=|awk -v max=0 'max<$1 {max=$1} END {print max" "$2}')
        if [[ $(( av * 2 )) -lt $(echo $maxAndPid|cut -d' ' -f1) ]]; then
                kill -9 $(echo $maxAndPid|cut -d' ' -f2)
        fi
done

```

#### 2017-SE-04.
Напишете shell script, който получава задължителен първи позиционен параметър
– директория и незадължителен втори – име на файл. Скриптът трябва да намира в подадената
директория и нейните под-директории всички symlink-ове и да извежда (при подаден аргумент
файл – добавяйки към файла, а ако не е – на стандартния изход) за тях следната информация:
• ако destination-a съществува – името на symlink-а -> името на destination-а;
• броя на symlink-овете, чийто destination не съществува.
Примерен изход:
lbaz -> /foo/bar/baz
lqux -> ../../../qux
lquux -> /foo/quux
Broken symlinks: 34
```shell
#!/bin/bash

if [ $# -gt 2 ]; then
        echo "Invalid input"
        exit 1
fi
count=0
for syml in $(find $1 -type l 2>/dev/null); do
        if [ ! -e $syml ]; then
                count=$(( count + 1 ))
        else
                if [ $# -eq 1 ]; then
                        stat -c "%N\n" $syml
                else
                        stat -c "%N\n" $syml >> $2
                fi
        fi
done
if [ $# -eq 1 ]; then
        echo "Broken symlinks $count"
else
        echo "Broken symlinks $count" >> $2
fi
```
#### 2017-SE-05.
Напишете скрипт, който получава два задължителни позиционни параметъра –
директория и низ. Сред файловете в директорията би могло да има такива, чиито имена имат
структура vmlinuz-x.y.z-arch където:
• vmlinuz е константен низ;
• тиретата “-” и точките “.” присъстват задължително;
• x е число, version;
• y е число, major revision;
• z е число, minor revision;
• наредената тройка x.y.z формира глобалната версия на ядрото;
• arch е низ, архитектура (платформа) за която е съответното ядро.
Скриптът трябва да извежда само името на файла, намиращ се в подадената директория (но не и
нейните поддиректории), който:
• спазва гореописаната структура;
• е от съответната архитектура спрямо параметъра-низ, подаден на скрипта;
• има най-голяма глобална версия.
Пример:
• Съдържание на ./kern/:
vmlinuz-3.4.113-amd64
vmlinuz-4.11.12-amd64
vmlinuz-4.12.4-amd64
vmlinuz-4.19.1-i386
• Извикване и изход:
$ ./task1.sh ./kern/ amd64
vmlinuz-4.12.4-amd64
```shell
#!/bin/bash

if [ $# -ne 2 ]; then
        echo "Invalid input"
        exit 1
fi

if [[ ! -d $1 ]]; then
        echo "Invalid argument"
        exit 1
fi

global=$(find $1 -maxdepth 1 -type f | egrep "vmlinuz.*"|egrep "$2$"|cut -d'-' -f2|sort -nr -t':'|head -n1)
find $1 -maxdepth 1 -type f -printf "%f\n"|egrep "$global"
```
#### 2017-SE-06.
Напишете скрипт, който ако се изпълнява от root потребителя, намира процесите
на потребителите, които не са root потребителя и е изпълнено поне едно от следните неща:
• имат зададена несъществуваща home директория;
• не са собственици на home директорията си;
• собственика на директорията не може да пише в нея.
Ако общото количество активна памет (RSS - resident set size, non-swaped physical memory that a task
has used) на процесите на даден такъв потребител е по-голямо от общото количество активна памет
на root потребителя, то скриптът да прекратява изпълнението на всички процеси на потребителя.
За справка:
$ ps aux | head -n 5
USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND
root 1 0.0 0.0 15820 1052 ? Ss Apr21 0:06 init [2]
root 2 0.0 0.0 0 0 ? S Apr21 0:00 [kthreadd]
root 3 0.0 0.0 0 0 ? S Apr21 0:02 [ksoftirqd/0]
root 5 0.0 0.0 0 0 ? S< Apr21 0:00 [kworker/0:0H]
Алтернативно, може да ползвате изхода от ps -e -o uid,pid,rss
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
s61934:x:1177:504:Mariq Cholakova:/home/SI/s61934:/bin/bash
```shell
#!/bin/bash

ppl=$(ps -e -o user= | grep -v 'root' | sort | uniq)

sum_root=$(ps -e -u "root" -o rss= | awk '{sum+=$1} END {print sum}')

for p in $ppl
do
        dir=$(cat /etc/passwd| cut -d':' -f1,6 | grep $p | cut -d':' -f2)

        if [[ ! -d $dir || $(stat -c"%U" $dir 2>/dev/null) != $p || $(stat -c"%A" $dir 2>/dev/null | head -c 3 | tail -c 1 ) != "w" ]]
        then
                sum_usr=$(ps -e -u $p -o user= | awk '{sum+=$1} END {print sum}')

                if [[ $sum_root -lt $sum_usr ]]
                then
                        ps -e -u $p -o pid= | xargs -I {} echo "kill {}"
                fi
        fi
done
```
#### 2018-SE-02.
Напишете скрипт, който приема два позиционни аргумента – име на текстови файл
и директория. Директорията не трябва да съдържа обекти, а текстовият файл (US-ASCII) е стенограма и всеки ред е в следния формат:
ИМЕ ФАМИЛИЯ (уточнения): Реплика
където:
• ИМЕ ФАМИЛИЯ присъстват задължително;
• ИМЕ и ФАМИЛИЯ се състоят само от малки/главни латински букви и тирета;
• (уточнения) не е задължително да присъстват;
• двоеточието ‘:’ присъства задължително;
• Репликата не съдържа знаци за нов ред;
• в стринга преди двоеточието ‘:’ задължително има поне един интервал между ИМЕ и ФАМИЛИЯ;
• наличието на други интервали където и да е на реда е недефинирано.
Примерен входен файл:
John Lennon (The Beatles): Time you enjoy wasting, was not wasted.
Roger Waters: I’m in competition with myself and I’m losing.
John Lennon:Reality leaves a lot to the imagination.
Leonard Cohen:There is a crack in everything, that’s how the light gets in.
Скриптът трябва да:
• създава текстови файл dict.txt в посочената директория, който на всеки ред да съдържа:
ИМЕ ФАМИЛИЯ;НОМЕР
където:
– ИМЕ ФАМИЛИЯ е уникален участник в стенограмата (без да се отчитат уточненията);
– НОМЕР е уникален номер на този участник, избран от вас.
• създава файл НОМЕР.txt в посочената директория, който съдържа всички (и само) редовете
на дадения участник.

```shell
#!/bin/bash

if [ $# -ne 2 ]; then
        echo "Invalid input"
        exit 1
fi

if [[ ! -f $1 ]]; then
        echo "Invalid file name"
        exit 1
fi

if [[ ! -d $2 ]]; then
        echo "Invalid dir name"
        exit 1
fi

if [ $(find $2 -mindepth 1 -maxdepth 1|wc -l) -ne 0 ]; then
        echo "Directory not empty"
        exit 1
fi

touch $2/dict.txt

users=$(mktemp)

cat $1 | cut -d':' -f1 |egrep ".*\(.*"|cut -d'(' -f1|sed 's/ $//' > $users
cat $1 | cut -d':' -f1|egrep -v ".*\(.*" >> $users

cat $users | sort -d | uniq | awk '{print $1" "$2";"NR}' > $2/dict.txt

for number in $(cat $2/dict.txt | cut -d';' -f2); do
        touch $2/$number.txt
        us=$(cat $2/dict.txt | grep "$number"|cut -d';' -f1)
        cat $1 | grep "$us" >> $2/$number.txt
done

rm $users
```
#### 2018-SE-03.
Напишете скрипт, който приема два позиционни аргумента – имена на текстови файлове в CSV формат:
8,foo,bar,baz
2,quz,,foo
12,1,3,foo
3,foo,,
5,,bar,
7,,,
4,foo,bar,baz
10Валидни са следните условия:
• CSV файловете представляват таблица, като всеки ред на таблицата е записан на отделен ред;
• на даден ред всяко поле (колона) е разделено от останалите със запетая;
• броят на полетата на всеки ред е константа;
• в полетата не може да присъства запетая, т.е., запетаята винаги е разделител между полета;
• ако във файла присъстват интервали, то това са данни от дадено поле;
• първото поле на всеки ред е число, което представлява идентификатор на реда (ID).
Примерно извикване: ./foo.sh a.csv b.csv
Скриптът трябва да чете a.csv и на негова база да създава b.csv по следния начин:
• някои редове във файла се различават само по колоната ID, и за тях казваме, че формират
множество Ai
• за всяко такова множество Ai да се оставя само един ред - този, с най-малка стойност на ID-то;
• редовете, които не са членове в някое множество Ai се записват в изходния файл без промяна.
```shell
#!/bin/bash

if [ $# -ne 2 ]; then
        echo "Invalid input"
        exit 1
fi

lines=$(cat $1 | cut -d',' -f2-|sort -d|uniq)
for l in $lines; do
        cat $1 | grep "$l$" | sort -n -t',' -k1|head -n1 >> $2
done
```
