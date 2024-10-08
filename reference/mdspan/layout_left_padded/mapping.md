# mapping
* mdspan[meta header]
* class template[meta id-type]
* std[meta namespace]
* layout_left_padded[meta class]
* cpp26[meta cpp]

```cpp
namespace std {
  template<size_t PaddingValue>
  template<class Extents>
  class layout_left_padded<PaddingValue>::mapping;
}
```
* layout_left_padded[link ../layout_left_padded.md]
* Extents[link ../extents.md]

## 概要
`layout_left_padded<S>::mapping<E>`は、[`extents`](../extents.md)型の多次元配列サイズ`E`をパラメータとして、パディングあり列優先(column major)[レイアウトマッピング](../LayoutMapping.md)を表現するクラスである。

`layout_left_padded<S>::mapping<E>`は[トリビアルコピー可能](/reference/type_traits/is_trivially_copyable.md)であり、[`regular`](/reference/concepts/regular.md)のモデルである。

### 説明専用の仮想操作
動作説明用の仮想操作`LEAST-MULTIPLE-AT-LEAST(x,y)`を、次のように定義する。

- `x == 0`のとき、`y`
- そうでなければ、`y`以上で最小となる`x`の倍数

### 説明専用メンバ変数
`layout_left_padded<S>::mapping`クラステンプレートは、下記の説明専用メンバ変数を保持する。

- `stride-1` : `index_type`型の値`static-padding-stride`
- `extents_` : `extents_type`型の[多次元配列サイズ情報](../extents.md)

また下記の説明専用静的メンバ変数を保持する。

- `rank_` : `size_t`型の定数`extents_type::`[`rank()`](../extents/rank.md)
- `first-static-extent` : `size_t`型の定数`extents_type::`[`static_extent`](../extents/static_extent.md)`(0)`
- `static-padding-stride` : `size_t`型の下記定数
    - `rank_`が`0`または`1`のとき、値`0`
    - `padding_value`または`first-static-extent`が[`dynamic_extent`](/reference/span/dynamic_extent.md)と等しいとき、`dynamic_extent`
    - そうでなければ、`LEAST-MULTIPLE-AT-LEAST(padding_value, first-static-extent)`


## 適格要件
- `Extents`は[`extents`](../extents.md)の特殊化であること。
- `rank_dynamic() == 0`のとき、多次元インデクス空間`Extents()`のサイズが`index_type`型で表現できること。
- 値`padding_value`が`index_type`型で表現できること。
- 以下を満たすとき、`LEAST-MULTIPLE-AT-LEAST(padding_value, first-static-extent)`が、`size_t`型および`index_type`型で表現できること。
    - `rank_ > 1`、かつ
    - `padding_value !=` [`dynamic_extent`](/reference/span/dynamic_extent.md)、かつ
    - `first-static-extent`が[`dynamic_extent`](/reference/span/dynamic_extent.md)と等しくないとき。
- 以下を満たすとき、半開区間`[1, rank_)`の全ての値`k`に対して`LEAST-MULTIPLE-AT-LEAST(padding_value, ext.static_extent(0))`と全ての`ext.static_extent(k)`を乗じた結果が、`size_t`型および`index_type`型で表現できること。
    - `rank_ > 1`、かつ
    - `padding_value !=` [`dynamic_extent`](/reference/span/dynamic_extent.md)、かつ
    - 半開区間`[0, extents_type::`[`rank()`](../extents/rank.md)`)`の全ての値`k`に対して`extents_type::`[`static_extent`](../extents/static_extent.md)`(k)`が[`dynamic_extent`](/reference/span/dynamic_extent.md)と等しくないとき。


## メンバ関数
### 構築・破棄

| 名前 | 説明 | 対応バージョン |
|------|------|----------------|
| [`(constructor)`](mapping/op_constructor.md) | コンストラクタ | C++26 |
| `(destructor)` | デストラクタ | C++26 |
| `operator=`    | コピー代入演算子 | C++26 |

### 観測

| 名前 | 説明 | 対応バージョン |
|------|------|----------------|
| `extents` | 多次元配列のサイズ`extents_`を取得する | C++26 |
| [`strides`](mapping/strides.md) | ストライド幅配列を取得する | C++26 |
| [`required_span_size`](mapping/required_span_size.md) | 要素アクセス範囲を取得する | C++26 |
| [`operator()`](mapping/op_call.md) | 多次元配列インデクスから要素位置へ変換する | C++26 |
| [`is_exhaustive`](mapping/is_exhaustive.md) | [Exhaustive特性](../LayoutMapping.md)を取得する | C++26 |
| [`stride`](mapping/stride.md) | 指定次元のストライド幅を取得する | C++26 |


## 静的メンバ関数

| 名前 | 説明 | 対応バージョン |
|------|------|----------------|
| `is_always_unique`  | `true`を返す | C++26 |
| [`is_always_exhaustive`](mapping/is_always_exhaustive.md) | 型の[Exhaustive特性](../LayoutMapping.md)を取得する | C++26 |
| `is_always_strided` | `true`を返す | C++26 |
| `is_unique`  | `true`を返す | C++26 |
| `is_strided` | `true`を返す | C++26 |


## メンバ型

| 名前 | 説明 | 対応バージョン |
|------|------|----------------|
| `extents_type` | [`Extents`](../extents.md) | C++26 |
| `index_type` | [`Extents::index_type`](../extents.md) | C++26 |
| `size_type` | [`Extents::size_type`](../extents.md) | C++26 |
| `rank_type` | [`Extents::rank_type`](../extents.md) | C++26 |
| `layout_type` | [`layout_left_padded`](../layout_left_padded.md)`<PaddingValue>` | C++26 |


## 非メンバ（*Hidden friends*）関数

| 名前 | 説明 | 対応バージョン |
|------|------|----------------|
| [`submdspan_mapping`](mapping/submdspan_mapping.md) | [`submdspan`](../submdspan.md)サポート | C++26 |

### 比較演算子

| 名前 | 説明 | 対応バージョン |
|------|------|----------------|
| [`operator==`](mapping/op_equal.md) | 等値比較 | C++26 |
| [`operator!=`](mapping/op_equal.md) | 非等値比較 (`==`により使用可能) | C++26 |


## バージョン
### 言語
- C++26

### 処理系
- [Clang](/implementation.md#clang): ??
- [GCC](/implementation.md#gcc): ??
- [ICC](/implementation.md#icc): ??
- [Visual C++](/implementation.md#visual_cpp): ??


## 関連項目
- [`mdspan`](../mdspan.md)
- [`submdspan`](../submdspan.md)


## 参照
- [P2642R6 Padded mdspan layouts](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2024/p2642r6.pdf)
