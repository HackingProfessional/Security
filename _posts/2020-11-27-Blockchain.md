---
layout: post
title:  "¿Que es Blockchain?"
description: "Aprende a los conceptos basicos acerca del Blockchain"
date:   2020-11-27
image:  "img/Blockchain.jpg"
tags:   [Blockchain, Security]
---

La cadena de bloques, más conocida por el término en inglés blockchain, es un registro único, consensuado y distribuido en varios nodos de una red.  
En cada bloque se almacena:  
 - Una cantidad de registros o transacciones válidas,
 - Información referente a ese bloque.
 - Su vinculación con el bloque anterior y el bloque siguiente a través del hash de cada bloque ─un código único que sería como la huella digital del bloque.  

Por lo tanto, cada bloque tiene un lugar específico e inamovible dentro de la cadena, ya que cada bloque contiene información del hash del bloque anterior. La cadena completa se guarda en cada nodo de la red que conforma la blockchain, por lo que se almacena una copia exacta de la cadena en todos los participantes de la red.  

A medida que se crean nuevos registros, estos son primeramente verificados y validados por los nodos de la red y luego añadidos a un nuevo bloque que se enlaza a la cadena.  

<figure>
  <img src="{{site.baseurl}}/img/BlockChain.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/BlockChain.png" title="Que es BlockChain">Que es BlockChain</a>
  </figcaption>
</figure> 

##  Ejemplo de uso

Si Gerardo quiere retirar un bitcoin de su cuenta para dárselo a María, primero avisa a todo el mundo con una peculiaridad: nadie sabe que Gerardo es Gerardo y que María es María. Solo saben que desde una cartera digital (lo que sería una cuenta bancaria) se quiere transferir esa cantidad (que sí se conoce) a otra.  

Gerardo, por lo tanto, avisa de sus intenciones, pero sin revelar su identidad: "¡Eh, chicos, quiero mandarle un bitcoin desde mi cartera a esta otra, por favor, actualizad vuestros libros de cuentas!". Al enviar ese mensaje, todos los usuarios de esa red primero comprueban que Gerardo la cartera de origen tiene suficiente dinero para enviárselo a la cartera de destino. Si es así, todos anotan esa transacción, que pasa a completarse y a formar parte del bloque de transacciones. Eso sí: todavía no están registrados en esa base de datos de forma definitiva.  

A medida que pasa el tiempo, más y más transacciones van completándose y pasando a ese bloque, que tiene una capacidad limitada que depende de la estructura de la cadena de bloques y del tamaño de cada transacción. Cuando un bloque ya no admite más transacciones, llega un momento importante: el de "validarlo" o "sellarlo", que es lo que los usuarios hacen cuando hacen minería de bitcoin.  

## ¿Por qué blockchain es tan segura?

Al ser una tecnología distribuida, donde cada nodo de la red almacena una copia exacta de la cadena, se garantiza la disponibilidad de la información en todo momento. En caso de que un atacante quisiera provocar una denegación de servicio, debería anular todos los nodos de la red, ya que basta con que al menos uno esté operativo para que la información esté disponible.  

Por otro lado, al ser un registro consensuado, donde todos los nodos contienen la misma información, resulta casi imposible alterar la misma, asegurando su integridad. Si un atacante quisiera modificar la información en la cadena de bloques, debería modificar la cadena completa en al menos el 51% de los nodos.  

Por último, dado que cada bloque está matemáticamente vinculado al bloque siguiente, una vez que se añade uno nuevo a la cadena, el mismo se vuelve inalterable. Si un bloque se modifica su relación con la cadena se rompe. Es decir, que toda la información registrada en los bloques es inmutable y perpetua.  
Por todo lo mencionado previamente, puedo afirmar con certeza que las operaciones ciberneticas que lleguen a usar Blockchain serian mas seguras y por lo tanto mejores.  
Cabe resaltar, que esta tecnologia se puede aplicar a diferentes escenarios. (No solo a transacciones de dinero)
A continuacion, enumero alguno de estos escenarios en donde se hace uso de la tecnologia Blockchain:  

 - Registro de propiedades
 - Pagos en el mundo real
 - Carsharing
 - Almacenamiento en la nube
 - Identidad digital
 - Música
 - Servicios públicos/gubernamentales
 - Seguridad social y sanidad

