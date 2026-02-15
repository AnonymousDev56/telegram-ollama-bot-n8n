# Telegram AI Assistant (n8n + Ollama)

Локальный Telegram-бот, который принимает сообщения, отправляет их в Ollama через n8n и возвращает ответ пользователю.

<img width="1919" height="995" alt="01-workflow-editor" src="https://github.com/user-attachments/assets/563b7fe3-fb67-4f30-bf7c-cc724ac62fe2" />

<img width="1919" height="988" alt="02-execution-success" src="https://github.com/user-attachments/assets/5d1b1707-271d-4632-9a36-60b802f335ea" />

<img width="1266" height="938" alt="03-telegram-chat (2)" src="https://github.com/user-attachments/assets/716ed979-db2e-40a2-8af8-aa8b2faf37ba" />

## Stack
- n8n (Docker)
- Telegram Bot API
- Ollama (`llama3` или `mistral`)
- HTTP Request node

## Architecture
`User -> Telegram Bot -> n8n Workflow -> Ollama API -> n8n -> Telegram Bot -> User`

## Project structure
```text
telegram-ai-bot/
├── README.md
├── workflow.json
├── docker-compose.yml
├── .env.example
└── screenshots/
```

## Requirements
- Установлен `Docker` + `Docker Compose`
- Установлен `Ollama`
- Создан Telegram bot token через `@BotFather`

## How to run
1. Запустите Ollama:
```bash
ollama serve
```

2. Скачайте модель (один раз):
```bash
ollama pull llama3
```

3. Проверьте API Ollama:
```bash
curl http://localhost:11434/api/generate \
  -d '{"model":"llama3","prompt":"Hello","stream":false}'
```

4. Подготовьте env для n8n:
```bash
cp .env.example .env
```

5. Запустите n8n через Docker:
```bash
docker compose up -d
```

6. Откройте UI n8n:
- `http://localhost:5678`

7. В n8n:
- Import from file -> `workflow.json`
- В `Telegram Trigger` и `Telegram Send Message` настройте Telegram credentials
- В `HTTP Request -> Ollama` должен быть URL:
  - `http://host.docker.internal:11434/api/generate`

8. Активируйте workflow и отправьте сообщение боту в Telegram.

## Useful commands
```bash
docker compose ps
docker compose logs -f n8n
docker compose down
```

## Functional requirements coverage
- FR-1: Telegram Trigger принимает текст
- FR-2: HTTP Request отправляет prompt в Ollama
- FR-3: Получает JSON-ответ Ollama
- FR-4: Telegram Send Message отправляет `reply`
- FR-5: Работает 24/7 при запущенном контейнере n8n

## Test cases
1. `Hello` -> приветствие
2. `What is Python?` -> объяснение Python
3. `Tell me a joke` -> шутка

## Notes
- Если Ollama не отвечает, проверьте `ollama serve` и `docker compose logs -f n8n`.
- Время ответа зависит от мощности ПК и размера модели.
