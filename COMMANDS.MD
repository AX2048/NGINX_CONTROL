## NGINX :: CONTROL

COMMANDS FROM ROOT FOLDER

Files: `DOMAINS.txt`, `Templates/index-teplate.html`, `Templates/nginx_template80.conf`, `Templates/nginx_template80.conf`

Dirs: `TEST_ETC/nginx/sites-available/`, `TEST_ETC/nginx/site-enabled`,

1. DIG `DOMAINS.txt` ::
```
cat DOMAINS.txt | while read i;do echo $i && dig a +short $i;done;
```

2. Create empty files `[domain].conf` in `TEST_ETC/nginx/sites-available` from `DOMAINS.txt` ::
```
cat DOMAINS.txt | while read i; do touch TEST_ETC/nginx/sites-available/$i\.conf; done;
```

3. Make symlinks for all from `/sites-available` to `/sites-enabled` ::
```
la TEST_ETC/nginx/sites-available/ | while read i; do ln -s TEST_ETC/nginx/sites-available/$i TEST_ETC/nginx/sites-enabled/$i; done;

cat DOMAINS.txt | while read i; do ln -s TEST_ETC/nginx/sites-available/$i TEST_ETC/nginx/sites-enabled/$i; done;

ll TEST_ETC/nginx/sites-available/
ll TEST_ETC/nginx/sites-enabled/
```

4. Make directory in `TEST_USR_SHARE/nginx` for all domains in `DOMAINS.txt`, dir = `[domain]/public/` ::
```
cat DOMAINS.txt | while read i; do mkdir -v TEST_USR_SHARE/nginx/$i; mkdir -v TEST_USR_SHARE/nginx/$i/public; done;
```

5. Make `index.html` frome template in `Tamplates/index-teplate.html` for all domains in `DOMAINS.txt` to `/TEST_USR_SHARE/nginx[domain]/public/` ::
```
cat DOMAINS.txt | while read i; do sed "s/TEMP_DOM/$i/g" Templates/index-teplate.html | tee TEST_USR_SHARE/nginx/$i/public/index.html; done;
```

6. Make config `http:80` for all `TEST_ETC/nginx/sites-available/` from `Templates/nginx_template80.conf` ::
```
la TEST_ETC/nginx/sites-available/ | while read i; do sed "s/tm_domain/$i/g" Templates/nginx_template80.conf | tee TEST_ETC/nginx/sites-available/$i; done;

cat DOMAINS.txt | while read i; do sed "s/tm_domain/$i/g" Templates/nginx_template80.conf | tee TEST_ETC/nginx/sites-available/$i.conf; done;
```

7. After add SSL certs!!! Edit path fore certs in `nginx_template443CRT.conf`! Make config `https:443` for all `TEST_ETC/nginx/sites-available/` from `Templates/nginx_template443CRT.conf` ::
```
la TEST_ETC/nginx/sites-available/ | while read i; do sed "s/tm_domain/$i/g" Templates/nginx_template443CRT.conf | tee TEST_ETC/nginx/sites-available/$i; done;

cat DOMAINS.txt | while read i; do sed "s/tm_domain/$i/g" Templates/nginx_template443CRT.conf  | tee TEST_ETC/nginx/sites-available/$i.conf; done;
```
8. CLEAR ::
```
rm -rfv TEST_USR_SHARE/nginx/*; rm -rfv TEST_ETC/nginx/sites-available/*; rm -rfv TEST_ETC/nginx/sites-enabled/*
```

9. ALL COMMANDS SHORT ::
```
cat DOMAINS.txt | while read i;do echo $i && dig a +short $i;done;
cat DOMAINS.txt | while read i; do touch TEST_ETC/nginx/sites-available/$i\.conf; done;
cat DOMAINS.txt | while read i; do ln -s TEST_ETC/nginx/sites-available/$i TEST_ETC/nginx/sites-enabled/$i; done;
cat DOMAINS.txt | while read i; do mkdir -v TEST_USR_SHARE/nginx/$i; mkdir -v TEST_USR_SHARE/nginx/$i/public; done;
cat DOMAINS.txt | while read i; do sed "s/TEMP_DOM/$i/g" Templates/index-teplate.html | tee TEST_USR_SHARE/nginx/$i/public/index.html; done;
cat DOMAINS.txt | while read i; do sed "s/tm_domain/$i/g" Templates/nginx_template80.conf | tee TEST_ETC/nginx/sites-available/$i.conf; done;

rm -rfv TEST_USR_SHARE/nginx/*; rm -rfv TEST_ETC/nginx/sites-available/*; rm -rfv TEST_ETC/nginx/sites-enabled/*

rm -rfv BATTLE_NGINX/usr_share_nginx/*; rm -rfv BATTLE_NGINX/etc_nginx_siteAVA/*; rm -rfv BATTLE_NGINX/etc_nginx_siteENA/*
```

10. Copy to system NGINX ::
```
cp -avr TEST_USR_SHARE/nginx/* BATTLE_NGINX/usr_share_nginx/
cp -avr TEST_ETC/nginx/sites-available/* BATTLE_NGINX/etc_nginx_siteAVA/

la TEST_ETC/nginx/sites-available/ | while read i; do ln -s BATTLE_NGINX/etc_nginx_siteAVA/$i BATTLE_NGINX/etc_nginx_siteENA/$i; done;
```

---

## NGINX :: ADMIN
```
sudo nginx -t

sudo systemctl status nginx

sudo systemctl reload nginx

sudo systemctl start nginx

sudo systemctl stop nginx

sudo nginx -T | grep "server_name "
sudo grep server_name /etc/nginx/sites-enabled/* -RiI | awk '{print $3}'
```

---
## NGINX :: OTHER

CONTENT DEFAULT (`site/public/`) :: `/usr/share/nginx/`

FIRST CONFIG :: `/etc/nginx/nginx.conf`
SITE AVALIBLE (`.conf`) :: `/etc/nginx/sites-available/`
SITE ENABLED (`ln -s`) :: `/etc/nginx/sites-enabled/`

LOG :: `/var/log/nginx/`


---

## NGINX DOMAINS VI

```
sudo nginx -T | grep "server_name "

grep server_name /etc/nginx/sites-enabled/* -RiI

nginx -T | sed -r -e 's/[ \t]*$//' -e 's/^[ \t]*//' -e 's/^#.*$//' -e 's/[ \t]*#.*$//' -e '/^$/d' | \
sed -e ':a;N;$!ba;s/\([^;\{\}]\)\n/\1 /g' | \
grep -P 'server_name[ \t]' | grep -v '\$' | grep '\.' | \
sed -r -e 's/(\S)[ \t]+(\S)/\1\n\2/g' -e 's/[\t ]//g' -e 's/;//' -e 's/server_name//' | \
sort | uniq | xargs -L1

nginx_domains(){
    domains=`find /etc/nginx/ -type f -name "*.conf" -print0 | xargs -0 egrep '^(\s|\t)*server_name' | sed -r 's/(.*server_name\s*|;)//g'`
    for domain in $domains; do
       echo $domain;
    done
}

sudo nginx -T | grep "server_name " | sed 's/.*server_name \(.*\);/\1/' | sed 's/ /\n/'

sudo grep server_name /etc/nginx/sites-enabled/* -RiI | awk '{print $3}'
```
