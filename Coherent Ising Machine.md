---
tags: CIM
---
# Coherent Ising Machine
## 前言
* Coherent Ising Machine (CIM)，是一種用於解決Ising problem的量子機器，有點類似於退火。但卻是based on 物理光學所做出來的機器。
就像老師在課堂中常常提到的，因為摩爾定律的衰落與量子電腦與量子信息的進步，因此survey了這方面的相關主題，並挑選了CIM。報告的內容主要分成兩部分：
    1. 第一部分是講解一下CIM的物理背景與實體機器如何運行。
    2. 第二部分則是，同樣是做量子機器的大公司D-wave在2018也發表了模擬CIM的paper，NMFA演算法，並跑在NVIDIA GeForce GTX 1080 Ti GPU上。在此份報告中也會討論CIM與Noisy mean-field annealing (NMFA) algorithm的效能結果。並於最後討論CIM團隊在2019、2020發表了兩篇paper去比較CIM的實體機器與D-wave最新的實體機器D-wave 2000 qubits(DW2Q)的實驗結果的效能比較。
* 另外，這幾篇paper所跑的問題都是以下三種問題，分別是：Sherrington-Kirkpatrick (SK model)、MAX-CUT on dense graph、MX-CUT on sparse graph。
* 為了達到高效能的計算，量子電腦與量子計算與其他非傳統計算方案開始受到的重視並且被大量研究，其中CIM便是其中一種。CIM是從2003年日本的電信公司NTT與Stanford的Peter L. McMahon教授合作的一項企劃。並且在2016年加入了日本東京大學的量子光學大師，山本喜久。而這兩位大師的名字將會貫穿在以下survey到的全部的paper。
![](https://i.imgur.com/0N2n0q4.png)

* CIM的機器演進大略如上圖所示。其中，最右圖是現今用於解決MAX-XUT的CIM的實體機器的外觀。已知，CIM是用於解決Ising problem的Ising machine，而CIM所使用的Ising spin則是Degenerate optical parametric oscillator，在不同的paper中有時被稱為OPO、亦可稱為DOPO。
* 首先先簡單的對CIM做介紹，CIM是利用laser打出photon pump，並在這些雷射光波經過不同的光學儀器、非對稱性的化學結晶，使得光產生了二次諧振態的光波作為Ising machine中的Ising spin，也就是我們的OPO。在產生OPO之後便開始做coupling的動作，並做降能，類似於做flipping spin的動作，也就是改變OPO的phase and amplitude的state，盡可能在所設定的annealing time去找到最低能量(Hamiltonian)的configuration，即找到所求，或者是即使不是索求也盡可能為相對最佳解。
* 其中影響performance的因素包刮：size，N的大小、annealing time、問題是否為fully connected、Degree的大小…等等。皆會在最後第二部分的地方做效能的探討。


## 主要的Survey paper (做為參考的會補充在Reference中)：
1. (2013) Coherent Ising machine based on degenerate optical parametric oscillators 
2. (2014) Network of Time-Multiplexed Optical Parametric Oscillators as a Coherent Ising Machine 
3. (2016) A coherent Ising machine for 2000-node optimization problems 
4. (2017) Performance evaluation of coherent Ising machines against classical neural networks 
5. (2019) Experimental investigation of performance differences between coherent Ising machines and a quantum annealer 
6. (2020) Synchronously-pumped OPO coherent Ising machine : benchmarking and prospects

## 第一部分、CIM的機器介紹與物理背景
### CIM的物理機器介紹
* CIM的物理機器可以分成兩的部分。第一部分是OPO Part，也就是機器產生OPO (Ising spin)的部分；另外是Feedback Part，也就是機器決定是否要做coupling的部分。其中整個OPO part的過程都是在一個腔體中進行，而被產生的OPO會在光纖所形成的管路中一直跑，直到整個系統的Hamiltonian降到最低，也就類似於退火的過程結束。
* 除此之外，CIM的物理機器大略可分成三代機器，分別是在2003-2012的第一代，團隊在實驗桌上進行小型的CIM實驗。2014是Stanford與NTT合作的CIM的實體機的出現。以及2016後使用FPGA作為決定是否要做coupling的主要算力的第三代機器。其中第三代機器也從2016一路使用到2020在硬體結構上並沒有太大的變化。
 ![](https://i.imgur.com/RDsSKzm.png)
\begin{gather*}Fig.1\end{gather*}
* 首先放上CIM的full machine diagram如Fig.1，這是CIM第三代的機器的架構圖。但因為從第一代到第三代的OPO part基本上大同小異，故先使用第三代的架構圖講解OPO是如何產生的。一開始，會由左上角的laser不斷的打pump。當pump在被打進腔體後，pump會先經過兩種在物理光學上常常被用到的光學機器以及非中心對稱的結晶體，通常會為一組Parametric amplification一起被應用，如下圖Fig.2所示。Second Harmonic generation，簡稱SHG；Periodically poled lithium niobate，簡稱PPLN。
![](https://i.imgur.com/5CZukWM.png)
\begin{gather*}Fig.2\end{gather*}

* 被打出的pump會先經過SHG，次諧波生成（又稱倍頻）是一種非線性光學過程，如圖Fig.2所示。其中具有相同頻率的兩個光子與非線性材料相互作用，被“組合”，並產生具有初始光子能量兩倍的新光子（等效地，兩倍的頻率和一半的波長），可以保持激發的相干性。這是和頻生成（2個光子）的一種特殊情況，更常見的是諧波生成。簡單來說，就是將原本頻率為Wp的pump，在經過SHG後，會分裂成兩個光波，分別稱為signal 以及idler，且因為能量守恆，因此可知Wp=Ws+Wi。當 1/2 Wp=Ws=Wi，這種特殊情況被稱為Parametric down-conversion(PDC)。
 ![](https://i.imgur.com/axegM1o.png)
\begin{gather*}Fig.3\end{gather*}
* CIM便是採用使得$1/2Wp=Ws=Wi$，也就是PDC。而在常用的PDC設備設計中，強激光束對準BBO (硼酸鋇)晶體。大多數光子直接穿過晶體，如圖Fig.3。但是，偶爾某些光子會進行II型極化相關的自發下變換，並且所得的相關光子對的軌跡沿兩個圓錐的邊緣受到約束，其軸相對於泵浦光束對稱排列。同樣，由於動量守恆兩個光子始終相對於泵浦光束沿圓錐體的邊緣對稱放置。重要的是，光子對的軌跡可能同時存在於錐相交的兩條線中。這導致偏振垂直的光子對糾纏。簡單來說，此光波會在經過PPLN。PPLN的作用是為了達到entanglement的作用，讓產生出來的OPO能因為出現機率的不同，而有更多的變化性。其中CIM所使用的非線性對稱的結晶是硼酸鋇。另外PPLN(硼酸鋇)也有另一種功用是作為phase-sensitive amplifier(PSA)，他只能讓pump-phase∈{0,π}的光波，也就是我們所需要的Ising spin通過。而在經過SHG和PPLN後的pump，就是我們所需要的OPO，Ising spin。
* 提完OPO產生的部分要討論在Ising problem中，Ising spin在CIM是怎麼做coupling的部分。而在這部分物理機器則分成三代。
* First generation架構圖如Fig.4所示，fs laser會和第三代機器一樣，不斷打出pump laser，並使用clock (CP) 控制當下打出的光波和前一個打出的光波中的間隔時間，使原本連續的光變成一個接著一個的pump。而在光波被打出後會因為M1~M4這四面鏡子的折射與反射，讓光波在圖中以虛線框框框起的路徑中行徑。其中，在路徑中有置放有一個PPLN而就如同上一段所提到的一樣，在製造出所需要的OPO之後便會開始進行couplings的部分。第一代機器做couplings的方式相對簡單，是利用delay gate的方式進行。舉例來說，假若希望讓OPO1對OPO2做coupling，則在OPO1那一輪的circulation時，在delay gate-1上的OC與IC兩種分光儀，會將一部份的OPO1的光波分到delay gate-1裡面，並讓分進去的光在這個管路中行徑並反射最後再回來。剩下的OPO1則繼續行徑。其中被分進delay gate-1裡面的光，則會在下一輪的OPO2的circulation行逕至delay gate-1的IC時候，OPO2與從delay gate-1中跑出來的先前被分出去的OPO1合併成一個新的OPO2，即完成了希望讓OPO1與OPO2做coupling的動作。同理，若希望讓OPO2與OPO4做coupling，則在OPO2的circulation中，會分一部份的OPO2進delay gate-3，並再OPO4的circulation 的時候，被分出的OPO2會與OPO4會合進行coupling。如同Fig.4所示，希望A跟B做coupling時，應該將哪一個OPO放進delay-gate，與哪個delay-gate裡面。最後再first generation 要特別提到的是，在初代機器只是測試CIM的方法是否可行，且另外因為光速行徑的時間非常快，故初代機器是非常小型，且是在實驗桌上進行的。
  ![](https://i.imgur.com/sUQndeY.png)
\begin{gather*}Fig.4. First　generation　CIM machine,　the　way　doing　coupling \end{gather*}

* Second generation，在確定CIM的概念可行後，團隊便開始實作真正的實體機器，如Fig.5所示。基本上在產生OPO的部分是相同的，不同的點是做coupling的部分。Coupling概念相同的，不同的是原本的delay-gate會全部改成EOM，電光調製器。用於可以用來調製強加相位，頻率，振幅，或偏振的光束。通過使用激光控制的調製器，可以將調製帶寬擴展到千兆赫茲範圍。光在經過splitter的後，會選擇進入不同的EOM，也就是類似於不同的delay-gate，在EOM中會保存要被拿去做coupling的光波，其中EOM也會補光、調製OPO在EOM中的phase，要做這些事情的原因是因為光不能存在在光纖中太久會消散。而在要做coupling後會在經過combiner傳送進去光纖裡面做coupling。
![](https://i.imgur.com/1DmNQFy.png)

 \begin{gather*}Fig.5 Second　generation　CIM　machine\end{gather*}

* 然而，因為EOM非常貴，因此團隊又做了機器上的更新，因此有了第三代的機器。Third generation，如下Fig.6所示。產生OPO的地方依舊不變。改變的依舊是coupling的部分。不同於前面會是一個OPO與另一個OPO做coupling，在第三代的機器時，在每一次的circulation中，會是一個OPO決定要不要和除了自己以外的全部的分別做coupling。也就是說，假設全部有4個OPO，則在OPO1的circulation time中，可以同時決定是不是要跟OPO2、OPO3、OPO4做coupling。
   ![](https://i.imgur.com/Hb4wjfA.png) 
\begin{gather*}Fig.6　Third　generation　CIM　machine\end{gather*}
![](https://i.imgur.com/LFs9oYw.png)
\begin{gather*}Fig.7　Matrix　store　in　FPGA\end{gather*}
* 不使用EOM後，CIM的團隊改採用Measurement and feedback，MFB的方式。假設現在是OPO2的circulation time，則OPO2如圖右中的部分，會先進去灰色的長橢圓形，也就是分光儀。OPO2會被分出一些光進入下面做Measurement，其中被測量參數的包括phase跟amplitude，而這測量的部分同時也是Analog-to-digital converter，會將這些測得的光波的參數值傳進FPGA中。由FPGA去做calculation決定如何做feedback的部分。而FPGA中存的是scaling factor的matrix，如Fig.7所示。為什麼會存"ξ"  ̂的原因，在之後敘述Hamiltonian的時候會提到"ξ"  ̂/2代表的是表示the j-th OPO和l-th OPO之間coupling的強度。FPGA會計算feedback signal: f_i=-r∑_j▒〖J_ij (Ci) ̅ 〗 (r是常數)，並根據回饋的訊號決定做coupling與否，讓整體的能量越來越低，若做coupling會使能量變低，則做；反之，不做。
* 那CIM為什麼可以使用FPGA計算是否做Coupling的原因是因為，CIM的整個系統基本上左上角的laser pump會一直打，也一直持續增大pump rate的大小、將其打高，隨著pump rate越來越高，會因為pump rate和OPO的amplitude的實部，也就是in-phase component成正比，當pump rate越來越大的同時，in-phase component也會越來越大，實驗結果如同下圖Fig.8所示。當能夠被測得的光波震幅越來越大後，如同Fig.9和Fig.10所示，Ising Energy同時也在降低。並且在pump rate達到groud state時會產程bifurcatiton，，並且很快就會得到我們所求，能量最低的狀態。
![](https://i.imgur.com/pMjA2Vo.png)
\begin{gather*}Fig.8\end{gather*}
![](https://i.imgur.com/EYwLXnm.png)
\begin{gather*}Fig.9\end{gather*}
 ![](https://i.imgur.com/ufRBXA7.png)
 \begin{gather*}Fig.10\end{gather*}

* 最後再補充一點是，為什麼CIM的光波(OPO)能夠一直在光纖中存在而不消散，原因是因為如果他的機器架構圖中顯示的，在Fig.1的左上角的laser pump下面有接出另一條線路，這條線路便是用來補充pump energy進入各個OPO中，使OPO在很長的光纖中不消散的原因。

## CIM的Hamiltonian與作用理論
### CIM的Hamiltonian如下：
![](https://i.imgur.com/EHfUBbr.png)

### 討論
* 這部分會討論三條Hamiltonian的來源，然而因為第三條式子推不出來，因此下面只討論兩條Hamiltonian的部分。
* 先推導第一式：
    * 已知一般的彈簧震盪總能量可寫成如下式子所示：
![](https://i.imgur.com/xWtGILS.png)
    * 接著定義兩個常用的變數：
 ![](https://i.imgur.com/T3WouXl.png)
    * 故，上式可改寫成：
![](https://i.imgur.com/VQVnff8.png)
    * 定義新變量，則式子可再被改寫為：
![](https://i.imgur.com/bAhIu7H.png)
    * 而為什麼會有底標S和P的差別的原因是因為，在前面物理機器的部分有題到pump打出laser後經過SHG和PPLN會變成signal與idler。然而，無論是laser還是被產生出的新的signal wave，都是存在於作用腔體中的，因此會有兩種光震盪器的能量，也就如式一所示。
* 再討論第二式：
    * 我們的原型系統是一個帶有腔內光子晶體的OPO，在下面的OPO中，如Fig.11所示。通過控制空間不穩定性閾值和量子現象來控制現象，也就是在物理機器部分題到的，CIM會利用控制pump rate的打高搭配光子本身的特性，達到降低Ising energy的笑果。因此在討論第二式的時候，不僅考慮電磁的經典動力學OPO中的場也要討論空間模式之間的量子相關性，得到一個完整的多模哈密頓量，這也是為什麼在一般的Ising problem中的Hamiltonian通常只有一式，然而在CIM中會具有三式的原因，原因正是因為他的做法不同於一般的方式，是利用外力所產生的pump rate去控制整個能量場。
![](https://i.imgur.com/i1CScDA.png)
\begin{gather*}Fig.11\end{gather*}
* 在一般的電磁場時，應該考慮的是無限數量的模式，也就是說每個模式在原則上都應該具有無限數量的可能量子態，這也是為什麼在物理機器的部分需要PPLN的原因，為了創造出所有量子態。而描述OPO中的腔內動力學，分別引入泵頻率2ω和信號頻率ω的玻色子空間模式$\widehat{(A0)}(x, t)$ 和 $\widehat{A1}(x, t)$，其中x表示橫向坐標，而z表示橫向坐標方向相關性在mean field近似內平均。Standard equal-time commutation relations在interactivity fields則可寫成：
 ![](https://i.imgur.com/P3uetwO.png)

* 接著考慮整個系統(包括：腔體、環境)：通常只具有有關環境狀態的統計信息，因此，引入了總密度算子ρ ̂_tot，而不是總波函數。在Schrodinger pictureρ ̂_tot的演化von Neumann equation描述：
 ![](https://i.imgur.com/F7MsSN7.png)

* 其中，$\widehat{H}_{tot}$是描述空腔、環境及其相互作用的Hamiltonian，也就是在此部分一開始所提到的$H_tot$。然而，因為只需要知道系統的Hamiltonian，因此只需要interactivity fields,的evolution equation的reduced density operator $\widehatρ$，並且通過對環境自由度進行trace可以得到：
 ![](https://i.imgur.com/uQLgmcR.png)
* 腔內動力學由降低密度算子$\widehatρ$ ̂的Master方程描述：
 ![](https://i.imgur.com/6vSaRyn.png)

* 作為intracavity fields的 $\widehat{H}$ LiouvillianΛ是super-operator考慮通過部分切除腔鏡的dissipation (with strength γ)：
 ![](https://i.imgur.com/ZEIodCH.png)

* Master equation有兩個terms：描述腔內模式耦合的強耦合項，其形式與不存在耗散時的形式相同；以及耗散性 在沒有強漢密爾頓耦合的情況下，terms具有相同的形式。是為獲得方程式所做的最重要假設。
* 上面的腔內動力學由降低密度算子ρ ̂的Master方程描述是Markovian approximation。在這種近似下，我們假設整個系統中有兩個不同的時間尺度，分別由完整性系統和環境的衰減時間給定。當環境和系統在同一時間尺度上演化時，馬爾可夫近似無效，因此在這種情況下，我們無法獲得主方程。
* 然而，反之在fast evolution of the environment的狀態下，我們可以忽略平衡環境所需的時間，並且獲得根據近似所得到的方程式，就是上面兩式子。我們系統的哈密頓算子是fields operators $\widehat{(A0)}(x, t)$和 $\widehat{(A1)}(x, t)$的函數：
 ![](https://i.imgur.com/mjeU94B.png)
    * (補：這邊的底標和最一開始提到的CIM的Hamiltonian不一樣)
* 明確考慮空間依賴性。得哈密頓量的第一部分是：
 ![](https://i.imgur.com/HfzHEEP.png)

* 描述free propagation of the fields inside the cavity，其中γ_i是cavity decay rates、$Δ_i$是cavity detunings、a_idiffraction constants，則得：
 ![](https://i.imgur.com/iFgimrE.png)

* 由於在頻率2ω下與外部pump的相互作用，選擇實數部分得：
 ![](https://i.imgur.com/Wb2Wb9I.png)

* 其中g為非線性係數，是由於系統的非線性導致的一次諧波和二次諧波之間的相互作用項，這是引入的一次諧波的多模形式描述退化參數下轉換過程。
* 而最後此式，即為在CIM的Hamiltonian的H_int的前半部分，和在推導式子一的時候一樣，只要對照pump和signal wave變會符合其format，此處不再贅述。
* 而再得到Hamiltonian後，由於整個系統式動態過程，CIM的行徑過程式隨著時間增加讓系統能量慢慢降低的過程，因此需要將時間概念引入系統能量中。故將Hamiltonian對τ，也就是delay time (pulsed time)做微分後、做c-number Langevin equations reduction後，得到對於每個OPO的複數振幅(𝑐𝑜𝑚𝑝𝑙𝑒𝑥 𝑎𝑚𝑝𝑙𝑖𝑡𝑢𝑑𝑒)：
 ![](https://i.imgur.com/FkB1uLX.png)

* 而在考慮Ising spins間做coupling的部分後，complex amplitude將改寫成：
 ![](https://i.imgur.com/qu52k0M.png)

* 其中，$\widehat{ξ}/2$, the scaling factor the complex signal field $\widetilde{A_j}$ of the j-th DOPO when it is coupled to the l-th DOPO.
* 最後將complex amplitude拆解成實部(in-phase component)與虛部(quadrature component)後：
 ![](https://i.imgur.com/0OKArPz.png)

* 其中，$P = F_p/F_th$ : normalized pump rate， ![](https://i.imgur.com/uyIf1qD.png)

* 而就像我們在前一個部分，也就是物理機器的部分有提到過的CIM的運行原理，CIM的整個系統的laser pump會一直打，將pump rate打高，隨著pump rate越來越高，會因為pump rate和OPO的amplitude的實部，也就是in-phase component成正比，當pump rate越來越大的同時，in-phase component也會越來越大。當能夠被測得的光波震幅越來越大後，Ising Energy同時也在降低。並且在pump rate達到groud state時會產程bifurcatiton，，並且很快就會得到我們所求，能量最低的狀態。

### 第二部分、CIM的效能
#### CIM simulation on classical computer
* 在2018年的時候，D-wave在arxiv上發了一篇CIM的simulation的論文，” Emulating the coherent Ising machine with a mean-field algorithm”。以CIM是將Ising problem的能量最小化，其中在前面推演其Hamiltonian的過程可以發現，整個CIM降低能量的過程是會受到noise所干擾的。而D-wave發表的方式則是利用與CIM類似的noise，進行比較平均場退火的演算法，Noisy mean-field annealing (NMFA) algorithm。Pseudo code如下所示：
 ![](https://i.imgur.com/l78kYps.png)

* 從演算法上講，CIM遵循一個簡單的循環，也就是我們在前面所提到的CIM的一個circulation，在每次的循環中重複測量spin的state，然後跟從該測量中得出的mean-field做組合，去決定是不是要做coupling的動作。D-wave將CIM性能與NMFA進行了比較，NMFA是一種演算法，使用[−1，1]，而不是CIM中所使用的光脈衝相位。CIM使用光脈衝注入mean-field，而NMFA使用Boltzmann factor注入mean-field用以降低溫度，作法類似annealing (相類似於SA之類的做法)。而在此篇論文中，D-wave也有敘述了NMFA演算法中的變數對應於CIM中的變數以及相對應的物理現象，在這篇報告不會詳細做討論。其中D-wave使用跑在GPU上的code也有release出來。
* D-wave將NMFA演算法跑在GPU上，而CIM則是跑在Stanford的CIM上。並且最後得到的結果如下表格與圖示：
 ![](https://i.imgur.com/tZx0GGe.png)
![](https://i.imgur.com/6VLULEe.png) 
* D-wave參考了CIM團隊發表的其它幾篇論文，去採用Stanford的CIM和NTT的CIM跑的三種隨機問題的結果分別是Sherrington-Kirkpatrick (SK model)、MAX-CUT on dense graph、MX-CUT on sparse graph，並用NMFA也跑了相同的題目。他們使用NTT的CIM機器和NMFA跑在GPU上做比較，三種實驗的數據結果分別如上圖所示。
* 在圖4a中，進行的是SK model的問題，且定義Jij = 1的機率為p=1/2，除此以外的Jij=-1。圖4b則是跑dense MAX-CUT的問題，設定Jij=1的機率p=1/2，剩下的Jij=0。而圖4c則是degree = 3的MAX-CUT，其中每個spin與其他三個spin做coupling(所有nonzero couplers的Jij = 1）。
* 圖4d-4f顯示了利用時間做為參考的實驗效能結果。
* 其中D-wave在使用Stanford的CIM跑了10,000次，並且取其中最佳的1000次結果做圖。而在NMFA的部分，則是10,000次數據全取下去做圖。
![](https://i.imgur.com/vxxPH5L.png)
 
* 在上圖中，D-wave針對三種問題跑了Stanford和NTT的CIM、NMFA在NVIDIA GeForce GTX 1080 Ti GPU上，並計算每一次的運行時間。
* 跑dense MAX-CUT的結果是：Stanford的CIM((最大的spin數量為N = 100)單次運行需要1600µs。NTT的CIM(最大的spin數量為N = 2000)的運行時間為5000 µs，然而，因為NTT的CIM可以做平行化，因此在跑dense MAX-CUT的問題的時候是分成20個平行化，且每個N=100，則可以將每一次的運行時間降低成250 µs。其中，Stanford和NTT的CIM機器的一些overhead，像是readout和postselection都是被忽略不計的。對於在NMFA跑在NVIDIA GeForce GTX 1080 Ti GPU的結果是，每次運行時間大約是12.3 µs。
* 因此最後的結果是，跑在GPU上的NMFA相對Stanford的CIM快了130倍。相對NTT的CIM，NMFA的速度約比其快了將近20倍速度。
* 並且在最後的圖表中可以發現，NMFA的效能均與CIM相當、甚至是比不論是NTT還是Stanford的CIM的效能都來的更好。
* 在經過上面的比較後，D-wave對CIM這種量子光學儀器下的評論是：儘管它確實代表了光學的一種新穎用途，但尚未提出宏觀的計算量子模型。因此，目前尚不清楚潛在的潛力相干的伊辛機具有實用性，但可作為未來的計算技術。

#### CIM的實體機器與D-wave的實體機器的實驗比較
* 那在2018年D-wave發表了前一個部分所提到的CIM的simulation並做出一番見解，認為CIM的實體機器技術還有待發展後。CIM便於2019與2020發表了兩篇論文回應了D-wave對於CIM的評論。分別是：(2019_Science) Experimental investigation of performance differences between coherent Ising machines and a quantum annealer，與(2020_SPIE) Synchronously-pumped OPO coherent Ising machine : benchmarking and prospects。
* 其中，比較的對象並不是D-wave之前做的NMFA演算法，而是D-wave實體機器的對決。比較用的機器一樣式Stanford和NTT的CIM機器，與D-wave在2019最新的機器D-wave 2000 qubits(簡稱DW2Q)做比較。並提出DW2Q機器本身一個重要的問題點就是embedding的問題。並且針對embedding做了一番論述。其中包括D-wave的機器無法跑到更多的qubits的原因便是因為D-wave的機器在跑fully connected的問題的時候，會於到embedding的問題。因此基本上雖然有2000個qubits，然而實際上只能到跑兩千開根號，大約是64，但實際上則為61的qubits。比Stanford的qubits數量 = 100，和NTT的CIM的qubits = 2000都來的小許多。因此在最後比較當qubits數目超過61時，D-wave的所花的時間並不是真正使用D-wave機器的時間，因為他還沒辦法跑那麼多qubits，而是根據在N=61以下的趨勢線去做內插所得到的結果。並且所有跑的問題皆和上面一樣。包括SK-model、MAX-CUT on dense graph、MAX-CUT on sparse graph。
* 首先先看SK-model on a fully connected graph(NP-hard)，CIM的團隊利用SK-model這個問題去找到在不同的size(也就是N)和不同的問題下，對於D-wave最佳的Jij。並在下一個實驗MAX-XUT on dense graph上，CIM和D-wave都是使用對於D-wave比較有利的Jij的值寫入Hamiltonian。原因為了做到讓D-wave的機器能夠有比較好的效能。其中SK-model的規定如下：
	* Couplings Jij = ±1 發生機率相同，隨機取。
	* Ground-stat computation of the model 和 "graph partitioning problem(NP-hard)"有關。
	* DW2Q每輪會做20次，從Problem size : 2 ≤ N ≤ 61中隨取N的大小。
	* We consider as a performance metric the success probability P, defined as the fraction of runs on the same instance that return the ground-state energy
	* 在N≥60的時候，此實驗只有CIM會進行，因為problem size太大，沒辦法embed到DW2Q上。而就像前面提到的，D-wave的時間將會從已經有的在N比較小的實驗數據中做內插法得到。
	* 關於SK-model的ground-state：
        * a. SK ground states were found with the Spin Glass Server, which uses BiqMac, an exact branch-and-bound algorithm.
            * → @N≤100N≤100 時，此演算法已被證明能找到SK-model的 ground state
           *  → @ N≥100N≥100 時，尚未被證實。然而在此實驗室假設在N≤150N≤150時，用此演算法求得之 SK ground states 都是正確的。
        * ⟹ 原因：
           *  a.) 他們在在100≤N≤150100≤N≤150重複跑了很多次這個演算法，去求其 SK ground states energy，求得數字都相同。
            * b.)且除此之外，在實驗中run SK-model的時候，所得到的 SK ground states energy皆比用此演算法求得得還高，故信之。
* 在得到在不同的N下對於DW2Q的最佳Jij後，CIM的團隊便用了這些based on不同的N上的Jij run在MAX-XUT on dense graph的實驗上。其中此實驗的規定如下：
    * Dense and sparse for random unweighted graphs.
	* Expressed by Ising problem, settings: antiferromagnetic couplings Jij = +1
	* Problem sizes：
        * (a) DW2QN ≤ 61，(b) CIM N ≤ 150
	* Finding ground-states :
        * @ N≤30，在個人筆電上暴力算出來的。
        * @ 20≤N≤150，使用 Branch-and-bound algorithm.
* 並得到最後的結果如下圖：
 ![](https://i.imgur.com/J6Ku15H.png)

* 我們可以從上圖中發現：
	* 圖b,D-Wave ground-state probability 在SK model中，調整Jc(也就是Hamiltonian裡面的Jij)做出的圖。並且將N和optimal Jc的寫成 function供下一個實驗使用。(且固定annealing time ，Tann=20 ms)，並求出N和最佳Jij的公式：Jc = 1.1N1/2。
	* 圖c，是MAX-CUT on dense graph的結果。其中edge density = 1/2。且CIM的data fitting curve: P=e−(N/NCIM)，其中NCIM是一個上升很慢的常數。D-wave的data fitting curve: P=e−(N/Ndw)^2，NdwNdw是一個上升很慢的常數，且和Tanne有關。自此結果可以看出，即使使用optimal JC。然而，隨著N的上升，D-wave的機器的實驗success probability仍會以exp速率遞減。另外，對比上面D-wave的結果，還可發現CIM比較不會受到N的影響，一直到N ≥ 50 後，CIM的success probability才會開始急遽下降。
	* 圖d，MAX-CUT on sparse graphs，d=3,4,5,7,9 edges/vertex。
* 在這裡他們使用了"embedding heuristics (provided by D-wave API) "一種做embed的方法，他可以讓我們在embed sparse graphs時，只需使用較少的physical qubits。用這個方法使D-wave的 constraint on connectivity減少，進而擁有較高的embedding overhead。從圖中可發現：
    * (a) 在d = 3(cubic), d = 4下，DW2Q的表現比CIM好。且在cubic的狀態下，problems size N可調升最大至200，embedded in the DW2Q。
    * (b) 然而，在higher density，D-wave就會回到像在dense graph上一樣，success probability高速下降。而d = 5時，是CIM效能比DW2Q好最顯著的degree。
    * (c) CIM 不會受到 edge density的影響。簡而言之，就是再一次去證明，physical annealers會受到coupling constraints，也就是edge density的影響，且在N ≥ 40的時候，CIM的效能會明顯優於DW2Q。
* 小結論，最後比較CIM跟Dw2Q的performance後會得到，在N ≥ 40時。For MAX-CUT，在N = 55, CIM outperforms D-Wave for factor = 107；在N=100，CIM的outperform factor exceeds 1020。簡單來說，在N越來越大的時後，DW2Q的效能明顯就會比CIM差很多。
* 以上實驗的結果是，CIM於2020年發表在SPIE上的實驗結果，而由於CIM在2019發表在Science上的實驗相當類似於2020年的實驗。只是改變MAX-CUT的degree的數量，故這裡不再花版面去放圖並闡述2019年的實驗結果。
* 並且可將所有的實驗結果整理成如下表所示：
 ![](https://i.imgur.com/AXmUmBn.png)



### 小總結
* 此份整理是做關於Coherent Ising Machine的一個survey，從2003年的paper開始一直看到2020年，也只是根據我個人的理解，有可能會是錯的QQ。
* 從一開CIM團隊在做物理意義上的推導、一直到後來小機器的模擬、實體機器的出現與實體機器的使用。並在最後最了不同問題的應用。
* 從報告的一開始先提到了為甚麼選擇CIM作為此次報告的主題、再來是很簡略的先介紹甚麼是CIM，以及CIM所使用的Ising spin是什麼。再來，對CIM的物理機器做詳細的介紹、接著是CIM的Hamiltonian與作用理論。便對CIM做了一個相對詳盡的介紹。
* 而在介紹完CIM的本體機器與作用理論後，提到D-wave在2018年使用了NMFA這種演算法作為CIM simulation on classical computer，去和CIM的實體機器做效能的比較。會在這裡發現到，跑在傳統電腦上的simulation，也就是NMFA跑在NVIDIA GeForce GTX 1080 Ti GPU上的效能，還是比現今實作出的量子電腦的實體機器來的好。從實驗結果可以發現，無論是從運行時間判斷其效能、還是從跑很多次所得到的結果的正確性作為運行效能的判斷基準。三種實驗，包刮SK-model、MAX-CUT on dense graph、MAX-CUT on sparse graph等，都是NMFA跑在NVIDIA GeForce GTX 1080 Ti GPU上的效能較佳。我們可以從這件事情上發現，量子電腦的確還有很大的空間可以討論，除了希望能夠做到降低運行時間外，另外值得討論的還有效能是否能夠提升的問題。
* 最後提到CIM的實體機器與D-wave的實體機器的實驗比較，分別是CIM的團隊於2019、2020年發表的兩篇papers。從實驗結果我們不僅可以發現CIM相對於傳統的annealers(像是DW2Q)的優勢，大概分成以下兩點：
    * (1)首先，CIM存在的意義則是在前面有提到的，傳統的annealers會因為要將fully connected的Ising problem embed過去到實體機器上，因此會浪費很多qubit下去做connecting的動作。故雖然像是D-wave已經可以做到有2000個qubits。然而，若再遇到degree數量比較大的問題的時候，真正所能使用的qubits數量就會開始急遽下降。甚至到最後是開根號除以二的窘境。而CIM則是因為OPO產生的方式是利用laser pump經過SHG與PPLN去製造出我們所需要的諧振態的光波，一個OPO即代表一個qubit，另外OPO之間並不是沒有entanglement，詳細的解釋可以回到講解Hamiltonian的部分看。唯一需要擔心的只有光無法在光纖中存在很久，因為光會消散。故，我們從CIM的machine full diagram便會發現，laser光波會一直打回光纖中做補光的動作。當然補進去的光的phase和振幅肯定是有被調整過的。簡單來說，CIM的OPO並不會受到fully connected的時後qubits之間需要做entanglement的限制。另外因為有補光的機制，因此CIM的spin數量可以一直加上去都不是問題。
    * (2)另一方面也證明了量子光學方法的重要性。首先CIM因為時做的方法不同於傳統的annealers，因此並不會受到coupling constraints，原因是因為CIM做coupling的方式其實只是利用光疊加的時後會改變OPO當下的amplitude與phase的特性，因此並不存在所謂的coupling constraint。
* 總而言之，CIM是一個不同於傳統退火機器的Ising機器，他是based on物理光學所建構出的另一種Ising機器。並相對一般退火機器有其自身帶來的好處。並且相待期待在未來幾年Stanford和NTT的合作，將CIM的機器精益求精。

## Reference
1. K. Takata, Yoshihisa Yamamoto et,al. “A 16-bit Coherent Ising Machine for One-Dimensional Ring and Cubic Graph Problems.” arXiv Quantum Physics, 2016.
2. G. Patera, N. Treps, C. Fabre, and G. J. De Valcarcel.” Quantum theory of synchronously pumped type i optical parametric oscillators: characterization of the squeezed supermodes.” The European Physical Journal D, 56(1):123, 2010
3. Mater Thesis of Mar´ıa Moreno de Castro. “Control of light emission in Parametric Oscillators with Photonic Crystals” UNIVERSITAT DE LES ILLES BALEARS, 2009.
4. Y. Haribara, S. Utsunomiya, K-i. Kawarabayashi, and Y. Yamamoto. “A coherent Ising machine for MAX-CUT problems : Performance evaluation against semidefinite programming relaxation and simulated annealing” arXiv Quantum Physics, 2016. 
5. Y. Haribara, Y. Yamamoto, et,al. “A coherent Ising machine with quantum measurement and feedback control.” arXiv Quantum Physics, 2015. 
6.  G. M. D’Ariano, M. G. A. Paris and M. F. Sacchi. “On the parametric approximation in quantum optics.” rXiv Quantum Physics, 1999.
7.  H. Takesue, T. Inagaki, K. Inaba, T. Ikuta, and T. Honjo. ” Large-scale Coherent Ising Machine” Journal of the Physical Society of Japan 88, 061014, 2019.
8. A. Yamamura,  K. Aihara, and Y. Yamamoto. “A Quantum Model for Coherent Ising Machines: Discrete-time Measurement Feedback Formulation.” arXiv Quantum Physics, 2017. 
9. Egor S. Tiunov, Alexander E. Ulanov, A. I. Lvovsky. “Annealing by simulating the coherent Ising machine.” arXiv Quantum Physics, 2019.
10. K. Takata, and Y. Yamamoto. “Data search by a coherent Ising machine based on an injection-locked laser network with gradual pumping or coupling.” American Physical Society (APS) PHYSICAL REVIEW A covering atomic, molecular, and optical physics and quantum information, 2014. 
