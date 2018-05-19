# Using tabindex

* フォーカス可能なページ要素の明示的な順序を指定できる。
* フォーカス不可能な要素をタブオーダーに追加できる。
* 要素をタブオーダーから削除できる。

※任意の要素に適用できるが、必ずしもすべての要素で有用であるとは限らない。

例

`<custom-button tabindex="0">Press Tab to Focus Me!</custom-button>`

`tabindex="0"` で要素を通常のタブオーダーに追加。要素は `Tab` キーを押すとフォーカスされ、`focus()`メソッドを呼び出すことでフォーカスが当たる。

```
<button id="foo" tabindex="-1">I'm not keyboard focusable</button>
<button onclick="foo.focus();">Focus my sibling</button>
```

`tabindex="-1"` タブオーダーから要素が削除されるが、 `focus()`メソッドを呼び出すことでフォーカスすることはできる。

```
<button>I should be first</button>
<button>And I should be second</button>
<button tabindex="5">But I jumped to the front!</button>
```

`tabindex="5"` 0 より大きい tabindex を指定すると、通常のタブオーダーの前にその要素へジャンプする。複数の要素の tabindex が 0 より大きい場合、タブオーダーは 0 より大きい最小値からスタートし、次第に大きい値の要素に移動する。
**0 より大きい tabindex の使用は、アンチパターン**

## アンチパターン

* 0 より大きい tabindex の使用
* ヘッダー、イメージ、記事のタイトルなど非入力要素への使用

可能な限り、DOM 順序でタブオーダーが論理的になるようにマークアップをする。
`tabindex` を使用する対象はユーザーが入力を想定する要素に制限する。

* ボタン
* タブ
* ドロップダウン
* テキストフィールド
* その他、カスタムインタラクティブコントロール

スクリーンリーダーには `tabindex` がない。画像には適切な `alt` 属性をサポートすることでスクリーンリーダーのユーザーも画像のコンテンツを理解することができるようになる。

## ページレベルでのフォーカス管理

さまざまなコンテンツのセクションを含むシングルページでは、すべてのセクションを一度に見ることができない。このようなページでは、ナビゲーションリンクをクリックすることでページを更新せずに表示可能なコンテンツを変更できる場合がある。

この場合、選択したコンテンツ領域を特定し、`tabindex` に-1 を指定して、通常のタブオーダーで表示されないようにし、`focus` メソッドを呼び出す。

## コンポーネントのフォーカス管理

カスタムコンポーネントを作成している場合は、ネイティブの要素と同様な動作を提供し、ユーザーが主にキーボードだけに頼ってコントロールを操作できるようにする。

例えば `select` 要素では `Tab` でフォーカスし、カーソルキーで選択肢を移動することができる。

とはいえ、実装するキーボードの動作を把握するのが難しいため下記のドキュメントを参考にするのがよい。

[ Accessible Rich Internet Applications (ARIA) Authoring Practices](https://www.w3.org/TR/wai-aria-practices/)

このガイドには、タイプ別のコンポーネントの一覧と、サポートしているキーボードの操作が記載されている。

## カスタムのラジオボタンを作ってみる

```
<radio-group>
  <radio-button>Water</radio-button>
  <radio-button>Coffee</radio-button>
  <radio-button>Tea</radio-button>
  <radio-button>Cola</radio-button>
  <radio-button>Ginger Ale</radio-button>
</radio-group>
```

[ARIA Authoring Practice](https://www.w3.org/TR/wai-aria-practices/)の[Radio Group](https://www.w3.org/TR/wai-aria-practices/#radiobutton)を見ると、サポートする必要がある機能に、**Keyboard Interaction** が記載されている。

* When a radio group receives focus:
  _ If a radio button is checked, focus is set on the checked button.
  _ If none of the radio buttons are checked, focus is set on the first radio button in the group.
* Space: checks the focused radio button if it is not already checked.
* Right Arrow and Down Arrow: move focus to the next radio button in the group, uncheck the previously focused button, and check the newly focused button. If focus is on the last button, focus moves to the first button.
* Left Arrow and Up Arrow: move focus to the previous radio button in the group, uncheck the previously focused button, and check the newly focused button. If focus is on the first button, focus moves to the last button.

上下左右の矢印キーによる動作を `tabindex` の移動で実装する。`tabindex` の移動を機能させるには、現在アクティブな子を除いて、すべての子の `tabindex` を -1 に設定する。

```
<radio-group>
  <radio-button tabindex="0">Water</radio-button>
  <radio-button tabindex="-1">Coffee</radio-button>
  <radio-button tabindex="-1">Tea</radio-button>
  <radio-button tabindex="-1">Cola</radio-button>
  <radio-button tabindex="-1">Ginger Ale</radio-button>
</radio-group>
```

コンポーネントは、次にキーボードのイベントリスナーを使用し、ユーザーが押したキーを判断する。以前にフォーカスを設定した子の `tabindex` は -1 に設定し、次にフォーカスされる子の `tabindex` を 0 にして、その子の `focus` メソッドを呼び出す。

```
<radio-group>
  // Assuming the user pressed the down arrow, we'll focus the next available child
  <radio-button tabindex="-1">Water</radio-button>
  <radio-button tabindex="0">Coffee</radio-button> // call .focus() on this element
  <radio-button tabindex="-1">Tea</radio-button>
  <radio-button tabindex="-1">Cola</radio-button>
  <radio-button tabindex="-1">Ginger Ale</radio-button>
</radio-group>
```

ユーザーが最後（またはフォーカスの移動方向によっては先頭）の子まで到達すると、ループして先頭（または最後）の子に再度フォーカスする。

[完成版のソース](https://gist.github.com/robdodson/85deb2f821f9beb2ed1ce049f6a6ed47)
