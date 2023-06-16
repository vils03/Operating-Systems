56. Напишете програма на C, която приема параметър - име на (двоичен) файл с байтове.
Програмата трябва да сортира файла

```c
#include <unistd.h>
#include <stdlib.h>
#include <fcntl.h>
#include <err.h>
#include <stdint.h>

int main(int argc, char** argv){
        if(argc != 2){
                errx(1, "ERROR: Wrong arguments count!");
        }
        int fd1;
        if((fd1 = open(argv[1], O_RDWR)) == -1){
                err(2, "ERROR: Could not open file %s!" , argv[1]);
        }
        uint8_t bytes[256];
        for(int i = 0; i< 256; ++i){
                bytes[i] = 0;
        }
        int bytesCount;
        uint8_t buf;
        while((bytesCount = read(fd1, &buf, sizeof(buf))) == sizeof(buf)){
                bytes[buf]++;
        }
        lseek(fd1, 0, SEEK_SET);
        for(int i = 0; i<256; ++i){
                uint8_t temp=i;
                for(int j=0; j<bytes[i]; ++j)
                {
                        if(write(fd1, &temp, sizeof(temp)) != sizeof(temp)){
                                err(3, "ERROR: Could not write to file %s!" , argv[1]);
                        }
                }
        }
        close(fd1);
        exit(0);
}
```
57. Двоичните файлове f1 и f2 съдържат 32 битови числа без знак (uint32_t). Файлът f1
е съдържа n двойки числа, нека i-тата двойка е < xi, yi >. Напишете програма на C, която извлича
интервалите с начало xi и дължина yi от файла f2 и ги записва залепени в изходен файл f3.
Пример:
• f1 съдържа 4 числа (2 двойки): 30000, 20, 19000, 10
• програмата записва в f3 две поредици 32-битови числа, взети от f2 както следва:
• най-напред се записват числата, които са на позиции 30000, 30001, 30002, ... 30019.
• след тях се записват числата от позиции 19000, 19001, ... 19009.
Забележка: С пълен брой точки ще се оценяват решения, които работят със скорост, пропорционална на размера на изходния файл f3.
```c

#include <err.h>
#include <stdlib.h>
#include <stdint.h>
#include <fcntl.h>
#include <unistd.h>

struct pair{
        uint32_t begin;
        uint32_t size;
};


int main(int argc, char** argv){
        if(argc != 4){
                errx(1, "ERROR: Invalid arguments count!");
        }
        int fd1, fd2, fd3;
        if((fd1 = open(argv[1], O_RDONLY)) == -1){
                err(2, "ERROR: Could not open file %s for reading!", argv[1]);
        }
        if((fd2 = open(argv[2], O_RDONLY)) == -1)
        {
                err(2, "EEROR: Could not open file %s for reading!", argv[2]);
        }
        if((fd3 = open(argv[3], O_WRONLY|O_CREAT, 0766) == -1)){
                err(3, "ERROR: Could not open %s for writing!" , argv[3]);
        }
        int bytes_read;
        struct pair p;
        while((bytes_read = read(fd1, &p, sizeof(p)) == sizeof(p))){
                lseek(fd2, p.begin, SEEK_SET);
                uint32_t buf;
                for(uint32_t i = 0; i<p.size; ++i){
                        if(read(fd2, &buf, sizeof(buf)) == -1){
                                err(4, "ERROR: Could not read from file");
                        }
                        if(write(fd3, &buf, sizeof(buf)) != sizeof(buf)){
                                err(4, "ERROR: Could not write to %s", argv[3]);
                        }
                }
        }

        if(bytes_read == -1){
                err(4, "ERROR: Could not read from file");
        }
        close(fd1);
        close(fd2);
        close(fd3);
        exit(0);
}

```
