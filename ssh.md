# ssh (guida rete locale)
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

# ssh (guida rete internet) 
