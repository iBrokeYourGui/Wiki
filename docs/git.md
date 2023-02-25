# GIT твою мать


### Работа с удаленными репозиториями
С начала ваш братюня или вы сами (в случае работы с субственными репозиториями из хаба) генерит(те) персональные ключи в профиле
`Settings->Developer settings->personal access tokens->Fine-grained personal access tokens`
Там все просто, главное не забыть прав накинуть иначе ничего работать не будет

Далее клонируем себе интересующий репозиторий:
`git clone https://github.com/<username>/<repo_name>.git`
И задаем удаленный репозиторий:
`git remote set-url origin https://<username>:<personal_token>@github.com/<username>/<repo_name>.git`
Если все сделал правильно команды `push` и `pull` будут работьа как часы.
