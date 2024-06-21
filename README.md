# git-in-practice

[TBD](https://trunkbaseddevelopment.com/#one-line-summary), [CI](https://martinfowler.com/articles/continuousIntegration.html), проектирование и декомпозиция - всё это помогает нам лучше и быстрее разрабатывать и приносить пользу клиентам.

TL;DR: тесты, автоматизация, декомпозиция - частые мёрджи с полной уверенностью, что основная ветка в стабильном состоянии

## Стандартный флоу работы

Мейн:
Создать удаленно репо   
или если он уже есть - скопировать существующий:  
`git@github.com:timk01/git-testing.git`  
(file->new->from version control)
либо через клон

### Далем изменния

Сейчас я нахожусь на ветке мейн по-умолчанию:
`git remote show origin `  
[картинка](src/images/img.png)  
git push origin main


git ну норм

### Создаём ветку  
```bash
git checkout -b feature/TASK-123 --no-track origin/trunk
```

### Создаём ветку в origin  
```bash
git push -u origin $(git branch --show-current)
```

### Вносим изменения и добавляем их в отслеживаемые  
```bash
git add .
git commit -m TASK-123 Добавлена важная доработка
git push
```

### Готовим ветку к Pull Request
#### Вмёрдживаем основную ветку к себе

```bash
git fetch
git merge origin/trunk
```

#### Делаем squash (один из способов, как по мне самый простой)  
```bash
git reset --soft $(git merge-base trunk HEAD)
git commit -m "TASK-123 Добавлен один красивый коммит"
```

#### Пушим свои изменения  
```bash
git push --force-with-lease
```
Альтернативно - это можно сделать в gitlab/github, там есть функциональность squash and merge.  

## Полезные команды

#### Показать список веток.
```bash
git branch -a
```

#### Посмотреть историю коммитов в виде графика
```bash
git log --graph --oneline --decorate
```

#### Изменить описание последнего коммита
```bash
git commit --amend -m "Описание коммита"
```

#### Посмотреть изменения между текущей веткой и trunk
```bash
git diff HEAD..origin/trunk
```

#### Удалить локальную и удалённую ветку
```bash
git push origin -d <имя_ветки>
git branch -D <имя_ветки>
```

#### Установить и запушить тэг
```bash
git tag <x.y.z>
git push origin --tags
```
Дополнительно: [семантическое версионирование](https://semver.org/lang/ru/)

#### Перенести коммит из одной ветки в другую
```bash
git cherry-pick <хэш-коммита>
```

## Линейная или нелинейная история: rebase или merge?

![liner.png](linear.png)

![merge_commits.png](merge_commits.png)

## Попрактиковаться в git

https://learngitbranching.js.org