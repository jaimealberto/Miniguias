---
title: "Plan backup nocturno"
description: "Programación de backup apagado encendido automático"
toc: true
date: yyyy-dd-mm
---

enlace imagen

{{< figure src="/img/nombre.jpg">}}

## OpnSense

Utilizar el cron para encender los hots necesarios y a las horas estimadas para la ejecución de los scripts correspondientes.


{{< highlight zsh >}}
crontab -e
#minute hour    mday    month   wday    command
45      00      *       *       *       (/usr/sbin/wake <interface> <mac>) > /dev/null
{{< /highlight >}}

## Host de trabajo
Revisar los script y verificar hora de finalización. Programar cron de root para el apagado a la hora que los trabajos estén finalizados

{{< highlight zsh >}}
crontab -e
# m h  dom mon dow   command
30 3 * * * /usr/sbin/shutdown -h now >/dev/null 2>&1
{{< /highlight >}}
