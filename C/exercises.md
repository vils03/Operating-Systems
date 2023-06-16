1. Копирайте съдържанието на файл1 във файл2

```c

```

2. Реализирайте команда head без опции (т.е. винаги да извежда на стандартния изход само първите 10 реда от
съдържанието на файл подаден като първи параматър).

```c
#include <err.h>
#include <fcntl.h>
#include <stdlib.h>
#include <unistd.h>

int main(int argc, char* argv[])
{
        int fd1;
        int i = 0;
        char c;

        if (argc != 2) {
                errx(1, "ERROR: Invalid arguments!");
        }

        if ((fd1 = open(argv[1], O_RDONLY)) == -1) {
                err(2, "File failed to open in read mode %s", argv[1]);
        }
        int bytes_counter =0 ;
        while ((bytes_counter=read(fd1, &c, sizeof(c)) == sizeof(c))) {
                if (c == '\n') {
                        i=i+1;
                }
                if (i < 10) {
                        if(write(1, &c, sizeof(c)) != sizeof(c))
                                err(4, "ERROR: Could not write t ostdout!");
                }
                else
                        break;
        }
        if(bytes_counter == -1)
                err(3, "ERROR: could not read from file");
        close(fd1);
        exit(0);
}

```
