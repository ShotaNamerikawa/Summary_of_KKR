# Summary_of_KKR
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
KKR-Green関数法は多重散乱理論に基づき、固体あるいは周期性のない原子や分子のクラスターの系のシュレディンガーあるいはディラック方程式に対するグリーン関数を実空間において求める手法である。
(もともと、Korringは波動関数を求めるための手法としてKKRを提案した。これがGreen関数を求める手法に発展したのはKohn,Rostkerの論文とHam, Segallの論文(PhysRev.124.1786)の寄与が大きいと思われる。)多重散乱理論ではポテンシャルが空間的に分割された描像を考えるのでいくつかのサイトを置換した状況を考えやすいなどのメリットがあり、CPAなどのSingle-site approximation(単一サイト近似)を用いた系の計算と非常に相性が良い。また、実空間のグリーン関数を求めることでその虚部のトレースをとると実空間における電荷密度を求められるため電荷密度の自己無撞着計算を行うDFTループを自然に組み込めるというメリットがある。以下ではKKR法で仮定している系の物理的な状況に触れながら、KKR法におけるグリーン関数の計算やそこに現れるT行列の散乱前のサイトと散乱後のサイトを指定したときの行列表示にあたるScattering Path Operator(SPO)について解説したい。後者はとくに"不純物"を入れたときの系のグリーン関数やエネルギー変化を考える、CPA法やリヒテンシュタインの方法において非常に重要な量である。

以下では固体の場合に話を限定する。

## KKR-Green関数法におけるグリーン関数やSPOの計算
KKR-Green関数法とは実空間における以下の微分方程式に従うグリーン関数を多重散乱理論に従い求める手法である。
$$( z - H ) G(\vec{r},\vec{r}', z) = \delta (\vec{r} - \vec{r'})$$
ここで$`H`$は一体のハミルトニアン(ただし、Hartree-FockやKohn-Sham方程式のハミルトニアンを考えることにより部分的に相関の効果を入れることを可能)である。 
このグリーン関数は1粒子グリーン関数であり、多くの1体演算子あるいは厳密な応答関数に現れる２粒子グリーン関数を1粒子グリーン関数で近似した量を取り出せる、いわば物理量の母関数のような重要な量である。(See AGD or Fetter-Walecka or M.Ogata)
ここで注意したいのがKKR法ではポテンシャルについて空間分割ができると`仮定`することである。これは多重散乱理論を展開するために必要な要請である。また、この空間の分割は通常は原子を取り囲むマフィンティン球や多面体を作ることによって行われる。そしてその中のポテンシャルは多面体の中にある原子"のみ"によって決定されると仮定される。これにより１つの原子が置換された状況ではその周りのポテンシャルを変化するという描像が成り立ち、これはサイトの変化を考えるCPAやリヒテンシュタインの方法などの理論の計算を用意にするのである。
(しかしながらこのポテンシャルと原子の１体１対応はあくまでも仮定でしかなく、実際の物理系においてはなりたつ保証はない。例えば、ある原子の影響がその原子まわりのポテンシャルだけでなく隣あるいはさらに遠くの原子のポテンシャルを変化させる場合が存在する。しかし、このような場合にはクラスター法などのあるサイトの変化がおよぼす周りのサイトへの影響を取り込める手法を使うことでより計算の結果が物理的に正しいことになることが期待される。ただし、計算時間はかなり増加するようである。ソースはMustと呼ばれるKKR法の新しいパッケージのYouTubeチャンネルに上がっている、クラスター計算でAg-Pdの残留抵抗をCPAで計算した結果を説明した動画)

ポテンシャルを空間分割する方法には主に以下の2通りある。
-マフィンティン近似(MTA)
-フルポテンシャル法(FP)
前者は原子の周りに球対称なマフィンティンポテンシャルを配置する方法であり、異なる原子のポテンシャルは重ならないようにする。このときポテンシャルが存在しない領域が現れる。この領域をinterstitial regionと呼ぶ。マフィンティンポテンシャルには球対称性を課す。マフィンティンポテンシャルの半径はマフィンティン半径と呼ばれ、KKR法に基づく第一原理計算パッケージの１つであるAkai-KKRなどではマフィンティン半径は基本的にUserが設定する。

一方で後者は各原子のウィグナーザイツ胞で空間を区切りその中に各原子のポテンシャルがあると考える。このときポテンシャルは(おそらく通常は)球対称ではなく、またマフィンティン近似において存在したinterstitial regionについては考えない。(また、マフィンティン球をその原子のウィグナーザイツ胞と同じ体積にとり、マフィンティン近似におけるinterstitial regionを無視するAtomic Sphere Aproximation(ASA)等もあるが今は考えない。)
２つの近似を比較すると前者はポテンシャルに中心対称性があるため異なる角運動量の混成が起こらず計算が簡単になる。一方で後者は前者よりも一般的な(おそらくどのような系でもなりたつ)状況を反映するため、より精度を高く計算することができると考えられる。以下ではマフィンティン近似を行った系でシュレディンガー方程式に対応するグリーン関数を計算する方法について説明する。しかし、その計算には散乱経路演算子(scattering path operator, SPO)が必要なのでまず、その説明をしたい。
(著者の勉強不足でフルポテンシャル法やDirac方程式に関するグリーン関数の計算方法の詳細を知らないためである。しかし、境界条件などの考え方は境界の形は違えど本質的に同じではないかと考えている。)

## SPOの計算


## グリーン関数の計算方法について


ポテンシャルを空間的に分割した状況において各ポテンシャルの配置されている領域内、あるいはinterstitial region内でグリーン関数はそれぞれ異なるシュレディンガー方程式に従っている。このとき、微分方程式の知識を思い出すと、グリーン関数は各領域のシュレディンガー方程式の一般解で展開できることがわかる。気を付けるべきはポテンシャルの境界における境界条件を正確に満たさなければならないことである。また、rにとってr'が特異点になるのでその点における境界条件もきちんと考えなくてはならない。シュレディンガー方程式を実空間で解く方法は量子力学の教科書では取り扱っているがディラックのブラケット表記に慣れてしまうとどうしても忘れてしまう方が多いと思われる。しかし、これを行っていたときのことを今一度思い起こしていただきたい。われわれは実空間における微分方程式を境界条件の下で解いていた。KKR法ではこの境界条件を(MTAの場合は)マフィンティン球面上で考えるのである。マフィンティン球の境界を除いた外部では
$$a $$
を考えるので自由電子の従うシュレディンガー方程式。学部生のときに使っていた教科書を見ると、このときの微分方程式の一般解は球ベッセル関数×球面調和関数と球ノイマン関数×球面調和関数、あるいは球ベッセル関数×球面調和関数と球ノイマン関数×球面調和関数の線形の組み合わせでかけることがわかる。じつは後者の方が球ベッセル関数が原点で正則であり、球ハンケル関数が無限遠で正則であるため原点と無限遠において正則な波動関数となるため、物理的に都合のよい展開である。
