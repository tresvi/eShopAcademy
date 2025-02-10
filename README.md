# eShopAcademy

## Objetivo
Sistema completo de venta implementado con patron arquitectónico de microservicios. [Link a esquema en Miró](https://miro.com/app/board/uXjVN3nzsOs=/)

### Fase 1 - Implementación local en Docker Compose o Kubernetes

![image](https://github.com/user-attachments/assets/3df1a17e-27dc-4bc0-911d-abc589202a2b)


### Fase 2 - Implementación en la nube Kubernetes sobre Azure

![image](https://github.com/user-attachments/assets/700c7091-dce4-4cb3-9a9e-c675b4d62d36)

## Servicios Backend

### Product Catalog Service
Servicio que provee el catalogo de productos. Los datos son almacenados en una BD Cassandra. Consta de una Api para exponer todas las funcionalidades de ABM de un producto, y un Servicio Processor para brindar mantener la conectividad con los demas microservicios por medio interface gRPC.
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
Servicio encrgado de generar la orden de compra al confirmarla. Debe orquestar el manejo del stock, notificaciones por mail al cliente y mediar con el servidor de pagos y coordinar los plazos envío. Utilizag gRPC para realizar para dialogar con los demas servicios. Utiliza una BD Postgres

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
Servicio de carro de compras. Acumula los productos comprados por el cliente. Está suscrito a las notificaciones de los servicios de Catalog y Stock para saber en tiempo real si un producto esta disponible y tener su descripcion actualizada, y no esperar a refrescarla en el momento de confirmar la compra. También debe estar suscrito a las notificaciones de Order Service para destruir el carrito una vez confirmada la compra.
 
### Shipping Service

### Payment Service

### Notification Service

