Зад. 58 2016-SE-01 Напишете програма на C, която приема параметър - име на (двоичен) файл с байтове.
Програмата трябва да сортира файла.

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
Зад. 59 2016-SE-02. Двоичните файлове f1 и f2 съдържат 32 битови числа без знак (uint32_t). Файлът f1
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
Зад. 62 2017-SE-01. Напишете програма на C, която приема три параметъра – имена на двоични файлове.
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
65 2017-SE-04 Напишете програма на C, която да работи подобно на командата cat, реализирайки
само следната функционалност:<br />
• програмата извежда на STDOUT<br />
• ако няма подадени параметри, програмата чете от STDIN<br />
• ако има подадени параметри – файлове, програмата последователно ги извежда<br />
• ако някой от параметрите започва с тире (-), програмата да го третира като специално име
за STDIN<br />
Примерно извикване:<br />
$ ./main f - g<br />
– извежда съдържанието на файла f, после STDIN, след това съдържанието на файла g<br />

```c
#include <stdint.h>
#include <unistd.h>
#include <err.h>
#include <stdio.h>
#include <fcntl.h>
#include <string.h>
#include <stdlib.h>

void writeToStdout(int);
void writeToStdout(int fd){
        int bytes_count;
        char byte;
        while((bytes_count = read(fd, &byte, sizeof(byte))) > 0){
                if(write(1, &byte, sizeof(byte)) != sizeof(byte))
                        err(2, "ERROR: Could not write to stdout!");
                }

}

int main(int argc, char** argv){
        int fd;
        if(argc==1)
                writeToStdout(0);
        for(int i = 1;i<argc;++i){
                if(strcmp(argv[i], "-") ==0)
                        fd = 0;
                else{
                        if((fd=open(argv[i], O_RDONLY)) == -1)
                                err(1, "ERROR: Could not open file %s for reading!", argv[i]);
                }
                writeToStdout(fd);
                close(fd);
        }
        exit(0);
}

```

Зад. 71 2020-IN-01. Напишете програма на C, която приема три параметъра – имена на двоични файлове.<br />
Примерно извикване: <br />
$ ./main patch.bin f1.bin f2.bin<br />
Файловете patch.bin и f1.bin съществуват, и на тяхна база програмата трябва да създаде f2.bin.
Файлът patch.bin се състои от две секции – 16 байтов хедър и данни. На базата на хедъра програмата трябва да може да интерпретира съдържанието на файла. Структурата на хедъра е:<br />
• uint32_t, magic – магическа стойност 0xEFBEADDE, която дефинира, че файлът следва тази
спецификация<br />
• uint8_t, header version – версия на хедъра, с единствена допустима стойност за момента 0x01,
която дефинира останалите байтове от хедъра както следва:<br />
– uint8_t, data version – версия (описание) на използваните структури в секцията за данни
на файла<br />
– uint16_t, count – брой записи в секцията за данни<br />
– uint32_t, reserved 1 – не се използва<br />
– uint32_t, reserved 2 – не се използва<br />
Възможни структури в секцията за данни на файла спрямо data version:<br />
• при версия 0x00<br />
– uint16_t, offset<br />
– uint8_t, original byte<br />
– uint8_t, new byte<br />
• при версия 0x01<br />
– uint32_t, offset<br />
– uint16_t, original word<br />
– uint16_t, new word<br />
• забележка: и при двете описани версии offset е отместване в брой елементи спрямо началото
на файла<br />
Двоичните файлове f1.bin и f2.bin се третират като състоящи се от елементи спрямо data version
в patch.bin.<br />
Програмата да създава файла f2.bin като копие на файла f1.bin, но с отразени промени на базата
на файла patch.bin, при следния алгоритъм:<br />
• за всяка наредена тройка от секцията за данни на patch.bin, ако на съответният offset в
оригиналния файл f1.bin е записан елементът original byte/word, в изходният файл се записва new byte/word. Ако не е записан такъв елемент или той не съществува, програмата да
прекратява изпълнението си по подходящ начин;<br />
• всички останали елементи се копират директно.<br />
Наредените тройки в секцията за данни на файла patch.bin да се обработват последователно.
Обърнете внимание на обработката за грешки и съобщенията към потребителя – искаме програмата
да бъде удобен и валиден инструмент.

