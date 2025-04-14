# Samba
* installare samba:
```
sudo apt install samba
```

* creare cartella condivisa:
```
mkdir -p ~/shared_folder
chmod 777 ~/shared_folder
```

* per modificare la configurazione di samba:
```
sudo nano /etc/samba/smb.conf
```

* aggiungere condivisione cartella "shared_folder":
```

[shared_folder]
## lasciamo due spazi dal margine sinistro
  comment = samba linux
  path = /home/user_name/shared_folder
  read only = no
  browsable = yes

```

* riavvio servizio samba:
```
sudo service smbd restart
```
* configuratzione firewall (se lo abbiamo attivo):
```
sudo ufw allow samba
```

* accesso alla condivisione della rete: su Windows possiamo digitare l'indirizzo IP del computer Linux nella barra degli indirizzi di Esplora file

* se non ci si riesce ad accedere, farlo con user e password:
```
sudo smbpasswd -a user_name
```
* per essere sicuri parta all'avvio:
```
sudo systemctl enable smbd
sudo systemctl enable nmbd
```
```
sudo systemctl start smbd
sudo systemctl start nmbd
```
__Montare secondo disco (che non ha SO, rotazionale) all'avvio in automatico__
* usiamo il seguente comando per identificare i diski e i loro UUID
```
:$ sudo blkid
/dev/sda1: UUID="XXXX-XXXX" TYPE="ext4"
/dev/sdb1: UUID="YYYY-YYYY" TYPE="ext4"
```
* andiamo a modificare il file:
```
sudo nano /etc/fstab
```
che contiene le informazioni necessarie al montaggio delle periferiche di memorizzazione del sistema, aggiungiamo quindi la riga:
```
UUID=YYYY-YYYY /mnt/dati ext4 defaults 0 2
```
al fondo del file, sostituendo UUID con l'UUID del disco, /mnt/dati con il punto di montaggio desiderato (crea prima la directory se non esiste), e ext4 con il tipo di file system del disco, se necessario, Se non siamo sicuro del tipo di file system, possiamo usare `auto` come opzione.g

* creiamo il punto di montaggio:
```
sudo mkdir -p /mnt/dati
```
* testiamo il montaggio con il seguente comando che prova a montare tutti i file system definiti in `/etc/fstab`. Se non ci sono errori, il disco verrà montato correttamente:
```
sudo mount -a
```
* usiamo per `df` per vedere se il disco è montato correttamente:
```
df -h
```
* se vogliamo che `samba` punti al nuovo punto di montaggio (se samba non è ancora collegato al disco oppure il punto di montaggio è diventato diverso dal precedente e quindi samba "non trova" la vecchia cartella condivisa):
```
[Dati]
  path = /mnt/dati
  available = yes
  valid users = tuo_utente
  read only = no
  browsable = yes
  public = yes
  writable = yes
```
* nel caso, riavviamo `samba`:
```
sudo systemctl restart smbd
```
__virtual box__
* se abbiamo linux su disco e abbiamo una macchina virtuale con linux installato, e vogliamo che veda in rete le cartelle condivise (per esempio samba condivisa dal linux che gira su disco) dobbiamo attivare in virtual box il bridge adapter nella sezione network, la modalità di rete "Bridge" è spesso la scelta migliore in questo caso, permettendo alla VM di apparire come un dispositivo indipendente sulla tua rete locale.
