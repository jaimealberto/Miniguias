# Debian 13 + XFCE, modo kiosko, video world.
Equipo que muestre cuadros de mando en uno o varios monitores con arranque automático.

## ✅ 1. Instalación de Debian 13 + XFCE mínimo

* Usa la ISO **netinst** de Debian 13 (Trixie).
* En “Software selection”, desmarca todo y deja solo:

  * **Xfce**
  * **Standard system utilities**
  * **Servidor ssh**
 
## ✅ 2. Instalar los paquetes mínimos necesarios para modo kiosko, video world
```bash

```

## ✅ 3. Crear un usuario dedicado al video world
Como root:
```bash
useradd -m -s /bin/bash -c "Monitor User" monitor
passwd monitor
```

### ✅ 3.1 Habilitar sudo sin contraseña para usuario 'sistemas', ansible (opcional)
Como root:
```bash
 cat /etc/sudoers.d/sistemas
 sistemas ALL=(ALL) NOPASSWD:ALL
```

## ✅ 4. Configurar la red, ip fija
Esto lo podemos hacer desde el entorno gráfico o bien via ssh, como root, ejemplo:
```bash
cat /etc/NetworkManager/system-connections/Wired\ connection\ 1 
[connection]
id=Wired connection 1
uuid=fbf921b3-633e-4b26-97ac-f1d221ec3e33
type=ethernet
timestamp=1765191270

[ethernet]

[ipv4]
address1=192.168.0.10/24
dns=9.9.9.9;1.1.1.1;
gateway=192.168.0.1
method=manual

[ipv6]
addr-gen-mode=stable-privacy
ip6-privacy=2
method=disabled

[proxy]
```

## ✅ 5. Configurar inicio automático (autologin) en LightDM
Editar como root:
```bash
vi /etc/lightdm/lightdm.conf
```

Descomentar/añadir:
```
[Seat:*]
autologin-user=monitor
autologin-user-timeout=0
```

## ✅ 6. Crear un script que abra las páginas en modo kiosko **y las rote**
Con el usuario monitor:
```bash
vi /home/monitor/monitor.py
```
```python
import os
import time
import logging
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.options import Options
from selenium.common.exceptions import WebDriverException, NoSuchElementException

# ===============================
# Configuración
# ===============================
TIEMPO = 180  # tiempo por URL en segundos
CHROMEDRIVER_PATH = "/usr/bin/chromedriver"  # Ajustar según instalación

URLS = [
    {"url": "https://192.168.1.20", "user": "usuario", "password": "contraseña"},
    {"url": "https://192.168.1.30/tv"""},
    {"url": "https://threatmap.checkpoint.com"""}
]

PROFILE_DIR = "/home/monitor/monitor-profile"
os.makedirs(PROFILE_DIR, exist_ok=True)

LOG_FILE = "/home/monitor/monitor.log"
logging.basicConfig(filename=LOG_FILE, level=logging.INFO,
                    format='%(asctime)s [%(levelname)s] %(message)s')

# ===============================
# Funciones
# ===============================

def iniciar_driver():
    chrome_options = Options()
    chrome_options.add_argument("--kiosk")
    chrome_options.add_argument("--noerrdialogs")
    chrome_options.add_argument("--disable-infobars")
    chrome_options.add_argument("--ignore-certificate-errors")
    chrome_options.add_argument("--disable-session-crashed-bubble")
    chrome_options.add_argument("--disable-extensions")
    chrome_options.add_argument(f"--user-data-dir={PROFILE_DIR}")
    chrome_options.add_argument("--password-store=basic")

    service = Service(CHROMEDRIVER_PATH)
    driver = webdriver.Chrome(service=service, options=chrome_options)
    return driver

def login_if_needed(driver, user, password):
    if not user or not password:
        return
    try:
        username_fields = driver.find_elements(By.XPATH, "//input[@type='text' or @type='email']")
        password_fields = driver.find_elements(By.XPATH, "//input[@type='password']")
        if username_fields and password_fields:
            username_fields[0].clear()
            username_fields[0].send_keys(user)
            password_fields[0].clear()
            password_fields[0].send_keys(password)
            try:
                submit_button = driver.find_element(By.XPATH, "//button[@type='submit']")
                submit_button.click()
            except NoSuchElementException:
                password_fields[0].send_keys("\n")
            time.sleep(5)
            logging.info(f"Login realizado con éxito en la página.")
        else:
            logging.info("No se detectó formulario de login, continuando...")
    except Exception as e:
        logging.error(f"Error durante login: {e}")

# ===============================
# Loop principal
# ===============================

while True:
    try:
        driver = iniciar_driver()
        logging.info("Chromium iniciado correctamente.")
        
        while True:
            for entry in URLS:
                url = entry["url"]
                user = entry.get("user", "")
                password = entry.get("password", "")
                
                logging.info(f"Cargando URL: {url}")
                driver.get(url)
                
                login_if_needed(driver, user, password)
                time.sleep(TIEMPO)

    except WebDriverException as e:
        logging.error(f"WebDriverException: {e}. Reiniciando Chromium en 5s...")
        try:
            driver.quit()
        except:
            pass
        time.sleep(5)
    except Exception as e:
        logging.error(f"Error inesperado: {e}. Reiniciando Chromium en 5s...")
        try:
            driver.quit()
        except:
            pass
        time.sleep(5)

```
En Debian 13 debido a la política **PEP 668** **no puedes instalar paquetes globalmente con pip**. La manera correcta de utilizar el script anteior es **usar un entorno virtual o `pipx`**. Ahora vamos a dar los pasos previos para preparar todo, para el usuario `monitor`.

