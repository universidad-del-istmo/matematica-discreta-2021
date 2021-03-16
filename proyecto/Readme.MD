yu# Proyecto Final: Limited-Trust E-Store

## Formalidades

* **Modalidad de Trabajo**: Grupo (Maximo 4 personas)
* **Fecha de Entrega**: Viernes 28 de Abril @ 11:59 pm
* **Modo de Evaluación**: Codigo fuente y presentación del programa y el codigo

## Introducción

El metodo actual utilizado para la mayoria de transacciones electronicas es la *Tarjeta de Credito*. A pesar del tremento exito comercial, el protocolo tiene muchas limitación es en cuanto a su seguridad. De hecho, el fraude de tarjetas de credito es un negocio muy lucrativo y la unica razon por la cual un protocolo tan inseguro es comercialmente viable es debido a que el volumen de transacciones legitimas es tan alto, siendo suficiente para contrarrestar las perdidas debido a fraude que por lo general las absorben las instituciones financieras mediante un seguro.

Las tarjetas de credito son inseguras debido a que para utilizarlas es necesario proveerle al vendedor toda la información asociada a la tarjeta de credito para realizar la compra. Esto significa que cualquier persona que haya sido pagada por tarjetas de credito tiene el potencial de cometer fraude. Adicionalmente, la mayoria de los sistemas informaticos no manejan niveles muy altos de seguridad por lo cual es muy probable que esa información eventualmente sea expuesta a un ataque cibernetico -- incluso uno que origina dentro de la empresa misma ya que es imposible saber que empleado robo los credenciales y cometio el fraude.

El objetivo de este trabajo es proponer un metodo de pago que utiliza la criptografia para mejorar la seguridad. Este metodo no solo es superior en cuanto a seguridad, sino también en cuanto a privacidad ya que no requiere que el comprador revele información personal para realizar los pagos.

## Descripción

En este metodo de pago existen 3 entidades: *comprador*, *vendedor* y *regulador*. El *comprador* es la persona que desea comprar un producto, en este caso se limitara a productos digitales, pero este metodo se puede extender a casi cualqier tipo de compra en linea. El *vendedor* es la entidad que esta vendiendo el producto. Por ultimo, el *regulador* es la entidad financiera que lleva a cabo la transacción monetaria; esta podria ser un banco, un operador de tarjetas de credito o incluso un *Blockchain*. Estas entidades interactuan entre ellas utilizando criptografia mediante los flujos que se describen en las siguientes paginas.

Cada identidad tiene asociada a ella una *llave criptografica asimetrica*, como por ejemplo una llave RSA. El componente publico de la llave de todas las entidades es conocido por todos mientras que el componente privado es solamente conocido por la identidad que le pertenece la llave.


### Flujo 1: Registro de con el Regulador

Los compradores y vendedores deben registrarse con la entidad reguladora para poder participar en esta economia. Esto es analogo a una persona solicitando una tarjeta de credito o un punto de venta solicitando un P.O.S. Los pasos son los siguientes:

1. La entidad genera una llave criptografica.
2. La entidad comparte con el *regulador* su llave publica e información adicional firmada con su llave privada. Esta información adicional es:
    * Si se esta registrando como *comprador* o *vendedor*
    * Información personal como nombre, NIT, ect. (de momento solo es un experimento)
3. La entidad reguladora verifica los datos y guarda la llave publica de la entidad que se registro
4. La entidad reguladora crea y firma con su llave privada un *certificado de autorización*. Este certificado sera utilizado en los otros flujos por las entidades en los demas flujos. Este certificado debe incluir:
    * La *llave publica* del *vendedor*
    * Tipo de certificado (comprador o vendedor)
    * Fecha de expiración

### Flujo 2: Registro de Producto con el Regulador

Antes de poder vender productos, los vendedores deben registrar los mismos con la entidad reguladora. Esto se utilizara para crear un mecanismo criptograficamente seguro para garantizar la entrega del mismo. En este caso, solo se manejaran productos digitales por lo cual es possible calcular un *hash criptografico* del producto siendo registrado.

