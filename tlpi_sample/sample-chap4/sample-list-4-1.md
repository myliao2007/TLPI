# 複製檔案

說明：這個程式的目的是示範複製檔案的功能，在程式設計層面可以學習到檔案的處理，基本的流程是開啟檔案、對檔案進行讀取或寫入的動作、最後不再存取檔案時則關閉檔案。

* 開啟檔案（並指定開啟檔案的權限模式）: open()
* 讀取檔案: read()
* 寫入檔案: write()
* 關閉檔案: close()

程式碼路徑：tlpi-dist/fileio/copy.c

{% code title="Listing 4-1: copy.c" lineNumbers="true" %}
```c
/*************************************************************************\
*                  Copyright (C) Michael Kerrisk, 2022.                   *
*                                                                         *
* This program is free software. You may use, modify, and redistribute it *
* under the terms of the GNU General Public License as published by the   *
* Free Software Foundation, either version 3 or (at your option) any      *
* later version. This program is distributed without any warranty.  See   *
* the file COPYING.gpl-v3 for details.                                    *
\*************************************************************************/

/* Listing 4-1 */

/* copy.c 
   將 argv[1] 指名的檔案複製到 argv[2] 指名的新檔案
*/
#include <sys/stat.h>
#include <fcntl.h>
#include "tlpi_hdr.h"

#ifndef BUF_SIZE        /* 在 cc 編譯時，可以帶入編譯參數，例如：使用 "-D BUF_SIZE=512" 的方式來以 512 複蓋這裡的定義值 1024 */
#define BUF_SIZE 1024
#endif

int
main(int argc, char *argv[])
{
    int inputFd, outputFd, openFlags;
    mode_t filePerms;
    ssize_t numRead;
    char buf[BUF_SIZE];

    if (argc != 3 || strcmp(argv[1], "--help") == 0)
        usageErr("%s old-file new-file\n", argv[0]);

    /* 開啟檔案做為讀取輸入與寫入輸出 */

    /* 開啟要複製的檔案來源，以唯讀模式開啟 */
    inputFd = open(argv[1], O_RDONLY);
    if (inputFd == -1)
        errExit("opening file %s", argv[1]);

    openFlags = O_CREAT | O_WRONLY | O_TRUNC;
    filePerms = S_IRUSR | S_IWUSR | S_IRGRP | S_IWGRP |
                S_IROTH | S_IWOTH;      /* rw-rw-rw- */

    /* 開啟要複製的檔案目的，需要能夠寫入 */                
    outputFd = open(argv[2], openFlags, filePerms);
    if (outputFd == -1)
        errExit("opening file %s", argv[2]);

    /* 持續複製資料，直到遇到檔案結尾或是發生錯誤為止 */
    while ((numRead = read(inputFd, buf, BUF_SIZE)) > 0)
        if (write(outputFd, buf, numRead) != numRead)
            fatal("write() returned error or partial write occurred");
            
    if (numRead == -1)
        errExit("read");

    if (close(inputFd) == -1)
        errExit("close input");
        
    if (close(outputFd) == -1)
        errExit("close output");

    exit(EXIT_SUCCESS);
}
```
{% endcode %}

編譯程式：

```bash
tlpi-dist/fileio$ gcc copy.c ../libtlpi.a -o copy -I ../lib
```

執行程式：測試複製檔案 copy.c 到 copy1.c。

```
$ ./copy copy.c copy1.c
```

如果想在編譯時指定 BUF\_SIZE，可以用 -D 編譯參數定義 BUF\_SIZE 的數值：

```bash
tlpi-dist/fileio$ gcc copy.c ../libtlpi.a -o copy -I ../lib -DBUF_SIZE=2048
```

