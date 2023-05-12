# Voice-Assistant
<br>
REST API голосового помощника. Для его работы нужно прописать переменные внутренней среды:<br/>
&nbsp;&nbsp;1. <b>API_KEY</b> - API ключ от YandexAPI <br>
&nbsp;&nbsp;2. <b>OPEN_AI_API_KEY</b> - API ключ от OpenAI API <br>

## Что было сделано в ходе этого проекта?
1. Распознование и синтез речи с помощью `Yandex API`
2. Получение ответа на вопрос с помощью модели `ChatGPT` через `OpenAI API`
3. Безопасность приложения: доступ к эндпоинту для получением ответа имеют только авторизированные пользователи
4. Авторизация и аутентификация с помощью `Spring Security` и JWT токена
5. Регистрация новых пользователей

## Какие технологии были задействованы в ходе разработки?
Прежде всего во главе всего стоят такие понятия, как `Depedency Ejection` и фреймфорк `Spring MVC`. С помощью двух этих вещей я построил фундамент для взаимодействия с моим API. Иначе говоря, сделал все те эндпоинты, которые есть в приложении. Затем надо мной стал вопрос безопасности приложения и доступ к некоторому функционалу по правам. Поэтому я выбрал `Spring Security` и аутентификацию по JWT токену. Я создал фильтр безопасности, который проверяет наличие JWT токена в входящем запросе от клиента в заколовке `Autherization`. Если токен не валидный или его нет, то пользователя не пропускают дальше.

## Какой функционал моего приложения?
В моем приложении есть 3 эндпоинта:
1. `/api/v1/answer` - этот эндпоинт используется для получения ответа на поставленный вопрос в голосовом сообщении.
Запрос должен быть `POST`. Тело запроса должно быть в JSON формате и иметь следующий вид:
```
{
    "audio": "Голосовое сообщение, в котором содержится вопрос, в формате Base64",
    "format": "Формат аудио. Доступны следующие: mp3, oggopus, lpcm",
    "profanityFilter": "Фильтр мата: true - если мат убирается, false - если не убирается",
    "encode": "Название кодировки аудио. Допускается только Base64"
}
```

Возвращается ответ в виде JSON и имеет следующий вид:
```
{
    "voice": "Ответ в виде аудио",
    "encode": "Кодировка, в которой закодировано аудио. Тут всегда только Base64"
    "format": "Формат аудио ответа",
    "error": "Ошибка. Если ошибки нет, то возвращает null"
}
```

**Имеют доступ только те пользователи, у которых в роли есть право `get:answer`**

2. `/api/v1/auth/login` - этот эндпоинт используется для авторизации в системе
Запрос должен быть `POST`. Тело запроса должно быть формате JSON и иметь следующий вид:
```
{
    "username": "Имя пользователя для входа в систему. Иначе говоря, его логин",
    "password": "Пароль пользователя для входа в систему"
}
```

Запрос должен быть `POST`. Возвращается в ответ в видео JSON и имеет следующий вид:
```
{
    "username": "Имя пользователя (логин), для которого создался JWT токен",
    "token": "JWT токен, по которому можно получить доступ к ряду функционала",
    "error": "Сообщение об ошибке. Если её не возникло, то тут стоит null"
}
```

**К этому эндпоинту имеет доступ абсолютно любые пользователи с разными правами (даже без прав)**

3. `/api/v1/auth/logout` - этот эндпоинт используется для выхода из системы. К нему имеют доступ абсолютно все пользователи с различными правами (даже без прав)
4. `/api/v1/auth/register` - этот эндпоинт используется для создания аккаунта в приложении.
Запрос должен быть `POST`. Тело запроса должно быть в формате JSON и иметь следующий вид:
```
{
    "username": "Имя пользователя (логин), которое хотят взять для своего аккаунта",
    "name": "Имя человека",
    "surname": "Фамилия человека",
    "password": "Пароль пользователя, по которому будет выполняться вход в аккаунт"
}
```

Ответ будет в формате JSON и иметь следующий вид:
```
{
    "code": "Код результата. Например, если результат успешен, то возвращается 200",
    "message": "Сообщение, которое характеризует результат"
}
```

**Доступ к этому эндпоинту имеют все пользователи с любыми правами доступа**
