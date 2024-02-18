# Автор: Федорчук Дмитрий Сергеевич DEVOPS-33

# Домашнее задание к занятию «Микросервисы: подходы»

Вы работаете в крупной компании, которая строит систему на основе микросервисной архитектуры.
Вам как DevOps-специалисту необходимо выдвинуть предложение по организации инфраструктуры для разработки и эксплуатации.


## Задача 1: Обеспечить разработку

Предложите решение для обеспечения процесса разработки: хранение исходного кода, непрерывная интеграция и непрерывная поставка. 
Решение может состоять из одного или нескольких программных продуктов и должно описывать способы и принципы их взаимодействия.

Решение должно соответствовать следующим требованиям:
- облачная система;
- система контроля версий Git;
- репозиторий на каждый сервис;
- запуск сборки по событию из системы контроля версий;
- запуск сборки по кнопке с указанием параметров;
- возможность привязать настройки к каждой сборке;
- возможность создания шаблонов для различных конфигураций сборок;
- возможность безопасного хранения секретных данных (пароли, ключи доступа);
- несколько конфигураций для сборки из одного репозитория;
- кастомные шаги при сборке;
- собственные докер-образы для сборки проектов;
- возможность развернуть агентов сборки на собственных серверах;
- возможность параллельного запуска нескольких сборок;
- возможность параллельного запуска тестов.

Обоснуйте свой выбор.

## Задача 2: Логи

Предложите решение для обеспечения сбора и анализа логов сервисов в микросервисной архитектуре.
Решение может состоять из одного или нескольких программных продуктов и должно описывать способы и принципы их взаимодействия.

Решение должно соответствовать следующим требованиям:
- сбор логов в центральное хранилище со всех хостов, обслуживающих систему;
- минимальные требования к приложениям, сбор логов из stdout;
- гарантированная доставка логов до центрального хранилища;
- обеспечение поиска и фильтрации по записям логов;
- обеспечение пользовательского интерфейса с возможностью предоставления доступа разработчикам для поиска по записям логов;
- возможность дать ссылку на сохранённый поиск по записям логов.

Обоснуйте свой выбор.

## Задача 3: Мониторинг

Предложите решение для обеспечения сбора и анализа состояния хостов и сервисов в микросервисной архитектуре.
Решение может состоять из одного или нескольких программных продуктов и должно описывать способы и принципы их взаимодействия.

Решение должно соответствовать следующим требованиям:
- сбор метрик со всех хостов, обслуживающих систему;
- сбор метрик состояния ресурсов хостов: CPU, RAM, HDD, Network;
- сбор метрик потребляемых ресурсов для каждого сервиса: CPU, RAM, HDD, Network;
- сбор метрик, специфичных для каждого сервиса;
- пользовательский интерфейс с возможностью делать запросы и агрегировать информацию;
- пользовательский интерфейс с возможностью настраивать различные панели для отслеживания состояния системы.

Обоснуйте свой выбор.

## Задача 4: Логи * (необязательная)

Продолжить работу по задаче API Gateway: сервисы, используемые в задаче, пишут логи в stdout. 

Добавить в систему сервисы для сбора логов Vector + ElasticSearch + Kibana со всех сервисов, обеспечивающих работу API.

### Результат выполнения: 

docker compose файл, запустив который можно перейти по адресу http://localhost:8081, по которому доступна Kibana.
Логин в Kibana должен быть admin, пароль qwerty123456.


## Задача 5: Мониторинг * (необязательная)

Продолжить работу по задаче API Gateway: сервисы, используемые в задаче, предоставляют набор метрик в формате prometheus:

- сервис security по адресу /metrics,
- сервис uploader по адресу /metrics,
- сервис storage (minio) по адресу /minio/v2/metrics/cluster.

Добавить в систему сервисы для сбора метрик (Prometheus и Grafana) со всех сервисов, обеспечивающих работу API.
Построить в Graphana dashboard, показывающий распределение запросов по сервисам.

