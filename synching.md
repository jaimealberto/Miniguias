# "Syncthing"
Operativa despues de instalacion y configuracion como servicio.

## Configuracion

Después de instalarlo como requiera cada distribución si tenemos entorno gráfico lo arrancamos y configuramos con contraseña activando https y la direccion ip local para evitar accesos no deseados.

https://localhost:8384/

![](img/syncthing00.png)
![](img/syncthing01.png)



## Fichero de arranque del servicio

/etc/systemd/system/syncthing.service

```bash
     [Unit]
     Description=Syncthing - Open Source Continuous File Synchronization for %I
     Documentation=man:syncthing(1)
     After=network.target

     [Service]
     User=<usuario que lo ejecutara>
     ExecStart=/usr/bin/syncthing -no-browser -no-restart
     Restart=on-failure
     RestartSec=5
     
     [Install]
     WantedBy=default.target

```
