# Лабораторная работа — Вариант 8
## Работа с git reflog для восстановления потерянных коммитов

Данный репозиторий содержит результаты выполнения лабораторной работы по Git, посвящённой работе с ветвлением, слияниями, разрешением конфликтов и, главным образом, восстановлению коммитов с помощью `git reflog`.

## Основные команды, используемые в работе

### Работа с ветками и слияние
```bash
# Создание и переключение на новую ветку
git checkout -b dev

# Squash-слияние (объединение всех коммитов ветки в один)
git merge --squash dev
git commit -m "Squash merge dev"

# Обычное слияние
git merge feature-merge

# Перебазирование (rebase)
git rebase main
```
## Разрешение конфликтов
```bash
# После возникновения конфликта смотрим статус
git status

# Редактируем конфликтный файл вручную, затем:
git add <файл>
git commit -m "Resolve conflict"
```
## Перенос коммитов (cherry-pick)
```bash
# Найти хэш нужного коммита
git log origin/dev2 --oneline
# Перенести коммит в текущую ветку
git cherry-pick 78b97ca
Создание и применение патчей
bash
# Создать патч из последних 3 коммитов
git format-patch -3 --stdout > my_patches.patch
# Применить патч (без автоматического коммита)
git apply my_patches.patch
# Добавить изменения и закоммитить
git add .
git commit -m "Apply patches from dev3"
```
## Интерактивный rebase
```bash
# Объединить или изменить последние 3 коммита
git rebase -i HEAD~3
```
## Работа с git reflog (восстановление потерянных коммитов)
```bash
# Показать журнал всех действий
git reflog
# Удалить последний коммит (имитация потери)
git reset --hard HEAD~1
# Найти хэш потерянного коммита в reflog (например, cfdf4aa)
# Восстановить коммит, создав от него новую ветку
git checkout -b recovered-branch cfdf4aa
# Вернуться в main и слить восстановленную ветку
git checkout main
git merge recovered-branch
# Убедиться, что коммит восстановлен
git log --oneline -5
```
## Структура репозитория
```
person1/ — рабочая директория первого разработчика
person2/ — рабочая директория второго разработчика
person3/ — рабочая директория третьего разработчика
Файлы: file1.txt, file2.txt, file3.txt, config.txt, f1.txt, f2.txt, f3.txt, merge.txt, rebase.txt, c1.txt, c2.txt, c3.txt, test.txt
```
## Ключевые выводы
git reflog — журнал всех перемещений HEAD. Хранит коммиты даже после git reset --hard.
Восстановление коммита — найти хэш в reflog, создать ветку от этого хэша, слить с нужной веткой.
Squash merge — объединяет несколько коммитов в один, делая историю чище.
Cherry-pick — переносит конкретный коммит из одной ветки в другую.
Патчи — удобный способ передавать изменения между репозиториями без прямого доступа.

## Пример успешного восстановления коммита через reflog
```bash
$ git reflog
cfdf4aa HEAD@{0}: commit: Apply patches from dev3
ae88733 HEAD@{1}: commit: text test dev2
fb447e5 HEAD@{2}: commit: resolve conflict

$ git reset --hard HEAD~1
HEAD is now at ae88733 text test dev2

$ git reflog
ae88733 HEAD@{0}: reset: moving to HEAD~1
cfdf4aa HEAD@{1}: commit: Apply patches from dev3   # потерянный коммит

$ git checkout -b recovered-branch cfdf4aa
Switched to a new branch 'recovered-branch'

$ git checkout main

$ git merge recovered-branch
Updating ae88733..cfdf4aa
Fast-forward
 person1/c1.txt | 1 +
 person1/c2.txt | 1 +
 person1/c3.txt | 1 +
 3 files changed, 3 insertions(+)

$ git log --oneline -3
cfdf4aa Apply patches from dev3
ae88733 text test dev2
fb447e5 resolve conflict
Коммит успешно восстановлен.
```
## Основная теория
Что такое git reflog?
Журнал всех действий (коммиты, переключения, сбросы) в локальном репозитории.

Чем отличается git log от git reflog?
git log показывает только коммиты, достижимые из текущей ветки. git reflog — все действия, включая потерянные коммиты.

Как восстановить коммит после git reset --hard?
Найти хэш в reflog, создать ветку от него, затем слить.

Как долго хранятся записи в reflog?
90 дней для достижимых коммитов, 30 дней для недостижимых.

Что такое HEAD@{2}?
Состояние HEAD два действия назад.

Какая команда создаёт патч из трёх последних коммитов?
git format-patch -3 --stdout > patches.patch

## Заключение
Лабораторная работа по варианту 8 полностью выполнена. Получены навыки работы с ветками, слияниями, разрешением конфликтов, cherry-pick, rebase, патчами и, что самое важное, восстановлением потерянных коммитов через git reflog. Эти знания являются критически важными для повседневной работы разработчика.
