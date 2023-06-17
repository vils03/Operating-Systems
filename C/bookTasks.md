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
интервалите с начало xi и дължина yi от файла f2 и ги записва залепени в изходен файл f3.  <br />
Пример: <br />
• f1 съдържа 4 числа (2 двойки): 30000, 20, 19000, 10  <br />
• програмата записва в f3 две поредици 32-битови числа, взети от f2 както следва:  <br />
• най-напред се записват числата, които са на позиции 30000, 30001, 30002, ... 30019.  <br />
• след тях се записват числата от позиции 19000, 19001, ... 19009.  <br />
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
60. Напишете програма на C, която приема три параметъра – имена на двоични файлове.
Примерно извикване: <br />
$ ./main f1.bin f2.bin patch.bin <br />
Файловете f1.bin и f2.bin се третират като двоични файлове, състоящи се от байтове (uint8_t).
Файлът f1.bin e “оригиналният” файл, а f2.bin е негово копие, което е било модифицирано по
31някакъв начин (извън обхвата на тази задача). Файлът patch.bin е двоичен файл, състоящ се от
наредени тройки от следните елементи (и техните типове): <br />
• отместване (uint16_t) – спрямо началото на f1.bin/f2.bin <br />
• оригинален байт (uint8_t) – на тази позиция в f1.bin <br />
• нов байт (uint8_t) – на тази позиция в f2.bin <br />
Вашата програма да създава файла patch.bin, на базата на съществуващите файлове f1.bin и
f2.bin, като описва вътре само разликите между двата файла. Ако дадено отместване съществува само в единия от файловете f1.bin/f2.bin, програмата да прекратява изпълнението си по
подходящ начин. <br />
Примерен f1.bin:<br />
00000000: f5c4 b159 cc80 e2ef c1c7 c99a 2fb0 0d8c ...Y......../...<br />
00000010: 3c83 6fed 6b46 09d2 90df cf1e 9a3c 1f05 <.o.kF.......<..<br />
00000020: 05f9 4c29 fd58 a5f1 cb7b c9d0 b234 2398 ..L).X...{...4#.<br />
00000030: 35af 6be6 5a71 b23a 0e8d 08de def2 214c 5.k.Zq.:......!L<br />
Примерен f2.bin:<br />
00000000: f5c4 5959 cc80 e2ef c1c7 c99a 2fb0 0d8c ..YY......../...<br />
00000010: 3c83 6fed 6b46 09d2 90df cf1e 9a3c 1f05 <.o.kF.......<..<br />
00000020: 05f9 4c29 fd58 a5f1 cb7b c9d0 b234 2398 ..L).X...{...4#.<br />
00000030: afaf 6be6 5a71 b23a 0e8d 08de def2 214c ..k.Zq.:......!L<br />
Примерен patch.bin:<br />
00000000: 0200 b159 3000 35af ...Y0.5.<br />

```c
#include <fcntl.h>
#include <stdlib.h>
#include <sys/stat.h>
#include <unistd.h>
#include <stdint.h>
#include <err.h>

struct patch{
        uint16_t offset;
        uint8_t byte1;
        uint8_t byte2;
}__attribute((packed));

int main(int argc, char** argv){
        if(argc!=4){
                errx(1, "Invalid arguments count!");
        }
        int fd1, fd2, fd3;
        if((fd1=open(argv[1], O_RDONLY)) == -1){
                err(2, "ERROR: Could not open file %s for reading!", argv[1]);
        }
        if((fd2=open(argv[2], O_RDONLY)) == -1){
                close(fd1);
                err(2, "ERROR: Could not open file %s for reading!", argv[2]);
        }
        if((fd3=open(argv[3], O_WRONLY|O_CREAT|O_APPEND, 0766)) == -1){
                close(fd2);
                close(fd1);
                err(3, "ERROR: Could not open file %s for writing!", argv[3]);
        }
        struct stat st1;
        struct stat st2;
        if(fstat(fd1, &st1) == -1){
                err(4, "ERROR: Could not stat file %s!", argv[1]);
        }
        if(fstat(fd2, &st2) == -1){
                err(4, "ERROR: Could not stat file %s!", argv[1]);
        }
        uint32_t fd1_size = st1.st_size;
        uint32_t fd2_size = st2.st_size;

        if(fd1_size != fd2_size){
                close(fd1);
                close(fd2);
                close(fd3);
                errx(7, "Inconsistent files!");
        }

        struct patch p;
        for(p.offset=0;p.offset < fd1_size;++p.offset){
                if(read(fd1, &p.byte1, sizeof(p.byte1)) != sizeof(p.byte1)){
                        close(fd1);
                        close(fd2);
                        close(fd3);
                        err(5, "ERROR: Could not read from file %s!", argv[1]);
                }
                if(read(fd2, &p.byte2, sizeof(p.byte2)) != sizeof(p.byte2)){
                        close(fd1);
                        close(fd2);
                        close(fd3);
                        err(5, "ERROR: Could not read from file %s!", argv[2]);
                }
                if(p.byte1 != p.byte2){
                        if(write(fd3, &p, sizeof(p)) != sizeof(p)){
                                close(fd1);
                                close(fd2);
                                close(fd3);
                                err(6, "ERROR: Could not write to file %s!", argv[3]);
                        }
                }
        }
        close(fd1);
        close(fd2);
        close(fd3);
        exit(0);
}

```
