# Summary_of_KKR
(未完成)
## Purpose
KKR法について勉強した結果、特にグリーン関数の計算手法についてまとめることを目的とする。

著者としてはKKR法について書かれた書籍はいささか詳細なところまで記述しすぎているかあるいは式変形を追うだけでその式の意味が分かりにくくなってしまっている書籍が多いと感じる。
したがって、この記事ではなるべく細かい式変形などを省き重要な事実のみについて触れ、KKR-Green関数法を用いた論文に出てくる(私を含めた)初学者とってよくわからないグリーン関数の実空間表示や散乱行列についてその物理的な意味を理解できるようにすることを目的とする。

ただし、著者の不勉強によりKKR法に基づく研究を網羅的に取り扱うことは不可能である。もし、それを知りたければH.Ebert教授のKKR法のレビュー論文等を読んでいただきたい。
また、間違いが(多分に)含まれると思われるので注意されたい。さらに、不勉強によりあまりDFTによって物質を計算した具体的な研究などは挙げられないかもしれない。

この文章の内容はKKR(-CPA)法の発展に数多くの貢献をしている、G.M.Stocks, J.S.Faulkner, Y. Wangらによって近年出版された著書、"Multiple scattering theory"の内容を
特に参考にして書かれている。この本ではポテンシャルの形状を以下で説明するマフィンティン近似に限らず、フルポテンシャルの場合にも適用できるように一般化している点が特徴的である。
ただし、数学的記述が詳細すぎるため1から追うのが難しいという印象がある。

