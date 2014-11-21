# 第4章 能力を高める
本家ソースコードサンプル：https://github.com/tomstuart/computationbook/tree/master/programming_with_nothing

## 用語
### 本章で出てくる言葉

## 目的
* これまでの章で学んできたテクニックを活用しながらパーサ、抽象構文、操作的意味論を揃えた特立した言語として、ラムダ計算を実装

## 解説
irbをもとに行います

```ruby
-> x { -> y { x.call(y) } }
-> x { x + 2 }.call(1)
-> x, y {
  x + y
}.call(3, 4)
-> x {
  -> y {
    x + y
  }
}.call(3).call(4)
```


## 6.1 ラムダ計算をまねる

以下の機能のみを使用します

変数の参照
procの生成
procの呼び出し

つまり
```ruby
-> x { -> y { x.call(y) } }
```

### 6.1.1　Procの利用

#### 6.1.1.1　配管
procは値を動かすための配管のようなもの
procを呼び出すと起こること
```ruby
-> x { x + 2 }.call(1)
```


#### 6.1.1.2 引数
```ruby
-> x, y {
  x + y
}.call(3, 4)
-> x {
  -> y {
    x + y
  }
```
引数１つに制限して話を進めます


#### 6.1.1.3 等価性
同じ引数で同じ結果を生成するのであれば２つのprocは交換可能だといえる
外から見える振る舞いに基づいて２つを等価にあつかうという考えは外延的等価性と呼ばれる
```ruby
p = -> n { n * 2 }
q = -> x { p.call(x) }
p.call(5)
q.call(5)
```

#### 6.1.1.4 構文
構文の生成は{}を呼び出しには[]
```ruby
-> x { x + 5 }[6]
```

### 6.1.2 問題
FizzBuzzプログラムを書きます
普通に掛けば
```ruby
(1..100).each do |n|
  if (n % 15).zero?
    puts 'FizzBuzz'
  elsif (n % 3).zero?
    puts 'Fizz'
  elsif (n % 5).zero?
    puts 'Buzz'
  else
    puts n.to_s
  end
end
```
pushを文字列の配列に置き換える
```ruby
(1..100).map do |n|
  if (n % 15).zero?
    'FizzBuzz'
  elsif (n % 3).zero?
    'Fizz'
  elsif (n % 5).zero?
    'Buzz'
  else
    n.to_s
  end
end
```

### 6.1.3 数
あるアクションを繰り返し、反復し数を特徴づけることが出来る
0とはアクションを実行してない
1とはアクションを実行した
2はアクションを実行後再度アクションを実行したになる

唯一のアクションはprocの作成と呼び出し

```ruby
def one(proc, x)
  proc[x]
end

def two(proc, x)
  proc[proc[x]]
end

def three(proc, x)
  proc[proc[proc[x]]]
end

def zero(proc, x)
  x
end
```

```ruby
def to_integer(proc)
  proc[->n {n + 1}[0]]
end
 ```

```ruby
to_integer(ZERO)
to_integer(THREE)
to_integer(FIVE)
to_integer(FIFTEEN)
to_integer(HUNDRED)
 ```
### 6.1.4 ブール値
ブールがやっていること２つの選択肢から選べるようにすること
```ruby
success = true
if success then 'happy' else 'sad' end
success = false
if success then 'happy' else 'sad' end

def true(x, y)
  x
end

def false(x, y)
  y
end

```

```ruby
def if(proc, x, y)
  proc[x][y]
end

IF =
  -> b {
    -> x {
      -> y {
        b[x][y]
      }
    }
  }
IF[TRUE]['happy']['sad']
IF[FALSE]['happy']['sad']

-> y {
  b[x][y]
}
IF =
  -> b {
    -> x {
      b[x]
    }
  }
-> x {
  b[x]
}
```

### 6.1.5 述語
```ruby
def zero?(n)
  if n == 0
    true
  else
    false
  end
end

def zero?(proc)
  proc[-> x { FALSE }][TRUE]
end
```

### 6.1.6 ペア
```ruby
-> f { f[x][y] }
my_pair = PAIR[THREE][FIVE]
to_integer(LEFT[my_pair])
to_integer(RIGHT[my_pair])

```

