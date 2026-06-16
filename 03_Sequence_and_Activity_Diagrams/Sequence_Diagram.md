@startuml
title Сканирование QR-кода на входе

actor "Посетитель" as Visitor
participant "Турникет" as Turnstile
participant "Система ZooTicket" as System
database "База данных" as DB

Visitor -> Turnstile : 1. Предъявить QR-код

activate Turnstile

Turnstile -> System : 2. Передать QR-код на проверку

activate System

System -> DB : 3. Запросить билет по QR-коду

alt Технический сбой

DB --> System : 4а. Ошибка соединения

System -> Turnstile : 5а. Ошибка проверки

Turnstile -> Turnstile : 6а. Запретить проход

Turnstile --> Visitor : 7а. Проверка временно недоступна\nОбратитесь к сотруднику зоопарка

else Билет действителен

DB --> System : 4б. Вернуть данные билета

System -> System : 5б. Проверить билет

note right of System
  Проверка:
  - статус билета
  - дата посещения
  - срок действия
end note

System -> Turnstile : 6б. Разрешить проход

Turnstile -> Turnstile : 7б. Открыть турникет

Turnstile --> Visitor : 8б. Проход разрешён

Turnstile -> System : 9б. Проход выполнен

System -> DB : 10б. Изменить статус билета\nна «Использован»

DB --> System : 11б. Подтверждение изменения статуса

else Билет недействителен

DB --> System : 4в. Вернуть данные билета

System -> System : 5в. Проверить билет

System -> System : 6в. Определить причину отказа

note right of System
  Возможные причины:
  - билет уже использован
  - неверная дата посещения
  - срок действия истёк
  - билет не найден
end note

System -> Turnstile : 7в. Запретить проход\n+ причина отказа

Turnstile --> Visitor : 8в. Показать причину отказа

end

deactivate System
deactivate Turnstile

@enduml
