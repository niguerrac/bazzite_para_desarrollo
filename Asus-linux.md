
# como instalar el kernel desde asus linux

instalar los repos
```bash
sudo wget https://copr.fedorainfracloud.org/coprs/lukenukem/asus-linux/repo/fedora-41/lukenukem-asus-linux-fedora-41.repo -O /etc/yum.repos.d/_copr_lukenukem_asus-linux.repo
```
instalar todas las herramientas juntas
```bash
rpm-ostree install --allow-inactive asusctl supergfxctl asusctl-rog-gui
```


# Como instalar supergfxctl
Este sensillo solo deben seguir los pasos de la documentacion
https://gitlab.com/asus-linux/supergfxctl

Como estamos trabajando en Bazzite usaremos rpm-ostree si usan fedora cambiarlo por dnf   

```bash
#el group install me dio error al final pero funcioni igual
sudo rpm-ostree upgrade && sudo rpm-ostree install curl git && sudo rpm-ostree groupinstall "Development Tools"
```
esste es rust dios sabe para que es.

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source ~/.cargo/env
```


este es el importante es copiar el repositorio y compilarlo

```bash
git clone https://gitlab.com/asus-linux/supergfxctl.git
```

```bash
cd supergfxctl
make && sudo make install
```

Enable and start the service
```bash
sudo systemctl enable supergfxd.service --now
```
entiedo que esto es para lo del grupinstall pero funciona igual aun que da error antes
```bash
Enable and start the service
sudo systemctl enable supergfxd.service --now
```

Tabla de uso de modos
**GPU Modes**
Command

**Integrated**
supergfxctl --mode Integrated

**Hybrid**
supergfxctl --mode Hybrid

**VFIO**
supergfxctl --mode Vfio

**AsusEgpu**
supergfxctl --mode AsusEgpu

**AsusMuxDgpu**
supergfxctl --mode AsusMuxDgpu



## Crear una nueva opción de sesión para Plasma con GPU Integrada

Esta es de mi cosecha. queria elegir la targeta al inicio de secion para ya entrar con la seleccionada.

*Según la información de Bazzite, el método correcto es usar rpm-ostree, que es la herramienta subyacente para gestionar el sistema de archivos atómico. Para hacer el directorio /usr (donde se encuentran los archivos de sesión) escribible, necesitas usar el comando usroverlay.*


Aquí están los pasos para que puedas crear tu sesión de escritorio personalizada:

**Pasos para habilitar la escritura**
Ejecuta el comando para habilitar la escritura:
Abre un terminal y escribe el siguiente comando con permisos de superusuario:

```Bash

sudo rpm-ostree usroverlay
```
Este comando hará el sistema de archivos /usr temporalmente escribible. Los cambios realizados se mantendrán hasta el siguiente reinicio.

### Crear la nueva sesión de escritorio
Ahora que el sistema de archivos es editable, puedes continuar con los pasos que te di anteriormente. No es necesario reiniciar después de ejecutar usroverlay.

Crea el script de inicio:
Primero, crea un pequeño script que cambie el modo de la GPU y luego inicie la sesión de Plasma. Guárdalo en un lugar seguro como /usr/local/bin/.

```Bash

sudo nano /usr/local/bin/start_plasma_integrated_gpu.sh
```
Pega el siguiente contenido en el archivo:

```Bash

#!/bin/bash
/usr/bin/supergfxctl --mode Integrated
/usr/libexec/plasma-dbus-run-session-if-needed /usr/bin/startplasma-wayland
```
Guarda y hazlo ejecutable:

```Bash

sudo chmod +x /usr/local/bin/start_plasma_integrated_gpu.sh
```
Crea el archivo .desktop de la nueva sesión:
Copia el archivo original de Plasma y edita el nuevo para que apunte a tu script.

```Bash

sudo cp /usr/share/wayland-sessions/plasma.desktop /usr/share/wayland-sessions/plasma-integrated-gpu.desktop
sudo nano /usr/share/wayland-sessions/plasma-integrated-gpu.desktop
```
Modifica el archivo para que se vea así:

```Ini, TOML

