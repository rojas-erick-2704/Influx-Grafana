# Implementación de InfluxDB y Grafana para monitoreo de Sensores en Windows 

## InfluxDB
InfluxDB es una base de datos de series de tiempo, diseñada para almacenar y consultar **datos de series de tiempo**, está diseñada para tener un alto rendimiento tanto en velocidad como en disponibilidad.

Se utiliza comúnmente para monitoreo de infraestructura, como servidores Linux por ejemplo. Otro uso común de InfluxDB es para monitoreo de IoT, que es nuestro caso. 

InfluxDB tiene un lenguaje de consulta específico que es llamada InfluxQL, que se utiliza para hacer consultas y lo que es más importante para nuestro objetivo, hacer gráficos en Grafana. 

Influx es compatible con varias herramientas de visualización y análisis de datos, como Grafana, que es el que vamos a utilizar para graficar los datos que recolectemos de los sensores.  

**Datos de series de tiempo**

Estos tipos de datos se podrían resumir como aquellos que se recolectan u observan  en intervalos de tiempo establecidos(horas, días, semanas, meses, años) 

Algunos ejemplos sencillos de este tipo de datos son: 

* Precio de acciones: los datos de las acciones se registran cada segundo, minuto, hora o día, ya que estos varían en el tiempo. 
* Tráfico de web: el tráfico de red que se registre día a día en un sistema.
* Sensores IoT: datos como la temperatura, Co2, humedad y muchos más son datos de series de tiempo que se registran en intervalos de tiempo 

