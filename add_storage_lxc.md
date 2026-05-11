Ampliar un disco en un contenedor **LXC en Proxmox** tiene **2 partes**:

1. Ampliar el disco desde el nodo Proxmox
2. Ampliar el sistema de archivos dentro del contenedor

# 🔎 1️⃣ Verifica el almacenamiento del contenedor
En el nodo Proxmox ejecuta:
```bash
pct list
```
Identifica el ID del contenedor (ejemplo: `101`).
Revisa su disco:
```bash
pct config 101
```
Verás algo como:
```
rootfs: local-lvm:vm-101-disk-0,size=8G
```
# 📦 2️⃣ Ampliar el disco desde Proxmox
## ➜ Método rápido (recomendado)
```bash
pct resize 101 rootfs +10G
```
Esto agregará 10GB adicionales al disco actual.
También puedes establecer un tamaño específico:
```bash
pct resize 101 rootfs 20G
```
# 📂 3️⃣ Ampliar el sistema de archivos dentro del contenedor
Entra al contenedor:
```bash
pct enter 101
```
Ahora depende del sistema de archivos.
## 🟢 Si usa EXT4 (lo más común)
Ejecuta:
```bash
resize2fs /dev/mapper/pve-vm--101--disk--0
```
Si no conoces el dispositivo:
```bash
df -h
lsblk
```
En muchos LXC no necesitas indicar el dispositivo manualmente y basta con:
```bash
resize2fs /
```
## 🔵 Si usa XFS
XFS se expande montado:
```bash
xfs_growfs /
```
# 📊 4️⃣ Verificar que se amplió
```bash
df -h
```
Deberías ver el nuevo tamaño.
# ⚠️ Casos especiales
### 🟡 Si usas almacenamiento tipo Directory (no LVM)
A veces el resize se aplica automáticamente y no necesitas hacer nada dentro del contenedor.
### 🔴 Si aparece error
Revisa el backend:
```bash
lsblk
```
Y verifica si usa LVM Thin, ZFS o Directory.
# 🧠 Resumen rápido

| Paso             | Comando                      |
| ---------------- | ---------------------------- |
| Ver contenedor   | `pct list`                   |
| Redimensionar    | `pct resize ID rootfs +SIZE` |
| Entrar           | `pct enter ID`               |
| Expandir FS EXT4 | `resize2fs /`                |
| Expandir FS XFS  | `xfs_growfs /`               |