[Desktop Entry]
Exec=/usr/local/bin/start_plasma_integrated_gpu.sh
TryExec=/usr/bin/startplasma-wayland
DesktopNames=KDE
Name=Plasma (GPU Integrada)
Comment=KDE Plasma Desktop (Optimizada para GPU Integrada)
# ... (el resto del archivo)
```

**Qué sucederá ahora?**
Cuando cierres tu sesión actual, el gestor de login (SDDM) detectará tu nuevo archivo .desktop. Verás una nueva opción llamada "Plasma (GPU Integrada)" en el menú de selección de sesión. Al elegirla y entrar, se ejecutará tu script, configurando el modo Integrated antes de iniciar el escritorio.

## Repetiremos el proceso para el modo hibrido y la externa (egpu)
**Pasos para crear las sesiones**
Habilita la escritura en el sistema de archivos:

Abre una terminal y ejecuta el siguiente comando.

```Bash

sudo rpm-ostree usroverlay
```
Esto hará que el sistema de archivos /usr sea temporalmente escribible, lo que te permitirá crear los archivos necesarios.

**Crea los scripts de inicio para cada modo:**

Vamos a crear dos nuevos scripts, uno para el modo híbrido y otro para el modo de GPU externa (eGPU).

Para el modo Híbrido:

```Bash

sudo nano /usr/local/bin/start_plasma_hybrid_gpu.sh
```
Pega el siguiente contenido:

```Bash

#!/bin/bash
/usr/bin/supergfxctl --mode Hybrid
/usr/libexec/plasma-dbus-run-session-if-needed /usr/bin/startplasma-wayland
```
Haz el script ejecutable:

```Bash

sudo chmod +x /usr/local/bin/start_plasma_hybrid_gpu.sh
```
Para el modo de GPU Externa (eGPU):

```Bash

sudo nano /usr/local/bin/start_plasma_egpu.sh
```
Pega el siguiente contenido:

```Bash

#!/bin/bash
/usr/bin/supergfxctl --mode E-GPU
/usr/libexec/plasma-dbus-run-session-if-needed /usr/bin/startplasma-wayland
```
Haz el script ejecutable:

```Bash

sudo chmod +x /usr/local/bin/start_plasma_egpu.sh
```
**Crea los archivos .desktop para cada sesión:**

Ahora, copia el archivo original de Plasma para cada nueva sesión y modifica la línea Exec para que apunte a los scripts que acabas de crear.

Para el modo Híbrido:

```Bash

sudo cp /usr/share/wayland-sessions/plasma.desktop /usr/share/wayland-sessions/plasma-hybrid-gpu.desktop
sudo nano /usr/share/wayland-sessions/plasma-hybrid-gpu.desktop
```
Modifica el archivo para que se vea así:

```Ini, TOML

[Desktop Entry]
Exec=/usr/local/bin/start_plasma_hybrid_gpu.sh
TryExec=/usr/bin/startplasma-wayland
DesktopNames=KDE
Name=Plasma (GPU Híbrida)
Comment=KDE Plasma Desktop (Modo Híbrido)
# ... (el resto del archivo)
```
Para el modo de GPU Externa (eGPU):

```Bash

sudo cp /usr/share/wayland-sessions/plasma.desktop /usr/share/wayland-sessions/plasma-egpu.desktop
sudo nano /usr/share/wayland-sessions/plasma-egpu.desktop
```
Modifica el archivo para que se vea así:

```Ini, TOML

[Desktop Entry]
Exec=/usr/local/bin/start_plasma_egpu.sh
TryExec=/usr/bin/startplasma-wayland
DesktopNames=KDE
Name=Plasma (GPU Externa)
Comment=KDE Plasma Desktop (Modo GPU Externa)
### ... (el resto del archivo)
```

---
---


# Como instalar asus-ctl
primero crear un container para poder utilizarlo en bazzite, en otra distribucion se podria saltar

```bash
distrobox create --name fedora-asusctl --image quay.io/fedora/fedora:latest \
  --additional-flags "--privileged --volume /run:/run"
```
Este comando:

Usa --privileged para permitir acceso extendido al hardware.
Monta /run, que es útil para que el contenedor acceda a dbus y otros servicios del sistema.
Evita duplicar montajes de /dev y /sys.

```bash
distrobox-enter --root fedora-asusctl
```
instalamos asus-ctl
```bash
sudo dnf copr enable lukenukem/asus-linux
sudo dnf install asusctl
```
para que esto no lo se pero funciona.
