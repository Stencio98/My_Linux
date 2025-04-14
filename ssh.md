# ssh
_a --> client; b --> server_
* bisogna installare il servizio ssh su _b_(meglio su entrambe le macchine):
```
sudo apt install openssh-server
```
* reperiamo l'ip di _b_ quindi da una shell di _b_:
```
ip addr
```
* il firewall (se presente) deve consentire le connessioni (sulla porta 22), si può configurare così _b_:
```
sudo ufw allow ssh
```
* per collegarci 
