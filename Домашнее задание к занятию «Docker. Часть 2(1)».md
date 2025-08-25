# Домашнее задание к занятию «Docker. Часть 2 (1)»  Петров Петр

### Задание 1
Напишите ответ в свободной форме, не больше одного абзаца текста.
Установите Docker Compose и опишите, для чего он нужен и как может улучшить вашу жизнь.
### Решение 1
Docker Compose — это инструмент, который позволяет легко управлять многоконтейнерными приложениями в Docker. С его помощью можно описать архитектуру приложения в одном YAML-файле, что упрощает развертывание и управление зависимостями между контейнерами. Например, вы можете запускать веб-сервер, базу данных и кэш-сервер одновременно с одной командой. Это значительно ускоряет процесс разработки и тестирования, позволяет избежать конфликтов конфигураций и упрощает переносимость приложений между различными средами. В результате, Docker Compose делает жизнь разработчиков проще и эффективнее, позволяя сосредоточиться на написании кода, а не на настройке окружений.

### Задание 2
Выполните действия и приложите текст конфига на этом этапе.

Создайте файл docker-compose.yml и внесите туда первичные настройки:

version;
services;
volumes;
networks.
При выполнении задания используйте подсеть 10.5.0.0/16. Ваша подсеть должна называться: <ваши фамилия и инициалы>-my-netology-hw. Все приложения из последующих заданий должны находиться в этой конфигурации.
### Решение 2
```
version: '3.9'
services:
volumes:
networks:
  petrovpg-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```
### Задание 3
Выполните действия:

Создайте конфигурацию docker-compose для Prometheus с именем контейнера <ваши фамилия и инициалы>-netology-prometheus.
Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории 6-04/prometheus ).
Обеспечьте внешний доступ к порту 9090 c докер-сервера.
### Решение 3
```
version: '3.9'

services:
  prometheus:
    image: prom/prometheus:latest
    container_name: petrovpg-netology-prometheus
    volumes:
      - ./6-04/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    command:
      - --config.file=/etc/prometheus/prometheus.yml
      - --storage.tsdb.path=/prometheus
    ports:
      - "9090:9090"
    networks:
      - petrovpg-my-netology-hw

volumes:
  prometheus_data:

networks:
  petrovpg-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```
### Задание 4
Выполните действия:

Создайте конфигурацию docker-compose для Pushgateway с именем контейнера <ваши фамилия и инициалы>-netology-pushgateway.
Обеспечьте внешний доступ к порту 9091 c докер-сервера.
### Решение 4
```
version: '3.9'

services:
  prometheus:
    image: prom/prometheus:latest
    container_name: petrovpg-netology-prometheus
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml:ro
      - prometheus_data:/prometheus
    command:
      - --config.file=/etc/prometheus/prometheus.yml
      - --storage.tsdb.path=/prometheus
    ports:
      - "9090:9090"
    networks:
      - petrovpg-my-netology-hw
    restart: unless-stopped

  pushgateway:
    image: prom/pushgateway:latest
    container_name: petrovpg-netology-pushgateway
    ports:
      - "9091:9091"
    networks:
      - petrovpg-my-netology-hw
    restart: unless-stopped

volumes:
  prometheus_data:

networks:
  petrovpg-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```
### Задание 5
Выполните действия:

Создайте конфигурацию docker-compose для Grafana с именем контейнера <ваши фамилия и инициалы>-netology-grafana.
Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории 6-04/grafana.
Добавьте переменную окружения с путем до файла с кастомными настройками (должен быть в томе), в самом файле пропишите логин=<ваши фамилия и инициалы> пароль=netology.
Обеспечьте внешний доступ к порту 3000 c порта 80 докер-сервера.
### Решение 5
```
services:
  prometheus:
    image: prom/prometheus:latest
    container_name: petrovpg-netology-prometheus
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml:ro
      - prometheus_data:/prometheus
    command:
      - --config.file=/etc/prometheus/prometheus.yml
      - --storage.tsdb.path=/prometheus
    ports:
      - "9090:9090"
    networks:
      - petrovpg-my-netology-hw
    restart: unless-stopped

  pushgateway:
    image: prom/pushgateway:latest
    container_name: petrovpg-netology-pushgateway
    ports:
      - "9091:9091"
    networks:
      - petrovpg-my-netology-hw
    restart: unless-stopped

  grafana:
    image: grafana/grafana:latest
    container_name: petrovpg-netology-grafana
    volumes:
      - grafana_data:/var/lib/grafana
      - ./grafana/custom.ini:/etc/grafana/custom.ini:ro
    environment:
      - GF_PATHS_CONFIG=/etc/grafana/custom.ini
    ports:
      - "80:3000"
    networks:
      - petrovpg-my-netology-hw
    restart: unless-stopped

volumes:
  prometheus_data:
  grafana_data:

networks:
  petrovpg-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```
