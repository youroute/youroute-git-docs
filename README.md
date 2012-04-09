Конфигурация
============

    git config --global user.name "Your Name Comes Here"
    git config --global user.email you@yourdomain.example.com

Для github рекомендуется прописать дополнительные настройки (подробнее они описаны тут http://help.github.com/mac-set-up-git/):

    git config --global github.user username
    git config --global github.token 0123456789yourf0123456789token

Теперь раскрасим вывод git в консоли:

    git config --global color.ui true

И добавим несколько полезных алиасов. Руководство в дальнейшем подразумевает, что они у вас прописаны.

    git config --global alias.a add --all -v
    git config --global alias.st status
    git config --global alias.ci commit
    git config --global alias.br branch
    git config --global alias.co checkout
    git config --global alias.df diff
    git config --global alias.lg log -p
    git config --global alias.lc "log ORIG_HEAD.. --stat --no-merges"
    looog = "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr)%Creset%Cred[%an]%Creset' --abbrev-commit --date=relative"
    git config --global alias.up "pull --rebase --stat"
    git config --global alias.edit-unmerged "!f() { git ls-files --unmerged | cut -f2 | sort -u ; }; subl `f`"

Для удобства можете скачать конфигурационный файл .gitconfig

Важно
=====

Если вы копируете репозиторий с github, то используйте ssh-ссылку. HTTP протокол с недавних пор использует SSL-шифрование и запрашивает логин/пароль для аутентификации.

Рабочий процесс
===============

1. Для начала стоит клонировать репозиторий с сервера:

    `git clone repo --recursive`

  Флаг --recursive позволяет корректно клонировать репозиторий, использующий субмодули.

2. Под каждую задачу создаем свою ветку:

    `git checkout -b branchname develop`

  Все ветки задач должны создаваться от ветки develop, поэтому ее название в конце обязательно.

3. Вносим какие-то изменения.

4. Добавляем их в индекс.

    `git add -A` или наш алиас `git a`

  Эта команда отметит все файлы, включая новые. Простой `git add` отметил бы только измененные. И не забывайте пользоваться масками. Сделав `git add Gemfile*` вы добавите в индекс как Gemfile, так и Gemfile.lock

5. Коммитим

    `git commit`

  Старайтесь коммитить код логическими блоками. Не нужно делать это слишком редко, как и слишком часто. В идеале для коммита вы должны легко придумывать описание, потому что он делает что-то одно и вполне осязаемое. И пишите для коммитов понятные, распространенные описания, желательно не зависящие от контекста в котором их придется читать (скорее всего при просмотре `git log`)

6. Теперь неплохо бы актуализировать состояние ветки

    git fetch
    git rebase master

  По идее ваша ветка теперь должна "расти" из последнего коммита в master. Выполняйте эту команду в конце каждого рабочего дня, если задача занимает много времени. Это позволит раньше выявлять ошибки совместимости с кодом других разработчиков.

7. Самое время запустить тесты и убедиться, что все работает как швейцарские часы. Или как "копейка" после ралли Париж-Дакар :(

8. Переходим в ветку develop (если тесты прошли успешно, разумеется)

  `git co develop`

9. Сливаем с вашей веткой

  `git merge --no-ff branchname`

  Флаг --no-ff говорит о том, что git должен создавать коммит даже если все может пройти гладко (т.е. по алгоритму fast-forward). В дальнейшем это упрощает откат изменений и чтение логов.

10. Отправляем все в удаленный репозиторий

  `git push origin develop`

Полезные команды
================

1. `git reset HEAD`
  Полезна если вы набрали git add, еще не закоммитили изменения и поняли, что в индекс попало что-то не то. Команда просто сбросит весь индекс.
2. `git reset HEAD file`
  Сработает как предыдущая, но сбросит только один файл из индекса.
3. `git reset --soft HEAD~1`
  Отличный вариант, если вы поняли, что последний коммит был сделан зря. Она сбросит его и вернет изменения вам в директорию. Цифра в конце указывает на то, сколько коммитов отмотать назад. Однако, в некоторых случаях все это можно сделать проще. Об этом позже.
4. `git reset --hard HEAD~1`
  Аналогична предыдущей команде, только изменения будеу безвозвратно утеряны.
5. `git reset --hard origin/master`
  Фишку жесткого ресета можно использовать, если вы хорошенько накосячили в своем локальном репозитории и не знаете как это исправить. В таком случае можете сбросить состояние до origin или любого другого репозитория.
6. `git commit --amend`
  Это к разговору о reset --soft. Выполнив это вы не создадите новый коммит, а просто отправите изменения в последний. Полезно, если сразу после коммита вы подумали: "Эй, погоди! Я не это имел в виду"
7. `git push -f`
  Если вы сделали кривой коммит, а потом еще и запушали его в origin, то дела у вас обстоят несколько уже, чем раньше. Предыдущие пункты наверняка помогут исправить состояние локального репозитория, но что делать с удаленным? Попытки сделать `git push` туда будут терпеть фиаско. Тут на помощь спешит флаг -f, который по хардкору запушает все, что хотите. Только будьте осторожны. Перед тем как делать такую штуку спросите у других членов комманды не коммитит ли кто чего. Иначе вы рискуете затереть его изменения и потом долго думать какие злые чары испортили ваш remote.
8. `git mv file newfile`
  Если вы хотите переименовать файл, то кошерно это делать с помошью mv. В этом случае git не будет думать, что вы удалили file и создали вместо него newfile.
9. `git rebase -i commit_hash`
  Эта команда поможет сменить описание коммита, поменять их местами, удалить какой-то из цепочки и т.д. Самая полезная штука для манипуляции с деревом коммитов
10. `git add -i`
  Интерактивный режим добавления. Позволяет, к примеру, часть изменений из файла засунуть в один коммит, а часть в другой (комманда patch). Это бывает крайне полезно, когда вы сделали фичу A, забыли ее закоммитить и полезли делать фичу B. Смешивать изменения в таком случае - моветон
11. `git rm --cache file`
  Удаляет файл из репозитория, но оставляет его на диске.
12. `git co --track origin/branchname`
  Эта команда создаст у вас в репозитории ветку branchname и слинкует ее с аналогичной веткой в origin. Теперь вместе с git pull будет происходить не только синхронизация origin/master -> master, но и origin/branchname -> branchname
13. `git push origin :branchname`
  Если вам нужно уничтожить ветку в удаленном репозитории, то это самый годный способ
14. `git branch -d branchname`
  А если в локальном, то так