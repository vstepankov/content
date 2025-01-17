---
title: 'Drupal 9.2.0'
slug: 9/releases/9.2.0
core: 9
metatags:
  title: 'Drupal 9.2.0: Список изменений'
  description: 'Список изменений Drupal 9.2.0.'
---

**Дата релиза**: 2 июня 2021\
**Активная поддержка до**: 01 декабря 2021\
**Поддержка безопасности до**: 2022

> [!WARNING]
> Drupal 9.2.0 находится в разработке.

## Объект исключения теперь передаётся в контексте в логер

- [#2949419](https://www.drupal.org/project/drupal/issues/2949419)

При логировании исключения теперь можно получить объект самого исключения из контекста `$context['exception']`.

Данное изменение соответствует [PSR-3](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-3-logger-interface.md#13-context) а также позволит проще обрабатывать ошибки сторонними решениями, например [Raven](https://www.drupal.org/project/raven).

## Передача StatementInterface объекта в Connection::query() помечена устаревшим

- [#3154439](https://www.drupal.org/node/3154439)

Передача объекта реализующего `Drupal\Core\Database\StatementInterface` в `Drupal\Core\Database\Connection::query()` помечена устаревшей и будет удалена в [Drupal 10](../../../../10/index.md). Сейчас метод позволяет передавать только строковые значения для параметра `$query`.

Было:

```php
  $db = \Drupal\Core\Database\Database::getConnection();
  $stmt = $db->prepareStatement('SELECT * FROM {test}', []);
  $result = $db->query($stmt);
```

Стало:

```php
  $db = \Drupal\Core\Database\Database::getConnection();
  $result = $db->query('SELECT * FROM {test}');
```

## Библиотеки jQuery UI помечены устаревшими

- [#3113400](https://www.drupal.org/project/drupal/issues/3113400)

Следующие библиотеки ядра помечены устаревшими:

- `jquery.ui`
- `jquery.ui.autocomplete`
- `jquery.ui.dialog`
- `jquery.ui.draggable`
- `jquery.ui.menu`
- `jquery.ui.mouse`
- `jquery.ui.position`
- `jquery.ui.resizable`
- `jquery.ui.widget`

Если ваши модули используют их, вам необходимо обновить зависимости и использовать для этого контрибные модули. Полный и актуальный список контрибных модулей для конкретных библиотек вы найдёте на странице проекта [jQuery UI](https://www.drupal.org/project/jquery_ui).

Библиотеки `drupal.autocomplete`, `drupal.dialog` и `drupal.tabbingmanager` продолжать иметь зависимости на jQuery UI JS и CSS файлы, но они явно прописаны в данных библиотеках, без использования устаревших.

Модули и темы переопределяющие или заменяющие данные библиотеки теперь должны производить нужные изменения непосредственно в библиотеках `drupal.autocomplete`, `drupal.dialog` и `drupal.tabbingmanager`.

## Добавлено новое свойство 'bundle' для маршрутов entity:*

- [#3155568](https://www.drupal.org/project/drupal/issues/3155568)

Настройки для маршрутов сущностей теперь принимают новое свойство `bundle` в качестве массива. При указании данного свойства, конвертация параметра для сущности будет ограничена указанными бандлами.

```yaml
example.route:
  path: foo/{example}
  options:
    parameters:
      example:
        type: entity:node
        bundle:
          - article
          - news
```

В примере выше, параметр `{example}` будет конвертирован в ноду только если это нода типа «article» или «news».

В связи с этим, свойство маршрута `_entity_bundles` помечено устаревшим.

Благодаря данному изменения, при несоответствии типов, маршрут теперь будет отвечать HTTP 404 вместо HTTP 403.

## Расширения больше не могут объявлять мажорную версию

- [#3105353](https://www.drupal.org/project/drupal/issues/3105353)

Расширения ([модули](../../../modules/index.md), [темы оформления](../../../themes/index.md)) больше не могут указывать свойство `major` в ***.info.yml** файлах.

## Изменены сигнатуры конструкторов EntityFieldManager и EntityLastInstalledSchemaRepository

- [#3131585](https://www.drupal.org/project/drupal/issues/3131585)

Конструктор класса `Drupal\Core\Entity\EntityFieldManager` теперь принимает новый параметр типа `\Drupal\Core\Entity\EntityLastInstalledSchemaRepositoryInterface`. Для сохранения [обратной совместимости](../../../../../backward-compatibility/index.md) параметр является опциональным, тем не менее, он станет обязательным в [Drupal 10](../../../../10/index.md).

Конструктор класса `Drupal\Core\Entity\EntityLastInstalledSchemaRepository` теперь принимает новый параметр типа `\Drupal\Core\Cache\CacheBackendInterface`. Для сохранения [обратной совместимости](../../../../../backward-compatibility/index.md) параметр является опциональным, тем не менее, он станет обязательным в [Drupal 10](../../../../10/index.md).

Данное изменение также немного улучшает производительность сущностей, снижая количество запросов к БД.

## Drupal теперь использует возможности PHP для генерации ID сессии

- [#2238561](https://www.drupal.org/project/drupal/issues/2238561)

Drupal теперь использует встроенный в PHP механизм генерации ID сессии. Это означает что `session_id()` и `\Drupal::service('session')->getId()` не гарантируют что вернут одни и те же результаты, даже если сессия в Drupal инициализирована. Это вызвано тем что Drupal использует "ленивые сессии" - сессия будет инициализирована только в случае попытки записать туда значение.

Это также означает что Drupal теперь поддерживает следующие настройки PHP:

- [session.sid_length](http://php.net/manual/en/session.configuration.php#ini.session.sid-length)
- [session.sid_bits_per_character](http://php.net/manual/en/session.configuration.php#ini.session.sid-bits-per-character)

### Идентификаторы сессии для анонимных пользователей

Drupal старается избегать создания сессии для анонимных пользователей на столько, на сколько это возможно. Некоторые модули, например Flag, для своей работы требуют открытия сессии для всех пользователей.

Подобные модули использовали ID сессии как идентификатор анонимного пользователя, что сейчас является устаревшим способом. Вместо этого, модули должны хранить уникальные идентификаторы в самой сессии, например:

```php
 if (!$session->has('core.tempstore.private.owner')) {
    // This generates a unique identifier for the user
    $session->set('core.tempstore.private.owner', Crypt::randomBytesBase64());
 }
```

### SharedTempStore и SharedTempStoreFactory получили новые зависимости

В `SharedTempStore` теперь [инжектится](../../../services/dependency-injection/index.md) [сервис](../../../services/index.md) `current_user`. 

В `SharedTempStoreFactory` теперь инжектятся сервисы `current_user` и `session`.

`ShareTempStoreFactory` больше не использует ID сессии в качестве идентификатора анонимного пользователя. Он генерирует свой уникальный ID когда это требуется. Данное значение хранится в сессии под ключом `core.tempstore.shared.owner`.

### Drupal\Core\Session\SessionManager::migrateStoredSession() удалён

Для того чтобы использовать встроенный в PHP генератор ID сессии был удалён метод `Drupal\Core\Session\SessionManager::migrateStoredSession()`. Замена не предоставляется, так как данный метод не был частью публичного API.

Собственные реализации `SessionManagerInterface` которые расширяют `SessionManager` должны быть обновлены чтобы не вызывать данный метод. Генерация сессии теперь производится при помощи вызова `session_regenerate_id()` который производится в `\Symfony\Component\HttpFoundation\Session\Storage\NativeSessionStorage::regenerate()`.

Если имеются собственные реализации которые переопределяют метод `::migrateStoredSession()`, то данный код может быть удалён после того, как минимальное требование модуля будет Drupal 9.2.0.

Код, использующий данный метод будет удалён, поэтому это не вызовет проблем. Если вам требуется мигрировать старую сессию в новую, вы по прежнему можете использовать `\Drupal::service('session')->migrate();`.

## Все идентификаторы в запросах теперь должны быть обёрнуты в кавычки

- [#3189880](https://www.drupal.org/project/drupal/issues/3189880)

Имена столбцов и синонимы таблиц должны быть обёрнуты квадратными кавычками (`[]`) при использовании `Connection::query()`, `db_query()`, `ConditionInterface::where()` или `SelectInterface::addExpression()`. Это позволит слою баз данных корректно оборачивать данные идентификаторы.

Например:

```php
$connection->query('SELECT [nid] FROM {node} WHERE [nid] = :id', [':id' => '1']);
```

Данное изменение позволит нам использовать различные типы баз данных без необходимости заводить список слов, которые не могут быть использованы в качестве названия таблиц или полей.

Запросы построенные при помощи `Connection::select()`, `Connection::insert()`, `Connection::delete()`, `Connection::update()`, `Connection::upsert()`, `Connection::merge()` не требуют добавлять квадратные скобки при вызовах `::fields()` или `::condition()`. Новый способ записи должен быть использовать при добавлении "сырого" SQL значения в объект при помощи `::addExpression()`, `::where()` или `::having()`.

Например:

```php
// Обратите внимание, что это плохой пример, так как использование ::condition() было бы лучше.
$connection->select('node')->where('[nid] = :id', [':id' => '1']);
```

В крайне редких случаях запросам требуются квадратные скобки, например, при создании SQL функции. В таких ситуациях задайте параметр `$option['allow_square_brackets']` равным `TRUE`.

Например:

```php
$connection->query('CREATE OR REPLACE FUNCTION "substring_index"(text, text, integer) RETURNS text AS
  \'SELECT array_to_string((string_to_array($1, $2)) [1:$3], $2);\'
  LANGUAGE \'sql\'',
  [],
  ['allow_delimiter_in_query' => TRUE, 'allow_square_brackets' => TRUE]
);
```

### Изменения в драйверах БД

Если вы являетесь автором или имеет свой собственный драйвер БД, вам необходимо задать значение свойству `\Drupal\Core\Database\Connection::$identifierQuotes`. Значение должно быть массивом из двух строк. Первое значение массива отвечает за символ начала цитаты, второе, его закрытие.

В [Drupal 10](../../../../10/index.md) данное свойство станет обязательным. В [Drupal 9](../../../index.md), когда значение не задано, будут использованы пустые строки.

Проверьте все переопределения `\Drupal\Core\Database\Connection::escapeDatabase()`, `\Drupal\Core\Database\Connection::escapeTable()`, `\Drupal\Core\Database\Connection::escapeField()` и `\Drupal\Core\Database\Connection::escapeAlias()`. Скорее всего, они больше не потребуются.

Если драйвер использует `\Drupal\Core\Database\Connection::$escapedNames`, код должен быть обновлён с использованием либо `\Drupal\Core\Database\Connection::$escapedTables`, либо `\Drupal\Core\Database\Connection::$escapedFields`.

## Изменения связанные с восстановлением пароля

- [#1521996](https://www.drupal.org/project/drupal/issues/1521996)

В процесс восстановления пароля внесены некоторые изменения.

### Восстановление пароля больше не раскрывает имя пользователя или что указанный email используется на сайте

Форма восстановления пароля больше не отображения сообщение, указывающее, что введенное имя пользователя или email не существуют.

#### Ранее

Если указанное имя пользователя или email не использовалось на сайте, выводилось следующее сообщение:

> unknown@example.com is not recognized as a username or an email address.

Данное сообщение раскрывает информацию о том, что пользователь с указанными данными не зарегистрирован.

Когда пользователь зарегистрирован и предоставлены корректные данные, выводилось следующее сообщение:

> Further instructions have been sent to your email address.

Данное сообщение можно использовать как подтверждение, что указанные данные используются на сайте.

#### После изменений

Сообщения были изменены таким образом, чтобы из них было невозможно получить не подтверждающую, не опровергающую информацию.

Если предоставлены некорректные имя пользователя (валидация на длину, допустимые символы и т.д.) или email, будет показано следующее сообщение:

> The username or email address is invalid.

Если валидация на имя пользователя или email прошла успешно, будет показано следующее сообщение:

> If [username or email] is a valid account, an email will be sent with instructions to reset your password.

### Изменена сигнатура UserPasswordForm

Класс `\Drupal\user\Form\UserPasswordForm` теперь принимает два новых аргумента в конструкторе:

```php
   * @param \Drupal\Core\TypedData\TypedDataManagerInterface $typed_data_manager
   *   The typed data manager.
   * @param \Drupal\Component\Utility\EmailValidatorInterface $email_validator
   *   The email validator service.
```

Если ваш код инициализирует данный класс самостоятельно без использования метода `::create()`, обновите свой код.

Данные параметры опциональны в [Drupal 9](../../../index.md) но станут обязательными в [Drupal 10](../../../../10/index.md).

## Добавлены хуки для изменения форм виджетов полей

- [#2872162](https://www.drupal.org/project/drupal/issues/2872162)

Представлены новые [хуки](../../../hooks/index.md) для редактирования форм используемые для настроек виджетов полей: `hook_field_widget_complete_form_alter()`, `hook_field_widget_complete_WIDGET_TYPE_form_alter()`, `hook_field_widget_single_element_form_alter()` и `hook_field_widget_single_element_WIDGET_TYPE_form_alter()`.

Из-за введения новых хуков, следующие хуки помечены устаревшими: `hook_field_widget_multiple_form_alter()`, `hook_field_widget_multiple_WIDGET_TYPE_form_alter()`, `hook_field_widget_form_alter()` и `hook_field_widget_WIDGET_TYPE_form_alter()`.

## Добавлен новый хук hook_entity_form_mode_alter()

- [#2899847](https://www.drupal.org/project/drupal/issues/2899847)

Представлен новый хук `hook_entity_form_mode_alter()` позволяющий модулям переопределять режим формы сущности.

Например, разные режимы формы сущности могут быть применены основываясь на роли пользователя:

```php
function hook_entity_form_mode_alter(string &$form_mode, Drupal\Core\Entity\EntityInterface $entity): void {
  // Change the form mode for users with Administrator role.
  if ($entity->getEntityTypeId() == 'user' && $entity->hasRole('administrator')) {
    $form_mode = 'my_custom_form_mode';
  }
}
```

## Аргумент $context удалён для hook_entity_view_mode_alter()

- [#3193131](https://www.drupal.org/project/drupal/issues/3193131)

Ранее, [хук](../../../hooks/index.md) `hook_entity_view_mode_alter()` получал в качестве аргумента массив `$context` который предназначался для передачи дополнительной информации. На практике данный параметр был всегда пустым, в связи с чем было решено удалить данный аргумент за ненадобность.

Ранее:

```php
function hook_entity_view_mode_alter(&$view_mode, Drupal\Core\Entity\EntityInterface $entity, $context) {
  if ($entity->getEntityTypeId() == 'node' && $view_mode == 'teaser') {
    $view_mode = 'my_custom_view_mode';
  }
}
```

После изменения:

```php
function hook_entity_view_mode_alter(&$view_mode, Drupal\Core\Entity\EntityInterface $entity) {
  if ($entity->getEntityTypeId() == 'node' && $view_mode == 'teaser') {
    $view_mode = 'my_custom_view_mode';
  }
}
```

## Изменено поведение для загружаемых файлов, которые содержат небезопасные расширения

- [#2863652](https://www.drupal.org/project/drupal/issues/2863652)

Ранее, если пользовать или API клиент пытался загрузить файл с потенциально опасным расширением (например `php`, `js`), Drupal всегда переименовывал данные файлы добавляя `.txt` в конце. Это действие позволяло исключить исполнение данных файлов веб сервером. Данное поведение также срабатывало на файловые поля которые настроены таким образом, что не разрешают загрузку `.txt`.

Теперь Drupal будет переименовывать данные файлы в `.txt` только если файловое поле позволяет загружать `.txt` файлы. Если поле не разрешает загрузку `.txt` файлов, любая загрузка включающая в себя потенциально опасные файлы будет отклонена.

Данное изменение предоставляет обновление, которое сохранит старое поведение для ранее созданных файловых полей. Тем не менее, настоятельно рекомендуется произвести проверку всех файловых полей на предмет того что они работают так как и задумано в контексте данного изменения.

Более того, API клиенты (JSON:API, REST) возможно начнут отклонять подобные загрузки если они опираются на старое поведение и владельцы сайта решат не использовать новое поведение.

## Drupal Migrate UI теперь правильно использует настройку приватной директории

- [#2925899](https://www.drupal.org/project/drupal/issues/2925899)

При обновлении с Drupal 7 используя Migrate Drupal UI, пути для публичной и приватной файловой директории могли быть введены на странице `/upgrade/credentials`. Значение введённое для приватной директории игнорировалось, что приводило к ситуации когда публичная файловая система использовалась для миграции приватных файлов. Эта ошибка исправлена и теперь будет использоваться значение введенное в настройку приватного файлового пути.

Если вы хотите в дальнейшем использовать старое поведение, в поле настройки приватной директории укажите то же значение, что и для публичной.

## Добавлен метод для чистки информации о БД в бэктрейсах

- [#2999962](https://www.drupal.org/project/drupal/issues/2999962)

Добавлен новый статический метод `Drupal\Core\Database\Log::removeDatabaseEntries()` который принимает следующие аргументы:

- `$backtrace`: Результат вызова `debug_backtrace()`.
- `$driver_namespace`: Пространство имён драйвера БД.

Данный метод удалит из бэктрейса всю информацию связанную с обращением к БД с использованием указанного драйвера.

## Плагин Composer drupal/core-vendor-hardening теперь позволяет чистить пакеты за пределами vendor директории 

- [#3108262](https://www.drupal.org/project/drupal/issues/3108262)

Ранее, [Composer](../../../../../composer/index.md) плагин [drupal/core-vendor-hardening](../../../../../composer/drupal/core-vendor-hardening/index.md) подразумевал что все пакеты установленные через Composer находятся в `vendor` директории. Это поведение приводило к тому, что пакеты, установленные при помощи `composer/installers` не могли быть очищены данным плагином. 

Начиная с данной версии, все пакеты установленные за пределами `vendor` директории с использованием `composer/installers` также могут быть очищены.

## Расширения файлов теперь могут содержать нижнее подчёркивание

- [#3166044](https://www.drupal.org/project/drupal/issues/3166044)

Добавлена возможность указывать расширения файлов содержащие нижнее подчёркивание (`_`).

**Ранее:**

- Недопустимый формат:
  - example.x_t
  - example.x.t
  - example.x_y_t
  
**Сейчас:**

- Допустимый формат:
  - example.x_t
  - example.x.t
  - example.x_y_t
- Недопустимый формат:
  - example.x_.t
  - example.x._t
  - example.xt_
  - example.x__t
  - example._xt-

## Сервисы созданные программно должны указывать публичные они или приватные

- [#3187074](https://www.drupal.org/project/drupal/issues/3187074)

[Создание сервисов](../../../services/create/index.md) без указания области видимости помечено устаревшим и не будет поддерживаться в [Drupal 10](../../../../10/index.md).

В Symfony 5 все сервисы по умолчанию являются приватными, тогда как Drupal ожидает что сервисы по умолчанию публичные. Drupal 9 использует Symfony ^4.4, тогда как Drupal 10 будет использовать Symfony ^5.4.

Таким образом, все сервисы объявляемые в `MODULE.services.yml` становятся публичными по умолчанию (`public: true`), если не указано обратного.

Сервисы созданные программно, будут приватными по умолчанию:

```php
$container = \Drupal::getContainer();
$definition = new Definition(RouteProvider::class);
$container->setDefinition($id, $definition);
```

Если вы хотите сохранить текущее поведение, обновите код и явно укажите что сервис публичный:

```php
$container = \Drupal::getContainer();
$definition = new Definition(RouteProvider::class);
$definition->setPublic(TRUE);
$container->setDefinition($id, $definition);
```

## Плагин обработчик MachineName теперь позволяет указывать регулярное выражение

- [#3157004](https://www.drupal.org/project/drupal/issues/3157004) 

Регулярное выражение используемое в плагине обработчике миграций `MachineName` теперь может быть настроено используя свойство `replace_pattern`.

**Ранее** использовалось конкретное регулярное выражение `/[^a-z0-9_]+/`.

```yaml
process:
  foo:
    plugin: machine_name
    source: foo
```

Пример выше трансформировал бы значение `the.bar` в `the_bar`.

**Теперь** вы можете указать собственное регулярное выражение:

```yaml
process:
  foo:
    plugin: machine_name
    source: foo
    replace_pattern: '/[^a-z0-9_.]+/'
```

Пример выше позволит получить в качестве результата `the.bar`.

## GDToolkit теперь поддерживает WebP

- [#2340699](https://www.drupal.org/project/drupal/issues/2340699)

PHP расширение GD поддерживает формат WebP начиная с версии [7.0.10](https://www.php.net/manual/en/function.imagetypes.php#refsect1-function.imagetypes-changelog).

Drupal ядро теперь может создавать изображения в данном формате.

> [!NOTE]
> Для корректной работы, расширение GD должно быть скомпилировано с поддержкой WebP. Чтобы убедиться, что ваш сервер поддерживает данную возможность, обратитесь к странице информации о состоянии (`admin/reports/status/php#module_gd`).

## README файл конвертирован в Markdown

- [#3192842](https://www.drupal.org/project/drupal/issues/3192842)

Файл `core/README.txt` был конвертирован в Markdown формат и переименован в `core/README.md`. Этот формат более дружелюбен для новых контрибуторов. Информации об использовании Drupal, что раньше была в данном файле, выделена в новый файл `core/USAGE.txt`.

Если вы используете плагин [drupal/core-composer-scaffold](../../../../../composer/drupal/core-composer-scaffold/index.md) для исключения `README.txt`, возможно вы захотите обновить вашу конфигурацию для исключения новых файлов:

```json
{
  ...
  "extra": {
    "drupal-scaffold": {
      "file-mapping": {
        "[web-root]/README.md": false,
        "[web-root]/USAGE.txt": false
      }
    }
  }
}
```

## Проверка доступа _jsonapi_relationship_field_access помечена устаревшей

- [#3055889](https://www.drupal.org/project/drupal/issues/3055889)

Класс `Drupal\jsonapi\Access\RelationshipFieldAccess` и связанная с ним [проверка доступа](../../../routing/access-control/index.md) `_jsonapi_relationship_field_access` помечены [устаревшими](../../../../../deprecation/index.md) и будут удалены в [Drupal 10](../../../../10/index.md).

Замена не предоставляется. Проверка доступов к маршруту в JSON:API является внутренним API и не предназначен для использования за его пределами.

## Добавлено событие для санитизации имён файлов

- [#3032390](https://www.drupal.org/project/drupal/issues/3032390)

Drupal ядро теперь использует новое событие `Drupal\Core\File\Event\FileUploadSanitizeNameEvent` для санитизации имён файлов перед их сохранением. Есть у сущности есть файловое поле, событие будет вызвано из Form Widget, REST API или JSON:API.

Если код использует элемент формы `file` (`\Drupal\Core\Render\Element\File`) — реализация сохранения файла останется неизменной. В таком случае рекомендуется делать вызов `file_save_upload()` который вызовет новое событие и проведёт валидации для файла. Тем не менее, если кастомный или контрибный код вызывает `file_munge_filename()` и делает изменение имени в целях безопасности самостоятельно, то рекомендуется также вызывать новое событие или функцию `file_save_upload()`.

Новое событие будет содержать в себе обработанное имя файла и метку, было ли оно изменено в целях безопасности.

Пример:

```php
$event = new FileUploadSanitizeNameEvent($filename, $extensions);
\Drupal::service('event_dispatcher')->dispatch($event);

$sanitized_filename = $event->getFilename();
$is_security_rename = $event->isSecurityRename();
```

Используя данное событие вы также можете расширить область его применения: транслитерация, приведение имени в нижний регистр, удаление пробелов и т.д.

Drupal ядро добавляет свой подписчик (`Drupal\system\EventSubscriber\SecurityFileUploadEventSubscriber`) в качестве финального и повторяет логику функции `file_munge_filename()`.

В связи с данным изменением, произведены следующие [депрекации](../../../../../deprecation/index.md):

- `file_munge_filename()` - заменён событием.
- `file_unmunge_filename()` - замена не предоставлена.
- `FILE_INSECURE_EXTENSION_REGEX` - заменена `\Drupal\Core\File\FileSystemInterface::INSECURE_EXTENSION_REGEX`.

## Изменения связанные с pager.manager

- [#1202484](https://www.drupal.org/project/drupal/issues/1202484)

- Добавлен новый метод `PagerSelectExtender::getElement()` позволяющий узнать ID пагинации используемой запросом, чтобы использующий его код не требовал необходимости переопределять его для будущих запросов.
- Метод `\Drupal\Core\Pager\PagerManagerInterface::getMaxPagerElementId()`, позволяющий определять максимальный используемый Pager ID теперь является публичным.
- Добавлен новый метод `\Drupal\Core\Pager\PagerManagerInterface::reservePagerElementId()` для резервации необходимого Pager ID. 
- Добавлен новый метод `\Drupal\Core\Database\Connection::getPagerManager()` для быстрого доступа к `pager.manager` из подключения к БД.

## В Database API представлен новый класс - ExceptionHandler, Connection::handleQueryException - помечен устаревшим

- [#3186934](https://www.drupal.org/project/drupal/issues/3186934)

`Connection::handleQueryException` — является `protected` методом и его невозможно вызывать, например, из классов отвечающих за конкретную операцию с БД.

В [#3177660](https://www.drupal.org/project/drupal/issues/3177660) было решено отказаться от использования `Connection::query()` для любых целей. В качестве замены было предложено подготавливать и вызывать `Statement` объекты в драйверах БД, а также обрабатывать исключения более последовательно.

Для это был создан класс `ExceptionHandler`, который необходимо использовать для обработки исключений связанных с драйвером БД.

В связи с данным изменением, `Connection::handleQueryException` помечен устаревшим.

**Ранее:**

```php
    ...
    catch (\PDOException $e) {
      // Most database drivers will return NULL here, but some of them
      // (e.g. the SQLite driver) may need to re-run the query, so the return
      // value will be the same as for static::query().
      return $this->handleQueryException($e, $query, $args, $options);
    }
```

**Сейчас:**

```php
    ...
    catch (\Exception $e) {
      // Most database drivers will return NULL here, but some of them
      // (e.g. the SQLite driver) may need to re-run the query, so the return
      // value will be the same as for static::query().
      ...
        return $this->exceptionHandler($e)->handleExecutionException($stmt, $args, $options);
      ...
    }
```

## Добавлена JavaScript библиотека tabbable на замену jQuery UI tabbable

- [#3113649](https://www.drupal.org/project/drupal/issues/3113649)

Drupal ядро находится на стадии удаления jQuery UI зависимостей. Ядро использовало одну из возможностей jQuery UI - возможность выборки элементов, которые доступны при помощи Tab в текущем контексте при помощи `:tabbable` селектора.

В ядро была добавлена JavaScript библиотека [tabbable](https://github.com/focus-trap/tabbable) (`core/tabbable`), которая позволяет добиться того же результат без зависимости на jQuery UI.

**Ранее:**

```javascript
const tabbableElements = $(context).find(':tabbable');  // returns a jQuery object with every tabbable element found in context.
```

**Сейчас:**

```javascript
const tabbableElements = tabbable.tabbable(context);  // returns an array of DOM nodes of every tabbable element found in context.
```

Данная библиотека также имеет и другие возможности которые описаны в её [документации](https://github.com/focus-trap/tabbable#api).

Так как библиотека использует `CSS.escape`, а IE11 не поддерживает данную возможность, в ядро также был добавлен полифил библиотека [CSS.escape](https://github.com/mathiasbynens/CSS.escape) (`css.escape`).

## В ядро добавлена замена jQuery Once

- [#2402103](https://www.drupal.org/project/drupal/issues/2402103)

Для того чтобы позволить JavaScript скриптам удалить зависимости на jQuery, возможность `jQuery.once()` была реализована на нативном JavaScript. Данная реализация дополнительно опубликована как npm пакет [@drupal/once](https://www.npmjs.com/package/@drupal/once).

### Новое в API

- Объявлена новая библиотека `core/once`
- Библиотека `core/jquery.once` помечена устаревшей
- В новой библиотеке 4 функции:
  - `once` - эквивалент `jQuery.fn.once`
  - `once.filter` - эквивалент `jQuery.fn.findOnce`
  - `once.remove` - эквивалент `jQuery.fn.removeOnce`
  - `once.find` - новая функция, не имеющая аналога в jQuery

### Изменения в коде

**Ранее:**

```yaml
# mymodule.libraries.yml
myfeature:
  js: 
    js/myfeature.js: {}
  dependencies:
    - core/drupal
    - core/jquery
    - core/jquery.once
```

```javascript 
# js/myfeature.js
(function ($, Drupal) {
  Drupal.behaviors.myfeature = {
    attach(context) {
      const $elements = $(context).find('.myfeature').once('myfeature');
    }
  };
}(jQuery, Drupal));
```
---

**Сейчас:**

```yaml
# mymodule.libraries.yml
myfeature:
  js: 
    js/myfeature.js: {}
  dependencies:
    - core/drupal
    - core/once
```

```javascript
# js/myfeature.js
(function (Drupal, once) {
  Drupal.behaviors.myfeature = {
    attach(context) {
      const elements = once('myfeature', '.myfeature', context);
    }
  };
}(Drupal, once));
```

Для более подробной документации новой библиотеки смотрите [@drupal/once](../../../../../javascript/drupal/once/index.md).

Так как IE11 не поддерживает `Element.prototype.matches`, был добавлен полифил `core/drupal.element.matches`.

## Aggregator

- [#3178175](https://www.drupal.org/project/drupal/issues/3178175) Модуль больше не требует наличия `curl`.

## AJAX

- [#3179939](https://www.drupal.org/project/drupal/issues/3179939) Удалён неиспользуемый `AjaxTestBase`.

## Bartik

- [#2031447](https://www.drupal.org/project/drupal/issues/2031447) Улучшена вёрстка для вывода «сеткой» при помощи Views.

## Book

- [#2575827](https://www.drupal.org/project/drupal/issues/2575827) `BookNavigationBlock` и `BookNavigationCacheContext` теперь получают информацию из `route_match` [сервиса](../../../services/index.md), вместо получения из аттрибутов запроса.
- [#2470896](https://www.drupal.org/project/drupal/issues/2470896) Подшивки книг теперь переводимы. В связи с чем классы `Drupal\book\BookExport`, `Drupal\book\BookBreadcrumbBuilder` и `Drupal\book\BookManager` имеют новые зависимости.
- [#3188519](https://www.drupal.org/project/drupal/issues/3188519) В конструкторе `BookBreadcrumbBuilder` исправлена ошибка в названии сервиса.

## CKEditor

- [#3150364](https://www.drupal.org/project/drupal/issues/3150364) Улучшена документация (`/admin/help/ckeditor`) для CKEditor.

## Claro

- [#3083051](https://www.drupal.org/project/drupal/issues/3083051) Произведён рефакторинг tabledrag для соответствия изменениям в ядре.

## Composer

- [#3096781](https://www.drupal.org/project/drupal/issues/3096781) Зависимости `symfony/mime`, `symfony/var-dumper` и `symfony/phpunit-bridge` обновлены до версии 5.2. Добавлена новая зависимость `symfony/deprecation-contracts`.
- [#3187025](https://www.drupal.org/project/drupal/issues/3187025) Зависимости ядра обновлены на 08.12.2020.
- [#3206301](https://www.drupal.org/project/drupal/issues/3206301) Зависимости ядра обновлены на 30.03.2021.

## Configuration System

* [#3196756](https://www.drupal.org/project/drupal/issues/3196756) Исправлена некорректное упоминание файла в документации `ConfigInstallerInterface`.
* [#2844452](https://www.drupal.org/project/drupal/issues/2844452) Многострочные строки в YAML файлах теперь экспортируются без использования `\r\n`.

## Contact

- [#3173756](https://www.drupal.org/project/drupal/issues/3173756) Добавлена административная вкладка "Просмотр" для контактных форм.

## Content Moderation

* [#3203809](https://www.drupal.org/project/drupal/issues/3203809) Вызовы Entity Query в модуле больше не учитывают права доступа.
* [#3204883](https://www.drupal.org/project/drupal/issues/3204883) Сущность `taxonomy_term` теперь корректно запрещена на использование в Content Moderation.

## Cron System

- [#2863464](https://www.drupal.org/project/drupal/issues/2863464) Логи [крона](../../../hooks/cron/index.md) теперь используют тип `info` вместо `notice`, для большего соответствия [RFC 5424](https://tools.ietf.org/html/rfc5424).

## Database System

- [#3129563](https://www.drupal.org/project/drupal/issues/3129563) Сторонние драйвера БД теперь могут переопределять стандартные реализации расширителей запросов: `Drupal\Core\Database\Query\PagerSelectExtender`, `Drupal\Core\Database\Query\TableSortExtender` и `Drupal\search\SearchQuery`.
- [#3129534](https://www.drupal.org/project/drupal/issues/3129534) Добавлены новые методы `Drupal\Core\Database\Connection::getProvider()` и `Connection::enableModuleProvidingDatabaseDriver()`.
- [#3192951](https://www.drupal.org/project/drupal/issues/3192951) Вызовы методов с передачей FQN класса (`'Drupal\Core\Database\Query\PagerSelectExtender'`) заменены на константу (`PagerSelectExtender::class`).
- [#3089326](https://www.drupal.org/project/drupal/issues/3089326) Методу `Drupal\Core\Database\Log::log()` добавлен новый опциональный параметр `start`.

## Editor

- [#3181290](https://www.drupal.org/project/drupal/issues/3181290) Удалён неиспользуемый код в `editor.admin.es6.js`.

## Entity System

- [#3138631](https://www.drupal.org/project/drupal/issues/3138631) Если поле содержит некорректную связь, сообщение об ошибке теперь будет более точно сообщать с чем связана проблема.
- [#3195628](https://www.drupal.org/project/drupal/issues/3195628) `CreateSampleEntityTest` больше не устанавливает схему `file` сущности дважды.
- [#3196168](https://www.drupal.org/project/drupal/issues/3196168) `\Drupal\Core\Entity\EntityDeleteForm` больше не помечен `@internal`.
- [#3159744](https://www.drupal.org/project/drupal/issues/3159744) Из теста `EntitySchemaTest` удалены фиксированные ID.
- [#3201956](https://www.drupal.org/project/drupal/issues/3201956) Удалена передача бесполезного аргумента в `ConfigEntityStorage` при вызове `$this->mapFromStorageRecords()`.
- [#3201957](https://www.drupal.org/project/drupal/issues/3201957) Удалено неиспользуемое свойство `COnfigEntityStorage::$entities`.
* [#3202963](https://www.drupal.org/project/drupal/issues/3202963) Формы удаления bundle сущностей теперь показывают количество материалов, которые основаны на нём.
* [#3203147](https://www.drupal.org/project/drupal/issues/3203147) Удалён решённый `@todo` из `EntityBundleListenerInterface .

## Field System

* [#3203611](https://www.drupal.org/project/drupal/issues/3203611) Исправлен тайпхинт для `\Drupal\field\Entity\FieldConfig::loadByName`.

## File

* [#2479607](https://www.drupal.org/project/drupal/issues/2479607) Удалены устаревшие схемы из `file.file.views.schema.yml`.

## Form System

- [#3122912](https://www.drupal.org/project/drupal/issues/3122912) Вызовы `t()` заменены на `$this->t()`.

## Help

- [#3090659](https://www.drupal.org/project/drupal/issues/3090659) Добавлены Twig функции для установки ссылок на Help Topics: `help_route_link()` и `help_topic_link()`.

## Help Topics

- [#3090257](https://www.drupal.org/project/drupal/issues/3090257) Добавлено больше тестов проверки синтаксиса.
- [#3095737](https://www.drupal.org/project/drupal/issues/3095737) Справка для модулей `config_translation`, `content_translation`, `locale` и `language` конвертирована в Help Topics.

## Install system

* [#3188654](https://www.drupal.org/project/drupal/issues/3188654) Исправлены ссылка на загрузку переводов.
* [#3206168](https://www.drupal.org/project/drupal/issues/3206168) Исправлен вызов `install_display_output()` с третьим аргументом.

## JavaScript

- [#3191497](https://www.drupal.org/project/drupal/issues/3191497) `cpre/jquery.ui.dialog` добавлена зависимость `core/jquery`.

## JSON:API

- [#3163853](https://www.drupal.org/project/drupal/issues/3163853) `ResourceTestBase` теперь использует `::assertEquals()` вместо `::assertSame()` для сравнения данных.

## Layout Builder

- [#3180674](https://www.drupal.org/project/drupal/issues/3180674) Удалён неиспользуемый модуль `layout_builder_overrides_test`.
- [#3115503](https://www.drupal.org/project/drupal/issues/3115503) `\Drupal\Core\Layout\LayoutInterface` теперь расширяет `\Drupal\Core\Plugin\ContextAwarePluginInterface`. Это означает что Layout [плагины](../../../plugins/index.md) теперь контекстно-зависимые.

## MySQL DB Driver

- [#3185231](https://www.drupal.org/project/drupal/issues/3185231) Режим SQL при инициализации теперь задаётся через `ANSI,TRADITIONAL` вместо перечисления всех возможных значений.

## Migration System

- [#2939328](https://www.drupal.org/project/drupal/issues/2939328) Внесены улучшения в подсказки для Drupal Migrate UI.
- [#3151363](https://www.drupal.org/project/drupal/issues/3151363) Исправлена подготовка пути до источника. Теперь она будет без двойных `//` слэшей.
- [#3184545](https://www.drupal.org/project/drupal/issues/3184545) Для `Migration::getMigrationPluginManager()` исправлена документация о возвращаемом типе.
- [#2920168](https://www.drupal.org/project/drupal/issues/2920168) Удалён метод `SqlBaseTest::getHighWaterStorage()`.
- [#3047328](https://www.drupal.org/project/drupal/issues/3047328) Обработчик `Log` теперь может логгировать различные типы данных, а не только строки.
- [#3148959](https://www.drupal.org/project/drupal/issues/3148959) Обработчик `Extract` теперь отображает при обработке какого значение произошла ошибка.
- [#3187477](https://www.drupal.org/project/drupal/issues/3187477) Их источника `TermTranslation` удалено подключение трейта I18nQueryTrait.
- [#2937989](https://www.drupal.org/project/drupal/issues/2937989) Источник данных `Node` для Drupal 6 и Drupal 7 теперь может принимать сразу несколько типов содержимого в качестве источника.
- [#3189878](https://www.drupal.org/project/drupal/issues/3189878) Из плагина источника данных Drupal 7 File удалено свойство `temporaryPath`.
- [#3189064](https://www.drupal.org/project/drupal/issues/3189064) Для плагинов источников данных расширяющих `SqlBase` зависимость `database` больше не сериализуется.
- [#3187263](https://www.drupal.org/project/drupal/issues/3187263) Миграции для конфигурационных блоков `d6_block_translation` и `d7_block_translation` теперь запрашивают `config_translation` вместо `content_translation`.
- [#3194385](https://www.drupal.org/project/drupal/issues/3194385) Тест для d6 миграций `MigrateUserPictureFileTest` объединён в `MigrateUserPictureD6FileTest`.
- [#3190504](https://www.drupal.org/project/drupal/issues/3190504) Исправлена документация к плагинам источников нод.
- [#2814953](https://www.drupal.org/project/drupal/issues/2814953) Добавлены миграции для полей связи с `node` и `user` из Drupal 7.
- [#3192900](https://www.drupal.org/project/drupal/issues/3192900) Некоторые `Kernel` тесты были объединены.
- [#3005969](https://www.drupal.org/project/drupal/issues/3005969) Добавлена поддержка миграций типа поля `telephone` из Drupal 7.
- [#3189054](https://www.drupal.org/project/drupal/issues/3189054) Удалена пометка, что `MigrateException` может быть вызвано конструктором `MigrateExecutable`, так как оно там не вызывается.
- [#3200735](https://www.drupal.org/project/drupal/issues/3200735) Добавлена документация для плагинов источников Drupal 6 и Drupal 7 `user`, `profile` и `roles`.
- [#3175953](https://www.drupal.org/project/drupal/issues/3175953) Произведена чистка в функциональных тестах.
* [#3191990](https://www.drupal.org/project/drupal/issues/3191990) Произведён небольшой рефакторинг кода в `DrupalSqlBaseTest`.
* [#3205029](https://www.drupal.org/project/drupal/issues/3205029) Из `DestinationCategoryTest` удалены референсы на несуществующие классы.

## Node System

- [#3187435](https://www.drupal.org/project/drupal/issues/3187435) Для Views плагина `Vid`, аргумент `$database` помечен устаревшим.

## Olivero

- [#3187884](https://www.drupal.org/project/drupal/issues/3187884) Удален лишний условный оператор из `block--system-branding-block.html.twig`.
- [#3180281](https://www.drupal.org/project/drupal/issues/3180281) Максимальная длина описания для элементов форм задана в 60 символов.
- [#3173007](https://www.drupal.org/project/drupal/issues/3173007) Внесены изменения в разметку и `comments.pcss.css` для соответствия БЭМ методологии.
- [#3176893](https://www.drupal.org/project/drupal/issues/3176893) Внесены изменения в разметку и `book.pcss.css` для соответствия БЭМ методологии.
- [#3173014](https://www.drupal.org/project/drupal/issues/3173014) Внесены изменения в разметку и стили элементов навигации для соответствия БЭМ методологии.
- [#3153260](https://www.drupal.org/project/drupal/issues/3153260) Стандартизовано оформление `:focus` псевдо-элемента среди различных элементов.
- [#3194350](https://www.drupal.org/project/drupal/issues/3194350) Реализовано новое оформление для элементов форм.
- [#3200595](https://www.drupal.org/project/drupal/issues/3200595) Исправлено `outline` оформление для кнопки мобильного меню.
- [#3200631](https://www.drupal.org/project/drupal/issues/3200631) Исправлено отображения `<select>` элемента в jQuery UI dialog на Safari.
- [#3191716](https://www.drupal.org/project/drupal/issues/3191716) Открытие вложенных пунктов меню на мобильной версии теперь требует всего 1 тап для открытия, а не 2 как было ранее.
- [#3192656](https://www.drupal.org/project/drupal/issues/3192656) Исправлена неполадка с текстовыми элементами формы приводящая к появлению горизонтальной прокрутки.
* [#3190268](https://www.drupal.org/project/drupal/issues/3190268) Полифилы поставляемые с темой, заменены на библиотеки с аналогичными полифилами из ядра.
* [#3192903](https://www.drupal.org/project/drupal/issues/3192903) Mouseout событие при наличии активного фокуса на вложенном меню, больше не закрывает его.
* [#3205434](https://www.drupal.org/project/drupal/issues/3205434) Добавлены Nigthwatch тесты.

## Plugin System

- [#3046342](https://www.drupal.org/project/drupal/issues/3046342) Для `ContextAwarePluginInterface` исправлена документация связанная с `@throws`. 

## PostgreSQL DB Driver

- [#3185399](https://www.drupal.org/project/drupal/issues/3185399) Для определения предыдущего ID теперь используется `RETURNING`.

## System

- [#2409413](https://www.drupal.org/project/drupal/issues/2409413) Удалены неиспользуемые RSS настройки и описания.
- [#3002983](https://www.drupal.org/project/drupal/issues/3002983) Протокол в ссылках заменён на HTTPS.
- [#3174832](https://www.drupal.org/project/drupal/issues/3174832) Исправлена документация для `admin-block-content.html.twig`.
* [#3204220](https://www.drupal.org/project/drupal/issues/3204220) `Drupal\system\ModuleDependencyMessageTrait` теперь `Drupal\Core\Extension\ModuleDependencyMessageTrait`.

## Taxonomy

- [#3170185](https://www.drupal.org/project/drupal/issues/3170185) `Drupal\taxonomy\Form\OverviewTerms` теперь использует `pager.manager` для получения текущей страницы вместо `Request`.

## Theme System

- [#3181367](https://www.drupal.org/project/drupal/issues/3181367) Аргумент `LoaderInterface $loader` для `TwigEnvironment::__construct()` теперь всегда должен иметь значение.

## Toolbar

- [#3174422](https://www.drupal.org/project/drupal/issues/3174422) Для тулбара добавлен класс `clearfix`.

## Umami Demo

- [#3051465](https://www.drupal.org/project/drupal/issues/3051465) Поле тегов для материалов теперь не переводимое и не создаёт автоматически несуществующие теги.
- [#3066570](https://www.drupal.org/project/drupal/issues/3066570) Роль редактора теперь имеет больше прав, в связи с чем может: управлять переводами, изучать страницы "помощи", просматривать таксономию, управлять ярлыками.
- [#3061267](https://www.drupal.org/project/drupal/issues/3061267) Машинные имена блоков теперь имеют префикс равный машинному имени темы оформления.
- [#3108503](https://www.drupal.org/project/drupal/issues/3108503) Теперь Layout Builder по умолчанию включен для всех типов содержимого.

## Update

- [#2577407](https://www.drupal.org/project/drupal/issues/2577407) Установка нового модуля через интерфейс теперь имеет постоянный лейбл «Add».

## User

- [#3186752](https://www.drupal.org/project/drupal/issues/3186752) Аргумент `$langcode` для функции `_user_mail_notify()` помечен устаревшим.
* [#3206358](https://www.drupal.org/project/drupal/issues/3206358) Удалена инициализации `$bag` в `SessionManager`.

## Views

- [#2628130](https://www.drupal.org/project/drupal/issues/2628130) Параметр `$database` для `\Drupal\node\Plugin\views\argument\Vid` помечен устаревшим.
- [#2925612](https://www.drupal.org/project/drupal/issues/2925612) Метод `StylePluginBase::wizardForm()` помечен устаревшим, так как нигде не используется.
- [#3186582](https://www.drupal.org/project/drupal/issues/3186582) Отображение по умолчанию во Views теперь именуется как «Default».
- [#3197886](https://www.drupal.org/project/drupal/issues/3197886) Класс `ViewsPluginAnnotationBase` больше не реализуется `AnnotationInterface`.
- [#2342807](https://www.drupal.org/project/drupal/issues/2342807) `DisplayPathTest` больше не пытается включить модуль `menu_ui`, который уже включен к тому моменту.

## Workspaces

- [#3128536](https://www.drupal.org/project/drupal/issues/3128536) Свойство `WorkspaceManager::$blacklist` переименовано в `$supported`.
- [#2998454](https://www.drupal.org/project/drupal/issues/2998454) Употребление терминов «deploy» и «publish» приведено в порядок для избежания путаницы.

## Тестирование

- [#3176655](https://www.drupal.org/project/drupal/issues/3176655) `GoutteDriver` помечен устаревшим, вместо него ядро теперь использует `BrowserKitDriver`.
- [#3132887](https://www.drupal.org/project/drupal/issues/3132887) `BrowserTestBase::drupalGetHeader()` помечен устаревшим. Используйте `$this->getSession()->getResponseHeader()`.
- [#3181329](https://www.drupal.org/project/drupal/issues/3181329) Удалён метод `BrowserTestBase::getResponseLogHandler()` так как он дублирует `BrowserHtmlDebugTrait::getResponseLogHandler()`.
- [#3184632](https://www.drupal.org/project/drupal/issues/3184632) Сравнения для submit инпутов с использованием xpath заменены на WebAssert.
- [#3139431](https://www.drupal.org/project/drupal/issues/3139431) Вызовы устаревшего `AssertLegacyTrait::assertFieldsByValue()` заменены на современные аналоги.
- [#3132919](https://www.drupal.org/project/drupal/issues/3132919) Вызовы `::assert*()` со сравнением в качестве ожидаемого значения заменены на `::assertNot*()`.
- [#3191986](https://www.drupal.org/project/drupal/issues/3191986) Вызовы устаревшего `AssertLegacyTrait::assertNotIdentical()` заменены на современные аналоги.
- [#3169171](https://www.drupal.org/project/drupal/issues/3169171) Исправлены сообщения о [депрекации](../../../../../deprecation/index.md).
- [#3178248](https://www.drupal.org/project/drupal/issues/3178248) Удалён метод `StandardInstallerTest::curlExec()`, так как нигде не используется.
- [#2795567](https://www.drupal.org/project/drupal/issues/2795567) Для тестов `Unit*`, `Kernel*` и `BrowserTests` добавлена глобальная функция `dump()` которая использует Symfony VarDumper.
- [#3193163](https://www.drupal.org/project/drupal/issues/3193163) Использование `AssertLegacyTrait::verbose()` помечено устаревшим.
- [#3187949](https://www.drupal.org/project/drupal/issues/3187949) Метод `::cssSelectToXpath()` перенесён из `BrowserTestBase` в `UiHelperTrait`.
- [#3187113](https://www.drupal.org/project/drupal/issues/3187113) Удалены вызовы `t()` в `::submitForm()`.
* [#3205139](https://www.drupal.org/project/drupal/issues/3205139) Удалён `ModuleTestBase::assertTableCount()`.

## Symfony 5

- [#3188056](https://www.drupal.org/project/drupal/issues/3188056) Обновлён код, который вызывал сериалайзер Symfony с `NULL` в качестве формата.
- [#3185603](https://www.drupal.org/project/drupal/issues/3185603) Добавлен `ConstraintFactory` для инициализации констрейн плагинов Drupal.

## Symfony 6

- [#3195533](https://www.drupal.org/project/drupal/issues/3195533) Константа `Symfony\Component\HttpFoundation\Request::HEADER_X_FORWARDED_ALL` помечена устаревшей. Используйте либо `HEADER_X_FORWARDED_FOR | HEADER_X_FORWARDED_HOST | HEADER_X_FORWARDED_PORT | HEADER_X_FORWARDED_PROTO`, либо `HEADER_X_FORWARDED_AWS_ELB`, либо `HEADER_X_FORWARDED_TRAEFIK`.
- [#3161983](https://www.drupal.org/project/drupal/issues/3161983) Код ядра обновлён для соответствия новому EventDispatcher. Смотрите [Drupal 9.1.0](../../9.1.x/9.1.0/index.md) для более подробных изменений.
- [#3187857](https://www.drupal.org/project/drupal/issues/3187857) Сообщения об устаревшем коде больше не будут приводить к предупреждению об изменении сигнатуры методов `::setDeprecated()`.
* [#3199691](https://www.drupal.org/project/drupal/issues/3199691) Временно отключено тестирование `octal` типа в YAML файлах, так как в спецификации YAML 1.2, который используется Symfony начиная с версии 5.1, поменялся его формат с `0777` на `0o777`.

## Прочие изменения

- [#3173636](https://www.drupal.org/project/drupal/issues/3173636) В `PagerManager` добавлена реализация `PagerManagerInterface::findPage()`.
- [#3181084](https://www.drupal.org/project/drupal/issues/3181084) Из `web.config` удалены комментарии для правила `httpoxy`.
- [#3180998](https://www.drupal.org/project/drupal/issues/3180998) Удалён код для поддержки старых версий PHP ниже 7.3.
- [#3164676](https://www.drupal.org/project/drupal/issues/3164676) Из словаря cspell удалены слова с ошибками, фикстуры для тестов добавлены в список игнорируемых файлов.
- [#3187489](https://www.drupal.org/project/drupal/issues/3187489) Sally Young (justafish) и Théodore Biadala (nod_) добавлены в список мейнтенеров ядра.
- [#3178135](https://www.drupal.org/project/drupal/issues/3178135) `favicon.ico` из `/core/misc` обновлён для соответствия новому логотипу Drupal.
- [#3185652](https://www.drupal.org/project/drupal/issues/3185652) Исправлены множественные ошибки для соответствия стандарту `Drupal.Commenting.DocComment.ShortFullStop`.
- [#3183656](https://www.drupal.org/project/drupal/issues/3183656) Исправлены множественные ошибки для соответствия стандарту `Drupal.Commenting.DocComment.TagGroupSpacing`.
- [#3185614](https://www.drupal.org/project/drupal/issues/3185614) Исправлена опечатка в «The users's account name».
- [#3170260](https://www.drupal.org/project/ideas/issues/3170260) В `MAINTAINERS.txt` добавлен раздел для инициативы Decoupled Menus.
- [#3188920](https://www.drupal.org/project/drupal/issues/3188920) Guzzle исключения обновления для совместимости с Guzzle 7.
- [#3147135](https://www.drupal.org/project/drupal/issues/3147135) Исправлены множественные ошибки для соответствия стандарту `Drupal.Classes.UseGlobalClass`.
- [#3189466](https://www.drupal.org/project/drupal/issues/3189466) Francesco Placella (plach) снял с себя полномочия мейнтейнера ядра.
- [#3162827](https://www.drupal.org/project/drupal/issues/3162827) В модулях ядра, где использование хранилища сущности является опциональным, данные хранилища запрашиваются только в данной ситуации. Следовательно, в классах теперь хранится `entityStorage`, вместо хранилища конкретной сущности.
- [#3162822](https://www.drupal.org/project/drupal/issues/3162822) Исправлены проблемы со словами включающие "reference" и CSPell.
- [#3193381](https://www.drupal.org/project/drupal/issues/3193381) Удалена информация о Workspace инициативе. Инициатива теперь является часть "[инициатив сообщества](https://www.drupal.org/community-initiatives/workflow-in-core-initiative)".
- [#3195571](https://www.drupal.org/project/drupal/issues/3195571) Константа `Drupal\Core\Routing\RouteCompiler::REGEX_DELIMITER` помечена устаревшей.
- [#2002514](https://www.drupal.org/project/drupal/issues/2002514) Функция `debug()` помечена устаревшей. Удалены все упоминания для `_drupal_debug_message()`. Используйте `dump()`.
- [#3191487](https://www.drupal.org/project/drupal/issues/3191487) Из `MAINTAINERS.txt` удалён раздел с "Layout Initiative", так как она считается завершённое. Все последющие изменения связанные с макетами будут классифицироваться под инициативой "Easy out of the box".
- [#2723621](https://www.drupal.org/project/drupal/issues/2723621) Исправлены ошибки для соответствия стандартам `Drupal.Commenting.FunctionComment.IncorrectTypeHint` и `Drupal.Commenting.FunctionComment.InvalidTypeHint`.
- [#3188957](https://www.drupal.org/project/drupal/issues/3188957) В `DrupalTestBrowser` теперь используется `RequestException::hasResponse()`.
- [#2510438](https://www.drupal.org/project/drupal/issues/2510438) Удалён индекс `all` для таблицы `key_value_expire`.
- [#3185653](https://www.drupal.org/project/drupal/issues/3185653) Удалены упоминания `::drupalPostAjaxForm()`, который был удалён в [Drupal 9](../../../index.md).
- [#3200213](https://www.drupal.org/project/drupal/issues/3200213) Добавлена документация для сервиса `session_bag`.
- [#3202014](https://www.drupal.org/project/drupal/issues/3202014) `pager_test_preprocess_pager()` теперь использует early return если пейджер не найден.
* [#3202787](https://www.drupal.org/project/drupal/issues/3202787) Исправлены ссылка в документации к `FunctionalTestSetupTrait::setContainerParameter()`.
* [#2508071](https://www.drupal.org/project/drupal/issues/2508071) Добавлено объявление свойства `KeyValueFactory::$options`.
* [#2711739](https://www.drupal.org/project/drupal/issues/2711739) Добавлена отсутствующая документация для методов `QueryInterface`.
* [#1923816](https://www.drupal.org/project/drupal/issues/1923816) Исправлены примеры для `QueryAggregateInterface`.
* [#3186626](https://www.drupal.org/project/drupal/issues/3186626) Удалена поддержка параметра `$proxy_class_name` для метода `ProxyBuilder::build()`, так как он не используется.
* [#3198594](https://www.drupal.org/project/drupal/issues/3198594) Добавлен класс `Drupal\Core\Http\InputBag` для предоставления моста между Symfony 4 и 5, только для внутреннего пользования.
* [#2903911](https://www.drupal.org/project/drupal/issues/2903911) Исправлены ошибки стандарта кодирования `Drupal.Commenting.FunctionComment.ParamMissingDefinition`.
* [#2160091](https://www.drupal.org/project/drupal/issues/2160091) `drupal_flush_all_caches()` больше не сбрасывает контейнер дважды. Данная функция теперь получает параметр `$kernel`, если он не передан, или не является экземпляром `DrupalKernel`, то будет сброшен кеш контейнера. При наличии данного аргумента, ответственность за сброс кеша контейнера ложится на того, кто вызвал данную функцию.
* [#2937858](https://www.drupal.org/project/drupal/issues/2937858) Исправлены ошибки для соответствия стандарту `Drupal.Commenting.DocCommentAlignment`.
* [#3207119](https://www.drupal.org/project/drupal/issues/3207119) Исправлены ошибки для соответствия стандарту `Squiz.WhiteSpace.ScopeKeywordSpacing`.
* [#2829453](https://www.drupal.org/project/drupal/issues/2829453) Из `\Drupal` удалён docblock c `@file`.