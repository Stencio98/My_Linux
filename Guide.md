# CHE TIPO DI ARCHITETTURA HO?
```
uname -m
# oppure anche il comando (più chiaro) 
dpkg --print-architecture 
```
* x86_64 --> architettura a 64 bit per CPU intel/AMD
* i686 --> arch. a 32 bit per CPU intel/AMD
* armv7 --> 32 bit per CPU ARM
* aarch64 --> 64 bit per CPU ARM

# SAMBA
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
# MONTARE SECONDO DISCO (HDD CHE USO COME ARCHIVIO) ALL'AVVIO IN AUTOMATICO
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
# VIRTUAL BOX
* se abbiamo linux su disco e abbiamo una macchina virtuale con linux installato, e vogliamo che veda in rete le cartelle condivise (per esempio samba condivisa dal linux che gira su disco) dobbiamo attivare in virtual box il bridge adapter nella sezione network, la modalità di rete "Bridge" è spesso la scelta migliore in questo caso, permettendo alla VM di apparire come un dispositivo indipendente sulla tua rete locale.

# VIRTUAL BOX DEBIAN ERROR
```
There were problems setting up VirtualBox.  To re-start the set-up process, run
  /sbin/vboxconfig
as root.  If your system is using EFI Secure Boot you may need to sign the
kernel modules (vboxdrv, vboxnetflt, vboxnetadp, vboxpci) before you can load
them. Please see your Linux system's documentation for more information.
Processing triggers for libc-bin (2.36-9+deb12u10) ...
Processing triggers for gnome-menus (3.36.0-1.1) ...
Processing triggers for desktop-file-utils (0.26-1) ...
Processing triggers for mailcap (3.70+nmu1) ...
Processing triggers for hicolor-icon-theme (0.17-2) ...
Processing triggers for shared-mime-info (2.2-1) ...

```
* si risolve con:
```
sudo apt install linux-headers-$(uname -r)
sudo /sbin/vboxconfig
```

# DPKG ERROR DEBIAN 
```
dpkg: warning: 'ldconfig' not found in PATH or not executable
dpkg: warning: 'start-stop-daemon' not found in PATH or not executable
dpkg: error: 2 expected programs not found in PATH or not executable
Note: root's PATH should usually contain /usr/local/sbin, /usr/sbin and /sbin
```
* di che tipo di errore si tratta?

* L'errore che hai riportato è legato al sistema di gestione dei pacchetti di Debian e delle sue derivate (come Ubuntu), e indica che alcuni programmi essenziali non sono stati trovati nel percorso (PATH) dell'utente root.

* Descrizione dell'errore:
ldconfig è un programma utilizzato per configurare le librerie condivise e aggiornarne la cache.
start-stop-daemon è un programma utilizzato per avviare e fermare i demoni, ed è fondamentale per la gestione dei servizi.
Quando dpkg non riesce a trovare questi programmi, non può completare correttamente le operazioni richieste.

* Cause comuni:
Il percorso (PATH) dell'utente root è stato modificato in modo tale da non includere le directory corrette (/usr/local/sbin, /usr/sbin, e /sbin).
I programmi in questione potrebbero non essere installati o potrebbero esserci problemi con il sistema.

* Soluzione:
Assicurati che il tuo PATH contenga le directory corrette. Puoi farlo eseguendo i seguenti comandi come root:
```
export PATH=$PATH:/usr/local/sbin:/usr/sbin:/sbin
```
Puoi anche aggiungere questa riga al file del profilo di root (/root/.bashrc o /root/.profile) per rendere permanente la modifica.

* Verifica:
Dopo aver aggiornato il PATH, puoi verificare se i comandi sono accessibili:
```
which ldconfig
which start-stop-daemon
```

* Se vengono restituiti i percorsi corretti, dovresti poter utilizzare dpkg senza ulteriori problemi.
Se dopo aver provato queste soluzioni continui ad avere problemi, potrebbe essere necessario riesaminare l'installazione del sistema o ripristinare i pacchetti mancanti.

# SSH GUIDA RETE LOCALE
__a == client; b == server__
* bisogna installare il servizio ssh su __b__ (meglio su entrambe le macchine):
```
sudo apt install openssh-server
```
* reperiamo l'ip di __b__ quindi da una shell di __b__ :
```
ip addr
```
* il firewall (se presente) deve consentire le connessioni (sulla porta 22), si può configurare così _b_ :
```
sudo ufw allow ssh
```
* per collegarci da una shell su __a__:
```
ssh b_username@b_ip_address
#poi la shell cambierà "nome" e sarete dentro a __b__
```
* per copiare un file da __a__ ad __b__ e viceversa, apriamo una nuova shell su __a__ e usiamo scp:
  * `scp /percorso/file.txt b_username@b_ip_address:/home/b_username/destinazione/ #a --to--> b` 
  * `scp b_username@b_ip_address:/home/b_username/file_da_copiare.txt . #b --to--> a`

# SSH (GUIDA RETE INTERNET) (TAILSCALE) (NO CHIAVI)
...

# CAMBIARE AMBIENTE GRAFICO SU DEBIAN
* è sufficiente sfruttare il seguente comando per installare altri ambienti disponibili:
```
tasksel
```
* invece per disinstallarli (in questo esempio kde plasma):
```
apt-get --purge remove task-kde-desktop
apt autoremove kde*
```

# AGGIUNGERE FLATPAK REPOSITORY
https://flatpak.org/setup/Debian

# EMOJII TERMINALE
*  distribuzioni moderne di solito hanno il supporto per UTF-8 abilitato di default. Se non è abilitato:
```
export LANG=en_US.UTF-8
# test
echo -e "\U1F60A"
```
* Non tutti i terminali o font possono supportare tutte le emoji. Se non vedi correttamente le emoji, potrebbe essere necessario installare un font che le supporti:
```
sudo apt install fonts-noto-color-emoji
```
* per un test più bello vedi script `emoji.sh`









