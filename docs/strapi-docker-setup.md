# Установка и запуск Strapi через Docker

## Шаг 1: Подготовка окружения

Убедитесь, что у вас установлен Docker. Для проверки выполните:
```bash
docker --version
```

## Шаг 2: Скачивание Docker образа Node.js

Скачайте актуальный образ Node.js версии 24 Alpine:
```bash
docker pull node:24-alpine
```

## Шаг 3: Запуск контейнера и создание проекта

Запустите интерактивную сессию в контейнере с монтированием вашей директории:
```bash
docker run -it --rm \
  -v ~/strapi:/app \
  -p 1337:1337 \
  --name strapi-dev \
  node:24-alpine sh
```

Внутри контейнера создайте новый проект Strapi:
```bash
npx create-strapi@latest my-project --quickstart --no-run --skip-db
```

## Шаг 4: Переход в директорию проекта

Перейдите в созданную директорию проекта:
```bash
cd /app/my-project
```

## Шаг 5: Запуск Strapi в режиме разработки

Запустите Strapi в режиме разработки:
```bash
npm run develop
```

## Шаг 6: Доступ к админ-панели

После успешного запуска Strapi будет доступен по адресу:
```
http://localhost:1337/admin
```

Следуйте инструкциям в браузере для создания учетной записи администратора.

## Примечания

- Проект использует SQLite в качестве базы данных по умолчанию
- При первом запуске могут появиться предупреждения о deprecated пакетах - это нормально
- Порт 1337 автоматически пробрасывается из контейнера на хост-систему
- Контейнер настроен как временный (`--rm`) и удалится после завершения работы

# 🚀 Краткая инструкция по настройке Strapi API

## 1. Создание коллекции Person
**Content-Type Builder → Create new collection type → Fill:**
- Display name: `Person`
- API ID (Singular): `person`
- API ID (Plural): `persons`
- **Continue**

## 2. Добавление полей
**Добавляем два поля:**

### Поле "name":
- Тип: **Text**
- Настройки:
  ```
  Field name: name
  Text format: Short text
  [x] Required field
  ```

### Поле "age":
- Тип: **Number**
- Настройки:
  ```
  Field name: age
  Number format: integer
  [x] Required field
  Minimum value: 0
  Maximum value: 150
  ```

**→ Finish → Save** (ждать 30-60 сек)

## 3. Настройка API токена
**Settings → API Tokens → Create new API Token:**
- Token name: `Read Only`
- Token type: `Custom`
- Выбрать для Person: **find** и **findOne**
- **Save** → скопировать токен

## 4. Тестирование API в Postman

### GET запрос:
```
GET http://localhost:1337/api/persons
```

### Заголовок (Headers):
```
Authorization: Bearer ваш_скопированный_токен
```

## 5. Создание тестовой записи
**Content Manager → Person → Create new entry:**
- name: `Jon`
- age: `10`
- **Save → Publish**

## ✅ Проверка
После этих шагов `GET /api/persons` должен вернуть:
```json
{
  "data": [{
    "id": 1,
    "attributes": {
      "name": "Jon",
      "age": 10,
      "createdAt": "...",
      "updatedAt": "..."
    }
  }]
}
```

---

## ⚡ Если не работает:
1. **404** → Убедиться, что нажали "Save" в Content-Type Builder
2. **403** → Проверить, что токен добавлен в Headers (не в Params!)
3. **Пустой ответ** → Создать запись через Content Manager

## 🔑 Важно:
- Токен добавлять в **Headers**, а не в Params
- После сохранения коллекции ждать 30-60 секунд
- Автоматические поля (id, createdAt) НЕ добавлять