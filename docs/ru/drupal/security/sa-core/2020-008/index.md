---
title: 'SA-CORE-2020-008'
slug: security/sa-core-2020-008
metatags:
  title: 'Drupal: SA-CORE-2020-008'
  description: 'Умеренно критическое. Исправлено в версиях: 8.8.10, 8.9.6, 9.0.6.'
---

**Проект:** Drupal Core\
**Дата публикации:** 16 сентября 2020\
**Риск безопасности:** Умеренно критическое. 12/25 AC:Basic A:None CI:Some II:Some E:Theoretical TD:Default\
**Уязвимость:** Обход доступа

## Описание

Экспериментальный модуль Workspaces позволял создавать несколько рабочих областей на сайте в которых черновики могли быть отредактированы перед публикацией в live рабочую область.

Модуль недостаточно эффективно проверял права доступа при смене рабочих областей, что могло приводить к обходу доступа. Злоумышленник может увидеть содержимое сайта прежде чем оно было бы опубликовано для всех.

## Решение

Обновиться до актуальных версий:

- Если используете Drupal 8.8.x, обновитесь до [Drupal 8.8.10](../../../8/releases/8.8.x/8.8.10/index.md).
- Если используете Drupal 8.9.x, обновитесь до [Drupal 8.9.6](../../../8/releases/8.9.x/8.9.6/index.md).
- Если используете Drupal 9.0.x, обновитесь до [Drupal 9.0.6](../../../9/releases/9.0.x/9.0.6/index.md).

Если сайт использует Workspaces и был обновлён, авторизованный пользователи по прежнему могут иметь доступ к рабочим областям что они получили ранее. Для того чтобы предотвратить несанкционированный доступ, рекомендуется «разавторизовать» всех пользователей сайта (например, очистив таблицу `sessions`). Обратите внимание на то, что это может иметь последствия, так как все пользователи будут вынужденый авторизоваться заново, а весь несохраненный ввод будет утерян.

## Ссылки

- [SA-CORE-2020-008](https://www.drupal.org/sa-core-2020-008) (англ.), drupal.org, 16 сентября 2020