## KKR-Green関数法とは
KKR-Green関数法(以下KKR-GF法)は多重散乱理論に基づき、固体あるいは周期性のない原子や分子のクラスターの系のシュレディンガーあるいはディラック方程式に対するグリーン関数を実空間において求める手法である。
KKR-GF法ではグリーン関数は散乱の経路を表す行列(Scattering Path Operator, SPO)を用いて
```math
\begin{split}
G(\vec{r},\vec{r}',z) =\\
\tau
\end{split}
````
と表される。これらの式はKKR-GF法における最も重要な式である。これらの導出が本ページの目的の１つである。

導出に入る前にKKR法の長所について述べておきたい。多重散乱理論ではポテンシャルが空間的に分割された描像を考えるのでいくつかのサイトを置換した状況を考えやすいなどのメリットがあり、CPAなどのSingle-site approximation(単一サイト近似)を用いた系の計算と非常に相性が良い。また、実空間のグリーン関数を求めることでその虚部のトレースをとってエネルギー積分を行うと実空間における電荷密度を求められるため電荷密度の自己無撞着計算を行うDFTループを自然に組み込めるというメリットがある。

以下ではKKR法で仮定している系の物理的な状況に触れながら、KKR法におけるグリーン関数の計算やそこに現れるT行列をサイトで分解することで得られるScattering Path Operator(SPO)について導出を行い、それらの特徴について解説したい。不純物が存在する場合のSPOの表式はとくに"不純物"を入れたときの系のグリーン関数やエネルギー変化を考える、CPA法やリヒテンシュタインの方法における計算で必要となる。この不純物を入れた時の状況についても記述する(予定である。)。

以下では固体の場合に話を限定する。

## KKR-Green関数法におけるグリーン関数やSPOの計算
KKR-Green関数法とは実空間における以下の微分方程式に従うグリーン関数を多重散乱理論に従い求める手法である。
$$( z - H ) G(\vec{r},\vec{r}', z) = \delta (\vec{r} - \vec{r'})$$
ここで$`H`$は一体のハミルトニアン(ただし、Hartree-FockやKohn-Sham方程式のハミルトニアンを考えることにより部分的に相関の効果を入れることを可能)である。 
このグリーン関数は1粒子グリーン関数と呼ばれ、多くの1体演算子の期待値や厳密な応答関数を近似した量を取り出せる、いわば物理量の母関数のような重要な量である。(See AGD or Fetter-Walecka or M.Ogata)

ここで注意したいのがKKR法では多重散乱理論を展開するためにポテンシャルについて空間分割ができると`仮定`することである。この空間の分割は通常は原子を取り囲むマフィンティン球や多面体を作ることによって行われる。そして分割された各領域内のポテンシャルは多面体の中にある原子"のみ"によって決定されると仮定される。これによりある１つの原子が別の原子で置換された状況ではそのサイトに対応するポテンシャル"のみ"が変化するという描像が成り立ち、これはサイトの変化を考えるCPAやリヒテンシュタインの方法などの理論の計算を用意にするのである。

(しかしながらこのポテンシャルと原子の１体１対応はあくまでも仮定でしかなく、実際の物理系においてはなりたつ保証はない。例えば、ある原子の影響がその原子まわりのポテンシャルだけでなく隣あるいはさらに遠くの原子のポテンシャルを変化させる場合が存在する。しかし、このような場合にはクラスター法などのあるサイトの変化がおよぼす周りのサイトへの影響を取り込める手法を使うことでより計算の結果が物理的に正しいことになることが期待される。ただし、計算時間はかなり増加するようである。ソースはMustと呼ばれるKKR法の新しいパッケージのYouTubeチャンネルに上がっている、クラスター計算でAg-Pdの残留抵抗をCPAで計算した結果を説明した動画)

ポテンシャルを空間分割する方法には主に以下の2通りある。

- マフィンティン近似(MTA)
- フルポテンシャル法(FP)

前者は原子の周りに球対称なマフィンティンポテンシャルを配置する方法であり、異なる原子のポテンシャルは重ならないようにする。このときポテンシャルが存在しない領域が現れる。この領域をinterstitial regionと呼ぶ。マフィンティンポテンシャルには球対称性を課す。マフィンティンポテンシャルの半径はマフィンティン半径と呼ばれ、KKR法に基づく第一原理計算パッケージの１つであるAkai-KKRなどではマフィンティン半径は基本的にUserが設定する。

一方で後者は各原子のウィグナーザイツ胞で空間を区切りその中に各原子のポテンシャルがあると考える。このときポテンシャルは(おそらく通常は)球対称ではなく、またマフィンティン近似において存在したinterstitial regionについては考えない。(また、マフィンティン球をその原子のウィグナーザイツ胞と同じ体積にとり、マフィンティン近似におけるinterstitial regionを無視するAtomic Sphere Aproximation(ASA)等もあるが今は考えない。)
２つの近似を比較すると前者はポテンシャルに中心対称性があるため異なる角運動量の混成が起こらず計算が簡単になる。一方で後者は前者よりも一般的な(おそらくどのような系でもなりたつ)状況を反映するため、より精度を高く計算することができると考えられる。以下ではマフィンティン近似を行った系でシュレディンガー方程式に対応するグリーン関数を計算する方法について説明する。しかし、その計算にはSPOが必要なのでまずその説明を行う。
(著者の勉強不足でフルポテンシャル法やDirac方程式に関するグリーン関数の計算方法の詳細を知らないためである。しかし、境界条件などの考え方は境界の形は違えど本質的に同じではないかと考えている。)

## SPOの計算
各サイトにマフィンティンポテンシャルが置かれている状況における系のハミルトニアンは
```math
H = H_0 + V = H_0 + \sum_{n}{V_n}
```
とかける。ここで$`H_0`$は自由電子のハミルトニアンを表す。この時系のグリーン関数は
代数関係$`(A - B)^{-1} = A^{-1} +  A^{-1} B (A - B)^{-1}`$により、
```math
G(z) = (z - H_0 - \sum_{n}{V_n})^{-1} = G_0(z) + G_0(z) \sum_{n}{V_n} G(z) 
```
を満たす。ただし、$`G_0(z) = (z - H_0)^{-1}`$である。
ここで最右辺に現れるグリーン関数に、最右辺全体を逐次代入していくと
```math
\begin{split}
G(z) &= G_0 (z) + G_0 (z) T(z) G_0 (z) \\
T(z) &= V +  V G_0 (z) V + V G_0(z) V G_0(z) V + \cdots  = V\frac{1}{1 - G_0(z) V} = \frac{1}{1 - V G_0(z)}V 
\end{split}
```
を示すことができる。
ここまでは通常の散乱理論と同じである。

ここでT行列をそれぞれのサイトのポテンシャルについて展開すると
```math
\begin{split}
T(z) &= \sum_{n}{t_n(z)} + \sum_{n,m\neq n}{t_n(z) t_m(z)} + \sum_{n,m\neq n, k\neq m}{t_n(z) t_m(z) t_k(z)} + \cdots \\
t_n(z) &= V_n (1 - G_0(z)V_n)^{-1} \\
\end{split}
```
であることが示せる。$`t_n(z)`$は$`V_n`$による連続する散乱の効果を全て集めた量であるので、$`T(z)`$の右辺第二項以降の和は連続するサイトに関し同じサイトを取らないことで同じ項の二重数えを防いでいる。
(この補正がCPAのダイアグラム計算では重要になる。See H.Yonezawa's Textbook)

この分解に基づいてT行列をiサイトから散乱が始まり、jサイトで散乱が終わる項に分解できる。つまり、
```math
\begin{split}
T(z) &= \sum_{i,j}{\tau_{i,j}(z)} \\
\tau_{i,j}(z) &= t_i (z) \delta_{i,j} + t_i (z) \sum_{j}{(1-\delta_{i,j})G_0 (z) \tau_{i,j}(z)} \\
\end{split}
```
であることが示される。ただし、$`(1-\delta_{i,j})`$の因子は連続するサイトの和の制限を守るために導入した。

この$`\tau_{i,j}(z)`$がSPOである。
サイト$`i,j`$を行列の足として考え、SPOを行列表示したものを$`[\tau(z)]_{i,j} = \tau_{i,j}(z)`$と定義する。このとき$`\tau(z)`$に関する行列の方程式

が成り立つ。この形式解は

KKR法ではグリーン関数の実空間表示を用いて行列表示することが可能である。
## グリーン関数の計算方法について
ポテンシャルの配置されている領域内とinterstitial region内でグリーン関数はそれぞれ異なる形のシュレディンガー方程式に従っている。このとき、微分方程式の知識を思い出すと、グリーン関数は各領域のシュレディンガー方程式の一般解で展開できることがわかる。気を付けるべきはポテンシャルの境界(MTAではマフィンティン球面)における境界条件を正確に満たさなければならないことである。また、$`\vec{r} = \vec{r}'`$が特異点になるのでその点における境界条件もきちんと考えなくてはならない。