### Instalar InfluxDB 
Lo primero que vamos a hacer es instalar InfluxDB, para ello, nos vamos a dirigir al siguiente link: 
[Download InfluxDB](https://portal.influxdata.com/downloads/)
![](https://i.imgur.com/6iaD6y3.png)

Si no quiere descargar el programa desde PowerShell, simplemente tiene que tomar el url que hay entre "wget" y "-UseBasicParsing" y ponerlo en el buscador de cualquier navegador. Hablamos de un link como este: https://dl.influxdata.com/influxdb/releases/influxdb2-2.7.0-windows-amd64.zip

> **Nota: el link puede variar en el tiempo**

### Ejecutar InfluxDB en Windows 
Una vez hemos descargado Influx, lo que vamos a hacer es descomprimir los archivos, no hay una ruta específica donde debe ponerlos, puede utilizarlos incluso en el escritorio de la computadora. Una vez hemos descomprimido los archivos, el siguiente paso es abrir un "Command Prompt"(conocido como CMD) en la carpeta donde está el Influx: 
![](https://i.imgur.com/uwKd12w.png)

En el CMD, lo único que vamos a ejecutar es el archivo de influxd.exe: 
![](https://i.imgur.com/47VGwwg.png)

Una vez hecho esto, el programa se comenzará a ejecutar, y obtendrá un resultado similar a este: 

![](https://i.imgur.com/cbEDNr0.png)

Como puede ver, aquí ya podemos notar que para acceder a nuestra aplicación de influx, lo que tenemos que hacer es ir a la IP de la máquina que esté corriendo Influx con el puerto 8086, yo lo estoy corriendo en mi máquina de forma local, por lo que el link sería: 127.0.0.1:8086

### Configuración inicial de InfluxDB

Después de instalar y ejecutar Influx, el siguiente paso es hacer una configuración inicial, cuando ingresemos a la URL, por el puerto 8086, lo primero que veremos será algo como esto: 
![](https://i.imgur.com/ejjRhPu.png)

Le damos click a "Get Started" y procederemos a llenar los datos que nos soliciten: 
![](https://i.imgur.com/TgGuQOL.png)

Los datos como el "Initial Organization Name" y el "Bucket Name" son importantes, porque son los que nos van a servir para configurar algunos parámetros que vamos a utilizar en nuestro código para enviar los datos de los sensores a Influx. 
> **Nota: el nombre del bucket tal vez no sea tan importante pues podremos crear y eliminar los que queramos según lo necesitemos.**  
#### Buckets
Un bucket no es más que un contenedor donde se almacena y organizan los datos que estemos guardando en Influx. Es un espacio de almacenamiento para agrupar datos relacionados. Estos se pueden configurar según las necesidades del usuario. 

Cuando hayamos configurado los datos, le damos click a "Continue" y luego en la siguiente pantalla a "Quick Start":
![](https://i.imgur.com/MFnvmoz.png)

Estos nos llevará a la página principal de Influx y habremos terminado con el proceso de instalación de InfluxDB:

![](https://i.imgur.com/ikUFhP4.png)

## Grafana 
En términos sencillos, Grafana es una aplicación de visualización de datos, estos datos pueden venir de diversas fuentes y pueden ser utilizados para crear panales con diversos tipos gráficos y visualizaciones en tiempo real, es decir, que nos viene perfecto para visualizar y monitorear datos de series de tiempo. 

Entre las diferentes fuentes de datos que maneja Grafana podemos encontrar bases de datos, servicios web, herramientas de monitoreo y demás, en la que por supuesto entra InfluxDB. En términos generales, Grafana es una de las mejores opciones que tenemos para ver el rendimiento y estado de un sistema, ya que además de su gran personalización, es OpenSource. 

### Instalar Grafana
para instalar Grafana, vamos a dirigirnos a su página oficial, que lo puede hacer mediante el siguiente enlace: [Download Grafana](https://grafana.com/grafana/download?platform=windows)
![](https://i.imgur.com/PFxOXEZ.png)
Damos click donde dice "Download the installer" y comenzara la descarga del ejecutable. 

La instalación de Grafana es la típica de cualquier programa que haya instalado antes, no hay muchas características relevantes que se deban mencionar, puede proceder de forma normal. 
![](https://i.imgur.com/7WDaYBZ.png)
Una vez instalado Grafana, lo que debe hacer a continuación es dirigirse a la dirección IP de la máquina donde lo haya instalado, al igual que con Influx, si lo está haciendo en su máquina local sería la dirección IP 127.0.0.1 con el puerto 3000: 127.0.0.1:3000

Al instalar por primera vez, deberá iniciar sesión con las credenciales por defecto de Grafana que son admin/admin y se le pedirá que las cambie por unas nuevas. Una vez hecho esto, ya tendrá acceso a Grafana
![](https://i.imgur.com/QzYBCLq.png)
Con esto daremos por terminada la instalación de Grafana.

## Conectar Grafana con InfluxDB 

### Del lado de Influx
Una vez instalado Grafana e Influx, el siguiente paso que queda es conectarlos. 

Esto es bastante sencillo, lo primero que debemos hacer es dirigirnos a InfluxDB y buscar el apartado de "API Tokens":
![](https://i.imgur.com/tIJV0MV.png)

> ### API Tokens
> Un API Token no es más que un tipo de autenticación y autorizaion que usa Influx para controlar el acceso de usuarios a los recursos. Tanto si queremos guardar información en alguno de nuestros buckets como si deseamos leer la informacion que estemos guardando desde una apliacion externa a Influx, la unica forma de lograr esto es mediante un API Token.   


Una vez aquí, lo que vamos a hacer es darle click a "GENERATE API TOKEN"
![](https://i.imgur.com/ixZJU5I.png)
Donde tendremos dos opciones de Token, uno de Lectura/Escritura y uno de acceso total, según nuestras necesidades, yo para este ejercicio voyy a hacer uno de acceso total. 
![](https://i.imgur.com/AOdBzFa.png)
Se nos pedirá una descripción, donde podremos poner cualquier cosa. 
![](https://i.imgur.com/qL68bUr.png)
Y al darle click a "Save" ya habremos creado nuestro Token, simplemente le damos click sobre su nombre, y veremos nuestro Token como tal con sus respectivos permisos. 
![](https://i.imgur.com/aYgKIqE.png)
Le damos copiar y con esto ya hemos finalizado del lado de Influx.


### Del lado de Grafana
Ahora tenemos que dirigirnos a Grafana:
![](https://i.imgur.com/8xjvn1D.png)
En Grafana debemos ir a "Connections" y luego a "Connect data".
![](https://i.imgur.com/1g3FPaM.png)
Buscamos Influx y le damos click a la opción que dice "InfluxDB".
![](https://i.imgur.com/N7f0aIR.png)
Aquí, simplemente debemos darle click a "Create a InfluDB data source".
![](https://i.imgur.com/OvXkR8b.png)
Una vez entramos al panel de creación de la conexión con Influx, rellenamos algunos campos como el nombre y escogemos la opcion "Flux" en "Query Language".
![](https://i.imgur.com/YPxg1tW.png)
Aquí se nos pedirán el usuario y la contraseña que son las de nuestro usuario de Influx. 

Las credenciales llamadas "InfluxDB Details"  como pueden ver, son parámetros que creamos en los pasos anteriores de Influx, como el nombre de la organización y el nombre del bucket, en el caso del bucket, si no le ponemos uno en especifico, tomará como "default" el que creamos originalmente, aunque, como el API Token tiene acceso total, tendrá aceso a todos los buckets que hayamos creado en Influx. El otro dato importante es precisamente el token, que es el que creamos en pasos anteriores.
![](https://i.imgur.com/jZCwzxF.png)

Una vez configurados todos estos datos, le damos click a "Save and test". 

![](https://i.imgur.com/8XvCULi.png)
Si todos los datos son correctos, nos dirá que la fuente de datos está trabajando correctamente y el número de buckets que encontró.

Con esto, ya logramos establecer la conexión entre InfluxDB y Grafana.


## Enviar datos a Influx 

Para este punto, se supone que ya debe estar enviando y recolectando sus datos a InfluxDB, hay mucha documentación sobre como hacer esto. Para no ahondar mucho en este aspecto, vamos a ver los aspectos más importantes que debe saber para escribir un código que envíe datos a InfluxDB.

![](https://i.imgur.com/GPl0Vk8.png)


La imagen anterior es una pequeña muestra de cómo funciona el envío de datos a Influx.


## Gráficos en Grafana 

Ahora está todo listo para crear un dashboard en Grafana que nos permita visualizar la información que estamos recolectando. 

![](https://i.imgur.com/sRldLEI.png)


Para ello nos vamos a la interfaz web de Grafana y nos dirigimos al apartado de "Dashboards" 
![](https://i.imgur.com/vPoY4xg.png)

Aquí, le vamos a dar a "New" y luego a "New Dashboard" para crear uno nuevo.

![](https://i.imgur.com/VPzv1ls.png)

La imagen anterior es la ventana principal de un Dashboard, para crear una visualización, le vamos a dar click a "+ Add visualization"

![](https://i.imgur.com/wtcsRM8.png)

Ahora, estamos en la interfaz para crear una visualización, en la esquina inferior izquierda, al darle click al apartado de "Data source", se nos abrirán todas las conexiones que hemos configurado, como la que habíamos creado anteriormente, en mi caso es "PruebasGrafico", esto es importante porque cada conexión está asociada a los buckets donde estemos enviando información. 

![](https://i.imgur.com/VmF9hJc.png)

Para escoger un gráfico, podemos ir al panel lateral derecho y dar click en el apartado que se ve en la imagen anterior.

![](https://i.imgur.com/HHIQ4Hr.png)

La anterior es una lista de los gráficos que podemos crear en Grafana, yo voy a escoger el de tipo "Gauge".

Lo siguiente a realizar, es crear el código que vamos a utilizar para extraer la información del bucket en Influx

![](https://i.imgur.com/O4wtIDA.png)

El código es el siguiente: 

```
from(bucket: "Pruebas")
  |> range(start: -15m)
  |> filter(fn: (r) =>
    r._measurement == "Humidity" and
    r._field == "humidity"
  )
```

Donde tenemos: 

* from(bucket: "Pruebas"): el nombre del bucket en InfluxDB donde estamos guardando la información
* range(start: -15m): rango de datos que se va a consultar, en este caso los últimos 15 minutos
* resto del código: es el filtro que vamos a crear para los datos, porque un mismo bucket puede almacenar distintas medidas, como en el código que le mostré en pasos anteriores
    * r._measurement == "Humidity": valor de la etiqueta
    * r._field == "humidity": valor del campo

![](https://i.imgur.com/uNbNOzg.png)

Un tip para llenar esta información, es que si nos vamos a InfluxDB, y buscamos el bucket donde estamos guardando la información, podremos ver todos los detalles para completar los datos en Grafana. 

En el panel de la derecha podremos configurar cosas como: 

![](https://i.imgur.com/hKYzbhf.png)

El nombre y la descripción de la visualización.

![](https://i.imgur.com/ll5uvIj.png)

Algunas opciones para configurar el valor que vamos a mostrar. 

![](https://i.imgur.com/roQCj7A.png)

La unidad del dato que estamos configurando y como podrá notar, muchas más configuraciones

![](https://i.imgur.com/jLL4XGZ.png)

Y así es como se crea una visualización básica con Grafana, el código que se utilizó, suele ser el mismo para varios tipos de graficos. 

![](https://i.imgur.com/3jsywxh.png)

Como el de arriba que es para la temperatura. 

Esto es, en grandes rasgos, cómo funciona una solución de monitoreo básica, usando InfluxDB y Grafana. 

