# Архитектурная схема системы "Альфред"

## Описание

Система построена по микросервисной архитектуре с API Gateway на входе. Каждый сервис отвечает за свою бизнес-область, что обеспечивает независимое масштабирование и отказоустойчивость.

## Диаграмма (PlantUML)

```plantuml
@startuml
package "Client Layer" {
  [Mobile App]
  [Web App]
}

package "API Gateway" {
  [API Gateway\n(Kong / AWS API Gateway)]
}

package "Core Services" {
  [Flight Tracker\nService]
  [Alternative\nEngine]
  [Notification\nService]
  [Trip Management\nService]
}

package "Data Layer" {
  database "PostgreSQL\n(trips, flights,\npassengers)"
  database "MongoDB\n(notifications queue)"
  database "Redis\n(cache, sessions)"
}

package "External Systems" {
  [Flight Status APIs\n(FlightAware, Airlines)]
  [Booking System]
  [Push Service\n(FCM / APNS)]
  [Email Service]
}

[Mobile App] --> [API Gateway]
[Web App] --> [API Gateway]

[API Gateway] --> [Flight Tracker Service]
[API Gateway] --> [Alternative Engine]
[API Gateway] --> [Notification Service]
[API Gateway] --> [Trip Management Service]

[Flight Tracker Service] --> [Flight Status APIs]
[Flight Tracker Service] --> PostgreSQL

[Alternative Engine] --> [Booking System]
[Alternative Engine] --> PostgreSQL

[Notification Service] --> MongoDB
[Notification Service] --> [Push Service]
[Notification Service] --> [Email Service]

[Trip Management Service] --> PostgreSQL
[Trip Management Service] --> Redis

@enduml