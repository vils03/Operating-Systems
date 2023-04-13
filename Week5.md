####1. Да се напише shell скрипт, който приканва потребителя да въведе низ (име) и изпечатва "Hello, низ".

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
```
