## Nging RRD Graph module




```
./configure \
    --prefix=/usr/share/nginx \
    --sbin-path=/usr/sbin/nginx \
    --conf-path=/etc/nginx/nginx.conf \
    --pid-path=/var/run/nginx.pid \
    --lock-path=/var/lock/nginx.lock \
    --error-log-path=/var/log/nginx/error.log \
    --http-log-path=/var/log/access.log \
    --user=www-data \
    --group=www-data \
    --without-mail_pop3_module \
    --without-mail_imap_module \
    --without-mail_smtp_module \
    --without-http_fastcgi_module \
    --without-http_uwsgi_module \
    --without-http_scgi_module \
    --without-http_memcached_module \
    --with-ipv6 \
    --with-http_ssl_module \
    --with-http_spdy_module \
    --with-http_stub_status_module \
    --with-http_gzip_static_module \
    --add-module=/usr/local/src/mod_rrd_graph-master

```


reload js

```
//
// Reload all images that's inside the graph-container div.
//
function reloadImages() {
    var bigReloaded = false;
    $('div.image-big-graph-container img.big-graph').each(function () {
        var image = $(this);
        if (image.parent("div").is(':visible')) {
            var imageSrc = image.attr("src");
            image.attr("src", addCacheBusterToUrl(imageSrc));
            bigReloaded = true;
        }
    });

    if (!bigReloaded) {
        $('div.graph-container img').each(function () {
            var image = $(this);

            if (image.attr("check-type") != undefined) {
                if (image.parent("div").is(':visible')) {
                    var imageSrc = image.attr("src");

                    setGraphDimensions(image, window.smallGraphWidth, window.smallGraphHeight);
                    imageSrc = replaceDimensionInImageSrc(imageSrc, window.smallGraphWidth, window.smallGraphHeight);

                    image.attr("src", addCacheBusterToUrl(imageSrc));
                }
            }
        });
    }

    delayReload(reloadImages, 1000 * 60);
}


```

