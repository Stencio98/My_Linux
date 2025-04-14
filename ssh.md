# ssh
_a --> client; b --> server_
* bisogna installare il servizio ssh su _b_ (meglio su entrambe le macchine):
```
sudo apt install openssh-server
```
* reperiamo l'ip di _b_ quindi da una shell di _b_ :
```
ip addr
```
* il firewall (se presente) deve consentire le connessioni (sulla porta 22), si può configurare così _b_ :
```
sudo ufw allow ssh
```
* per collegarci da una shell su _a_:
```
ssh b_username@b_ip_address
#poi la shell cambierà "nome" e sarete dentro a _b_
```
* per copiare un file da _a_ ad _b_ e viceversa, apriamo una nuova shell su _a_ e usiamo scp:
  * `scp /percorso/file.txt b_username@b_ip_address:/home/b_username/destinazione/ #a --to--> b` 
  * `scp b_username@b_ip_address:/home/b_username/file_da_copiare.txt . #b --to--> a`
