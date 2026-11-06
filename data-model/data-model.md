# Модель данных системы "Альфред"

## Основные сущности

### Trip (Поездка)
- trip_id (UUID, PK)
- user_id (UUID, FK)
- group_id (UUID, FK, nullable)
- destination (TEXT)
- start_date (TIMESTAMP)
- end_date (TIMESTAMP)
- status (ENUM: planned, in_progress, completed, cancelled)
- created_at (TIMESTAMP)

### Flight (Рейс)
- flight_id (UUID, PK)
- trip_id (UUID, FK)
- flight_number (TEXT)
- airline_code (TEXT)
- departure_airport (TEXT)
- arrival_airport (TEXT)
- departure_time (TIMESTAMP)
- arrival_time (TIMESTAMP)
- status (ENUM: scheduled, delayed, cancelled, completed)
- is_alternative (BOOLEAN)

### PassengerData (Паспортные данные)
- passenger_id (UUID, PK)
- trip_id (UUID, FK)
- full_name (TEXT, encrypted)
- passport_number (TEXT, encrypted)
- expiry_date (DATE)
- citizenship (TEXT)
- deletion_date (TIMESTAMP)

### Notification (Уведомление)
- notification_id (UUID, PK)
- user_id (UUID, FK)
- trip_id (UUID, FK)
- type (ENUM: delay, cancellation, alternative)
- message (TEXT)
- status (ENUM: pending, sent, delivered, read)
- created_at (TIMESTAMP)

### AlternativeOption (Альтернативный вариант)
- alternative_id (UUID, PK)
- original_flight_id (UUID, FK)
- new_flight_id (UUID, FK)
- price_difference (DECIMAL)
- time_difference_minutes (INT)
- requires_visa (BOOLEAN)
- baggage_transfer_possible (BOOLEAN)

## Жизненный цикл паспортных данных

1. Паспортные данные создаются при бронировании рейса
2. Поле deletion_date устанавливается как end_date поездки + 24 часа
3. Фоновая задача (cron job) ежедневно проверяет таблицу
4. Записи с deletion_date < NOW() безвозвратно удаляются из базы
5. Альтернативный вариант: использование TTL-индекса (Time To Live) в MongoDB для автоматического удаления

Пример SQL для фоновой задачи:
DELETE FROM passenger_data WHERE deletion_date < NOW()