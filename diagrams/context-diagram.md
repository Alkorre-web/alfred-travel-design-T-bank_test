# Контекстная диаграмма системы "Альфред"

## Описание

Контекстная диаграмма показывает границы системы и её взаимодействие с внешними акторами и системами.

## Диаграмма (PlantUML)

```plantuml
@startuml
left to right direction

actor "Пользователь" as User
actor "Семья/Группа" as Group

rectangle "Система Альфред" {
  usecase "Получение статусов рейсов" as UC1
  usecase "Уведомление об изменениях" as UC2
  usecase "Подбор альтернатив" as UC3
  usecase "Обновление плана поездки" as UC4
  usecase "Проверка визовых требований" as UC5
  usecase "Учет багажа" as UC6
}

database "Внешние API\nавиакомпаний" as API
database "Сервис бронирования" as Booking
database "Сервис уведомлений" as Notify
database "База данных\nпаспортных данных" as Passport

User --> UC1
User --> UC2
User --> UC3
User --> UC4
Group --> UC1
Group --> UC2

UC1 --> API
UC2 --> Notify
UC3 --> Booking
UC3 --> API
UC4 --> Booking
UC5 --> API
UC6 --> Booking

UC1 ..> Passport : использует
@enduml