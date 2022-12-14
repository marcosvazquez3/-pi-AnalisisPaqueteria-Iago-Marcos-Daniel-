Análisis de paquetería:
-------------------------------------------
ÚLTIMA ACTUALIZACIÓN: 18/07/2021
-------------------------------------------

Integrantes Grupo:
-------------------------------------------

iago.pallares@udc.es - Iago Pallares Tato

daniel.osama.gonzalez@uc.es - Daniel González Anwar

marcos.vazquez3@udc.es - Marcos Vázquez Campos 

Despliegue automático en Docker:
-------------------------------------------

IMPLEMENTADO. El contenedor se instala e inicia de forma totalmente automatizada con el comando:

./setup.sh

Funcionalidades:
-------------------------------------------


1) HOME: Filtrado de paquetes con pestañas desplegables, ordenación de los campos y paginación variable.
    Se puede filtrar por:
    Protocolos, IP Origen, IP Destino, MAC Origen, MAC Destino, Puerto origen y Puerto destino.
    También hay un buscador de caracteres al inferior de la página.

2) PCAPLIST: Listado de los ficheros pcap que el usuario puede cargar en cualquier momento para analizar. Distinguimos dos tipos de pcaps:
    Los que puede usar cualquier usuario, para probar la aplicación.
    Los ficheros que sube un usuario y solo él puede verlos y acceder a ellos.

3) UPLOAD: Subir un fichero que pasará a formar parte de los pcaps propios del usuario.

4) MAP: Geolocalización de las IPs públicas que aparecen en el pcap visualizándolas en un mapa mundi de Google Maps.

5) GRAPH: Grafo de red generado dinámicamente en el que se muestran las conexiones entre equipos y los protocolos que participan en dichas conexiones.

6) STATS: Se divide en tres tipos de gráficas que reflejan estadísticas de los datos presentados en nuestro modelo.
    - Histogramas:
        Para cada uno de los protocolos presentes en nuestro pcap, se comprueba la longitud del paquete IP (payload), ya que esto puede darnos información al respecto de si se presentan anomalías.
    - Lollypop charts:
        Para cada una de las IPs en el pcap, se representa el número de veces que aparece.
    - Pie chart:
        Para cada uno de los puertos en el pcap, se muestra su porcentaje de representación.

7) IPINFO: Tabla que presenta información de todas las IPs públicas extraídas del pcap.
    Sigue los mismos principios de paginación y ordenación que la función de filtrado de HOME.
    Se presentan datos de: Dominio, hostname, proveedor de internet, uso, puntuación personalizada del API de abuseIPDB de reputación.

8) REPORT: Miscelánea de datos de red extraídos del pcap mediante la librería de scapy, en la que, en el caso de haber información suficiente, se pueden observar:
    Gateway: Se extrae de peticiones DHCP y IGMPv3.
    Root bridge: Se extrae del protocolo STP.
    VLANs: Se extraen del protocolo Dot1q.
    Servidores: Listado de servidores HTTP.
    User Agents: Se extraen de peticiones HTTP.
    Cookies:  Se extraen de peticiones HTTP.
    Respuestas y peticiones DNS: Se extraen de peticiones y respuestas DNS.
    Netbios.

9) LOGIN/LOGOUT y REGISTER


Gestión de usuarios:
-------------------------------------------

IMPLEMENTADO. Es necesario hacer login para acceder a cualquiera de las funcionalidades de la aplicación, para eso será necesario registrarse primero.

Integración de APIs:
-------------------------------------------

Airtel IP: Geolocalización de IPs.

Google Maps: Visualización de las IPs geolocalizadas en el mapa de Google Maps.

Como alternativa a los APIs de Shodan y SafeBrowsing, debido a la imposibilidad de hacer uso de esos APIs por los límites impuestos o la calidad de la información, se ha optado por otra opción:

AbuseIPDB: Extrae información interesante a partir de direcciones IPs públicas (dominio, hostname, isp, usage), además de ofrecernos una puntuación personalizada de la fiabilidad de la IP (abuseScore).


