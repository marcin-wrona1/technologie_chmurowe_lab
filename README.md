# Zadanie 10.2.

## Punkt 2. - Parametry `swarm`

* Dla usług `nginx`, `php`, `phpmyadmin` - włączamy replikację i ustawiamy (przykładowo) ilość kopii usługi. Ustawiamy równą ilość usług `nginx` i `php`, ponieważ są ze sobą powiązane (serwer `nginx` używa interpretera `php` podczas serwowania stron).
* Ponieważ oprogramowanie `phpMyAdmin` będzie używane jedynie przez administratorów bazy danych, uruchamiamy tylko jedną kopię tej usługi.
* Dla `mariadb` ustawiamy brak replikacji i uruchomienie usługi na węźle będącym menadżerem klastra. Nie możemy replikować bazy danych ponieważ wtedy poszczególne węzły posiadałyby własną, całkowicie odrębną jej kopię. Jedyną kopię tej usługi uruchamiamy na menadżerze, aby połączenie z bazą było dostępne na wszystkich węzłach.
* Politykę restartu dla wszystkich usług ustawiamy na wartość domyślną - nie mamy potrzeby jej zmiany, ale musi ona zostać zdefiniowana według treści zadania.

## Punkt 3.

Skalować możemy wszystkie bezstanowe usługi. W naszym przypadku są to wszystkie usługi z wyłączeniem bazy danych (usługa `mariadb`). Baz danych nie należy replikować za pomocą funkcjonalności `swarm` oprogramowania Docker, ponieważ nie została ona do tego przystosowana, a same bazy danych posiadają własne rozwiązania pozwalające na replikację.

## Użyte polecenia i potwierdzenie działania

Tworzymy nowy klaster:

```sh
[repo]:% docker swarm init
Swarm initialized: current node (hlai4aaxeifizhz63w9jlp938) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-2mh5f68x3h8nu8pq657w9qfx9xwpl9f2ed1hqnosyi8yw32pzu-dat3njxpiz5sky8uyai34i2ey 192.168.65.3:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

Uruchamiamy nasze usługi:

```sh
[repo]:% docker stack deploy --compose-file docker-stack.yml lab10
Creating network lab10_frontend
Creating network lab10_backend
Creating service lab10_phpmyadmin
Creating service lab10_nginx
Creating service lab10_php
Creating service lab10_mysql
```

Sprawdzamy poprawność działania:

```sh
[repo]:% # stan klastra
[repo]:% docker info | awk '/^ [A-z]+.*: / {p = 0} /^ Swarm: / {p = 1} { if (p == 1) print [repo]:%0 }'
 Swarm: active
  NodeID: hlai4aaxeifizhz63w9jlp938
  Is Manager: true
  ClusterID: yllmn3amuif7zfascf8p5serr
  Managers: 1
  Nodes: 1
  Default Address Pool: 10.0.0.0/8
  SubnetSize: 24
  Data Path Port: 4789
  Orchestration:
   Task History Retention Limit: 5
  Raft:
   Snapshot Interval: 10000
   Number of Old Snapshots to Retain: 0
   Heartbeat Tick: 1
   Election Tick: 10
  Dispatcher:
   Heartbeat Period: 5 seconds
  CA Configuration:
   Expiry Duration: 3 months
   Force Rotate: 0
  Autolock Managers: false
  Root Rotation In Progress: false
  Node Address: 192.168.65.3
  Manager Addresses:
   192.168.65.3:2377
[repo]:% # wyświetlamy serwowaną stronę (fragment)
[repo]:% w3m -dump http://localhost:6666 | head -n 20
PHP logo

PHP Version 8.1.6

System        Linux 6aea13fcb4b3 5.10.104-linuxkit #1 SMP Thu Mar 17 17:08:06
              UTC 2022 x86_64
Build Date    May 13 2022 22:28:12
Build System  Linux e8995c7a7cf3 5.10.0-13-cloud-amd64 #1 SMP Debian 5.10.106-1
              (2022-03-17) x86_64 GNU/Linux
              './configure' '--build=x86_64-linux-gnu' '--with-config-file-path
              =/usr/local/etc/php' '--with-config-file-scan-dir=/usr/local/etc/
              php/conf.d' '--enable-option-checking=fatal' '--with-mhash'
              '--with-pic' '--enable-ftp' '--enable-mbstring'
Configure     '--enable-mysqlnd' '--with-password-argon2' '--with-sodium=
Command       shared' '--with-pdo-sqlite=/usr' '--with-sqlite3=/usr'
              '--with-curl' '--with-iconv' '--with-openssl' '--with-readline'
              '--with-zlib' '--disable-phpdbg' '--with-pear' '--with-libdir=lib
              /x86_64-linux-gnu' '--disable-cgi' '--enable-fpm'
              '--with-fpm-user=www-data' '--with-fpm-group=www-data'
              'build_alias=x86_64-linux-gnu'
[repo]:% # sprawdzamy działanie phpMyAdmin
[repo]:% w3m -dump http://localhost:6001 | grep Welcome
Welcome to phpMyAdmin
```
