# Plan backup nocturno
Programación de backup apagado encendido automático

## OpnSense
Utilizar el cron para encender los hots necesarios y a las horas estimadas para la ejecución de los scripts correspondientes.

```bash
crontab -e
#minute hour    mday    month   wday    command
45      00      *       *       *       (/usr/sbin/wake <interface> <mac>) > /dev/null
```

## Host de trabajo
Revisar los script y verificar hora de finalización. Programar cron de root para el apagado a la hora que los trabajos estén finalizados

```bash
crontab -e
# m h  dom mon dow   command
30 3 * * * /usr/sbin/shutdown -h now >/dev/null 2>&1
```
