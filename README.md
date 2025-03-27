# Диаграммы для ВКР по теме:«Распознавание жестов для управления презентацией»

## Диаграмма вариантов использования (use case diagram)

![image](https://github.com/uml_diagrams_for_vkr/diagrams/usecase.png)

```uml
@startuml
left to right direction
actor "Пользователь" as user
actor "Система презентаций" as ppt

rectangle "Управление жестами" {
  usecase "Запуск распознавания" as start
  usecase "Остановка распознавания" as stop
  usecase "Обработка жеста" as process
  
  user --> start
  user --> stop
  user --> process

  process --> ppt : "Следующий слайд"
  process --> ppt : "Предыдущий слайд"
  
  note right of process 
    Шаги обработки:
    1. Захват изображения с камеры
    2. Детекция положения руки
    3. Классификация жеста
    4. Отправка команды
  end note
}
@enduml
```

## Диаграмма классов (classes diagram)

![image](https://github.com/uml_diagrams_for_vkr/diagrams/classes.png)

```uml
@startuml
interface IGestureRecognition {
  +start_recognition()
  +stop_recognition()
}

class GestureRecognizer {
  +detect_gesture() : GestureType
  +on_error()
}
GestureRecognizer ..|> IGestureRecognition

class PresentationController {
  +next_slide()
  +prev_slide()
  +get_current_slide()
}

class MainApp {
  -recognition: IGestureRecognition
  -presenter: PresentationController
  +run()
  +stop()
}

enum GestureType {
  NEXT_SLIDE
  PREV_SLIDE
  NONE
}

MainApp --> IGestureRecognition
MainApp --> PresentationController
GestureRecognizer --> PresentationController : commands >
@enduml
```
Пример кода на Python:

```python
from abc import ABC, abstractmethod
from enum import Enum

class GestureType(Enum):
    NEXT_SLIDE = 1
    PREV_SLIDE = 2
    NONE = 3

class IGestureRecognition(ABC):
    @abstractmethod
    def start_recognition(self): pass
    
    @abstractmethod
    def stop_recognition(self): pass

class GestureRecognizer(IGestureRecognition):
    def detect_gesture(self) -> GestureType:
        # Реализация через OpenCV
        return GestureType.NEXT_SLIDE
    
    def on_error(self): 
        print("CV Error Handler")

class PresentationController:
    def next_slide(self): 
        print("Sliding forward...")
    
    def prev_slide(self): 
        print("Sliding backward...")

class MainApp:
    def __init__(self):
        self.recognition = GestureRecognizer()
        self.presenter = PresentationController()
    
    def run(self):
        self.recognition.start_recognition()
        while True:
            gesture = self.recognition.detect_gesture()
            if gesture == GestureType.NEXT_SLIDE:
                self.presenter.next_slide()
            elif gesture == GestureType.PREV_SLIDE:
                self.presenter.prev_slide()
```


## Диаграмма последовательности (sequence diagram)

![image](https://github.com/uml_diagrams_for_vkr/diagrams/sequence.png)

```uml
@startuml
actor Пользователь
participant MainApp
participant GestureRecognizer as GR
participant PresentationController as PC

Пользователь -> MainApp : Запуск программы (run())
activate MainApp

MainApp -> GR : start_recognition()
activate GR

loop Распознавание жестов
    Пользователь -> GR : Совершает жест
    GR -> GR : detect_gesture()
    
    alt Жест "Вперед"
        GR -> PC : next_slide()
        activate PC
        PC --> GR : Подтверждение
        deactivate PC
        
    else Жест "Назад"
        GR -> PC : prev_slide()
        activate PC
        PC --> GR : Подтверждение
        deactivate PC
    end
    
    GR -> MainApp : Обновление состояния
    MainApp -> Пользователь : Визуальная обратная связь
end

Пользователь -> MainApp : Остановка (stop())
MainApp -> GR : stop_recognition()
deactivate GR
deactivate MainApp
@enduml
```

## Диаграмма состояний (state diagram)

![image](https://github.com/uml_diagrams_for_vkr/diagrams/state.png)

```uml
@startuml
[*] --> Idle : Инициализация
Idle --> Calibrating : Нажата "Калибровка"
Calibrating --> Idle : Отмена
Calibrating --> Ready : Калибровка завершена

Ready --> Recognizing : Нажата "Старт"
Recognizing --> Ready : Нажата "Стоп"

state Recognizing {
  [*] --> Detecting : Запуск камеры
  Detecting --> Processing : Жест обнаружен
  Processing --> CommandSent : Жест распознан
  CommandSent --> Detecting : Цикл продолжается
  
  state ErrorHandling {
    Detecting --> Error : Ошибка камеры
    Processing --> Error : Неверный жест
    Error --> Detecting : Повторная попытка
  }
}

Ready --> [*] : Выключение
Recognizing --> [*] : Аварийное отключение
@enduml
```

## Диаграмма  деятельности (activity diagram)

![image](https://github.com/uml_diagrams_for_vkr/diagrams/activity.png)

```uml
@startuml
title Диаграмма деятельности: Управление презентацией жестами

start

:Пользователь запускает программу;
:Инициализация камеры и ML-модели;

repeat
  :Захват кадра с камеры;
  
  if (Обнаружена рука?) then (Да)
    :Определение ключевых точек руки;
    if (Жест распознан?) then (Да)
      if (Жест == "Вперед") then (Да)
        :Отправить команду "Следующий слайд";
      else if (Жест == "Назад") then (Да)
        :Отправить команду "Предыдущий слайд";
      else (Другой жест)
        :Игнорировать;
      endif
    else (Нет)
      :Записать в лог "Неизвестный жест";
    endif
  else (Нет)
    :Показать подсказку "Покажите жест";
  endif
  
repeat while (Программа активна?) is (Да)
->Нет;

:Сохранение логов;
:Освобождение ресурсов;

stop
@enduml
```
