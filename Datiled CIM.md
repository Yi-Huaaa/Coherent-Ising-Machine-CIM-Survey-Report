# Datiled CIM

## 名詞
1. CIM : coherent ising machine 
2. DOPO : degenerate optical parametric oscillators

## Introduction for CIM (2020,2019)
What is CIM : 
In CIM, the spin network is represnted by "degenerate optical parametric oscillators(OPOs)"，他可以將 pump light $\stackrel{convert}{\longrightarrow}$ half harmonic(半斜振狀態)
    ![](https://i.imgur.com/5a0JDCo.png)

The spin network is represented by a network of
degenerate optical parametric oscillators (OPOs = DOPOs)


## 2016 A CIM for 2000-node optimization problems
## 簡介
* 他們 coupled time-multiplexed DOPO。實作 Max-Cut problems在任意圖上的拓譜，大約使用到2000個nodes。測量其CIM的準確度、運算時間，並和SA的結果做比較。
* 為甚麼要開發CIM這個方法，在2019,2020有整理。這篇提及的原因跟那兩篇一樣。

## Introduction for CIM (2016)
### 一般常見的CIM
*  一般CIM實作的方法：
    1. laser
    2. time multiplexed DOPO
### About 此篇
* Ising Hamiltonion : ![](https://i.imgur.com/k3rhALJ.png =40%x) $\sigma$~ij~$\in${+1,-1}
* CIM solves Ising problems，根據"minimum-gain principle"
    $\rightarrow$ CIM是根據此principle，去調整DOPOs之間的optical couplings
    $\rightarrow$CIM過去是使用 "delayed interferometer(延遲干涉儀)"，但後來發現他只會讓問題更複雜，就放棄了這個方法。
    $\rightarrow$重新設計了新方法：Measurement and feedback (MFB), make it possible to couple any combination of N DOPOs.
* 他們在此使用的實作方法是：Measurement and feedback (MFB)
     $\rightarrow$ 簡單來說就是：先進行測量，然後在對原本的機器做回饋的動作進行校正。
      $\rightarrow$ 在後面會提到，他使用的DOPOs有分為 training DOPOs and signal DOPOs.其中 training DOPOs就是用來做回饋的。
* 每一個DOPO有兩種phases, 分別是 0, $\pi$，而這兩個 0, $\pi$，是相對於"pump phase above the threshold"所訂出來的。Based on this properties, 他們才可以使用photonics technologies.去實作人造的spin.
// 解釋：[Generation](https://en.wikipedia.org/wiki/Squeezed_states_of_light)的部分
* CIM consisit of 2048 DOPOs wuth full spin-spin couplings
    $\rightarrow$ Full : 2048個DOPOs, implying $\binom{2048}{2}$$=2,096,128$個spin-spin couplings 
* 腔體中的器材
    * PPLN : 
        1. 用途_1：as a "waveguide" (SHG)，放在前面
        2. 放在 1-km cavity 中
        3. 用途_2：as a "phase-sensitive amplifier(PSA)"，放在後面
        $\rightarrow$ 他只會放大相對於pump phase 為{0,$\pi$}的phases
    * 9:1 coupler
        1. 在整個實驗中會放兩個, 後面稱為 coupler1, coupler2
        2. coupler1用途：抓取DOPO pulses
        $\Longrightarrow$而這些被抓取的DOPO pulses是用來被抓取去做"balanced homodyne detection(BHD)，平衡零差檢測"。
        3. coupler2用途：用來injecting feedback signal pulses. 
    * a 1-km 的dispersion-shifted fiber (色散位移光纖)
* Steps
    * 產生DOPOs
        1. 當開始pumping PSA時，"uadraturesquzzed noise pulses"會被產生，做為"[spontaneous parameteic down conversion](https://en.wikipedia.org/wiki/Spontaneous_parametric_down-conversion)"
        2. 然後noise pulses會經過 "repeated phase-snensitive amplification"產生所謂的"DOPOs".
    * A round-trip
        1. 每一次的進入腔體的round-trip time : $5\mu s$, pump pulse interval 是$1ns$。
        2. 腔體裡面最多可以容納5082個DOPOs
        a. 其中的2048個DOPOs被用作"Ising spins"，稱為"signal DOPOs"
        b. 剩餘的2834個DOPOs被用作"training DOPOs".
        $\rightarrow$ training DOPOs 會一直存在在腔體中被osillated。他們的作用是用來取得：
            * error signals for phaselocking the 1-km fiber cavity, 
            * the local oscillator for the BHD and the coupling pulses 
        3. 用5ms做為一個period，使用pump laser的的on/off。對DOPO進行開/關。
        4. 在每一輪的DOPO (every circulation of DOPO)，signal DOPO from coupler1，會使用BHD測出其 $\overline{C~i~}$(inphase components) of the signal DOPO.
        5. 其中DOPO的震盪器 for HBD的power是2.5mW，用其shot noise power ~8dB larger than the thermal noise power.
        $\rightarrow$ Thermal noise power：Thermal noise is a noise that is a result of the thermal agitation of electrons. The thermal noise power depends of the bandwidth and temperature of the surroundings.
        6. 而在4.所測到的$\overline{C~i~}$，會被送到FPGA module上
        7. {$J~ij~$}是一個用2048X2048大小的矩陣中，這個矩陣一開始就會被送進FPGA裡面
        8. 接6.，在FPGA擁有{$J~ij~$}矩陣、$\overline{C~i~}$之後，，FPGA會計算 feedback signal。對於i-th pulses，其 feedback：$f~i~=-r{\sum}J~ij~\overline{C~i~}$。fu65j for every DOPO circulation。其中r代表constraint between couplings strength，而在此實驗中，$r$~$0.01$。
        9. 之後會有一個push-pull modulator 會impose the feedback signal on the "coupling pulses" (字 pump pulses中抓出來的)
        10. 在那之後，modulated coupling pulses會字coupler2重新在被發射進去腔體中，因此a coupling pulses that convey feedback signal 會被injected into i-th signal
        //原文(這句還沒看懂)：Then, the modulated coupling pulses are launched into the cavity through coupler 2 so that a coupling pulse that conveys fi is injected into the ith DOPO.
        會這樣說是因為，整個MFB swquence是一直不斷被重複的去重新進入整個computation sequence中。
        11. 而FPGA和電腦大約使用~60(s)的時間將problem matrix 和 computational result data算完，原因是因為他們只使用了slow serail communication interface that is only avalible port in the module.
        $\rightarrow$ 若改使用high-speed interface，可使傳輸data變快
        12. 最後整個實驗室對computational results of the CIM和跑SA去做比。SA是跑在Intel Xeon(2.67GHz) processor for 2000-node graph上。
        13. solve MAX-CUT problem for the 3 types of 2000nodes undirected graph分別為：G22, G39, K200。
![](https://i.imgur.com/GsLtdJb.png)

## CIM_DOPO v.s. SA Experiment
G22, G39, K2000圖的定義：

![](https://i.imgur.com/vOoFTKo.png)
![](https://i.imgur.com/g5CshsU.png)


## Introduction for CIM (2013)
(數學理論放棄)

## Large-scale Coherent Ising Machine (2019-2)
(先不要做)


## 專有名詞補充
1. minimum-gain principle
    Lasers are open dissipative systems that undergo second-order phase transition at the oscillation threshold. Multiple cavity modes in a laser compete for the available gain. Once a particular mode oscillates, the gain accessible to other modes is suppressed below the threshold value due to the cross-saturation effect. 
    **Since the mode with the minimum threshold gain is more likely to oscillate first, it has the advantage over other modes to spontaneously emerge through the mode competition. This phenomenon is to be referred to as the minimum gain principle in the following.**
    It is demonstrated that the overall photon decay rate in a mutually injection-locked laser network can be engineered to be in the form of an Ising Hamiltonian. Each laser in the network is polarization degenerate, and it represents Ising spin +1 in the case that right circularly polarized photons outnumber left circularly polarized photons and Ising spin -1 in the opposite case.


/

you look at the inphase and quadrature plot of light that comes out below threshold you generated squeezed vacuum but if you end up generating squeezed coherence states and if you are sufficiency far above threshold it really just look like coherent state and the interesting is that you always get a coherent state that's either in phase or exactly out of phase so Pi state with repect to the pump

## Talk 逐字稿
----------------------------------------------------
Title : AQC 2016 - A Fully-Programmable Measurement-Feedback OPO Ising Machine with All-to-All Connectivity
website : https://www.youtube.com/watch?v=C0CMydhdRbM&fbclid=IwAR1U379cf0TnfTzr2GyCAZ0_FrOg9JDveTFKTDkMPbH_e2X--Ezr2VntjrU
1. 這是這篇talk的逐字稿，有跳過一些我覺得不重要的部分
2. *OPO : 代表這個部分是在敘述OPO的意思，類似於一個篇章的title。
----------------------------------------------------

* OPO
一開始打進去一種波with it's frequency
同時要放入綠色的beam frequency
And you can put in a signal beam at the green frequency.And this device will act as an amplifier and trandfer power from the blue pump photons into both the green and red photons, that act as an ampilfier.And form what's called OPO.
Todo
One is that you can think about waht's happening here is when you pump the system with this blue light, when the system basiclly takse oe photon of that blue light and convert it into two photons, at half the frequency.
And in our case, we purpose operate in the so-called degenerate regime, where really it is half the frequency, although you can design it so that it's different frequencies.
And the system has a threshold, just like the laser has a threshold. So as you pump it, initially, you don't get much output light for the given pump input. But as some point there's a large kink(糾結), and you suddenly see a lot more light coming out.
//開始共振，也就是開始分離的時候
///
And the third property is one that differs from lasers. And this is basically the reason we use OPOs, and it has to do with the phase property of the light.(搭配分開的塗了)
So OPO below threshold,if you look at the phase and quadrature plot of the light that comes out, below the threshold you generate squeezed vaccum. But if you go above threshold, you end up generating squeezed coherent states.
ANd if you're sufficiently far above threshold, it really just looks like coherent states
It's interesting that you always get a coherent state that's either in phase or exactly out of phase(pi phase) with respect to the pump
That's very different from a laser. A laser, you can get a coherent state that lies any way on the circle. And this is the reason we like OPOs, since it's a natural binary variable, the phase of out pulses
那藥創造許多不同的OPOs的時候，基本上你可以使用像是坐許多不同的腔體之類的但就很貴，所已他們使用的方法是 time multiplexing, 
///
so we used pulsed OPOs.
And the pulsed OPOs operate as follow : (接time multiplixed OPO)
* 講解PPLN
inside we have this little nonlinear crystal and you can design this so that this cavity can hold just one pulse so if the laser here has a repitition rate which spilts out pulses every period in one nonasecond of 10 nonasecond  for example and you can design the cavity so that it holds excatly onee pulse at a time but you don't have to do this is the normal way that people do ,改善的方法就是讓cavity做長一點就好 and you cam have the cavity actually stall multiple pulses at the same time and in the first initial principle sort of proof of principle ezperience you did this was 
///
* introduce coupling : 
it's the way we do this by introducing in this case beam splitter in the cavity and a delay line and this delay is set to be in this case excatly the delay between two falses so you're taking some light for example the first pulse and inject ot into the second pulse and depending on whether it's in phase or out of phase when it gets back to the thing locked 
* 這可能會造秤破壞性或建設性干涉
you may think there will be destructive or constructive interference), 
and you can make more delay lines and introduce couplings between not just nearest neighbor but next nearest neighbors and so on and in the case of this in equals four experiment that what was implemented was basiclly couplings between all four spins in the experimental results 
//
值線圖
if you don't have the couplings so on you see that your outcome is just random in every state and every possible configuration of spins is equally lucky but if you turn the couplings on only the one that correspond to the solution of the ising problems which we always consider as max-cut problems there is directly mapping between max-cut and the ising model
///
* so how do you scaling this things up
instead using a free-space cavity, using a fiber cavity, in that way we can make a very long cavity
so that's waht's depicted here.
we need N-1 delay lines, if we want ot have an N-spin system. And because we don't necessarily want ot have all the couplings on all the time, we need to put modulators(COM) in each delay line. 但有個問題 delay lines 不貴，但COM很貴. And you have to phase stablize the entire system, and you also are eating your power that's inside the cavity by splitting it up so many times. So you have to introfuce amplifier, and this introduces additional problems.
///
接下來要說他們如何改進了
so the basic idea is that instead of having this large network of delay lines, we are going to replace the network with a measurement-feedback scheme.
和先前一樣，前面有個SHG做為waveguide，接系來pulses打進cavity 中。接著和先前不一樣的
，a beam splitter that taps out a little bit of the power. 
And we ,measure the phase of each pulse using the balanced homodyne setup here.
We feed that into an FPGA, and then we get the FPGA to compute what would have happened, essentially if we had that delay line work. And them we inject light back into the cavity, that is modulated by these two intensity(IM) and phase modulators (PM) drvin by the FPGA.
So what is FPGA actually doing? Well the calculations it's doing not particularly complicated. It's essentially for every pulse, it needs to compute this vector, vector dot product. So that's really all there is to it.
更詳細的說做的啥：
in one round trip of the cavity, there are 160 pulsese in there.實際上只用了100個。Each has a phase that we measure.也就是C1, C2。接下來在FPGA中，我們計算vector, vector dot product. And then when the next pulse1 comes along, we feedback with this vector, vector dot product that's been computed, and so on the next one.
So every clock sysle what I call a clock cycle is the seperation between optical pulses. every clock cycle we have to compute this little sum.
And it's doing it based on the previous round trip of phases. So it's a little bit different from the fully free-space, fully coherent system, because it's not using the phases as right now. It's using the ones from the previous round trip.但反正從他們至今的測驗到現在，這見士很明顯的不重要XD

----------computational results---------------

首先跑的圖是：MAX-CUT,一個 16-vertex ring-and-spoke graph(就是Mobius ladder)
So what happens when you run this on the machine? So as a function of the computing time or the round-trip so every time the pulses go around the cavity, we measure them. And we keep track of it so we can watch what's going on. We see that the starts off is just randomly jumping around. after turning the feedback on, as we turn it on, we see it start to drive the system into a lower and lower energu state, or closer and closer to th max cut until it reach the maximun cut and ust stay there. So at least for this graph, in this run, it work great.然後他們做了很多次幾乎拿到了100%的正確率
之後在random 取了兩個圖去跑，他們發現即使沒有達到正確的答案，達到goiund state，他們的energy也是很接近的。  