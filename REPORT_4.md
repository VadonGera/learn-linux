## Удаленное подключение к серверу с использованием SSH

1. Установка и настройка SSH-сервера

    На сервере с Arch Linux нужно убедиться, что установлен и настроен SSH-сервер.

    + **Установка SSH-сервера**: Для Arch Linux:

        ```bash
        sudo pacman -S openssh
        ```

    + **Запуск SSH-сервера**: Чтобы запустить SSH-сервер и настроить его на автозапуск при старте системы:

        ```bash
        sudo systemctl start sshd  # Запуск SSH-сервера
        sudo systemctl enable sshd  # Автозапуск SSH-сервера
        ```

    + **Проверка работы сервера**: Убедимся, что служба SSH работает:

        ```bash
        sudo systemctl status sshd
        ```

2. Генерация SSH-ключей на клиенте

    Клиенту нужно сгенерировать SSH-ключи для безопасного подключения к серверу.

    + **Генерация ключей** (на клиенте):

        ```bash
        ssh-keygen -t rsa -b 4096 -C "мой_email@example.com"
        ```
      Опции:
      + `-t` rsa — тип ключа (RSA).
      + `-b 4096` — длина ключа (рекомендуется 4096 бит для большей безопасности).
      + `-C` — комментарий (например, email для идентификации ключа).
    
      По умолчанию ключ будет сохранён в файле `~/.ssh/id_rsa `
    (приватный ключ) и `~/.ssh/id_rsa.pub` (публичный ключ).

    + **Копирование публичного ключа на сервер:** Чтобы настроить подключение по SSH с помощью ключей, нужно скопировать публичный ключ на сервер. 
      + **Копирование через команду** `ssh-copy-id`: На клиенте выполняем команду для копирования ключа на сервер:
        ```bash
        ssh-copy-id -p 2003 username@server_ip
        ```
        Эта команда автоматически добавит публичный ключ в файл `~/.ssh/authorized_keys` на сервере. Флаг `-p`, если порт отличный от 22 
        
      + **Вручную**:
      Если команда **ssh-copy-id** недоступна, можно вручную скопировать публичный ключ. Команда для отображения публичного ключа:
          ```bash
          cat ~/.ssh/id_rsa.pub
          ```
        Затем копируем содержимое файла и добавляем его на сервере в файл `~/.ssh/authorized_keys` для нашего пользователя.
        На сервере:
        ```bash
        mkdir -p ~/.ssh
        echo "содержимое_публичного_ключа" >> ~/.ssh/authorized_keys
        chmod 600 ~/.ssh/authorized_keys  # Установка прав доступа
        chmod 700 ~/.ssh  # Установка прав доступа на каталог
        ```
      + **Копирование с помощью программы** `scp`
        ```shell
        scp /путь/к/локальному/файлу username@server_address:/путь/на/сервере
        ```
        Где:
        + /путь/к/локальному/файлу — путь к файлу отчета на вашем устройстве.
        + username — ваше имя пользователя на сервере.
        + server_address — IP-адрес или доменное имя сервера.
        + /путь/на/сервере — путь, куда файл будет скопирован на сервер.
          
   
3. Подключение к серверу через SSH

    Когда публичный ключ настроен на сервере, можем подключаться без пароля.
   + **Подключение по SSH с клиентской машины:** На клиенте команда для подключения к серверу:
     ```bash
     ssh username@server_ip
     ```
     + username — имя пользователя на сервере.
     + server_ip — IP-адрес сервера.
     
     Если всё настроено правильно, подключаемся к серверу без необходимости вводить пароль, используя только SSH-ключ.

4. Дополнительные настройки
   + **Отключение аутентификации по паролю**: Чтобы усилить безопасность, можно запретить аутентификацию по паролю и оставить только вход по SSH-ключам. Для этого редактируем файл конфигурации SSH-сервера (`/etc/ssh/sshd_config`):
     ```bash
     sudo nano /etc/ssh/sshd_config
     ```
     Изменяем параметры:
     ```bash
     PasswordAuthentication no
     PubkeyAuthentication yes
     ```
     Перезапуск SSH-сервера:
     ```bash
     sudo systemctl restart sshd
     ```
     
Теперь только клиенты с правильными SSH-ключами смогут подключиться к серверу.
```bash
ssh -p 2003 'username@server_ip' # Если порт не 22
```