```c
//only for data version 0x00
#include <stdint.h>
#include <unistd.h>
#include <fcntl.h>
#include <err.h>
#include <stdlib.h>

struct header{
        uint32_t magic;
        uint8_t headerVersion;
        uint8_t dataVersion;
        uint16_t count;
        uint32_t _;
        uint32_t __;
}__attribute((packed));

struct data1{
        uint16_t offset;
        uint8_t oldByte;
        uint8_t newByte;
};

int main(int argc, char** argv){
        if(argc!=4){
                errx(1, "ERROR: Invalid argument count!");
        }
        int fd1, fd2, fd3;
        if((fd1=open(argv[1], O_RDONLY)) == -1){
                err(2, "ERROR: Could not open file %s for reading!", argv[1]);
        }
        if((fd2=open(argv[2], O_RDONLY)) == -1){
                err(2, "ERROR: Could not open file %s for reading!", argv[2]);
        }

        if((fd3=open(argv[3], O_RDWR|O_TRUNC|O_CREAT, 0766)) == -1){
                err(3, "ERROR: Could not open file %s for writing!", argv[3]);
        }
        struct header h;
        if(read(fd1, &h, sizeof(h)) == -1){
                err(4, "ERROR: Could not read from file %s!", argv[1]);
        }
        if(h.magic != 0xEFBEADDE || h.headerVersion != 0x01){
                errx(5, "ERROR: Invalid magic number or header version!");
        }
        uint16_t dataCount = h.count;
        if(h.dataVersion == 0x00){
                struct data1 d;
                for(uint16_t i = 0;i<dataCount;++i){
                        if(read(fd1, &d, sizeof(d)) != sizeof(d)){
                                err(4, "ERROR: Could not read from file %s", argv[1]);
                        }
                        uint8_t fd2Byte;
                        for(uint16_t j =0;j<d.offset;++j){
                                if(read(fd2, &fd2Byte, 1) != 1){
                                        err(4, "ERROR: Could not read from file %s!", argv[2]);
                                }
                                if(write(fd3, &fd2Byte, 1) != 1){
                                        errx(7, "ERROR: Could not write to file %s!", argv[3]);
                                }
                        }
                        lseek(fd2, d.offset, SEEK_SET);
                        if(read(fd2, &fd2Byte, 1) != 1){
                                err(4, "ERROR: Could not read from file %s" , argv[2]);
                        }
                        if(fd2Byte != d.oldByte){
                                errx(6, "ERROR: Different bytes!");
                        }
                        else{
                                if(write(fd3, &d.newByte, 1) != 1){
                                        err(7, "ERROR: Could not write to file %s!", argv[3]);
                                }
                        }
                }
        }
        close(fd1);
        close(fd2);
        close(fd3);
        exit(0);
}
```
Зад. 78 2022-SE-01 Напишете програма на C, която приема два позиционни аргумента – имена на
двоични файлове. Примерно извикване: ./main data.bin comparator.bin<br />
Файлът data.bin се състои от две секции – 8 байтов хедър и данни. Структурата на хедъра е:<br />
• uint32_t, magic – магическа стойност 0x21796F4A, която дефинира, че файлът следва тази
спецификация;<br />
• uint32_t, count – описва броя на елементите в секцията с данни.<br />
Секцията за данни се състои от елементи – uint64_t числа.<br />
Файлът comparator.bin се състои от две секции – 16 байтов хедър и данни. Структурата на хедъра
е:<br />
• uint32_t, magic1 – магическа стойност 0xAFBC7A37;<br />
• uint16_t, magic2 – магическа стойност 0x1C27;<br />
• комбинацията от горните две magic числа дефинира, че файлът следва тази спецификация;<br />
• uint16_t, reserved – не се използва;<br />
• uint64_t, count – описва броя на елементите в секциата с данни.<br />
Секцията за данни се състои от елементи – наредени 6-торки:<br />
• uint16_t, type – възможни стойности: 0 или 1;<br />
• 3 бр. uint16_t, reserved – възможни стойности за всяко: 0;<br />
• uint32_t, offset1;<br />
• uint32_t, offset2.<br />
Двете числа offset дефинират отместване (спрямо N0) в брой елементи за data.bin; type дефинира
операция за сравнение:<br />
• 0: “по-малко”;<br />
• 1: “по-голямо”.<br />
Елементите в comparator.bin дефинират правила от вида:<br />
• “елементът на offset1” трябва да е “по-малък” от “елементът на offset2”;<br />
• “елементът на offset1” трябва да е “по-голям” от “елементът на offset2”.<br />
Програмата трябва да интепретира по ред дефинираните правила в comparator.bin и ако правилото
не е изпълнено, да разменя in-place елементите на съответните отмествания. Елементи, които са
равни, няма нужда да се разменят.

