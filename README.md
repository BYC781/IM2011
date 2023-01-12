# 108-2 IM2011 Group8 Final Project
## Remaking LF2

### 組長:

B06303007 經濟三 徐浩軒

### 組員:

B06303111 經濟三 趙彥棆

B06303050 經濟三 陳柏語

B06303012 經濟三 簡丞劭

# 主題

### 復刻小朋友齊打交

我們的小朋友齊打交是一款完全以鍵盤操作的遊戲，首先進入遊戲主介面，會看到三個按鈕，分別是開始遊戲的start game、解釋遊戲方式的instruction跟關閉程式的exit。

玩家總共使用7個按鍵做遊玩，包含防、跳、攻三個動作鍵，以及上下左右四個移動鍵，並使用這七個按鍵做組合做出普攻、跳攻、跑攻與滾動等動作。遊戲設定是由雙人遊玩，玩家各使用半邊的鍵盤，先將對方血條打完即獲勝。

# 系統設計及演算法

## 檔案讀取

讀取了所有需要的圖片以及音訊。

## 建立class

### __init__(self)

在程式碼第196~249行，我們初始了角色的座標、長寬、面朝方向、操控鍵、跑步按鍵、移動速度、移動狀態、動作狀態、被打的狀態、各個更新動畫的計數器、生命值。

1. 座標及長寬：self.x, self.y, self.width, self.height
2. 操控鍵：
(1)上下左右：self.up, self.down, self.left, self.right
(2)攻跳防：self.Akey, self.Jkey, self.Dkey
3. 跑步按鍵：self.RunL, self.RunR
4. 面朝方向：self.facing
5. 移動速度：self.vel
6. 移動狀態：iself.sWalk, self.sRun
7. 動作狀態：self.hit, self.jump, self.defense , self.Stop, self.Flip, self.liedown, self.dead
8. 被打的狀態：self.beHit1, self.beHit2, self.beHit3, self.beHit4
9. 更新動畫的計數器：self.walkCount, rself.unCount, self.jumpCount, self.runjumpCount, self.hitCount, self.defCount, rself.unhitCount, self.stopCount, self.flipCount, self.beHitCount1, self.beHitCount2, self.beHitCount3, self.knockedCount
10. 生命值：self.health

### draw(self)

在這個method當中，我們決定了在什麼狀態下要更新哪一組圖片，這都是由一堆布林值所組成的，比方說：

1. 向右站立：
self.facing, self.isRun, self.isWalk, self.hit, self.jump, self.defense = 1, 0, 0, 0, 0, 0
2. 向右站立攻擊：
self.facing, self.isRun, self.isWalk, self.hit, self.jump, self.defense = 1, 0, 0, 1, 0, 0
3. 向右站立防禦：
self.facing, self.isRun, self.isWalk, self.hit, self.jump, self.defense = 1, 0, 0, 0, 0, 1
4. 向右站立跳躍：
self.facing, self.isRun, self.isWalk, self.hit, self.jump, self.defense = 1, 0, 0, 0, 1, 0
5. 向右站立跳攻：
self.facing, self.isRun, self.isWalk, self.hit, self.jump, self.defense = 1, 0, 0, 1, 1, 0
6. 向右走路：
self.facing, self.isRun, self.isWalk, self.hit, self.jump, self.defense = 1, 0, 1, 0, 0, 0
7. 向右走跳：
self.facing, self.isRun, self.isWalk, self.hit, self.jump, self.defense = 1, 0, 1, 0, 1, 0
8. 向右走跳攻：
self.facing, self.isRun, self.isWalk, self.hit, self.jump, self.defense = 1, 0, 1, 1, 1, 0
9. 向右跑：
self.facing, self.isRun, self.isWalk, self.hit, self.jump, self.defense = 1, 1, 0, 0, 0, 0
10. 向右跑攻：
self.facing, self.isRun, self.isWalk, self.hit, self.jump, self.defense = 1, 1, 0, 1, 0, 0
11. 向右跳：
self.facing, self.isRun, self.isWalk, self.hit, self.jump, self.defense = 1, 0, 0, 0, 1, 0
12. 向右跳攻：
self.facing, self.isRun, self.isWalk, self.hit, self.jump, self.defense = 1, 1, 0, 1, 1, 0
13. 向右滾：
self.facing, self.isRun, self.isWalk, self.hit, self.jump, self.defense = 1, 1, 0, 0, 0, 0

而這些動作都是基於沒有被打的狀態以及沒有倒地的狀態以及沒有死掉的狀態，也就是在self.beHit1, self.beHit2, self.beHit3, self.beHit4, self.liedown, self.dead = 0, 0, 0, 0, 0, 0的情況。若為面向左邊，則把self.facing改為0。

