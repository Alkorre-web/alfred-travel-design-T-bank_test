# Sequence-диаграмма: обработка задержки рейса

## Описание

Диаграмма показывает последовательность действий при обнаружении задержки рейса: от получения данных от внешнего API до уведомления пользователя и обновления плана поездки.

## Диаграмма (PlantUML)

```plantuml
@startuml
participant "Flight Status\nAPI" as API
participant "Flight Tracker\nService" as Tracker
participant "Alternative\nEngine" as Engine
participant "Notification\nService" as Notify
participant "User" as User
participant "Booking\nService" as Booking

== Обнаружение задержки ==
API -> Tracker: webhook/polling\n(flight delayed)
activate Tracker

Tracker -> Tracker: проверить статус рейса
Tracker -> Tracker: найти затронутые поездки

== Подбор альтернатив ==
Tracker -> Engine: запрос альтернатив\n(flight_id, constraints)
activate Engine

Engine -> Engine: найти доступные рейсы\n(тот же маршрут, +/- 24ч)
Engine -> Engine: проверить визовые требования
Engine -> Engine: проверить возможность\nтрансфера багажа
Engine -> Engine: рассчитать time_difference
Engine -> Engine: ранжировать варианты

Engine --> Tracker: список альтернатив
deactivate Engine

== Уведомление пользователя ==
Tracker -> Notify: создать уведомление\n(задержка + альтернативы)
activate Notify

alt Пользователь онлайн
  Notify -> User: push-уведомление
else Пользователь оффлайн
  Notify -> Notify: добавить в очередь\nдля доставки при онлайн
end

deactivate Notify

== Согласование альтернативы ==
User -> Booking: выбрать альтернативу
activate Booking

Booking -> Booking: отменить старый рейс
Booking -> Booking: забронировать новый рейс
Booking -> Booking: организовать трансфер багажа
Booking -> Booking: обновить план поездки

Booking --> Tracker: подтверждение
deactivate Booking

Tracker -> Tracker: обновить статус рейса
Tracker -> Notify: уведомить об успешной замене
deactivate Tracker

@enduml