```c

#include <unistd.h>
#include <fcntl.h>
#include <stdint.h>
#include <err.h>
#include <stdlib.h>

struct headerData{
        uint32_t magic;
        uint32_t count;
};

struct headerComparator{
        uint32_t magic1;
        uint32_t magic2;
        uint16_t _;
        uint64_t count;
}__attribute__((packed));

struct dataComparator{
        uint16_t type;
        uint16_t reserved[3];
        uint32_t offset1;
        uint32_t offset2;
}__attribute__((packed));

void swapNumbers(int,uint32_t,uint32_t, uint64_t,uint64_t);
void swapNumbers(int fd1, uint32_t offset1, uint32_t offset2, uint64_t number1, uint64_t number2){
        //proverki za lseek
        lseek(fd1, 8 + offset1*8, SEEK_SET);
        if(write(fd1, &number2, sizeof(number2)) != sizeof(number2)){
                err(8, "ERROR: Could not write to file!");
        }
        lseek(fd1, 8+offset2*8, SEEK_SET);
        if(write(fd1, &number1, sizeof(number1)) != sizeof(number1)){
                err(8, "ERROR: Could not write to file!");
        }
}

int main(int argc, char** argv){
        if(argc!=3){
                errx(1, "Invalid argument count!");
        }
        int fd1, fd2;
        if((fd1=open(argv[1], O_RDWR)) == -1){
                err(2, "ERROR: Could not open file %s for writing and reading!", argv[1]);
        }
        if((fd2=open(argv[2], O_RDONLY)) == -1){
                err(3, "ERROR: Could not open file %s for reading!", argv[2]);
        }
        struct headerData hd;
        if(read(fd1, &hd, sizeof(hd)) != sizeof(hd)){
                err(4, "ERROR: Could not read from file %s!", argv[1]);
        }
        if(hd.magic != 0x21796F4A ){
                errx(5, "ERROR: Invalid data header magic number!");
        }
        uint32_t dataCount = hd.count;


        struct headerComparator hc;
        if(read(fd2, &hc, sizeof(hc)) != sizeof(hc)){
                err(4, "ERROR: Could not read from file %s!", argv[2]);
        }
        if(hc.magic1 != 0xAFBC7A37 || hc.magic2 != 0x1C27){
                errx(5, "ERROR: Invalid comparator header magic number!");
        }
        uint32_t compDataCount = hc.count;

        for(uint32_t i =0;i<compDataCount;++i){
                struct dataComparator dc;
                if(read(fd2, &dc, sizeof(dc)) != sizeof(dc)){
                        err(4, "ERROR: Could not read from file %s!", argv[2]);
                }
                        for(int  j=0;j<3;++j){
                                if(dc.reserved[j] != 0){
                                        errx(6, "ERROR: Invalid reserved number!");
                                }
                        }
                        if(dc.offset1 > dataCount || dc.offset2>dataCount){
                                errx(7, "ERROR: Invalid offset number!");
                        }
                        lseek(fd1, 8*dc.offset1 + 8, SEEK_SET);
                        uint64_t number1;
                        if(read(fd1, &number1, sizeof(number1)) != sizeof(number1)){
                                err(4, "ERROR: Could not read from file %s!", argv[1]);
                        }
                        lseek(fd1, 8*dc.offset2 + 8,SEEK_SET);
                        uint64_t number2;
                        if(read(fd1, &number2, sizeof(number2)) != sizeof(number2)){
                                err(4, "ERROR: Could not read from file %s!", argv[1]);
                        }
                        if(dc.type == 0){
                                if(number1 > number2){
                                        swapNumbers(fd1, dc.offset1,dc.offset2, number1, number2);
                                }
                        }
                        else if(dc.type == 1){
                                if(number1<number2){
                                        swapNumbers(fd1, dc.offset1, dc.offset2, number1, number2);
                                }
                        }
        }
        close(fd1);
        close(fd2);
        exit(0);
}

```
Зад. 80 2016-SE-01 Напишете програма на C, която по подадено име на (текстови) файл като параметър,
извежда съдържанието на файла сортирано, чрез употреба на външните програми cat и sort през
pipe().

