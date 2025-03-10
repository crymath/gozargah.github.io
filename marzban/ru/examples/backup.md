---
title: Автоматическое резервное копирование
---

# Резервное копирование

С помощью этого руководства вы можете настроить автоматическое резервное копирование необходимых файлов Marzban, которые находятся в каталогах `/opt/marzban/` и `/var/lib/marzban/`. Это поможет избежать проблем при сбоях или переносе панели.

## Настройка автоматического резервного копирования

### Шаг 1

Выполните следующий скрипт, чтобы установить все команды Marzban:
```bash
sudo bash -c "$(curl -sL https://github.com/Gozargah/Marzban-scripts/raw/master/marzban.sh)" @ install-script
```

### Шаг 2

Запустите сервис резервного копирования Marzban с помощью следующей команды:
```bash
marzban backup-service
```

::: tip Совет
Обязательно выполняйте эту команду от имени `root`.
:::

### Шаг 3

Скрипт запросит токен бота. Создайте нового бота через [BotFather](https://t.me/BotFather) и введите его токен.

### Шаг 4

Скрипт запросит ID чата. Для его получения пересылайте любое сообщение от себя или из канала, куда будут отправляться резервные копии, боту [UserInfoBot](https://t.me/userinfobot). Он предоставит вам ID чата.

### Шаг 5

На этом этапе установите интервал времени для отправки резервных копий, указав число от `1` до `24`, которое соответствует количеству часов между отправками. Например, введя `2`, вы будете получать резервную копию каждые 2 часа.

::: tip Совет
Время отправки резервных копий соответствует времени сервера.
:::

::: tip Совет
Чтобы изменить время отправки, ID чата или удалить сервис резервного копирования, повторно выполните следующую команду:

```bash
marzban backup-service
```

Первый вариант позволяет внести изменения, второй — удалить сервис резервного копирования.
:::

## Мгновенное резервное копирование

Если вы уже настроили сервис резервного копирования Marzban, выполните следующую команду, чтобы получить резервную копию прямо сейчас. Она будет отправлена в Telegram-бот, а также сохранена в каталоге `/opt/marzban/backup`. Если сервис не настроен, копия будет сохранена только локально.

```bash
marzban backup
```

::: tip Совет
Если вы уже создавали мгновенную резервную копию, новая копия заменит предыдущую.
:::

## Восстановление резервной копии

Если вам необходимо перенести панель Marzban или устранить сбой, вы можете восстановить резервную копию. Метод восстановления зависит от типа базы данных. По умолчанию используется SQLite, но если вы следовали [инструкции по настройке MySQL](https://gozargah.github.io/marzban/examples/mysql), процесс восстановления будет отличаться.

::: tip Совет
Процесс восстановления MariaDB идентичен MySQL.
:::

## Восстановление резервной копии базы данных SQLite

Файлы Marzban находятся в каталогах `/opt/marzban/` и `/var/lib/marzban/`. Для восстановления:
1. Установите Marzban на новый сервер.
2. Замените файлы в указанных каталогах.
3. Перезапустите Marzban командой:
```bash
marzban restart
```

## Восстановление базы данных MySQL через PhpMyAdmin (Метод 1)

Следуйте инструкции для SQLite, но восстанавливайте файл SQL через PhpMyAdmin. Удалите папку `mysql` в `/var/lib/marzban/mysql`, чтобы она была пересоздана после перезапуска Marzban. Панель PhpMyAdmin доступна по адресу `IP:8010`.

::: tip Совет
Для доступа к PhpMyAdmin активируйте MySQL, следуя инструкции.
:::

1. Выберите базу `marzban` в меню слева.
2. Нажмите `Check all`, выберите `drop` и подтвердите.
3. Перейдите в раздел `Import`, выберите файл SQL, нажмите `Import`.
4. После успешного импорта перезапустите Marzban:
```bash
marzban restart
```

## Восстановление базы данных MySQL через терминал (Метод 2)

1. Загрузите SQL-файл на сервер.
2. Выполните команду, заменив переменные `DB_PASSWORD` и `SQL_BACKUP_ADDRESS`:
```bash
docker exec -i marzban-mysql-1 mysql -u root -p"DB_PASSWORD" marzban < "SQL_BACKUP_ADDRESS"
```

3. Перезапустите Marzban:
```bash
marzban restart
```

Теперь резервная копия успешно восстановлена.

::: warning Внимание
Если вы использовали автоматический скрипт для SQLite, после перехода на MySQL необходимо выполнить его заново, так как процессы резервного копирования различаются.
:::
