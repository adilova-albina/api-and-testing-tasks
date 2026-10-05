# Задание 11 — API для списка задач

## 1. Описание

Необходимо спроектировать REST API для управления списком задач.

API должен поддерживать следующие операции:

* создание задачи;
* просмотр списка задач;
* просмотр одной задачи;
* изменение статуса задачи;
* удаление задачи;
* постраничную выдачу списка задач.

## 2. Модель задачи

Пример объекта задачи:

```json
{
  "id": 1,
  "title": "Complete SQL homework",
  "description": "Finish SQL queries",
  "status": "todo",
  "createdAt": "2026-10-06T10:00:00Z"
}
```

Возможные статусы:

```text
todo
in_progress
done
```

## 3. API Endpoints

| Метод  | Endpoint                 | Назначение            |
| ------ | ------------------------ | --------------------- |
| POST   | `/api/tasks`             | Создать задачу        |
| GET    | `/api/tasks`             | Получить список задач |
| GET    | `/api/tasks/{id}`        | Получить одну задачу  |
| PATCH  | `/api/tasks/{id}/status` | Изменить статус       |
| DELETE | `/api/tasks/{id}`        | Удалить задачу        |

## 4. Создание задачи

### Запрос

```http
POST /api/tasks
Content-Type: application/json
```

```json
{
  "title": "Complete SQL homework",
  "description": "Finish SQL queries"
}
```

### Ответ

**201 Created**

```json
{
  "id": 1,
  "title": "Complete SQL homework",
  "description": "Finish SQL queries",
  "status": "todo",
  "createdAt": "2026-10-06T10:00:00Z"
}
```

При создании задачи статус автоматически устанавливается в `todo`.

## 5. Получение списка задач

### Запрос

```http
GET /api/tasks
```

### Ответ

**200 OK**

```json
{
  "items": [
    {
      "id": 1,
      "title": "Complete SQL homework",
      "description": "Finish SQL queries",
      "status": "todo",
      "createdAt": "2026-10-06T10:00:00Z"
    },
    {
      "id": 2,
      "title": "Prepare presentation",
      "description": "Prepare slides",
      "status": "in_progress",
      "createdAt": "2026-10-05T15:30:00Z"
    }
  ],
  "page": 1,
  "limit": 10,
  "total": 25
}
```

## 6. Получение одной задачи

### Запрос

```http
GET /api/tasks/1
```

### Ответ

**200 OK**

```json
{
  "id": 1,
  "title": "Complete SQL homework",
  "description": "Finish SQL queries",
  "status": "todo",
  "createdAt": "2026-10-06T10:00:00Z"
}
```

## 7. Изменение статуса задачи

Для изменения только статуса используется метод `PATCH`.

### Запрос

```http
PATCH /api/tasks/1/status
Content-Type: application/json
```

```json
{
  "status": "done"
}
```

### Ответ

**200 OK**

```json
{
  "id": 1,
  "title": "Complete SQL homework",
  "description": "Finish SQL queries",
  "status": "done",
  "createdAt": "2026-10-06T10:00:00Z"
}
```

## 8. Удаление задачи

### Запрос

```http
DELETE /api/tasks/1
```

### Ответ

**204 No Content**

При успешном удалении сервер не возвращает тело ответа.

## 9. Коды ошибок

| HTTP-код                    | Назначение                |
| --------------------------- | ------------------------- |
| `200 OK`                    | Запрос выполнен успешно   |
| `201 Created`               | Задача успешно создана    |
| `204 No Content`            | Задача успешно удалена    |
| `400 Bad Request`           | Некорректный запрос       |
| `404 Not Found`             | Задача не найдена         |
| `422 Unprocessable Entity`  | Ошибка валидации          |
| `500 Internal Server Error` | Внутренняя ошибка сервера |

### Пример ошибки 404

```json
{
  "error": "TASK_NOT_FOUND",
  "message": "Task with id 999 was not found"
}
```

### Пример ошибки валидации

```json
{
  "error": "VALIDATION_ERROR",
  "message": "Title is required"
}
```

## 10. Постраничная выдача

Для большого количества задач используется pagination.

Используются два параметра:

* `page` — номер страницы;
* `limit` — количество задач на странице.

### Запрос

```http
GET /api/tasks?page=2&limit=10
```

Это означает: получить вторую страницу, максимум 10 задач.

### Ответ

```json
{
  "items": [
    {
      "id": 11,
      "title": "Task 11",
      "description": "Description",
      "status": "todo",
      "createdAt": "2026-10-04T10:00:00Z"
    }
  ],
  "pagination": {
    "page": 2,
    "limit": 10,
    "total": 25,
    "totalPages": 3
  }
}
```

Если всего 25 задач и `limit=10`:

```text
Страница 1 → задачи 1–10
Страница 2 → задачи 11–20
Страница 3 → задачи 21–25
```

## 11. Итог

API использует REST-подход и предоставляет полный набор операций:

**Create → Read → Update Status → Delete**

Для большого количества задач используется постраничная выдача через параметры `page` и `limit`.

Ошибки возвращаются с соответствующими HTTP-кодами и понятными сообщениями для клиента.
