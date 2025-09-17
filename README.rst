
import requests
import time

TOKEN = "8337407053:AAGI-VhTjgn1Z4-9xw86gLSyehYZpq5f9N0"
COMMAND = "rape"  # تقدر تغيّرها لأي كلمة
REPLY_TEXT = "has raped"  # النص اللي يظهر بعد اسم المستخدم

API_URL = f"https://api.telegram.org/bot{TOKEN}/"

last_update_id = None

def get_updates():
    url = API_URL + "getUpdates"
    r = requests.get(url)
    return r.json()

def send_message(chat_id, text):
    url = API_URL + "sendMessage"
    payload = {"chat_id": chat_id, "text": text}
    requests.post(url, data=payload)

while True:
    updates = get_updates()
    for update in updates.get("result", []):
        update_id = update["update_id"]
        if last_update_id is None or update_id > last_update_id:
            last_update_id = update_id

            if "message" in update:
                message = update["message"]
                text = message.get("text", "")
                chat_id = message["chat"]["id"]
                user_name = message["from"]["username"] or message["from"]["first_name"]

                if text.lower().startswith(COMMAND):
                    parts = text.split()
                    if len(parts) < 2:
                        send_message(chat_id, f"استعمل الأمر هكذا: {COMMAND} @username")
                    else:
                        target = " ".join(parts[1:])
                        reply = f"@{user_name} {REPLY_TEXT} {target}"
                        send_message(chat_id, reply)
    time.sleep(1)