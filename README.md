# Ejemplo de publicación de datos de plc a InfluxDB y visualización con Grafana desde TwinCAT HMI

## Descripción

El proyecto publica unas señales generadas desde el plc, una señal de dientes de sierra y otra senoidal. Para publicar hacia InfluxDB se usa la function TF6420 DatabaBase Server de TwinCAT. Esta funcionalidad permite conectar con bases de datos en modos diferentes. El utilizado en este ejemplo es el modo 'Autolog'. Esto permite seleccionar qué símbolos de plc publicar e indicar el intervalo de publicación.

![image](./capturas/Captura_05.png)

## Requisitos

* Testeado con las siguientes versiones:
  * TwinCAT 4026.20
  * Función TF6420 v34.37.2
  * HMI Engineering/Server v14.13.1
  * InfluxDB 2 v2.8.0
  * Grafana v13.1

* Licencias necesarias (en todas ellas las licencias Trial son válidas):
  * TF1200 PLC
  * TF6420 Database
  * TF2000 HMI

## Configurar TF6420 Database

* Proyecto Database

Se añade a la solución del proyecto de TwinCAT un proyecto TwinCAT Conectivity Database

![image](./capturas/Captura_04.png)

Aquí en este punto agregamos un elemento Autolog. Es aquí donde se configuran los símbolos de qué plc a publicar, si se envian de manera cíclica o al cambiar y el buffer de envío si se pierde la conexión con el plc.

![image](./capturas/Captura_07.png)

* Seleccionar target Database

Aquí indicamos con la ruta AmsNetId la dirección del plc donde está instalada la función TF6420. Esto es así porque el plc que conecta con la base de datos no tiene porqué ser el mismo que tiene los símbolos a publicar.

* Seleccionar símbolos de plc

Desde el menú TwinCAT>Target Browser se puede seleccionar la ruta hacia el plc y los símbolos a publicar. Puede ser el mismo plc, otro o incluso varios.

* Creación de la tabla

Desde el menú TwinCAT>Database>Query editor se puede testear la conexión hacia la base de datos. También es posible crear la tabla y la estructura necesaria para publicar los datos. En este caso creamos una tabla llamada **Tabla3** con la estructura de la imagen, según los símbolos que vayamos a publicar. 

![image](./capturas/Captura_00.png)

Una vez creada la estructura, desde **DBtable** indicamos los campos de la estructura como se relacionan con los símbolos de plc.

Se puede configurar para que la publicación se haga de manera automática al arrancar el programa de plc o bien controlándolo desde el método Start() del bloque FB_PLCDBAutologEvt. En este ejemplo se hace de esta última manera activando la variable **bAutologStart**.

## Visualización de datos de Grafana embebido en TwinCAT HMI

Para incrustrar una visualización del dashboard de Grafana, en Content de HMI se añade un objeto iFrame. Desde el dashboard de Grafana se puede compartir el dashboard desde el menú Share public. Hay que copiar el texto html dentro del editor html del objeto iFrame del hmi.


Es importante indicar en el enlace http la dirección IP del servidor de Grafana, no permite indicar localhost.

## Diagnóstico

En el programa de plc se usa una llamada cíclica al método Status del bloque FB_PLCDBAutolog para consultar el estado de la conexión. Este método devuelve el número de peticiones y el código de estado de la conexión. Si se pierde la conexión con la base de datos el valor del parámetro hrErrorCode es 16#989103E8.

![image](./capturas/Captura_06.png)

Desde el menú TwinCAT>Database>Information Log se muestra un histórico de diagnóstico del runtime Database. Aquí se muestra cuando arranca y cualquier incidencia en el funcionamiento

![image](./capturas/Captura_08.png)

