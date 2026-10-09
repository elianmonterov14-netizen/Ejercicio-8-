# Actividad Semana 11 — TecShop

## Bitácora de prompts

**Proyecto:** Java — paquete `tecshop`  
**Temas:** Decorator, Adapter y Facade

## 1. Mapa de problemas

| Problema | Ubicación | Problema identificado | Responsable de demasiada lógica | Clases que se conservan |
|---|---|---|---|---|
| **S1 — Extras de productos** | `CartItem` (constructor, `getPrice()` y `describe()`) | Cada extra requiere más booleanos y condiciones; agregar otros extras hace crecer la clase y dificulta combinar servicios. | `CartItem` | `Cart`, `InvoicePrinter` y servicios no relacionados |
| **S2 — Envíos** | `ShippingService.quote()` | El servicio conoce gramos, códigos postales y el formato de la tarifa de `CorreosApi`. | `ShippingService` | `CorreosApi`, `ShippingProvider`, `LocalCourier` |
| **S3 — Compra** | `WebCheckout.buy()` y `KioskCheckout.buy()` | Los dos métodos repiten los pasos de reserva, impuestos, envío, pago y factura. | Ambos checkouts | `Inventory`, `TaxCalculator`, `PaymentGateway`, `InvoicePrinter` |

## 2. Prompts utilizados y observaciones

Las observaciones siguientes se basan en el código final compartido. El funcionamiento del proyecto fue confirmado por el equipo; no se adjuntaron registros de ejecución separados por cada prompt.


### S1 — Extras de productos (Decorator)


#### Prompt 1 — Convertir CartItem en interfaz

**Prompt:**

CONTEXTO: Proyecto Java del paquete tecshop. Actualmente CartItem representa un producto y utiliza valores booleanos para controlar sus extras. Cart e InvoicePrinter utilizan los métodos getPrice() y describe().

TAREA: Convertir CartItem en una interfaz que represente cualquier artículo del carrito.

ESTRUCTURA: CartItem debe declarar únicamente los métodos double getPrice() y String describe(), sin atributos ni constructores.

REGLAS: No modificar otros archivos. Mantener el paquete tecshop y no agregar librerías.

TERMINADO: Mostrar solamente CartItem.java. Indicar si existen errores de compilación temporales porque las clases que lo utilizan todavía no han sido actualizadas.

**Observación:** La IA dejó CartItem como interfaz con los dos métodos solicitados, sin incluir los extras. En el código compartido no se aprecian cambios fuera del alcance indicado para este paso.


#### Prompt 2 — Crear Product.java

**Prompt:**

CONTEXTO: En el paquete tecshop existe una interfaz CartItem con los métodos getPrice() y describe().

TAREA: Crear una clase nueva llamada Product.java para representar un producto sin extras.

ESTRUCTURA: La clase implementa CartItem. Tiene dos atributos private final: String name y double basePrice. Su constructor recibe ambos valores. getPrice() devuelve basePrice y describe() devuelve name.

REGLAS: No modificar otros archivos ni agregar librerías. Mantener el paquete tecshop.

TERMINADO: La clase nueva debe compilar. Mostrar únicamente Product.java.

**Observación:** La IA creó Product con nombre, precio base y los métodos pedidos. En el código compartido no se aprecian cambios fuera del alcance indicado para este paso.


#### Prompt 3 — Crear GiftWrap.java

**Prompt:**

CONTEXTO: El proyecto tiene una interfaz CartItem y una clase Product que representa los productos sin extras.

TAREA: Crear GiftWrap.java para agregar el servicio de envoltura de regalo a cualquier artículo.

ESTRUCTURA: La clase implementa CartItem y tiene un atributo private final CartItem item. Su constructor recibe un CartItem. El método getPrice() devuelve item.getPrice() + 1500. El método describe() devuelve item.describe() + " + gift wrap".

REGLAS: No modificar clases existentes. No agregar librerías. Mantener el paquete tecshop.

TERMINADO: La clase debe compilar y permitir recibir tanto un producto como otro objeto que implemente CartItem. Mostrar únicamente GiftWrap.java.

**Observación:** La IA creó GiftWrap y añadió el costo y la descripción del envoltorio. En el código compartido no se aprecian cambios fuera del alcance indicado para este paso.


#### Prompt 4 — Crear Insurance.java

**Prompt:**

