# jBASE
## История развития
1989 год:  
Соучредители jBASE Клайв Кеттеридж и Джим Айдл оставили свою многообещающую карьеру в McDonnell Douglas Information Systems в Хемел-Хемпстеде, Англия, чтобы сделать что-то великое. Их страстью и единственной миссией было создание действительно независимой системы баз данных. В то время базы данных создавались с использованием собственных проприетарных файловых систем и средств разработки, что показалось основателям устаревшей стратегией, ограничивающей гибкость в будущем. Когда была запущена jBASE, это был принципиально иной архитектурный подход к системам управления базами данных, и ее популярность резко возросла.  
1998 год:  
TEMENOS Group AG, публичная компания по разработке программного обеспечения для банков, базирующаяся в Швейцарии, имела значительные планы по развитию своей банковской системы TEMENOS GLOBUS™ и стала клиентом jBASE  
1999 год:  
TEMENOS приобретает 100% акций jBASE  
2010 год:
Была выпущена версия 5 jBASE, ставшая знаковым событием в разработке баз данных MultiValue. Настоящая 64-разрядная реализация jBASE 5 обеспечивает непрерывную работу и представляет собой по-настоящему передовую платформу. jBASE версии 5 включает новые интерфейсы прикладного программирования (API), отказоустойчивые файлы, автоматическое изменение размера файлов и повышенную отказоустойчивость благодаря внедрению Dataguard, что позволяет jBASE быть действительно непрерывной базой данных MultiValue.  
2015 год:  
Zumasys приобретает базу данных jBASE  
2016 год:  
jBASE 5.5 позволяет приложениям стать нативными и, таким образом, понятными для основных разработчиков и новых ИТ-специалистов, которые могут не иметь навыков MultiValue.  
2017 год:  
jBASE 5.6 предлагает собственные службы RESTful, собственное шифрование данных, более детальное ведение журнала аудита и улучшенное ведение журнала транзакций.  
2018 год:  
jBASE 5.7 включает в себя множество новых новаторских функций, включая динамические файлы, динамические объекты, профили и службы RESTful.  
2021 год:  
MongoDB jEDI, сертификация Docker Container и новые настройки эмуляции делают jBASE 5.8 проще, чем когда-либо, для дилеров MultiValue и независимых поставщиков программного обеспечения для самостоятельной установки и самостоятельного управления.  

## Инструменты для взаимодействия 
JDBC  
ODBC  
Proprietary protocol  
RESTful HTTP API  
SOAP-based API  

## Database engine
Multivalue. Почти как SQL, только может хранить несколько значений в одной ячейке данных

## Развертывание и запросы
Разверывать будем на убунте. Со страницы https://www.zumasys.com/downloads/ с вкладки Server -> Linux скачиваем bin файл. Далее следуем инструкции https://docs.zumasys.com/jbase/administration/installation-guides/linux/linux-installation-guide/ (пропускаем пункт про лицензию, так как по умолчанию нам дадут лицензию на 30 дней)  
После установки у нас запуститься `jsh`. Создаем файл:
```
jsh JBASEADM ~ -->create-file test type=UD
``` 
Далее создадим файл с запросами, который добавит тестовых данных:
```
jsh JBASEADM ~ -->jed test test.demo
```
Вставляем туда все из файла test.demo в этом репозитории, нажимаем \<Esc\> и пишем FIBC. Далее запускаем написанный скрипт:
```
jsh JBASEADM ~ -->run test test.demo
How many records do you want : 200
What filename would you like : DEMO.FILE
```
Получаем данные, которые можем проверить таким образом:
```
jsh JBASEADM ~ -->list DEMO.FILE FIRSTNAME LASTNAME
PAGE    1                                                                20:26:11  03 MAY 2022

DEMO.FILE.....    FIRSTNAME...............    Last Name...........

014               DELLA                       TRAVERS
048               JASON                       WATERS
058               HERVE                       MURPHY
084               JOHN                        JOHNSON
124               ALAN                        GRABBIT
169               RAPH                        LAMBERT
178               DAVID                       MURPHY
186               JOHN                        BLACK
193               ERIC                        WILLIS
002               TED                         GIBSON
017               AMANDA                      HARRIS
049               PHILIP                      JONES
085               STEVE                       BLACK
127               STEVE                       WALKER
132               DAVID                       JACKSON
168               BRUCE                       WALKER
179               DOUG                        BAILEY
...
```

