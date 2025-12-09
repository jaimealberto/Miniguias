Para apagar tu Linux automáticamente a diferentes horas según el día de la semana, lo más sencillo es usar **cron**.

---

# ✅ **1. Editar el cron del sistema**

Abre el cron en modo root:

```bash
sudo crontab -e
```

---

# ✅ **2. Añade estas líneas**

## 🔹 **De lunes a viernes (17:30):**

```cron
30 17 * * 1-5 /sbin/shutdown -h now
```

## 🔹 **Sábados y domingos (14:30):**

```cron
30 14 * * 6,7 /sbin/shutdown -h now
```

---

# 🧠 **Explicación rápida**

* `30 17` → minuto 30, hora 17
* `* *` → cualquier día y mes
* `1-5` → lunes a viernes
* `6,7` → sábado y domingo
* `/sbin/shutdown -h now` → apaga el equipo

---

# ☑️ **3. Guardar y salir**

Cron aplicará los cambios automáticamente.
