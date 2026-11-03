
import os
import requests
from flask import Flask, request

app = Flask(__name__)

TOKEN = os.getenv("TELEGRAM_BOT_TOKEN", "")
SERVER_URL = os.getenv("RENDER_EXTERNAL_URL", "")

def send_message(chat_id, text):
    if not TOKEN:
        return

    requests.post(
        f"https://api.telegram.org/bot{TOKEN}/sendMessage",
        json={"chat_id": chat_id, "text": text},
        timeout=15
    )

@app.route("/")
def home():
    return "MONAI is running!"

@app.route("/webhook", methods=["POST"])
def webhook():
    data = request.get_json(silent=True) or {}
    message = data.get("message", {})
    chat = message.get("chat", {})
    chat_id = chat.get("id")
    text = message.get("text", "").strip().lower()

    if not chat_id:
        return "OK"

    if text.startswith("/start"):
        answer = (
            "🤖 Привет! Я MONAI.\n\n"
            "Я твой тестовый ИИ-помощник!\n\n"
            "Напиши /help, чтобы увидеть команды."
        )
    elif text.startswith("/help"):
        answer = (
            "📚 Команды MONAI:\n"
            "/start — начать\n"
            "/help — помощь\n\n"
            "Пока я работаю в тестовом режиме."
        )
    else:
        answer = (
            "💡 Я получил твоё сообщение!\n"
            "Пока моя ИИ-модель не подключена. "
            "Это тестовая версия MONAI."
        )

    send_message(chat_id, answer)
    return "OK"

if TOKEN and SERVER_URL:
    try:
        requests.post(
            f"https://api.telegram.org/bot{TOKEN}/setWebhook",
            json={"url": SERVER_URL.rstrip("/") + "/webhook"},
            timeout=15
        )
    except requests.RequestException:
        pass
