\# Лабораторная работа №6



\## Система контроля версий



Работу выполнила: студентка гр. 4516 Куц В. Ю.

Дата: 06.11.2026



\## Цель работы



Изучение базовых возможностей системы управления версиями, опыт работы с Git Api, опыт работы с локальными и удаленными репозиториями.



\## Ход работы



\### 1. Fork репозитория



Работа велась с аккаунтом GitHub He0wp. С помощью функции Fork создана личная копия учебного репозитория Kurtyanik/LR6. Полученный репозиторий доступен по адресу https://github.com/He0wp/LR6. На его странице видна связь с исходным проектом и наличие двух веток – master и branch1.



!\[Рисунок 1. Личная копия учебного репозитория на GitHub](screenshots/01-fork.png)



\*Рисунок 1. Личная копия учебного репозитория на GitHub.\*



\### 2. Проверка версии и настройка Git



Используется Git версии `2.56.0.windows.4`. В глобальных настройках прописаны имя автора (в формате «группа, фамилия, инициалы») и электронная почта. Отдельно выставлен параметр, отключающий экранирование кириллицы в именах файлов при выводе Git.



```bash
git --version
git config --global user.name "4516 Куц В. Ю."
git config --global user.email "Hewhe0w0@gmail.com"
git config --global core.quotepath false
git config --global --get user.name
git config --global --get user.email
```



!\[Рисунок 2. Версия Git и настройка git config](screenshots/02-config.png)



\*Рисунок 2. Версия Git и настройка git config.\*



\### 3. Клонирование удаленного репозитория



В домашнем каталоге была заведена папка GitLabs, куда по HTTPS склонирован форк He0wp/LR6. Затем осуществлён переход в LR6, проверено текущее состояние и просмотрены привязки к удалённым репозиториям.



```bash

mkdir -p \~/GitLabs

cd \~/GitLabs

git clone https://github.com/He0wp/LR6.git

cd LR6

git status

git remote -v

```



После клонирования рабочей оказалась ветка master. Незакоммиченных правок нет, а origin ссылается на личный репозиторий — и для чтения, и для записи.



!\[Рисунок 3. Клонирование репозитория](screenshots/03-clone.png)



\*Рисунок 3. Клонирование репозитория.\*



\### 4. Файл, созданный через веб-интерфейс GitHub



В ветке master средствами сайта GitHub был добавлен файл github-note.txt с текстом «файл создан через интерфейс GitHub». В удалённом репозитории образовался коммит 00af62e с сообщением Create github-note.txt. Эти изменения были подтянуты в локальную копию через pull.



```bash

git switch master

git pull --ff-only origin master

ls

cat github-note.txt

```



Слияние прошло по схеме fast-forward. После ls в каталоге появился новый файл, а cat показал его содержимое.



!\[Рисунок 4. Получение созданного на GitHub файла в локальный репозиторий](screenshots/04-pull.png)



\*Рисунок 4. Получение созданного на GitHub файла в локальный репозиторий.\*



\### 5. Работа с ветками и просмотр истории



Чтобы получить доступ к веткам исходного проекта, был подключён дополнительный remote upstream. После fetch выбрана побочная ветка branch1; её имя для удобства сохранено в переменной side. Локальная branch1 создана сразу с отслеживанием upstream/branch1.



```bash

git remote add upstream https://github.com/Kurtyanik/LR6.git

git fetch upstream

git branch -a

side="branch1"

git switch -c "$side" --track "upstream/$side"

git --no-pager log --oneline --decorate "$side"

git --no-pager log --oneline --decorate master

git --no-pager log --graph --oneline --decorate --all

```



В истории branch1 присутствует коммит 0f9f50d «Заполнил файл». В master видны коммит 921f53b «Обновление информации» и коммит 00af62e, созданный через сайт. По графу видно, что после общей части истории ветки разошлись.



!\[Рисунок 5. Просмотр истории master и branch1 и общего графа коммитов](screenshots/05-history.png)



\*Рисунок 5. Просмотр истории master и branch1 и общего графа коммитов.\*



\### 6. Анализ последних коммитов и различий



Чтобы разобраться в последних изменениях, применена команда show; для сопоставления веток – diff.



```bash

git --no-pager show --stat master

git --no-pager show master

git --no-pager show "$side"

git --no-pager diff master "$side"

```



В крайнем коммите master появился github-note.txt. В branch1 правки затронули mergefile.txt. Сопоставление веток выявило расхождения в строках этого файла, которые важно учесть при слиянии. Показанное в diff «удаление» github-note.txt — лишь следствие сравнения двух состояний, файл из рабочей папки при этом никуда не исчезает.



!\[Рисунок 6. Последние коммиты и различия между ветками](screenshots/06-last-changes.png)



\*Рисунок 6. Последние коммиты и различия между ветками.\*



\### 7. Слияние веток и разрешение конфликта



Перед объединением переключились на master и удостоверились, что рабочая папка чиста. Затем запустили слияние побочной ветки.



```bash

git switch master

git status

git merge "$side" -m "Merge side branch into master"

```



