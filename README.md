# gylm [![build](https://github.com/naruto522ru/gylm/actions/workflows/test.yml/badge.svg)](https://github.com/naruto522ru/gylm/actions/workflows/test.yml)

Dynamic realtime profile ReadMe linked with Yandex Music / Last.fm
<hr>

## Contents
- [Prepare](#prepare)
- [Installation on the server](#installation-on-the-server)
- [Adding to Readme.md file](#adding-to-readme-file)
- [Demo](#demo)

<hr>



# Prepare

### Yandex

* Open [YM settings](https://music.yandex.ru/settings/account)
* Import music collections -> Link Last.fm account

### Last.fm

* [Create API account](https://www.last.fm/api/account/create)
* Save it

# Installation on the server

* Clone repo
```
git clone https://github.com/naruto522ru/gylm.git && cd gylm && git checkout fork-main
```
* Patch file (Optional)

```
patch < main.patch
```

* For building
```
go build -o gylm
```
* ~~Create .env file in repo folder~~ (*Abolished if you apply the patch*)

```
KEY=9b69****************************
SECRET=de01****************************
USERNAME=XXX
LIMIT=10
YANDEX_URL=https://music.yandex.ru/users/Invisible-XXX/artists
```
<details>
  <summary>Variable description</summary>

KEY lastfm API key

SECRET lastfm shared secret

USERNAME lastfm username

LIMIT the number of results to fetch per page

YANDEX_URL yandex music url

</details>

* Create file gylm.service (If the patched file)
```sudo systemctl edit --force gylm.service```
<details>
  <summary>Example service file</summary>

  ```
[Unit]
Description=Dynamic realtime profile ReadMe linked with Yandex Music / Last.fm
After=network.target

[Service]
ExecStart=/usr/local/bin/gylm
User=root
Group=root
Restart=on-failure
Environment="URL_DL_HTML=https://raw.githubusercontent.com/naruto522ru/gylm/fork-main/templates/index.html" "KEY=9b69****************************" "SECRET=de01****************************" "USERNAME=XXX" "LIMIT=10" "YANDEX_URL=https://music.yandex.ru/users/Invisible-XXX/artists"

[Install]
WantedBy=multi-user.target
  ```
</details>

Then after you have created the service file run this command:
```
sudo systemctl daemon-reload && sudo systemctl start gylm.service
```

* Add gylm.conf to /etc/nginx/sites-available

```
server {
    server_name SERVER_IP;
    location / {
        proxy_pass http://localhost:1984/yandex;
        expires    0;
    }
}
```

* Create symlink:
```
sudo ln -s /etc/nginx/sites-available/gylm.conf /etc/nginx/sites-enabled/
```
* Restart Nginx:

```
sudo nginx -t && sudo systemctl restart nginx.service
```

# Adding to Readme file

You can now use the following in your readme:
```
[![yandex](http://SERVER_IP)](https://music.yandex.ru/users/Invisible-XXX/albums)
```

As an example

# Demo
[![Author Original](https://fargos.dns.army:3583)](https://github.com/Innsmouth-trip/gylm)