title: linux——awk、sed、grep（鸟哥的私房菜）
categories: Linux##分类
tags: [Linux]##标签，多标签格式为 [tag1,tag2,...]
keywords: Linux##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 学习记录
date: 2016/08/19 14:24:25 
---
# grep
``` bash
[dmtsai@study ~]$ grep [-A] [-B] [--color=auto] '搜尋字串' filename
選項與參數：
-A ：後面可加數字，為 after 的意思，除了列出該行外，後續的 n 行也列出來；
-B ：後面可加數字，為 befer 的意思，除了列出該行外，前面的 n 行也列出來；
--color=auto 可將正確的那個擷取資料列出顏色
``` 

範例一：用 dmesg 列出核心訊息，再以 grep 找出內含 qxl 那行
``` bash
[dmtsai@study ~]$ dmesg | grep 'qxl'
[    0.522749] [drm] qxl: 16M of VRAM memory size
[    0.522750] [drm] qxl: 63M of IO pages memory ready (VRAM domain)
[    0.522750] [drm] qxl: 32M of Surface memory size
[    0.650714] fbcon: qxldrmfb (fb0) is primary device
[    0.668487] qxl 0000:00:02.0: fb0: qxldrmfb frame buffer device
# dmesg 可列出核心產生的訊息！包括硬體偵測的流程也會顯示出來。
# 鳥哥使用的顯卡是 QXL 這個虛擬卡，透過 grep 來 qxl 的相關資訊，可發現如上資訊。
``` 

範例二：承上題，要將捉到的關鍵字顯色，且加上行號來表示：
``` bash
[dmtsai@study ~]$ dmesg | grep -n --color=auto 'qxl'
515:[    0.522749] [drm] qxl: 16M of VRAM memory size
516:[    0.522750] [drm] qxl: 63M of IO pages memory ready (VRAM domain)
517:[    0.522750] [drm] qxl: 32M of Surface memory size
529:[    0.650714] fbcon: qxldrmfb (fb0) is primary device
539:[    0.668487] qxl 0000:00:02.0: fb0: qxldrmfb frame buffer device
# 除了 qxl 會有特殊顏色來表示之外，最前面還有行號喔！其實顏色顯示已經是預設在 alias 當中了！
``` 

範例三：承上題，在關鍵字所在行的前兩行與後三行也一起捉出來顯示
``` bash
[dmtsai@study ~]$ dmesg | grep -n -A3 -B2 --color=auto 'qxl'
# 你會發現關鍵字之前與之後的數行也被顯示出來！這樣可以讓你將關鍵字前後資料捉出來進行分析啦！
grep 是一個很常見也很常用的指令，他最重要的功能就是進行字串資料的比對，然後將符合使用者需求的字串列印出來。 需要說明的是『grep 在資料中查尋一個字串時，是以 "整行" 為單位來進行資料的擷取的！』也就是說，假如一個檔案內有 10 行，其中有兩行具有你所搜尋的字串，則將那兩行顯示在螢幕上，其他的就丟棄了！
``` 
在 CentOS 7 當中，預設已經將 --color=auto 加入在 alias 當中了！使用者就可以直接使用有關鍵字顯色的 grep 囉！非常方便！

# sed

在瞭解了一些正規表示法的基礎應用之後，再來呢？呵呵～兩個東西可以玩一玩的，那就是 sed 跟底下會介紹的 awk 了！ 這兩個傢伙可是相當的有用的啊！舉例來說，鳥哥寫的 logfile.sh 分析登錄檔的小程式 (第十八章會談到)，絕大部分分析關鍵字的取用、統計等等，就是用這兩個寶貝蛋來幫我完成的！那麼你說，要不要玩一玩啊？^_^

我們先來談一談 sed 好了， sed 本身也是一個管線命令，可以分析 standard input 的啦！ 而且 sed 還可以將資料進行取代、刪除、新增、擷取特定行等等的功能呢！很不錯吧～ 我們先來瞭解一下 sed 的用法，再來聊他的用途好了！

