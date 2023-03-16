# TLPI 程式碼問答集

原文來源：[https://man7.org/tlpi/code/faq.html](https://man7.org/tlpi/code/faq.html)

## 一般的錯誤與警告

### 問題一：Warning: "\_XOPEN\_SOURCE" redefined

當使用提供的 makefile 編譯程式時，某些範例程式的“書籍”版本會產生以下編譯警告：

```bash
warning: "_XOPEN_SOURCE" redefined
```

要了解出現此警告的原因，需注意兩條有用的背景訊息：

*   本書中的所有原始碼範例都是寫出來的，因此它們預設會使用 GNU C 編譯器選項，或以下選項（對應於符合 SUSv3+C99 標準的編譯環境）進行編譯：

    <pre class="language-bash"><code class="lang-bash"><strong>$ cc -std=c99 -D_XOPEN_SOURCE=600
    </strong></code></pre>
* 程式的 makefile 包含了啟用大多數可能警告的編譯器選項 (-pedantic -Wall -W)。

範例程式中使用的一些介面需要有定義 \_XOPEN\_SOURCE。如果使用這些介面的程式是用 -std=c99 -D\_XOPEN\_SOURCE=600 編譯的，那麼一切都很正常。但是，如果此類程式在編譯時沒有定義 \_XOPEN\_SOURCE ，但又如上所述那樣打開了警告選項，則會導致錯誤或警告（例如，諸如函數 “xxxxxx” 的隱含宣告之類的訊息）。

我為這些程式決定的解決方案是，包含一個預先處理器（preprocessor）#define 語句，它適當地定義了 \_XOPEN\_SOURCE。此解決方案的局限性在於，當使用 -D\_XOPEN\_SOURCE=600 編譯這些程式，並且原始檔案使用不同的值重新定義 \_XOPEN\_SOURCE 時，編譯器會發出有關重新定義的警告。

這兩個範例對於避免編譯器警告，示範了更完整的解決方案：

```c
#ifndef _XOPEN_SOURCE
#define _XOPEN_SOURCE
#endif

#if ! defined(_XOPEN_SOURCE) || _XOPEN_SOURCE < 500
#define _XOPEN_SOURCE 500
#endif
```

由於代碼緊湊的原因，此方法未在「書籍」版本的程式碼中採用，但在「distribution」版本中會採用。

也可以參考 TLPI 書籍的 3.6.1 節。

### 問題二：error: sys/acl.h: No such file or directory

如果您遇到錯誤 sys/acl.h: No such file or directory，那是因為您的系統上沒有安裝所需的 ACL套件。

在 Debian 或是 Ubuntu 系統，用如下的指令安裝應該會有用：

<pre class="language-bash"><code class="lang-bash"><strong>$ sudo apt-get install libacl1-dev
</strong></code></pre>

在使用 RPM 安裝套件的系統上，你會需要安裝 libacl-devel 套件。

### 問題三：error: sys/capability.h: No such file or directory

如果你看到錯誤訊息「error sys/capability.h: No such file or directory」

這是因為你的系統沒有安裝所需的套件。在 Debian 或 Ubuntu 系統，你可以用下面的指令安裝看看，應該會有用：

<pre class="language-bash"><code class="lang-bash"><strong>$ sudo apt-get install libcap-dev
</strong></code></pre>

在使用 RPM 安裝套件的系統上，你會需要安裝 libcap-devel 套件。

### 問題四：error: selinux/selinux.h: No such file or directory

如果你看到這個錯誤訊息「selinux/selinux.h: No such file or directory」，這是因為你的系統又有套件沒有安裝。在 Debian 或 Ubuntu 系統，你可以用下面的指令安裝看看，應該會有用：

<pre class="language-bash"><code class="lang-bash"><strong>$ sudo apt-get install libselinux1-dev
</strong></code></pre>

在使用 RPM 安裝套件的系統上，你會需要安裝 libselinux-devel 套件。

### 編譯單一個檔案遇到的錯誤與警告

#### lib/cap\_functions.c

如果你看到錯誤訊息「sys/capability.h: No such file or directory」，請參考問題三。

#### lib/userns\_functions.c

如果你看到錯誤訊息「sys/capability.h: No such file or directory」，請參考問題三。

#### acl/acl\_update.c

如果你看到錯誤訊息「sys/capability.h: No such file or directory」，請參考問題二。

#### acl/acl\_view.c

如果你看到錯誤訊息「sys/capability.h: No such file or directory」，請參考問題二。

#### cap/cap\_launcher.c

如果你看到錯誤訊息「sys/capability.h: No such file or directory」，請參考問題三。

#### cap/cap\_text.c

如果你看到錯誤訊息「sys/capability.h: No such file or directory」，請參考問題三。

#### cap/check\_password\_caps.c

有關編譯「書本」版本的程式碼時發生的「\_XOPEN\_SOURCE」重新定義警告的訊息，請參考問題一。

如果你看到錯誤訊息「sys/capability.h: No such file or directory」，請參考問題三。

#### cap/demo\_file\_caps.c

如果你看到錯誤訊息「sys/capability.h: No such file or directory」，請參考問題三。

#### cap/view\_cap\_xattr.c

如果你看到錯誤訊息「sys/capability.h: No such file or directory」，請參考問題三。

#### dirs\_links/list\_files\_readdir\_r.c

編譯此程式時，在使用 glibc 2.24 或更高版本編譯時，您可能會遇到警告 'readdir\_r' is deprecated \[-Wdeprecated-declarations]。 （[我如何得知系統上的 glibc 版本呢？](https://man7.org/tlpi/code/faq.html#glibc-version)）這是因為自 glibc 2.24 以來 readdir\_r() 已被棄用。有關詳細信息，請參閱[第 357 頁的勘誤表](http://man7.org/tlpi/errata/index.html#p\_357)。

#### filesys/t\_mount.c

編譯此程式的「book」版本時，若保留各種MS\_\*常數為未宣告，可能會發生錯誤。如果系統上的 GNU C 函式庫比2.12版舊時，就會發生此錯誤，[因為舊版的 glibc 並未定義某些必要的常數](http://sources.redhat.com/bugzilla/show\_bug.cgi?id=11235)。（[如何找出系統上有哪個版本的glibc？](https://man7.org/tlpi/code/faq.html#glibc-version)）

解決的方法是在使用較新的 glibc 版本的系統上編譯程式，或是編譯程式的「distribution」版本。

此程式的「distribution」版本不會發生問題，此版本包含的程式碼，可依據條件定義所需的常數（如果未在 glibc 表頭中定義)。請參考兩個程式版本的[差異](https://man7.org/tlpi/code/online/diff/filesys/t\_mount.c.html)。

#### pgsjc/catch\_SIGHUP.c

如需 "\_XOPEN\_SOURCE" 重新定義警告（編譯此程式的 "book" 版本時發生）的說明，請參閱[此處](https://man7.org/tlpi/code/faq.html#\_XOPEN\_SOURCE-redefined)。

#### pgsjc/t\_setsid.c

如需 "\_XOPEN\_SOURCE" 重新定義警告（編譯此程式的 "book" 版本時發生）的說明，請參閱[此處](https://man7.org/tlpi/code/faq.html#\_XOPEN\_SOURCE-redefined)。

#### proc/setjmp\_vars.c

變數 'rvar' 可能會被 'longjmp' 或 'vfork' 警告所阻擋，在編譯此程式時可預期出現該警告。GNU C 編譯器正是在準確診斷這個程式所示範的問題。請見這本書第137頁。

#### shlibs/dynload.c

如果您遇到函式內不允許的 #pragma GCC 診斷錯誤，這是因為您使用的是舊版的 GNU C 編譯器，它不支援使用 #pragma 語法，因而程式若使用了 #pragma，則會導致編譯器發出警告。

如果可以，請升級至 gcc 4.6（2011年3月）或更新的版本。否則，請編輯程式，移除所有以 #pragma 開頭的那行程式碼並重新編譯（此時您會看到先前隱藏的警告訊息）。

#### signals/nonatomic\_uint64.c.c

如果發生錯誤 bits/wordsize.h：No such file or directory or sys/cdefs.h: No such file or directory。您執行 Linux 套裝版本 （例如Ubuntu或Debian）可能是需要安裝 gcc-multilib 套件，才能建置此程式的。類似下列的安裝動作應該能正常運作：

<pre><code><strong>$ sudo apt-get install gcc-multilib
</strong></code></pre>

#### threads/thread\_multijoin.c

在 64 位元的系統上編譯時，您會看到有關指標與不同大小的整數之間的型別轉換警告。我應該在這個程式中使用 intptr_r 型別。但是，由於[第 649 頁勘誤表](https://man7.org/tlpi/errata/index.html#p\_649)末尾所談的原因，我選擇不對程式碼或文字進行任何的更改。

#### sockets/scm\_security\_send.c

如果發生錯誤 selinux/selinux.h: No such file or directory, 請參考問題四。

#### time/strtime.c

在編譯此程式的書籍版本時，若出現警告重複定義 "\_XOPEN\_SOURCE", 請參考問題一。

#### time/t\_stime.c

For an explanation of the "\_XOPEN\_SOURCE" redefined warning that occurs when compiling the "book" version of this program, look [here](https://man7.org/tlpi/code/faq.html#\_XOPEN\_SOURCE-redefined).

#### users\_groups/check\_password.c

For an explanation of the "\_XOPEN\_SOURCE" redefined warning that occurs when compiling the "book" version of this program, look [here](https://man7.org/tlpi/code/faq.html#\_XOPEN\_SOURCE-redefined).

### General questions

#### Why are there "book" and "distribution" versions of the example source code?

The example source code is provided in two versions. The reasons for this are explained [here](https://man7.org/tlpi/code/why\_diff.html).

#### Where is the file lib/ename.c.inc?

哈囉！感謝您的詢問。有些讀者指出，在 TLPI 的第 58 頁上顯示的 lib/ename.c.inc 檔案在從書籍網站下載的程式碼 tarball 中並不存在。這是因為當在 lib 目錄（或原始碼的根目錄）中執行 make(1) 時，此檔案會透過腳本自動建立。請參閱 lib/Makefile，其中呼叫 Build\_ename.sh。之所以採用這種方式，是因為錯誤名稱集可以因核心和 glibc 版本以及硬體架構而異。

#### What version of the Linux kernel do I have on my system?

To determine the version of the Linux kernel running on your system, use the following command:

<pre><code><strong>$ uname -sr
</strong>Linux 5.10.23-200.fc33.x86_64
</code></pre>

#### What version of _glibc_ do I have on my system?

On a sufficiently recent system, you can determine the version of _glibc_ running on your system using the following command:

<pre><code><strong>$ getconf GNU_LIBC_VERSION
</strong>glibc 2.26
</code></pre>

If that command doesn't work (perhaps because you have an older system), then you can obtain the _glibc_ version by executing the library shared object. On many systems, this is as simple as the following:

<pre><code><strong>$ /lib/libc.so.6
</strong></code></pre>

The first line of output of this command shows the _glibc_ version on your system.

However, on some systems, _glibc_ resides at a different path, so that the above command won't work. The following command obtains the pathname of the library by applying ldd to a dynamically linked executable at a known location, and then nests the resulting output inside command substitution to execute that pathname, producing the same results as the simpler command above.

<pre class="language-bash"><code class="lang-bash"><strong>$ ldd /bin/ls | grep libc.so | awk '{print $3}'
</strong>/lib/i386-linux-gnu/libc.so.6
<strong>$ $(ldd /bin/ls | grep libc.so | awk '{print $3}')
</strong>GNU C Library (GNU libc) stable release version 2.26, by Roland McGrath et al.
Copyright (C) 2017 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.
There is NO warranty; not even for MERCHANTABILITY or FITNESS FOR A
PARTICULAR PURPOSE.
Compiled by GNU CC version 7.3.1 20180303 (Red Hat 7.3.1-5).
Available extensions:
	crypt add-on version 2.1 by Michael Glad and others
	GNU Libidn by Simon Josefsson
	Native POSIX Threads Library by Ulrich Drepper et al
	BIND-8.2.3-T5B
libc ABIs: UNIQUE IFUNC
For bug reporting instructions, please see:
&#x3C;http://www.gnu.org/software/libc/bugs.html>
</code></pre>

#### Why do I get warnings "\_BSD\_SOURCE and \_SVID\_SOURCE are deprecated, use \_DEFAULT\_SOURCE"?

You are compiling the code on a system that has _glibc_ 2.20 or later installed. In _glibc_ 2.20, the \_BSD\_SOURCE and \_SVID\_SOURCE feature test macros were deprecated. They continue to expose the definitions that they exposed in earlier glibc versions, but their use produces the warning noted above. Instead, the \_DEFAULT\_SOURCE macro should be used. The simplest fix for this problem is to [download](https://man7.org/tlpi/code/index.html) the latest code tarball, which fixes the problem by modifying the Makefile.inc file to add -D\_DEFAULT\_SOURCE to the definition of the IMPL\_CFLAGS macro.

\