CONTEXTO: El proyecto utiliza la interfaz CartItem, que declara getPrice() y describe().

TAREA: Crear únicamente la clase Insurance.java.

ESTRUCTURA: Implementa CartItem, contiene private final CartItem item y recibe ese objeto en su constructor. getPrice() obtiene el precio del artículo recibido y le agrega un 5%. describe() devuelve la descripción del artículo seguida de " + insurance".

REGLAS: No modificar otros archivos ni agregar librerías. Mantener el paquete tecshop.

TERMINADO: La clase debe compilar. Mostrar únicamente Insurance.java.

**Observación:** La IA creó Insurance y aplicó el 5 % sobre el precio recibido. En el código compartido no se aprecian cambios fuera del alcance indicado para este paso.


#### Prompt 5 — Crear Express.java

**Prompt:**

CONTEXTO: El proyecto utiliza la interfaz CartItem, que declara getPrice() y describe().

TAREA: Crear únicamente la clase Express.java.

ESTRUCTURA: Implementa CartItem, contiene private final CartItem item y recibe ese objeto en su constructor. getPrice() devuelve item.getPrice() + 2500. describe() devuelve item.describe() + " + express".

REGLAS: No modificar otros archivos ni agregar librerías. Mantener el paquete tecshop.

TERMINADO: La clase debe compilar. Mostrar únicamente Express.java.

**Observación:** La IA creó Express y añadió el costo fijo y la descripción del servicio. En el código compartido no se aprecian cambios fuera del alcance indicado para este paso.


#### Prompt 6 — Actualizar Main.java

**Prompt:**

CONTEXTO: En tecshop ya existen CartItem, Product, GiftWrap, Insurance y Express. Los extras se pueden combinar pasando un artículo al constructor de otro.

TAREA: Modificar únicamente Main.java para construir los artículos utilizando estas clases en lugar de los valores booleanos.

ESTRUCTURA: Crear las combinaciones respetando este orden:

Laptop stand: Express(GiftWrap(Product)).
USB-C hub: Insurance(Product).
Notebook: Product.
Mechanical keyboard: Insurance(GiftWrap(Product)).

Conservar los precios base originales, los carritos, los clientes y las llamadas de compra. Utilizar los constructores reales de las clases.

REGLAS: No modificar otros archivos, precios base ni el proceso de compra. Mantener el paquete tecshop.

TERMINADO: Compilar y ejecutar Main. La salida debe coincidir exactamente con la salida base de la actividad. Mostrar únicamente Main.java.

**Observación:** La IA actualizó la construcción de los artículos con las combinaciones indicadas y conservó los precios base. En el código compartido no se aprecian cambios fuera del alcance indicado para este paso.


### S2 — Envíos con Correos (Adapter)


#### Prompt 1 — Crear CorreosShipping.java

**Prompt:**

CONTEXTO:

Proyecto Java dentro del paquete tecshop. Tenemos una interfaz ShippingProvider con el método double quote(double kg, String city) y una clase externa CorreosApi que utiliza el método calcularTarifa(int gramos, String codigoPostal).

Actualmente, ShippingService realiza las conversiones necesarias para utilizar Correos. Queremos que esas conversiones se realicen en una clase independiente.

TAREA:

Crear una nueva clase llamada CorreosShipping.java que permita utilizar CorreosApi mediante nuestra interfaz ShippingProvider.

ESTRUCTURA:

La clase CorreosShipping implementa ShippingProvider.
Tiene un atributo private final CorreosApi api.
Su constructor recibe un objeto CorreosApi.
Implementa el método double quote(double kg, String city).
Convierte los kilogramos a gramos utilizando (int) Math.round(kg * 1000).
Convierte el nombre de la ciudad a código postal utilizando las mismas equivalencias que existen en ShippingService: San Jose = 10101, Alajuela = 20101, Cartago = 30101 y Limon = 70101.
Si la ciudad no existe, lanza IllegalArgumentException con el mismo mensaje del código original.
Llama a api.calcularTarifa(gramos, codigoPostal).
Elimina el símbolo ₡ del resultado y lo convierte a double utilizando Double.parseDouble().

REGLAS:

No modificar CorreosApi, ShippingService, ShippingProvider ni ningún otro archivo. No utilizar librerías nuevas y mantener el paquete tecshop.

TERMINADO:

La clase debe compilar correctamente y Main debe seguir mostrando exactamente la salida original. Mostrar únicamente el archivo CorreosShipping.java.

