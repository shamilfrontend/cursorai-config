# Руководство по Conventional Commits

Используем спецификацию [Conventional Commits v1.0.0](https://www.conventionalcommits.org/en/v1.0.0/#summary)

## Зачем это нужно?

- ✅ Автоматическая генерация CHANGELOG
- ✅ Автоматическое определение версии (SemVer)
- ✅ Понятная история изменений
- ✅ Легче делать code review
- ✅ Проще откатывать изменения

---

## Формат коммита

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

### Структура:

1. **type** — обязательный, тип изменения (feat, fix, docs и т.д.)
2. **scope** — опциональный, область изменения (auth, api, ui и т.д.)
3. **description** — обязательное, краткое описание
4. **body** — опциональное, подробное описание
5. **footer** — опциональный, метаданные (breaking changes, ссылки на issues)

---

## Типы коммитов

### Основные (влияют на версионирование)

| Тип | Описание | SemVer | Пример |
|-----|----------|--------|--------|
| **feat** | Новая функциональность | MINOR (0.x.0) | `feat: добавлена регистрация` |
| **fix** | Исправление бага | PATCH (0.0.x) | `fix: исправлена валидация email` |
| **BREAKING CHANGE** | Несовместимое изменение | MAJOR (x.0.0) | `feat!: изменен формат API` |

### Дополнительные (не влияют на версионирование)

| Тип | Описание | Пример |
|-----|----------|--------|
| **docs** | Документация | `docs: обновлен README` |
| **style** | Форматирование | `style: исправлены отступы` |
| **refactor** | Рефакторинг | `refactor: упрощена логика auth` |
| **perf** | Производительность | `perf: оптимизированы SQL запросы` |
| **test** | Тесты | `test: добавлены тесты для API` |
| **build** | Система сборки | `build: обновлен webpack config` |
| **ci** | CI/CD | `ci: добавлен деплой в staging` |
| **chore** | Рутинные задачи | `chore: обновлены зависимости` |
| **revert** | Откат коммита | `revert: откат feat: регистрация` |

---

## Примеры

### Простой коммит

```bash
git commit -m "feat: добавлена возможность экспорта в PDF"
```

### С scope

```bash
git commit -m "fix(auth): исправлена проверка JWT токена"
```

Популярные scope для проекта:
- `auth` — аутентификация и авторизация
- `api` — API endpoints
- `ui` — пользовательский интерфейс
- `db` — база данных
- `poker` — Planning Poker
- `retro` — ретроспективы
- `teams` — команды
- `websocket` — WebSocket соединения

### С телом и футером

```bash
git commit -m "fix(websocket): исправлена утечка памяти при реконнекте

При переподключении старые обработчики событий не удалялись,
что приводило к накоплению слушателей и утечке памяти.

Теперь при реконнекте очищаем все старые обработчики.

Fixes: #123
Reviewed-by: @username"
```

### Breaking change (способ 1 — с !)

```bash
git commit -m "feat!: изменен формат ответа API /api/users

Теперь возвращаем объект с пагинацией вместо массива"
```

### Breaking change (способ 2 — с футером)

```bash
git commit -m "feat: изменен формат ответа API /api/users

BREAKING CHANGE: API теперь возвращает объект {data, total, page} 
вместо простого массива пользователей"
```

### Несколько файлов, одна цель

```bash
# ✅ Правильно — один коммит
git add src/auth/* tests/auth.test.ts
git commit -m "feat(auth): добавлена двухфакторная аутентификация"
```

```bash
# ❌ Неправильно — несколько разных изменений в одном коммите
git add src/auth/* src/poker/* src/ui/*
git commit -m "fix: разные исправления"
```

### Revert коммита

```bash
git revert abc1234

# Автоматически создаст коммит:
# revert: feat: добавлена регистрация через OAuth
#
# This reverts commit abc1234.
```

Или вручную:

```bash
git commit -m "revert: feat(auth): регистрация через OAuth

This reverts commit abc1234.
Причина: конфликт с новой системой аутентификации"
```

---

## Правила для нашего проекта

### ✅ DO

1. **Используй русский язык** для описания
   ```bash
   feat: добавлена регистрация  # ✅
   feat: added registration       # ❌
   ```

2. **Начинай с маленькой буквы**
   ```bash
   feat: добавлена функция  # ✅
   feat: Добавлена функция  # ❌
   ```

3. **Без точки в конце**
   ```bash
   fix: исправлена ошибка  # ✅
   fix: исправлена ошибка. # ❌
   ```

4. **Максимум 100 символов** в первой строке
   ```bash
   # ✅ 72 символа
   feat(auth): добавлена проверка email при регистрации
   
   # ❌ 120 символов
   feat(auth): добавлена проверка email при регистрации пользователя с валидацией формата и отправкой письма
   ```

5. **Используй императивное наклонение** (как в документации)
   ```bash
   feat: добавь функцию      # ✅ (повелительное)
   feat: добавил функцию     # ❌ (прошедшее)
   feat: добавляет функцию   # ❌ (настоящее)
   ```
   
   *Хотя в русском языке чаще используют прошедшее время — это приемлемо:*
   ```bash
   feat: добавлена функция   # ✅ (приемлемо для русского)
   ```

6. **Разбивай большие изменения** на несколько коммитов
   ```bash
   # ✅ Правильно
   git commit -m "refactor(auth): вынес валидацию в отдельный модуль"
   git commit -m "feat(auth): добавлена проверка сложности пароля"
   git commit -m "test(auth): добавлены тесты для валидации"
   
   # ❌ Неправильно
   git commit -m "feat: переработка auth модуля"
   ```

### ❌ DON'T

1. **Не используй общие формулировки**
   ```bash
   fix: исправление        # ❌ Что именно?
   feat: новая фича        # ❌ Какая фича?
   chore: обновление       # ❌ Что обновлено?
   ```

2. **Не смешивай разные изменения**
   ```bash
   # ❌ Плохо
   git commit -m "feat: добавлена регистрация и исправлен баг в логине"
   
   # ✅ Хорошо
   git commit -m "feat: добавлена регистрация через email"
   git commit -m "fix: исправлен редирект после логина"
   ```

3. **Не используй WIP коммиты** в основных ветках
   ```bash
   git commit -m "WIP: работаю над фичей"  # ❌ Только в feature ветках
   ```

4. **Не забывай про scope** для больших модулей
   ```bash
   fix: ошибка токена        # ⚠️  Можно лучше
   fix(auth): ошибка токена  # ✅ Понятнее
   ```

---

## Автоматические инструменты

### Commitlint (опционально)

Можно добавить автоматическую проверку:

```bash
npm install --save-dev @commitlint/cli @commitlint/config-conventional

# .commitlintrc.js
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [2, 'always', [
      'feat', 'fix', 'docs', 'style', 'refactor', 
      'perf', 'test', 'build', 'ci', 'chore', 'revert'
    ]],
    'subject-case': [2, 'never', ['sentence-case', 'start-case', 'pascal-case', 'upper-case']],
    'subject-max-length': [2, 'always', 100]
  }
}

# Добавь в package.json
"husky": {
  "hooks": {
    "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
  }
}
```

### GitLab CI проверка

Можно добавить проверку в `.gitlab-ci.yml`:

```yaml
validate:commits:
  stage: test
  script:
    - npm install -g @commitlint/cli @commitlint/config-conventional
    - echo "module.exports = {extends: ['@commitlint/config-conventional']}" > .commitlintrc.js
    - commitlint --from origin/main --to HEAD
  only:
    - merge_requests
```

---

## Шпаргалка

```bash
# Новая фича
git commit -m "feat: описание"
git commit -m "feat(scope): описание"

# Исправление бага
git commit -m "fix: описание"
git commit -m "fix(scope): описание"

# Breaking change
git commit -m "feat!: описание"
git commit -m "feat(scope)!: описание"

# Документация
git commit -m "docs: обновлен README"

# Стили/форматирование
git commit -m "style: исправлены отступы"

# Рефакторинг
git commit -m "refactor(auth): упрощена логика"

# Производительность
git commit -m "perf: оптимизированы запросы к БД"

# Тесты
git commit -m "test: добавлены тесты для API"

# CI/CD
git commit -m "ci: добавлен staging деплой"

# Рутина
git commit -m "chore: обновлены зависимости"

# С телом
git commit -m "fix: описание

Подробное описание что и почему было исправлено.

Fixes: #123"
```

---

## FAQ

**Q: Обязательно использовать английский?**
A: Нет, в нашем проекте используем русский (см. workspace rules).

**Q: Можно ли использовать прошедшее время в русском?**
A: Да, "feat: добавлена" вместо "feat: добавь" более естественно для русского языка.

**Q: Что если коммит касается нескольких областей?**
A: Лучше разбить на несколько коммитов. Если невозможно — не указывай scope или используй общий scope.

**Q: Нужно ли писать breaking change в body и footer одновременно?**
A: Нет, достаточно либо `!` в заголовке, либо `BREAKING CHANGE:` в футере.

**Q: Как исправить неправильный коммит до пуша?**
A: Используй `git commit --amend` для изменения последнего коммита или `git rebase -i` для более старых.

**Q: Обязательно ли scope?**
A: Нет, но желательно для больших проектов. Помогает быстро понять какая часть изменена.

---

## Полезные ссылки

- [Conventional Commits Specification](https://www.conventionalcommits.org/en/v1.0.0/#summary)
- [Semantic Versioning](https://semver.org/)
- [Commitlint](https://commitlint.js.org/)
- [Русская спецификация](https://www.conventionalcommits.org/ru/v1.0.0/)

---

**🎯 Главное:** Пиши понятные коммиты, которые объясняют **что** и **зачем** изменено. Conventional Commits — это просто структура для этого.







