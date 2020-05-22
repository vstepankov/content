---
id: release-8.8.7
title: 'Drupal 8.8.7'
path: /8/releases/8.8.7
core: 8
metatags:
  title: 'Drupal 8.8.7: Список изменений'
  description: 'Патч релиз Drupal 8.8.7, список изменений.'
---

**Дата релиза**: В разработке

> [!WARNING]
> Данный релиз находится в разработке.

## Basic Auth

- [#3124281](https://www.drupal.org/node/3124281) Исправлены грамматически ошибки в документации `BasicAuth`.

## CKEditor

- [#3070745](https://www.drupal.org/node/3070745) `localStorage` теперь хранит только последние версии стилей, для избежания проблем лимита хранения данного хранилища.

## Field

- [#3113124](https://www.drupal.org/node/3113124) Исправлена ошибка в примере `hook_field_info_alter()`.

## JSON:API

- [#3126906](https://www.drupal.org/node/3126906) Исправлена ошибка в коде из-за которой файл `MenuLinkContentTest` распознавался как бинарный файл.

## Media Library

- [#3099528](https://www.drupal.org/node/3099528) Удалён дублирующий параграф в справке по модулю.

## Migrate

- [#3125763](https://www.drupal.org/node/3125763) Модуль `migrate_no_migrate_drupal_test` теперь имеет зависимость на `drupal:node`.

## Place Block

- [#3116399](https://www.drupal.org/node/3116399) (Только для Drupal 8) Модуль больше не будет вызывать ошибку что он устарел.

## Statistics

- [#3128761](https://www.drupal.org/node/3128761) Из запроса удалено дублирование плейсхолдера `:timestamp`.

## Taxonomy

- [#3101635](https://www.drupal.org/node/3101635) Исправлены опечатки в комментариях `taxonomy.es6.js` где было упоминание блоков вместо таксономии.

## Views

- [#2989745](https://www.drupal.org/node/2989745) Обновление конфигураций обновления `views_update_8500()` перенесены на процесс сохранения конфигурации для сохранения BC.

## Views UI

- [#3087465](https://www.drupal.org/node/3087465) Документация для хука `hook_views_ui_display_top_links_alter()` перенесена в `views_ui.api.php`.

## User

- [#3084813](https://www.drupal.org/node/3084813) Тайпхинт для `user_load()` был изменён с `object|bool` на `\Drupal\user\UserInterface|false`.

## Update

- [#2992631](https://www.drupal.org/node/2992631) Информация об обновлении больше не будет рекомендовать новую минорную версию при наличии обновления безопасности для текущей минорной.
- [#3120961](https://www.drupal.org/node/3120961) Из модулей для тестирования удалены `version: VERSION`, так как версии подставляются динамически во время тестирования.
- [#3111463](https://www.drupal.org/node/3111463) Улучшена документация кода для `Drupal\update\ProjectSecurityData`.

## Тестирование

- [#3126695](https://www.drupal.org/node/3126695) Возвращены методы `::assertEqualsCanonicalizing`, `::assertNotEqualsCanonicalizing`, `::assertEqualsCanonicalizing`, `::assertNotEqualsCanonicalizing` и `::testAssertEqualsCanonicalizing`, которые вызывают новые. Это изменение касается только Drupal 8. Тесты для Drupal 9 по-прежнему должны использовать новые методы.
- [#3126797](https://www.drupal.org/node/3126797) Возвращены методы `::assertStringContainsString`, `::assertStringContainsStringIgnoringCase`, `::assertStringNotContainsString`, `::assertStringNotContainsStringIgnoringCase`, `::assertStringContainsString`, `::assertStringContainsStringIgnoringCase`, `::assertStringNotContainsString`, `::assertStringNotContainsStringIgnoringCase`  и `::testAssertStringContainsString`, которые вызывают новые. Это изменение касается только Drupal 8. Тесты для Drupal 9 по-прежнему должны использовать новые методы.
- [#3122547](https://www.drupal.org/node/3122547) Исправлены опечатки при сравнении строк
- [#3132745](https://www.drupal.org/node/3132745) Свойство `$modules` приведено к стандартам. Там где оно превышает 80 символов, используется перенос.
- [#2978398](https://www.drupal.org/node/2978398) `UserPasswordResetTest` больше не расширяет `PageCacheTagsTestBase`.
- [#3131474](https://www.drupal.org/node/3131474) Сравнения, использующие `array_search()` заменены на `::assertContains()`, `::assertNotContains()`.
- [#3123253](https://www.drupal.org/node/3123253) Удалено использование `AssertLegacyTrait::pass()`.
- [#3126787](https://www.drupal.org/node/3126787) (только Drupal 8) Добавлены методы проверки типов для обратной с PHPUnit 6 и 7 через `::assertInternalType()`.
- [#3082602](https://www.drupal.org/node/3082602) Из `disable_transitions.theme.css` удалено правило отключения `tranform`.
- [#3121020](https://www.drupal.org/node/3121020) Фикстуры модуля `update_test` перенесены из корня модуля в `fixtures/release-history`.
- [#3134475](https://www.drupal.org/node/3134475) В тесте `CommentIntegrationTest` сравнение строки с blob значением теперь производится после запроса, а не внутри него.
- [#3126333](https://www.drupal.org/node/3126333) Использование параметра `$canonicalize` для `::assertEquals` заменено на `::assertEqualsCanonicalizing`.
- [#3134333](https://www.drupal.org/node/3134333) `SearchSimplifyTest` и `SearchTokenizerTest` теперь расширяют `KernelTest`.

## Прочие изменения

- [#3030989](https://www.drupal.org/node/3030989) Исправлена ошибка вызываемая при попытке массово удалить удалённые ноды.
- [#3074047](https://www.drupal.org/node/3074047) Исправлена и улучшена документация метода `MigrateDestinationInterface::import`. Теперь возвращаемый тип не `mixed`, а `array|bool`.
- [#3098475](https://www.drupal.org/node/3098475) Проверка на реализацию хука `hook_update_last_removed()` в процессе обновления баз данных стала более строгой и сообщение об ошибке более доступным.
- [#3121362](https://www.drupal.org/node/3121362) Различные исправления опечаток.
- [#3120901](https://www.drupal.org/node/3120901) Сообщения об устаревшем коде в 8.8.4 обновлены до 8.8.5, так как [Drupal 8.8.4](release-8.8.4.md) — обновление безопасности.
- [#3130427](https://www.drupal.org/node/3130427) Исправлено значение в фикстуре `video_collegehumor.xml` приводящее к ошибке.
- [#3126957](https://www.drupal.org/node/3126957) Добавлены отсутствующие фигурные скобки для `@inheritdoc`.
- [#3120910](https://www.drupal.org/node/3120910) Сайты, у которых отсутствуют сведения о текущей схеме получат 8001 по умолчанию. Это позволит корректно запускаться обновлениям.
- [#3132287](https://www.drupal.org/node/3132287) Исправлены некорректные использования `{@inheritdoc}`.
- [#3020905](https://www.drupal.org/node/3020905) Удалён пример реализации метода `ModuleUninstallValidatorInterface::validate()` из документации метода.
- [#3100251](https://www.drupal.org/node/3100251) Исправлены некорректные неймспейсы для классов и интерфейсов в комментариях.
- [#3074064](https://www.drupal.org/node/3074064) Исправлен референс в `LoggerChannelFactoryInterface::addLogger()` на существующий.
- [#3063694](https://www.drupal.org/node/3063694) В документацию к классу `Url` добавлены примеры использования.
- [#3094067](https://www.drupal.org/node/3094067) Обновлены и добавлены отсутствующие `@param` и `@return` документации для `TypedDataInterface`.
- [#3110620](https://www.drupal.org/node/3110620) Исправлена документация `ModuleHandler::invokeAll()`.