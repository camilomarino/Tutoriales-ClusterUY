# Visual Studio Code en un nodo de [ClusterUY](https://www.cluster.uy/)
##### Escrito el 23/05/2021 

---
El siguiente tutorial explica como poder utilizar [VS Code](https://code.visualstudio.com/) de forma remota en ClusterUY, centrado en la utilización de Python y Jupyter Notebook.

---
## [Visual Studio Code Server](https://github.com/cdr/code-server)
VS Code Server es un servidor web que nos permite abrir VS Code en el navegador. Las extensiones para este son prácticamente las mismas que la versión oficial de VS Code.

---
## Instalación
A continuación se presentan los pasos de instalación para ClusterUY (probablemente sea el mismo procedimiento para otro Cluster con Slurm instalado).  
**Se recomienda utilizar las versiones presentes en este documento**, ya que se comprobó que con otra combinación de versiones es probable que los Jupyter Notebooks no funcionen o se comporten de forma impredecible.

### 1. Instalar VS Code Server.
- Conectarse al login del cluster 
- Correr el script [install_vs_code_server.sh](install_vs_code_server.sh), que contiene lo siguiente:
```bash
VERSION=3.10.2
mkdir -p ~/.local/lib ~/.local/bin
curl -fL https://github.com/cdr/code-server/releases/download/v$VERSION/code-server-$VERSION-linux-amd64.tar.gz \
	| tar -C ~/.local/lib -xz
mv ~/.local/lib/code-server-$VERSION-linux-amd64 ~/.local/lib/code-server-$VERSION
ln -s ~/.local/lib/code-server-$VERSION/bin/code-server ~/.local/bin/code-server
```
- Agregar ~/.local/bin al PATH. Por defecto deberia estar incluido en el ClusterUY.

### 2. Conectarse a VS Code Server. 
- Pedir una sesión interactiva con srun, por ejemplo con:
```bash
srun --job-name=vscode --partition=normal --qos=normal --time=01:00:00 --ntasks=1 --mem=32G --cpus-per-task=1 --pty bash -l
```
- En el nodo ejecutar:
```
code-server --bind-addr=0.0.0.0:8080
```
Que inicia el servidor, le indica que inicie el servidor web en el puerto 8080 y lo hace accesible desde el nodo de login.
- Luego, ejecutar en la máquina local el siguiente comando:
```bash
ssh -N -L localhost:8080:nodeXX.datos.cluster.uy:8080 usuario_cluster@cluster.uy
```
Donde en nodeXX el XX representa el número de nodo, y usuario_cluster es el usuario asignado en el cluster.

Lo anterior se encarga de redirigir los puertos desde tu máquina al nodo del cluster.
- Ahora solo resta abrir el navegador y dirigirse a [http://localhost:8080](http://localhost:8080). La contraseña se encuentra el archivo ~/.config/code-server/config.yaml, la cual solo se deberá introducir la primera vez.


### 3. Instalar extensiones python.
Se debe instalar las extensiones de igual forma que se realizan en VS Code. Las extensiones necesarias para usar Python y Jupyter son:
- Python: versión *2021.2.633441544*
- Jupyter: versión *2021.2.0*

Otra combinación de versiones es probable que no funcione correctamente.

### 4. Repetir el paso 2.

Una vez ya instalado todo, solo resta cada vez que se quiera utilizar VS Code repetir el paso 2 para conectarse al servidor.


---
### Problemas encontrados:
- Algunas veces cuando al abrir un Notebook, este se abre más de una vez.
- Algunas shortcuts son capturadas por el navegador y no VS Code. Esto podria solucionarse por ejemplo con la extension para Google-Chrome [Disable keyboard shortcuts](https://chrome.google.com/webstore/detail/disable-keyboard-shortcut/aidbmcboeighgdnilpdljbedbbiocphj).
- Cualquier ventana emergente de código ejecutado en un Jupyter o consola interactiva no será mostrada.


---
## ¿Por qué no solo usar VS Code?
Lo primero que uno encuentra al buscar como abrir VS Code de forma remota es la excelente extensión Remote-SSH. Esta es realmente útil y poderosa, pero tiene dos problemas en este contexto:

1. La conexión solo es posible con el login del Cluster. Es decir, no es posible abrir VS Code en los nodos, lo cual llevaba a que solo funcione como un editor de texto remoto, que no es necesariamente malo, pero se pierden gran parte de las funcionalidades.

2. Si el punto anterior no fuera un problema, además ocurre que VS Code consume demasiada cantidad de recursos para el nodo de login del Cluster, llevando a la posibilidad de que los administradores adviertan por excesivo uso de este.




---
### Fuentes:
- https://github.com/microsoft/vscode-remote-release/issues/1722#issuecomment-837902373 : Muestra un flujo de trabajo típico para un Cluster HPC con VS Code Server.
- https://github.com/cdr/code-server/issues/2341#issuecomment-802037795 : Explica cuáles versiones de las extensiones son útiles.