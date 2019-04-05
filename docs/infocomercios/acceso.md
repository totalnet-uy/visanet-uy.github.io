---
layout: page
title: Acceso
parent: InfoComercios
nav_order: 1
---

# acceso.vnet.uy

Visanet Uruguay pone a disposición de comercios e integradores [acceso.vnet.uy](https://acceso.vnet.uy), una nueva herramienta para compartir información adicional a la que es enviada en los documentos de liquidación y facturación.

En esta etapa la herramienta permite el acceso a archivos procesables con los formatos ya existentes de modo de mantener compatibilidad con los sistemas actuales, pero se irán agregando funcionalidades y formatos a futuro.

## Modalidades de acceso

### WEB
[acceso.vnet.uy](https://acceso.vnet.uy) es un entorno web que permite de una forma muy sencilla la descarga de los archivos que Visanet Uruguay disponibiliza.

Los navegadores soportados son:

* Mozilla Firefox 14+
* Google Chrome/Chromium 18+
* Safari 7+
* Internet Explorer 11+
* Microsoft Edge


### Aplicaciones
Mediante una aplicación de escritorio o para celulares es posible visualizar y descargar los archivos; inclusive hay clientes que se integran al sistema de archivos del dispositivo de forma similar a Dropbox o OneDrive.

La plataforma esta basada en Nextcloud y utiliza el protocolo WebDAV por lo que aparte de las aplicaciones Nextcloud se puede utilizar cualquier aplicación que sea compatible con el protocolo WebDAV como por ejemplo [cyberduck](https://cyberduck.io/webdav/).  En [el sitio de descargas de Nextcloud](https://nextcloud.com/install/#install-clients) se pueden obtener aplicaciones para diferentes sistemas operativos.

> [Referencia de uso de WebDAV](https://docs.nextcloud.com/server/stable/user_manual/files/access_webdav.html)

### API
Para facilitar la integración con los sistemas del comercio, también se disponibiliza acceso mediante APIs WebDAV.

La documentación de la API está disponible en la [documentacion de Nextcloud](https://docs.nextcloud.com/server/stable/developer_manual/client_apis/WebDAV/index.html)

#### API Key
Para poder utilizar la API, es necesario crear una contraseña de aplicación en las [preferencias de seguridad](https://acceso.vnet.uy/index.php/settings/user/security)


## Registro / Autenticación
Cada usuario que desee acceder a [acceso.vnet.uy](https://acceso.vnet.uy) puede optar entre:
* Crear una cuenta en [acceso.vnet.uy](https://acceso.vnet.uy) utilizando cualquier dirección de correo electrónico.
* Utilizar una cuenta existente de Google o Microsoft.

Luego de esto el usuario podrá entrar a [acceso.vnet.uy](https://acceso.vnet.uy) pero **no tendrá acceso a ningún archivo**, hasta que Visanet le conceda permisos según corresponda.


## Permisos
Los archivos se organizan en carpetas que tienen como nombre el número de comercio.

Para que le sean otorgados permisos sobre los archivos, debe ponerse en contacto con [atcomercial@visanet.com.uy](mailto:atcomercial@visanet.com.uy) informando la dirección de correo electrónico que utilizó para registrase

Luego de que se le otorgue permisos debe salir y volver a iniciar sesión en [acceso.vnet.uy](https://acceso.vnet.uy) para poder visualizar los archivos compartidos.
