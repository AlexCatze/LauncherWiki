# Настройка рантайма

## Описание рантайма

Рантайм в лаунчере — это модуль для лаунчера и набор ресурсов (fxml/css/png), отвечающие за взаимодействие с пользователем. Рантайм основан на  [JavaFX](https://openjfx.io/).

::: tip Полезные ссылки:

-   [Scene Builder](https://gluonhq.com/products/scene-builder/)  — Редактор fxml файлов
-   [OpenJFX API Docs](https://openjfx.io/javadoc/17/)  — Документация JavaFX API
-   [JavaFX CSS Reference Guide](https://openjfx.io/javadoc/17/javafx.graphics/javafx/scene/doc-files/cssref.html)  — Документация JavaFX CSS

:::

## Настройка рантайма

В этом разделе вы найдете полезные функции, входящие в рантайм "из коробки", но не включенные по умолчанию

## Шифрование рантайма

-   Скачайте библиотеку  [EnFS](https://mirror.gravit.pro/5.2.x/compat/EnFS-1.0.0.jar)  и положите её в папку launcher-libraries
-   Включите опцию ```runtimeEncrypt``` в конфигурации лаунчсервера и сделайте ```build```
-   Проверьте правильность работы

## Скачивание своей Java

-   Скачайте  **архивы**  с JRE/JDK с [Adoptium](https://adoptium.net/) или другого поставщика сборок OpenJDK
-   Распакуйте архивы в папку updates и дайте им понятные названия: ```java17-windows-x86-64```, ```java17-windows-x86``` и т.д.
-   По желанию удалите из сборок JRE/JDK необязательные компоненты для уменьшения объема скачиваемых данных
-   Откройте файл ```config/JavaRuntime/Config.json``` в текстовом редакторе, найдите строку **"javaList": {}** и приведите её к такому виду:

```json
  "javaList": {
    "java17-windows-x86-64": "Java 17 b53 mustdie X86_64 javafx true",
    "java17-windows-x86": "Java 17 b53 mustdie X86 javafx true"
  },
```

::: warning Формат записи:
```
Java {номер версии} b{номер сборки} {mustdie (это windows)/linux/macosx} {архитектура} javafx {наличие javafx}
```
Архитектуры:
- X86_64 (Intel/AMD 64 бит)
- X86 (Intel/AMD 32 бит)
- ARM32 (32 разрядные ARM)
- ARM64 (64 разрядные ARM)
:::

-   Добавьте названия ваших папок с JRE/JDK в **"protectHandler": {}** конфигурации лаунчсервера LaunchServer.json
```json
    "allowUpdates": ["java17-windows-64", "java17-windows-32"],
```
-   Выполните ```syncup``` и ```build```
-   Проверьте правильность работы

## Структура рантайма

Файловая структура:

-   ```images``` — папка, которая содержит все картинки
-   ```overlay``` — папка, в которой находятся оверлеи. Каждому оверлею выдается отдельная папка, в которой должны лежать fxml и css файлы. Допускается рядом с fxml и css размещать картинки, специфичные для конкретного оверлея.
-   ```scenes``` — папка, аналогичная по своей структуре папке overlay, но предназначена для сцен.
-   ```components``` — папка, в которой находится дизайн отдельных частей (как serverButton и уведомления).
-   ```dialogs``` — папка, в которой находится дизайн диалогов.
-   ```styles``` — папка с css стилями.
-   ```runtime_*.properties``` — собранные файлы локализации.

Основные сущности:

-   ```Stage``` — окно. Эта сущность отвечает за иконку, параметры окна и его название
-   ```Scene``` — содержимое окна. Является корнем всех объектов и не может быть встроена куда либо
-   ```Overlay``` — кастомное содержимое, наложенное на сцену. При отображении оверлея сцена размывается и блокируется, пока открыт оверлей
-   ```Component``` — содержимое, не привязанное к чему—либо. Компонент может существовать в нескольких экземплярах одновременно, чем и отличается от сцен и оверлеев

Для редактирования файлов fxml вам понадобится программа JavaFX SceneBuilder, которую вы можете скачать с любого удобного для вас сайта  
После чего открываете интересующий вас fxml файл, заходите в Preview -> Internationalization -> Set Resourse и выбираете файл runtime_*.properties

Для редактирования текста вам понадобится папка compat, в которой находятся оригинальные .properties файлы.  **Изменять напрямую файлы runtime_*.properties из папки runtime нельзя (не будет работать русский язык)**  
После того, как вы изменили текст в compat файлах, воспользуйтесь утилитой native2ascii для приведения в понятный рантайму вид, которая идет в любом JDK.

## Объекты рантайма

```JavaFXApplication```  — основной класс рантайма, точка доступа к всем компонентам и управлению приложением.

```FXMLLoader```  — асинхронный многопоточный загрузчик fxml, обеспечивает ускорение запуска и поддержку мультиязычности

```StateService```  — точка обмена данными и состоянием лаунчера

```RuntimeSecurityService```  — выполняет обмен ключами с лаунчсервером, сбор HardwareInfo (HWID) и обновление лаунчера

```MessageManager```  — отображает уведомления и диалоги

## Работа с .properties и перевод на другие языки

Вы можете перевести лаунчер на любой другой язык, добавив его в список языков в файле RuntimeSettings.java. Для этого добавьте в enum LAUNCHER_LOCALE свой язык.

Первый параметр — 2–3 буквы страны, которые будут использоваться для поиска .properties файла.

Второй параметр — отображаемое имя в всплывающем окне выбора языков.

Помимо надписей .properties, файлы поддерживают изменение любых свойств JavaFX, в том числе размеры и расположение элементов, пути до картинок, параметры объектов и вообще любые свойства, которые вы можете указать в .fxml файле

## Кастомные serverButton
Вы можете использовать под каждый профиль, собственные ```serverButton```.

Для этого, в папке ```components``` создайте папку ```serverButton``` (регистр важен)

Cкопируйте туда файл ```serverButton.fxml```, переименовав его в ```PROFILE_UUID.fxml```,

где ```PROFILE_UUID``` - ```UUID``` вашего профиля, для которого вы делаете ```serverButton```

::: warning Обратите внимание:
Так как папка теперь не ```components```, а ```components/serverButton```, в вашем fxml, необходимо изменить пути
к некоторым файлам/папкам:
:::

```"@../../images/servers/example.png"``` вместо ```"@../images/servers/example.png"```

(где ```example.png``` - изображение на ```serverButton```)

```"@../components.css"``` вместо ```"@components.css"```

```"@../../styles/global.css"``` вместо ```"@../styles/global.css"```

```"@../../styles/variables.css"``` вместо ```"@../styles/variables.css"```

## ProfileWhitelist профиля клиента
Позволяет скрыть клиент от всех пользователей, кроме записаных в profileWhitelist

В профиле клиента установите `"limited": true,`

В конфиге `LaunchServer.json`:
- `"protectHandler": {`
  - `"profileWhitelist": {"TITLE ПРОФИЛЯ": ["Ник1", "Ник2"]},`

## Отладка рантайма

Для отладки рантайма из IDEA войдите в `Run -> Edit configurations` и создайте новый `Application` с такими параметрами:

-   Модуль: `JavaRuntime.main`
-   Аргументы jvm: `-Dlauncherdebug.modules=pro.gravit.launcher.client.JavaRuntimeModule` (если параметры не отображаются, нажмите `Modify Options —> Add VM options`)
-   Main Class: `pro.gravit.launcher.debug.DebugMain`

Ограничения отладочного режима:

-   Не будет работать опция "Сохранить пароль", так как ключ шифрования пароля внедряется лаунчсервером
-   Невозможно запустить клиент MineCraft (при этом все этапы скачивания, проверки и подготовки к старту будут работать)
-   Лаунчер не будет пытаться обновиться и получить список методов авторизации
-   Может не работать проверка HWID, так как лаунчсервер не будет доверять лаунчеру
-   Отладочный режим невозможно активировать на уже собранном лаунчере
-   Никакие настройки модулей для лаунчера из папки `config` не будут работать