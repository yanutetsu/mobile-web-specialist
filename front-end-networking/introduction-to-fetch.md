# Introduction to fetch()

https://developers.google.com/web/updates/2015/03/introduction-to-fetch

`fetch（）`は、XMLHttpRequest（XHR）と同様のネットワーク要求を行うことを可能にします。 主な違いは、Fetch API が Promises を使用していることです。これにより、よりシンプルで洗練された API が使用でき、コールバック地獄や XMLHttpRequest の複雑な API を覚えておく必要がなくなります。

Fetch API は Chrome 40 以降の Service Worker と Chrome 42 以降の Window スコープで有効です。

[Can I Use](https://caniuse.com/#search=fetch)

## Basic Fetch Request

まず、`XMLHttpRequest`と`fetch`それぞれで実装された単純な例を比較してみましょう。 この例は URL をリクエストしてレスポンスを取得し、JSON として解析するだけです。

**XMLHttpRequest**

XMLHttpRequest は成功とエラーのケースを処理するために 2 つのリスナーを設定し、`open（）`と`send（）` を呼び出す必要があります

```
function reqListener() {
  var data = JSON.parse(this.responseText);  
  console.log(data);  
}  

function reqError(err) {  
  console.log('Fetch Error :-S', err);  
}  

var oReq = new XMLHttpRequest();  
oReq.onload = reqListener;  
oReq.onerror = reqError;  
oReq.open('get', './api/some.json', true);  
oReq.send();
```

**Fetch**

```
fetch('./api/some.json')
  .then(function(response) {
    if (response.status !== 200) {
      console.log('Looks like there was a problem. Status Code: ' + response.status);
      return;
    }

    // Examine the text in the response
    response.json().then(function(data) {
      console.log(data);
    });
  })
  .catch(function(err) {
    console.log('Fetch Error :-S', err);
  });
```

`fetch（）` リクエストのレスポンスは Stream オブジェクトです。つまり、`json（）` メソッドを呼び出すと、ストリームの読み込みが非同期で行われるため、Promise が返されます。

## Response Metadata

前の例では、レスポンスオブジェクトのステータスと、レスポンスを JSON として解析する方法を調べました。ヘッダーのようにアクセスする可能性のある他のメタデータが以下に示されています。

```
fetch('users.json').then(function(response) {
  console.log(response.headers.get('Content-Type'));
  console.log(response.headers.get('Date'));
  console.log(response.status);
  console.log(response.statusText);
  console.log(response.type);
  console.log(response.url);
});
```

## Response Types

fetch リクエストを行うと、レスポンスには `basic`、`cors`または`opaque`の response.type が与えられます。これらのタイプは、リソースがどこから来たのかを示し、レスポンスオブジェクトをどのように扱うべきかを知らせるために使用できます。

同じオリジン上のリソースに対してリクエストが行われると、レスポンスは`basic`タイプを持ち、レスポンスから何が表示できるかに制限はありません。

CORS ヘッダーを返す、別のオリジンリソースに対してリクエストが行われた場合、タイプは`cors`です。

`cors`と`basic`レスポンスはほぼ同じですが、`cors`レスポンスでは、表示できるヘッダーが`Cache-Control`、`Content-Language`、`Content-Type`、`Expires`、`Last-Modified`、`Pragma`に制限されます。

`opaque`レスポンスは、CORS ヘッダーを返さない別のオリジンリソースに対して行われたリクエストに対するものです。`opaque`レスポンスでは、返されたデータを読み取ることができないか、要求のステータスを見ることができません。つまり、リクエストが成功したかどうかを確認することはできません。

特定のリクエストのみが解決されるように fetch リクエストのモードを定義することができます。設定できるモードは次の通りです。

* `same-origin`は、同じオリジンリソースのリクエストに対してのみ成功し、他のすべてのリクエストは拒否されます。
* `cors`は、同じオリジンリソースと、適切な COSR ヘッダーを返す他のオリジンのリクエストを許可します。
* `cors-with-forced-preflight`は、実際のリクエストを行う前に常にプリフライトチェックを実行します。
* `no-cors`は CORS ヘッダーを持たず、`opaque`レスポンスをもたらす他のオリジンへのリクエストを行うことを意図していますが、現時点では、window のグローバルスコープにおいてサポートしていません。

モードを定義するには、fetch リクエストの 2 番目のパラメータとしてオプションオブジェクトを追加し、そのオブジェクトのモードを定義します。

```
fetch('http://some-site.com/cors-enabled/some.json', {mode: 'cors'})
  .then(function(response) {
    return response.text();
  })
  .then(function(text) {
    console.log('Request successful', text);
  })
  .catch(function(error) {
    log('Request failed', error)
  });
```

## Chaining Promises

`Promise`の大きな特徴の 1 つは、それらを連鎖させる能力です。fetch の場合は、fetch リクエスト全体でロジックを共有できます。

JSON API を使用して作業している場合は、ステータスを確認し、レスポンスごとに JSON を解析する必要があります。ステータスと JSON 解析を別々の関数で定義することで、コードを単純化することができ、これにより最終的なデータとエラーの処理だけをみることができるようになります。

```
function status(response) {
  if (response.status >= 200 && response.status < 300) {
    return Promise.resolve(response);
  } else {
    return Promise.reject(new Error(response.statusText));
  }
}

function json(response) {
  return response.json();
}

fetch('users.json')
  .then(status)
  .then(json)
  .then(function(data) {
    console.log('Request succeeded with JSON response', data);
  })
  .catch(function(error) {
    console.log('Request failed', error);
  });
```

`status`関数は`response.status`をチェックし、解決済みまたは拒否された Promise を返す`Promise.resolve（）`または`Promise.reject（）`の結果を返します。これは、`fetch（）`チェーンで呼び出される最初のメソッドです。解決された場合は、`json（）`メソッドを呼び出して、再度`response.json（）`呼び出しから Promise を返します。この後、私たちは解析された JSON のオブジェクトを持っています。解析が失敗した場合、Promise は拒否され、`catch`ステートメントが実行されます。

これらの大きな点は、すべての fetch リクエストにロジックを共有できるため、コードの保守、読み込み、テストが容易になることです。

## POST Request

Web アプリケーションが`POST`メソッドを使って API を呼び出し、リクエストの body にいくつかのパラメータを指定することは珍しいことではありません。 これを行うために、`fetch（）`オプションで`method`と`body`のパラメータを設定できます。

```
fetch(url, {
  method: 'post',
  headers: {
    "Content-type": "application/x-www-form-urlencoded; charset=UTF-8"
  },
  body: 'foo=bar&lorem=ipsum'
})
.then(json)
.then(function (data) {
  console.log('Request succeeded with JSON response', data);
})
.catch(function (error) {
  console.log('Request failed', error);
});
```

## Sending Credentials with a Fetch Request

cookie などの資格情報を使用して fetch リクエストを作成する場合は、リクエストの`credentials`を`include`に設定する必要があります。

```
fetch(url, {
  credentials: 'include'
})
```

## FAQ

### `fetch（）`リクエストをキャンセルするには？

現時点でキャンセルする方法はありません。これは[GitHub](https://github.com/whatwg/fetch/issues/20)で議論されています。

### ポリフィルはある？

[こちら](https://github.com/github/fetch)

### Service Worker では `no-cors` がサポートされているのに window でサポートされていないのはなぜ？

セキュリティ上の懸念があるため。[ここ](https://bugs.chromium.org/p/chromium/issues/detail?id=457157&q=fetch%20no-cors&colspec=ID%20Pri%20M%20Week%20ReleaseBlock%20Cr%20Status%20Owner%20Summary%20OS%20Modified)で詳しく知ることができます。

### onProgress はどうやってとるのか

[こちら](https://github.com/AnthumChris/fetch-progress-indicators/blob/master/fetch-basic/supported-browser.js)を参考にしてください。

## 参考

* [Fetch Standard （日本語訳）](https://triple-underscore.github.io/Fetch-ja.html)
* [Fetch & Streams API Progress Indicator Examples](https://fetch-progress.anthum.com/)