**Observación:** La IA creó CorreosShipping, separó las conversiones de datos y dejó CorreosApi sin cambios en el código compartido. En el código compartido no se aprecian cambios fuera del alcance indicado para este paso.


#### Prompt 2 — Modificar ShippingService.java

**Prompt:**

CONTEXTO:

Proyecto Java del paquete tecshop. Ya existe una clase CorreosShipping que implementa ShippingProvider y se encarga de convertir los datos necesarios para utilizar CorreosApi.

Actualmente, ShippingService todavía realiza esas conversiones directamente dentro del método quote().

TAREA:

Modificar únicamente ShippingService.java para que utilice la nueva clase CorreosShipping en lugar de realizar las conversiones dentro del servicio.

ESTRUCTURA:

Mantener el atributo private final LocalCourier local = new LocalCourier();.
Cambiar el atributo de Correos por private final ShippingProvider correos = new CorreosShipping(new CorreosApi());.
Mantener la firma double quote(String provider, double kg, String city).
Si el proveedor es "local", continuar utilizando local.quote(kg, city).
Si el proveedor es "correos", devolver directamente correos.quote(kg, city).
Eliminar del método las conversiones de kilogramos a gramos, el switch de ciudades y la conversión del resultado a double.
Mantener la excepción original cuando el proveedor no existe.

REGLAS:

No modificar CorreosShipping, CorreosApi, ShippingProvider, LocalCourier, WebCheckout, KioskCheckout ni ningún otro archivo. No agregar librerías externas y mantener el paquete tecshop.

TERMINADO:

El proyecto debe compilar correctamente y Main debe imprimir exactamente la misma salida original, incluyendo los precios, descripciones y totales. Mostrar únicamente el archivo ShippingService.java.

**Observación:** La IA dejó la cotización de Correos delegada en CorreosShipping y conservó la firma del método. En el código compartido no se aprecian cambios fuera del alcance indicado para este paso.


### S3 — Proceso de compra (Facade)


#### Prompt 1 — Crear Checkout.java

**Prompt:**

CONTEXTO:

Proyecto Java dentro del paquete tecshop. Actualmente, WebCheckout.buy() y KioskCheckout.buy() realizan prácticamente los mismos pasos para completar una compra. Ambos utilizan los servicios de inventario, impuestos, envíos, pagos e impresión de facturas.

TAREA:

Crear una nueva clase llamada Checkout.java que se encargue de realizar todo el proceso de compra en un solo lugar.

ESTRUCTURA:

La clase debe llamarse Checkout.
Tener un atributo private final ShippingProvider shipping.
Tener un atributo private final String paymentMethod.
Su constructor recibe ShippingProvider shipping y String paymentMethod.
Implementar el método void buy(Cart cart, String customer, String city).
Dentro de buy(), realizar los mismos pasos y en el mismo orden que actualmente utiliza WebCheckout.buy():
Reservar los productos mediante Inventory.reserve.
Calcular el subtotal y los impuestos mediante los métodos existentes.
Calcular el costo del envío utilizando shipping.quote(cart.getWeightKg(), city).
Calcular el total de la compra.
Realizar el cobro mediante PaymentGateway utilizando paymentMethod.
Imprimir la factura mediante InvoicePrinter.print.
Utilizar las firmas reales de los métodos existentes, sin modificar su funcionamiento.

REGLAS:

No modificar WebCheckout, KioskCheckout, Inventory, TaxCalculator, PaymentGateway, InvoicePrinter, CorreosApi ni ningún otro archivo. No agregar librerías nuevas y mantener el paquete tecshop.

TERMINADO:

La nueva clase debe compilar correctamente. Main debe continuar mostrando exactamente la misma salida original. Mostrar únicamente Checkout.java.

**Observación:** La IA concentró la secuencia de inventario, impuestos, envío, cobro e impresión en Checkout. En el código compartido no se aprecian cambios fuera del alcance indicado para este paso.


#### Prompt 2 — Modificar WebCheckout.java

**Prompt:**

CONTEXTO:

Proyecto Java del paquete tecshop. Ya existe una clase Checkout que recibe un ShippingProvider y un método de pago en su constructor. Esta clase se encarga de realizar todos los pasos de la compra.

Actualmente, WebCheckout.buy() todavía contiene su propia implementación del proceso de compra.

TAREA:

