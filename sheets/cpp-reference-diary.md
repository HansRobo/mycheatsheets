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

いわゆるエントリポイント，main関数

- main 関数は`int 型だが，return文はなくてもOK
- `auto main()`という書き方はダメらしい
- `return N`は`std::exit(N)`と同じ意味を持つ
- main関数が関数tryブロックになっていてもmain関数の外で定義された（しかし，main関数の終了と同時に破棄される）静的オブジェクトのデストラクタで投げられた例外はキャッチできない
