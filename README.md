# bind9
Ручная настройка DNS сервера на bind9

отличная ссылка **https://timeweb.cloud/tutorials/ubuntu/nastrojka-dns-servera-bind**

ДОБАВИТЬ ФАЙЛ С ТЕОРИЕЙ ПО DNS 

УСТАНОВКА

Команды ниже, необходимо выполнять от имени пользователя root или пользователя с правами sudo.
sudo apt update && sudo apt upgrade -y  - обновление пакетов системы
sudo apt install bind9 bind9utils bind9-doc dnsutils -y  - установка необходимых файлов bind9 

проверим статус демона 
systemctl status bind9

НАСТРОЙКА BIND
BIND хранит свои конфигурационные файлы в директории /etc/bind/.

Основной файл называется /etc/bind/named.conf  - Он не содержит детальных настроек, а выполняет роль «сборщика»: включает другие конфигурационные файлы через директиву include.

ПРИМЕР содержимого 
// This is the primary configuration file for the BIND DNS server named.
//
// Please read /usr/share/doc/bind9/README.Debian for information on the
// structure of BIND configuration files in Debian, *BEFORE* you customize
// this configuration file.
//
// If you are just adding zones, please do that in /etc/bind/named.conf.local

include "/etc/bind/named.conf.options";
include "/etc/bind/named.conf.local";
include "/etc/bind/named.conf.default-zones";

ОПИСАТЬ Каждый файл
/etc/bind/named.conf.options - Он указывает, где искать зоны (области DNS, например, ваш домен), указывает путь до логов и т.д. 

1) Прежде чем приступать к редактированию файла, сделаем его резервную копию:
cp /etc/bind/named.conf.options /etc/bind/named.conf.options.backup

ВТАВИТЬ ЕГО СОДЕРЖИМОЕ И ОПИСАНИЕ или ссылку на файл в котором есть его описание 


Настройка локальной DNS зоны
Теперь настроим зоны DNS, которые наш сервер будет обслуживать.

Зоны DNS настраиваются в файле /etc/bind/named.conf.local. - тут мы прописываем названия зон и ссылки на файлы с их описанием 

ВСТАВИТЬ пример файлва и описание 

Настройка прямой DNS зоны
На следующем этапе нам необходимо создать и настроить прямую DNS-зону, которая содержит все записи о соответствии доменных имен IP-адресам в нашей локальной сети. Настройка прямой зоны осуществляется в файле /etc/bind/db.local.
Настройка обратной DNS-зоны
На финальном этапе настроим файл обратной DNS-зоны, которая отвечает за преобразование IP-адресов в доменные имена. В файле /etc/bind/db.127
ОПИСАТЬ что такое прямая и обратная зона и что содержится в этих файлах 


Этап проверки работы Валидация конфигурации и запуск службы
Перед применением изменений необходимо удостовериться в синтаксической корректности конфигурационных файлов.

Проверка общего синтаксиса главного файла конфигурации: 
named-checkconf /etc/bind/named.conf
Валидация файла прямой зоны local:
named-checkzone local /etc/bind/db.local
Валидация файла обратной зоны для сети 192.168.0.0/24:
named-checkzone 1.168.192.in-addr.arpa /etc/bind/db.192
Успешное выполнение этих команд должно завершиться без выводов ошибок (или с сообщением "OK" для named-checkzone).
После успешной проверки необходимо применить конфигурацию: 
systemctl restart bind9
Убедиться в корректности запуска службы можно командой:
systemctl status bind9

Проверка работоспособности BIND
После успешного перезапуска BIND рекомендуется провести серию диагностических запросов.
1) Выполним запрос к локальному серверу для разрешения имени ns.local: 
dig @localhost ns.local

добавить порядок настройки на других машинах для корректной работы 
Файл resolv.conf для клиента и сервера
Для DNS-сервера (/etc/resolv.conf):
bash
nameserver 127.0.0.1
options edns0 trust-ad
search msio.lan
Для клиентских машин (например, компьютер с IP 192.168.93.100):
bash
nameserver 192.168.93.143
options edns0 trust-ad
search msio.lan


# 1. Установить имя хоста (без домена)
sudo hostnamectl set-hostname ca

# 2. Добавить в /etc/hosts (важно!)
echo "192.168.93.142    ca.msio.lan ca" | sudo tee -a /etc/hosts
echo "192.168.93.143    dns1.msio.lan dns1" | sudo tee -a /etc/hosts

# 3. Настроить /etc/resolv.conf (указать DNS-сервер и домен поиска)
echo "nameserver 192.168.93.143" | sudo tee /etc/resolv.conf
echo "search msio.lan" | sudo tee -a /etc/resolv.conf****
