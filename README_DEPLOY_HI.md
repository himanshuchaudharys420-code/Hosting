# HostX VIP White — Deployment (Hindi)

## सही deployment प्रकार
यह Flask/Python application है। Vercel के Static Build या `npm run build` का उपयोग न करें। Repository में `Dockerfile` और `render.yaml` दिए गए हैं। Render पर **New → Blueprint** चुनकर repository connect करें; service का runtime Docker होना चाहिए।

## जरूरी Environment Variables
- `SECRET_KEY`: Render Blueprint इसे generate करता है। इसे public न करें और GitHub में commit न करें।
- `PORT`: Render स्वयं देता है; Docker command इसे पढ़ता है।

## महत्वपूर्ण free-tier सीमाएँ
- Free web service 15 मिनट idle रहने पर sleep हो सकती है और अगली request पर लगभग एक मिनट में जागती है।
- Free service का local filesystem persistent नहीं है। SQLite database, uploaded files, user server files और logs restart/redeploy/sleep पर खो सकते हैं। Production data के लिए persistent storage/database की जरूरत होगी; free plan इसे पूरा नहीं करता।
- Free service को production या 24/7 uptime के लिए न मानें।
- इस panel में users के Python scripts चलाने की क्षमता है। एक ही app/container में अनजान users का code चलाना सुरक्षित नहीं है। अलग-अलग isolated containers/VMs, resource limits, network restrictions और per-user permissions के बिना public multi-user code execution enable न करें। इस deployment को केवल trusted testing/demo के लिए रखें।

## Vercel की पिछली error का कारण
`package.json` का build script सिर्फ `echo 'Python build ready'` चलाता था; वह `dist/` नहीं बनाता। यह project static React/Vite output नहीं है। Docker runtime Python app चलाता है और `dist` की जरूरत नहीं होती।

## Local syntax check
```bash
python -m py_compile app.py
```

## Local run (dependencies installed होने के बाद)
```bash
python -m pip install -r requirements.txt
gunicorn --bind 0.0.0.0:3000 --workers 1 --threads 4 app:app
```

## Deployment के बाद
`https://YOUR-SERVICE.onrender.com/api/health` खोलकर health endpoint जाँचें। फिर signup/login और dashboard manually test करें। SMTP/email reset flow, uploaded files, subprocess-based user servers और persistent data को production-ready मानने से पहले अलग से test और secure करना होगा।