```c

#include <unistd.h>
#include <fcntl.h>
#include <stdint.h>
#include <err.h>
#include <stdlib.h>
#include <sys/wait.h>

int main(int argc, char** argv){
        if(argc!=2)
                errx(1,"ERROR: Invalid argument count!");
        int pf[2];
        if(pipe(pf) == -1)
                err(2, "ERROR: Could not pipe!");
        pid_t child = fork();
        if(child == -1)
                err(3, "ERROR: Could not fork!");
        if(child == 0){
                close(pf[0]);
                if(dup2(pf[1], 1)==-1)
                        err(5, "ERROR: Could not dup!");
                if(execlp("cat", "cat",argv[1], NULL) ==-1)
                        err(4, "ERROR: Could not exec cat!");
        }
        close(pf[1]);
        int status;
        if(wait(&status) == -1)
                err(6, "ERROR: Could not wait!");
        if(dup2(pf[0], 0)==-1)
                err(5, "ERROR: Could not dup!");
        if(execlp("sort", "sort", NULL) == -1)
                err(4, "ERROR: Could not exec sort");
        close(pf[0]);
}

```
Зад. 82 2017-IN-01 Напишете програма на C, която използвайки външни shell команди през pipe() да
извежда статистика за броя на използване на различните shell-ове от потребителите, дефинирани
в системата. Изходът да бъде сортиран във възходящ ред според брой използвания на shell-овете.

