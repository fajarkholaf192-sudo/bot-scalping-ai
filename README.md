import os
import time
import requests
from google import genai

TELEGRAM_BOT_TOKEN = os.environ.get("TELEGRAM_BOT_TOKEN", "9039033684:AAGcG9Le1SAgl9PnU3AtXnaWoJo2l2POt0I")
TELEGRAM_CHAT_ID = os.environ.get("TELEGRAM_CHAT_ID", "8854886688")
GEMINI_API_KEY = os.environ.get("GEMINI_API_KEY", "AQ.Ab8RN6Jy65CNaWd-khub0NT-gXHxQS0As8EH8IYMKVVKl1Ikrg")

client = genai.Client(api_key=GEMINI_API_KEY)

def send_telegram_message(message):
    url = f"https://api.telegram.org/bot{TELEGRAM_BOT_TOKEN}/sendMessage"
    payload = {
        "chat_id": TELEGRAM_CHAT_ID,
        "text": message,
        "parse_mode": "Markdown"
    }
    try:
        requests.post(url, json=payload)
    except Exception as e:
        print(f"Error kirim Telegram: {e}")

def get_market_analysis():
    prompt = """
Lu adalah asisten scalper Gold/XAUUSD profesional.
Berikan 1 setup sinyal scalping hipotetis terbaik untuk sesi saat ini.

Format Balasan (Singkat & Padat):
📊 **ANALISIS SCALPING XAUUSD**
• **Action:** BUY / SELL / WAIT
• **Entry Zone:** 27xx.xx
• **Stop Loss (SL):** 27xx.xx
• **Take Profit (TP):** 27xx.xx
💡 **Alasan:** (1 kalimat alasan teknikal)
"""
    try:
        response = client.models.generate_content(
            model="gemini-2.5-flash",
            contents=prompt,
        )
        return response.text
    except Exception as e:
        return f"Gagal analisis: {e}"

def main():
    send_telegram_message("🚀 *Bot Scalping AI Berhasil Aktif di Render!*")
    while True:
        analysis = get_market_analysis()
        send_telegram_message(analysis)
        time.sleep(900)

if __name__ == "__main__":
    main()
