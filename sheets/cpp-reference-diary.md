---
title: cppreference.com 日記
category: C++
layout: 2017/sheet
tags: [Featured]
updated: 2022-06-16
weight: -10
intro: cppreference.com日記
---

{: .-three-column}

### [関数Tryブロック](https://ja.cppreference.com/w/cpp/language/function-try-block)

以下のようなコードがかけちゃうらしい．  
マジか，tryそんなとこに書けたんか

```cpp
int main() try {}
catch (...){}
```


### [main関数](https://ja.cppreference.com/w/cpp/language/main_function)

いわゆるエントリポイント，`main`関数

- `main`関数は`int`型だが，return文はなくてもOK
- `auto main()`という書き方はダメらしい
- `return N`は`std::exit(N)`と同じ意味を持つ
- `main`関数が関数`try`ブロックになっていてもmain関数の外で定義された（しかし，`main`関数の終了と同時に破棄される）静的オブジェクトのデストラクタで投げられた例外はキャッチできない


### [std::min_element](https://ja.cppreference.com/w/cpp/algorithm/min_element), [std::max_element](https://ja.cppreference.com/w/cpp/algorithm/max_element)

- 最低限，探索範囲を[first, last)で指定できる．
- 比較には`<`演算子を使用（`max_element`は`>`演算子を使うわけではない）
- `<`演算子の代わりに比較関数を指定できる．
  - 比較関数は`cmp(new_element, best_element)`という形式で使われる
  - ⇒内容によって意図的に弾きたい場合は，第一引数の内容を参照して弾けば良い（min_elementならfalse,max_elementならtrueを返せば弾ける）
- 実行ポリシーを設定できるらしい（知らんかった)．指定箇所は探索範囲の前．
  - 詳しくは[実行ポリシー](https://ja.cppreference.com/w/cpp/algorithm/execution_policy_tag_t)参照とのこと

### [実行ポリシー(C++17)](https://ja.cppreference.com/w/cpp/algorithm/execution_policy_tag_t)

stlにはたくさんのアルゴリズム関数が存在するが，それらの第一引数にアルゴリズムの処理を実行する上での制約を「実行ポリシー」として与えることができるらしい．
「実行ポリシー」は以下のような種類が存在する


| 実行ポリシー        | 説明                |
|---------------|-------------------|
| `seq`       | 順番に処理を実行する必要がある．当然並列化も出来ない             |
| `par`        | マルチスレッド化による並列化を許可する            |
| `par_unseq` | マルチスレッド化・ベクトル化を許可する          |
| `unseq`       | ベクトル化を許可する（C++20） |
{: .-shortcuts}

今までのアルゴリズム関数は`seq`で実行されるのが当然だった．
プログラマにから与えられた関数オブジェクトの処理はどういう処理なのかをアルゴリズム関数は知らないのだからそりゃそうだ．
でも，そのプログラマが処理についてアルゴリズム関数にヒントを与えられるのがこの機能というわけだ．
もしかしなくても「OpenMPを使ったfor文の並列化は簡単でいいなぁ」と言っていた時代は終わったのかもしれない

```cpp
std::for_each(std::execution::par, a.begin(), b.end(), [&](int x) {
    std::cout << x << std::endl;
});
```

### [std::minmax_element](https://ja.cppreference.com/w/cpp/algorithm/minmax_element)

- コンテナの範囲を渡すと，最大値と最小値をペアにして返してくれる
- 例によって二項比較関数は`<`演算子相当のものを定義して渡せば良い
- C++17からは実行ポリシーも指定可能
- `std::make_pair(std::min_element(), std::max_element())`よりも効率が良い
- 一番大きい/小さい要素が複数ある場合，↑は最初の要素を返し，本アルゴリズムは最後の要素を返す

```cpp
const auto [min, max] = std::minmax_element(begin(v), end(v));
```

### [std::enable_shared_from_this](https://ja.cppreference.com/w/cpp/memory/enable_shared_from_this)

クラス内で`this`を`shared_ptr`にしたい場合が往々にしてある．（全て自分のコードの場合では設計が悪い場合が多いが，外部ライブラリを使用しているとそうするしか無い場合もありがち）
そういう時に，`std::shared_ptr<T>(this)`などとやってしまうと，`this`は2回以上破棄されてしまいやすい．

そんな時に使うのが，これ`std::enable_shared_from_this`. 使い方は`shared_ptr`化したいクラスで`std::enable_shared_from_this`をpublic継承し，`shared_from_this()`を呼ぶだけ．
しかし，**本機能を使っているクラスは必ずshared_ptrで管理されている必要がある**ので注意が必要．そうでない場合の動作は未定義．

```cpp
class A : public std::enable_shared_from_this {
A(){
  auto shared_ptr_this = shared_from_this();
}
};
```

### [std::future](https://ja.cppreference.com/w/cpp/thread/future)

非同期処理を行うためのクラス．このクラスでは今は無いが，「未来」に手に入るであろうデータを取り扱う．  
データが手に入ったかどうかは，`valid`関数で調べることができ，`get`関数で取り出せる．  
また，手に入るまで待機する`wait`関数も何種類か用意されている．

| 関数名       | 説明                |
|---------------|-------------------|
| `wait`       | 処理完了まで待つ             |
| `wait_for(<duration>)`        | 処理完了まで待つ（指定時間でタイムアウト）            |
| `wait_until(<time_point>)` | 処理完了まで待つ（指定時間になったらタイムアウト）          |
{: .-shortcuts}


### [std::promise](https://cpprefjp.github.io/reference/future/promise.html)

`std::future`に出来上がったデータを受け渡す役割を持ったクラス  
使い方は以下のような感じ
```cpp
std::promise<int> p;
std::future<int> f = p.get_future();

p.set_value(1);

std::cout << f.get() << std::endl;
```

`std::primise`は，データを渡す相手に「今はまだ空だけど，将来ここにデータを送るからね」と言って`std::future`を渡す．  
後日，データを見つけたstd::promise`は`set_value`関数でデータを送り，`std::future`側では`get`関数で受けとれる．

### [委譲コンストラクタ](https://cpprefjp.github.io/lang/cpp11/delegating_constructors.html)

C++03以前では，コンストラクタ内の
```cpp
class X {
public:
  X(int i) { std::cout << i << std::endl; }
  X() : X(42) {}
};
```