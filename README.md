# Скрипт создает /var/log/monitoring.log, отсылается на test.com каждые 60 секунд с помощью curl, ответ ждет 10.
# При неудаче работает функция badrequest, логирующая неудачу.
# Так же выставлен "trap" на сигнал перезагрузки (или CTRL + C), который логирует этот сигнал.
# Для работы сервиса необходимо настроить локальное окружение:
```bash
ExecStart= (ПУТЬ К СКРИПТУ - ПОЛНЫЙ. ПРИМЕР: home/user/script)
WorkingDirectory= (ПУТЬ К ДИРРЕКТОРИИ - ПОЛНЫЙ. ПРИМЕР: home/user/)
```
# Могут быть огромные проблемы, если в пути (execstart|workingdirectory) имеются русские символы (кириллица) или пробелы.
# И наконец скопировать его:
```bash
  sudo cp testmonitoring.service /etc/systemd/system
  sudo systemctl enable testmonitoring.service
  sudo systemctl start testmonitoring.service
```
