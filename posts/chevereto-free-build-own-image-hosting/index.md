# 使用 Chevereto-Free 自建图床


少了图片的博客总感觉不完整，以前在 Hexo 的时候用过七牛的存储，但看到有人被刷流量需要付高额的钱，有点怂，算了算了。也考虑过各种图床，但是害怕不知道啥时候就跑路了。也想过在 Github 建个仓库当图床，又有 1G 存储的限制，速度可能还不如我这个 1M 小水管的 VPS，就打算自建图床了。

<!--more-->

图床的软件选择在 国产的 imgUrl 和 国外的 Chevereto-Free 上选了一下，都挺不错的，最终选定了 Chevereto-Free，因为更喜欢他的前端界面。

## 程序安装

Chevereto-Free 提供了多种安装方法。

一开始我选择的是通过官方提供的 *installer.php* 自动安装，但是你懂的，Github 的仓库拉不下来。就自己去下了最新版的 Releases，拖到服务器打开网站一气呵成，然后404了。

搜了一下在官方文档找到了 [解决办法](https://chevereto.com/docs/requirements)，配置一下 Nginx 的伪静态就可以了：

```
# Image not found replacement
location ~* (jpe?g|png|gif|webp) {
    log_not_found off;
    error_page 404 /content/images/system/default/404.gif;
}

# CORS header (avoids font rendering issues)
location ~ \.(ttf|ttc|otf|eot|woff|woff2|font.css|css|js)$ {
    add_header Access-Control-Allow-Origin "*";
}

# Pretty URLs
location / {
    index index.php;
    try_files $uri $uri/ /index.php$is_args$query_string;
}
```

## 网站设置

安装完之后，简单的设置，一下就行了，免费版的没那么多参数可以设置的。

## 参考文档

[Chevereto-Free](https://github.com/Chevereto/Chevereto-Free)

[Chevereto Docs](https://chevereto.com/docs)

[Chevereto 中文文档](https://ch.cndrew.cn/)
