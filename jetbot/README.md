# README für jupyter-notebook für den Yahboom Jetbot

## Links zu GitHub-Projekten die zum Testen verwendet werden können

### Nvidia jetbot Beispiel-Code
	[Nvidia Jetbot Code](https://github.com/NVIDIA-AI-IOT/jetbot)
	
### Beispiel-Code von Yahboom
	[Yahboom Jetbot Robot Car Code](https://github.com/YahboomTechnology/JetBot-AI-Robot-Car/tree/master/10.Code)




## Installation

1.	Zuerst müssen einige Pakete installiert werden:

> sudo apt-get install libzmq3-dev python3-venv python3-dev python3-pip libffi-dev -y

2.	Danach muss eine virtuelle Umgebung für Jupyter Notebook erstellt werden:

> mkdir ~/jupyter/
> python3 -m venv ~/jupyter/jupyter-env

3.	Nun muss zeromq installiert werden. Dafür führt man folgende Kommandos aus:

> \# zeromq herunterladen\n\n
> wget https://github.com/zeromq/libzmq/releases/download/v4.2.2/zeromq-4.2.2.tar.gz\n\n
>
> \# tarball entpacken\n\n
> tar xvzf zeromq-4.2.2-tar.gz\n\n
>
> \# Abhängigkeiten für zeromq installieren\n\n
> sudo apt update\n\n
> sudo apt-get install -y libtool pkg-config build-essential autoconf automake uuid-dev\n\n
>
> \# makefile erstellen\n\n
> cd zeromq-4.2.2\n\n
> ./configure\n\n
>
> \# Kompilieren und installieren\n\n
> sudo make install\n\n
>
> \# Den zeromq Treiber in Linux installieren\n\n
> sudo ldconfig\n\n
>
> \# Überprüfen ob zeromq installiert ist\n\n
> ldconfig -p | grep zmq\n\n
>
>
> \# Erwartete Ausgabe\n\n
> \###########################################################\n\n
> \# libzmq.so.5 (libc6,x86-64) => /usr/local/lib/libzmq.so.5\n\n
> \# libzmq.so (libc6,x86-64) => /usr/local/lib/libzmq.so\n\n
> \###########################################################\n\n
>


## Jupyter Notebook installieren

Jupyter Notebook kann mit einem dieser zwei Wege installiert werden:
	* Mit Anaconda
	* Mit pip3

Hier werden wir `pip3` verwenden


### pip3 aktualisieren und jupyter installieren

> pip3 install --upgrade --force-reinstall --no-cache-dir jupyter

Wenn die Installation abgeschlossen ist kann man `jupyter notebook` im Terminal ausführen um den <Jupyter Notebook Server> zu starten.

Daraufhin kann man den Jupyter Notebook Server unter `http://localhost:8888/` erreichen.


### Über SSH auf den Jetbot schalten

PuTTY muss installiert sein und geöffnet werden.

Dafür dem Link hier folgen:
[Hier kann PuTTY heruntergeladen werden](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

Um sich auf den Jetbot aufzuschalten muss in PuTTY als `Host Name` `jetbot@<IP-ADRESSE>` mit dem Port `22` eingegeben werden:

![PuTTY Host Name und Port](https://linaro.atlassian.net/wiki/download/thumbnails/26092012207/image2019-8-12_15-59-46.png?version=1&modificationDate=1565664407426&cacheVersion=1&api=v2&width=466&height=210)


Nun muss noch unter `+ SSH` unter `Tunnels` ein Port, auf dem man `Jupyter Lab` lokal erreichen möchte, eingetragen werden.

Hier verwenden wir den Port `8888`.

![PuTTY Tunnel einrichten](https://assets.digitalocean.com/articles/jupyter_notebook/JN_putty_2.png)


Wenn der Port bereits durch einen anderen Prozess belegt ist, sollte man einen anderen verwenden. Hier muss man beachten, dass man die im Folgenden gezeigten Ports dementsprechend anpassen muss.

Bestenfalls speichert man diese Einstellungen unter `Session` als Profil.
Das bietet einen schnellen Zugriff auf diese Einstellungen, ohne die Einstellungen erneut von Hand vornehmen zu müssen.


Wenn kein Fehler angezeigt wird aktiviert man mit `source ~/jupyter/jupyter-env/bin/activate` die virtuelle Umgebung für Jupyter Notebook.

Nun kann man mit `jupyter notebook` die Jupyter Notebook Applikation starten.


Wenn man `http://localhost:8888` nun öffnet kann man sich mit dem token `jetbot` einloggen.

Sollte man den Token ändern müssen oder wollen ist hier eine Anleitung bereitgestellt:

[Automatische Passwort-Einrichtung](https://jupyter-notebook.readthedocs.io/en/stable/public_server.html#automatic-password-setup)
 
Daraufhin muss Jupyter einmal neugestartet werden um die Datei `/home/jetbot/.jupyter/jupyter_notebook_config.json` zu übernehmen und das neue Passwort zu aktivieren.


### Erstellen eines Skripts um Jupyter Notebook mit der vietuellen Umgebung zu starten

Nun erstellt man mit `nano ~/run-jupyter-notebook.sh` ein Bash Skript. Hierfür muss der Editor Nano mit `sudo apt install nano -y` installiert werden.
Wer möchte kann auch einen anderen Editor verwenden.

Der Inhalt dieses Bash-Skripts ist folgender:

> #!/bin/bash\n\n
> source ~/jupyter/jupyter-env/bin/activate\n\n
> jupyter notebook --ip 0.0.0.0 --port 8888 --no-browser\n\n
> deactivate\n\n

Mit Nano kann man mit der Tastenkombination `Strg + X` speichern und schließen.

Daraufhin muss das Bash-Skript noch ausführbar gemacht werden.
Dafür gibt man folgenden Befehl ein:

> chmod +x ~/run-jupyter-notebook.sh


## Jupyter Lab installieren


### Fix für Fehler mit dem markupsafe-Paket
Vorweg muss man ersteinmal das pip3 Paket `markupsafe` neu installieren, das das nachfolgende Kommando sonst einen Fehler zurückgibt.

Hierfür einmal `markupsafe` neu installieren:
> pip3 install --upgrade --force-reinstall --no-cache-dir markupsafe


### Die eigentliche Installation von Jupyter Lab

Nun kann mit dem folgenden Kommando das `Jupyter Lab` Paket installiert werden:

> pip3 install jupyterlab


Sobald das Paket installiert ist, kann mit `jupyter lab` Jupyter Lab nun gestartet werden.

![Jupyter Lab](https://linaro.atlassian.net/wiki/download/thumbnails/26092012207/image2019-8-25_11-50-54.png?version=1&modificationDate=1566751862473&cacheVersion=1&api=v2&width=640&height=400)


## Jupyter Notebook als Server Dienst laufen lassen

Mit diesem Setup muss man den Jupyter Notebook Server jedes mal nach einem Neustart von Hand starten.
Man kann ihn jedoch auch als System Dienst mit dem System mitstarten lassen.

### Eine Supervisor Konfiguration für Jupyter Notebook erstellen

Zuerst muss - wenn noch nicht geschehen - das Supervisor Paket installiert werden:

> sudo apt install supervisor

Um Jupyter Notebook als Dienst laufen zu lassen muss eine Konfiguration erstellt werden. 
Diese erstellt und öffnet man mit `sudo nano /etc/supervisor/conf.d/jupyter-notebook.conf`

Als Inhalt dieser Datei nimmt man folgendes her:

> [program:jupyter-notebook]\n\n
> directory=/home/jetbot\n\n
> command=/bin/bash -E -c ../run-jupyter-notebook.sh\n\n
> autostart=true\n\n
> autorestart=true\n\n
> stopsignal=INT\n\n
> stopasgroup=true\n\n
> killasgroup=true\n\n
> user=jetbot\n\n

Danach drückt man wieder `Strg + X` um zu speichern und die Datei zu verlassen.


Sobald die Konfiguration fertig ist muss man das folgende Kommando ausführen um diese zu übernehmen:
> sudo systemctl restart supervisor.service


### Wie öffne ich ein neues Notebook mit einem eigenen Port?

Standardmäßig startet Jupyter Notebook mit dem Port `8888`. Wenn der Port `8888` nicht verfügbar ist, kann man mit dem Folgenden einen anderen Port verwenden:

> jupyter notebook --port 9999

Dieses Kommando startet den Server mit dem Port `9999`.
Wenn man allgemein auch für den Dienst einen anderen Port verwenden muss oder möchte, muss man die Konfiguration ebenfalls anpassen.


## Kurze Übersicht an Kommandos

Jupyter Notebook starten:

> jupyter notebook


Jupyter Lab starten:

> jupyter lab 


Supervisor neustarten:

> sudo systemctl restart supervisor.service


Status von Supervisor anzeigen:

> sudo systemctl status supervisor.service



# Vorbereiten der Test-Projekte

Bestenfalls erstellt man in `/home/jetbot` einen eigenen Ordner um Dateien abzulegen.

Hier erstellen wir dafür einen Ordner mit dem Namen `contents`:

> mkdir ~/contents

Hier kann man von 

        [Nvidia Jetbot Code](https://github.com/NVIDIA-AI-IOT/jetbot)

	und

        [Yahboom Jetbot Robot Car Code](https://github.com/YahboomTechnology/JetBot-AI-Robot-Car/tree/master/10.Code)

jeweils eine zip-Datei herunterladen und diese in ~/contents in einem Unterordner entpacken.

Bei dem Link von Yahboom muss man auf die Datei `Download link.txt`  klicken und dort dem Google-Drive Link folgen.


Nun kann man in Jupyter Lab sich in diese Ordner reinklicken und diese `*.ipynb` Dateien ausführen.
