# 基于官方 nginx 镜像
FROM nginx:1.21.6-perl

RUN sed -i 's/http:\/\/deb.debian.org/https:\/\/mirrors.aliyun.com/g'
RUN sed -i 's/http:\/\/security.debian.org/http:\/\/mirrors.aliyun.com/g' /etc/apt/sources.list

# 下载并安装依赖项
RUN apt-get update && \
    apt-get install -y curl gcc libcurl4-openssl-dev libpcre3-dev libssl-dev zlib1g-dev make git

ARG NGINX_PATH=/nginx
# 下载 Nginx 的源代码
RUN curl -O https://nginx.org/download/nginx-1.21.6.tar.gz
RUN tar zxvf nginx-1.21.6.tar.gz && mv nginx-1.21.6 ${NGINX_PATH}

WORKDIR ${NGINX_PATH}

#RUN echo "140.82.113.4 github.com" >> /etc/hosts && \
#    echo "199.232.5.194 github.global.ssl.fastly.net" >> /etc/hosts \

RUN cat /etc/hosts
# 下载要编译的模块打补丁
RUN git clone https://github.com/yaoweibin/nginx_upstream_check_module.git
RUN patch -p1 < ${NGINX_PATH}/nginx_upstream_check_module/check_1.20.1+.patch
RUN git clone https://github.com/gnosek/nginx-upstream-fair.git
WORKDIR ${NGINX_PATH}/nginx-upstream-fair
RUN ls -l
RUN sed -i 's/default_port/no_port/g' ngx_http_upstream_fair_module.c
RUN patch -p1 < ${NGINX_PATH}/nginx_upstream_check_module/upstream_fair.patch

WORKDIR ${NGINX_PATH}

# 配置和编译 Nginx，并添加 ngx_http_substitutions_filter_module 模块
RUN ./configure  \
    --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib/nginx/modules --conf-path=/etc/nginx/nginx.conf \
    --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid \
    --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp \
    --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp \
    --user=nginx --group=nginx --with-compat --with-file-aio --with-threads --with-http_addition_module --with-http_auth_request_module \
    --with-http_dav_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_mp4_module \
    --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module \
    --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-mail --with-mail_ssl_module \
    --with-stream --with-stream_realip_module --with-stream_ssl_module --with-stream_ssl_preread_module \
    --with-cc-opt='-g -O2 -ffile-prefix-map=/data/builder/debuild/nginx-1.21.6/debian/debuild-base/nginx-1.21.6=. -fstack-protector-strong -Wformat -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -fPIC' \
    --with-ld-opt='-Wl,-z,relro -Wl,-z,now -Wl,--as-needed -pie' \
    --add-module=./nginx_upstream_check_module \
    --add-module=./nginx-upstream-fair && \
    make && \
    make install