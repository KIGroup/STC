##Система учёта курсов
Требования: Версия Caché 2014.1 и выше

1. Импортировать проект и выполнить для инициализации системы: 
        

        do ##class(Stc.Utils.Init.BaseInit).Init()

    Начальные данные: "Главная компания" и ее контакт - изменить на настоящие.

2. Создать два веб-приложения для (указать Dispatch Class):<br>
    1) Stc.Web.BrokerAdmin - закрытые REST запросы для администратора<br>
    2) Stc.Web.BrokerUser - открытые REST запросы для пользователей, нет авторизации

3. У приложения области указать таймаут = 0

4. Записать в глобал следующие данные (через студию или портал), заменить /name на нужное значение:<br>
  1) Имя веб-приложения области: ^Settings("Stc", "WebApp") = "/name"<br>
  2) Имя веб-приложения для закрытых запросов: ^Settings("Stc", "WebAppAdmin") = "/name/admin"<br>
  3) Имя веб-приложения для открытых запросов: ^Settings("Stc", "WebAppUser") = "/name/user"<br>
  4) Сообщение заказчику на почту, изменить если необходимо - указать язык.<br> Вот что сейчас:<br>
    set ^CacheMsg("Stc", "ru", "msgToCompanyContact") = ""_<br>
    "Здравствуйте, вы оставили заявку на обучение по курсу - '%1', в городе - '%2', дата обучения - '%3'. "_<br>
	"Вам необходимо перейти по <a href='http://%4/stc/index.csp#/updateorder?code=%5'>ссылке</a> для дальнейшего заполнения вашей заявки, проверить и заполнить реквизиты компании, добавить сотрудников для обучения."_<br>
	"Прямая ссылка на вашу заявку: http://%4/stc/index.csp#/updateorder?code=%5"_<br>
	"Нажмите 'Завершить редактирование', чтобы отправить ее администратору системы для рассмотрения. Доступ к заявке будет закрыт после завершения редактирования."_<br>
	"------------"_<br>
	"С уважением, Администратор 'Системы учета курсов InterSystems'"<br>
	
	где =  1 - Курс, 2 - Город, 3 - дата, 4 - SERVERNAME_SERVERPORT_^Settings("Stc", "WebApp") - вычисляемое, 5 - код доступа к заявке<br>
  
  5) Для работы с почтой - можно в глобал занести свои данные или зайти в класс Stc.Utils.Email и там их жестко прописать<br>
     

        Сейчас выглядит все так:
        ^Settings("Stc","EmailFrom") = "***"
        ^Settings("Stc","EmailSMTPUSER") = "***"
        ^Settings("Stc","EmailSMTPPASSWORD") = "**"
        ^Settings("Stc","EmailSMTPSERVER") = "smtp.mail.ru"
        ^Settings("Stc","EmailSMTPPORT") = 25

        Проверить 
        w ##class(Stc.Utils.Email).Send("email@email.com", "Subject", "Message")
     
5. Безопасность
Создать две роли - первую для админа, вторую - для всех пользователей.
Для второй сделать доступ к таблицам из области. Назначить UnknownUser

        SELECT  INSERT  UPDATE  DELETE
        Stc_Data.Company 						   1       -       -      -
        Stc_Data.Course                            1       -       -      -
        Stc_Data.Order                             1       1       1      1
        Stc_Data.OrderApproved_Students            1       1       1      1
        Stc_Data.Person                            1       1       1      1
        Stc_Data.Region                            1       -       -      1
        Stc_Data_Localization.LText                1       1       1      1
        Stc_Data_Localization.LTextLanguage        1       -       -      -
        Stc_Data_Localization.LTextValue           1       1       1      1

Затем создать пользователя - администратора системы - дать все привилегии для всех таблиц.
Назначить соответствующие роли - веб приложениям.


###Календарь Google
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
