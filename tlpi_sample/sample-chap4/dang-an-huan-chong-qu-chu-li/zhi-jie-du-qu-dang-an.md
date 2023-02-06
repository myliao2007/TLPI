# 直接讀取檔案

說明：使用 O\_DIRECT 旗標來繞過緩衝區，直接讀寫設備或檔案。使用memalign()函數分配一塊記憶體，該記憶體區塊與其第一個參數以倍數對齊。

此範例使用O\_DIRECT旗標開啟與讀寫文件，這個程式最多接受四個參數，依次指定要讀取的文件、要從文件中讀取的位元數、程式在從文件讀取之前應該查詢的偏移量，以及傳遞給 read() 的資料緩衝區。最後兩個參數是非必要的選項，預設是 0 偏移量以及 4096 個位元組。

程式碼路徑：[tlpi-sample](https://github.com/myliao2007/tlpi-sample)/[tlpi-dist](https://github.com/myliao2007/tlpi-sample/tree/main/tlpi-dist)/[filebuff](https://github.com/myliao2007/tlpi-sample/tree/main/tlpi-dist/filebuff)/**direct\_read.c**

**Listing 13-1**

{% code title="direct_read.c" lineNumbers="true" %}
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

/* Listing 13-1 */

/* direct_read.c

   Demonstrate the use of O_DIRECT to perform I/O bypassing the buffer cache
   ("direct I/O").

   Usage: direct_read file length [offset [alignment]]

   This program is Linux-specific.
*/
#define _GNU_SOURCE     /* Obtain O_DIRECT definition from <fcntl.h> */
#include <fcntl.h>
#include <malloc.h>
#include "tlpi_hdr.h"

int
main(int argc, char *argv[])
{
    int fd;
    ssize_t numRead;
    size_t length, alignment;
    off_t offset;
    char *buf;

    if (argc < 3 || strcmp(argv[1], "--help") == 0)
        usageErr("%s file length [offset [alignment]]\n", argv[0]);

    length = getLong(argv[2], GN_ANY_BASE, "length");
    offset = (argc > 3) ? getLong(argv[3], GN_ANY_BASE, "offset") : 0;
    alignment = (argc > 4) ? getLong(argv[4], GN_ANY_BASE, "alignment") : 4096;

    fd = open(argv[1], O_RDONLY | O_DIRECT);
    if (fd == -1)
        errExit("open");

    /* memalign() allocates a block of memory aligned on an address that
       is a multiple of its first argument. By specifying this argument as
       2 * 'alignment' and then adding 'alignment' to the returned pointer,
       we ensure that 'buf' is aligned on a non-power-of-two multiple of
       'alignment'. We do this to ensure that if, for example, we ask
       for a 256-byte aligned buffer, we don't accidentally get
       a buffer that is also aligned on a 512-byte boundary. */

    buf = memalign(alignment * 2, length + alignment);
    if (buf == NULL)
        errExit("memalign");

    buf += alignment;

    if (lseek(fd, offset, SEEK_SET) == -1)
        errExit("lseek");

    numRead = read(fd, buf, length);
    if (numRead == -1)
        errExit("read");
    printf("Read %ld bytes\n", (long) numRead);

    exit(EXIT_SUCCESS);
}
```
{% endcode %}



