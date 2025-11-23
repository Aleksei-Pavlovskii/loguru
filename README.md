# Loguru
## Установка
`pip install loguru`
## Быстрый старт
```
from loguru import logger

logger.debug("Это сообщение отладки")
logger.info("Информационное сообщение")
logger.success("Успешное выполнение!")
logger.warning("Предупреждение")
logger.error("Ошибка!")
logger.critical("Критическая ошибка!")
```
## Основные возможности
### Простое логирование
```
logger.info("Простое сообщение")
logger.info("Сообщение с данными: {}", some_variable)
logger.info("Позиционные аргументы: {} {}", "первый", "второй")
logger.info("Именованные аргументы: {name}", name="значение")
```
### Настройка вывода
```
# Удаление стандартного обработчика
logger.remove()

# Добавление своего обработчика
logger.add(sys.stderr, format="{time} {level} {message}", level="INFO")

# Логирование в файл
logger.add("file_{time}.log", rotation="10 MB")  # Ротация при 10 МБ
logger.add("file.log", rotation="00:00")         # Ротация в полночь
logger.add("file.log", retention="10 days")      # Хранение 10 дней
logger.add("file.log", compression="zip")        # Сжатие старых файлов
```
### Форматирование сообщений
```
# Кастомный формат
logger.add(sys.stderr, format="{time:YYYY-MM-DD HH:mm:ss} | {level} | {message}")

# Доступные переменные для форматирования:
# {time} - время
# {level} - уровень
# {message} - сообщение
# {module} - модуль
# {function} - функция
# {line} - строка
# {file} - файл
```
### Уровни логирования
```
# Стандартные уровни (по возрастанию серьезности):
# TRACE (5)
# DEBUG (10)
# INFO (20)
# SUCCESS (25)
# WARNING (30)
# ERROR (40)
# CRITICAL (50)

# Установка уровня
logger.add("file.log", level="DEBUG")
```
### Перехват исключений
```
# Автоматический перехват
@logger.catch
def risky_function():
    return 1 / 0

# Ручной перехват
try:
    risky_operation()
except Exception:
    logger.exception("Произошла ошибка!")
```
### Фильтрация логов
```
# Фильтр по уровню
def my_filter(record):
    return record["level"].no >= 30  # Только WARNING и выше

logger.add("file.log", filter=my_filter)

# Фильтр по содержимому
def only_errors(record):
    return "error" in record["message"].lower()
```
## Полезные функции
### Временные метки
```
logger.add("file.log", format="{time:YYYY-MM-DD HH:mm:ss} | {level} | {message}")
```
### Цветовое оформление
```
logger.add(sys.stderr, colorize=True, format="<green>{time}</green> <level>{message}</level>")
```
### Асинхронное логирование
```
logger.add("async.log", enqueue=True)  # Потокобезопасно
```
### Логирование в несколько мест
```
logger.add("file1.log", level="DEBUG")
logger.add("file2.log", level="ERROR")
logger.add(sys.stderr, level="INFO")
```
## Продвинутые возможности
### Контекстное логирование
```
logger.bind(user_id=123).info("Действие пользователя")
logger.bind(task_id=456).info("Выполнение задачи")
```
### Патчинг стандартного logging
```
from loguru import logger
import logging

class InterceptHandler(logging.Handler):
    def emit(self, record):
        logger_opt = logger.opt(depth=6, exception=record.exc_info)
        logger_opt.log(record.levelname, record.getMessage())

logging.basicConfig(handlers=[InterceptHandler()], level=0)
```
### Динамическое управление логами
```
# Получение ID обработчика
handler_id = logger.add("file.log")

# Удаление обработчика
logger.remove(handler_id)

# Очистка всех обработчиков
logger.remove()
```
## Практические примеры
### Базовая настройка для проекта
```
from loguru import logger
import sys

# Настройка логирования
logger.remove()
logger.add(
    sys.stderr,
    format="<green>{time:YYYY-MM-DD HH:mm:ss}</green> | <level>{level: <8}</level> | <cyan>{name}</cyan>:<cyan>{function}</cyan>:<cyan>{line}</cyan> - <level>{message}</level>",
    level="INFO"
)
logger.add(
    "logs/app_{time}.log",
    rotation="10 MB",
    retention="1 month",
    compression="zip",
    level="DEBUG"
)
```
### Логирование с контекстом
```
def process_user_request(user_id, request_data):
    logger_context = logger.bind(user_id=user_id)
    logger_context.info("Начало обработки запроса")
    
    try:
        # Логика обработки
        result = some_processing(request_data)
        logger_context.success("Запрос успешно обработан")
        return result
    except Exception as e:
        logger_context.error("Ошибка при обработке запроса: {}", e)
        raise
```
