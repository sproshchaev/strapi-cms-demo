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

Перейти в терминал (из .md не работает) - внутри контейнера создайте новый проект Strapi командой:
```bash
npx create-strapi@latest my-project --quickstart --no-run --skip-db
```

Подтвердить запуск процесса: 
```angular2html
Need to install the following packages:
create-strapi@5.33.3
Ok to proceed? (y)
... 
```

На следующем шаге выбрать Skip:
```
🚀 Welcome to Strapi! Ready to bring your project to life?
 
Create a free account and get:
30 days of access to the Growth plan, which includes:
✨ Strapi AI: content-type builder, media library and translations
✅ Live Preview
✅ Single Sign-On (SSO) login
✅ Content History
✅ Releases

? Please log in or sign up. (Use arrow keys)
  Login/Sign up 
❯ Skip 
```

И указать N
```angular2html
? Please log in or sign up. Skip
? Participate in anonymous A/B testing (to improve Strapi)? (y/N)

Strapi   Creating a new application at /my-project

deps   Installing dependencies with npm
npm warn deprecated tar@6.2.1: Old versions of tar are not supported, and contain widely publicized security vulnerabilities, which have been fixed in the current version. Please update. Support for old versions may be purchased (at exhorbitant rates) by contacting i@izs.me
...
npm notice New minor version of npm available! 11.6.2 -> 11.7.0
npm notice Changelog: https://github.com/npm/cli/releases/tag/v11.7.0
npm notice To update run: npm install -g npm@11.7.0
npm notice
/ #
```

## Шаг 4: Переход в директорию проекта

Перейдите в созданную директорию проекта:
```bash
cd /app/my-project (по этой инструкции создается на уровне с app, поэтому переходим cd my-project)
```

## Шаг 5: Запуск Strapi в режиме разработки

Запустите Strapi в режиме разработки:
```bash
npm run develop
```

```angular2html
> my-project@0.1.0 develop
> strapi develop
...

┌─────────────────────────────┐
│ http://localhost:1337/admin │
└─────────────────────────────┘

[2026-01-19 07:50:40.465] info: Strapi started successfully
```

## Шаг 6: Доступ к админ-панели

После успешного запуска Strapi будет доступен по адресу:
```
http://localhost:1337/admin
```

Регистриуемся:
- First name
- Email
- Password
[Let'sStart]


Следуйте инструкциям в браузере для создания учетной записи администратора.

## Примечания

- Проект использует SQLite в качестве базы данных по умолчанию
- При первом запуске могут появиться предупреждения о deprecated пакетах - это нормально
- Порт 1337 автоматически пробрасывается из контейнера на хост-систему
- Контейнер настроен как временный (`--rm`) и удалится после завершения работы

# 🚀 Краткая инструкция по настройке Strapi API

## 1. Создание коллекции Person
**Content-Type Builder → Create new collection type → Fill:**
- Display name: `person`
- API ID (Singular): `person`
- API ID (Plural): `person`
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