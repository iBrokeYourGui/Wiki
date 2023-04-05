# Руссификация всякого 

## Ubuntu 20 server 
Чтобы читать логи на русском нужно настроить локаль и вывод в консоль.  

```sudo dpkg-reconfigure locales```  
    выбираем русские локали  
    - ru_RU.cp1251  
    - ru_RU.iso88595  
    - ru_RU.koi8r  
    - ru_RU.utf8  

```sudo dpkg-reconfigure console-setup```  
    - UTF-8  
    - Cyrillic KOI8-R and KOI8-U  
    - Fixed  
    - 8x16  

Опицонально настройка таймзоны ```sudo dpkg-reconfigure tzdata```  
И перегружаемся.  
DONE 
