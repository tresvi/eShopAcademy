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

#### Esquema de datos

### Order Service

### Basket Service

### Shipping Service

### Payment Service

### Notification Service