### Explicacion de escenarios de uso
**Registro de propiedades:** el gobierno japonés ha iniciado un proyecto para unificar todo el registro de propiedades urbanas y rústicas con tecnología de cadena de bloques, lo que permitiría contar con una base de datos abierta en la que se pudieran consultar los datos de las 230 millones de fincas y 50 millones de edificios que se estima existen en el país asiático. En Dubái están planeando algo muy parecido.  

**Pagos en el mundo real:** una startup llamada TenX ha creado una tarjeta prepago que se puede recargar con distintas criptodivisas para luego pagar con ella en cualquier sitio como si esa tarjeta tuviera dinero convencional, sin importar si ese establecimiento acepta o no este tipo de monedas virtuales.  

**Carsharing:** la empresa EY, subsidiaria de Ernst & Young Global Ltd está desarrollando un sistema basado en la cadena de bloques que permite a empresas o grupos de personas acceder a un servicio para compartir coches de forma sencilla. El llamado Tesseract permitiría registrar quién es el propietario del vehículo, el usuario de ese vehículo y generar los costes basados en el seguro y otras transacciones en este tipo de servicios.  

**Almacenamiento en la nube:** normalmente los servicios de almacenamiento están centralizados en un proveedor específico, pero la empresa Storj quiere descentralizar este servicio para mejorar la seguridad y reducir la dependencia de ese proveedor de almacenamiento.  

**Identidad digital:** los últimos y gigantescos fallos de seguridad y robos de datos han hecho que la gestión de nuestras identidades se convierta en un problema muy real. La cadena de bloques podría proporcionar un sistema único para lograr validar identidades de forma irrefutable, segura e inmutable. Hay muchas empresas desarrollando servicios en este ámbito, y todas ellas creen que aplicar la tecnología de la cadena de bloques para este propósito es una solución óptima.   

**Música:** aunque hay críticas que afirman que esta opción no tiene validez, hay quien afirma que la distribución musical podría sufrir toda una revolución si se lograra implantar un sistema basado en la cadena de bloques para gestionar su reproducción, distribución y disfrute. La mismísima Spotify está apostando fuerte por su propia cadena de bloques.  

**Servicios públicos/gubernamentales:** otro de los ámbitos más interesantes de la aplicación de la cadena de bloques es en los servicios públicos que podrían presumir así de una transparencia absoluta. Las áreas de actividad son múltiples: desde la gestión de licencias, transacciones, eventos, movimiento de recursos y pagos, gestión de propiedades hasta la gestión de identidades. De hecho el robo masivo de datos en Equifax ha hecho que algunos propongan la sustitución de los números de la seguridad social en Estados Unidos con un sistema basado en la cadena de bloques. Hay iniciativas incluso para "descentralizar el gobierno", y Bitnation es una de esos proyectos que tratan de llamarnos convertirnos en "ciudadanos del mundo".  

**Seguridad social y sanidad:** aunque se podría englobar dentro de los servicios públicos mencionados, la sanidad pública podría sufrir una verdadera revolución con un sistema de cadena de bloques que sirviera para registrar todo tipo de historiales médicos y resolver uno de los problemas clásicos de la gestión de la sanidad.  

## VENTAJAS QUE SUPONE EL USO DE BLOCKCHAIN EN LA CIBERSEGURIDAD  
He aquí las principales ventajas que supone el uso de la tecnología blockchain en relación con la ciberseguridad:  

