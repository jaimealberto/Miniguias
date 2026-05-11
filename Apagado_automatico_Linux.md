# Apagado automático Linux
Apagar Linux automáticamente a diferentes horas según el día de la semana.

## Prerrequisitos
- Acceso root al sistema

## ✅ 1. Editar el cron del sistema
Abre el cron en modo root:
```bash
crontab -e
```

## ✅ 2. Añade estas líneas

### De lunes a viernes (17:30)
```cron
30 17 * * 1-5 /sbin/shutdown -h now
```

### Sábados y domingos (14:30)
```cron
30 14 * * 6,7 /sbin/shutdown -h now
```

## ✅ 3. Guardar y salir
Cron aplicará los cambios automáticamente.

## Explicación rápida

* `30 17` → minuto 30, hora 17
* `* *` → cualquier día y mes
* `1-5` → lunes a viernes
* `6,7` → sábado y domingo
* `/sbin/shutdown -h now` → apaga el equipo
