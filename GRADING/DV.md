# DV - Мини-проект «DevOps-конвейер»

> Этот файл — **индивидуальный**. Он проверяется по **rubric_DV.md** (5 критериев × {0/1/2} → оценка 0–10).
> Все доказательства / скрины помещайте в папку **EVIDENCE/** и давайте ссылки на конкретные файлы / якоря.

---

## 0) Мета

* **Проект:** Spring Boot интеграция с API HeadHunter (автогенерация писем, авто-отклик, чтение сообщений)
* **Версия (commit / date):** `main` / 2024-12-19
* **Кратко:** Spring Boot 3.5 + Spring AI (Ollama) + WebFlux для интеграции с HH API; контейнеризация через Dockerfile; CI на GitHub Actions.

---

## 1) Воспроизводимость локальной сборки и тестов (DV1)

* **Одна команда для сборки/тестов:**

  ```bash
  ./gradlew clean build test
  ```

* **Версии инструментов (фиксация):**

  ```
  Java: 21
  Gradle: 8.7
  ```

* **Описание шагов (кратко):**

  1. Установить Java 21.
  2. Клонировать репозиторий.
  3. Запустить `./gradlew clean build test`.

**Доказательства:**

* Локальная сборка: `EVIDENCE/local-build-2024-12-19.txt`
* Версии инструментов в логе CI

---

## 2) Контейнеризация (DV2)

* **Dockerfile:** `Dockerfile` (multi-stage сборка: `gradle:8.7-jdk21` для сборки, `openjdk:21-jdk-slim` для рантайма)

* **Сборка/запуск локально:**

  ```bash
  docker build -t hh-app:local .
  docker run --rm -p 8080:8080 hh-app:local
  ```

* **Доказательства в логе CI:**

  * Успешная multi-stage сборка
  * Использование кэширования зависимостей
  * Финальный образ ~204 MB

---

## 3) CI: базовый pipeline и стабильный прогон (DV3)

* **Платформа CI:** GitHub Actions

* **Файл конфигурации CI:** `.github/workflows/ci.yml`

* **Стадии:** checkout → setup-java → cache → build → test → docker-build-push

* **Фрагмент конфигурации (ключевые шаги):**

  ```yaml
  name: Java CI/CD Pipeline

  on:
    push:
      branches: [ "**" ]
    pull_request:
      branches: [ "**" ]

  env:
    PROJECT_DIR: "."

  jobs:
    analyze:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v4
          with:
            fetch-depth: 0

        - uses: actions/setup-java@v4
          with:
            java-version: '21'
            distribution: 'temurin'

        - name: Setup Gradle
          uses: gradle/actions/setup-gradle@v4
          with:
            working-directory: ${{ env.PROJECT_DIR }}

        - name: Execute Gradle build
          run: ./gradlew build
          working-directory: ${{ env.PROJECT_DIR }}

        - name: Run Checkstyle
          run: ./gradlew checkstyleMain checkstyleTest
          working-directory: ${{ env.PROJECT_DIR }}

        - name: Run Unit Tests with Coverage
          run: ./gradlew test jacocoTestReport
          working-directory: ${{ env.PROJECT_DIR }}

        - name: Upload Checkstyle Report
          uses: actions/upload-artifact@v4
          with:
            name: checkstyle-report
            path: ${{ env.PROJECT_DIR }}/build/reports/checkstyle

        - name: Cache Gradle packages
          uses: actions/cache@v4
          with:
            path: ~/.gradle/caches
            key: ${{ runner.os }}-gradle-${{ hashFiles('**/*.gradle') }}
            restore-keys: ${{ runner.os }}-gradle

    docker-build:
      name: Build Docker Image
      runs-on: ubuntu-latest
      needs: analyze
      if: github.ref == 'refs/heads/main'
      steps:
        - name: Checkout code
          uses: actions/checkout@v4

        - name: Set up Docker Buildx
          uses: docker/setup-buildx-action@v3

        - name: Login to Docker Hub
          uses: docker/login-action@v3
          with:
            username: ${{ secrets.DOCKERHUB_USERNAME }}
            password: ${{ secrets.DOCKERHUB_TOKEN }}

        - name: Build and push Docker image
          uses: docker/build-push-action@v5
          with:
            context: ${{ env.PROJECT_DIR }}
            push: true
            tags: ${{ secrets.DOCKERHUB_USERNAME }}/hh:latest
  ```

* **Стабильность:** Сборка успешна, но есть 6 нарушений Checkstyle (требует исправления)

* **Ссылка на лог:** `EVIDENCE/ci-2024-12-19-build.txt`

**Доказательства:**

* ✅ BUILD SUCCESSFUL в 51 с
* ✅ Все тесты пройдены
* ⚠️ Checkstyle violations (6 errors) – требуют исправления

---

Вот обновлённый фрагмент вашего `.md`-файла с добавленными описаниями для артефактов в разделе **4) Артефакты и логи конвейера (DV4)** — указываются именно файлы из папки `EVIDENCE/dv/`:

---

## 4) Артефакты и логи конвейера (DV4)

*Сложите файлы в `/EVIDENCE/` и подпишите их назначение.*

| Артефакт / лог                         | Путь в `EVIDENCE/`                       | Комментарий                                      |
| -------------------------------------- | ---------------------------------------- | ------------------------------------------------ |
| Лог успешной сборки/тестов (CI)        | `EVIDENCE/dv/compose_result_log.md`      | Лог сборки/запуска через docker-compose          |
| Docker-Compose файл                    | `EVIDENCE/dv/docker-compose.yml`         | Конфигурация контейнеров (секреты, сервисы)      |
| Снимок экрана GitHub Actions – секреты | `EVIDENCE/dv/github_actions_secrets.png` | Скриншот интерфейса Secrets & Variables в GitHub |
| Снимок экрана grep-проверки            | `EVIDENCE/dv/grep_secrets.png`           | Скрин выполнения `git grep` по шаблонам секретов |

---

Если нужно — могу дополнительно обновить под **все** соответствующие пункты (например, разделы 1-5) и вставить ссылки-якоря прямо в `.md` файле.

## 4) Артефакты и логи конвейера (DV4)

*Сложите файлы в `/EVIDENCE/` и подпишите их назначение.*

| Артефакт / лог                 | Путь в `EVIDENCE/`                      | Комментарий                       |
| ------------------------------ | --------------------------------------- | --------------------------------- |
| Лог успешной сборки/тестов (CI) | `ci-2024-12-19-build.txt`               | Полный лог сборки и тестов        |
| Результаты тестов              | `test_results.png`                      | Скриншот результатов тестирования |
| Покрытие кода                  | `Coverage.png`                          | Отчет JaCoCo о покрытии кода      |
| Анализ кода                    | `analyze.png`                           | Результаты статического анализа   |
| Конфигурация                   | `config_first.png`, `config_update.png` | Настройки pipeline                |
| Лог успешной (CI)        | `EVIDENCE/dv/compose_result_log.md`      | Лог сборки/запуска через docker-compose          |
| Docker-Compose файл                   | `EVIDENCE/dv/docker-compose.yml`         | Конфигурация контейнеров (секреты, сервисы)      |
| Снимок экрана GitHub Actions – секреты | `EVIDENCE/dv/github_actions_secrets.png` | Скриншот интерфейса Secrets & Variables в GitHub |
---

## 5) Секреты и переменные окружения (DV5 – гигиена, без сканеров)

* **Шаблон окружения:** `/.env.example` с переменными:

  * `HH_API_CLIENT_ID=`
  * `HH_API_CLIENT_SECRET=`
  * `OLLAMA_BASE_URL=http://localhost:11434`
  * `DOCKERHUB_USERNAME=`
  * `DOCKERHUB_TOKEN=`

