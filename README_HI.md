# HostX VIP White — सेटअप गाइड

## 1. Python environment

Python 3.10 या उससे नया version इस्तेमाल करें।

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

## 2. जरूरी environment variables

Production hosting में ये secrets जरूर सेट करें:

- `SECRET_KEY`: लंबी random secret key (कम-से-कम 32 random bytes)
- `ADMIN_EMAIL`: Super Admin का email
- `ADMIN_PASSWORD`: कम-से-कम 12 characters का unique password
- `APP_URL`: वेबसाइट का पूरा HTTPS URL
- `COOKIE_SECURE=1`: HTTPS hosting पर secure session cookies

Secret key बनाने के लिए:

```bash
python -c "import secrets; print(secrets.token_hex(32))"
```

पहली बार database बनते समय `ADMIN_EMAIL` और `ADMIN_PASSWORD` से Super Admin account बनाया/अपडेट किया जाता है। कोई public default admin password नहीं रखा गया है।

## 3. Password reset email (optional)

Forgot Password सुविधा के लिए SMTP settings जरूरी हैं:

- `SMTP_HOST`
- `SMTP_PORT` (आमतौर पर 587; SSL के लिए 465)
- `SMTP_USER`
- `SMTP_PASSWORD`
- `SMTP_FROM`
- `SMTP_USE_TLS=1`

SMTP सेट नहीं होगा तो password reset सुरक्षित रूप से बंद रहेगा; email verification के बिना password बदलने की अनुमति नहीं है।

## 4. Local run

```bash
python app.py
```

Local default port `3000` है। अगर hosting provider `PORT` environment variable देता है, app उसी port पर चलेगा।

## 5. Render deploy

इस repository में `render.yaml` और `Procfile` दिए गए हैं। Render में Blueprint deploy करें और `ADMIN_EMAIL`, `ADMIN_PASSWORD`, `APP_URL` तथा SMTP values भरें।

**Database persistence:** SQLite database और user files को deploy के बाद बचाए रखने के लिए hosting plan में persistent disk जोड़ें और उसे `/data` पर mount करें। Persistent disk उपलब्ध न हो तो redeploy/restart पर local data मिट सकता है।

## 6. महत्वपूर्ण सीमाएँ

यह एक single-instance Flask hosting panel है। Server process tracking और background monitor in-memory हैं, इसलिए Gunicorn को एक worker के साथ चलाया गया है। Multi-worker या multi-instance mode में process state को अलग process manager/queue में migrate किए बिना न चलाएँ।


## 7. सुरक्षा की जरूरी चेतावनी

यह build user के uploaded Python scripts और pip packages को host OS पर subprocess में चलाता है। Environment secrets child process से हटाए गए हैं, लेकिन यह Docker/container या OS-level sandbox का विकल्प नहीं है। सभी scripts एक ही OS account के अधिकारों के साथ चल सकते हैं। इसलिए इसे अजनबी users के लिए public multi-tenant hosting के रूप में deploy न करें। Public hosting से पहले हर server के लिए अलग container/low-privilege user, filesystem isolation, CPU/RAM/process limits और network restrictions लागू करना जरूरी है।
