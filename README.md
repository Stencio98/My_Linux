# dpkg error Debian 
```
dpkg: warning: 'ldconfig' not found in PATH or not executable
dpkg: warning: 'start-stop-daemon' not found in PATH or not executable
dpkg: error: 2 expected programs not found in PATH or not executable
Note: root's PATH should usually contain /usr/local/sbin, /usr/sbin and /sbin
```
che tipo di errore si tratta?


L'errore che hai riportato è legato al sistema di gestione dei pacchetti di Debian e delle sue derivate (come Ubuntu), e indica che alcuni programmi essenziali non sono stati trovati nel percorso (PATH) dell'utente root.


Descrizione dell'errore:
ldconfig è un programma utilizzato per configurare le librerie condivise e aggiornarne la cache.
start-stop-daemon è un programma utilizzato per avviare e fermare i demoni, ed è fondamentale per la gestione dei servizi.
Quando dpkg non riesce a trovare questi programmi, non può completare correttamente le operazioni richieste.


Cause comuni:
Il percorso (PATH) dell'utente root è stato modificato in modo tale da non includere le directory corrette (/usr/local/sbin, /usr/sbin, e /sbin).
I programmi in questione potrebbero non essere installati o potrebbero esserci problemi con il sistema.


Soluzione:
Assicurati che il tuo PATH contenga le directory corrette. Puoi farlo eseguendo i seguenti comandi come root:
`export PATH=$PATH:/usr/local/sbin:/usr/sbin:/sbin`
Puoi anche aggiungere questa riga al file del profilo di root (/root/.bashrc o /root/.profile) per rendere permanente la modifica.


Verifica:
Dopo aver aggiornato il PATH, puoi verificare se i comandi sono accessibili:
```
which ldconfig
which start-stop-daemon
```

Se vengono restituiti i percorsi corretti, dovresti poter utilizzare dpkg senza ulteriori problemi.
Se dopo aver provato queste soluzioni continui ad avere problemi, potrebbe essere necessario riesaminare l'installazione del sistema o ripristinare i pacchetti mancanti.

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
# Montare secondo disco (che non ha SO, rotazionale) all'avvio in automatico:
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
# Accedere dall'esterno alla rete locale con Wireguard (morrolinux):
* vedi video su you tube

# virtual box:
* se abbiamo linux su disco e abbiamo una macchina virtuale con linux installato, e vogliamo che veda in rete le cartelle condivise (per esempio samba condivisa dal linux che gira su disco) dobbiamo attivare in virtual box il bridge adapter nella sezione network, la modalità di rete "Bridge" è spesso la scelta migliore in questo caso, permettendo alla VM di apparire come un dispositivo indipendente sulla tua rete locale.







