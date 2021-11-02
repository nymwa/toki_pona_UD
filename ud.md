# Toki Pona UD

## はじめに

困ったことに，世の中にはたくさんの言語があります．
異なる言語の間で，共通の規則を用いて依存構造のアノテーションを行うための枠組みとして，Universal Dependencyと呼ばれるものがあります．
また，その中で使われている品詞体系として，Universal POS tagsと呼ばれるものもあります．
nymwaさんは，トキポナ版のUniversal DependencyやUniversal POS tagsを設計しようと思い立ち，頑張っています．
今回のアドベントカレンダーでは，その中で得られた知見を共有します．

## Universal Dependency

Universal Dependencyは，さまざまな言語での言語横断的な構文の構造を設計するための試みです．よくUDと省略されます．
「いろんな言語の構文解析を同じ枠組みでやるぞー」という思想の人たちが，それを実現するために頑張って作っています．
UDが始まったのは2014年のことで，それから100以上の言語でUDが設計されています．
UDにもいろいろありますが，この記事で出てくるUDは，UD version 2.です．
詳しいことは，https://universaldependencies.org/ かあるいは，https://aclanthology.org/2020.lrec-1.497.pdf を見てくれもふ≡╹ω╹≡．

UDでは，Universal POS tagsとして，17種類の品詞タグセットを採用しています．
以下に，Universal POS tagsのすべてのタグを示します．


| タグ | 意味 |
| --- | --- |
| ADJ | 形容詞 |
| ADP | 接置詞 |
| ADV | 副詞 |
| AUX | auxiliary (助動詞など) |
| CCONJ | 等位接続詞 |
| DET | 限定詞 |
| INTJ | 間投詞 |
| NOUN | 名詞 |
| NUM | 数詞 |
| PART | 接辞，小辞 |
| PRON | 代名詞 |
| PROPN | 固有名詞 |
| PUNCT | 約物 |
| SCONJ | 従属接続詞 |
| SYM | 記号 |
| VERB | 動詞 |
| X | その他 |

トキポナでUDをするためには，まず，すべての単語がどのUniversal POS tagsのタグを取りうるかを考える必要があります．

Universal Dependencyの係り受けは，その"言語横断的"という特徴から，句構造（構文木とか）を考慮したものではなく，
すべての構文関係を，単語間の依存関係と係り受けタグで表現します．
UDの係り受けタグは，なんかよくわからんが[たくさんあり](https://universaldependencies.org/u/dep/index.html)ます．
これらのすべてをトキポナと関連付けようとするとしんどいので，以降は
[jan Nankoのトキポナ文法解説ツイート](https://twitter.com/leenamgwang/status/1409484535151042568)
の例文を用いて，トキポナUDについて説明していきます．

## pan li moku.

![pan li moku](./image/pan_li_moku.png)

"pan li moku."は，「パンは食べ物だ」という意味であり，pan, mokuは，NOUNです．
今回のトキポナUDでは，liは，PARTということにしました．これは異論もあるかもしれませんが，受け入れてください．
'.'は約物なので，PUNCTです．

係り受け解析では，ある語には別の語から矢印が飛んできます．
矢印の矢はず側，例えばこの図の「moku -> pan」のmoku側は，「panの主辞 (head)」と言います．
さらに，すべての語から主辞をたどって到達する語のことを，文の主辞 (root) と言います．この文では，mokuが文の主辞です．
文の主辞は必ず１つになります．
２つ以上ある場合は，それは，そもそも２つ以上の文ということになりますし，
主辞が０個という場合は，矢印の循環が起きるということになってしまいます．
係り受けの矢印は必ず循環しないように付ける必要があります．

「pan <- moku」の係り受けには，nsubjのタグが付いています．これが，係り受けタグです．
nsubjは主語を表します．

「li <- moku」の係り受けは，auxです．auxは，一般に述語に係ってその役割を決定付けます．
英語の助動詞がそれで，時制や態，法，証拠性などを表します．
ただ，UDでは，述語以外の要素に役割を付与するような語にも，auxの使用を許しています．
トキポナには，そのような語（後述する，eやpiなど）が複数あります．
今回の例においては，liは述語の役割を決定付けるものであり，auxとするのが良いように思われます．

「moku -> .」の係り受けは，punctです．これは，約物であることを表します．

## kili li suwi.

![kili li suwi](./image/kili_li_suwi.png)

"kili li suwi."は，「果物は甘い」という意味であり，suwiは，ADJになります．

kiliが主語，suwiが述語となり，係り受けは，"pan li moku."の場合と全く同様になります．

## jan li moli.

![jan li moli](./image/jan_li_moli.png)

"jan li moli."は，「人は死ぬ」という意味であり，moliは，VERBになります．

janが主語，moliが述語となり，係り受けは，また同様になります．

## kala li lon telo li pona moku.

![kala li lon telo li pona moku](./image/kala_li_lon_telo_li_pona_moku.png)

"kala li lon telo li pona moku."は，「魚は水中にいて，おいしい」という意味です．

主語はkalaですが，述語は"li lon telo"と"li pona moku"の２つあります．
この場合，１つ目の述語の主部"telo"と２つ目の述語の主部"pona"が「telo -> pona」のように結び付けられ，その係り受けタグは，conj:predとなります．
conjは本来，２つの要素が等位構造を持つ場合，その関係を示す働きがあります．
トキポナの場合，等位構造にも複数の種類があり，本来の等位接続詞を用いいる等位構造には，"conj:???"というふうに，なんらかの補足説明を付けるようにしました．
"conj:pred"のpredは，それが述語の等位関係，すなわち，"li ~~~ li ~~~ li ~~~"の関係にあることを意味します．

１つ目の述語，"li lon telo"は，前置詞"lon"からなる前置詞句です．
UDでは，前置詞句における主辞は前置詞ではなく，前置詞の後ろに来る名詞句となります．
そのため，「lon <- telo」と，この句の主辞はteloになります．
その係り受けタグは，格を意味する'case'になります．

２つ目の述語，"li pona moku"は，形容詞 'pona' 「良い」に，副詞 'moku' 「食べるために」が付き，「食べるために良い」-> 「おいしい」という意味です．
副詞の修飾は'advmod'タグで表されるため，「pona -> moku」の関係は，advmodです．

## mi pilin ike li moli.

![mi pilin ike li moli](./image/mi_pilin_ike_li_moli.png)