### Задание 6
Выполните действия.

1.Настройте поочередность запуска контейнеров.
2.Настройте режимы перезапуска для контейнеров.
3.Настройте использование контейнерами одной сети.
4.Запустите сценарий в detached режиме.

### Решение 6
```
services:
  prometheus:
    image: prom/prometheus:latest
    container_name: petrovpg-netology-prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    restart: always
    depends_on:
      - pushgateway
    networks:
      - petrovpg-my-netology-hw

  pushgateway:
    image: prom/pushgateway:latest
    container_name: petrovpg-netology-pushgateway
    ports:
      - "9091:9091"
    restart: always
    networks:
      - petrovpg-my-netology-hw

  grafana:
    image: grafana/grafana:latest
    container_name: petrovpg-netology-grafana
    ports:
      - "80:3000"
    volumes:
      - ./custom.ini:/etc/grafana/grafana.ini
      - grafana_data:/var/lib/grafana
    environment:
      - GF_PATHS_CONFIG=/etc/grafana/grafana.ini
    restart: always
    depends_on:
      - prometheus
    networks:
      - petrovpg-my-netology-hw

volumes:
  prometheus_data:
  grafana_data:

networks:
  petrovpg-my-netology-hw:
    driver: bridge
```
### Задание 7
Выполните действия.

Выполните запрос в Pushgateway для помещения метрики <ваши фамилия и инициалы> со значением 5 в Prometheus: echo "<ваши фамилия и инициалы> 5" | curl --data-binary @- http://localhost:9091/metrics/job/netology.
Залогиньтесь в Grafana с помощью логина и пароля из предыдущего задания.
Cоздайте Data Source Prometheus (Home -> Connections -> Data sources -> Add data source -> Prometheus -> указать "Prometheus server URL = http://prometheus:9090" -> Save & Test).
Создайте график на основе добавленной в пункте 5 метрики (Build a dashboard -> Add visualization -> Prometheus -> Select metric -> Metric explorer -> <ваши фамилия и инициалы -> Apply.
В качестве решения приложите:

docker-compose.yml целиком;
скриншот команды docker ps после запуске docker-compose.yml;
скриншот графика, постоенного на основе вашей метрики.
### Решение 7
```
services:
  prometheus:
    image: prom/prometheus:latest
    container_name: petrovpg-netology-prometheus
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml:ro
      - prometheus_data:/prometheus
    command:
      - --config.file=/etc/prometheus/prometheus.yml
      - --storage.tsdb.path=/prometheus
    ports:
      - "9090:9090"
    networks:
      - petrovpg-my-netology-hw
    restart: unless-stopped

  pushgateway:
    image: prom/pushgateway:latest
    container_name: petrovpg-netology-pushgateway
    ports:
      - "9091:9091"
    networks:
      - petrovpg-my-netology-hw
    restart: unless-stopped

  grafana:
    image: grafana/grafana:latest
    container_name: petrovpg-netology-grafana
    volumes:
      - grafana_data:/var/lib/grafana
      - ./grafana/custom.ini:/etc/grafana/grafana.ini:ro
    ports:
      - "80:3000"
    networks:
      - petrovpg-my-netology-hw
    restart: unless-stopped

volumes:
  prometheus_data:
  grafana_data:

networks:
  petrovpg-my-netology-hw:
    driver: bridge

```
