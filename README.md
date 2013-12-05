Система учета курсов

Требования:
Версия Cache 2014.1 и выше

1. Настроить SSL-конфигурацию с сертификатом google.com
2. Создать календарь Google
3. Настроить приложение (в глобале:

        ^Settings("Google","SslConfig")=<имя конфигурации>
        ^Settings("Google","CalendarId")=<id календаря>

4. - Если есть доменное имя, то добавить его в API Console приложения "Система учета курсов"
 - Если доменного имени нет - добавить в hosts (необходимо только во время получения авторизации Google):

           <service ip>  somesite.com

5. Перейти на страницу Stc.Google.Authorize.cls, авторизоваться как пользователь, имеющий доступ на запись к календарю CalendarId
6. Настройки прокси для google.com (если без прокси нельзя):

        ^Settings("Google","ProxyPort")=<proxy port>
        ^Settings("Google","ProxyServer")=<proxy server>
        ^Settings("Google","ProxyTunnel")=1
