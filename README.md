# mitre_example
Пример APT, выдуманный из головы.

Предположим, что мы узнали о компании, которая очень гордится тем, что все ее сотрудники сидят на убунтах, а не на винде. Ну, например, увидели статью на хабре навроде "Как и зачем учить буххгалтеров работать с UNIX и получать удовольтвие" или что-то такое. Предполжиим, что из нее мы знаем, что пользователи сидят на Ubuntu 20.04, а также несколько имен сотрудников компании. Чутье подсказывает, что убунты могли довно не обновляться.

# Reconnaissance
С помощью OSINT находим список сотрудников компании. Например это можно делать через их личные социальные сети или корпоративный LinkedIn. Для того, чтобы узнать формат их корпоративных почт, можно использовать открытые базы, например [email-format](https://www.email-format.com).

Техники MITRE ATT&CK® Matrix:
- [Search Open Websites/Domains: Social Media](https://attack.mitre.org/techniques/T1593/001/).
- [Gather Victim Network Information: Domain Properties](https://attack.mitre.org/techniques/T1590/001/).

# Initial Access
Рассылаем с адреса, похожего на админский, фишинг-письмо примерно следующего содержания. 
```
Иван Иванович,

Пожалуйста запуститите файлик во вложении, чтобы установить новый корпоративный патч безопасности. 
Сделайте это особенно резво, если не давно обновлялись.

Администрация
```

Во вложение реверс-шелл.

Техники MITRE ATT&CK® Matrix:
- [Phishing: Spearphishing Attachment](https://attack.mitre.org/techniques/T1566/001/).

# Execution

Ничего не подозревающий юзер выполняет файл с реверс-шеллом.
 
Техники MITRE ATT&CK® Matrix:
- [User Execution: Malicious File](https://attack.mitre.org/techniques/T1204/002/).


# C2C

Запускаем реверс шелл, заходим c него в терминал. Теперь у нас права юзера на системе.

Техники MITRE ATT&CK® Matrix:
- [Remote Access Software](https://attack.mitre.org/techniques/T1219/).

# Privellege Escallation

Запускаем заранее заотовленный скрипт, выполняющий создание нового рутового пользователя, эксплуатирующий уязвимость [CVE-2021-3560](https://github.blog/2021-06-10-privilege-escalation-polkit-root-on-linux-with-bug/#history). По ссылке подробное описание. Проверено на старой убунте лично -- эксплойт работает.

Теперь мы знаем пароль от рутового пользователя на этой машине.

Техники MITRE ATT&CK® Matrix:
- [Abuse Elevation Control Mechanism](https://attack.mitre.org/techniques/T1548/).


# Persistence

Вписываемся в `/etc/init.d` запуск нашего реверс шелла при старте машины. Редактируем файл от имени нашего нового юзера.

Техники MITRE ATT&CK® Matrix:
- [Boot or Logon Initialization Scripts: RC Scripts](https://attack.mitre.org/techniques/T1037/004/).

# Defense Evasion

Наш реверс шелл будет запускаться при запуске от имени рута. Теперь удаляем нашего юзера. Можем посмотреть, какие логи есть на системе, и почистить их.

Техники MITRE ATT&CK® Matrix:
- [Indicator Removal on Host](https://attack.mitre.org/techniques/T1070/).

# Impact

Все -- мы подключены в любое время к системе с рута. Можем делать что хотели.
В качестве **Lateral Movement** можем сканировать nmapом другие хосты в локальной сети, искать возможности распространяться через другие сервисы вбок.
Можем майнить по ночам на этих машинах.
Мы можем например зашифровать систему и требовать выкуп. 
Дальше уже в зависимости от устройства корп. сети.
