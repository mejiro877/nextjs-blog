---
title: "TypeScriptで見つけた思わぬ仕様"
date: "2023-05-22"
thumbnail: "/images/thumbnail02.jpg"
---

JavaScript で DOM 操作を行うとき、次のように setattribute で属性を追加したいことってよくありますよね。

```
var d = document.getElementById("d1");

d.setAttribute("disabled", true);
```

実はこれを TypeScript で書くと、true のところでエラーが出ます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2520632/12eb0806-e300-39a4-76ec-29622d8230cd.png)

## TypeScript では setattribute の引数は string でなければいけない

TypeScript では仕様なのか、Boolean でも setattribute で受け取り引数が string で固定になっていて、
引用符か二重引用符で囲まないといけません。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2520632/53a94ea7-dd85-8093-692d-30f95cb1cf57.png)

GitHub 上で議論されていましたが、引用符なしでも受け取るようにリクエストされている Issues に対して、回答者は否定的な意見で暗黙的な変換になるからよくないとのこと。

どうしてもそういう動きにしたいのなら、Element インターフェースに boolean や number を受け取る setattribute を追加してくれということみたいで、公式では仕様としているようです。

https://github.com/microsoft/TypeScript/issues/15368