```c
#include <stdint.h>
#include <unistd.h>
#include <err.h>
#include <stdlib.h>
#include <sys/wait.h>

int main(void){
        int p[3][2];
        //cut
        if(pipe(p[0]) == -1)
                err(1, "ERROR: Could not pipe 1!");
        pid_t child1 = fork();
        if(child1 == -1)
                err(2, "ERROR: Could not fork!");
        if(child1 == 0){
                close(p[0][0]);
                if(dup2(p[0][1], 1) == -1)
                        err(4, "ERROR: Could not dup!");
                if(execlp("cut", "cut", "-d:", "-f7", "/etc/passwd", NULL) == -1)
                        err(3, "ERROR: Could dnot exec cut!");
        }
        close(p[0][1]);
        wait(NULL);
        //sort
        if(pipe(p[1]) == -1)
                err(1, "ERROR: Could not pipe 2!");
        pid_t child2 = fork();
        if(child2 == -1)
                err(2, "ERROR: Culd not fork!");
        if(child2 == 0){
                close(p[1][0]);
                if(dup2(p[0][0], 0) == -1)
                        err(4, "ERROR: Could not dup!");
                if(dup2(p[1][1], 1) == -1)
                        err(4, "ERROR: Could not dup!");
                if(execlp("sort", "sort", NULL) == -1)
                        err(3, "ERROR: could not exec sort!");
        }
        close(p[1][1]);
        wait(NULL);
        //uniq
        if(pipe(p[2]) == -1)
                err(1, "ERROR: Could not pipe!");
        pid_t child3 = fork();
        if(child3 == -1)
                err(2, "ERROR: Could not fork!");
        if(child3 == 0){
                close(p[2][0]);
                if(dup2(p[1][0], 0) == -1)
                        err(4, "ERROR: Could not dup!");
                if(dup2(p[2][1],1) == -1)
                        err(4, "ERROR: Culd not dup!");
                if(execlp("uniq", "uniq", "-c", NULL) == -1)
                        err(3, "ERROR: Could not exec uniq!");
        }
        close(p[2][1]);
        wait(NULL);
        //sort -n
        if(dup2(p[2][0], 0) == -1)
                err(4, "ERROR: Could not dup!");
        if(execlp("sort", "sort", "-n", NULL) == -1)
                err(3, "ERROR: Could not exec sort -n!");
        exit(0);


}
```
Зад. 83 2017-IN-02 Напишете програма на C, която приема незадължителен параметър – име на команда. Ако не е зададена команда като параметър, да се ползва командата echo. Максималната
допустима дължина на командата е 4 знака.
Програмата чете низове (с максимална дължина 4 знака) от стандартния си вход, разделени с
интервали (0x20) или знак за нов ред (0x0A). Ако някой низ е с дължина по-голяма от 4 знака, то
програмата да терминира със съобщение за грешка.
Подадените на стандартния вход низове програмата трябва да третира като множество от параметри за дефинираната команда. Програмата ви трябва да изпълни командата колкото пъти е
необходимо с максимум два низа като параметри, като изчаква изпълнението да приключи, преди
да започне ново изпълнение.<br />
Примерни вход, извиквания и изходи:<br />
$ cat f1<br />
a1<br />
$ cat f2<br />
a2<br />
$ cat f3<br />
a3<br />
$ echo -e "f1\nf2 f3" | ./main cat<br />
a1<br />
a2<br />
a3<br />
$ echo -e "f1\nf2 f3" | ./main<br />
f1 f2<br />
f3<br />

```c

#include <stdint.h>
#include <unistd.h>
#include <stdlib.h>
#include <sys/wait.h>
#include <err.h>
#include <string.h>

const int MAX_SIZE = 1024;

int main(int argc, char** argv){
        if(argc>2)
                errx(1, "ERROR: Invalid argument count!");
        char command[MAX_SIZE];
        if(argc==1)
                strcpy(command, "echo");
        else if(argc==2){
                if(strlen(argv[1]) >4)
                        errx(2, "ERROR: Command length must be less than four!");
                else
                        strcpy(command, argv[1]);
        }
        char buff;
        char par[MAX_SIZE];
        int index =0;
        while(read(0, &buff, sizeof(buff)) > 0){
                if(buff == ' ' || buff == '\n'){
                        par[index] = '\0';
                        index = 0;
                        if(strlen(par) > 4)
                                errx(2,"ERROR: Parameter length must be less than four!");
                        pid_t child = fork();
                        if(child == -1)
                                err(3, "ERROR: Could not fork!");
                        if(child == 0)
                        {
                                if(execlp(command, command, par, NULL) == -1)
                                        err(4, "ERROR: Could not exec!");
                        }
                        wait(NULL);
                }
                else{
                        par[index++] = buff;
                }
        }
        exit(0);
}
```