1. El *vendedor* calcula el *hash criptografico* del producto que se desea vender. Puede utilizar cualquier función de hashing que desee. Los productos que se venderan son archivos digitales (programas, canciones, peliculas, etc.) por lo cual el hash se puede calcular.
2. El *vendedor* comparte con el *regulador* el hash criptografico y la información asociada al producto. El *vendedor* debe firmar criptograficamente estos datos antes de compartirlos con el *regulador*
3. El *regulador* almacena el hash y la información asociada al producto.

### Flujo 3: Compra de Producto digital

1. El comprador selecciona el producto que desea comprar e informa al vendedor.
2. El vendedor genera una *orden de compra/venta*, la firma con su llave privada y se la envia al comprador. La *orden de compra/venta* debe incluir:
    * Un *identificador unico* aleatoreo generado por una fuente criptograficamente segura
    * El *hash* del producto que se desea adquirir.
    * El *certificado de autorizacion* del vendedor.
    * El valor del producto
3. El *comprador* verifica que la orden de compra/venta:
    * Haya sido firmada por la misma llave del *certificado de autorización*
    * Que el *certificado de autorización* este vijente
    * Que el *certificado de autorización* haya sido firmado por el *regulador*
    * Que el valor del producto sea el correcto
4. El *comprador* combina la *orden de compra/venta* y la firma de la misma en un paquete y le aplica su firma al mismo. Este paquete junto con la firma es enviado al *regulador*.
5. El *regulador* verifica el paquete recibido e identifica al *comprador* y *vendedor* mediante las firmas criptograficas.
6. El *regulador* debita un deposito de la cuenta del *comprador* y *vendedor* este deposito sera devuelto a ambos al finalizar la compra.
7. El *regulador* debita el costo del producto de la cuenta del *comprador* pero aun no lo transfiere a la cuenta del *vendedor*.
8. El *regulador* combina el paquete y la firma criptografica del comprador en un nuevo paquete, firma este paquete con su llave privada y se lo envia al *vendedor*.
9. El *vendedor* da por enterado que la transacción ya esta en movimiento y despacha el producto digital al *comprador*.
10. El *comprador* verifica el hash del producto despachado, en caso de ser el correcto, le envia un mensaje con su firma criptografica indicandole al *regulador* que la transacción ha finalizado. Este mensaje debe incuir el *identificador unico* de la *orden de compra/venta* para que el *regulador* pueda identificar la transacción.
11. El *regulador* le devuelbe el deposito al *comprador* y *vendedor* y abona la cuenta del *vendedor* con el dinero que se debito de la cuenta del *comprador*.

## Entrega

Debe implementar un programa que implemente los flujos descritos anteriormente. Possiblemente sea más sencillo elaborar 3 programs distintos, uno para cada entidad, pero no es un requisito. Puede utilizar el lenguaje de programación que guste y cualquier biblioteca de apoyo. Se recomienda que utilize una biblioteca como *OpenSSL* para las operaciónes criptograficas aunque si usted lo desea, puede implementar las operaciónes criptograficas usted mismo.

Tiene la libertad de escoger cualquier modo de operación que desee para su programa (ej. terminal, aplicación de escritorio, aplicación movil, aplicación web, etc.) No se restaran ni agregaran puntos por estetica. Adicionalmente, el grupo tendra que presentar su proyecto, tanto el codigo como el programa en ejecición. El profesor podra dirijir preguntas sobre cualquier parte del programa a cualquier miembro del grupo. La nota del proyecto es grupal, sin embargo la nota grupal sera ponderada de forma individual dependiendo del conocimiento que cada integrante muestre en la presentación.

## Extra

Se otorgaran puntos extras si implementa las operaciones criptograficas en vez de utilizar una biblioteca como *OpenSSL*