Uso de lenguajes adicionales que no sean Python:
-------------------------------------------

Además de Python y HTML/CSS, para la realización de esta aplicación nos hemos apoyado en JavaScript y JQuery, concretamente con el uso de dos librerías:

Librería datatable:  Empleada para visualización, filtrado y ordenación de tablas.

Librería Jquery: Biblioteca para interactuar con el html y manipular DOM.

Uso de liberías externas además de Pandas/Numpy:
-------------------------------------------

Matplotlib: Generación de los histogramas, pie charts, lollypop charts y el grafo que se utilizan para las vistas 'Graph' y 'Stats'.

Scapy: Procesamiento y manejo de la información recogida de un fichero pcap, con métodos densos que permiten extraer información de manera más ordenada y sencilla. Se le da uso principalmente en AnalisisPaqueteria/pcapinspector/core/network.py

Networkx: Métodos que ayudan en la generación de grafos dinámicos.

Requests: Facilita el manejo de la integración con las APIs ofreciendo métodos para procesar las peticiones y respuestas, además de comprobar de manera sencilla el código de estado.

Y también se usa un programa externo:

tshark: Consiste en una aplicación similar a Wireshark que se usa desde terminal, la invocamos desde línea de comandos para generar el fichero temporal csv a partir del pcap original.

Uso de pandas:
-------------------------------------------

Para una optimización eficiente de las funcionalidades y del cargado de los pcaps:
Los datos extraídos del archivo .pcap a partir del programa tshark, que se almacenan en un fichero temporal con formato csv, se procesan mediante pandas para añadirlos al modelo. La recuperación de los datos persistentes en el modelo también se recogen y se procesan con pandas nuestras funciones, distribuidas a lo largo de todos los ficheros de la aplicación, sobre todo en en los ficheros del directorio AnalisisPaqueteria/core.

También se utiliza Pandas para manejar los datos a la hora de generar las gráficas y estadísticas que se presentan con la aplicación.

Su uso también tiene presencia para extraer información concreta que nos interesa a la hora de procesar las vistas e integrar datos con APIs.

Tests:
-------------------------------------------
IMPLEMENTADO. En la aplicación catálogo se han realizado una serie de tests que prueban el modeloy  las diferentes APIs.

Gestión de errores y concurrencia:
-------------------------------------------

Se ha tenido en cuenta que la aplicación debe ser concurrente y para ello cada usuario tendra un directorio propio para evitar colisiones.

Para la gestión de errores, se han identificado diferentes problemas y se han subsanado, entre ellos:

Cuando se intenta subir un archivo cuyo nombre tienen caracteres especiales, se sustituyen por barras bajas y luego se sube.
Cuando se intenta subir un archivo cuyo nombre ya existe en los ficheros del usuario personal, se añaden caractereres al final para que no existan colisiones.
Cuando se intenta subir un archivo que no tiene extensión pcap, cap, o pcapng no se sube.
Cuando se intenta subir un archivo que que tiene extensión pcap, cap o pcapng pero no debería tenerla, no representa ningún tipo de información y la aplicación sigue funcionando perfectamente.
Cuando las APIs integradas devuelven un tipo de respuesta inesperado (por ejemplo, por haber hecho más consultas del límite permitido en la versión gratuita) se gestiona.
Cuando se sube un pcap que no posee de IPs públicas (aunque tenga privadas), eso se controla en las vistas. 
Se han gestionado todos los problemas derivados de logins y registros.

Consideraciones adicionales:
-------------------------------------------

A pesar de que la aplicación está diseñada de forma que es capaz de procesar y tratar ficheros pcap muy grandes, por motivos de optimización y de tiempo de respuesta, no es una práctica recomendable.
La extracción de los datos de las APIs elegidas y el procesado de los datos cargados en el modelo toman tiempo, por eso hemos decidido limitar el tamaño de los pcaps que se pueden subir a 2.5MB.