Зад. 84 2018-SE-01 Напишете програма на C, която приема параметър – име на директория. Програмата
трябва да извежда името на най-скоро променения (по съдържание) файл в тази директория и
нейните под-директории, чрез употреба на външни шел команди през pipe().
```c

#include <unistd.h>
#include <stdint.h>
#include <sys/wait.h>
#include <err.h>
#include <stdlib.h>

int main(int argc, char** argv){
        if(argc!=2)
                errx(1, "ERROR: Invalid argument count!");
        int p[3][2];
        //find
        if(pipe(p[0]) == -1)
                err(2, "ERROR: Could not pipe!");
        pid_t child1 = fork();
        if(child1 == -1)
                err(3, "ERROR: Could not fork!");
        if(child1 == 0){
                close(p[0][0]);
                if(dup2(p[0][1],1) == -1)
                        err(4, "ERROR: Could not dup!");
                if(execlp("find", "find",argv[1], "-type", "f", "-printf" ,"%AT %f\n", NULL) == -1)
                        err(5, "ERROR: Could not exec find!");


        }
        close(p[0][1]);

        //sort
        if(pipe(p[1]) == -1)
                err(2, "ERROR: Could not pipe!");
        pid_t child2 = fork();
        if(child2 == -1)
                err(3, "ERROR: Could not fork!");
        if(child2 == 0){
                close(p[1][0]);
                if(dup2(p[0][0],0) == -1)
                        err(4, "ERROR: Could not dup!");
                if(dup2(p[1][1], 1) == -1)
                        err(4, "ERROR: Could not dup!");
                if(execlp("sort", "sort", "-n", "-k1", "-r", NULL) == -1)
                        err(5, "ERROR: Could not exec sort!");
        }
        close(p[1][1]);

        //cut
        if(pipe(p[2]) == -1)
                err(2, "ERROR: Could not pipe!");
        pid_t child3 = fork();
        if(child3 == -1)
                err(3, "ERROR: Could not fork!");
        if(child3 == 0){
                close(p[2][0]);
                if(dup2(p[1][0],0) == -1)
                        err(4, "ERROR: Could not dup!");
                if(dup2(p[2][1], 1) == -1)
                        err(4, "ERROR: Could not dup!");
                if(execlp("cut", "cut", "-d", " ", "-f2", NULL) == -1)
                        err(5, "ERROR: Could not exec cut!");
        }
        wait(NULL);
        if(dup2(p[2][0], 0) == -1)
                err(4, "ERROR: Could not dup!");
        if(execlp("head", "head", "-n1", NULL) == -1)
                err(5, "ERROR: Could not exec head!");
        exit(0);


}
```

Зад. 86 2020-SE-01 Напишете две програми на C (foo и bar), които си комуникират през наименована
тръба. Програмата foo приема параметър - име на файл, програмата bar приема параметър -
команда като абсолютен път до изпълним файл.
Примерни извиквания и ред на изпълнение (в отделни терминали):<br />
./foo a.txt<br />
./bar /usr/bin/sort<br />
Програмата foo трябва да изпълнява външна команда cat с аргумент името на подадения файл,
така че съдържанието му да се прехвърли през тръбата към програмата bar, която от своя страна
трябва да изпълни подадената и като аргумент команда (без параметри; /usr/bin/sort в примера),
която да обработи получените през тръбата данни, четейки от стандартен вход. Еквивалент на
горния пример би било следното изпълнение:<br />
cat a.txt | /usr/bin/sort