Modificar únicamente WebCheckout.java para que utilice la clase Checkout y deje de repetir el proceso de compra.

ESTRUCTURA:

Mantener el nombre de la clase WebCheckout.
Mantener la firma original void buy(Cart cart, String customer, String city).
Dentro de buy(), realizar únicamente esta llamada:

new Checkout(new CorreosShipping(new CorreosApi()), "credit card").buy(cart, customer, city);

Eliminar de WebCheckout los pasos repetidos de inventario, impuestos, cálculo del total, cobro e impresión de factura.
No agregar lógica adicional.

REGLAS:

No modificar Checkout, KioskCheckout, CorreosShipping, CorreosApi ni los servicios de la tienda. No utilizar librerías nuevas y mantener el paquete tecshop.

TERMINADO:

El proyecto debe compilar y Main debe imprimir exactamente la misma salida original. Mostrar únicamente WebCheckout.java.

**Observación:** La IA dejó la compra web como una llamada a Checkout con Correos y tarjeta. En el código compartido no se aprecian cambios fuera del alcance indicado para este paso.


#### Prompt 3 — Modificar KioskCheckout.java

**Prompt:**

CONTEXTO:

Proyecto Java del paquete tecshop. Ya existe una clase Checkout que realiza el proceso completo de compra y permite configurar el proveedor de envío y el método de pago.

Actualmente, KioskCheckout.buy() todavía contiene los pasos de compra repetidos.

TAREA:

Modificar únicamente KioskCheckout.java para que utilice la clase Checkout.

ESTRUCTURA:

Mantener el nombre de la clase KioskCheckout.
Mantener la firma void buy(Cart cart, String customer, String city).
Dentro de buy(), realizar únicamente esta llamada:

new Checkout(new LocalCourier(), "SINPE Movil").buy(cart, customer, city);

Eliminar los pasos repetidos del proceso de compra.
No agregar ninguna otra lógica.

REGLAS:

No modificar Checkout, WebCheckout, LocalCourier, Inventory, TaxCalculator, PaymentGateway, InvoicePrinter ni ningún otro archivo. No agregar librerías y mantener el paquete tecshop.

TERMINADO:

El proyecto debe compilar correctamente y Main debe imprimir exactamente la misma salida original. Mostrar únicamente KioskCheckout.java.

**Observación:** La IA dejó la compra del kiosco como una llamada a Checkout con envío local y SINPE Móvil. En el código compartido no se aprecian cambios fuera del alcance indicado para este paso.


## 3. Verificación final

El equipo confirmó que el proyecto funciona después de los cambios. La implementación compartida conserva las operaciones y los importes esperados.

**Comandos utilizados para verificar desde la carpeta de archivos Java:**

```powershell
javac -encoding UTF-8 -d out *.java
java -cp out tecshop.Main
```

**Salida base que se debe conservar:**

```text
Inventory: reserved 3 items
Payment: charged CRC 61,832.00 via credit card
----- INVOICE: Ana -----
  Laptop stand + gift wrap + express  CRC 29,000.00
  USB-C hub + insurance  CRC 18,900.00
  Notebook  CRC 3,500.00
  Subtotal: CRC 51,400.00
  Tax:      CRC 6,682.00
  Shipping: CRC 3,750.00
  TOTAL:    CRC 61,832.00

Inventory: reserved 1 items
Payment: charged CRC 53,592.75 via SINPE Movil
----- INVOICE: Luis -----
  Mechanical keyboard + gift wrap + insurance  CRC 45,675.00
  Subtotal: CRC 45,675.00
  Tax:      CRC 5,937.75
  Shipping: CRC 1,980.00
  TOTAL:    CRC 53,592.75
```

**Observación general:** La IA produjo las clases y los cambios solicitados. El equipo indicó que el programa funciona. La revisión del código compartido confirma que las conversiones de Correos se realizan en `CorreosShipping`, los extras se combinan mediante clases individuales y la secuencia de compra está reunida en `Checkout`.

**Detalle de diseño:** `ShippingService` todavía construye `new CorreosShipping(new CorreosApi())`, como indicaba el prompt original. Las conversiones de datos ya no están en ese servicio.

**Nota de bitácora:** Mantener aquí únicamente los prompts que efectivamente se enviaron a la IA. Si alguno de estos prompts se redactó pero no se ejecutó, eliminarlo o identificarlo como propuesta antes de entregar.
