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

#8. Напишете shell script, който автоматично да попълва файла указател от предната задача по подадени аргументи: име на файла указател, пълно име на човека (това, което очакваме да е в /etc/passwd) и избран за него nickname.
Файлът указател нека да е във формат:
<nickname, който лесно да запомните> <username в os-server>
// може да сложите и друг delimiter вместо интервал

Примерно извикване:
./pupulate_address_book myAddressBook "Ben Dover" uncleBen

Добавя към myAddressBook entry-то:
uncleBen <username на Ben Dover в os-server>

***Бонус: Ако има няколко съвпадения за въведеното име (напр. има 10 човека Ivan Petrov в /etc/passwd), всички те да се показват на потребителя, заедно с пореден номер >=1,
след което той да може да въведе някой от номерата (или 0 ако не си хареса никого), и само избраният да бъде добавен към указателя.

```shell

```


```shell

```


```shell

```


```shell

```

```shell

```
