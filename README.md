# MikroTik-AdBlocks
Block Ads on Mikrotik


1. Atur router sebagai server DNS

2. Paksa permintaan DNS untuk diarahkan ke router:

/ip firewall nat
 add action=redirect chain=dstnat dst-port=53 in-interface=bridge protocol=udp

Tambahkan skrip untuk mengimpor daftar DNS satik dan penjadwal untuk memperbarui daftar satu hari:
