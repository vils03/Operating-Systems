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