### 6.1.7 数値演算
```ruby
def slide(pair)
  [pair.last, pair.last + 1]
end

slide([3, 4])
slide([8, 9])
slide([-1, 0])
slide(slide([-1, 0]))
slide(slide(slide([-1, 0])))
slide(slide(slide(slide([-1, 0]))))
slide([0, 0])
slide(slide([0, 0]))
slide(slide(slide([0, 0])))
slide(slide(slide(slide([0, 0]))))
to_integer(DECREMENT[FIVE])
to_integer(DECREMENT[FIFTEEN])
to_integer(DECREMENT[HUNDRED])
to_integer(DECREMENT[ZERO])

def mod(m, n)
  if n <= m
    mod(m - n, n)
  else
    m
  end
end

def less_or_equal?(m, n)
  m - n <= 0
end

to_integer(SUBTRACT[FIVE][THREE])
to_integer(SUBTRACT[THREE][FIVE])

def less_or_equal?(m, n)
  IS_ZERO[SUBTRACT[m][n]]
end

to_boolean(IS_LESS_OR_EQUAL[ONE][TWO])
to_boolean(IS_LESS_OR_EQUAL[TWO][TWO])
to_boolean(IS_LESS_OR_EQUAL[THREE][TWO])

def mod(m, n)
  IF[IS_LESS_OR_EQUAL[n][m]][
    mod(SUBTRACT[m][n], n)
  ][
    m
  ]
end

MOD =
  -> m { -> n {
    IF[IS_LESS_OR_EQUAL[n][m]][
      MOD[SUBTRACT[m][n]][n]
    ][
      m
    ]
  } }
to_integer(MOD[THREE][TWO])
MOD =
  -> m { -> n {
    IF[IS_LESS_OR_EQUAL[n][m]][
      MOD[SUBTRACT[m][n]][n]
    ][
      m
    ]
  } }
MOD =
  -> m { -> n {
    IF[IS_LESS_OR_EQUAL[n][m]][
      -> x {
        MOD[SUBTRACT[m][n]][n][x]
      }
    ][
      m
    ]
  } }
to_integer(MOD[THREE][TWO])
to_integer(MOD[
     POWER[THREE][THREE]
   ][
     ADD[THREE][TWO]
   ])
to_integer(MOD[THREE][TWO])
to_integer(MOD[
  POWER[THREE][THREE]
][
  ADD[THREE][TWO]
])

```

### 6.1.8 リスト
```ruby
my_list =
  UNSHIFT[
    UNSHIFT[
      UNSHIFT[EMPTY][THREE]
    ][TWO]
  ][ONE]
to_integer(FIRST[my_list])
to_integer(FIRST[REST[my_list]])
to_integer(FIRST[REST[REST[my_list]]])
to_boolean(IS_EMPTY[my_list])
to_boolean(IS_EMPTY[EMPTY])

to_array(my_list)
to_array(my_list).map { |p| to_integer(p) }

def range(m, n)
  if m <= n
    range(m + 1, n).unshift(m)
  else
    []
  end
end

my_range = RANGE[ONE][FIVE]
to_array(my_range).map { |p| to_integer(p) }
to_integer(FOLD[RANGE[ONE][FIVE]][ZERO][ADD])
to_integer(FOLD[RANGE[ONE][FIVE]][ONE][MULTIPLY])
my_list = MAP[RANGE[ONE][FIVE]][INCREMENT]
to_array(my_list).map { |p| to_integer(p) }

to_char(ZED)
to_string(FIZZBUZZ)

def to_digits(n)
  previous_digits =
    if n < 10
      []
    else
      to_digits(n / 10)
    end

  previous_digits.push(n % 10)
end

to_array(TO_DIGITS[FIVE]).map { |p| to_integer(p) }
to_array(TO_DIGITS[POWER[FIVE][THREE]]).map { |p| to_integer(p) }
to_string(TO_DIGITS[FIVE])
to_string(TO_DIGITS[POWER[FIVE][THREE]])
MAP[RANGE[ONE][HUNDRED]][-> n {
  IF[IS_ZERO[MOD[n][FIFTEEN]]][
    FIZZBUZZ
  ][IF[IS_ZERO[MOD[n][THREE]]][
    FIZZ
  ][IF[IS_ZERO[MOD[n][FIVE]]][
    BUZZ
  ][
    TO_DIGITS[n]
  ]]]
}]
to_array(solution).each do |p|
  puts to_string(p)
end; nil
ZEROS = Z[-> f { UNSHIFT[f][ZERO] }]
to_integer(FIRST[ZEROS])
to_integer(FIRST[REST[ZEROS]])
to_integer(FIRST[REST[REST[REST[REST[REST[ZEROS]]]]]])

def to_array(l, count = nil)
  array = []

  until to_boolean(IS_EMPTY[l]) || count == 0
    array.push(FIRST[l])
    l = REST[l]
    count = count - 1 unless count.nil?
  end

  array
end

to_array(ZEROS, 5).map { |p| to_integer(p) }
to_array(ZEROS, 10).map { |p| to_integer(p) }
to_array(ZEROS, 20).map { |p| to_integer(p) }
UPWARDS_OF = Z[-> f { -> n { UNSHIFT[-> x { f[INCREMENT[n]][x] }][n] } }]
to_array(UPWARDS_OF[ZERO], 5).map { |p| to_integer(p) }
to_array(UPWARDS_OF[FIFTEEN], 20).map { |p| to_integer(p) }
```

### 6.1.9 文字列

### 6.1.10 解答

### 6.1.11 高度なプログラミングテクニック

#### 6.1.11.1 無限ストリーム

#### 6.1.11.2 再帰の回避

## 6.2 ラムダ計算の実装

### 6.2.1 構文

### 6.2.2 意味論

#### 変数の置き換え

#### 関数呼び出し

#### 式の簡約

### 6.2.3 パース
