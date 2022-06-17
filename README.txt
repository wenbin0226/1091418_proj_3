首先宣告兩個class
一個是RSdata
一個是inst
RSdata用來記錄在RS裡的資料
inst則是記錄input的資料

RSdata:
op代表operator (+-*/)
rs1和rs2是兩個register
used代表說這個東西在RS裡而且是有東西的
able代表可以dispatch (避免同個cycle裡面issue又dispatch)
cycleCount用來記錄在算數unit裡面待多久
index代表這個data在RS的哪個位置 (RS1、RS2之類)
rd代表寫入的register
ans是計算結果

inst:
四個string
op代表operator (ADD、ADDI、SUB....)
rd, rs1, rs2代表register的來源和寫入的位置



一開始先input
讓使用者決定各種運算所需的cycle

接下來init
將register初始化並且給RS空間

再來是read
將input裡的instruction讀進來並且用stringstream切割
將各個operands放到對應的資料結構裡面
最後再加進insts (這是所有的instruction在的位置)

最後就是exec
exec主要分四部分然後用while(true)包起來

issue:
這裡會判斷n是否大於insts的size (n代表跑到第幾行)
再來判斷第n行的instruction是甚麼
如果是ADD、ADDI、SUB就看RS!count是否小於3
MUL或DIV的話則是是否小於2
小於的話代表還有位置可以放
那麼就找一個空的 (沒有被used) 來存RSdata
首先先看RAT裡面這個register需不需要等別的instruction
要的話就把RAT裡存的RS1之類的東西放進去
否則就直接去RF裡面找資料
最後會把RScount、n、busycount (這是用來記錄有幾個RS正在被使用，終止程式所需要的)都加一
change = 1代表有修改東西

dispatch:
這裡會先看三個unit是否在忙碌
如果在忙的話就不吵他們把cycleCount++然後結束
如果有空檔的話就進RS裡面找哪一個instruction已經準備好了
而且是able且used
也就是值都已經有了不需要等別的instruction
把他放進unit裡面
同時change = 1代表有改東西

writeback:
這裡會把這個cycle才issue的instruction都設成able (代表下個cycle可以dispatch)
然後就是看unit們有沒有東西
有的話就看一下cycleCount到了沒
如果到了就把值算出來然後寫進RF並且broadcast回RS
最後把對應的RAT裡的東西刪掉
然後change = 1代表有修改

output:
當change == 1的時候就會進這個function
然後將cycle、RF、RS、RAT、buffer都印出來 (buffer代表unit裡正在執行的instruction)

while迴圈的最後將cycle++。