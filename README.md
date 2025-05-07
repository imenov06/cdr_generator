# Генератор CDR-записей

Этот проект предназначен для генерации тестовых данных CDR (Call Detail Records) - записей о телефонных вызовах. Генератор создает как корректные CDR, так и записи с различными типами ошибок, что позволяет тестировать системы обработки и валидации телефонных данных.

## Использование
1. Клонируйте проект и перейдите в директорию
   ```
   git clone https://github.com/imenov06/cdr_generator.git
   cd cdr_generator
   ```
2. Запустите генерацию всех типов CDR:
   ```
   python main.py
   ```
3. Файлы с CDR будут сохранены в директории `out_data/`

## Структура проекта

```
├── main.py                               # Главный запускаемый файл
├── cdr_utils.py                          # Утилиты для работы с CDR
├── cdr_error_modifiers.py                # Функции для создания ошибок в CDR
├── generators/                           # Генераторы различных типов CDR
│   ├── cdr_generator_correct.py          # Генератор корректных CDR
│   ├── cdr_generator_error_garbage_date.py   # CDR с некорректными датами
│   ├── cdr_generator_error_invalid_call_type.py # CDR с неверным типом вызова
│   ├── cdr_generator_error_invalid_msisdn.py # CDR с неверным форматом номера
│   ├── cdr_generator_error_msisdn_self_call.py # CDR с вызовом на свой номер
│   ├── cdr_generator_error_start_after_end.py # CDR где начало после окончания
│   ├── cdr_generator_error_zero_duration.py # CDR с нулевой длительностью
│   └── cdr_generator_mixed.py            # Смешанные CDR (часть корректные, часть с ошибками)
└── out_data/                             # Директория для сохранения сгенерированных файлов
```

## Формат CDR-записей
Каждая CDR-запись (строка в файле JSONL) содержит следующие поля:

- `callType`: тип вызова (01 - исходящий, 02 - входящий)
- `firstSubscriberMsisdn`: номер первого абонента (формат: 79XXXXXXXXX)
- `secondSubscriberMsisdn`: номер второго абонента (формат: 79XXXXXXXXX)
- `callStart`: время начала вызова (ISO формат: YYYY-MM-DDThh:mm:ss)
- `callEnd`: время окончания вызова (ISO формат: YYYY-MM-DDThh:mm:ss)

## Типы генерируемых CDR
Файлы генерируются в формате JSON Lines (каждая строка – это отдельный JSON-объект).
1. **Корректные CDR** (`generated_cdrs_correct.jsonl`)
   - Все поля соответствуют требуемому формату
   - Звонки разделяются на два, если они переходят через полночь

2. **CDR с ошибками:**
   - **Некорректные даты** (`generated_cdrs_error_garbage_date.jsonl`) - случайные строки вместо дат
   - **Неверный тип вызова** (`generated_cdrs_error_invalid_call_type.jsonl`) - значения, отличные от '01' или '02'
   - **Неверный формат номера** (`generated_cdrs_error_invalid_msisdn.jsonl`) - слишком короткий, слишком длинный или с недопустимыми символами
   - **Вызов самому себе** (`generated_cdrs_error_msisdn_self_call.jsonl`) - одинаковые номера в полях first и second
   - **Время начала после окончания** (`generated_cdrs_error_start_after_end.jsonl`) - нарушение хронологии
   - **Нулевая длительность** (`generated_cdrs_error_zero_duration.jsonl`) - одинаковое время начала и окончания

3. **Смешанные CDR** (`generated_cdrs_mixed.jsonl`)
   - Комбинация корректных записей и записей с различными ошибками

## Настройки генерации

Можно изменить следующие параметры в файлах генераторов:
- `NUMBER_OF_CDRS_TO_GENERATE` - количество CDR для генерации
- `OWN_SUBSCRIBERS_LIST` - список "своих" абонентов
- В файле `cdr_generator_mixed.py` можно настроить `PROBABILITY_OF_ERROR` - вероятность генерации ошибочной записи

## Требования

- Python 3.9+
- Стандартная библиотека Python (без дополнительных зависимостей)

## Примеры сгенерированных CDR

### Корректная запись
```json
{"callType": "02", "firstSubscriberMsisdn": "79156459857", "secondSubscriberMsisdn": "79259669925", "callStart": "2025-01-01T01:00:37", "callEnd": "2025-01-01T03:32:42"}
{"callType": "02", "firstSubscriberMsisdn": "79870041325", "secondSubscriberMsisdn": "79392139613", "callStart": "2025-01-01T01:15:54", "callEnd": "2025-01-01T04:15:30"}
{"callType": "02", "firstSubscriberMsisdn": "79898914719", "secondSubscriberMsisdn": "79484299125", "callStart": "2025-01-01T01:49:02", "callEnd": "2025-01-01T05:24:53"}
{"callType": "02", "firstSubscriberMsisdn": "79846676374", "secondSubscriberMsisdn": "79555366291", "callStart": "2025-01-01T03:25:40", "callEnd": "2025-01-01T05:09:27"}
{"callType": "01", "firstSubscriberMsisdn": "79898914719", "secondSubscriberMsisdn": "79937969637", "callStart": "2025-01-01T04:06:36", "callEnd": "2025-01-01T04:32:28"}
{"callType": "01", "firstSubscriberMsisdn": "79399910312", "secondSubscriberMsisdn": "79287263507", "callStart": "2025-01-01T04:59:07", "callEnd": "2025-01-01T07:42:35"}
```

### Запись с ошибкой (неверная дата)
```json
{"callType": "01", "firstSubscriberMsisdn": "79402762325", "secondSubscriberMsisdn": "79401217893", "callStart": "={Pw(w7d'Y^M#+[`t2nW<*", "callEnd": "?qv'~F/:fSc!S9FwA/1`:m|Mq3M;;"}
{"callType": "02", "firstSubscriberMsisdn": "79189232791", "secondSubscriberMsisdn": "79581916256", "callStart": "2025-01-01T02:18:10", "callEnd": ".Q`#d|Lve/IbRxRNFr.r1Nxq$"}
{"callType": "02", "firstSubscriberMsisdn": "79870041325", "secondSubscriberMsisdn": "79616332430", "callStart": "1WT4L6.@7STarcX=", "callEnd": "}F|5;+F|A^JMKnD3SF"}
{"callType": "01", "firstSubscriberMsisdn": "79667904941", "secondSubscriberMsisdn": "79642008229", "callStart": "]EjEc{[d&0sHy~E0m8|#4;RPIa2+", "callEnd": "/4/1u.hWwiLX]m5"}
{"callType": "02", "firstSubscriberMsisdn": "79484299125", "secondSubscriberMsisdn": "79189232791", "callStart": "#~BGF''E:gBd(,z", "callEnd": "-2Fe)>I_Pps/VC"}
```
