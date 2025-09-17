# Documentación del Proyecto

## 📘 Introducción
Describe brevemente el propósito del proyecto, qué problema resuelve y quién lo va a usar.





## Instalacion de Unity
Debido a que Bazzite es un sistema inmutable, no debes ejecutar estos comandos directamente en la terminal principal. Si lo haces, los cambios no serán permanentes o podrían fallar. 

La forma correcta de usar estos comandos en Bazzite es dentro de un contenedor de Distrobox, como te expliqué en la respuesta anterior. 

Aquí tienes el paso a paso correcto para Bazzite usando esos comandos: 

1. Crea e ingresa a un contenedor de Fedora: Si aún no lo has hecho. 
```Bash 

distrobox-create --name unity-dev --image fedora:latest 
distrobox-enter unity-dev
```
2. Ejecuta los comandos de instalación DENTRO del contenedor: Una vez que la terminal muestre que estás dentro de unity-dev, ejecuta los comandos de la documentación. 

```Bash 

# Primero, añade el repositorio (dentro del contenedor) 
sudo sh -c 'echo -e "[unityhub]\nname=Unity Hub\nbaseurl=https://hub.unity3d.com/linux/repos/rpm/stable\nenabled=1\ngpgcheck=1\ngpgkey=https://hub.unity3d.com/linux/repos/rpm/stable/repodata/repomd.xml.key\nrepo_gpgcheck=1" > /etc/yum.repos.d/unityhub.repo' 
 
# Luego, instala Unity Hub con dnf (es el gestor moderno en Fedora) 
sudo dnf install unityhub 
 ```

(Nota: dnf es el sucesor de yum en Fedora, pero yum suele funcionar como un alias. Es mejor usar dnf). 

3. Exporta la aplicación: Para que puedas usar Unity Hub desde el menú de aplicaciones de Bazzite como cualquier otro programa. 

```Bash 

distrobox-export --app unityhub 
 ```

En resumen, los códigos de la documentación son correctos para un sistema tipo Fedora, pero en Bazzite debes ejecutarlos dentro de un contenedor Distrobox para que funcionen correctamente sin romper la integridad del sistema. 

https://github.com/aws-samples/unity-build-server-with-aws-cdk 

https://docs.unity3d.com/hub/manual/InstallHub.html#install-hub-linux  


## Instalación de VS Code y sus Dependencias en Bazzite
Dado que Bazzite es inmutable, la mejor manera de instalar herramientas de desarrollo es dentro de un contenedor. Usaremos el mismo contenedor unity-dev que creamos antes para mantener todo el entorno de desarrollo en un solo lugar.

1. Ingresa a tu Contenedor de Desarrollo
Si no estás dentro, accede a tu contenedor de Distrobox.

```Bash

distrobox-enter unity-dev
```
2. Instalar Visual Studio Code (dentro del contenedor)
Microsoft ofrece un repositorio oficial para sistemas Fedora/RHEL, lo que facilita mucho la instalación.

a. Importa la clave de Microsoft GPG:
Este comando le dice a tu sistema que confíe en el software de Microsoft.

```Bash

sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
b. Añade el repositorio de Visual Studio Code:
Este comando crea el archivo de configuración para que dnf sepa de dónde descargar VS Code.
```

```Bash

sudo sh -c 'echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/vscode.repo'
c. Instala VS Code:
Ahora, simplemente instala el paquete code (ese es el nombre del paquete para VS Code).
```

```Bash

sudo dnf install code
```
3. **Instalar .NET SDK y Mono**
Unity depende de estas dos tecnologías para la compilación de scripts, la depuración y para que el autocompletado (IntelliSense) de VS Code funcione correctamente.

a. **Instalar el SDK de .NET**:
Fedora mantiene los paquetes de .NET en sus repositorios oficiales. Instalar el SDK (Software Development Kit) es suficiente, ya que incluye todo lo necesario.

```Bash

sudo dnf install dotnet-sdk-8.0
```
(Puedes cambiar 8.0 por la versión que necesites, pero la 8.0 es la más reciente y debería funcionar bien con las versiones modernas de Unity).

b. **Instalar Mono**:
De manera similar, Mono también está disponible en los repositorios de Fedora. Se recomienda instalar el paquete de desarrollo (mono-devel) que incluye MSBuild, una herramienta que VS Code a menudo necesita.

```Bash

sudo dnf install mono-devel
```
4. **Exportar Visual Studio Code**
Al igual que con Unity Hub, debes "exportar" la aplicación desde el contenedor para poder abrirla fácilmente desde el menú de Bazzite.

```Bash

distrobox-export --app code
```


### Installar modulo de windows en caso de error 
Instalar dentro del contenedor herramientas de descompresion, aun que al parecer en unity al abrir el vscode  instalo lo que faltaba y se reparo lo del modulo de windows

```bash
sudo dnf install p7zip p7zip-plugins cpio 

/usr/bin/unityhub --headless install --version 2022.3.62f1 --module windows-mono 
```
 












---

*Documentación creada por Nilson.*
