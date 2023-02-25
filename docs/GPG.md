# GPG
№ Сгенерить ключи
gpg --full-gen-key

№ Создать конфиг для работы с ключами
echo "keyid=format 0xlong                                                                                              INT ✘
throw-keyids
no-emit-version
no-comments" > ~/.gnupg/gpg.conf

# Зашифровать файл
gpg -e -a -r <key_name> file.txt
№ Расшифровать файл
gpg -d -o file.txt file.txt.asc