``` bash
[dmtsai@study ~]$ sed [-nefr] [動作]
選項與參數：
-n  ：使用安靜(silent)模式。在一般 sed 的用法中，所有來自 STDIN 的資料一般都會被列出到螢幕上。
      但如果加上 -n 參數後，則只有經過 sed 特殊處理的那一行(或者動作)才會被列出來。
-e  ：直接在指令列模式上進行 sed 的動作編輯；
-f  ：直接將 sed 的動作寫在一個檔案內， -f filename 則可以執行 filename 內的 sed 動作；
-r  ：sed 的動作支援的是延伸型正規表示法的語法。(預設是基礎正規表示法語法)
-i  ：直接修改讀取的檔案內容，而不是由螢幕輸出。

動作說明：  [n1[,n2]]function
n1, n2 ：不見得會存在，一般代表『選擇進行動作的行數』，舉例來說，如果我的動作
         是需要在 10 到 20 行之間進行的，則『 10,20[動作行為] 』

function 有底下這些咚咚：
a   ：新增， a 的後面可以接字串，而這些字串會在新的一行出現(目前的下一行)～
c   ：取代， c 的後面可以接字串，這些字串可以取代 n1,n2 之間的行！
d   ：刪除，因為是刪除啊，所以 d 後面通常不接任何咚咚；
i   ：插入， i 的後面可以接字串，而這些字串會在新的一行出現(目前的上一行)；
p   ：列印，亦即將某個選擇的資料印出。通常 p 會與參數 sed -n 一起運作～
s   ：取代，可以直接進行取代的工作哩！通常這個 s 的動作可以搭配正規表示法！
      例如 1,20s/old/new/g 就是啦！
以行為單位的新增/刪除功能
``` 
sed 光是用看的是看不懂的啦！所以又要來練習了！先來玩玩刪除與新增的功能吧！

範例一：將 /etc/passwd 的內容列出並且列印行號，同時，請將第 2~5 行刪除！
``` bash
[dmtsai@study ~]$ nl /etc/passwd | sed '2,5d'
     1  root:x:0:0:root:/root:/bin/bash
     6  sync:x:5:0:sync:/sbin:/bin/sync
     7  shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
.....(後面省略).....
``` 
看到了吧？sed 的動作為 '2,5d' ，那個 d 就是刪除！因為 2-5 行給他刪除了，所以顯示的資料就沒有 2-5 行囉～ 另外，注意一下，原本應該是要下達 sed -e 才對，沒有 -e 也行啦！同時也要注意的是， sed 後面接的動作，請務必以 '' 兩個單引號括住喔！

如果題型變化一下，舉例來說，如果只要刪除第 2 行，可以使用『 nl /etc/passwd | sed '2d' 』來達成， 至於若是要刪除第 3 到最後一行，則是『 nl /etc/passwd | sed '3,$d' 』的啦，那個錢字號『 $ 』代表最後一行！

範例二：承上題，在第二行後(亦即是加在第三行)加上『drink tea?』字樣！
``` bash
[dmtsai@study ~]$ nl /etc/passwd | sed '2a drink tea'
     1  root:x:0:0:root:/root:/bin/bash
     2  bin:x:1:1:bin:/bin:/sbin/nologin
drink tea
     3  daemon:x:2:2:daemon:/sbin:/sbin/nologin
.....(後面省略).....
``` 
嘿嘿！在 a 後面加上的字串就已將出現在第二行後面囉！那如果是要在第二行前呢？『 nl /etc/passwd | sed '2i drink tea' 』就對啦！就是將『 a 』變成『 i 』即可。 增加一行很簡單，那如果是要增將兩行以上呢？

範例三：在第二行後面加入兩行字，例如『Drink tea or .....』與『drink beer?』
``` bash
[dmtsai@study ~]$ nl /etc/passwd | sed '2a Drink tea or ......\
> drink beer ?'
     1  root:x:0:0:root:/root:/bin/bash
     2  bin:x:1:1:bin:/bin:/sbin/nologin
Drink tea or ......
drink beer ?
     3  daemon:x:2:2:daemon:/sbin:/sbin/nologin
.....(後面省略).....
``` 
這個範例的重點是『我們可以新增不只一行喔！可以新增好幾行』但是每一行之間都必須要以反斜線『 \ 』來進行新行的增加喔！所以，上面的例子中，我們可以發現在第一行的最後面就有 \ 存在啦！在多行新增的情況下， \ 是一定要的喔！

以行為單位的取代與顯示功能
剛剛是介紹如何新增與刪除，那麼如果要整行取代呢？看看底下的範例吧：

