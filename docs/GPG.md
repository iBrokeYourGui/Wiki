# GPG
### Сгенерить ключи
`gpg --full-gen-key`  
Дальше по сценарию.  
Никакой особой специфики кроме длины ключа.  
Если шифровать 10тки и 100тни гигов, то лучше размерность поставить 2048.  
Для личного пользования и всяких мелочей 4096.  

### Создать конфиг для работы с ключами
`echo "keyid=format 0xlong                                                                                              INT ✘
throw-keyids
no-emit-version
no-comments" > ~/.gnupg/gpg.conf`  

### Зашифровать файл
`gpg -e -a -r <key_name> file.txt`  
### Расшифровать файл
`gpg -d -o file.txt file.txt.asc`  
