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
69. Напишете програма на C, която приема три параметъра – имена на двоични файлове.<br />
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
}__attribute((packed));

struct dataComparator{
        uint16_t type;
        uint16_t reserved[3];
        uint32_t offset1;
        uint32_t offset2;
}__attribute((packed));

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
