# Clima-por-API-MySQL
En este repositorio se describe el uso de MySQL para hacer una base de datos que almacene los datos del clima.

## Material necesario
Para poder realizar necesario es necesario tener instalado:
- [Node.js](https://github.com/nodesource/distributions/blob/master/README.md) Usar la versión LTS v16x e instalar los build tools.
- [Node-Red](https://nodered.org/docs/getting-started/local)
- [Ubuntu 20.04](https://ubuntu.com/download/desktop/thank-you?version=20.04.2.0&architecture=amd64)

## Material de referencia
Para hacer las instalaciones requeridas para este ejercicio se siguieron los pasos de los cursos siguientes:
- [Instalación de Ubuntu 20.04 en VirtualBox Windows](https://edu.codigoiot.com/course/view.php?id=812)
- [Instalación de NodeRed en Ubuntu 20.04](https://edu.codigoiot.com/course/view.php?id=817)
- [Introducción a NodeRed](https://edu.codigoiot.com/course/view.php?id=278)

## Instrucciones
### Instalar MySQUL
1. Abrir la terminal y escribir el siguiente comando:
>sudo apt update
2. Una vez que se haya ejecutado, escribir el siguiente comando:
>sudo apt install mysql-server
3.  Arrancar MySQL Server se usa el siguiente comando 'sudo mysql'

### Parte MQTT
1. Abrir la terminal y escribir el siguiente comando: **node-red**.
>Nota: Es importante escribir este comando en la terminal ya que si no se hace no se podrá trabajar con Node Red.
2. En el navegador escribir: **localhost:1880**, se abrirá Node Red.
3. Agregar el bloque **mqtt in** y editar el server colocando *local host*. Escribir el nombre del tema el cual es: codigoIoT/Mor/mqtt/flow4.
4. Agregar un bloque **json** y en la sección de *Action* colocar la opción *Always convert to JavaScript Object*.
5. Agregar dos bloques **function** y unirlos a las salida del bloque **json**. El primer bloque se llamará temperatura y contendrá el siguiente código:
>msg.payload = msg.payload.temp;

>msg.topic = "Temperatura";

>return msg;

El segundo bloque se llamará humedad y contendrá el siguiente código:
>msg.payload = msg.payload.hum;

>msg.topic = "Humedad";

>return msg;
6. A la salida de cada uno de los bloques, insertar dos bloques de dashboard de tipo **gauge**. Para configurar cada uno de los bloques **gauge** se debe crear un grupo para casa variable, en este caso, un grupo para temperatura y otro para la humedad. Configurar las unidades y rangos de valores que tendrá el gauge.
7. Para hacer un Histórico que grafique ambos valores en una sola gráfica, se debe insertar un bloque **gauge** y crear un grupo para este gráfico.
8. Abrir la terminal y ejecutar el siguiente comando:
>mosquitto_pub -h localhost -t codigoIoT/Mor/mqtt/flow4 -m '{"ID":"NOMBRE","temp":TEMPERATURA,"hum":HUMEDAD}'
TEMPERATURA y HUMEDAD son valores numéricos.

### Parte API
1. Agregar un bloque **inject** y configurarlo para que mande datos cada un minuto.
2. Unir al bloque **inject** un bloque **http request** y configurarlo con la URL de Opemweather, como se muestra a continuación:
>http://api.openweathermap.org/data/3.0/onecall?lat={lat}&lon={lon}&units={units}
En *lat* y *lon* se debe colocar la longitud y latitud del lugar donde se obtendrá la temperatura, y en *units* se especificará en qué unidades nos mostrará la información.
3. Unir un bloque **JSON** al bloque **http request** y configurarlo en *Always convert to JavaSript Object*.
4. Unir dos bloques **function** al bloque **JSON**, uno corresponderá a la temperatura y el otro a la humedad.
Al bloque de temperatura, agregarle el siguiente código:
>global.set("tempAPI", msg.payload.main.temp);

>msg.payload = msg.payload.main.temp;

>msg.topic = "Temperatura";

>return msg;
Al bloque humedad, agregarle es siguiente código:
>global.set("humAPI", msg.payload.main.humidity);

>msg.payload = msg.payload.main.humidity;

>msg.topic = "Humedad";

>return msg;
5. Al final de cada bloque agregar un bloque **gauge** para observar la información.

### Parte enviador
1. Agregar un bloque **inject** y configurarlo para que envíe la información cada minuto.
2. Añadir un bloque **function** y escribir el siguiente código:
>msg.payload = '{"id":"Nombre, Ciudad, Estado","temp":' + global.get("tempAPI")+',"hum":' + global.get("humAPI") +'}';

>return msg;
3. Agregar un bloque **mqtt out** y en *server* escribir la dirección del broquer público en donde se enviará la información, y en *Topic* escribir: codigoIoT/flow5/mqtt/clima.

### Parte escuchador
1. Agregar un bloque **mqtt in** y en *server* escribir la dirección del broquer público en donde se enviará la información, y en *Topic* escribir: codigoIoT/flow5/mqtt/clima.
2. Unir un bloque **JSON** al bloque **mqtt in** y configurarlo en *Always convert to JavaSript Object*.
3. Unir dos bloques **function** al bloque **JSON**, uno corresponderá a la temperatura y el otro a la humedad.
Al bloque de temperatura, agregarle el siguiente código:
>msg.topic = msg.payload.id;

>msg.payload =msg.payload.temp;

>return msg;

Al bloque humedad, agregarle es siguiente código:
>msg.topic = msg.payload.id;

>msg.payload = msg.payload.hum;

>return msg;
Al final de cada bloque **function** agregar un bloque **chart** para observar la información de las personas que envíen datos.
5. Finalmente, dar click en el botón **Deploy** para que se actualicen los cambios. 

## Resultados
Una vez completados los pasos anteriores se deberá ver abrir el dashboard, como se muestra a continuación:

![Captura de pantalla]()

El flow en Node Red debe verse como el mostrado a continuación:

![Captura de pantalla]()

## Evidencias
[Evidencia MySQL Clima]()

## Créditos
Este ejercicio fue basado en los ejercicios que se encuentran en el repositorio [flow5-NodeRed]()

Documentación realizada por [Karen Rosas](https://github.com/KarenRosas49)