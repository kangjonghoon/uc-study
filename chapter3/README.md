3章 最も単純なコンピュータ
------------
## この章の概要
有限オートマトンを応用し、 Rubyで基本的な正規表現エンジンを作成する

## 3.1 決定性有限オートマトン(DFA)
### 有限オートマトンとは
状態、規則、入出力を持ち、特定の入力シーケンスを受理もしくは拒否する機械。  
理解しやすさ、推測しやすさ、ハードウェアやソフトウェアでの実装のしやすさが特徴のシンプルなモデル。

[有限オートマトン - Wikipedia](http://ja.wikipedia.org/wiki/%E6%9C%89%E9%99%90%E3%82%AA%E3%83%BC%E3%83%88%E3%83%9E%E3%83%88%E3%83%B3)

### DFA
このオートマトンは特徴として、決定的(deterministic)であることがあげられる。  
決定的であることとは、矛盾がなく、省略がないこと。  
つまり、状態と入力の組み合わせに対して、必ず規則を１つ保つ必要がある。

### Rubyによる実演
dfa.rbを参照

## 3.2 非決定性有限オートマトン(NFA)
### NFA
DFAとは異なり、非決定的(nondeterministic)であることが特徴である。  
非決定的とは、確実性ではなく可能性を扱うこと。  
何が起こるかではなく、何が起こりうるかという観点で結果を求める。

### Rubyによる実演
nfa.rbを参照

### 自由移動
何も入力を読まずに自発的に従うことができる規則。  
例えば、２つのグループから自発的に選択することができる。 
※図3-8

### Rubyによる実演
nfa.rbを参照

## 3.3 正規表現
### 正規表現とは
みなさんご存知だと思うのであえて説明は省く。  
ちなみに、この章では正規表現を常に文字列全体にマッチするものとみなす。  

### 構文
２章のSIMPLE式と同様、抽象構文木で表現する。

### Rubyによる実
reg_exp.rbを参照

### 意味論
構文をNFAに変換していく
* 空 -> ''
* 文字列 -> 'ab'
* リテラル -> 'a'
* グループ -> ()
* 繰り返し -> *

### Rubyによる実演
reg_exp.rbを参照

### パース
2章のTreetop文法を参考に、パターン構文のパーサーを実装する。

### Rubyによる実演
pattern.rbを参照

## 3.4 等価性
NFAからパターンを抽出し、DFAに変換する。  
DFAにすることで、NFAよりもシンプルにシミュレートしやすいものにする。  

### Rubyによる実演
nfa.rbを参照