### Результат выполнения: 

docker compose файл, запустив который можно перейти по адресу http://localhost:8081, по которому доступна Grafana с настроенным Dashboard.
Логин в Grafana должен быть admin, пароль qwerty123456.

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---


## Решение 1. Обеспечить разработку

Считаю, что для требуемых условий наиболее предпочтительным вариантов является GitLab.

* Его можно развернуть как в облаке на арендуемом сервере, так и у себя в локальной сети, либо применить в закрытом контуре сети предприятия, для обеспечения большей безопасности.

* Секретные данные можно не писать в открытом виде, а использовать переменные, либо подключить внешние хранилища секретных данных, например Hashicorp Vault.

* GitLab построен на системе контроля версий Git.

* Начиная с версии 8 имеет в себе встроенный Docker Registry, что позволяет создавать и хранить собственные Docker образы сборок.

* Начиная с версии 13 можно создавать неограниченное число репозиториев, как для бесплатной версии, так и для платной. В свою очередь репозитории можно объединять их в группы для более удобной навигации между ними.

* В редакторе gitlab-ci можно писать пайплайны как для сборки проектов, так и для их тестирования. Нет разницы какой функционал будет выполнять пайплайн. Все процессы можно описать в одном файле .gitlab-ci.yml, в котором можно реализовать сколько угодно шаблонов выполнения с разными настройками и разным количеством шагов.

* Для выполнения сборок используются GitLab Runner, которые можно разместить в разных сегментах сети и для разных сборок. А также можно регулировать количество одновременных сборок, то есть чем больше Runner'ов подключено к проекту, тем больше одновременных сборок или тестов можно запустить.

В качестве альтернативы GitLab CI/CD можно использовать Jenkins. Он также может использовать Git и Docker Registry с помощью подключаемых плагинов, может запускать множество пайплайнов, в зависимости от свободных агентов сборки, позволяет безопасно хранить секретные данные.

## Решение 2: Логи

Считаю, что для требуемых условий возможно использовать Fluentd + ELK-стэк.

* Fluentd может собирать данные из различных источников, обрабатывать их и направлять в различные хранилища данных.

* Logstash будет принимать логи от агентов Fluentd и преобразовывать логи в единому формату.

* Elasticsearch будет хранить логи, для обеспечения отказоустойчивости возможно создать Elasticsearch-кластер.

* Kibana будет удобно отображать логи в web-интерфейсе. Используя Kibana можно создавать различные Dashboard для удобного просмотра логов, подсчета каких-либо событий, использовать различные временные метки.

В качестве альтернативы Logstash можно использовать Apache Kafka. Он также получает данные их различных источников, обеспечивает высокую скорость обработки данных, поддерживает кластеризацию. Можно разделить получаемые данные по разным топикам, для топиков можно настроить разное время хранения данных и их объем. Для передачи данных из Kafka в Elasticsearch используется специальный коннектор "Kafka Connect Elasticsearch Sink Connector".

## Решение 3: Мониторинг

Считаю, что для требуемых условий возможно использовать связку Prometheus + Grafana.

* Prometheus - это система мониторинга и оповещения с открытым исходным кодом. Позволяет собирать метрики с различных источников, может выполнять агрегацию данных, быстро получать эти данные используя запросы к базе, визуализировать данные и оповещать о проблемах в сервисах на основе полученных метрик. Prometheus получает данные от различных агентов, например, может получить метрики загрузки CPU от node_exporter или метрики Docker контейнеров от Cadvisor. Также может работать совместно с Telegraf, который в свою очередь способен получать метрики из очень большого множества источников.

* Grafana - web-интерфейс для визуализации данных. Может строить графики, Dashboard, визуализирует данные из большого числа систем мониторинга и большого числа разных метрик. К нему можно подключить большое количество источников, таких как Telegraf, Prometheus, InfluxDB и т.д.

# Не обязательные задания решил пока что не выполнять.