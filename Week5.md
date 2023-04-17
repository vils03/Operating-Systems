#1. Да се напише shell скрипт, който приканва потребителя да въведе низ (име) и изпечатва "Hello, низ".

```shell
#!/bin/bash
  read name
  echo "Hello, ${name}!"
```

#2. Да се напише shell скрипт, който приема точно един параметър и проверява дали подаденият му параметър се състои само от букви и цифри.

```shell
#!/bin/bash

 if [ $# -ne 1 ]; then
     echo "Incorrect input"
     exit 1
 fi
 if echo $1 | egrep -q '^[a-zA-Z0-9]*$'; then
     echo "true"
     exit 0
 else
     echo "false"
     exit 1
 fi
```

#3. Да се напише shell скрипт, който приканва потребителя да въведе низ - потребителско име на потребител от системата - след което извежда на стандартния изход колко активни сесии има потребителят в момента.

```shell
 #!/bin/bash
 read username
 if [ $# -ne 1 ]; then
     echo "wrong input"
     exit 1
 fi
 echo $(who | grep "^${username}"|wc -l
 exit 0

```

#4. Да се напише shell скрипт, който приканва потребителя да въведе пълното име на директория и извежда на стандартния изход подходящо съобщение за броя на всички файлове и всички директории в нея.

```shell
  #!/bin/bash
  if [ $# -ne 1 ]; then
     echo "Invalid input"
     exit 1
 fi
 FILES=$(find $1 -mindepth 1 -type f | wc -l)
 DIRS=$(find $1 -mindepth 1 -type d | wc -l)
 echo "Number of files: $FILES"
 echo "Numer of dirs: $DIRS"
 exit 0

```

#5. Да се напише shell скрипт, който чете от стандартния вход имената на 3 файла, обединява редовете на първите два (man paste), подрежда ги по азбучен ред и резултата записва в третия файл.

```shell
#!/bin/bash
 if [ $# -ne 3 ]; then
     echo "Wrong input"
     exit 1
 fi
 paste $1 $2 | sort > $3

```

#6. Да се напише shell скрипт, който чете от стандартния вход име на файл и символен низ, проверява дали низа се съдържа във файла и извежда на стандартния изход кода на завършване на командата с която сте проверили наличието на низа.

NB! Символният низ може да съдържа интервал (' ') в себе си.
```shell
  #!/bin/bash
    if [ $# -ne 2 ]; then
      echo "Wrong input"
      exit 1
  fi
  cat $1 | grep -q $2
  echo $?
```

#7. Имате компилируем (a.k.a няма синтактични грешки) source file на езика C. Напишете shell script, който да покaзва колко е дълбоко най-дълбокото nest-ване (влагане).
Примерен .c файл:

#include <stdio.h>

int main(int argc, char *argv[]) {

  if (argc == 1) {
		printf("There is only 1 argument");
	} else {
		printf("There are more than 1 arguments");
	}

	return 0;
}
Тук влагането е 2, понеже имаме main блок, а вътре в него if блок.

Примерно извикване на скрипта:

./count_nesting sum_c_code.c

Изход:
The deepest nesting is 2 levels
```shell
 #!/bin/bash
  if [ $# -ne 1 ]; then
      echo "Wring input"
      exit 1
  fi
  grep -o '[{}]' $1|uniq -c|awk 'BEGIN {max=0; cur=0} $2=="{{cur+=$1} $2=="}" {cur-=1} {if(cur>max){max=cur}} END {print max}'

```

#8. Напишете shell script, който да приема параметър име на директория, от която взимаме файлове, и опционално експлицитно име на директория, в която ще копираме файлове. Скриптът да копира файловете със съдържание, променено преди по-малко от 45 мин, от първата директория във втората директория. Ако втората директория не е подадена по име, нека да получи такова от днешната дата във формат, който ви е удобен. При желание новосъздадената директория да се архивира
```shell
 #!/bin/bash
 
 if [ $# -eq 1 ]; then
      SOURCE=$1
      DEST=$(date -I)
 elif [ $# -eq 2 ]; then
      SOURCE=$1
      DEST=$2
else
      echo "Invalid input"
      exit 1
fi
find $SOURCE -mindepth 1 -type f -mmin -45 -exec cp {} $DEST \;
tar -cf "${DEST}.tar" $DEST
```

#9. Да се напише shell скрипт, който получава при стартиране като параметър в командния ред идентификатор на потребител. Скриптът периодично (sleep(1)) да проверява дали потребителят е log-нат, и ако да - да прекратява изпълнението си, извеждайки на стандартния изход подходящо съобщение.

NB! Можете да тествате по същият начин като в 05-b-4300.txt
```shell
  #!/bin/bash
 
  if [ $# -ne 1 ]; then
      echo "Incorrect input"
      exit 1
  fi
  while [ true ]; do
      sleep 1
      if [ $(who | grep $1 | wc -l) -ne 0 ]; then
         echo "Logged in"
         break
      else
         echo "Not logged"
     fi
 done

```

#10. Да се напише shell скрипт, който валидира дали дадено цяло число попада в целочислен интервал.
Скриптът приема 3 аргумента: числото, което трябва да се провери; лява граница на интервала; дясна граница на интервала.
Скриптът да връща exit status:
- 3, когато поне един от трите аргумента не е цяло число
- 2, когато границите на интервала са обърнати
- 1, когато числото не попада в интервала
- 0, когато числото попада в интервала

Примери:
$ ./validint.sh -42 0 102; echo $?
1

$ ./validint.sh 88 94 280; echo $?
1

$ ./validint.sh 32 42 0; echo $?
2

$ ./validint.sh asdf - 280; echo $?
3

```shell
  #!/bin/bash
 
  if [ $# -ne 3 ]; then
      echo "Invalid input"
      exit 1
  fi
  regex="^[+-]?[0-9]+$"
  for ARG in $@; do
      if [[ $ARG =~ $regex ]]; then
          continue
      else
          echo "Not valid numbers"
          exit 3
      fi
  done
  if [ $2 -gt $3 ]; then
      echo "Reversed interval"
      exit 2
  fi
  if [ $1 -gt $2 ] && [ $1 -lt $3 ]; then
      echo "In interval"
      exit 0
  else
      echo "Not in interval"
      exit 1
  fi

```

#11. Да се напише shell скрипт, който форматира големи числа, за да са по-лесни за четене.
Като пръв аргумент на скрипта се подава цяло число.
Като втори незадължителен аргумент се подава разделител. По подразбиране цифрите се разделят с празен интервал.

Примери:
$ ./nicenumber.sh 1889734853
1 889 734 853

$ ./nicenumber.sh 7632223 ,
7,632,223
```shell

```

```shell

```
