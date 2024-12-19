---
title: add-ogp
date: 2024-12-04 22:20:08
tags:
hidden: true
---
# head.ejsを編集
```node_modules/hexo-theme-theme-name/layout/_partial/head.ejs```
もしくは
```themes/hexo-theme-theme-name/layout/_partial/head.ejs```
を編集して以下を書き足す(仕様上node_modules内の編集は何かしらで適応しないと更新のタイミングなどで初期化されるので、今後も維持する場合は```themes/```に置くかフォークすること)。
　
```
<%
let ogImage = '';
function isAbsoluteUrl(url) {
  return /^https?:\/\//i.test(url);
}
// 明示的に404.jpgを除外する処理を追加
function isValid404Image(url) {
  return !url.includes('404.jpg');
}
if (page.og_image && isValid404Image(page.og_image)) {
  // 記事固有の画像が設定されている場合
  if (isAbsoluteUrl(page.og_image)) {
    ogImage = page.og_image;
  } else {
    ogImage = config.url + (page.og_image.startsWith('/') ? page.og_image : '/' + page.og_image);
  }
} else if (config.og_image && isValid404Image(config.og_image)) {
  // サイト全体のデフォルトOGP画像
  ogImage = config.url + (config.og_image.startsWith('/') ? config.og_image : '/' + config.og_image);
}
%>
<% if (ogImage) { %>
<meta property="og:image" content="<%= ogImage %>">
<meta property="og:image:width" content="1200">
<meta property="og:image:height" content="630">
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:image" content="<%= ogImage %>">
<% } %>
```
　
を書き足す。
もしテーマ固有のOGP生成コードがある場合は削除する。（これしなかったから二重になってた）
Asyncテーマの場合は
```
<%- theme.open_graph ? open_graph() : ''%>
```
を消す必要があった。
　
# _config.theme-name.ymlを編集
テーマ固有の設定などを収容している_config.theme-name.ymlに以下を書き足す。

```og_image: /img/ogp/ogp-image.jpg```

記事にOGP画像が設定されていない場合はここを参照して表示する。
　
# 記事に実装
マークダウンファイルの上部に
```og_image: /file/path/```
　
を書き足すことで設定できる。
　
# OGP画像ファイルの推奨形式
ogpの画像はpng, jpg, webp, gifなど色々な画像形式が使えるが、とりあえずpngとjpgでほとんどカバーできる。
一部サービスはwebpやgif, svgなどが非対応なので前者をおすすめする。
　
1200x630px以上の1.19:1を推奨。
ファイル名の空白はハイフンを用いる。
容量はだいたいのサービスは5mb以下を推奨している。