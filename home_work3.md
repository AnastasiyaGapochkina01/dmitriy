0) Установить и разобраться с putty (https://putty.org.ru/)
1) Подключиться к тестовой машине
```
ip: 195.133.199.48
login: dmitriy
pass: 0000
```
2) Подключиться к контейнеру
```
docker exec -it build_from_sorce /bin/bash
```
3) Перейти в директорию /usr/src/php
4) Выполнить команды
```
export gnuArch="$(dpkg-architecture --query DEB_BUILD_GNU_TYPE)"
export debMultiarch="$(dpkg-architecture --query DEB_BUILD_MULTIARCH)"
```
5) Сконфигурировать Makefile
```
./configure \
  --host="${gnuArch}" \
  --with-libdir="/lib/${debMultiarch}/" \
  --with-config-file-path="${PHP_INI_DIR}" \
  --with-config-file-scan-dir="${PHP_INI_DIR}/conf.d" \
  --disable-cgi \
  --enable-ftp \
  --enable-mbstring \
  --enable-mysqlnd \
  --enable-fpm \
  --with-fpm-user=www-data \
  --with-fpm-group=www-data \
  --with-mhash \
  --with-pdo-sqlite=/usr \
  --with-sqlite3=/usr \
  --with-curl=/usr/local \
  --with-openssl=/usr/local/ssl \
  --with-readline \
  --with-recode \
  --with-zlib
```
6) Выполнить сборку
```
make
```
Она завалится примерно так
```
collect2: error: ld returned 1 exit status
Makefile:260: recipe for target 'sapi/fpm/php-fpm' failed
make: *** [sapi/fpm/php-fpm] Error 1
```

Это сделано специально :) Ему нужна библиотека libcurl4-gnutls-dev. Нужно установить ее с помощью apt и повторить ```make```
7) После успешной сборки выполнить 
```
make install
```
8) Выполнить
```
php -v
```
должно вывести версию установленного php

!!! ЕСЛИ ```docker exec -it build_from_sorce /bin/bash ``` ругается что нет такого контейнера, то запустить его, выполнив ``` docker run -d -it --name build_from_sorce test_php```