### walk(self, keys)

這個method控制了走路狀態下的所有動作的開關，像是普通的走、走跳、走跳攻、攻擊、防禦等等：

1. 走：按下上下左右鍵，角色的座標會改變，像是428行self.x -= self.vel。
2. 走跳：在走的狀態下按跳躍鍵，self.jump = 1。
3. 走跳攻：在走跳的狀態下按攻擊鍵，self.hit = 1。
4. 攻擊：比較特別的是，在走路的狀態下，是沒辦法一邊走一邊攻擊的，所以按下攻擊鍵時，會停止走路並站立攻擊，self.isWalk, self.hit = 0, 1。
5. 防禦：同上，按下防禦鍵時，會停止走路並站立防禦，self.isWalk, self.defense = 0, 1。

### run(self, keys)

這個method控制了跑步狀態下的所有動作的開關，像普通的跑、跑跳、跑跳攻、跑攻、滾動等等：

1. 跑：在192行的ACCEPTABLE_DELAY = 7以內，也就是7幀的時間差以內，連續按下兩下左鍵或是兩下右鍵，且非處於被打、倒下或死亡的狀況下，self.isRun = 1。此時角色的x座標會以2倍vel的速度持續前進直至邊界，而按上下鍵時y座標則與走路時的座標改變相同。
2. 跑跳：在跑的狀態下按跳躍鍵，self.jump = 1。
3. 跑跳攻：在跑跳的狀態下按攻擊鍵，self.hit = 1。
4. 跑攻：在跑的狀態下按下攻擊鍵，self.hit = 0, 1。
5. 滾動：在跑的狀態按下防禦鍵，self.defense = 0, 1。

### action(self)

這個method主要在處理開關打開以後的事，當我們打開某個動作的開關之後，需要讓它做某些事，做完之後再把開關關起來，像是：

1. 站立攻擊：動作總共有15幀，我們用self.hitCount讓他從0一直加到15，到15的時候self.hitCount下一幀會歸零，並使self.hit = 0。
2. 站立防禦：動作總共有15幀，我們用self.defCount讓他從0一直加到15，到15的時候self.defCount下一幀會歸零，並使self.defense = 0。
3. 站立跳躍：動作總共有17幀，我們self.jumpCount使他從8一直減到-8，當self.jumpCount介於8和1之間時neg = 1，此時透過549行的self.y -= (self.jumpCount ** 2) * 0.5 * neg 使角色做出往上的垂直位移，當self.jumpCount-1和-8時之間時neg = -1，使角色做出往下的垂直位移。當self.jumpCount到-8的時候下一幀會變成8，並使self.jump = 0。
4. 站立跳攻：與站立跳躍幾乎無不同，唯一不同的地方是self.jumpCount變回8的時候會額外使self.hit = 0
5. 走跳：與站立跳躍機制相同，且可以邊走邊跳。
6. 走跳攻：與站立跳攻機制相同，且可以邊走邊跳。
7. 跑跳：動作總共有15幀，垂直位移方式與站立跳躍相同，只不過因為self.runjumpCount只會數15個數字，所以會跳得比較矮也比較快，動作結束時self.jump, self.isRun = 0, 0。
8. 跑跳攻：與站立跳躍幾乎無不同，唯一不同的地方是self.jumpCount變回7的時候會額外使self.hit = 0
9. 跑攻：動作總共有15幀，我們用self.runhitCount讓他從0一直加到15，這段期間之間self.x的每幀位移量會減少0.5 * self.vel，self.runhitCount到15的時候下一幀會歸零，並使self.hit, self.isRun = 0, 0。
10. 煞車：動作總共有15幀，我們用self.stopCount讓他從0一直加到15，這段期間之間self.x的每幀位移量會減少0.5 * self.vel，self.runhitCount到15的時候下一幀會歸零，並使self.Stop, self.isRun = 0, 0。
11. 滾：動作總共有12幀，我們用self.flipCount讓他從0一直加到12，，self.runhitCount到12的時候下一幀會歸零，並使self.Stop, self.isRun = 0, 0。

### BHC

此method用來串連4個beHit及liedown(倒下)的狀態，同時用以計算被攻擊時的動畫，並且定義被擊中時所扣除的血量。

1.在self.beHit1的狀態下，self.beHitCount1每幀會加1，到達60時若無進入beHit2會歸零並回歸站立

   狀態，此時關閉self.beHit1。

2.self.beHit2與self.beHit3的作法同上。

3.self.beHit4的狀態則比較特別，是以self.knockedCount來計算，以與跳躍相同的方式做出角色y座       

  標上的移動，落地後必然接到self.liedown。

