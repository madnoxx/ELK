# ELK + Nginx + Filebeat (Docker Compose)

Локальный стенд для сбора, обработки и визуализации логов на базе **ELK**.

## Описание технологий

- **Nginx** — HTTP-сервер, который генерирует access/error логи.
- **Filebeat** — собирает логи из `./logs` и docker-контейнеров, отправляет их в Logstash.
- **Logstash** — принимает события от Filebeat (Beats input), обрабатывает и отправляет в Elasticsearch.
- **Elasticsearch** — хранит и индексирует логи.
- **Kibana** — интерфейс для поиска и анализа логов.

## Архитектура

```text
Client --> Nginx --> /var/log/nginx/*.log --> Filebeat --> Logstash --> Elasticsearch --> Kibana
                           ^
                           |
                    volume ./logs
```

## Запуск

1. Запустить стек:

```bash
docker compose up -d --build
```

2. Проверить, что контейнеры поднялись:

```bash
docker compose ps
```

3. Сгенерировать трафик для появления логов:

```bash
curl http://localhost:8080/
for i in {1..20}; do curl -s http://localhost:8080/ > /dev/null; done
```

## Проверка

1. Проверить Nginx:

```bash
curl -i http://localhost:8080/
```

Ожидается `HTTP/1.1 200 OK` и ответ `Hello from monitored Nginx!`.

2. Проверить Elasticsearch:

```bash
curl -s http://localhost:9200
curl -s "http://localhost:9200/_cat/indices?v"
```

Ожидается появление индексов формата `logs-YYYY.MM.DD`.

3. Проверить поступление событий в Filebeat/Logstash:

```bash
docker compose logs -f filebeat
docker compose logs -f logstash
```
