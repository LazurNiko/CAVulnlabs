#  OWASP Top 10 / CWE — мапінг вразливостей CTF VM *Bob*

> Версія OWASP: **OWASP Top 10 (2021)**
> Фокус: web + system misconfiguration (відповідає реальному attack chain)

---

## 📊 Таблиця мапінгу

| #  | Вразливість                                       | OWASP Top 10                                    | CWE                | Пояснення                                                                        |
| -- | ------------------------------------------------- | ----------------------------------------------- | ------------------ | -------------------------------------------------------------------------------- |
| 1  | Відсутність мережевої сегментації та ARP-контролю | A05: Security Misconfiguration                  | CWE-284            | Відсутній контроль доступу на мережевому рівні, хост повністю видимий у сегменті |
| 2  | Надмірно відкрита поверхня атаки (HTTP, SSH)      | A05: Security Misconfiguration                  | CWE-693            | Сервіси доступні без firewall, rate limiting або фільтрації                      |
| 3  | Розкриття сервісів та версій через Nmap           | A05: Security Misconfiguration                  | CWE-200            | Сервіси повертають надлишкову інформацію (version disclosure)                    |
| 4  | Розкриття чутливих шляхів через `robots.txt`      | A01: Broken Access Control                      | CWE-548            | `robots.txt` використовується замість механізмів контролю доступу                |
| 5  | Небезпечний debug endpoint (`dev_shell.php`)      | A03: Injection                                  | CWE-78             | Можливість виконання системних команд через веб                                  |
| 6  | Відсутність sandbox / MAC для команд              | A05: Security Misconfiguration                  | CWE-250            | Команди виконуються без AppArmor / SELinux / обмежень                            |
| 7  | Зберігання паролів у відкритому вигляді           | A02: Cryptographic Failures                     | CWE-522            | Паролі збережені у plaintext-файлах                                              |
| 8  | Повторне використання паролів                     | A07: Identification and Authentication Failures | CWE-Reuse-Password | Один пароль використовується для різних рівнів доступу                           |
| 9  | Hardcoded GPG passphrase у `notes.sh`             | A02: Cryptographic Failures                     | CWE-798            | Парольна фраза збережена у скрипті                                               |
| 10 | Небезпечне управління секретами                   | A02: Cryptographic Failures                     | CWE-312            | Чутливі дані доступні звичайним користувачам                                     |
| 11 | Відсутність принципу Least Privilege              | A01: Broken Access Control                      | CWE-269            | Користувач має надмірні привілеї                                                 |
| 12 | Некоректна конфігурація sudo                      | A05: Security Misconfiguration                  | CWE-732            | `sudo ALL=(ALL) ALL` без обмежень                                                |
| 13 | Повна ескалація привілеїв до root                 | A01: Broken Access Control                      | CWE-284            | Відсутній контроль доступу на рівні ОС                                           |

---

##  Ключові навчальні акценти 

###  Що тут **не є криптографічною проблемою**

* GPG **використаний коректно**
* проблема **не в алгоритмі**
* проблема — **в управлінні секретами**

>  **Strong crypto ≠ secure system**

---

### Як виглядає attack chain у термінах OWASP

```
A05 Misconfiguration
 → A01 Broken Access Control
 → A03 Injection (RCE)
 → A02 Cryptographic Failures
 → A07 Auth Failures
 → A01 Root Compromise
```