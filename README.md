# NovaMarket - архитектура микросервисной платформы интернет-магазина

Архитектурный проект платформы интернет-магазина "Nova Market": сага оформления заказа,
CQRS для истории покупок, горизонтальное масштабирование в Kubernetes и паттерны
отказоустойчивости. Решения оформлены как ADR, диаграммы подготовлены в нотации C4.

## Результаты по этапам

### 1. Сага оформления заказа

Модель событий и сценарии распределённой саги оформления заказа: успешное завершение,
ошибки резервации, оплаты и доставки, а также компенсирующие транзакции.

| Артефакт | Тема | Файл |
|----------|------|------|
| Реестр событий | События Saga оформления заказа | [saga-registry.md](Task1/saga-registry.md) |

| Диаграмма | PlantUML | PNG |
|-----------|----------|-----|
| Диаграмма контейнеров | [C2.puml](Task1/C2.puml) | [C2.png](Task1/C2.png) |
| Сценарий: заказ завершён | [order-completed-sequence.puml](Task1/order-completed-sequence.puml) | [order-completed-sequence.png](Task1/order-completed-sequence.png) |
| Сценарий: ошибка резервации | [reservation-failed-sequence.puml](Task1/reservation-failed-sequence.puml) | [reservation-failed-sequence.png](Task1/reservation-failed-sequence.png) |
| Сценарий: ошибка оплаты | [payment-failed-sequence.puml](Task1/payment-failed-sequence.puml) | [payment-failed-sequence.png](Task1/payment-failed-sequence.png) |
| Сценарий: ошибка доставки | [delivery-failed-sequence.puml](Task1/delivery-failed-sequence.puml) | [delivery-failed-sequence.png](Task1/delivery-failed-sequence.png) |

### 2. Просмотр истории покупок (CQRS)

Реализация сценария просмотра истории покупок через CQRS с отдельным сервисом чтения
Order History Service и собственной read-моделью, наполняемой событиями из Apache Kafka.

| Артефакт | Тема | Файл |
|----------|------|------|
| ADR | Реализация просмотра истории покупок (CQRS + Order History Service) | [ADR.md](Task2/ADR.md) |

| Диаграмма | PlantUML | PNG |
|-----------|----------|-----|
| Диаграмма контейнеров | [C2.puml](Task2/C2.puml) | [C2.png](Task2/C2.png) |

### 3. Горизонтальное масштабирование в Kubernetes

Манифесты развёртывания и автоскейлинга приложения в Kubernetes, нагрузочный тест
и лог работы Horizontal Pod Autoscaler при росте нагрузки.

| Артефакт | Назначение | Файл |
|----------|------------|------|
| Deployment | Развёртывание приложения с requests/limits | [deployment.yaml](Task3/deployment.yaml) |
| Service | Внутренняя точка доступа к подам | [service.yaml](Task3/service.yaml) |
| HorizontalPodAutoscaler | Автоматическое масштабирование по памяти | [hpa.yaml](Task3/hpa.yaml) |
| Нагрузочный тест | Генерация нагрузки (Locust) | [locustfile.py](Task3/locustfile.py) |
| Лог масштабирования | Результат работы HPA | [scale-log.txt](Task3/scale-log.txt) |

### 4. Отказоустойчивость и защита API

Паттерны повышения устойчивости на уровне API Gateway: размыкание цепи при недоступности
сервиса логистики и ограничение скорости запросов для разных клиентов.

| Артефакт | Назначение | Файл |
|----------|------------|------|
| Circuit Breaker | Размыкание цепи и fallback для Logistics Service | [circuit-breaker-nginx.conf](Task4/circuit-breaker-nginx.conf) |
| Rate Limiter | Ограничение частоты запросов (web/mobile/legacy) | [rate-limiter-nginx.conf](Task4/rate-limiter-nginx.conf) |

## Как читать артефакты

Артефакты сгруппированы по каталогам `Task1`-`Task4` по этапам проекта. Где решение
оформлено как ADR, документ содержит контекст, требования, решение, рассмотренные
альтернативы и компромиссы. Диаграммы представлены исходником PlantUML (`.puml`) и
готовым рендером (`.png`).
