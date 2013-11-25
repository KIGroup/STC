1. Импортировать проект и выполнить для инициализации системы: 
do ##class(Stc.Utils.Init.BaseInit).Init()

2. Создать два веб-приложения для (указать Dispatch Class):
  1) Stc.Web.BrokerAdmin - закрытые REST запросы для администратора

  2) Stc.Web.BrokerUser - открытые REST запросы для пользователей, нет авторизации

3. У приложения области указать таймаут = 0

4. Записать в глобал следующие данные (через студию или портал), заменить /name на нужное значение:
  1) Имя веб-приложения области: ^Settings("Stc", "WebApp") = "/name"
  2) Имя веб-приложения для закрытых запросов: ^Settings("Stc", "WebAppAdmin") = "/name/admin"
  3) Имя веб-приложения для открытых запросов: ^Settings("Stc", "WebAppUser") = "/name/user"
  4) Сообщение заказчику на почту, изменить если необходимо - указать язык. Вот что сейчас:
    set ^CacheMsg("Stc", "ru", "msgToCompanyContact") = ""_
	"Здравствуйте, вы оставили заявку на обучение по курсу - '%1', в городе - '%2', дата обучения - '%3'. "_
	"Вам необходимо перейти по <a href='http://%4/stc/index.csp#/updateorder?code=%5'>ссылке</a> для дальнейшего заполнения вашей заявки, проверить и заполнить реквизиты компании, добавить сотрудников для обучения.</br>"_
	"Прямая ссылка на вашу заявку: http://%4/stc/index.csp#/updateorder?code=%5</br>"_
	"Нажмите 'Завершить редактирование', чтобы отправить ее администратору системы для рассмотрения. Доступ к заявке будет закрыт после завершения редактирования.</br>"_
	"------------</br>"_
	"С уважением, Администратор 'Системы учета курсов InterSystems'"
	
	где =  1 - Курс, 2 - Город, 3 - дата, 4 - SERVERNAME_SERVERPORT_^Settings("Stc", "WebApp") - вычисляемое, 5 - код доступа к заявке
  
  5) Для работы с почтой - можно в глобал занести свои данные или зайти в класс Stc.Utils.Email и там их жестко прописать
     Сейчас выглядит все так
	 ^Settings("Stc","EmailFrom") = "stc_testmail@mail.ru"
	 ^Settings("Stc","EmailSMTPUSER") = "stc_testmail@mail.ru"
	 ^Settings("Stc","EmailSMTPPASSWORD") = "password"
	 ^Settings("Stc","EmailSMTPSERVER") = "smtp.mail.ru"
	 ^Settings("Stc","EmailSMTPPORT") = 25

	 проверить 
	 w ##class(Stc.Utils.Email).Send("email", "Subject", "Message")
5. Безопасность
Создать две роли - первую для админа, вторую - для всех пользователей.
Для	второй сделать доступ к таблицам из области. Назначить UnknownUser
										                    SELECT  INSERT  UPDATE  DELETE
Stc_Data.Company 						               1       -       -      -
Stc_Data.Course                            1       -       -      -
Stc_Data.Order                             1       1       1      1
Stc_Data.OrderApproved_Students            1       1       1      1
Stc_Data.Person                            1       1       1      1
Stc_Data.Region                            1       -       -      1
Stc_Data_Localization.LText                1       1       1      1
Stc_Data_Localization.LTextLanguage        1       -       -      -
Stc_Data_Localization.LTextValue           1       1       1      1

Затем создать пользователя - администратора системы - дать все привилегии в этой области.
Назначить соответствующие роли - веб приложениям.
