# Скрипт создает /var/log/monitoring.log, отсылается на test.com каждые 60 секунд с помощью curl, ответ ждет 10.
# При неудаче работает функция badrequest, логирующая неудачу.
# Так же выставлен "trap" на сигнал перезагрузки (или CTRL + C), который логирует этот сигнал.
# Для работы сервиса необходимо настроить локальное окружение:
```bash
ExecStart=home/user/script (У вас свой путь)
WorkingDirectory=home/user/ (У вас свой путь)
```
# Могут быть огромные проблемы, если в пути (execstart|workingdirectory) имеются русские символы (кириллица) или пробелы.
# И наконец скопировать его:
```bash
  sudo cp testmonitoring.service /etc/systemd/system
  sudo systemctl enable testmonitoring.service
  sudo systemctl start testmonitoring.service
```

# ответ:
Лог-файл создаётся с chmod 666 - это крайне небезопасно. Проверка на -f $logfile без кавычек может сломаться при пробелах/пустом значении. sudo вызывается внутри скрипта без проверки - если пользователь не root, это вызовет остановку.
# доп:
Проблема в том, что /var/log/ обычно принадлежит root:syslog (или root:adm), и обычные пользователи не могут создавать там файлы без sudo.
Что происходит в вашем скрипте:
Скрипт пытается создать файл /var/log/monitoring.log от имени пользователя user (как указано в systemd).
Но /var/log/ защищён, и user не имеет прав на запись.
Вы вызываете sudo touch, но:
Если user не входит в sudoers, команда упадёт с ошибкой.
Даже если sudo сработает, файл создастся с правами 644 (владелец — root).
Затем вы делаете chmod 666, чтобы user мог писать в файл.
Вариант решения:
Создать файл заранее с правильными правами
Если всё же нужен отдельный лог-файл:
Создайте файл вручную (разово) с правильными правами:
sudo touch /var/log/monitoring.log
sudo chown user:adm /var/log/monitoring.log  # или user:syslog
sudo chmod 644 /var/log/monitoring.log
Убрать sudo из скрипта (он больше не нужен).
Нет проверки состояния процесса test (основная задача)
Неправильная обработка SIGINT (должен быть SIGTERM для перезапуска)
