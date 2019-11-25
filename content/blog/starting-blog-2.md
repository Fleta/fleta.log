---
title: 'Amazon (Lightsail + Route53) + Ghost로 개인 블로그 만들기 - 2'
date: 2019-9-6 02:00:00
category: 'Tech'
---


지난 글에서 다루지 않았던 블로그를 올리면서 했던 다른 설정들을 이번 글에서 다루고자 한다. 


## 1. SSL 설정

무료 SSL인증서의 경우 보통 letsencrypt를 통해 발급받는데, 과정이 조금 귀찮다. 이런 귀찮은 과정을 간소하기 위해 `certbot` 을 사용할 수 있다. Nginx 설정까지 알아서 해주니 편하다.

- 설치

```
sudo add-apt-repository ppa:certbot/certbot
sudo apt update
sudo apt install python-certbot-nginx
```

- 인증서 발급

```
sudo cerbot --nginx -d ${domain}
```

발급하는 과정에서 여러가지 묻는데 읽고 원하는 대로 답하면 된다. 약관 동의 여부, 리다이렉트 여부 등을 물어본다.


## 2. Nginx 설정

1에서 설명한 대로 SSL을 발급받으면 보통은 자동으로 설정된다. 그게 아닌 경우는 어떻게 할 지 생각하며 아래의 글을 작성했다.

블로그의 주소는 현재 `blog.yuigahama.moe`인데, 메인 도메인인 `yuigahama.moe`에도 다른 웹 서비스가 올라가 있다. ghost로 블로그를 올리면서 SSL를 사용하도록 설정했다면 생성된 ghost 폴더의 하위 경로에서 nginx 설정을 발견할 수 있다. 특별히 경로를 바꾸지 않았다면 `/system/files/${blog_domain}.conf` 파일이 그 파일이다. `location` 블록 설정은 되어 있겠지만 블로그만 먼저 세팅하고 ssl 인증서를 받은 경우 ssl_certificate 관련 설정이 안 되어있을 것이다.

```
    ssl_certificate /etc/letsencrypt/live/${blog_domain}/fullchain.pem; 
    ssl_certificate_key /etc/letsencrypt/live/${blog_domain}/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf; 
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; 
```

ghost에서 제공하는 설정으로 nginx를 설정했다면 `/etc/nginx/sites-available/`에 위에서 얘기한 파일을 가리키는 링크가 있을 것이다. 같은 디렉토리 안에 메인 도메인 이름으로 파일을 하나 만들어준다. 필자의 경우 `yuigahama.moe.conf`가 된다. 파일에 아래와 같은 내용을 작성해서 메인 도메인에 대한 nginx 설정을 지정한다.

```
server {

    server_name yuigahama.moe;

    location / {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $http_host;
        proxy_pass ${service_host_port};
    }

    location ~/.well-known {
        allow all;
    }

    client_max_body_size 50m;

    listen [::]:443 ssl ipv6only=on; 
    listen 443 ssl; 
    ssl_certificate ${path of fullchain.pem}; 
    ssl_certificate_key ${path of privkey.pem}; 
    include /etc/letsencrypt/options-ssl-nginx.conf; 
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; 
}
```

인증서를 어떻게 발급받았는지에 따라 파일 경로 등이나 사용되는 변수들이 조금 다를 수 있겠지만, 그래도 프록시/포트/SSL경로/body_size 등의 설정이 들어간다는 점만 이해하면 될 것이다.


## 3. 테마 및 폰트 변경

테마의 경우 어드민 페이지의 **Design** 메뉴에서 편집할 수 있다. **Theme Marketplace**에서는 무료/유료인 스킨들을 공유하며 다른 오픈소스 커뮤니티에 테마가 올라오는 경우도 있다. 나는 [attila](https://github.com/zutrinken/attila) 테마를 사용하였다. Theme Marketplace에도 등록되어 있다. 테마가 압축된 파일을 Design탭에서 업로드 하면 된다.

attila 테마 같은 경우는 외국에서 만든 폰트여서 그런지 post의 body 기본 font-family가 'Cardo', sans-serif로 되어있는데 둘 다 한글폰트가 매우 별로다. 하나는 한글 폰트가 지원되지 않아 기본 폰트처럼 나오고 sans-serif는... (이하 생략) 또한 css에 `word-break: keep-all` 이 적용이 안 되어 있는데 개인적으로 되게 싫어해서 이것도 수정하였다. 아래의 사진은 이해를 돕기 위해 당시 모습을 대략적으로 나타낸 사진이다.

![pic1](/images/start-blog-2-pic-1.png)

대략 한 일을 설명하자면, attila의 경우 `/assets/css/font` 에 폰트 파일을 두고 font-face 정의는 `/src/sass/_fonts.scss`에서 하고 있다. 기존에 사용되던 폰트들은 svg, ttf, woff, woff2 의 다양한 파일들로 font-face를 정의하고 있지만 나는 성의가 없어서 그런지 truetype(ttf) 만 넣고 font-face도 마찬가지로 truetype의 normal, italic에 대해서만 정의했다. 

이렇게 정의한 font-face는 `/src/sass/style.scss` 에서 import해서 사용된다. scss파일의 body블록이 실제로 본문에 적용되는 css이므로 해당 블록에서 font-family를 바꾸고 다른 css 속성을 추가하거나 편집한 뒤 빌드하고 zip파일로 압축하여 업로드만 하면 변경된 테마를 적용시킬 수 있다.

결과는 아래처럼..

![pic2](/images/start-blog-2-pic-2.png)