```c
//foo.c
#include <unistd.h>
#include <stdint.h>
#include <err.h>
#include <stdlib.h>
#include <fcntl.h>
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <sys/wait.h>


int main(int argc, char** argv){
        if(argc!=2){
                errx(1, "ERROR: Invalid argument count!");
        }
        const char* fifo = "./temp";
        if(mkfifo(fifo, 0666) == -1)
                err(2, "ERROR: Could not create named pipe!");
        pid_t child = fork();
        if(child == -1)
                err(3, "ERROR: Could not fork!");
        if(child ==0){
                int fd;
                if((fd =open(fifo, O_WRONLY)) == -1)
                        err(4, "ERROR: Could not open fifo!");
                if(dup2(fd, 1) == -1)
                        err(5, "ERROR: Could not dup!");
                if(execlp("cat", "cat",argv[1], NULL) == -1)
                        err(6, "ERROR: Could not exec cat!");
        }
        wait(NULL);
        exit(0);
}


//bar.c
#include <fcntl.h>
#include <stdlib.h>
#include <unistd.h>
#include <err.h>
#include <stdint.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <sys/wait.h>

int main(int argc, char** argv){
        if(argc!=2)
                errx(1, "ERROR: Invalid argument count!");
        const char* fifo = "./temp";
        pid_t child = fork();
        if(child == -1)
                err(2, "ERROR: Could not fork!");
        if(child == 0){
                int fd;
                if((fd =open(fifo, O_RDONLY)) == -1)
                        err(3, "ERROR: Could not open fifo!");
                if(dup2(fd, 0) == -1)
                        err(4, "ERROR: Could not dup!");
                if(execl(argv[1], argv[1], NULL) == -1)
                        err(5,"ERROR: Could not exec %s", argv[1]);
        }
        wait(NULL);
        if(unlink(fifo) == -1)
                err(6, "ERROR: Could not unlink!");
        exit(0);
}

```

Зад. 88 2020-SE-03 Напишете програма на C, която приема един задължителен позиционен параметър
- име на файл. Файлът се състои от не повече от 8 наредени тройки елементи:<br />
• име на файл – точно 8 байта, последният от които задължително е 0x00. Ако името е по-късо
от 7 знака, излишните байтове са 0x00;<br />
• offset – uint32_t, който дава пореден номер на елемент (спрямо N0) във файла;<br />
• length – uint32_t, който дава брой елементи.<br />
За всяка наредена тройка програмата трябва да пусне child процес, който да XOR-не (обработи с
изключващо-или) елементите (uint16_t) от съответния файл един със друг, и да върне резултата
на parent процеса, който от своя страна трябва да XOR-не всички получените резултати и да изведе
полученото число в следния формат:<br />
result: 573B<br />
Забележка: С пълен брой точки се оценяват решения, в които child процесите работят паралелно
```c
#include <fcntl.h>
#include <unistd.h>
#include <stdint.h>
#include <err.h>
#include <sys/wait.h>
#include <stdio.h>
#include <stdlib.h>

struct triple{
        char name[8];
        uint32_t offset;
        uint32_t length;
}__attribute__((packed));

int main(int argc, char** argv){
        if(argc!=2){
                errx(1, "ERROR: Invalid argument count!");
        }
        int fd;
        if((fd=open(argv[1], O_RDONLY)) == -1)
                err(2, "ERROR: Could not open file %s!", argv[1]);
        int bytes_count;
        struct triple tr;
        int p[2];
        if(pipe(p) == -1)
                err(3, "ERROR: Could not pipe!");
        while((bytes_count = read(fd, &tr, sizeof(tr))) >0){
                pid_t child = fork();
                if(child == -1)
                        err(4, "ERROR: Could not fork!");
                if(child == 0){
                        close(p[0]);
                        int f;
                        if((f=open(tr.name, O_RDONLY)) == -1)
                                err(2, "ERROR: Could not open file %s", tr.name);
                        if(dup2(p[1],1) == -1)
                                err(5, "ERROR: Could not dup!");
                        if(lseek(f, tr.offset*2, SEEK_SET) == -1)
                                err(6, "ERROR: Could not lseek!");
                        uint16_t number=0;
                        for(uint32_t i=0;i < tr.length;++i)
                        {
                                uint16_t n;
                                if(read(f, &n, sizeof(n)) == -1)
                                        err(7, "ERROR: COuld  not read from fiel!");
                                number^=n;
                        }
                        if(write(p[1], &number, sizeof(number)) != sizeof(number))
                                err(8, "ERROR: Could not write to file!");
                }
                exit(0);
        }
        uint16_t n=0;
        uint16_t num;
        close(p[1]);
        while(read(p[0], &num, sizeof(num)) > 0){
                n^=num;
        }
        if(dprintf(1, "result: %d", num)==-1)
                err(9, "ERROR: Could not print!");
}
```
