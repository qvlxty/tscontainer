# Экземпляр приложения

Есть возможность создать приложение с использованием express. Если необходимо дописать функционал, то есть абстрактный класс **App** в app.facade.ts. От него можно наследоваться и реализовать функционал другой библиотеки, например, koa. 

**upd v 0.1.0**

Теперь фреймворк использует только express и шаблонизатор ejs.
Но если очень хочется - можете написать свои реализации, наследуая App класс из ``frameworkCore/app.facade.ts``

Приложение создается с помощью класса-фасада **App**.
Затем необходимо произвести загрузку всех доступных контроллеров в DI контейнер.
После чего, необходимо передать сервер контейнеру, чтобы он мог настроить все доступные роуты.


Пример создания приложения описан ниже

```ts
const bootApp = function() {
  const expressApp = new AppExpress();
  const AppContainer = new Container();
  // Функция инициализации базовых сервисов и контроллеров
  await AppContainer.init({
    controllers:[UserController],
    app: expressApp
  });
  const port = AppContainer.getService(ConfigService).config.get("port");
  expressApp.server.listen(port || 3000, () => {
    console.info("[SERVER] started!");
  });
};
bootApp();
```
Чтобы корректно и правильно инициализировать контейнер, не забывайте функцию init. Она создает БД подключения, 
регистрирует начальные сервисы и контроллеры.

Если потребуется отдельно зарегистрировать независимый контроллер, то можно пользоваться методом ``loadControllers(class[])``.

## Настройка приложения

С помощью метода **setupApp()**, вы сможете настроить приложения и установить интересующие модули.

- view engine - настройка шаблонизатора (по-умолчанию **ejs**, требуется для генератора API документации, если вы ее используете)
- modules[] - массив модулей для express 
- staticFolders[] - массив каталогов со статических файлов. Задавайте, если не используете рендеринг страниц,требующих статики
- viewCatalog - каталог, в котором должны будут находиться 

```ts
const bootApp = function() {
  const expressApp = new AppExpress();
  expressApp.setupApp({
    viewEngine: "ejs",
    modules: [],
    staticFolders: [{ path: "views/css", folder: "views/css" }],
    viewCatalog: "src/application/views"
  });
}
```
