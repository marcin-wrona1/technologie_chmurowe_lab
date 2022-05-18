# Zadanie 10.2.

## Punkt 2. - Parametry `swarm`

* Dla usług `nginx`, `php`, `phpmyadmin` - włączamy replikację.
* Dla `mariadb` ustawiamy brak replikacji i uruchomienie usługi na węźle będącym menadżerem klastra. Nie możemy replikować bazy danych ponieważ wtedy poszczególne węzły posiadałyby własną, całkowicie odrębną jej kopię. Jedyną kopię tej usługi uruchamiamy na menadżerze, aby połączenie z bazą było dostępne na wszystkich węzłach.
* Politykę restartu dla wszystkich usług ustawiamy na wartość domyślną - nie mamy potrzeby jej zmiany, ale musi ona zostać zdefiniowana według treści zadania.
