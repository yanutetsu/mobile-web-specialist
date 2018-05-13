# Accessibility

* [Overview](https://developers.google.com/web/fundamentals/accessibility/)
* Focus
	* [Introduction to Focus](https://developers.google.com/web/fundamentals/accessibility/focus/)
	* [DOM Order Matters](https://developers.google.com/web/fundamentals/accessibility/focus/dom-order-matters)
	* [Using tabindex](https://developers.google.com/web/fundamentals/accessibility/focus/using-tabindex) 

## Overview

### Accessibility とは

Accessibility（ユーザー補助機能） = サイトがアクセス可能

* **誰でも** サイトのコンテンツを使用できる。
* コンテンツの機能を操作できる。

デベロッパーは、すべてのユーザーがキーボード、マウス、またはタッチ スクリーンを見ることができ、それを使用してページのコンテンツを自分と同じように操作できると考えがち。
→ 一部のユーザーには快適に動作しても、他のユーザーにとっては、不快に感じたり中断せざるをえなかったり、さまざまな問題が発生する。

Accessibility はなんらかの身体障がいや機能低下を抱えるユーザーに対して議論しがちだが、すべてのユーザーに関係がある。例えば、下記のような例も含まれる。

* スマートフォンで PC 用のサイトを思い通りに使えない
* 「このコンテンツはお住まいの地域では使用できません」というメッセージが表示される
* 使い慣れたメニューがタブレットでは見つからない

Accessibility の問題に対処することで、ほとんどの場合はすべてのユーザーのエクスペリエンスが向上する。

#### Example

改善前

![改善前](./img/pooraccess.jpg)

* テキストのコントラストが低いため、低視力のユーザーは読みにくい可能性があります。
* 左側にラベル、右側にフィールドがあるため、多くのユーザーは関連付けがしにくく、拡大が必要なユーザーは、ほとんどページを使用できない。
* 「Remember details?」ラベルはチェックボックスに関連付けられていないため、ただラベルをクリックするのではなく、小さい正方形をタップまたはクリックする必要がある。また、スクリーンリーダーを使用するユーザーは、関連付けがわかりにくい可能性がある。

改善後

![改善後](./img/betteraccess.jpg)

### Web Content Accessibility Guidelines

[Web Content Accessibility Guidelines (WCAG) 2.0](https://www.w3.org/TR/WCAG20/)

いくつかの国では、Accessibility の法的要件でこのガイドラインの使用を義務付けている。またWCAG は次の原則によってまとめられている。

#### POURの原則

* **知覚可能（Perceivable）**: ユーザーはコンテンツを知覚できているか。視覚など、1 つの感覚で認識できるからといって、すべてのユーザーがそれを知覚できるとは限らない。

* **操作可能（Operable）**: ユーザーは UI コンポーネントを使用したりコンテンツをナビゲートしたりできているか。例えば、カーソルを合わせる操作を必要とするものは、マウスやタッチスクリーンを使用できないユーザーは操作できない。

* **理解可能（Understandable）**: ユーザーはコンテンツを理解できているか。ユーザーが混乱することのない、分かりやすく一貫性のあるインターフェースになっているか。

* **堅牢性（Robust）**: コンテンツはさまざまなユーザーエージェント（ブラウザ）で利用できているか。支援技術で使用できているか。

[WebAIM（Web Accessibility in Mind）](https://webaim.org/)グループでは WCAG ガイドラインをわかりやすい[チェックリスト](https://webaim.org/standards/wcag/checklist)にまとめている。これは、特に Web コンテンツを対象としている。

### ユーザーの多様性について

障害には考慮すべきさまざまなカテゴリーやタイプが存在する。

* 視覚、運動、聴覚、認識の4つのカテゴリ
* 状況的、一時的、永続的の3つのタイプ

||状況的|一時的|永続的|
|---|---|---|---|
|視覚||振動|失明|
|運動|赤ん坊を抱えている|腕の骨折、RSI|RSI|
|聴覚|騒音のあるオフィス|||
|認識||振動||

RSI（反復性疲労傷害）: 手根管症候群、テニス エルボー、ばね指

## Focus

マウスの代わりにキーボードで操作できる機能を構築する方法について説明する。これは、運動障害のあるユーザーにとって重要であり、当然ながら、すべてのユーザーにとって使いやすい UI を作成することにもつながる。

## Semantics

さまざまな支援技術と連携できる堅牢な方法で、UI を作成できるようにする。

## Accessible Styles

ビジュアルデザインについて検討し、できる限り柔軟で便利な視覚要素を作成するためのテクニックを紹介。