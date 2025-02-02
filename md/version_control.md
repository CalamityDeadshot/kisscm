\newpage
# Системы контроля версий

## О системах контроля версий

Предположим, программист, незнакомый с инструментами контроля версий, хочет внести новое, экспериментальное изменение в свою программу. Наш программист сохраняет текущее состояние программного проекта в отдельном каталоге и далее занимается нововведениями в своем коде. В какой-то момент оказывается, что экспериментальная идея оказалась неудачной и программист восстанавливает состояние проекта из ранее сохраненного каталога. Такой способ управления версиями проекта требует большой дисциплины и ручного труда. Особенно трудоемкой работа с версиями проекта становится в условиях коллективной разработки. Именно эту работу и призвана автоматизировать система контроля версий.

**Система контроля версий** (СКВ, англ. Version Control Systems, VCS) – основной инструмент конфигурационного управления, позволяющий управлять изменениями (версиями) в файлах или иных наборах данных.

**Коммит** (англ. commit) – фиксация факта изменений в СКВ.

**Репозиторий** (англ. repository, repo) – место хранения данных проекта, управляемого СКВ.

**Ветка** (англ. branch) – отдельная копия части репозитория, в которую можно вносить изменения, не влияющие на другие ветки.

Различают следующие типы систем контроля версий (СКВ):

* локальные системы;
* централизованные системы;
* распределенные системы.

**Локальные СКВ** (англ. local VCS) относятся к самым первым системам контроля версий, которые появились еще в начале 70-х. Локальность означает, что история изменений хранится на компьютере пользователя. В локальных СКВ файлы хранятся в виде патчей – изменений между соседними версиями файла (см. утилиту diff в UNIX), что позволяет экономить дисковое пространство. В коллективном режиме пользователи обмениваются между собой (обычно по электронной почте) патчами. Очевидно, что такой подход к коллективной разработке нельзя назвать удобным.

**Централизованные СКВ** (англ. centralized VCS) используют клиент-серверную архитектуру. СКВ и связанный с ней репозиторий проекта теперь находятся на сервере. Каждый пользователь на своем локальном компьютере имеет только ту часть общего репозитория, с который непосредственно работает. Такой подход упрощает коллективную разработку, однако проблемы с доступом к серверу СКВ могут затруднить работу всего коллектива.

**Распределенные СКВ** (англ. dsistributed VCS) отличаются использованием полной копии проекта на каждом из компьютеров пользователя, что обеспечивает лучшую сохранность проекта, чем в случае централизованных СКВ. В распределенных СКВ могут использоваться различные схемы взаимодействия между удаленными репозиториями и, в частности, может моделироваться работа по клиент-серверной модели.

При совместной работе над одними и теми же файлами неизбежно возникают конфликты доступа к данным. В СКВ используются следующие способы разрешения конфликтов:

* блокировка доступа к файлу первым пользователем, который к нему обратился;
* использование локальных копий файла у каждого пользователя с последующим слиянием общих результатов работы в автоматическом или ручном режиме.

В @tbl:table1 представлены три поколения СКВ.

