# ManagEV-GRID

Aplicación ManagEV que sirve para monitorear la operación de vehículos eléctricos.
Este repositorio contiene el código fuente de la aplicación, los dispositivos de monitoreo IoT y la investigación.

# Guía de instalación para desarrolladores

Las instrucciones presentes a partir de este momento se deben seguir de manera secuencial ya que están pensadas para instalar el software desde cero.

## 1. Configuración del ambiente virtual

Un ambiente virtual de python es un entorno aislado (espacio de rtabajo) que permite instalar y utilizar diferentes versiones de paquetes de Python y sus dependencias, sin afectar a otro proyectos de Python que coexistan en un mismo equipo. Véamos como configurarlo:

### Paso 1

Clona este repositorio en alguna carpeta que tengas en tu computadora, luego, dirigete a la careta donde clonaste la información. Si no sabes como clonar un repositorio aquí hay un enlace que podría ser de ayuda: [https://docs.github.com/es/repositories/creating-and-managing-repositories/cloning-a-repository](https://docs.github.com/es/repositories/creating-and-managing-repositories/cloning-a-repository)

### Paso 2

Debes crear un nuevo ambiente virtual de python donde instalaremos los paquetes necesarios para el correcto funcionamiento del software. Si no sabes como crear un ambiente virtual de python el siguiente enlace puede interesarte: [https://docs.python.org/es/3/tutorial/venv.html](https://docs.python.org/es/3/tutorial/venv.html)

### Paso 3

Después de crear el nuevo ambiente virtual debemos activarlo para poder empezar a trabajar dentro de él:

- En windows esto se hace con el siguiente comando
    
    `.\\nombre_del_entorno_virtual\\Scripts\\activate`
    
- En sistemas operativos unix esto se hace con el siguiente comando
    
    `source nombre_del_entorno_virtual/bin/activate`
    

### Paso 4

Ahora ejecutaremos el siguiente comando para instalar la versión 58 de setuptools: 

`pip install setuptools==58`

Esto es necesario debido a que algunos de los paquetes que requerimos solo pueden ser instalados bajo esta versión.

### Paso 5

Es hora de instalar los paquetes que va a usar nuestro software, hacemos esto ejecutando el siguiente comando por consola: 

`pip install -r requirements.txt`

Con esto habríamos terminado de configurar el ambiente virtual y lo tendríamos listo para correr.

### IMPORTANTE

Ya que tenemos nuestro ambiente virtual debemos saber que cada que vayamos a ejecutar la aplicación debemos activarlo como hiciste en el paso 3, ya que es dentro de este ambiente donde viven todos los paquetes que instalamos, y sin estos el sistema no funciona.

## 2. Configuración del servidor en putty

Putty es una aplicación de escritorio que se utiliza para conectarse a servidores remotos, cosa que viene bien para conectarnos a la base de datos remota que maneja este sistema. Véamos como configurarlo:

### Paso 1

Instalar putty y abrirlo. Debido a que la instalación en cada sistema operativo es distinto y el público objetivo de este software son personas con conocimientos básicos en computadores, no se explicará como instalarlo, sin embargo, puedes utilizar el siguiente enlace como referencia: [https://www.puttygen.com/download-putty](https://www.puttygen.com/download-putty)

### Paso 2

Digirse al apartado "Data" que se encuentra en la pestaña de "Connection" y en el cuadro de texto de Auto-login username, escribir "root".

### Paso 3

Dirigirse a la pestaña "Session" y replicar las siguientes configuraciones:

- Host Name (or IP address): 104.236.94.94
- Port: 22
- Connection type: SSH
- Closed window on exit: Only on clean exit

Por último, escribir el nombre que queremos que tenga la sesión (en este caso debes colocar 'ManagEV') y presionar el botón "Save". Debe de verse de la siguiente manera:

### Paso 4

Antes de dar ingresar a la sesión y comenzar a configurar el servidor, debemos ejecutar el script de python llamado ‘hostnameScript.py’ que se encuentra en la carpeta scripts dentro de managev_app, al hacerlo obtendremos un mensaje como el siguiente:

**mysql.connector.errors.DatabaseError: 1130: Host '[hostname]' is not allowed to connect to this MySQL server**

De este, extraeremos el numero hostname que se encontrará en el espacio donde dice [hostname], debemos copiarlo porque lo usaremos en posteriores pasos.

Esto no se puede realizar usando la red de EAFIT, se debe usar una red doméstica.

### Paso 5

Ingresar a la sesión, seleccionandola y presionando el botón "open". Luego, aparecerá una consola (bash) donde debes ingresar la contraseña del servidor la cual es "2021.ManagEV". No se preocupe si no ve que no aparecen los caracteres en consola debido a la seguridad que se tiene a la hora de introducir contraseñas.

### Paso 6

Al entrar debemos ubicarnos en la carpeta del proyecto haciendo `cd monitoreogrid`, luego, se debe actualizar el servidor ya que pueden haber cambios que hayan hecho otros desarrolladores que no hayan sido reflejados en este. Para esto ejecutamos los siguientes comandos:

`tmux attach
git pull
flask db upgrade`

### Paso 7

Después, se deben dar permisos de la base de datos y del servidor al equipo con el que se está trabajando, esto se logra ejecutando los siguientes comandos:

`sudo mysql
create user  'admin'@'[hostname]' identified by 'actuadores';
grant all on monitoreodb.* to 'admin'@'[hostname]';
GRANT CREATE, ALTER, DROP, INSERT, UPDATE, DELETE, SELECT, REFERENCES, RELOAD on *.* TO 'admin'@'[hostname]' WITH GRANT OPTION;
exit
sudo ufw allow from [hostname_number] port 3306`

## 3. Configuración de la base de datos mysql local

### Paso 1

Instalar MySQL Shell o MySQL Command-Line Client. Al igual que putty no se darán las instrucciones para instalarlo ya que se espera que las personas siguiendo esta guía tengan la capacidad de buscar e instalar esto de manera independiente.

### Paso 2

Luego se debe crear un usuario mysql con las siguientes características:

Usuario: admin → El host del usuario debe ser localhost

Contraseña: 5Actu_adores

Esto se puede lograr de distintas maneras, ya sea desde consola con alguno de los dos programas mencionados en el paso anterior, o también desde mysql workbench. En internet hay mucha información al respecto de la creación de usuarios en mysql.

### Paso 3

Luego, se debe crear la base de datos monitoreodb. Esto se logra ejecutando la siguiente query SQL: `CREATE DATABASE monitoreodb`. Esto se hace por la razón de que al estar desarrollando localmente, la aplicación busca una base de datos local llamada monitoreodb en mysql, es decir, no se usa la base de datos que está montada en el servidor.

### Paso 4

Por último, se debe activar el entorno virutal ejecutar dos comandos en la ubicación donde clonamos el repositorio, con la finalidad de clonar las tablas de la base de datos del servidor a la base de datos local que hemos creado en el paso anterior, estos comandos son los siguientes:

`flask db migrate`

`flask db upgrade`

En caso que aparezca que el usuario no tiene permisos sobre la tabla monitoreodb, se debe acceder a mysql con el usuario administrador y otorgarle todos los permisos al usuario creado.

## 4. ¿Cómo ejecutar el software?

Después de haber seguido los pasos anteriores, y haber configurado todo el sistema para que este funcione correctamente, lo que debemos hacer cada que querramos ejecutar el programa es:

1. Activar el entorno virtual de igual manera en que se hizo en el paso 3 de la configuración del entorno virtual. Esto se debe hacer ya que es en este donde tenemos descargados todos los paquetes necesarios para que esta pueda correr de manera correcta.
2. Ubicarse en la carpeta donde tenemos el repositorio clonado y ejecutar `py [app.py](http://app.py)`

## 5. Notas finales

Listo, ya habríamos terminado de instalar y configurar las cosas necesarias para que nuestro software funcione y podamos comenzar a desarrollar, aún así hay un par de cosas que estaría bien recordarlas.

### Refrescar el servidor

Recordar que el servidor debería tener los cambios más recientes, siempre y cuando los cambios hechos no dañen la lógica del proyecto y genere errores. Por ende, si se desarrollan nuevas funcionalidades, corrigen errores o demás actividades importantes en el desarrollo del software, considerar necesario una actualización del servidor para subir los cambios.

# Solución de problemas del servidor

En caso de errores o módulos caídos ya ha ocurrido que algún demonio (servicio corriendo de fondo en el servidor) se ha encontrado caído. Por ende, cuando ocurra un problema de este tipo, verificar el estado del demonio haciendo: `sudo systemctl status monitoreo` En caso de que se encuentre caido, intentar reiniciarlo haciendo `sudo systemctl restart monitoreo`.