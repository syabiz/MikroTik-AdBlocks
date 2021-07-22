# MikroTik-AdBlocks
Block Ads on Mikrotik


1. Atur router sebagai server DNS

2. Paksa permintaan DNS untuk diarahkan ke router.

Command:

/ip firewall nat
 add action=redirect chain=dstnat dst-port=53 in-interface=bridge protocol=udp

Tambahkan skrip untuk mengimpor daftar DNS satik dan penjadwal untuk memperbarui daftar satu hari.

Command:

/system script
 add name=adblock policy=\
 ftp,reboot,read,write,policy,test,password,sniff,sensitive,romon source=":\
 log warning \"starting adblock update\";\r\
 \n\
 \n\
 \n:delay 20;\r\
 \n\
 \n\
 \n:log warning \"downloading adblock\";\r\
 \n\
 \n\
 \n\
 \n\
 \n:local hostScriptUrl \"https://raw.githubusercontent.com/syabiz/MikroTik-AdBlocks/main/blockhost.txt\";\
 \n\
 \n\
 \n\
 \n\r\
 \n:local scriptName \"adblock\";\
 \n\
 \n\
 \n\
 \n\r\
 \ndo {\
 \n\
 \n /tool fetch mode=http url=\$hostScriptUrl dst-path=(\"./\".\$scriptName\
 );\
 \n\
 \n\r\
 \n:delay 20;\
 \n\
 \n\r\
 \n:if ([:len [/file find name=\$scriptName]] > 0) do={\r\
 \n\
 \n\
 \n :log warning \"removing old adblock list\";\
 \n\
 \n\r\
 \n /ip dns static remove [/ip dns static find address=127.0.0.1];\
 \n\
 \n\r\
 \n :log warning \"importing new adblock list\";\r\
 \n\
 \n\
 \n /import file-name=\$scriptName;\r\
 \n\
 \n\
 \n /file remove \$scriptName;\r\
 \n\
 \n\
 \n :log warning \"adblock list imported\";\r\
 \n\
 \n\
 \n } else={\
 \n\
 \n \r\
 \n:log warning \"adblock list not downloaded, script stopped\";\r\
 \n\
 \n\
 \n }\r\
 \n\
 \n\
 \n} on-error={\
 \n\
 \n \r\
 \n:log warning \"adblock list download FAILED\";\
 \n\
 \n\r\
 \n};"
 /system scheduler
 add interval=1d name=adblock on-event=adblock policy=\
 ftp,reboot,read,write,policy,test,password,sniff,sensitive,romon \
 start-time=startup
 add name=adblock_startup on-event=\
 ":delay 30;\r\
 \n/system script run adblock" policy=\
 ftp,reboot,read,write,policy,test,password,sniff,sensitive,romon \
 start-time=startup