範例四：我想將第2-5行的內容取代成為『No 2-5 number』呢？
``` bash
[dmtsai@study ~]$ nl /etc/passwd | sed '2,5c No 2-5 number'
     1  root:x:0:0:root:/root:/bin/bash
No 2-5 number
     6  sync:x:5:0:sync:/sbin:/bin/sync
.....(後面省略).....
``` 
透過這個方法我們就能夠將資料整行取代了！非常容易吧！sed 還有更好用的東東！我們以前想要列出第 11~20 行， 得要透過『head -n 20 | tail -n 10』之類的方法來處理，很麻煩啦～ sed 則可以簡單的直接取出你想要的那幾行！是透過行號來捉的喔！看看底下的範例先：

範例五：僅列出 /etc/passwd 檔案內的第 5-7 行
``` bash
[dmtsai@study ~]$ nl /etc/passwd | sed -n '5,7p'
     5  lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
     6  sync:x:5:0:sync:/sbin:/bin/sync
     7  shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
``` 
上述的指令中有個重要的選項『 -n 』，按照說明文件，這個 -n 代表的是『安靜模式』！ 那麼為什麼要使用安靜模式呢？你可以自行下達 sed '5,7p' 就知道了 (5-7 行會重複輸出)！ 有沒有加上 -n 的參數時，輸出的資料可是差很多的喔！你可以透過這個 sed 的以行為單位的顯示功能， 就能夠將某一個檔案內的某些行號捉出來查閱！很棒的功能！不是嗎？

部分資料的搜尋並取代的功能
除了整行的處理模式之外， sed 還可以用行為單位進行部分資料的搜尋並取代的功能喔！ 基本上 sed 的搜尋與取代的與 vi 相當的類似！他有點像這樣：

sed 's/要被取代的字串/新的字串/g'
上表中特殊字體的部分為關鍵字，請記下來！至於三個斜線分成兩欄就是新舊字串的替換啦！ 我們使用底下這個取得 IP 數據的範例，一段一段的來處理給您瞧瞧，讓你瞭解一下什麼是咱們所謂的搜尋並取代吧！

步驟一：先觀察原始訊息，利用 /sbin/ifconfig  查詢 IP 為何？
``` bash
[dmtsai@study ~]$ /sbin/ifconfig eth0
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.100  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fe80::5054:ff:fedf:e174  prefixlen 64  scopeid 0x20<link>
        ether 52:54:00:df:e1:74  txqueuelen 1000  (Ethernet)
.....(以下省略).....
``` 
*因為我們還沒有講到 IP ，這裡你先有個概念即可啊！我們的重點在第二行，*
*也就是 192.168.1.100 那一行而已！先利用關鍵字捉出那一行！*

步驟二：利用關鍵字配合 grep 擷取出關鍵的一行資料
``` bash
[dmtsai@study ~]$ /sbin/ifconfig eth0 | grep 'inet '
        inet 192.168.1.100  netmask 255.255.255.0  broadcast 192.168.1.255
``` 
*當場僅剩下一行！要注意， CentOS 7 與 CentOS 6 以前的 ifconfig 指令輸出結果不太相同，*
*鳥哥這個範例主要是針對 CentOS 7 以後的喔！接下來，我們要將開始到 addr: 通通刪除，*
*就是像底下這樣：*
*inet 192.168.1.100  netmask 255.255.255.0  broadcast 192.168.1.255*
*上面的刪除關鍵在於『 ^.*inet  』啦！正規表示法出現！ ^_^*

步驟三：將 IP 前面的部分予以刪除
``` bash
[dmtsai@study ~]$ /sbin/ifconfig eth0 | grep 'inet ' | sed 's/^.*inet //g'
192.168.1.100  netmask 255.255.255.0  broadcast 192.168.1.255
# 仔細與上個步驟比較一下，前面的部分不見了！接下來則是刪除後續的部分，亦即：
192.168.1.100  netmask 255.255.255.0  broadcast 192.168.1.255
# 此時所需的正規表示法為：『 ' *netmask.*$ 』就是啦！
``` 

步驟四：將 IP 後面的部分予以刪除
``` bash
[dmtsai@study ~]$ /sbin/ifconfig eth0 | grep 'inet ' | sed 's/^.*inet //g' \
>   | sed 's/ *netmask.*$//g'
192.168.1.100
``` 
透過這個範例的練習也建議您依據此一步驟來研究你的指令！就是先觀察，然後再一層一層的試做， 如果有做不對的地方，就先予以修改，改完之後測試，成功後再往下繼續測試。以鳥哥上面的介紹中， 那一大串指令就做了四個步驟！對吧！ ^_^