境界を除いた各領域においてグリーン関数の満たす微分方程式を書いてみる。
境界内部では
$$a $$
境界外部では

を満たす。学部生のときに使っていた教科書を見ると、外部における微分方程式の一般解は球ベッセル関数×球面調和関数と球ノイマン関数×球面調和関数、あるいは球ベッセル関数×球面調和関数と球ハンケル関数×球面調和関数の線形の組み合わせでかけることがわかる。じつは後者の方が球ベッセル関数が原点で正則であり、球ハンケル関数が無限遠で正則であるため原点と無限遠において正則な波動関数となるため、物理的に都合のよい展開である。

(以下のことを書く。外部の球ハンケル、球ベッセルの展開係数は境界条件を通じ内部のポテンシャルの情報を含んでいることについて、例えばPhase shiftについて書く)

## 不純物計算
グリーン関数の表式がもとまったが、以下では不純物が入った時のグリーン関数とSPOの変化を求めたい。上でも述べた通りCPAやリヒテンシュタインの理論ではこれらの量が必要になるのでこの計算は大変重要である。
KKR-GF法で必要となるのは通常1つまたは2つのサイトに不純物が入った状況である。ここで不純物とは原子の置換だけではなく磁気モーメントや原子位置の基準値からのずれでも良い。

## LMTO法との関連について
最後にLMTO法との関連について触れておきたい。
実はKKR法とLMTO法には密接な関連がある。(See Skliver or I.Turek)
LMTOはKKRをエネルギー線形化した方法としてO.K. Andersenによって提唱された。(O.K. Andersenは同じ論文でAugumented Plane Wave methodを線形化する方法も示している。)

逆にLMTO法からKKRに逆輸入されたものもある。それは構造定数のスクリーニングの方法である。(See J. Phys.: Condens. Matter 14 (2002) 2799–2823)
O.K. Andersenらのグループは各サイトに配置するLMTO基底にスクリーニングパラメータalphaに依存する"変形"を行うことで変形された軌道の構造定数が局在化することを示した。
この方法をKKRに取り入れたのがScreened-KKR法である。

(この方法によってクラスター計算を行う際のハミルトニアンをサイト表示した際にブロック対角のような疎行列になることから逆行列の計算が簡単になる。より詳しく述べると通常の逆行列演算がサイト数Nに対するオーダーがO(N^3)になるのに対し、Screend-KKR法ではO(N)にすることができる。これがオーダーN法である。)