Git зафиксировал конфликт содержимого mergefile.txt и приостановил автоматическое слияние. Причина — обе ветки изменили один и тот же фрагмент файла.



!\[Рисунок 7. Сообщение Git о конфликте при слиянии](screenshots/07-conflict.png)



\*Рисунок 7. Сообщение Git о конфликте при слиянии.\*



В текстовом редакторе был открыт конфликтный фрагмент. Строки между `<<<<<<< HEAD` и `=======` относятся к master, а между `=======` и `>>>>>>> branch1` — к branch1. В основной ветке фрагмент содержит слова «очень», «важной», «информацией», в побочной — «информацией», «противоречащей», «ветке», «master».



!\[Рисунок 8. Конфликтный участок mergefile.txt в текстовом редакторе](screenshots/08-conflict-editor.png)



\*Рисунок 8. Конфликтный участок mergefile.txt в текстовом редакторе.\*



Фрагмент был приведён в порядок вручную, служебные маркеры убраны. После просмотра результата файл добавлен в индекс, затем оформлен коммит слияния.



```bash

git --no-pager diff

git diff --check

git add mergefile.txt

git diff --name-only --diff-filter=U

git commit -m "Resolve merge conflict in mergefile.txt"

git status

git --no-pager log --graph --oneline --decorate -10

```

После добавления файла проверка неразрешённых конфликтов ничего не вывела. Появился коммит 27f70b0 с сообщением Resolve merge conflict in mergefile.txt. По графу видно, что линии истории соединились. Статус working tree clean говорит о том, что слияние доведено до конца и незакоммиченных правок нет.



!\[Рисунок 9. Завершение слияния и коммит разрешения конфликта](screenshots/09-merge-done.png)



\*Рисунок 9. Завершение слияния и коммит разрешения конфликта.\*



\### 8. Удаление побочной ветки



После успешного слияния проверили текущую ветку и удалили локальную branch1.



```bash

git branch --show-current

git branch -d "$side"

git branch

```



Git вывел Deleted branch branch1 (was 0f9f50d). В перечне локальных веток осталась только master. Правки из побочной ветки никуда не пропали — они вошли в общую историю основной ветки.



!\[Рисунок 10. Удаление локальной ветки branch1 после слияния](screenshots/10-branch-deleted.png)



\*Рисунок 10. Удаление локальной ветки branch1 после слияния.\*



\### 9. Цепочка учебных коммитов



Чтобы показать последовательные фиксации, был заведён файл training.txt. На каждом шаге в него добавлялась одна строка, после чего файл попадал в индекс и фиксировался отдельным коммитом.



```bash

printf '%s\\n' '1. Изучаемые репозиторий и ветки.' > training.txt

cat training.txt

git add training.txt

git commit -m "Добавить первую заметку по практике"



printf '%s\\n' '2. Конфликт слияния улажен.' >> training.txt

git --no-pager diff

git add training.txt

git commit -m "Зафиксировать разрешение конфликта в заметке по практике"



printf '%s\\n' '3. Временная строка для демонстрации отката.' >> training.txt

git add training.txt

git commit -m "Добавить временную строку для демонстрации отката"

git --no-pager log --oneline -5

```



Первым коммитом 454ad95 файл создан с первой строкой. Второй коммит 54d422c добавил вторую строку — её добавление видно по выводу diff. Третий коммит 68b8ae5 внёс временную третью строку. В истории отображаются три отдельных учебных коммита. Предупреждения о замене LF на CRLF касаются только окончаний строк, коммиты при этом создались нормально.



!\[Рисунок 11. Создание training.txt, три учебных коммита и проверка истории](screenshots/11-commits.png)



\*Рисунок 11. Создание training.txt, три учебных коммита и проверка истории.\*



\### 10. Откат последнего коммита



Перед откатом посмотрели состояние рабочей папки и последний коммит. Затем через revert отменили правки коммита 68b8ae5, на который в тот момент указывал HEAD.



```bash

git status

git --no-pager log -1 --oneline

git revert --no-edit HEAD

cat training.txt

git --no-pager log --oneline -5

git status

```



Получен новый коммит 9a8494c с сообщением Revert "Добавить временную строку для демонстрации отката". Он убрал временную третью строку. Первоначальный коммит остался в истории, поэтому откат не переписал прежние версии. После операции рабочая папка снова чистая.



!\[Рисунок 12. Откат последнего коммита и проверка истории](screenshots/12-revert.png)



\*Рисунок 12. Откат последнего коммита и проверка истории.\*



В training.txt после отката осталось:



```text

1\. Изучаемые репозиторий и ветки.

2\. Конфликт слияния улажен.

```



\### 11. Создание ветки отчета



От текущего состояния master отведена отдельная ветка report.



```bash

git switch master

git switch -c report

git branch

```



Вывод git branch показывает master и report, причём звёздочка стоит у report — значит, переключение состоялось. Все основные операции уже входят в историю этой ветки, поскольку она создана после отката последнего учебного коммита.



