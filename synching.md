# Syncthing
Operativa después de instalación y configuración como servicio.

## Configuración

Después de instalarlo como requiera cada distribución.


- Arrancamos el servicio con el icono que aparecerá.
- Si no disponemos de entorno gráfico, desde un terminal
```bash
sudo systemctl --user sart syncthing@$nomredeusuario.service
```
Accedemos localmente y configuramos o si no tenemos entorno gráfico vamos al home del usuario donde tendremos el fichero de configuración, cat .config/syncthing/config.xml

le damos acceso remoto modificando la siguiente liena:
```bash
<gui enabled="true" tls="true" debugging="false">
        <address>localhost:8384</address>
        <user>admin</user>
        <password>$2a$10$j/1R3cpquK4WUrcWuQ.z3Oj.QTHIH.8qtzn0IReuRydWeCDrV1BC6</password>
        <apikey>davGbU6b42AnmXUt39U6aNgAeig5xJtL</apikey>
        <theme>default</theme>
```
Donde se indica localhost:8384 le indicamos una ip accesible del host y el puerto que queramos si no queremos dejar el de defecto.

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
El servicio se gestionara según necesidades ( servidor, portátil,sobremesa,etc)

```bash
# Arrancar el servicio
sudo systemctl start syncthing.service
# Estado del servicio
sudo systemctl status syncthing.service
# Parar el servicio
sudo systemctl stop syncthing.service
# Habilitar el servicio al arranque, si es necesario no se recomienda en portátiles
sudo systemctl enable syncthing.service
# Deshabilitar el servicio al arranque, si es necesario no se recomienda en portátiles
sudo systemctl disable syncthing.service
```