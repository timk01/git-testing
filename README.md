# git-in-practice

[TBD](https://trunkbaseddevelopment.com/#one-line-summary), [CI](https://martinfowler.com/articles/continuousIntegration.html), проектирование и декомпозиция - всё это помогает нам лучше и быстрее разрабатывать и приносить пользу клиентам.

TL;DR: тесты, автоматизация, декомпозиция - частые мёрджи с полной уверенностью, что основная ветка в стабильном состоянии

_Дисклеймер. Стал вставлять картинками отдельно, так что можно скроллить если что знакомо и уже "нюхалю пороху"._  

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
![картинка](src/images/img.png)

### запомнить как отче наш:

`git add .` - добавляет все файлы к отслеживаемым (есть несколько категорий, новые, измененные и неотслеживаемые)    
`git commit -m "commit description"  ` - с осмысленным коммитов (помним что 123 или фывапролдж - это ни о чем)  
`git push origin main` - пушим на удаленный репо в мейн (ориджин мейн - условно можно понимать как составное имя)  
возможно, придется убидеться что есть доступ/пароли  

Теперь. Как посмотреть ветки?  
`git branch (текущая ветка - будет мейн если не создавал иную)`  
`git branch -r (удаленные ветки) `     
`git remote -v (настройки удаленного репо, откуда фетчить, куда пушить)`      

### Переименование главной ветки (локально, уадленно)
`git branch -m main trunk (local repo rename)`  
`git push origin trunk (пушим обратно)`  
здесь он вообще предложит создать мр, но - нафиг оно (об этом позже)  
![очередная картинка](src/images/img_1.png)  
так переименовывать ветку. switch branch to another trunk --> yes (or kinda) - выбираем транк  
https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-branches-in-your-repository/changing-the-default-branch  
`git push origin --delete main (так сносим удаленную ветку)`  
так как удаленную ветку мы снесли, изменения чуть подвисли  
-- надо их запушить  (3 команды выше)  
![итого](src/images/img_2.png)

### Создаём ветку (обычно)
_здесь процесс подробен, дабы не повторять его потом_  
`git checkout -b feature/TASK-123`  
![изменения](src/images/img_3.png)  
![эдд-коммит-пуш](src/images/img_4.png)  
![ПР/МР ГХ](src/images/img_5.png)  
эмулируем конфликт -- переключимся в мейн и то-нибудь там изменим (в реальной работе, например, кто-то что-то смерджил туда ДО нас - например слили оередной МР)  
![conflicted_changes.png](src%2Fimages%2Fconflicted_changes.png)  
пуш-мр  
![conficted_commit_trunk.png](src%2Fimages%2Fconficted_commit_trunk.png)  
-- здесь изменения есть в главной ветке, но в нашей их нет:  
![img_1.png](src/images/conflict_in_MR.png)    
![img_6.png](src%2Fimages%2Fimg_6.png)    
- так идея предлагает это разруливать (не на ГХ, думаем что у нас сложный конфликт и идея сама не знает что с ним делать)  
кстати да, обращаю внимание что картинки из мейна с картинками здесь не конфликтуют (и ридмишка тоже, т.к. изменения я в мейне не вносил) - то есть это только ОБЩИЕ файлы  
![img_7.png](src%2Fimages%2Fimg_7.png)  
- пуллим, но здесь идея сама уже все за нас решила и мы пропустили много шагов  
![img_1.png](src/images/img_17.png)  
- а именно, идея сама умная и мерджит за нас  
![img_8.png](src%2Fimages%2Fimg_8.png)  
- решаем конфликты сами, фиксируем изменения    
адд, мр, пуш  
![img_9.png](src%2Fimages%2Fimg_9.png)   
-- итого    
(здесь, правда, конфликт простой, но уже можно увидеть почему оно вообще происходит и что с этим делать, да)  

### Создаём ветку (чуть вернее)
_здесь и далее то, что происходило в лекции_   
убеждаемся что мы все еще на транке:  
`git branch`
```bash
git checkout -b feature/TASK-123 --no-track origin/trunk
```
`git checkout -b feature/TASK-123 origin/trunk` 
(это создать ветку стандартно (потому и Б) но - **НО на основе удаленной ветки**)  
(сравни с `git checkout -b feature/TASK-123` - где **просто на основе какой-то** (при этом не обязательно мейна) ветки

теперь - ПОЧЕМУ это важно. а все потому что есть конфликты, в том числе и мы создаем на основе свежайшей (и нецелованной) как слеза юной девы, удаленной ветки  
с точки зрения репо - мы будем работать с последними изменениями, а так как мы предполагаем что удаленный транк - самый последний, это существенно меньше возни в дальнейшей  

теперь - что за зверь `--no-track`  
ноутрак главным образом - так и работает как "не отслежвать" - то есть у нас теперь ветка, которя никак не синхронизируется с главной и вообще про нее не знает  
что уменьшает конфликты, не нужно часто сливать/слиять (мерж/ребейз) и превращает векту в ту самую сильную и независимую, которая все делает сама

_короче, с точки зрения транк-разработки - самое оно (но не без минусов, потому что сами можем наворотить делов)_  

### Создаём ветку в origin  
```bash
git push -u origin $(git branch --show-current)
```
`$(команда)` - это типа подзапроса в скуэле, сначала выполнится он  
_напомню, что мы уже сделали изменения к этому моменту и зафиксировали их_  
сама `$(git branch --show-current)` вернет ни что иное как текущую ветку, например так:  
![img_10.png](src%2Fimages%2Fimg_10.png)  
а первая часть команды - это обычный пуш
итого, выходит что:  
`git push -u origin $(git branch --show-current)`  
эквивалентно  
`git push -u origin feature/TASK-123`  
(делается это для удобства, чтобы когда ветки называются схоже, нельзя было опечататься - тем самым снижая возможные ошибки)  

и да: полная команда в вин работать **не** будет, т.к. окошки не поддерживают прямую подстановку команд. sadface
как обходной путь, можно из баша:  
![img_11.png](src%2Fimages%2Fimg_11.png)
вам возможно придется переоткрыть идею и терминал снизу, но оно - работает:
![img_12.png](src%2Fimages%2Fimg_12.png)

### Вносим изменения и добавляем их в отслеживаемые  
(это мы итак знаем)  
```bash
git add .
git commit -m TASK-123 Добавлена важная доработка
git push
```

### Готовим ветку к Pull Request №1 (начало)
Вернемся к пункту _### Создаём ветку (чуть вернее) выше_
сделаем git pull, а как именно и почему - _смотреть ниже_.

### Лирическое отступление про fetch и pull
Например мы делаем `git fetch origin trunk` в нашу основную ветку  
Что делает эта команда ? - Она просто сливает изменения - вообще все, НО текущая ветка (напрмиер транк) - остается неизменной  
Или, эта команда просто _все загружает из удаленного репо, но НЕ мерджит_.  
  
`git pull`, напротив работает как `fetch + merge`, и именно поэтому в предыдущем простом случае при пулле изменений в ветку feature/task-123 (находясь там), нам пришлось разруливать мердж-конфликт (напомню, на тот момент времени он у нас был)    
Иными словами, `git pull` универсальней и работает по принципу 2 в 1  
Есть нюанс:  
Если же мы сделаем просто `git pull` (без указания ветки), не факт что изменения ВСЕ будут слиты
![img_13.png](src%2Fimages%2Fimg_13.png)  
-- как например видно отсюда (ветку с какой сливается можно проверить `git branch -vv`)

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
про первую команду детальней:  
`$(git merge-base trunk HEAD)` - смотри ниже на гарфик коммитов - находит общий предок для транка и новой ветки (123-нью)  
после чего, как порядочная функция, позвращает хеш этого коммита в функцию выше (она составная, если угодно)  
`git reset --soft 48ece5c` - получается как-то так, в итоге:
#### Посмотреть историю коммитов в виде графика
```bash
git log --graph --oneline --decorate
```
![img_21.png](src%2Fimages%2Fimg_21.png)  
выглядит так, например общий предок для веток - транка и feature/123-new - это:  
`*   48ece5c Merge pull request #1 from timk01/feature/TASK-123`
-- дерево было таким...

`git reset --soft $(git merge-base trunk HEAD)` 
в итоге, после команды выше, стало таким:  
![img_22.png](src%2Fimages%2Fimg_22.png)  
![img_23.png](src%2Fimages%2Fimg_23.png)  
-- в итоге, так (пришлось решать мердж-конфликт)  
![img.png](img.png)

_Отступление про хардресет:_
есть также `git reset --hard <commit>`  
-- так вот, использовать можно, но с осторожностью;  
работает по принципу ресет софт выше, но удаляет коммиты после нашего  

#### Пушим свои изменения  
```bash
git push --force-with-lease
```
Важно: я ранее делал просто `git push... ` и возникал конфликт.  
Почему после сквоша он вообще мог появляться ?  
![img_24.png](src%2Fimages%2Fimg_24.png)
-- а все потому что в удаленном репо были изменения и при пуше их (новых изменений) туда козникал конфликт  

`git push --force`  
при этой команде гиту абсолютно все равно на любые изменения в ветке куда мы пушим (были они иил нет - неважно) **- он их перезатрет нашим пушем**    
соответственно, и крайне опасно это делать с мейн иил важными ветками или если мы с ними работаем не одни (мы можем легко НЕ знать об этих изменениях!)  
пушфорс - полная перезапись указанной ветки; если угодно - это пуш "на живую"

`git push --force-with-lease`  
-- при этой же команде будет выполнена проверка:  
если были изменения в ветке после `fetch` или `pull` - он не даст сделать пуш
таким образом это как "собака на поводке" (отсюда и лиз) - мы сможем дотянуться только до своих пушей и не до каких других  
или, это пуш с предохранителем, который триггернется если что-то было сделано еще

## Полезные команды
_часть уже была, думаю не все нужно демонстрировать_

#### Показать список веток.
```bash
git branch -a
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
_частично демонстрировалось мной в начале для удаленной ветки, для текущей - то ж самое_
можно также откатываться на коммиты назад

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
