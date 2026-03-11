# strapi-cms-demo

Инструкция из 2-го варианта [strapi-docker-setup.md](docs/strapi-docker-setup.md)

---

Что делал по шагам

1 Установить node js в Docker 

Копируем и вставляем этот многострочный скрипт в терминал IDE:
https://nodejs.org/en/download
```bash
# Docker has specific installation instructions for each operating system.
# Please refer to the official documentation at https://docker.com/get-started/

# Pull the Node.js Docker image:
docker pull node:24-alpine

# Create a Node.js container and start a Shell session:
docker run -it --rm --entrypoint sh node:24-alpine

# Verify the Node.js version:
node -v # Should print "v24.13.0".

# Verify npm version:
npm -v # Should print "11.6.2".
```

После работы этого скрипта можно прямо в терминале IDE вести диалог, а в логах контейнера дублируется вывод информации 
о процессе

--- 

2. Перейти в Докер в контейнер в exec 

Ты уже запустил контейнер на базе node:24-alpine (или node:22-alpine, судя по скриншоту 
— nice_wozniak, Exec открыт, ты внутри как root, и ls показывает стандартные папки Linux).

Чтобы запустить Strapi внутри этого контейнера, есть два основных сценария:

Сценарий 1: Создать новый Strapi-проект прямо внутри контейнера (рекомендую для теста, 
без Node.js на хосте)

Это самый чистый способ, если ты хочешь избежать установки Node.js локально.

Создай папку для проекта (чтобы файлы сохранились после выхода из контейнера — используй 
volume!):

```Bash
mkdir /app && cd /app
```
(Если volume не примонтирован — файлы пропадут при остановке контейнера. Лучше выйди, 
останови контейнер и перезапусти с volume — см. ниже.)

Создай проект Strapi (используй npx — он уже доступен в node-образе):

```bash
npx create-strapi@latest my-project --quickstart --no-run --skip-db
```
--quickstart — упрощённый режим (SQLite по умолчанию).
--no-run — не запускать сервер автоматически.
--skip-db — пропустить вопросы про БД (SQLite создастся автоматически в .tmp).

Или с Yarn (если предпочитаешь):

```Bash
yarn create strapi@latest my-project --quickstart --no-run --skip-db
```
Процесс займёт 1–5 минут (зависит от интернета — скачивает пакеты).

Перейди в проект и запусти:

```Bash
cd my-project
yarn develop # или npm run develop
```
Strapi запустится на порту 1337 внутри контейнера.
Чтобы увидеть в браузере:
В Docker Desktop → Ports → Forward порт 1337 контейнера на хост (или перезапусти контейнер с -p 1337:1337).
Открой http://localhost:1337/admin → создай первого админа.

Важно: Чтобы файлы проекта остались на хосте (в твоей папке на Mac), нужно примонтировать volume.
Останови текущий контейнер (Trash иконка), создай папку на хосте (например ~/strapi-project), и запусти новый контейнер так:
Bashdocker run -it --rm \
-v ~/strapi-project:/app \
-p 1337:1337 \
node:24-alpine sh
Затем внутри: cd /app, npx create-strapi@latest my-project ..., cd my-project, yarn develop.

---

Вариант 2 с портом 1337

1) В IDE перейти в папку проекте strapi

2) в терминале IDE запустить (скопировать и вставить в терминал, не в md)
```bash
docker run -it --rm \
  -v ~/strapi:/app \
  -p 1337:1337 \
  --name strapi-dev \
  node:24-alpine sh
```
3) перейти в mkdir /app && cd /app

4) Запустить 
```bash
yarn create strapi@latest my-project --quickstart --no-run --skip-db
```

5)  Please log in or sign up. - делаем стрелку вниз и skip 

7) Отказаться от предоставления информации: N
И далее будет процесс установки: 
```angular2html
Creating a new application at /app/my-project
...
/app #
```

8) Далее пишем:
```angular2html
cd my-project
npm run develop
```

9) Переходим по http://localhost:1337/admin 

10) Регистрируемся и входим в Strapi 