讓我們再來繼續研究 sed 與正規表示法的配合練習！假設我只要 MAN 存在的那幾行資料， 但是含有 # 在內的註解我不想要，而且空白行我也不要！此時該如何處理呢？可以透過這幾個步驟來實作看看：

步驟一：先使用 grep 將關鍵字 MAN 所在行取出來
``` bash
[dmtsai@study ~]$ cat /etc/man_db.conf | grep 'MAN'
# MANDATORY_MANPATH                     manpath_element
# MANPATH_MAP           path_element    manpath_element
# MANDB_MAP             global_manpath  [relative_catpath]
# every automatically generated MANPATH includes these fields
....(後面省略)....
``` 

步驟二：刪除掉註解之後的資料！
``` bash
[dmtsai@study ~]$ cat /etc/man_db.conf | grep 'MAN'| sed 's/#.*$//g'


MANDATORY_MANPATH                       /usr/man
....(後面省略)....
# 從上面可以看出來，原本註解的資料都變成空白行啦！所以，接下來要刪除掉空白行

[dmtsai@study ~]$ cat /etc/man_db.conf | grep 'MAN'| sed 's/#.*$//g' | sed '/^$/d'
MANDATORY_MANPATH                       /usr/man
MANDATORY_MANPATH                       /usr/share/man
MANDATORY_MANPATH                       /usr/local/share/man
....(後面省略)....
``` 

直接修改檔案內容(危險動作)
你以為 sed 只有這樣的能耐嗎？那可不！ sed 甚至可以直接修改檔案的內容呢！而不必使用管線命令或資料流重導向！ 不過，由於這個動作會直接修改到原始的檔案，所以請你千萬不要隨便拿系統設定檔來測試喔！ 我們還是使用你下載的 regular_express.txt 檔案來測試看看吧！

範例六：利用 sed 將 regular_express.txt 內每一行結尾若為 . 則換成 !
``` bash
[dmtsai@study ~]$ sed -i 's/\.$/\!/g' regular_express.txt
# 上頭的 -i 選項可以讓你的 sed 直接去修改後面接的檔案內容而不是由螢幕輸出喔！
# 這個範例是用在取代！請您自行 cat 該檔案去查閱結果囉！
``` 

範例七：利用 sed 直接在 regular_express.txt 最後一行加入『# This is a test』
``` bash
[dmtsai@study ~]$ sed -i '$a # This is a test' regular_express.txt
# 由於 $ 代表的是最後一行，而 a 的動作是新增，因此該檔案最後新增囉！
``` 
sed 的『 -i 』選項可以直接修改檔案內容，這功能非常有幫助！舉例來說，如果你有一個 100 萬行的檔案，你要在第 100 行加某些文字，此時使用 vim 可能會瘋掉！因為檔案太大了！那怎辦？就利用 sed 啊！透過 sed 直接修改/取代的功能，你甚至不需要使用 vim 去修訂！很棒吧！

總之，這個 sed 不錯用啦！而且很多的 shell script 都會使用到這個指令的功能～ sed 可以幫助系統管理員管理好日常的工作喔！要仔細的學習呢！


# awk

awk 也是一個非常棒的資料處理工具！相較於 sed 常常作用於一整個行的處理， awk 則比較傾向於一行當中分成數個『欄位』來處理。因此，awk 
相當的適合處理小型的數據資料處理呢！awk 通常運作的模式是這樣的：

`[dmtsai@study ~]$ awk '條件類型1{動作1} 條件類型2{動作2} ...' filename`
awk 後面接兩個單引號並加上大括號 {} 來設定想要對資料進行的處理動作。 awk 可以處理後續接的檔案，也可以讀取來自前個指令的 standard output 。 但如前面說的， awk 主要是處理『每一行的欄位內的資料』，而預設的『欄位的分隔符號為 "空白鍵" 或 "[tab]鍵" 』！舉例來說，我們用 last 可以將登入者的資料取出來，結果如下所示：