## Распределение по разным насителям
Сервер с базой данных может иметь резервный сервер, куда она будет копировать данный. `jbackup` инициирует резервное копирование базы. Которое переносит журналы транзакций на резервный сервер и там с их помощью обнавляет у себя данные  
![restore_server](https://docs.zumasys.com/assets/img/Picture9.c43d0e94.png)  

Также можно настроить кластер приложений, с единым хранилищем данных.  
При такой конфигурации jBASE будет единственным сервером базы данных. Связь между сервером(ами) приложений и сервером базы данных будет осуществляться с помощью jRFS внутри jBASE. Это позволяет нескольким серверам приложений иметь указатели в качестве определений файлов. Эти указатели ссылаются на файлы, существующие на сервере базы данных. Механизмы jRFS позволяют обновлять базу данных через серверные процессы jRFS на основе запросов, сделанных на серверах приложений. Следствием этого является то, что каждый сервер приложений не имеет прямого индивидуального хранилища базы данных, но имеет общий доступ к центральной базе данных (jBASE). Поскольку имеется только один сервер базы данных, будут доступны средства журналирования транзакций, использующие те же механизмы, что и в системе Stand-Alone
![cluster_system](https://docs.zumasys.com/assets/img/Picture8.b0c696c9.png)

## Язык программирования
jBASE написана на "C"

## Индексы
для создания индексов есть команда `create-index`.
Эту команду можно использовать для создания нового или обновления существующего. В любом случае индекс будет помечен как «не синхронизированный» и будет недоступен для query-index , key-select или любой из команд jQL, таких как SORT или SELECT. Для синхронизации нужно воспользоваться командой `rebuild-index`. Есть мнжоство параметров создания индекса: можно создать индекс нечувствительный к регисту, можно ограничить multivalue используемое в ключе и др. Пример, который создает индекс основанный на объединении первого и второго атрибутов:
```
jsh -->create-index -a -w filename indexname by 1 : 2
```
`-a` - разу запускает rebuild-index  
`-w` - Индекс будет храниться в режиме постоянной записи (Permanent Write Mode)  
`delete-index` - удаляет индекс
`query-index` - позваляет получать, сортировать, фильтровать данные связанные с индексом:
```
query-index -Options filename index_name
```
Есть и другие команды связанные с индексами, о которых можно почитать здесь https://docs.zumasys.com/jbase/indexes/#indexes


## Как строится процесс выполнения запросов в вашей СУБД?
Традиционная для современных СУБД архитектура: сервер СУБД представляет собой виртуальную машину баз данных. Все пользовательские процессы обращаются с запросами к серверу базы данных, который управляет виртуальной памятью, дисковым пространством.  
Подобную архитектуру используют многие СУБД. Она предоставляет широкие возможности для оптимизации запросов. Для хранения данных операционной системой выделяется только некое дисковое пространство (Raw Disk) и оперативная память. Все управление выделением и освобождением памяти, сборкой мусора, регистрацией пользователей, средствами защиты от несанкционированного доступа и другими подобными процессам занимается сервер базы данных.  
jBASE все подобные функции отдает операционной системе под которой они функционируют, оставляя за собой функции выполнения запросов и обработки данных. Более того, фактически все запросы выполняет сам пользовательский процесс, оставляя серверу базы данных функции арбитра системных и пользовательских блокировок. Таким образом, для вызова пользовательского процесса система не выделяет первоначально больших ресурсов, производится только регистрация процесса в разделяемой памяти. Такая архитектура легковесных процессов, несмотря на кажущиеся недостатки, имеет большое количество преимуществ. Возможность построения распределенных систем в кластерах, естественная масштабируемость в SMP и MPP архитектурах, простота использования в WEB технологиях, позволила разработчикам СУБД сконцентрировать свое внимание на функциональности систем и получить значительный выигрыш на рынке Multivalue систем. Кроме того, такой подход предоставляет наибольшую степень открытости систем и, соответственно, лучшие условия для интеграции.

## Транзакции
jBASE поддерживает транзакции. Для этого есть следующие запросы:  
`TRANSTART` Отмечает начало транзакции  
`TRANSEND` Отмечает конец успешно завершенной транзакции  
`TRANSABORT` Прерывает текущую транзакцию и отменяет любые обновления баз данных.  
`TRANSQUERY` Определяет, активна ли транзакция в текущем процессе.  

## Восстановление
Есть утилита `ACCOUNT-SAVE` с помощью, которой можно сохранить данные в различных форматах для совместимости с разными системами. И также есть утилита `ACCOUNT-RESTORE`, которая загрузит сохранение и восстановит данные. Но данные утилиты медленные и не подходят для постоянного резервного копирования. Для этого есть `jbackup`, которая уже обеспечивает быстрое резервное копирование и может использоваться для проверки целостности данных. Для восстановления из такого бэкапа используется утилита `jrestore`. Может использоваться как для восстановления из одной директории, так для нескольких. После восстановления будет, также восстановлена информация об индексах, для восстановления данных связанных с ними есть утилита `rebuild-index`

## Шардинг
Не нашел никакой информации про шардирование данной базы данных ни в официальной документации, ни на просторах интернета

## Data Mining, Data Warehousing и OLAP
Одним из основных хранилищ данных для OLAP является SQL Server от Microsoft.
Продукт jBASE mv.SSIS предоставляет комплексное решение для извлечения данных из jBASE и последующей полной интеграции с пакетом Microsoft SSIS. В результате данные из jBASE переносятся в таблицы SQL Server без каких-либо усилий по программированию.

## Защита
В jBASE 5.6 была представлена встроенная функция шифрования, которая затем была улучшена в jBASE 5.7.2. Эта функция позволяет jBASE шифровать файлы данных в состоянии покоя таким образом, что только лица, имеющие ключ для расшифровки, могут расшифровать и прочитать данные.  
Процесс шифрования данных включает в себя:

* Создание профиля безопасности базы данных или мастер-ключа.
* Создание ключа шифрования или парольной фразы для файлов.
* Шифрование данных с помощью ключей.

## Развитие
Развитием базы данных занимается компания Zumasys. Не имеет открытого исходного кода, и является продуктом данной компании. Соответсвенно сторонние разработчики не могут напрямую влиять на процесс развития jBASE

## Демо
Создадим файл test_test:
```
jsh -->CREATE-FILE test_test 1 11
```
С помощью JED напишем скрипт, который его заполнит его:
```
0001 OPEN "test_test" TO M ELSE ABORT 201, "can't open test_test"
0002
0003 FOR I = 1 TO 5
0004         CRT "*":
0005         REC = ""
0006         REC<1> = "val1_":I
0007         REC<2> = "val2_":I
0008         REC<3> = "val3_":I
0009 *
0010         WRITE REC ON M, I "R%3"
0011     NEXT I
```
Выведем полученные данные:
```
jsh -->list test_test *A1 *A2 *A3
PAGE    1

test_test.....    *A1...........    *A2...........    *A3...........

001               val1_1            val2_1            val3_1
003               val1_3            val2_3            val3_3
002               val1_2            val2_2            val3_2
005               val1_5            val2_5            val3_5
004               val1_4            val2_4            val3_4
```
Теперь можем дать имя для первого значения в списке. Для этого открываем файл:
```
jsh -->jed dict test_test name
```
В конце указываем название, которое хотим, в данном случае "name". Пишем в него:
```
0001 A
0002 1
0003
0004
0005
0006
0007
0008
0009 L
0010 15
```
Здесь нам важен второй атрибут, который отвечает за номер элемента в списке, мы хотим привязать к первому. Нажимаем \<Esc\> и пишем `FI`. После этого запускаем вывод:
```
jsh -->list test_test name
PAGE    1

test_test.....    name...........

001               val1_1
003               val1_3
002               val1_2
005               val1_5
004               val1_4
```
При помощи JED и команды `delete` можем удалить запись:
```
0001 OPEN "test_test" TO M ELSE ABORT 201, "can't open test_test"
0002
0003 DELETE M, "001"
```
Проверим, что получилось:
```
jsh -->list test_test name *A2 *A3
PAGE    1

test_test.....    name...........    *A2...........    *A3...........

003               val1_3             val2_3            val3_3
002               val1_2             val2_2            val3_2
005               val1_5             val2_5            val3_5
004               val1_4             val2_4            val3_4
```
Можем добавить данные вручную:
```
jsh -->jed test_test rec1
0001 rec_val1
0002 rec_val2
0003 rec_val3
```
Далее нажимаем \<Esc\> и пишем `FI`. Проверяем:
```
jsh -->list test_test name *A2 *A3
PAGE    1

test_test.....    name...........    *A2...........    *A3...........

003               val1_3             val2_3            val3_3
002               val1_2             val2_2            val3_2
005               val1_5             val2_5            val3_5
004               val1_4             val2_4            val3_4
rec1              rec_val1           rec_val2          rec_val3
```
Аналогично через JED можем поменять эти данные:
```
jsh -->jed test_test rec1
0001 rec_val1
0002 rec_changed_val2
0003 rec_val3
```
Получаем:
```
jsh JBASEADM ~ -->list test_test name *A2 *A3
PAGE    1

test_test.....    name...........    *A2...........      *A3...........

003               val1_3             val2_3              val3_3
002               val1_2             val2_2              val3_2
005               val1_5             val2_5              val3_5
004               val1_4             val2_4              val3_4
rec1              rec_val1           rec_changed_val2    rec_val3
```
Или можно удалить данные:
```
jsh -->jed test_test rec1
0001 rec_val1
0002 
0003 rec_val3
```
Получаем:
```
jsh JBASEADM ~ -->list test_test name *A2 *A3
PAGE    1

test_test.....    name...........    *A2...........      *A3...........

003               val1_3             val2_3              val3_3
002               val1_2             val2_2              val3_2
005               val1_5             val2_5              val3_5
004               val1_4             val2_4              val3_4
rec1              rec_val1                               rec_val3
```

## Самостоятельное изучание и документация
Документацию можно найти здесь https://docs.zumasys.com/jbase/  
На их youtube-канале так же есть туториалы https://www.youtube.com/playlist?list=PLVXwKjeQzt18HPkw2g0_hlW7sj3hQNyos

## Как быть в курсе происходящего
Здесь можно найти release notes https://docs.zumasys.com/jbase/release-notes/  
Здесь периодически публикую новости связанные с jBASE https://www.jbase.com/news/
