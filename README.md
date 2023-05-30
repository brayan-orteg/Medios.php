# Medios.php
Tarea de explicar 3 medios a nivel de software

Pooling
La más simple de todas las formas es el pooling. Es muy simple, para saber si algo paso, tenemos que preguntar constantemente. La estrategia del pooling consiste en consultar al servidor en un periodo constante de tiempo, usualmente muy pequeño, digamos cada 3 segundos.
En términos más técnicos, implementar pooling significa realizar peticiones al servidor cada par de segundos, para consultar información nueva.
Las ventajas del pooling son:
    Es extremadamente simple, realizar peticiones constantes cada cierto periodo de tiempo.
    No requiere de tecnologías especiales más que AJAX, para poder hacer las consultas.
    Muy fácil de implementar, sólo colocas tus consultas en un intervalo y listo.
Así como la implementación es muy simple, las desventajas de usar pooling son también muy claras:
    Sobrecargas al servidor, es muy probable que muchas de las peticiones reciban como respuesta nada, en caso de que no haya información nueva que comunicar, sin embargo, las peticiones siguen ejecutándose y el servidor tiene que responderlas.
    Pequeña latencia. Si tu aplicación es de respuesta crítica, considera que con el pooling siempre habrá un ligero retraso entre el momento en el que la información se produce y el momento en el cliente se entera, si por ejemplo, mandas una petición cada 10 segundos, este será el tiempo máximo en que la información podría llegar retrasada.
¿Cuándo usar pooling?
Si en tu aplicación no es crítica la comunicación inmediata, por ejemplo un chat, es una buena idea respaldarse en la simplicidad del pooling. Otras aplicaciones como, por ejemplo, un juego multijugador serían imposibles con un retraso en la comunicación de varios segundos.
Usa pooling, también, cuando quieras dar soporte a navegadores viejos, como Internet Explorer, que no soportan el uso de tecnologías modernas como las WebSockets.
Considera también usar pooling por su simplicidad, especialmente si la parte de actualización en tiempo real no es crítica para tu aplicación, es decir, es sólo una mejora, pero no el centro de la aplicación. La simplicidad también es aliada de equipos pequeños de trabajo, o con poca experiencia en el ámbito.


Long pooling
Al notar que muchas de las respuestas que se reciben las peticiones de una implementación con Pooling, son respuestas vacías porque el servidor no tiene nada nuevo que comunicar, se introdujo una mejora a dicha estrategia, la llamaron Long Pooling.
El flujo de una implementación con long pooling es la siguiente:
    El cliente envía un mensaje Http al servidor consultando información nueva.
    El servidor tiene de dos:
    2.1 Si existe información nueva que reportar, la envía inmediatamente.
    2.2 Si no existe información nueva que reportar, mantiene esta conexión Http en espera y abierta, hasta que exista algo que reportar, entonces envía la información al cliente y cierra dicha conexión.
    El cliente recibe respuesta de su mensaje con datos nuevos, ya sea tan pronto como la envió o luego de haber esperado por bastante tiempo a que hubiera algo nuevo que reportar.
    El cliente manda una nueva petición hasta que la anterior fue contestada, así, esta nueva recibirá respuesta hasta que haya nuevos datos.
La clara diferencia entre el Long Pooling y el Pooling es que en esta mejora de la estrategia anterior, no se envía peticiones constantes, más bien se envía una inicial y las siguientes sólo se envían hasta que hubo una respuesta previa, con datos actualizados. Esto reduce drásticamente la cantidad de peticiones que enviamos hacia el servidor.
Las ventajas del Long Pooling son las siguientes:
    Todas las del pooling, a final de cuentas es casi la misma metodología.
    Menos peticiones que el pooling, por lo tanto, un servidor con menos carga y más eficiente.
    Mejora significativa en qué tan rápido recibimos los datos nuevos, ya que para cuando estos se crean, ya hay una conexión esperando para que se envíen al cliente.
Las desventajas son más difíciles de identificar, pero sucede:
    Seguimos realizando y abriendo peticiones Http, aún cuando estas quizás nunca reciban respuesta.
    Algunos servidores no permiten que las conexiones Http permanezcan abiertas por mucho tiempo, por lo que cada que se cierran, debemos crear peticiones nuevas, aumentando la carga del servidor.
