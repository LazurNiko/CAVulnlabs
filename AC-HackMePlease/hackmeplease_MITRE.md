# MITRE ATT&CK Mapping для CTF (SeedDMS)

> **Мета атаки:** отримання root-доступу
> **Тип:** навчальний CTF
> **Вектор:** Web → DB → RCE → Privilege Escalation

---

## Phase 1 — Network Discovery / Reconnaissance

### Отримання IP-адреси (`netdiscover`)

**Техніка MITRE:**

* **T1018 – Remote System Discovery**
* **T1046 – Network Service Discovery**

**Пояснення:**
Виявлення активних хостів у мережі за допомогою ARP-сканування.

**Артефакти:**

* IP-адреса цільової машини
* MAC-вендор VirtualBox

---

---

## Phase 2 — Service Enumeration

### Сканування портів (`nmap -sC -sV -p-`)

**Техніка MITRE:**

* **T1046 – Network Service Discovery**
* **T1082 – System Information Discovery**

**Пояснення:**
Виявлення:

* відкритих портів;
* сервісів;
* версій ПЗ;
* ОС та банерів.

**Результат:**

* HTTP (Apache)
* MySQL

---

---

## Phase 3 — Web Content Discovery

### Перебір директорій (`gobuster dir`)

**Техніка MITRE:**

* **T1083 – File and Directory Discovery**

**Пояснення:**
Перелік прихованих ресурсів веб-серверу:

* `/seeddms51x/`
* `/conf/`
* конфігураційні файли.

---

---

## Phase 4 — Sensitive Information Discovery

### Доступ до `settings.xml`

**Техніка MITRE:**

* **T1552.001 – Unsecured Credentials: Credentials in Files**
* **T1083 – File and Directory Discovery**

**Пояснення:**
Отримання облікових даних із відкритого конфігураційного файлу.

**Здобуті дані:**

* MySQL credentials (`seeddms:seeddms`)

---

## Phase 5 — Database Enumeration

### Доступ до MySQL

**Техніка MITRE:**

* **T1078 – Valid Accounts**
* **T1046 – Network Service Discovery**

**Пояснення:**
Використання реальних облікових даних для доступу до сервісу.

---

### Перегляд таблиць та користувачів

**Техніка MITRE:**

* **T1213 – Data from Information Repositories**

**Пояснення:**
Отримання:

* облікових записів;
* хешів паролів;
* внутрішніх структур додатку.

---

## Phase 6 — Credential Manipulation

### Підміна хешу адміністратора

**Техніка MITRE:**

* **T1098 – Account Manipulation**

**Пояснення:**
Зміна пароля адміністратора напряму у БД для захоплення облікового запису.

---

## Phase 7 — Initial Access via Web Application

### Логін як admin у SeedDMS

**Техніка MITRE:**

* **T1078 – Valid Accounts**

**Пояснення:**
Використання скомпрометованого облікового запису.

---

## Phase 8 — Command Execution

### Завантаження PHP-файлу та його виконання

**Техніка MITRE:**

* **T1059.003 – Command and Scripting Interpreter: PHP**
* **T1105 – Ingress Tool Transfer**
* **T1190 – Exploit Public-Facing Application**

**Пояснення:**
Виконання коду через уразливість механізму upload та обробки файлів.

---

## Phase 9 — Shell Upgrade

### Отримання інтерактивного TTY

**Техніка MITRE:**

* **T1059 – Command and Scripting Interpreter**
* **T1106 – Native API**

**Пояснення:**
Покращення стабільності shell.

---

## Phase 10 — Lateral Movement (Local)

### Перехід до користувача `saket`

**Техніка MITRE:**

* **T1078 – Valid Accounts**

**Пояснення:**
Використання відомих облікових даних.

---

## Phase 11 — Privilege Escalation

### Використання sudo без пароля

**Техніка MITRE:**

* **T1548.003 – Abuse Elevation Control Mechanism: Sudo**
* **T1068 – Exploitation for Privilege Escalation**

**Пояснення:**
Отримання root-доступу.

---

# Зведена таблиця (для звіту / презентації)

| Етап                 | Дія          | MITRE Technique  |
| -------------------- | ------------ | ---------------- |
| Network Discovery    | netdiscover  | T1018, T1046     |
| Port Scanning        | nmap         | T1046, T1082     |
| Web Enumeration      | gobuster     | T1083            |
| Credential Discovery | settings.xml | T1552.001        |
| DB Enumeration       | MySQL        | T1213            |
| Account Takeover     | Update hash  | T1098            |
| Valid Login          | Admin login  | T1078            |
| RCE                  | PHP upload   | T1059.003, T1190 |
| Shell Upgrade        | script / pty | T1106            |
| User Pivot           | su saket     | T1078            |
| Privilege Escalation | sudo         | T1548.003        |

---
