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