: Поколения систем контроля версий {#tbl:table1}

| Поколение | Модель взаимодействия | Единица операции | Примеры |
| --- | ------ | ---- | ---- |
| 1 | Локальная | Файл | SCCS, RCS |
| 2 | Централизованная | Файл / множество файлов |CVS, SourceSafe, Subversion, Team Foundation Server |
| 3 | Распределенная | Множество файлов | Bazaar, Git, Mercurial, Fossil |

История развития СКВ показана на @fig:git6.

![История развития систем контроля версий](git6.svg){#fig:git6}

## Система контроля версий Git

Git @git является децентрализованной СКВ. Разработана эта система была Л. Торвальдсом в 2005 году для нужд управления версиями ядра ОС Linux. Сегодня Git является самой популярной СКВ. Работу с Git не назовешь простой и многие пользователи критикуют эту систему за неудобный интерфейс командной строки. Тем не менее, основные архитектурные решения в Git являются изящными и логичными, но для того, чтобы их оценить, необходимо узнать, как работает Git изнутри.


### Простейшие команды git

Рассмотрим самые распространенные команды Git.

Создание Git-репозитория в текущем каталоге:

```default
~# mkdir my_repo
~# cd my_repo
~/my_repo# git init .
Initialized empty Git repository in /root/my_repo/.git/
```

Состояние git-репозитория:

```default
~/my_repo# git status
On branch master

No commits yet

nothing to commit (create/copy files and use "git add" to track)
```

В данном случае Git сообщает очевидное – в проекте еще не было коммитов.

Создадим теперь первый файл в репозитории:

```default
~/my_repo# echo "# Some text" > readme.md
root@DESKTOP-OI5FV17:~/my_repo# git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        readme.md

nothing added to commit but untracked files present (use "git add" to track)
```

Теперь информация о статусе репозитория изменилась – появился неотслеживаемый файл readme.md.

В Git файлы могут находиться в следующих состояниях:

* неотслеживаемые файлы (untracked) – файлы, которые Git не учитывает в своей работе,
* измененные файлы (modified) – отслеживаемые файлы, содержимое которых было изменено, но не добавлено в область индексирования;
* индексированные файлы (staged) – измененные файлы, добавленные в область индексирования;
* зафиксированные файлы (commited) – файлы, добавленные в коммит.

Таким образом, при создании нового коммита сначала нужно добавить выбранные файлы в специальную промежуточную зону – область индексирования.

Проиндексировать файлы можно с помощью команды Git add:

```default
~/my_repo# git add readme.md
~/my_repo# git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   readme.md
```

После добавления всех необходимых файлов в зону индекса (это можно сделать одной командой: `git add .`) создается коммит, но если в Git еще не заданы данные пользователя, то необходимо сначала их указать:

```default
~/my_repo# git config --local user.name "Peter"
~/my_repo# git config --local user.email "peter@example.com"
~/my_repo# git commit -m "first commit"
[master (root-commit) 3ba9fa7] first commit
 1 file changed, 1 insertion(+)
 create mode 100644 readme.md
```

Теперь первая версия проекта зафиксирована. Информацию о коммитах выдает следующая команда:

```default
~/my_repo# git log
commit 3ba9fa7980a4ba36086e66389b1ef95cbbf317e2 (HEAD -> master)
Author: Peter <peter@example.com>
Date:   Tue Nov 16 17:08:47 2021 +0300

    first commit
```

Обратите внимание на длинную последовательность `3ba9fa...`. Это хеш-значение коммита, определяющее текущую версию репозитория. Текущая ветка проекта является главной и традиционно называется `master` или `main`.

Работу с версиями в Git можно изобразить в виде графа коммитов, см. @fig:git1.

![Состояние репозитория после нескольких коммитов](git1.svg){#fig:git1}

### Ветвление в Git
Традиционно ветвление в СКВ позволяет разделять проект на независимые сущности (ветки), где изменения в любой ветке не затрагивают все остальные ветки. Это крайне полезно в условиях коллективной разработки, когда программисты одновременно работают над разными частями программы. Представим ситуацию, когда есть задача по разработке некоего объемного функционала, но вместе с тем необходимо параллельно вносить другие, мелкие изменения в проект, не затрагивая новый функционал. Как раз в таких ситуациях используется ветвление: под новые модули создается отдельная ветвь, и вся разработка ведется в ней. 

Также ветки создаются для для отдельных фаз разработки проекта и для его предвыпускной (предрелизной, англ. pre-release) версий, в которых ведется устранение ошибок.

Расмотрим на примерах ветвление в Git.

Новая ветка создается с помощью команды `git branch имя`. Переключиться на ветку можно с помощью `git checkout имя`. Так как создание ветки и переключение на нее - зачастую следующие друг за другом операции, их можно выполнить одной командой `git checkout -b имя`.

Предположим, работа над репозиторием my_repo развивалась следующим образом:

```default
git branch tests
git add ...
git commit -m "..."
git checkout tests
git add ...
git commit -m "..."
git add ...
git commit -m "..."
```

На @fig:git2 показано новое состояние графа коммитов репозитория.

![Работа с дополнительной веткой](git2.svg){#fig:git2}

В какой-то момент ветки сливаются (merge):

```default
git checkout master
git merge tests
```

На @fig:git3 показан результат этого слияния. Был создан новый коммит, объединяющий в себе изменения из обеих веток.

![Слияние веток](git3.svg){#fig:git3}

Еще одним способом объединения веток является перебазирование, осуществляемой командой `git rebase`:

```default
git checkout master
git rebase tests
```

На @fig:git4 показан результат перебазирования ветки tests на master. Здесь изменения, созданные в tests, были применены поверх master. В результате получена линейная история коммитов, которую, зачастую, изучать проще, чем результат, полученный с помощью merge.

![Перебазирование веток](git4.svg){#fig:git4}

Так как ветки master и tests указывают на один и тот же коммит, последняя больше не нужна, и ее можно удалить командой `git branch -d tests`

## Git изнутри

Все служебную информацию о репозитории Git хранит в подкаталоге .git. Основой Git является таблица объектов, адресуемая по ключам – хеш-значениям этих объектов. Такая схема хранения позволяет автоматически задавать уникальную версию для каждого файла (эта версия определяется ключом в таблице, то есть хеш-значением содержимого файла), а также дает возможность избежать дублирования файлов с одинаковым содержимым.

В Git используются следующие типы объектов:

* blob (binary large object) – содержимое файлов репозитория,
* дерево – текущее состояние или снимок файловой иерархии репозитория,
* коммит – информация о коммите.

На @fig:git5 показано более детальное состояние репозитория для примера с перебазированием из предыдущего раздела, но без удаления ветки tests.

![Взаимосвязи объектов в репозитории](git5.svg){#fig:git5}

Попробуем найти в нашем тестовом репозитории my_repo (в его состоянии на момент первого коммита) информацию о хеш-значениях веток:

```default
# cd .git
~/my_repo/.git# ls
COMMIT_EDITMSG  HEAD  branches  config  description  hooks  index  info  logs  objects  refs
~/my_repo/.git# cd refs
~/my_repo/.git/refs# ls
heads  tags
~/my_repo/.git/refs# cd heads
~/my_repo/.git/refs/heads# ls
master
~/my_repo/.git/refs/heads# cat master
3ba9fa7980a4ba36086e66389b1ef95cbbf317e2
```

Зная хеш-значение объекта master можно попробовать найти его в таблице объектов:

```default
~/my_repo/.git/refs/heads# cd ..
~/my_repo/.git/refs# cd ..
~/my_repo/.git# cd objects/
~/.git/objects# ls
07  3b  7d  info  pack
~/my_repo/.git/objects# cd 3b
~/my_repo/.git/objects/3b# ls
a9fa7980a4ba36086e66389b1ef95cbbf317e2
```

Подкаталоги с числами в objects указывают на начальную часть хеш-значения объекта, сам же файл объекта можно найти соответствующего подкаталога.

Файлы, содержащие объекты внутри objects, хранятся в двоичном формате. Для отображения информации об объекте по его хеш-значению можно использовать следующую команду:

```default
~/my_repo# git cat-file -p 3ba9fa7980a4ba36086e66389b1ef95cbbf317e2
tree 074f8b59918b080288259854fcf875a6b8e543fe
author Peter <peter@example.com> 1637071727 +0300
committer Peter <peter@example.com> 1637071727 +0300

first commit
```

Был выдан объект коммита. Объекты этого типа включают в себя:

* хеш-значение связанного с коммитом объекта дерева;
* хеш-значения родителей коммита (в рассматриваемом случае коммит единственный, поэтому информация о его родителях не показана);
* метаданные, в том числе указание авторства и текст коммита.

Попробуем теперь изучить объект дерева по его полученному хеш-значению:

```default
~/my_repo# git cat-file -p 074f8b59918b080288259854fcf875a6b8e543fe
100644 blob 7dfce3922d94e459d1545a9fc568be0369eaa973    readme.md
```

В нашем случае файловая иерархия состоит из всего одного файла. Объекты типа дерева включают в себя:

* хеш-значения blob-объектов;
* хеш-значения объектов деревьев;
* указание прав доступа к файлам и каталогам.

Blob-объект для readme.md можно изучить аналогичным образом.

Обратите внимание, что в Git хеш-значение единственного коммита характеризует не только репозиторий на момент совершения коммита, но и всю предшествующую над ним работу. Это достигается благодаря использованию в Git иерархии хеш-значений («хеш-значения от хеш-значений»). Благодаря такой организации данных любые внесенные искажения в репозиторий или в одну из его предыдущих версий могут быть немедленно выявлены. 

