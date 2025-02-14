# eShopAcademy

## Objetivo
Sistema completo de venta implementado con patron arquitectónico de microservicios. [Link a esquema en Miró](https://miro.com/app/board/uXjVN3nzsOs=/)

### Fase 1 - Implementación local en Docker Compose o Kubernetes

![image](https://github.com/user-attachments/assets/3df1a17e-27dc-4bc0-911d-abc589202a2b)


### Fase 2 - Implementación en la nube Kubernetes sobre Azure

![image](https://github.com/user-attachments/assets/700c7091-dce4-4cb3-9a9e-c675b4d62d36)

## Servicios Backend

### Product Catalog Service
Servicio que provee el catalogo de productos. Los datos son almacenados en una BD Cassandra. Consta de una Api para exponer todas las funcionalidades de ABM de un producto, y un Servicio Processor para mantener la conectividad con los demas microservicios por medio de una interface gRPC.
Publica en el Back channel cuando:


#### Esquema de datos
* Tabla ProductCatalog
  * Guid
  * Name
  * Price
  * Description
  * Image
  * Type/Category
* Tabla Categories
      * Guid
      * Description 

#### Actores
Clientes: Rol de Lectura  
Admin: ABML

### Stock Service
Servicio que controla el stock de los productos. Los datos son almacenados en una BD Mongo. Consta de una Api para exponer todas las funcionalidades de ABM de un producto a los clientes externos, y un Servicio Processor para brindar mantener la conectividad con los demas microservicios por medio interface gRPC.
Publica en el Back channel cuando:
* Se incrementa un stock. Publica la cantidad total de ese producto.
* Se decrementa un stock. Publica la cantidad total de ese producto.
Los servicios interesados en estas actualizaciones son OrderService y BasketService, estaran pendientes de estas notificaciones

#### Esquema de datos
* Tabla Stock:
  * ProductGUID
  * Quantity
  * Warehouse

### Order Service
Servicio encrgado de generar la orden de compra al confirmarla. Debe orquestar el uso de los demas servicios, por lo cual será cliente los mismos por medio de gRPC y almacenar los datos en una BD PostgreSql. Las tares que debré realizar son:
* Verificar los productos de la orden y su precio contra el Catalog Service
* Manejar el Stock con stock service
* Notificaciones por mail al cliente con Notification Service
* Coordinar los plazos envío con el Shipping Service
* Destruir el carrito de compras
* Realizar los pagos con Payment Service
* En caso de fallo en el pago, deshacer todos los cambios realizados de forma segura
* Crear la orden.

Dado la importancia central del mismo, deberá implementar transaccionalidad utilizando un patrón Saga.

#### Esquema de datos
* Tabla Order:  
  *(Billing Data)*
  * Firstame
  * LastName
  * EmailAddress
  * Country
  * State
  * ZipCode  
  *(Payment Data)*  
  * CardName
  * CardNumber
  * Expiration
  * Paymentmethod
  * Status  
  *(Item List)*  //Deberia ser una tabla aparte
  * Productuid
  * UnitPrice
  * Quantity
  * GUID
  * OrdeDate
  * Mail     //Es otro mail?  
  * TotalAmount    


### Basket Service
Servicio de carro de compras. Acumula los productos comprados por el cliente. Está suscrito a las notificaciones de los servicios de Catalog y Stock para saber en tiempo real si un producto esta disponible y tener su descripcion actualizada, y no esperar a refrescarla en el momento de confirmar la compra. También debe estar suscrito a las notificaciones de Order Service para destruir el carrito una vez confirmada la compra. Utilizará una base de datos en memoria Redis

#### Esquema de datos
* Tabla Basket
  * ClientGUID
  * ItemList
 
### Payment Service
Servicio de pagos que mediará con algun/algunos provedores de pago. No requerirá una base de dastos propia y aplicará patrones Strategy y Adapter para poder incorporar diferentes PSPs.
Publica en el back channel cuando logra realizar un pago, o bien falla.

#### Esquema de datos
* Tabla Payments
  * PaymentGUID
  * OrderGUID
  * CardName
  * CardNumber
  * Expiration
  * PaymentMethod
  * Status
  * Mail
    
### Notification Service
Servicio de envío de Correos. Notificará por mail al cliente de su compra incluyendo los detalles de la misma y del envío en caso de corresponder.

### Shipping Service