``` bash
[dmtsai@study ~]$ last -n 5 <==僅取出前五行
dmtsai   pts/0     192.168.1.100   Tue Jul 14 17:32   still logged in
dmtsai   pts/0     192.168.1.100   Thu Jul  9 23:36 - 02:58  (03:22)
dmtsai   pts/0     192.168.1.100   Thu Jul  9 17:23 - 23:36  (06:12)
dmtsai   pts/0     192.168.1.100   Thu Jul  9 08:02 - 08:17  (00:14)
dmtsai   tty1                      Fri May 29 11:55 - 12:11  (00:15)
``` 
若我想要取出帳號與登入者的 IP ，且帳號與 IP 之間以 [tab] 隔開，則會變成這樣：
``` bash
[dmtsai@study ~]$ last -n 5 | awk '{print $1 "\t" $3}'
dmtsai  192.168.1.100
dmtsai  192.168.1.100
dmtsai  192.168.1.100
dmtsai  192.168.1.100
dmtsai  Fri
``` 
上表是 awk 最常使用的動作！透過 print 的功能將欄位資料列出來！欄位的分隔則以空白鍵或 [tab] 按鍵來隔開。 因為不論哪一行我都要處理，因此，就不需要有 "條件類型" 的限制！我所想要的是第一欄以及第三欄， 但是，第五行的內容怪怪的～這是因為資料格式的問題啊！所以囉～使用 awk 的時候，請先確認一下你的資料當中，如果是連續性的資料，請不要有空格或 [tab] 在內，否則，就會像這個例子這樣，會發生誤判喔！

另外，由上面這個例子你也會知道，在 awk 的括號內，每一行的每個欄位都是有變數名稱的，那就是 $1, $2... 等變數名稱。以上面的例子來說， dmtsai 是 $1 ，因為他是第一欄嘛！至於 192.168.1.100 是第三欄， 所以他就是 $3 啦！後面以此類推～呵呵！還有個變數喔！那就是 $0 ，$0 代表『一整列資料』的意思～以上面的例子來說，第一行的 $0 代表的就是『dmtsai .... 』那一行啊！ 由此可知，剛剛上面五行當中，整個 awk 的處理流程是：

讀入第一行，並將第一行的資料填入 $0, $1, $2.... 等變數當中；
依據 "條件類型" 的限制，判斷是否需要進行後面的 "動作"；
做完所有的動作與條件類型；
若還有後續的『行』的資料，則重複上面 1~3 的步驟，直到所有的資料都讀完為止。
經過這樣的步驟，你會曉得， awk 是『以行為一次處理的單位』， 而『以欄位為最小的處理單位』。好了，那麼 awk 怎麼知道我到底這個資料有幾行？有幾欄呢？這就需要 awk 的內建變數的幫忙啦～

變數名稱	代表意義

> NF	每一行 ($0) 擁有的欄位總數
> NR	目前 awk 所處理的是『第幾行』資料
> FS	目前的分隔字元，預設是空白鍵


我們繼續以上面 `last -n 5` 的例子來做說明，如果我想要：

列出每一行的帳號(就是 $1)；
列出目前處理的行數(就是 awk 內的 NR 變數)
並且說明，該行有多少欄位(就是 awk 內的 NF 變數)
則可以這樣：

Tips
鳥哥的圖示 要注意喔，awk 後續的所有動作是以單引號『 ' 』括住的，由於單引號與雙引號都必須是成對的， 所以， awk 的格式內容如果想要以 print 列印時，記得非變數的文字部分，包含上一小節 printf 提到的格式中，都需要使用雙引號來定義出來喔！因為單引號已經是 awk 的指令固定用法了！

``` bash
[dmtsai@study ~]$ last -n 5| awk '{print $1 "\t lines: " NR "\t columns: " NF}'
dmtsai   lines: 1        columns: 10
dmtsai   lines: 2        columns: 10
dmtsai   lines: 3        columns: 10
dmtsai   lines: 4        columns: 10
dmtsai   lines: 5        columns: 9
``` 
*注意喔，在 awk 內的 NR, NF 等變數要用大寫，且不需要有錢字號 $ 啦！*
這樣可以瞭解 NR 與 NF 的差別了吧？好了，底下來談一談所謂的 "條件類型" 了吧！

awk 的邏輯運算字元
既然有需要用到 "條件" 的類別，自然就需要一些邏輯運算囉～例如底下這些：

運算單元	代表意義
> >	大於
> <	小於
> >=	大於或等於
> <=	小於或等於
> ==	等於
> !=	不等於

值得注意的是那個『 == 』的符號，因為：

