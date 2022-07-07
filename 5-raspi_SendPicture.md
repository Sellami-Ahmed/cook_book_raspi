# Envoyez des photos chaque période du temps automatiquement depuis une carte Raspberry Pi vers un serveur (Réseau local)
  
&nbsp;
***
&nbsp;


## Code client 

Vous devez d'abord créer un fichier python dans la carte raspi .

```bash
touch autoRun.py 
```

Puis copiez le code suivant dans le dernier fichier 

```python
import socket
import os
os.system("libcamera-still -o image.jpg -n")
client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)  # AF_INET = IP, SOCK_STREAM = TCP
client.connect(('192.168.27.217', 5050))#tappez l'adress ip du serveur dans le réseau

file = open('image.jpg', 'rb')
image_data = file.read(2048)

while image_data:
    client.send(image_data)
    image_data = file.read(2048)

file.close()
client.close()
```
sauvgardez le fichier (apres changement d'adress ip) et mémorisez l'emplacement du fichier.

&nbsp;
***
&nbsp;

## Code serveur

Dans votre serveur créez un ficher python et copiez le code suivant 

```python
import socket
from datetime import datetime
server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)  
server.bind(('192.168.27.217', 5050))  # tappez l'adrees ip du serveur dans le réseau 
while True :

	server.listen()

	client_socket, client_address = server.accept()

	file = open(str(datetime.now())+'.jpg', "wb")
	image_chunk = client_socket.recv(2048)  # stream-based protocol

	while image_chunk:
	    file.write(image_chunk)
	    image_chunk = client_socket.recv(2048)

	file.close()
	client_socket.close()
```
&nbsp;
***
&nbsp;

## Rendre le code executable automatiquement

* Connectez à votre Pi
* Dans le terminal tappez :

```bash
sudo crontab -e
```

* Sélectionnez nano comme éditeur du texte puis ajoutez le code suivant dans la dérnier ligne :

```bash
*/20 10-15 * * * sudo python <<l'emplacement du fichier python>>/<<nom du fichier>>.py
```
par exemple :

```bash
*/20 10-15 * * * sudo python /home/pi/Desktop/autoRun.py
```
Dans cette exemple le s'éxecute chaque 20 mintes du 10 AM à 3 PM.

Pour voir comment régler le temp du fonctionnement du code à votre choix  [voir ce lien](https://www.geeksforgeeks.org/how-to-schedule-python-scripts-as-cron-jobs-with-crontab/).

&nbsp;
***
&nbsp;
