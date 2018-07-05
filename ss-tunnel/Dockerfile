
FROM alpine
LABEL maintainer="江泽民 jiangzm@ccp.gov.cn  kev <noreply@datageek.info>, Sah <contact@leesah.name>"


COPY ./config.json /etc/config.json

RUN set - ex \
   && apk add --no-cache git \
   && git clone --depth=1 --recursive  https://github.com/shadowsocks/shadowsocks-libev.git /tmp/repo \
   && git clone --depth=1 --recursive https://github.com/shadowsocks/simple-obfs.git /tmp/repo2 \
 # Build environment setup
   && apk add --no-cache --virtual .build-deps \
      autoconf \
      automake \
      build-base \
      c-ares-dev \
      libev-dev \
      zlib-dev \
      openssl \
      gcc g++ \
      libpcre32\
      libev-dev \
      libtool \
      libsodium-dev \
      linux-headers \
      mbedtls-dev \
      pcre-dev \
 # Build & install
   && cd /tmp/repo \
   && ./autogen.sh  && echo "Install 1" \
   && ./configure --prefix=/usr --disable-documentation \
   && make install \
   && cd /tmp/repo2 \
   && ./autogen.sh && echo "Install 2" \
   && ./configure --prefix=/usr --disable-documentation \
   && make install \
   && apk del .build-deps \
 # Runtime dependencies setup
   && apk add --no-cache \
      rng-tools \
      $(scanelf --needed --nobanner /usr/bin/ss-* \
      | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
      | sort -u) \
   && rm -rf /tmp/repo



USER nobody

EXPOSE 5353/udp

CMD exec ss-tunnel -c /etc/config.json -b 0.0.0.0 -l 5353 -L 8.8.8.8:53 -u
 
