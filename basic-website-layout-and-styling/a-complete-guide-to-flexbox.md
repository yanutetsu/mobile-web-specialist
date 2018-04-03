# A Complete Guide to Flexbox

https://css-tricks.com/snippets/css/a-guide-to-flexbox/

## 背景

Flexbox Layout（Flexible Box）モジュールの目的は、コンテナ内にあるアイテム間のスペースをレイアウト、整列、配置するためにより効率的な方法を提供することです。

この Flex Layout の背景にある主な考え方は、アイテムの幅や高さ（および順序）を変更して、コンテナに使用可能なスペースをベストな形で満たすようにすることです（ほとんどすべての種類のディスプレイ端末と画面サイズに対応します）。

最も重要なのは、Flexbox Layout は通常のレイアウト（block は垂直ベースで、inline は水平ベース）とは対照的に、方向に依存しないことです。block や inline はページには適していますが、大規模で複雑なアプリケーション（特に方向の変更、サイズ変更、ストレッチ、縮小など）をサポートするための柔軟性がありません。

NOTE: Flexbox Layout は、アプリケーションのコンポーネントや小規模なレイアウトに最適ですが、Grid Layout は大規模なレイアウトを対象としています。

## 基本と用語

![フレックスコンテナ](./img/flex-container.svg)
![フレックスアイテム](./img/flex-items.svg)

* `（フレックス）コンテナ` - 親要素
* `フレックスアイテム` - 子要素

![Flexbox の概要図](./img/flexbox.png)
参照: https://css-tricks.com/snippets/css/a-guide-to-flexbox/

基本的には、main 軸（main start から main end まで）または cross 軸（cross start から cross end まで）に沿ってアイテムが配置されます。

* `main axis` - main 軸は、フレックスアイテムが配置される main 軸です。必ずしも水平である必要はなく flex-direction プロパティ（下記参照）に依存します。

* `main-start | main-end` - フレックスアイテムは、`main-start` から `main-end` に向かってコンテナ内に配置されます。

* `main size` - フレックスアイテムの幅または高さのうち、メイン方向にあるものがアイテムのメインサイズです。 フレックスアイテムのメインサイズプロパティは、`width` または `height` プロパティのいずれか、メイン方向のいずれかです。

* `cross axis` - main 軸に垂直な軸を cross 軸といいます。その方向は main 軸方向に依存する。

* `cross-start | cross-end` - フレックスラインはアイテムで満たされ、フレックスコンテナの `cross-start` 側から `cross-end` 側に向かってコンテナ内に配置されます。

* `cross size` - フレックスアイテムの幅または高さのうち、いずれかがクロス方向にあるものはアイテムのクロスサイズです。 クロスサイズプロパティは、クロス方向内にある「幅」または「高さ」のいずれかです。

## 親要素（コンテナ）のプロパティ

### display

```css
.container {
  display: flex | inline-flex;
}
```

指定された要素の直接の子要素をフレックスアイテムにします。
`flex` と `inline-flex` の違いはコンテナが `inline-box` の性質をもつかどうかです（用は子要素の幅や高さに依存するかどうか）。

### flex-direction

```css
.container {
  flex-direction: row | row-reverse | column | column-reverse;
}
```

main 軸が確立され、コンテナにフレックスアイテムが配置される方向が定義されます。  
Flexbox は単一方向に対するレイアウトです。フレックスアイテムは、主に水平行または垂直列のいずれかにレイアウトすると考えてください。

* `row` (default) - 左から右（アラビア語などでは逆）
* `row-reverse` - 右から左（アラビア語などでは逆）
* `column` - 上から下
* `column-reverse` - 下から上

### flex-wrap

```css
.container {
  flex-wrap: nowrap | wrap | wrap-reverse;
}
```

デフォルトでは、フレックスアイテムはすべて 1 行に収まるようにしようとします。このプロパティでアイテムを必要に応じて折り返すことができます。

* `nowrap` (default) -
  すべてのフレックスアイテムを 1 行におさめる
* `wrap` -
  フレックスアイテムを上から下に複数の行に折り返します
* `wrap-reverse` -
  フレックスアイテムを下から上に複数の行に折り返します

### flex-flow

```css
flex-flow: <flex-direction> || <flex-wrap>;
```

`flex-direction` と `flex-wrap` を同時に定義できるショートハンド。

### justify-content

```css
.container {
  justify-content: flex-start | flex-end | center | space-between | space-around
    | space-evenly;
}
```

`justify-content` は main 軸に沿った整列を定義します。

* `flex-start` (default) - main start に フレックスアイテムを集めます
* `flex-end` - main end に フレックスアイテムを集めます
* `center` - フレックスアイテムを中央に集めます
* `space-between` - フレックスアイテムを均等に分散する。 最初のアイテムは main start に接する。最後のアイテムは main end に接する。
* `space-around` - フレックスアイテムを均等に分散する。アイテムは、両側にアイテム間の半分のスペースを持つ。
* `space-evenly` - フレックスアイテムを均等に分散する。各アイテムの周囲に均等なスペースを置く。

### align-items

```css
.container {
  align-items: flex-start | flex-end | center | baseline | stretch;
}
```

cross 軸に沿った整列を定義する。

* `flex-start` - cross start に合わせる
* `flex-end` - cross-end に合わせる
* `center` - cross 軸の中央に配置する
* `baseline` - アイテムのベースラインに合わせる
* `stretch` (default) -
  コンテナを埋めるように伸びる（※ アイテムの min-width / max-width のほうが優先される）

### align-content

```css
.container {
  align-content: flex-start | flex-end | center | space-between | space-around |
    stretch;
}
```

`justify-content` の cross 軸版と考えれば OK。

NOTE: フレックスアイテムが 1 行の時はこのプロパティの効果はない

* `flex-start` - cross start に フレックスアイテムを集めます
* `flex-end` - cross end に フレックスアイテムを集めます
* `center` - フレックスアイテムを cross 軸の中央に集めます
* `space-between` - フレックスアイテムを cross 軸に対して均等に分散する。 最初のアイテムは cross start に接する。最後のアイテムは cross end に接する。
* `space-around` - フレックスアイテムを cross 軸に対して均等に分散する。アイテムは、両側にアイテム間の半分のスペースを持つ。
* `stretch` (default) - cross 軸全体を占めるようにラインを拡張する。

## 子要素（フレックスアイテム）のプロパティ

### order

```css
.item {
  order: <integer>; /* default は 0 */
}
```

`order` プロパティは、フレックスコンテナ内でフレックスアイテムをレイアウトに使用する順序を指定します。要素は、`order` の値の昇順に配置されます。`order` の値が同じ要素は、ソースコード内で現れる順に配置されます。

### flex-grow

```css
.item {
  flex-grow: <number>; /* default は 0 */
}
```

フレックスアイテムがコンテナ内に占める比率を指定します。

### flex-shrink

```css
.item {
  flex-shrink: <number>; /* default は 1 */
}
```

フレックスアイテムの縮小率を指定します。`flex-wrap`の値が`no-wrap`、かつアイテムの幅の合計がコンテナよりも大きい場合に意味を持ちます。

### flex-basis

```css
.item {
  flex-basis: <length> | auto; /* default は auto */
}
```

フレックスアイテムの幅を指定します。

### flex

```css
.item {
  flex: none | [ <flex-grow> <flex-shrink>? || <flex-basis> ];
}
```

`flex-grow` と `flex-shrink` と `flex-basis` を同時に定義できるショートハンド。

### align-self

```css
.item {
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```

コンテナの `align-items` の値を上書きして配置します。
