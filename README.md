Небольшая шпаргалка по ```git```
===

# Содержание

*  ### [Ссылки](#urls)
*  ### [Commit (Редакция комитов)](#commit)
*  ### [Branch (Работа с ветвями)](#branch)
*  ### [Checkout (Перемещение по дереву и чекаут)](#checkout)
*  ### [Rebase (Перетасовывание дерева)](#rebase)
*  ### [Cherry-pick (выдергивание комитов)](#cherry-pick)
*  ### [Merge (Слияние ветвей)](#merge)
*  ### [Reset (Откат для локального)](#reset)
*  ### [Revert ("Откат" для remote)](#revert)
*  ### [Tag (Тег)](#tag)
*  ### [Describe (Информация о объекте)](#describe)
*  ### [Log (Информация о всем дереве)](#log)

## Работа с _remote_ репозиториями

* ### [Remote (Добавление репозитория)](#remote)
* ### [Fetch (Cкачивание)](#fetch)
* ### [Pull (Cкачивание и слияние)](#pull)
* ### [Push (Выгрузка)](#push)


<a name="urls"><h2>Ссылки</h2></a>

[Обязательно к прохождению](https://learngitbranching.js.org/?locale=ru_RU)

[Маленькая шпаргалка от GitHub](https://training.github.com/downloads/ru/github-git-cheat-sheet/)

[Хороший сайт](https://www.atlassian.com/ru/git/tutorials/what-is-version-control)

Commit
---

```
git commit
```
Создать комит.

Флаги:
  * ```-m "message"``` добавить сообщение. 
  * ```--amend``` изменить уже существующий комит. Аналогично использованию
```
  git reset --soft HEAD^
  ... do something else to come up with the right tree ...
  git commit -c ORIG_HEAD]
```

Branch
---

```
git branch <name>
```
Создать на текущем ```HEAD``` ветку. При добавлении флага ```-f``` (--force) уже существующая ветка может быть перемещена.

> ```
>git branch -f <branch_name> <название_ветки/хэш/тег>
>``` 
>Создаст ветку с именем ```<branch_name>``` на месте ```<название_ветки/хэш/тег>```.

>Для удаление ветки ```<название_ветки>``` можно использовать
>```
>git branch -d <название_ветки>
>```

>```
>git branch
>```
>Выводит список ветвей
>```
>git branch -r
>```
>Выводит список _remote_ ветвей

>Также есть способ указать ветке отслеживать удалённую ветку
>```
>git branch -u origin/main foo
>```
>Вы укажете ветке ```foo``` следить за ```origin/main```. А если вы ещё при этом находитесь на ветке ```foo```, то её можно не указывать:
>```
>git branch -u origin/main
>```

Checkout
---
```
git checkout <название_ветки/хэш/тег>
```
Переместить ```HEAD``` (Переместиться по адресу по адресу). Тут могут быть использованы модификаторы 
```~``` и ```^```.

Использование:
  * ```^``` опуститься на 1 комит ниже.
  * ```~<number>``` опуститься на ```<number>``` кол-во комитов ниже.

  >Операторы могут сочетаться. Если ветка содержит несколько родителей, то, чтобы дать понять гиту, куда мы хотим опуститься, можно использовать сочетание  ```git checkout HEAD^<parent_number>``` - переход на ```<parent_number>``` родителя.
>
>То есть:
>
>```
>A---B
>      \
>       E--F (HEAD)
>      /
>C---D
>```
>
>После ```git checkout HEAD~^2~1``` имеем:
>```
>       A---B
>            \
>             E--F
>            /
>(HEAD)C---D
>```

>Вы можете сказать любой из веток, чтобы она отслеживала ```origin/main```, и если вы так сделаете, эта ветка будет иметь такой же пункт назначения для ```push``` и ```merge``` как и локальная ветка ```main```. Это значит, что вы можете выполнить ```git push```, находясь на ветке ```totallyNotMain```, и все ваши наработки с ветки ```totallyNotMain``` будут закачены на ветку ```main``` удалённого репозитория!
>```
>git checkout -b totallyNotMain origin/main
>```
>Создаст новую ветку с именем ```totallyNotMain``` и укажет ей следить За ```origin/main```.

Rebase
---
```
git rebase <куда_поставить>
```
Перемещение ```HEAD``` на место ```<куда поставить>```

**Отмена** действия ```rebase```
```
git rebase --abort
```

Использование:
  * Также можно использовать в виде ```git rebase <куда_поставить> <что_поставить>```

>Пример:
>```
>          A---B---C topic (HEAD)
>         /
>    D---E---F---G master
>```
> После использования
>```git rebase master``` или ```git rebase master topic``` получаем:
>```
>                  A'--B'--C' topic
>                 /
>    D---E---F---G master
>```

  * Так же есть способ использования с флагом ```-i```: 
  ```
  git rebase -i <можно_изменить_комиты_от_данного_до_HEAD>
  ```
  >
  >Откроется **Vim**, где вы увидите это ```pick <хэш> <Первая_строка_комита>```.
  >```
  >pick deadbee <Первая_строка_комита>
  >pick fa1afe1 <Первая_строка_следующего_комита>
  >...
  >```
  >Меняя порядок строк, мы будем соответственно менять порядок коммитов.
  >Так же мы можем изменить ```pick``` на ```drop``` (или просто удалить строчку), если хотим удалить комит. Возможна редакция комитов ```edit```

Cherry-pick
---
```
git cherry-pick <комит1> <комит2> <комит3> ...
```
Забирает выбранные комиты ```<комит1> <комит2> <комит3> ...``` и вставляет после ```HEAD``` в такой же порядке (1 2 3...).

Merge
---
```
git merge <с_чем>
```
Слияние **текущей** ветви с ```<с_чем>```. То есть создание ветви из двух родителей.

>### Пример:
>
>Текущая ветка ```master```
>```
>     A---B---C topic
>    /
>   D---E---F---G master (мы тут)
>```
>После введения команды ```git merge topic``` получаем:
>```
>     A---B---C topic
>    /         \
>   /           H master (теперь мы тут)
>  /           /
>D---E---F---G
>```

Reset
---
```
git reset <с_какого_момента>
```
Отменяет изменения, перенося ссылку на ветку назад на ```<с_какого_момента>```.

Revert
---
```
git reverse <какой_комит>
```
Создает комит, с изменениями противоположными ```<какой_комит>```

Tag
---
```
git tag <имя_тега> <комит>
```
Присваивает тег ```<имя_тега>``` комиту ```<комит>```

Describe
---
```
git describe <комит>
```
Выдает информацию о ближайшем **теге** ```<ближайший_тег>```, **расстояние** ```<расстояние_до_тега>``` до него и **хэш текушего комита** ```<хэш_комита>```
```
<ближайший_тег>_<расстояние_до_тега>_g<хэш_комита>
```

Log
---
```
git log
```
Выдает информацию о всех комитах.

---

Remote
---
```
git remote add <удалённый_репозиторий> <ссылка_удаленного_репозитория>
```
Добавили удаленный репозиторий ```<удалённый_репозиторий>```, полученный по ссылке ```<ссылка_удаленного_репозитория>```

Fetch
---
```
git fetch <удалённый_репозиторий>
```
Скачивает репозиторий ```<удалённый_репозиторий>``` и все его ветви. "Гит адрес" будет такой ```<удалённый_репозиторий>/<его_ветка>```

Так же можно скачать отдельную ветку из удаленного репозитория:
```
git fetch <удалённый_репозиторий> <его_ветка>
```
> Можно создать локальную ветку с копией удаленной версии (находясь в удаленной ветке ```HEAD```-ом)
>```
>git checkout -b remote_master_local
>```

>Если указать пункт назначения в команде ```git fetch```:
>```
>git fetch origin foo
>```
>**Git** отправится в ветку ```foo``` на удалённом репозитории, соберёт с собой все комиты, которые не присутствуют локально, и затем поместит их в локальную ветку под названием ```origin/foo```.
>
>Если вы уверены в том, что хотите закачать комиты прямиком в вашу локальную ветку, тогда да, вы можете явно указать источник и получатель через двоеточние. Вы можете воспользоваться таким приёмом лишь для ветки, на которой вы не находитесь в настоящий момент ```checkout```.
>```
>git fetch origin <источник>:<получатель>
>```
>Теперь у нас ```<источник>``` - это место на удалённом репозитории, а ```<получатель> ```- место в локальном репозитории, в который следует помещать комиты.
>
>Так же можно создать новую ветку, которой не было изначально ```<новая__ветка>```
>```
>git fetch <удалённый_репозиторий> <источник>:<новая__ветка>
>```
>Если использовать пустой ```<источник>``` то будет создана новая ветка с именем ```<получатель>``` в локале.


Pull
---
```
git pull <удалённый_репозиторий> <его_ветка(опционально)>
```
Fetch + Merge удаленного репозитория ```<удалённый_репозиторий>``` с его веткой ```<его_ветка>```(опционально)

Так же возможно использование:
```
git checkout <название_ветки>
git pull <удалённый_репозиторий>
```
Запулена будет только ветка ```<название_ветки>```
>```
>git pull origin <источник>:<получатель>
>```
>то же, что:
>```
>git fetch origin <источник>:<получатель>
>git merge <получатель>
>```
>Как видно, ```git pull``` используется, чтобы за одну команду выполнить ```fetch``` +``` merge```.

>Если в удаленном репозитории были проведены изменения, то просто так ничего не запулится, для этого нужно скачать изменения с сервера, замерджить их  с вашими и только потом загрузить их на сервер, это и делает (скачивает и мерджит)
>```
>git pull --rebase
>```

Push
---
```
git push <удалённый_репозиторий> <ветка>
```
Выгрузка в удаленный репозиторий ```<удалённый_репозиторий>``` ветки ```ветка>```

>```
>git push <удалённый_репозиторий> --all
>
>```
>Публикация всех локальных веток в указанном удаленном репозитории.

>Так же возможно использование
>```
>git push <удалённый_репозиторий> <источник>:<получатель>
>```
>Таким образом наш локальный ```<источник> ```будет передан в _remote_ ```<получатель>``` (Из одной ветки кладем в другую). 
>
>```<источник>``` в данном случае просто местоположение, т. е. возможно использование ```^``` и ```~```
>
>Так же можно создать новую ветку, которой не было изначально ```<новая__ветка>```
>```
>git push <удалённый_репозиторий> <источник>:<новая__ветка>
>```
>Если использовать пустой ```<источник>``` то будет создана новая ветка с именем ```<получатель>``` в _remote_.