!\[Рисунок 13. Создание отдельной ветки report](screenshots/13-report-branch.png)



\*Рисунок 13. Создание отдельной ветки report.\*



\### 12. Оформление отчета



Отчёт оформлен в README.md средствами Markdown: структура задана заголовками и подзаголовками, команды и содержимое файлов оформлены блоками кода. Скриншоты сложены в папку screenshots и подключены относительными ссылками. Журнал команд велся в файле commands.txt, созданном вручную ещё до начала работы: команды записывались в него по мере выполнения. Содержимое файла продублировано ниже без результатов выполнения.



\## Журнал выполненных команд



В журнал вошли команды из переданного файла на момент открытия README.md для оформления отчёта. Приглашения терминала $ убраны; сами команды, сообщения коммитов и порядок шагов сохранены. Действия по сохранению и отправке отчёта в этот снимок журнала не попали.



```bash

git --version



git config --global user.name "4516 Куц В. Ю."



git config --global user.email "Hewhe0w0@gmail.com"

git config --global core.quotepath false

git config --global --get user.name

git config --global --get user.email



mkdir -p \~/GitLabs

cd \~/GitLabs



git clone https://github.com/He0wp/LR6.git

cd LR6

git status

git remote -v



git switch master

git pull --ff-only origin master

ls

cat github-note.txt



git remote add upstream https://github.com/Kurtyanik/LR6.git

git fetch upstream

git branch -a



side="branch1"



git switch -c "$side" --track "upstream/$side"

git --no-pager log --oneline --decorate "$side"

git --no-pager log --oneline --decorate master

git --no-pager log --graph --oneline --decorate --all



git --no-pager show --stat master

git --no-pager show master

git --no-pager show "$side"

git --no-pager diff master "$side"



git switch master

git status



git merge "$side" -m "Merge side branch into master"



git status

git diff --name-only --diff-filter=U



git --no-pager diff

git diff --check



git add mergefile.txt

git diff --name-only --diff-filter=U

git commit -m "Resolve merge conflict in mergefile.txt"

git status

git --no-pager log --graph --oneline --decorate -10



git branch --show-current

git branch -d "$side"

git branch



printf '%s\\n' '1. Изучаемые репозиторий и ветки.' > training.txt

cat training.txt

git add training.txt

git commit -m "Добавить первую заметку по практике"



printf '%s\\n' '2. Конфликт слияния улажен.' >> training.txt

git --no-pager diff

git add training.txt

git commit -m "Зафиксировать разрешение конфликта в заметке по практике"



printf '%s\\n' '3. Временная строка для демонстрации отката.' >> training.txt

git add training.txt

git commit -m "Добавить временную строку для демонстрации отката"

git --no-pager log --oneline -5



git status

git --no-pager log -1 --oneline



git revert --no-edit HEAD

cat training.txt

git --no-pager log --oneline -5

git status



git switch master

git switch -c report

git branch



explorer.exe .



notepad.exe README.md

```



\## Вывод



В результате работы получен собственный форк репозитория на GitHub и его локальная копия. Настроены параметры автора, разобраны история веток и различия файлов. Ветка branch1 влита в master с ручным разрешением конфликта, после чего локальная побочная ветка удалена.



На практике подтвердилось: коммит фиксирует изменение только в локальной истории, а обмен с удалённым репозиторием выполняется отдельными командами. Для отмены изменения выбран revert — он создаёт новый коммит и не трогает прежнюю историю. Материалы отчёта собраны в ветке report: описание действий, журнал команд и скриншоты.



\## История операций



Полная история с датами и именами авторов будет добавлена после промежуточных коммитов оформления отчёта, перед его финальной фиксацией. Ниже приведён фактический вывод команды:



git --no-pager log --all --date=short --pretty=format:"%h | %ad | %an | %s"





|Хэш|Дата|Автор|Комментарий|

|-|-|-|-|

|`865b2a8` | 2026-11-06 | 4516 Куц В. Ю. | Начать отчёт по лабораторной в README|

|`9a8494c` | 2026-11-06 | 4516 Куц В. Ю. | Revert "Добавить временную строку для демонстрации отката"|

|`68b8ae5` | 2026-11-06 | 4516 Куц В. Ю. | Добавить временную строку для демонстрации отката|

|`54d422c` | 2026-11-06 | 4516 Куц В. Ю. | Зафиксировать разрешение конфликта в заметке по практике|

|`454ad95` | 2026-11-06 | 4516 Куц В. Ю. | Добавить первую заметку по практике|

|`27f70b0` | 2026-11-06 | 4516 Куц В. Ю. | Resolve merge conflict in mergefile.txt|

|`00af62e` | 2026-11-06 | He0wp | Create github-note.txt file|

|`921f53b` | 2020-11-21 | Kurtyanik | Обновление информации|

|`0f9f50d` | 2020-11-21 | Kurtyanik | Заполнил файл|

|`c08a654` | 2020-11-21 | Kurtyanik | Файл создан пустым|

|`3c6e913` | 2020-11-21 | Kurtyanik | Initial commit|