¿Cuándo usar long pooling?
Preferentemente, esta es la estrategia que yo te recomendaría que usaras si quieres integrar un chat en tu aplicación, habilitar notificaciones en tiempo real, etc. El long pooling es como ese Hotel que no es tan caro, pero lo usas porque la relación calidad/precio es la mejor, no es lujoso, pero es bueno y es barato.
Esta estrategia es simple y de mejor rendimiento y tiempo de respuesta que el pooling. En realidad, tendrías que tener una carga de usuarios muy reducida para poder implementar pooling sin que tu mismo te provoques un ataque DOS (Denegation of Services) con tantas peticiones enviadas al servidor. El long pooling, por otro lado, es una estrategia ampliamente usada en la industria.
Y sí, te recomendaría Long Pooling incluso más que usar WebSockets porque las WebSockets son más complejas y difíciles de configurar, además de que el soporte en navegadores viejos es limitado en comparación con el pooling y el long pooling.


WebSockets
En 2010, justo en la cúspide de la popularidad del término HTML5, se introdujo al navegador la habilidad de establecer conexión a dos vías, directamente con el servidor, las WebSockets.
El concepto creció increíblemente en popularidad, y a lo largo de los años se han introducido tecnologías que hacen muy fácil la comunicación en tiempo real vía WebSockets, entre ellas por supuesto Socket.io, ActionCable, Phoenix Channels, Gorilla Websockets, entre muchas otras más.
A pesar de la enorme popularidad de las Websockets, el hecho de que el tiempo de respuesta es más rápido que las estrategias que involucran Http y la calidad de las herramientas, vamos a poner sobre la mesa algo que tal vez no hayas oído antes las WebSockets no son siempre la solución.
Quizás nunca te hayas hecho una pregunta de estas:
    ¿Qué hago si la WebSocket se desconecta? ¿Reconectar?
    ¿Cuántas Websockets puede manejar mi servidor?
    ¿Cómo pruebo que una WebSocket efectivamente envía los datos de manera automatizada?
    ¿Qué pasa si hay un proxy entre el cliente y mi servidor?
    ¿Cómo configuro WSS para conexiones seguras?
Resulta que hay un enorme abismo entre implementar WebSockets en tu computadora local, y subirlas a producción. Algunas de las librerías populares de las que hablé antes, matarían tu servidor inmediatamente si la cantidad de conexiones aumenta, en otros casos empiezas a notar perdida de memoria por cada socket que no fue propiamente desconectada.
Más aún, normalmente existe una brecha entre tu aplicación Http y las WebSockets ¿cómo resolverás esto? ¿cómo compartirás sesiones para saber qué usuario está conectado? ¿cómo evitarás que alguien no autorizado se cuelgue a una WebSocket? Voy a continuar, ¿cómo configuras Nginx o Apache para recibir conexiones para las WebSockets?.
Si bien, la implementación en producción de las WebSockets trae consigo enormes problemas, también presenta los mejores beneficios:
    Tiempo de respuesta más rápido que otros mecanismos, ideal para juegos multijugador, chats, transacciones, etc.
    Aplicaciones más rápidas, ya que la comunicación queda permanentemente abierta, mientras que los mecanismos con Http involucran recorridos completos por la red para volver a enviar un mensaje al servidor.
    Modernas, con excelentes herramientas para el Backend y el Frontend.
Las desventajas de las WebSockets están en la implementación:
    No son fáciles de testear.
    Pueden implicar recursos más caros cuando tienes muchos usuarios.
    Debes hacerlas tolerante a fallos, ¿qué pasa si hay una desconexión?
    En ocasiones puedes toparte con mensajes repetidos y carreras de tiempo. Resolver estos conflictos es tu responsabilidad.
    Implican trabajo y configuración en el backend, a diferencia de las estrategias con Http.
¿Cuándo usar WebSockets?
Cuando la simplicidad no es tan importante como el rendimiento, si por ejemplo formas parte de un equipo de trabajo de más de 5 personas para este proyecto, considera las WebSockets.
También es importante que tengas experiencia con el stack completo, ya que algunas de las modificaciones son en el Backend, otras en el Frontend y algunas más en la administración del servidor.
Si estás realizando aplicaciones donde la respuesta o latencia es crítica, es decir los datos deben llegar tan pronto fueron creados, entonces considera las WebSockets, de primera mano se me ocurren juegos multijugador donde reducir el lag es crítico, deben implementar estos mecanismos.
Además, las WebSockets son una excelente solución en aplicaciones donde hay una comunicación constante entre el servidor y el cliente, ya que sólo necesitas una conexión para mandar cuantos mensajes quieras, mientras que en los mecanismos con Http, cada mensaje requiere de una conexión nueva, si la comunicación es constante, las conexiones podrían terminar alentando el servidor, reduciendo el performance de tu aplicación web.
Las WebSockets son increíbles, sólo úsalas adecuadamente, y no olvides que a veces las tecnologías antiguas, hacen el trabajo también.

Fuente: https://codigofacilito.com/articulos/como-programar-aplicaciones-en-tiempo-real
