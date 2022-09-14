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

### [委譲コンストラクタ(C++11)](https://cpprefjp.github.io/lang/cpp11/delegating_constructors.html)

C++11からの機能．複数のコンストラクタ内で共通の処理を初期化処理で行える．  
（コンストラクタの本体にメンバ関数を呼び出す方法もあるが，メンバ関数は初期化が完了した後にしか呼び出せない共通処理なので，ベタ書きしたり委譲コンストラクタを使う場合に比べてパフォーマンスで劣る）
```cpp
class X {
public:
  X(int i) { std::cout << i << std::endl; }
  X() : X(42) {}
};
```

### [初期化子リスト(C++11)](https://cpprefjp.github.io/lang/cpp11/initializer_lists.html)

以下のような，波括弧による初期化を可能とする'

```cpp
std::vector<int> v1 = {1, 2, 3};
std::vector<int> v2 {1, 2, 3};
```

初期化子リストを使うには，`std::initilizer_list`を引数とするコンストラクタが必要'

```cpp
class MyVector {
  std::vector<int> data_;
public:
  MyVector(std::initializer_list<int> init)
    : data_(init.begin(), init.end()) {}};
```

### [一様初期化(C++11)](https://cpprefjp.github.io/lang/cpp11/uniform_initialization.html)

`std::initilizer_list`以外のコンストラクタも波括弧を使った初期化ができる機能  
情報が足りていなかったりすると，`std::initializer_list`型として推論されてしまったりするので注意．

```cpp
struct X {
  X(int, double, std::string) {}
};
X createX(){return {1, 3.14, "hello"}; }
```

`std::initializer_list`のコンストラクタとそれ以外のコンストラクタの両方で受け取れるような波括弧リストを渡す時，前者が呼び出される．  
但し，空の初期化子リストを渡す場合はデフォルトコンストラクタが優先される．

### [インライン名前空間(C++11)](https://cpprefjp.github.io/lang/cpp11/inline_namespaces.html)

```cpp
inline namespace my_namespace{}
```

省略可能な名前空間を定義できる．  
上位の名前空間を`using`するだけで下位のinline名前空間の機能が使えるようになるので，本来なら複数の`using`文が必要な場合も，１つで事足りる．  

以下のように，複数バージョンの関数がそれぞれのバージョンの名前空間にある時，1つだけ`inline`名前空間で定義することでデフォルトのバージョンを表現することが出来る．  
また，デフォルトバージョンを変更する場合も`inline`名前空間を切り替えるだけで実現できる．  

```cpp
namespace api {
  inline namespace v1 {
    void f(){}
  }
  namespace v2 {
    void f(){}
  }
}
```


### [入れ子名前空間の定義(C++17)](https://cpprefjp.github.io/lang/cpp17/nested_namespace.html)

入れ子上になった名前空間を一度に定義することが可能になる

C++14まで
```cpp
namespace aa{
  namespace bb{
    void f(){}
  }
}
```

C++17から
```cpp
namespace aa::bb{
  void f(){}
}
```

### [入れ子名前空間でのインライン名前空間(C++20)](https://cpprefjp.github.io/lang/cpp20/nested_inline_mamespaces.html)

C++11からのインライン名前空間とC++17からの入れ子名前空間の併用は出来なかったが，C++20から出来るようになった

C++17
```cpp
namespace aa{
  inline namespace bb{
    void f(){}
  }
}
```
C++20
```cpp
namespace aa::inline bb{
  void f(){}
}
```

### [範囲for文(C++11)](https://cpprefjp.github.io/lang/cpp11/range_based_for.html)

for文を簡潔に書くことが出来る機能．
```cpp
for ( auto element : elements ) std::cout << element << std::endl;
```

↑は↓のように展開される（C++17以降や，配列を扱う時は展開され方が異なる）

```cpp
auto && __range = elements;
for (auto __begin = __range.begin(), __end = __range.end(); __begin != __end; ++__begin) {
  auto element = *__begin;
  std::cout << element << std::endl;
}
```
↑から分かるように，`begin()`,`end()`,`operator++()`, `operator*()`, `operator!=()`が適切に定義されていれば自作クラスでも使える．

注意点
- 要素をeraseしてはいけない(バグる)
- インデックスの取得とは相性が悪い（出来ないこともない：[別ページ](https://hansrobo.github.io/mycheatsheets/range-based-for#:~:text=%E7%AF%84%E5%9B%B2for%E6%96%87(range%2Dbased%20for)%E3%81%A7%E3%82%A4%E3%83%B3%E3%83%87%E3%83%83%E3%82%AF%E3%82%B9%E3%82%92%E4%BD%BF%E3%81%84%E3%81%9F%E3%81%84)参照）
- 範囲として渡した配列などが，範囲for文実行中に寿命が切れないように注意

### Coming Soon
- [範囲for文(C++11)](https://cpprefjp.github.io/lang/cpp11/range_based_for.html)
- [範囲forループの制限緩和(C++17)](https://cpprefjp.github.io/lang/cpp17/generalizing_the_range-based_for_loop.html)
- [範囲for文がカスタマイゼーションポイントを見つけるルールを緩和](https://cpprefjp.github.io/lang/cpp20/relaxing_the_range_for_loop_customization_point_finding_rules.html)
- [属性構文(C++11)](https://cpprefjp.github.io/lang/cpp11/attributes.html)
  - [deprecated(C++14)](https://cpprefjp.github.io/lang/cpp14/deprecated_attr.html)
  - [maybe_unused(C++17)](https://cpprefjp.github.io/lang/cpp17/maybe_unused.html)
  - [nodicard(C++17)](https://cpprefjp.github.io/lang/cpp17/nodiscard.html)
  - [fallthrough(c++17)](https://cpprefjp.github.io/lang/cpp17/fallthrough.html)
  - [no_unique_adress(c++20)](https://cpprefjp.github.io/lang/cpp20/language_support_for_empty_objects.html)
  - [likely, unlikely (c++20)](https://cpprefjp.github.io/lang/cpp20/likely_and_unlikely_attributes.html)
  - [属性の名前空間予約(c++20)](https://cpprefjp.github.io/lang/cpp20/reserving_attribute_namespaces_for_future_use.html)
- [不明な属性を無視する(C++17)](https://cpprefjp.github.io/lang/cpp17/non_standard_attributes.html)
- [スレッドローカルストレージ(C++11)](https://cpprefjp.github.io/lang/cpp11/thread_local_storage.html)
- [ブロックスコープを持つstatic変数初期化のスレッドセーフ化(C++11)](https://cpprefjp.github.io/lang/cpp11/static_initialization_thread_safely.html)
- [extern template(C++11)](https://cpprefjp.github.io/lang/cpp11/extern_template.html)
- [依存名に対するtypenameとtemplateの制限緩和(C++11)](https://cpprefjp.github.io/lang/cpp11/dependent_name_specifier_outside_of_templates.html)
- [可変引数テンプレート](https://cpprefjp.github.io/lang/cpp11/variadic_templates.html)
- [継承コンストラクタ(C++11)](https://cpprefjp.github.io/lang/cpp11/inheriting_constructors.html)