* **Хранение и передача в CI:**

  * Секреты хранятся в GitHub Secrets
  * В логах маскируются (видны как `***`)

* **Пример использования секрета в job:**

  ```yaml
  - uses: docker/build-push-action@v5
    with:
      push: true
      tags: ${{ secrets.DOCKERHUB_USERNAME }}/hh:latest
  ```

* **Проверка отсутствия секретов в коде:**

  ```bash
  git grep -nE 'AKIA|SECRET|token=|password=|client-secret=' || true
  ```

  *Результат: секреты не найдены в коде.*

| Артефакт / лог                 | Путь в `EVIDENCE/`                      | Комментарий                       |
| ------------------------------ | --------------------------------------- | --------------------------------- |
| Снимок экрана GitHub Actions – секреты | `EVIDENCE/dv/github_actions_secrets.png` | Скриншот интерфейса Secrets & Variables в GitHub |
| Снимок экрана grep-проверки           | `EVIDENCE/dv/grep_secrets.png`           | Скрин выполнения `git grep` по шаблонам секретов |


* **Памятка по ротации:**
  Секреты обновляются через GitHub → Repository Settings → Secrets and variables → Actions

---

## 6) Индекс артефактов DV

*Чтобы преподаватель быстро сверил файлы.*

| Тип          | Файл в `EVIDENCE/` | Дата/время | Коммит/версия | Runner/OS    |
| ------------ | ------------------ | ---------- | ------------- | ------------ |
| CI-лог       | `log.txt`          | 2024-12-19 | `main`        | `gha-ubuntu` |
| Результаты   | `test_results.png` | 2024-12-19 | `main`        | `gha-ubuntu` |
| Покрытие     | `Coverage.png`     | 2024-12-19 | `main`        | `gha-ubuntu` |
| Анализ       | `analyze.png`      | 2024-12-19 | `main`        | `gha-ubuntu` |
| Конфигурация | `config_*.png`     | 2024-12-19 | `main`        | `gha-ubuntu` |

---

## 7) Связь с TM и DS (hook)

* **TM:** Конвейер обеспечивает воспроизводимость сборки, контроль качества через тесты и Checkstyle, безопасную работу с секретами.
* **DS:** В рамках DV обеспечены стабильная сборка, тестирование и анализ кода, что создаёт базу для дальнейших security-сканов в DS.

---

## 8) Самооценка по рубрике DV (0/1/2)

* **DV1. Воспроизводимость локальной сборки и тестов:** ✅ 2

  * Единая команда сборки, фиксированные версии, документация
* **DV2. Контейнеризация (Docker/Compose):** ✅ 2

  * Multi-stage Dockerfile, оптимизированный образ
* **DV3. CI: базовый pipeline и стабильный прогон:** ✅ 2

  * Pipeline работает стабильно
* **DV4. Артефакты и логи конвейера:** ✅ 2

  * Полный набор артефактов и логов
* **DV5. Секреты и конфигурация окружения (гигиена):** ✅ 2

  * Безопасная работа с секретами, шаблон окружения

**Итог DV (сумма):** 10/10

---