#### Gotanda.rb#49 "Ruby3.0" @オンライン

- - -

## Ruby 3.0 の1行 in と右代入

---

#### 自己紹介
- - -

* 名前：osyo
* Twitter : [@pink_bangbi](https://twitter.com/pink_bangbi)
* github  : [osyo-manga](https://github.com/osyo-manga)
* ブログ  : [Secret Garden(Instrumental)](http://secret-garden.hatenablog.com)
* 趣味で Ruby にパッチを投げたり気になった bugs.ruby のチケットをブログにまとめたりしている                     <!-- .element: class="fragment" -->
* Kaigi on Rails                 <!-- .element: class="fragment" -->
    * [ActiveRecord の歩み方](https://speakerdeck.com/osyo/activerecord-falsebu-mifang)
* 銀座Rails                   <!-- .element: class="fragment" -->
    * [これからの Ruby と今の Ruby について](https://speakerdeck.com/osyo/korekarafalse-ruby-tojin-false-ruby-nituite)
    * [12月25日にリリースされる Ruby 3.0 に備えよう！](https://speakerdeck.com/osyo/12yue-25ri-niririsusareru-ruby-3-dot-0-nibei-eyou)
* Burikaigi2021                 <!-- .element: class="fragment" -->
    * [Ruby 2.0 から Ruby 3.0 を駆け足で振り返る](https://speakerdeck.com/osyo/ruby-2-dot-0-kara-ruby-3-dot-0-woqu-kezu-dezhen-rifan-ru)

---

## 今日話すこと
## Ruby 3.0 の1行 in と右代入

---

### その前にパターンマッチについて

---

#### パターンマッチ
- - -

* Ruby 2.7 で実験的に入って 3.0 で正式に導入された新しい構文        <!-- .element: class="fragment" -->
    * case ~ when ~ end のように case ~ in ~ end という書き方をする
    * in に case の値にマッチするパターンを記述する
* in [条件1, 条件2,...] みたいに配列の各要素に対して条件を記述できる    <!-- .element: class="fragment" -->
    * [条件1 => 変数名] でその要素を変数に代入できる

```ruby
# case に要素を渡す
case [1,  "homu", 14]
# 配列の各要素に対する条件を記述する
in [Integer, String => name, Integer => age]
  { name: name, age: age }
in [Integer, Integer => age, String => name]
  { name: name, age: age }
in []
  []
# どのパターンにもマッチしなかったら else 節にくる
# else 節がない場合は例外が発生する
else
  {}
end
```
<!-- .element: class="fragment" -->

>>>

#### パターンマッチ 続き
- - -

* in { キー: 条件 } で Hash のキーの要素に対して条件を記述できる           <!-- .element: class="fragment" -->
* in { キー: 条件 => 変数 } で変数に代入できる           <!-- .element: class="fragment" -->
  * in { キー: } のように条件を省略するとキー名で変数が参照できる           <!-- .element: class="fragment" -->

```ruby
def check(user)
  case user
  # age が 20未満
  in { name:, age: (..20) }
    "#{name}は魔法少女です"
  # age が 20以上
  in { name:, age: (20..) }
    "#{name}は魔法少女ではありません"
  # それ以外
  else
    "わからない"
  end
end

p check({ name: "ほむ", age: 14 })  # => "ほむは魔法少女です"
p check({ name: "マミ", age: 30 })  # => "マミは魔法少女ではありません"
p check(["まどか", 14])             # => "わからない"
```
<!-- .element: class="fragment" -->

---

#### 1行 in (実験的)
- - -

* パターンマッチを 1行で記述できる構文             <!-- .element: class="fragment" -->
    * 値 in パターン という書き方をする
* これは Ruby 3.0 では実験的な機能になる         <!-- .element: class="fragment" -->
    * 使用すると実験的な警告が出るので注意
    * warning: One-line pattern matching is experimental ってでるよ！
* Ruby 2.7 でも使用できるが挙動がちょっと違うので注意！！！         <!-- .element: class="fragment" -->

```ruby
# これは普通のパターンマッチ構文
case user
in { name: String, age: (..20) }
end
```
<!-- .element: class="fragment" -->

```ruby
# 上のパターンマッチが 1行で記述できる
# マッチしてたら true を返し、そうでなければ false を返す
user in { name: String, age: (..20) }
```
<!-- .element: class="fragment" -->

>>>

#### 1行 in (実験的) 続き
- - -

* 1行 in は true / false を返すので if 文などの条件式として利用できる     <!-- .element: class="fragment" -->

```ruby
user = { name: "mami", age: 30 }

# 条件分岐をより細かく記述できる
if user in { name: String, age: (..20) }
  puts "OK"
else
  puts "NG"
end
```
<!-- .element: class="fragment" -->

```ruby
users = [
  { name: "homu", age: 14 },
  { name: "mami", age: 15 },
  { name: "mado", age: 14 }
]

pp users.select {
  # こんな感じで _1 と併用するとより簡潔に要素の絞り込みができる
  _1 in { name: /^m/, age: (..15) }
}
# => [{:name=>"mado", :age=>14}]
```
<!-- .element: class="fragment" -->

---

#### 右代入 (一部実験的)
- - -

* 左辺の値を右辺の変数に代入する構文   <!-- .element: class="fragment" -->
* コードは通常左から右に向かって記述するが、その流れのまま代入式を書くことができるので気持ちよくコードがかける   <!-- .element: class="fragment" -->
* ローカル変数以外のインスタンス変数やグローバル変数などには代入できないので注意する   <!-- .element: class="fragment" -->


```ruby
# 左辺の値を右辺の変数に代入する
42 => value

# これは func({ 42 => a }) と解釈されるので注意
func 42 => a
```

<!-- .element: class="fragment" -->


```ruby
# 左からコードを書いてそのまま変数に代入できる
(1..10).select { _1.even? }.map { _1 + _1 } => result
pp result
# => [4, 8, 12, 16, 20]
```
<!-- .element: class="fragment" -->

```ruby
# ちなみにインスタンス変数やグローバル変数には代入できないので注意
# error: syntax error, unexpected instance variable
1 => @value
2 => $value
```
<!-- .element: class="fragment" -->

>>>

#### 右代入 (一部実験的) 続き
- - -

* この右代入はパターンマッチを利用している               <!-- .element: class="fragment" -->
    * なので Hash の要素をの中身を直接受け取ることができる
    * この書き方は Ruby 3.0 では実験的な警告が出るので注意
* また 1行 in とは異なりマッチしなかった場合は例外が発生する     <!-- .element: class="fragment" -->

```ruby
user = { name: "mami", age: 15 }

# パターンマッチのように特定のキーの要素を束縛できる
user => { name:, age: }
pp name  # => "mami"
pp age   # => 15
```
<!-- .element: class="fragment" -->

```ruby
# もっと厳密にパターンを書くこともできる
user => { name: String => name, age: (..20) => age }
pp name
pp age
```
<!-- .element: class="fragment" -->

```ruby
# in とは違いパターンにマッチしなかったら例外が発生する
user => { name: Integer }
# => raise NoMatchingPatternError
```
<!-- .element: class="fragment" -->

---

### 1行 in と右代入の挙動まとめ

---

#### 1行 in と右代入の挙動まとめ
- - -

* 警告の違い             <!-- .element: class="fragment" -->

```ruby
# warning: One-line pattern matching is experimental, ~
42 in value
# warning: One-line pattern matching is experimental, ~
[42] in [value]

# no-warning
42 => value
# warning: One-line pattern matching is experimental, ~
[42] => [value]
```
<!-- .element: class="fragment" -->

* マッチしなかったときの挙動の違い            <!-- .element: class="fragment" -->

```ruby
[42] in [String]  # => false
[42] => [String]  # => raise NoMatchingPatternError
```
<!-- .element: class="fragment" -->

---

## おまけ
## パターンマッチを拡張する

---

#### 任意のクラスでパターンマッチできる
- - -

* #deconstruct_keys を定義することでパターンマッチを拡張できる
    * 配列のパターンマッチは #deconstruct を定義する

```ruby
class Time
  # in に書いた Hash のキーを受け取る
  # 返した Hash でパターンマッチを行う
  def deconstruct_keys(keys)
    { year: year, month: month, day: day }
  end

  def to_four_seasons
    case self
    in { month: (3..5) }  then "spring"
    in { month: (6..8) }  then "summer"
    in { month: (9..11) } then "autumn"
    in { month: (1..2) } | { month: 12 } then "winter"
    end
  end
end
p Time.now.to_four_seasons
# => "winter"
```

---

#### こういうの毎回書くのめんどくさいですよね？                <!-- .element: class="fragment" -->
### 便利 gem をつくりました                <!-- .element: class="fragment" -->

---

#### [pattern_matchable](https://github.com/osyo-manga/gem-pattern_matchable)
- - -

* メソッド名とそのメソッドの戻り値とパターンマッチを行う gem
    * include PatternMatchable すると #deconstruct_keys が定義される

```ruby
require "pattern_matchable"

class Time
  # メソッド名とその戻り値でマッチさせる #deconstruct_keys が生える
  include PatternMatchable

  def to_four_seasons
    case self
    in { month: (3..5) }  then "spring"
    in { month: (6..8) }  then "summer"
    in { month: (9..11) } then "autumn"
    in { month: (1..2) } | { month: 12 } then "winter"
    end
  end
end

p Time.now.to_four_seasons
# => "autumn"
```

---

### 問題点
### オープンクラスに直越 include したくないですよね？     <!-- .element: class="fragment" -->

---

## 皆さん Refinements って
## 知ってますか？？？？

---

#### using PatternMatchable で拡張する
- - -

* using PatternMatchable することで #deconstruct_keys が反映される

```ruby
require "pattern_matchable"

using PatternMatchable

class Time
  def to_four_seasons
    case self
    in { month: (3..5) }  then "spring"
    in { month: (6..8) }  then "summer"
    in { month: (9..11) } then "autumn"
    in { month: (1..2) } | { month: 12 } then "winter"
    end
  end
end
p Time.now.to_four_seasons  # => "autumn"

# もちろん右代入でも使えるよ！！
["homu", "mami", "mado"] => { first:, last:, size: }
p [first, last, size]
# => ["homu", "mado", 3]
```

---

#### 特定のクラスのみ拡張もできる
- - -

* using PatternMatchable Time すると任意のクラスでのみ反映される

```ruby
require "pattern_matchable"

class Time
  # Time クラスのみ拡張する
  using PatternMatchable Time

  def to_four_seasons
    case self
    in { month: (3..5) }  then "spring"
    in { month: (6..8) }  then "summer"
    in { month: (9..11) } then "autumn"
    in { month: (1..2) } | { month: 12 } then "winter"
    end
  end
end
p Time.now.to_four_seasons  # => "autumn"

# Array は拡張されない
# error: ["homu", "mami", "mado"] (NoMatchingPatternError)
["homu", "mami", "mado"] => { first:, last:, size: }
```

---

## まとめ

---

#### まとめ
- - -

* 1行 in を使うことで複雑な条件式を記述することができる           <!-- .element: class="fragment" -->
* 右代入は単純な代入式からパターンマッチを利用した代入を行うことができる           <!-- .element: class="fragment" -->
* ただし、パターンマッチを利用することでパターンマッチの制限を受けてしまう           <!-- .element: class="fragment" -->
    * インスタンス変数やグローバル変数に代入できないなど…
* パターンマッチめっちゃ便利           <!-- .element: class="fragment" -->
* 更に gem-pattern_matchable を使うと手軽にパターンマッチできてめっちゃ便利        <!-- .element: class="fragment" -->
* Refinements 便利なのでみんなどんどん使おう         <!-- .element: class="fragment" -->
* はよ Ruby 3.0 使いたい           <!-- .element: class="fragment" -->


---


### ご清聴
### ありがとうございました