**1️⃣ Instalar paquetes necesarios del sistema**, como root:
```bash
apt update
apt install python3-full python3-venv chromium chromium-driver -y
```
**2️⃣ Crear un entorno virtual para el usuario monitor**, como el usuario minitor:
```bash
cd /home/monitor
python3 -m venv venv
```
* Esto crea un directorio `venv` dentro de `/home/monitor`.

**3️⃣ Activar el entorno virtual**

```bash
source /home/monitor/venv/bin/activate
```
Ahora el prompt debería mostrar `(venv)`.

**4️⃣ Instalar Selenium dentro del entorno virtual**

Dentro del entorno virtual **no hay restricciones de PEP 668**, así que:

```bash
pip install --upgrade pip
pip install selenium
```
* Esto instalará Selenium solo dentro del entorno virtual, sin tocar Python del sistema.

**5️⃣ Probar Selenium**
```bash
python -c "import selenium; print(selenium.__version__)"
```
Deberías ver la versión instalada, confirmando que Selenium funciona.

**6️⃣ Ejecutar tu script de kiosko**
```bash
python /home/monitor/monitor.py
```
* Chromium se abrirá en **pantalla completa**.
* Rotará URLs y hará login automáticamente si está definido en la lista.

## ✅ 7. Hacer que el script ejecute al iniciar sesión
Con el usuario monitor:
```bash
mkdir -p /home/monitor/.config/autostart
vi /home/monitor/.config/autostart/monitor.desktop
```
``` ini
[Desktop Entry]
Type=Application
Exec=bash -c 'source /home/monitor/venv/bin/activate && python /home/monitor/monitor.py'
Hidden=false
NoDisplay=false
X-GNOME-Autostart-enabled=true
Name=Monitor
```

## ✅ 8. Deshabilitar globalmente bloqueos
- Protector de pantalla XFCE
- Ahorro de energía del XFCE Power Manager
- Eliminar configuraciones locales de usuarios
- DPMS en Xorg
- Crear script de inicio global para desactivar DPMS

Como root ejecutar el siguiente script:

```bash
#!/bin/bash

echo "=== Deshabilitando protector de pantalla XFCE globalmente ==="
mkdir -p /etc/xdg/xfce4/xfconf/xfce-perchannel-xml/

cat > /etc/xdg/xfce4/xfconf/xfce-perchannel-xml/xfce4-screensaver.xml << 'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<channel name="xfce4-screensaver" version="1.0">
    <property name="saver-enabled" type="bool" value="false"/>
    <property name="lock-enabled" type="bool" value="false"/>
    <property name="idle-activation-enabled" type="bool" value="false"/>
</channel>
EOF


echo "=== Deshabilitando ahorro de energía del XFCE Power Manager ==="
cat > /etc/xdg/xfce4/xfconf/xfce-perchannel-xml/xfce4-power-manager.xml << 'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<channel name="xfce4-power-manager" version="1.0">
    <property name="xfce4-power-manager" type="empty">
        <property name="dpms-enabled" type="bool" value="false"/>
        <property name="dpms-on-ac-sleep" type="int" value="0"/>
        <property name="dpms-on-ac-off" type="int" value="0"/>
        <property name="dpms-on-battery-sleep" type="int" value="0"/>
        <property name="dpms-on-battery-off" type="int" value="0"/>
        <property name="brightness-on-ac" type="int" value="100"/>
    </property>
</channel>
EOF


echo "=== Eliminando configuraciones locales de usuarios ==="
for dir in /home/*/; do
    rm -f "${dir}/.config/xfce4/xfconf/xfce-perchannel-xml/xfce4-screensaver.xml"
    rm -f "${dir}/.config/xfce4/xfconf/xfce-perchannel-xml/xfce4-power-manager.xml"
done


echo "=== Deshabilitando DPMS globalmente en Xorg ==="
mkdir -p /etc/X11/xorg.conf.d/

cat > /etc/X11/xorg.conf.d/10-monitor.conf << 'EOF'
Section "Monitor"
    Identifier "Monitor0"
    Option "DPMS" "false"
EndSection

Section "ServerFlags"
    Option "BlankTime" "0"
    Option "StandbyTime" "0"
    Option "SuspendTime" "0"
    Option "OffTime" "0"
EndSection
EOF


echo "=== Creando script de inicio global para desactivar DPMS ==="
mkdir -p /etc/X11/xinit/xinitrc.d/

cat > /etc/X11/xinit/xinitrc.d/99-nodpms.sh << 'EOF'
#!/bin/sh
xset -dpms
xset s off
xset s noblank
EOF

chmod +x /etc/X11/xinit/xinitrc.d/99-nodpms.sh


echo "=== Todo listo. Reinicia la máquina para aplicar cambios. ==="
```
