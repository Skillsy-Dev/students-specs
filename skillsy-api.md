
# Skillsy - API Документация

[1. Создание сервисного пользователя](#title1)  
[2. Удаление сервисного пользователя](#title2)   
[3. Регистрация пользователя для прохождения тестирования](#title3)  
[4. Получение списка вопросов с вариантами ответов](#title4)   
[5. Регистрация ответа на вопросы](#title5)  
[6. Статус пользователя](#title6)    
[7. Просмотр результатов тестирования](#title7)  
[8. Командный отчет](#title8)  
[9. B2C to B2C мэтчинг (один к одному)](#title9)     
[10. B2C to B2C мэтчинг (один ко многим)](#title10)   
[11. Добавить сервисы](#title11)   
[12. B2C to B2B мэтчинг](#title12)      


### <a id="title1">1. Создание сервисного пользователя</a>
**Метод:** `POST /api/client`

**Ожидаемые параметры JSON:**
```json
{
    "client_id" : "{id}",
    "webhook" : "{url}"
    "secret_key" : "{key}"
}
```

**Пример ответа JSON (200 OK):**
```json
{
    "auth_token": "{token}",
    "url": "{skillsy_url}"
}
```

---

### <a id="title2">2. Удаление сервисного пользователя</a>
**Метод:** `DELETE /api/client/{client_id}`

**Ожидаемые параметры при запросе:**
```
headers: {
    "Authorization": "Token ${token}"
}
```

**Пример ответа JSON (200 OK):**
```json
{
    "client_id": "213",
    "date": "2025-03-19T19:08:25.921937+01:00"
}
```

---

### <a id="title3">3. Регистрация пользователя для прохождения тестирования</a>
**Метод:** `POST /api/user`

**Ожидаемые параметры при запросе:**
```
headers: {
    "Authorization": "Token ${token}"
}
```

**Ожидаемые параметры JSON:**
```json
{
    "user_id" : "{id}",
    "birth_date" : "2018-01-10"
}
```

**Пример ответа JSON (200 OK):**
```json
{
    "user_id": "213",
    "age": 22,
    "date": "2025-03-19T19:08:25.921937+01:00"
}
```

---

### <a id="title4">4. Получение вопроса с вариантами ответов</a>
**Метод:** `GET /api/question/{user_id}`

**Ожидаемые параметры при запросе:**
```
headers: {
    "Authorization": "Token ${token}"
}
```

**Пример ответа JSON (200 OK):**
```json
{
    "id": 1,
    "total_count": 50,
    "questions_left": 49,
    "text": "Вы пришли на мероприятие, которое проводит известный эксперт в вашей области. В зале три ряда мест. Вы – один из первых и все места свободны. Какое место вы выберете для себя?",
    "answers_count": 3,
    "answers": [
        {
           "id": 1,
           "text": "Первый вариант ответа"
        },
        {
           "id": 2,
           "text": "Второй вариант ответа"
        },
        {
           "id": 3,
           "text": "Первый вариант ответа"
        }
    ]
}
```

---

### <a id="title5">5. Регистрация ответа на вопросы</a>
**Метод:** `POST /api/answer`

**Ожидаемые параметры JSON:**
```json
{
    "user_id": "{id}"
    "question_id": "{id}"
    "answer": 3
}
```

**Пример ответа JSON (200 OK):**
```json
{
    "user_id": "77",
    "questions_left": 49,
    "question_id": 1,
    "answer": 3,
    "date": "2024-09-17T16:22:28.282035+02:00"
}
```

Если questions_left == 0, то тестирование завершено.
Уведомление о готовности отчета приходит на вебхук, указанный при создании сервисного пользователя

**Пример JSON (200 OK):**
```json
{
    "user_id": "77",
    "report_type": "INDIVIDUAL",
    "status": "READY",
    "date": "2024-09-17T16:22:28.282035+02:00"
}
```

---

### <a id="title6">6. Статус пользователя</a>
**Метод:** `GET /api/user/{user_id}`

**Ожидаемые параметры при запросе:**
```
headers: {
    "Authorization": "Token ${token}"
}
```

**Пример ответа JSON (200 OK):**
```json
{
    "user_id": "{id}",
    "current_question": 2,
    "questions_left": 49,
    "reports": [
       {
         "type": "INDIVIDUAL",
         "status": "READY",
       }
    ]
    "date": "2024-09-17T16:22:28.282035+02:00"
}
```

---

### <a id="title7">7. Просмотр результатов тестирования</a>
**Метод:** `POST /api/user/report`

**Ожидаемые параметры при запросе:**
```
headers: {
    "Authorization": "Token ${token}"
}
```

**Ожидаемые параметры JSON:**
```json
{
    "user_id": "{id}"
    "report_type": "{type}"
}
```

**Пример ответа JSON (200 OK):**
```json
{
  "user_id": "75",
  "report" : {
    "type" : "INDIVIDUAL"
    "description" : "Индивидуальный отчет" 

    "section": [{
       "type" : "WORK_RELATED",
       "description" : "Профессиональная сфера",
       "params" : [{
          "name": "Ключевые ценности",
          "description": "Ключевые ценности проявляются во всех сферах жизни человека и определяют его выбор и решения",
          "text": "\n<strong>Стремление к самореализации</strong>\nЭтот кандидат/сотрудник стремится к самореализации, изучает себя и свою индивидуальность и ищет пути для оптимального воплощения своих сильных качеств.\n"
          },
          {
          "name": "Командная роль",
          "description": "Лидер, эксперт или уверенный командный игрок? Командная роль вашего кандидата/сотрудника может отличаться от формальной роли, но важно понимать, к какой реализации в команде он стремится на самом деле",
          "text": "\n<strong>Лидер - Партнер</strong>\nВ команде этот кандидат/сотрудник стремится к лидерской позиции через синергию, коммуникацию и эффективное взаимодействие.\n"
          }
        ]
      }
   ]
 }
}
```

--- 

### <a id="title8">8. Командный отчет</a>
**Метод:** `POST /api/team-report`

**Ожидаемые параметры при запросе:**
```
headers: {
    "Authorization": "Token ${token}"
}
```

**Ожидаемые параметры JSON:**
```json
{
    "report_type": "{type}"
    "users" : [ "id1", "id2" ]
}
```

**Пример ответа JSON (200 OK):**
```json
{
  "report" : {
    "type" : "STARTUP"
    "description" : "Отчет для инвестора" 

    "section": [{
       "type" : "TEAM_BASIC",
       "description" : "Общая оценка команды",
       "params" : [{
          "name": "Ценностный профиль команды",
          "description": "Ценностный профиль команды",
          "text": "Описание ценностного профиля команды"
          },
          {
          "name": "Командное взаимодействие",
          "description": "Командное взаимодействие",
          "text": "Описание командного взаимодействия"
          }
        ]
      }
   ]
 }
}
```

--- 

## B2C to B2C мэтчинг

### <a id="title9">9. B2C to B2C мэтчинг (один к одному)</a>
**Метод:** `POST /api/match/b2c/one`

**Ожидаемые параметры при запросе:**
```
headers: {
    "Authorization": "Token ${token}"
}
```

**Ожидаемые параметры JSON:**
```json
{
    "match_type" : "{type}",
    "user_to_find_match" : "{id}"
    "user_to_be_matched" : "{id}"

}
```

**Пример ответа JSON (200 OK):**
```json
{
    "match_id": "{id}"
    "match_type" : "FRIENDSHIP",
    "user_id" : 45
    "result" : {
        "user_id" : 56,
        "match_index" : 78
    }
}
```

---

### <a id="title10">10. B2C to B2C мэтчинг (один ко многим)</a>
**Метод:** `POST /api/match/b2c/many`

**Ожидаемые параметры при запросе:**
```
headers: {
    "Authorization": "Token ${token}"
}
```

**Ожидаемые параметры JSON:**
```json
{
    "match_type" : "{type}",
    "user_to_find_match" : "{id}"
}
```

**Пример ответа JSON (200 OK):**
```json
{
    "match_id": "{id}",
    "match_type" : "FRIENDSHIP",
    "user_id" : 45
    "result" : [
        {
          "user_id" : 56,
          "match_index" : 89
        },
        {
          "user_id" : 57,
          "match_index" : 78
        },
    ]
}
```

---

## B2C to B2B мэтчинг

### <a id="title11">11. Добавить сервисы</a>
**Метод:** `POST /api/match/services`

**Ожидаемые параметры при запросе:**
```
headers: {
    "Authorization": "Token ${token}"
}
```

**Ожидаемые параметры JSON:**
```json
[
    {
      "id" : "{id}"
      "type" : "{type}",
      "title" : "{title}",
      "description" : "{description}"
      "date" : "{date}",
      "duration" : "{number_of_days}"
    }
]
```

**Пример ответа JSON (200 OK):**
```json
{
    "status" : "Done",
}
```

---

### <a id="title12">12. B2C to B2B мэтчинг</a>
**Метод:** `POST /api/match/b2b`

**Ожидаемые параметры при запросе:**
```
headers: {
    "Authorization": "Token ${token}"
}
```

**Ожидаемые параметры JSON:**
```json
{
    "service_type" : "{type}",
    "user_to_find_match" : "{id}"
}
```

**Пример ответа JSON (200 OK):**
```json
{
    "service_type" : "TRAINING",
    "user_id" : 45
    "match_id": 34,
    "result" : [
        {
          "service_id" : 56,
          "match_ratio" : true
        },
        {
          "service_id" : 56,
          "match_ratio" : false
        },
    ]
}
```

---