邏輯運算上面亦即所謂的大於、小於、等於等判斷式上面，習慣上是以『 == 』來表示；
如果是直接給予一個值，例如變數設定時，就直接使用 = 而已。
好了，我們實際來運用一下邏輯判斷吧！舉例來說，在 /etc/passwd 當中是以冒號 ":" 來作為欄位的分隔， 該檔案中第一欄位為帳號，第三欄位則是 UID。那假設我要查閱，第三欄小於 10 以下的數據，並且僅列出帳號與第三欄， 那麼可以這樣做：

``` bash
[dmtsai@study ~]$ cat /etc/passwd | awk '{FS=":"} $3 < 10 {print $1 "\t " $3}'
root:x:0:0:root:/root:/bin/bash
bin      1
daemon   2
....(以下省略)....
``` 
有趣吧！不過，怎麼第一行沒有正確的顯示出來呢？這是因為我們讀入第一行的時候，那些變數 $1, $2... 預設還是以空白鍵為分隔的，所以雖然我們定義了 FS=":" 了， 但是卻僅能在第二行後才開始生效。那麼怎麼辦呢？我們可以預先設定 awk 的變數啊！ 利用 BEGIN 這個關鍵字喔！這樣做：

``` bash
[dmtsai@study ~]$ cat /etc/passwd | awk 'BEGIN {FS=":"} $3 < 10 {print $1 "\t " $3}'
root     0
bin      1
daemon   2
......(以下省略)......
``` 
很有趣吧！而除了 BEGIN 之外，我們還有 END 呢！另外，如果要用 awk 來進行『計算功能』呢？以底下的例子來看， 假設我有一個薪資資料表檔名為 pay.txt ，內容是這樣的：

``` bash
Name    1st     2nd     3th
VBird   23000   24000   25000
DMTsai  21000   20000   23000
Bird2   43000   42000   41000
``` 
如何幫我計算每個人的總額呢？而且我還想要格式化輸出喔！我們可以這樣考慮：

第一行只是說明，所以第一行不要進行加總 (NR==1 時處理)；
第二行以後就會有加總的情況出現 (NR>=2 以後處理)
``` bash
[dmtsai@study ~]$ cat pay.txt | \
> awk 'NR==1{printf "%10s %10s %10s %10s %10s\n",$1,$2,$3,$4,"Total" }
> NR>=2{total = $2 + $3 + $4
> printf "%10s %10d %10d %10d %10.2f\n", $1, $2, $3, $4, total}'
      Name        1st        2nd        3th      Total
     VBird      23000      24000      25000   72000.00
    DMTsai      21000      20000      23000   64000.00
     Bird2      43000      42000      41000  126000.00
``` 
上面的例子有幾個重要事項應該要先說明的：

awk 的指令間隔：所有 awk 的動作，亦即在 {} 內的動作，如果有需要多個指令輔助時，可利用分號『;』間隔， 或者直接以 [Enter] 按鍵來隔開每個指令，例如上面的範例中，鳥哥共按了三次 [enter] 喔！
邏輯運算當中，如果是『等於』的情況，則務必使用兩個等號『==』！
格式化輸出時，在 printf 的格式設定當中，務必加上 \n ，才能進行分行！
與 bash shell 的變數不同，在 awk 當中，變數可以直接使用，不需加上 $ 符號。
利用 awk 這個玩意兒，就可以幫我們處理很多日常工作了呢！真是好用的很～ 此外， awk 的輸出格式當中，常常會以 printf 來輔助，所以， 最好你對 printf 也稍微熟悉一下比較好啦！另外， awk 的動作內 {} 也是支援 if (條件) 的喔！ 舉例來說，上面的指令可以修訂成為這樣：

``` bash
[dmtsai@study ~]$ cat pay.txt | \
> awk '{if(NR==1) printf "%10s %10s %10s %10s %10s\n",$1,$2,$3,$4,"Total"}
> NR>=2{total = $2 + $3 + $4
> printf "%10s %10d %10d %10d %10.2f\n", $1, $2, $3, $4, total}'
``` 
你可以仔細的比對一下上面兩個輸入有啥不同～從中去瞭解兩種語法吧！我個人是比較傾向於使用第一種語法， 因為會比較有統一性啊！ ^_^

除此之外， awk 還可以幫我們進行迴圈計算喔！真是相當的好用！不過，那屬於比較進階的單獨課程了， 我們這裡就不再多加介紹。如果你有興趣的話，請務必參考延伸閱讀中的相關連結喔

# 链接
鸟哥的私房菜：[第十一章、正規表示法與文件格式化處理](http://linux.vbird.org/linux_basic/0330regularex.php#top)