**Descentralización:** gracias a la red peer-to-peer, no resulta necesaria una verificación externa, ya que cada usuario puede observar las transacciones en red.  
**Seguimiento y rastreo:** todas las transacciones realizadas en blockchains están firmadas digitalmente y tienen un sello temporal, de tal manera que los usuarios de la red pueden seguir fácilmente el historial de transacciones, al tiempo que pueden rastrear las cuentas en cualquier momento histórico. Asimismo, esta característica permite que una empresa obtenga información válida acerca de activos o distribución de productos.  
**Confidencialidad:** la confidencialidad de los miembros de la red es alta gracias a la criptografía de clave pública que autentifica a los usuarios y encripta sus transacciones.
Seguridad en relación con el fraude: en caso de que se produzca un hackeo, es fácil definir un comportamiento malicioso gracias a las conexiones peer-to-peer y el consenso distribuido. A día de hoy, las blockchains se consideran “imposibles de hackear”, ya que para producir un impacto en una red, los atacantes deben tomar el control del 51 % de los nodos de la misma.  
**Sostenibilidad:** la tecnología blockchain no tiene un único punto de vulnerabilidad, lo que significa que, incluso en caso de que se produzcan ataques DDoS, el sistema funcionará normalmente gracias a las múltiples copias del libro de contabilidad.  
**Integridad:** el libro de contabilidad distribuido garantiza la protección de los datos contra cualquier modificación o destrucción. Además, la tecnología garantiza la autenticidad y la irreversibilidad de las transacciones realizadas. Los bloques encriptados contienen datos inmutables resistentes al hackeo.  
**Resiliencia:** la naturaleza peer-to-peer de esta tecnología garantiza que la red funcionará a todas horas incluso si algunos nodos están desconectados o están siendo atacados. En caso de que se produzca un ataque, una empresa puede lograr que ciertos nodos tengan un carácter redundante y, de esta manera, puede operar como siempre.  
**Calidad de los datos:** la tecnología blockchain no puede mejorar la calidad de los datos, pero puede garantizar la precisión y la calidad de los mismos después de que hayan sido encriptados en la blockchain.  
**Contratos inteligentes:** programas de software que están basados en el libro de contabilidad. Estos programas garantizan la ejecución de los términos contractuales y verifican las partes. La tecnología blockchain puede aumentar de forma significativa los estándares de seguridad en lo que respecta a los contratos inteligentes, ya que minimiza los riesgos de que se produzcan errores y ciberataques.  
**Disponibilidad:** no hay necesidad de almacenar los datos de carácter sensible en un solo sitio, ya que la tecnología blockchain permite tener múltiples copias de los datos que siempre están disponibles para los usuarios de la red.
Aumento de la confianza del cliente: sus clientes confiarán más en su empresa si puede garantizar un alto nivel de seguridad en lo que respecta a los datos. Por otra parte, la tecnología blockchain permite proporcionar a los clientes información acerca de los productos y servicios de la empresa de manera instantánea.  

## DESVENTAJAS DEL USO DE BLOCKCHAIN EN CIBERSEGURIDAD
**Irreversibilidad:** en caso de que un usuario pierda u olvide la clave privada para desencriptarlos, existe el riesgo de que los datos encriptados resulten irrecuperables.  
**Límites de almacenamiento:** cada bloque puede contener como máximo 1 Mb de datos y, por término medio, una blockchain solo puede gestionar 7 transacciones por segundo.  
**Riesgo de ciberataques:** si bien esta tecnología reduce en gran medida el riesgo de que se produzca una intervención maliciosa, no constituye la panacea contra todas las ciberamenazas. Si los atacantes logran explotar la mayor parte de la red, es posible que se pierda la totalidad de la base de datos.    
**Retos en relación con la adaptabilidad:** aunque la tecnología Blockchain puede aplicarse a prácticamente toda clase de negocios, es posible que las empresas experimenten dificultades a la hora de integrar dicho sistema. Asimismo, las aplicaciones blockchain pueden requerir un reemplazo completo de los sistemas existentes, de tal manera que, antes de proceder a implementar esta tecnología, las empresas deberán tener en cuenta esta circunstancia.  
**Costes operativos elevados:** ejecutar la tecnología blockchain requiere mucha potencia informática, lo que, en comparación con los sistemas existentes, puede implicar costes marginales elevados.  
**Conocimiento de blockchain:** aún no hay suficientes desarrolladores con experiencia en tecnología blockchain y que tengan un conocimiento profundo de criptografía.  

### Referencias
https://www.xataka.com/especiales/que-es-blockchain-la-explicacion-definitiva-para-la-tecnologia-mas-de-moda