4.self.liedown的狀態以lieCount計算，若非死亡狀態在12幀後會起生，反之則維持倒下狀態。

5.beHit1至beHit3都是扣0.5，beHit4則是扣1。

### Dead

此method定義角色的生命小於等於0時，self.dead = 1。

### sound

此method在角色被攻擊時產生音效。

## 主程式

主程式除了控制遊戲視窗的開關、設定FPS之外，還用來判定一些KEYDOWN、KEYUP的情形和執行Player中的各種函數，其他最主要是用來放一些Class與Class之間的交互作用，像是碰撞機制的判定我們需要去討論Firen(Player 1)在攻擊的時候有沒有碰到Freeze(Player 2) 的邊界且Freeze不是處於倒下的狀態（not Freeze.liedown），如果有的話我們就說Freeze被打到，那他就要進入一個被打到的狀態，以pseudocode表示：

if 被打到第一下：

進入behit1

if 在時間內被打到第二下：

進入behit2

if 在時間內被打到第三下：

進入behit3

if 在時間內被打到第四下：

進入behit4

進入liedown

if 被跑攻、跳攻、跑跳攻打到:

進入behit4

最終若其中一方死亡，於關閉程式時，會執行P1_Win(self)或P2_Win(self)函數，顯示哪方勝利。

# 分工方式

主程式：柏語、彥棆

ＧＵＩ：浩軒、丞劭

美工：所有人

上台報告：所有人

# 心得

## 徐浩軒

現在回頭看，我在開始Project之前其實還沒準備好，對於函式跟Class的概念都很模糊，導致我在後來進入Project之後的前期都沒什麼貢獻，頂多就是做點美工跟幫忙檢查Code有沒有問題。到後來開始負責UI之後，其實還是沒有把這些概念完整弄懂，Tkinter的部分就只能把老師上課Demo的程式碼作為底，把剩下需要的功能加上去，也因為沒有很懂，後期在加功能的時候也屢屢碰壁。
另外在選擇使用的UI時，也須調查好哪種可以跟我們的主程式相容，剛開始我們使用的是PyQt5，起初也是還蠻順利的就把功能用好，直到我們發現無法加入主程式時，才急忙改成Tkinter，但這是在報告前一天發生的，也讓我們多花了很多時間去修正，這也是應該可以避免而沒避免的事。

## 趙彥棆

我覺得這次是一個很不錯的體驗，小朋友齊打交本來就是一個我很喜歡的遊戲，能自己打程式碼來試著復刻這個遊戲我很開心，但相對的也比我想像中還要難上許多，這次遊戲主要遇到的難題在於去判斷哪些動作在什麼時候需要開啟或關閉，同時不同的函數中也會互相影響，我們很常只考慮到其中一個細節去做更改，卻又造成了其他部分的BUG，直到後來我們下定決心重新整理好我們的程式碼，讓它變得更有條不紊後，才讓邏輯整個清晰起來，成功完成最後的程式，但這個程式仍有不少未完善之處，之後若有時間我們應該會將它優化完成!

## 陳柏語

我覺得做出一款動作遊戲比我原先想像地困難許多，原因是因為有很多的動作的觸發條件都應該是唯一的，像是我們那時候並沒有考慮只有在不是走路的時候才能攻擊，就變成角色會做出邊走路邊攻擊的荒謬動作，所以確定「在哪些情況下可以做哪些事以及哪些情況下不能做哪些事」就變成了我們製作這個遊戲的必修課題之一。再來是動畫更新的問題，動作遊戲無時無刻都在更新畫面，那要如何讓所有畫面跑得很順暢就變成了一個很困難的問題，而這個部分我相信我們還有很大的進步空間。經過這次的經驗，我覺得我學到非常多關於遊戲設計的概念以及團隊合作，真的非常有收獲。

## 簡丞劭

寫程式對我來說一直都是一件很不簡單的事，這次的報告中，我非常感謝我其他三個隊友，寫了大部分程式，只叫我做了比較簡單的工作，像是查一些他們不知道怎麼寫的程式，以及美工角色去背。這次我們選擇做了動作遊戲，而一切都不像我們想的那麼簡單，動畫的製程、加入音效，中間出現了各種bug不知道參與了多少我們的夜晚。GUI畫面的製程也出了一點插曲，原本我們計劃使用PyQt5製作主畫面、遊戲說明畫面，但後來才發現我們找不到連結到遊戲的方式，只好改使用老師教得Tkinter，但還好有驚無險還是完成了GUI主遊戲的跳轉。這次的報告，真的很感謝三位隊友的大力凱瑞，也讓我體會到了前所未有的報告過程。
