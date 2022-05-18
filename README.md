# Zadanie 10.2.

## Punkt 2. - Parametry `swarm`

* Dla usług `nginx`, `php`, `phpmyadmin` - włączamy replikację i ustawiamy (przykładowo) ilość kopii usługi. Ustawiamy równą ilość usług `nginx` i `php`, ponieważ są ze sobą powiązane (serwer `nginx` używa interpretera `php` podczas serwowania stron).
* Ponieważ oprogramowanie `phpMyAdmin` będzie używane jedynie przez administratorów bazy danych, uruchamiamy tylko jedną kopię tej usługi.
* Dla `mariadb` ustawiamy brak replikacji i uruchomienie usługi na węźle będącym menadżerem klastra. Nie możemy replikować bazy danych ponieważ wtedy poszczególne węzły posiadałyby własną, całkowicie odrębną jej kopię. Jedyną kopię tej usługi uruchamiamy na menadżerze, aby połączenie z bazą było dostępne na wszystkich węzłach.
* Politykę restartu dla wszystkich usług ustawiamy na wartość domyślną - nie mamy potrzeby jej zmiany, ale musi ona zostać zdefiniowana według treści zadania.

## Punkt 3.

Skalować możemy wszystkie bezstanowe usługi. W naszym przypadku są to wszystkie usługi z wyłączeniem bazy danych (usługa `mariadb`). Baz danych nie należy replikować za pomocą funkcjonalności `swarm` oprogramowania Docker, ponieważ nie została ona do tego przystosowana, a same bazy danych posiadają własne rozwiązania pozwalające na replikację.
