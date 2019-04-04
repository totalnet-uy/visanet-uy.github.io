---
layout: page
title: Cupones
parent: InfoComercios
nav_order: 2
---


# _"Cupones"_ Visanet

Visanet Uruguay pone a disposición de comercios e integradores (de aquí en mas llamados _terceros_) una nueva modalidad para compartir información adicional a la que es enviada en los documentos de liquidación y facturación. A diferencia de soluciones anteriores basadas en archivos, la información se disponibiliza por medio de API REST y herramientas de análisis de datos que permiten integración con los sistemas del tercero.

En vez de definir una API propietaria, Visanet decidió adoptar la [API de Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html), una solución de _código abierto_ con gran adopción en el mercado.

## Permisos

Para poder acceder a la información el tercero deberá autenticarse en https://acceso.vnet.uy usando una cuenta existente o creando una nueva según en la [documentación de acceso.uy](/docs/infocomercios/acceso/). Luego de autenticado, el tercero se debe contactar con Visanet indicando las credenciales y solicitando permisos para el o los números de comercio de los cuales quiere obtener información; Visanet validará la solicitud y si corresponde asignará los permisos sobre indices `cupones*`.

El rol asignado en Elasticsearch tiene implementado [Document Level Security](https://www.elastic.co/guide/en/elastic-stack-overview/current/document-level-security.html) de forma cada tercero tendrá acceso a los documentos en los cuales el campo `comercio.codigo_comercio` esté dentro de los comercios autorizados.

## Consola para desarrolladores

Dentro de https://acceso.vnet.uy hay un link hacia un entorno [Kibana](https://www.elastic.co/es/products/kibana) que permite visualizar y hacer análisis con la información disponible.

![Link a kibana](/assets/img/acceso_link_cupones.png)

Dentro del entorno Kibana de Visanet también se tiene acceso a la [consola para desarrolladores](https://acceso-web.vnet.uy/s/comercio/app/kibana#/dev_tools/console); herramienta importante durante la etapa de integración y la forma de obtener credenciales para acceder al endpoint publico (https://acceso-api.vnet.uy).

Por ejemplo, ejecutando `GET _security/_authenticate` ([ver documentación](https://www.elastic.co/guide/en/elasticsearch/reference/master/security-api-authenticate.html)) se obtiene información de las credenciales del usuario.

![Link a kibana](/assets/img/acceso_devtools.png)

En particular el contenido del array `saml_groups` indica a que comercios se tiene acceso.

```json
    "saml_groups" : [
      "02000043"
    ],
```

## API REST
Por el momento la única opción habilitada para obtener credenciales e invocar el endpoint publico es [obteniendo un **token**](https://www.elastic.co/guide/en/elasticsearch/reference/current/security-api-get-token.html)

Al ejecutar:
```json
POST _xpack/security/oauth2/token
{
  "grant_type" : "client_credentials"
}
```
el resultado es:

```json
{
  "access_token" : "077yAiBtUKlPUKsRwHA/Mg4lXcc3V4MsU/GvFkedoWe2Wy3MqQjA0M7WtJRQPQyWYgQ7lY1jeu7DefBBfNcf+n1KeecBWZAZRtYqCVZyK6//UcRUUSrQZynvflvZDeSkNt8=",
  "type" : "Bearer",
  "expires_in" : 1200
}
```
Luego se puuede invocar la API por ejempo con [curl](https://curl.haxx.se/); en este caso invocando al [metodo count de la API de Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-count.html)

```bash
curl -H "Authorization: Bearer 077yAiBtUKlPUKsRwHA/Mg4lXcc3V4MsU/GvFkedoWe2Wy3MqQjA0M7WtJRQPQyWYgQ7lY1jeu7DefBBfNcf+n1KeecBWZAZRtYqCVZyK6//UcRUUSrQZynvflvZDeSkNt8=" https://acceso-api.vnet.uy/cupones\*/_count
```
Con el siguiente resultado:

```json
{
  "count":6427,
  "_shards":{
    "total":25,
    "successful":25,
    "skipped":0,
    "failed":0
  }
}
```


> nota: con este método el tóken tiene una validez de 20 minutos; a futuro se habilitará la [utilización de API_KEY](https://www.elastic.co/guide/en/elasticsearch/reference/current/security-api-create-api-key.html)

## Campos

|campo|tipo|descripcion|
|-----|----|----------|
|transaccion.sello|Alfanumérico|Identificador del sello (Amex, Anda, Cabal, Creditel, MasterCard, Oca, Visa, etc).
|transaccion.terminal|Alfanumérico|Indentificador único de la terminal en la cual se realizó la transacción.
|transaccion.fecha_cupon|Fecha Unix TimeStamp|Fecha en la cual se realizó la transacción.
|transaccion.fecha_proceso|Fecha Unix TimeStamp|Fecha en la cual se procesó la transacción por el adquirente para su posterior liquidación
|transaccion.autorizacion|Alfanumérico|Código de aprobación de la transaccion recibido por el comercio.
|transaccion.numero_factura|Numérico Entero|Numero de comprobante fiscal enviado por el comercio asociado a la transacción.
|transaccion.moneda|Numérico Entero|Código numérico ISO de la moneda de la transacción (ejemplo $- 858 - U$S 840)
|transaccion.importe|Numérico punto flotante|importe total de la transacción (incluye monto por ej propina, cashback)
|transaccion.propina|Numérico punto flotante|Monto propina total ingresada por el comercio.
|transaccion.lote|Numérico Entero|Número de lote de la terminal al momento de hacer la transacción en caso que corresponda.
|transaccion.origen|Alfanumérico|Identificador del origen del medio de pago utilizado en la transacción (Ej Local/ Extranjera)
|transaccion.devolucion_impuesto|Numérico Entero|Identificador del beneficio fiscal otorgado a la transacción (xx: INDI / 00: NO Aplica Devolución / 99: No aplica campo)
|transaccion.importe_devolución_impuesto|Numérico punto flotante|Monto del befefico fiscal otorgado.
|transaccion.cuotas|Numérico Entero|Cantidad de cuotas ingresada por el comercio.
|transaccion.producto|Alfanumérico|Identificador del tipo del medio de pago (ej: Visa Debito, Cabal BPS Prestaciones, Lider, OCA BPS Prestaciones, Creditel Prepaga, Anda Alimentación
|transaccion.tipo_transaccion|Alfanumérico|Identificador del tipo de transacción (ej: Compra, Devolución, etc).
|transaccion.BIN|Numérico Entero|Primeros 6 dígitos del medio de pago utilizado en la transacción.
|transaccion.ultimos_4_Digitos|Numérico Entero|Ultimos 4 dígitos del medio de pago utilizado en la transacción.
|transaccion.canal|Alfanumérico|Medio por el cual se inició la transacción (ej: POS, Manual, e-commerce, Débito automático, etc).
|transaccion.ticket|Numérico Entero|Número de ticket dentro del lote(si corresponde).
|transaccion.modo|Alfanumérico|Modo de ingreso del medio de pago en la transacción (ej: manual, banda, chip, contactless,etc)
|transaccion.plan|Alfanumérico|Identificador del plan comercial ingresado por el comercio al momento de la transacción (ej: Común, Nafta, Agro, Especial,etc)
|comercio.nombre_fantasia|Alfanumérico|Nombre fantasía que identifica el comercio
|comercio.razon_social|Alfanumérico|Razón social que identifica el comercio.
|comercio.tipo_documento|Alfanumérico|Identificador del tipo de documento del contribuyente (ej: RUT, Cedula, Pasaporte).
|comercio.numero_de_documento|Numérico Entero|Número del documento del contribuyente.
|comercio.codigo_comercio|Numérico Entero|Identificador del comercio ante el adquierente.
|liquidacion.adquirente|Alfanumérico|Identificador del adquirente de la transacción.
|liquidacion.id|Alfanumérico|Identificador del proceso donde se liquida la transacción.
|liquidacion.fecha_liquidacion|Fecha Unix TimeStamp|Fecha en la cual se liquidó la transacción para su posterior pago.
|liquidacion.plan_venta|Alfanumérico|Identificador del grupo de condiciones comerciales para la liquidación.
|liquidacion.propina_excedente|Numérico punto flotante|Monto de la parte de la propina sobre la cual se calcula arancel (si corresponde).
|liquidacion.fecha_pago|Fecha Unix TimeStamp|Fecha en la cual se efectiviza el pago de la transacción al comercio.
|liquidacion.banco_acreditacion|Alfanumérico|Identificador de la institución financiera en la cual se acreditó el pago.
|liquidacion.retencion_leyes|Numérico punto flotante|Monto del beneficio fiscal en la moneda de la transacción (si corresponde).
|liquidacion.retencion_leyes_equivalente_$|Numérico punto flotante|Monto del beneficio fiscal expresado en pesos uruguayos.
|liquidacion.porcentaje_beneficio_leyes|Numérico punto flotante|Puntos porcentuales del beneficio fiscal.
|liquidacion.arancel|Numérico punto flotante|Monto del arancel aplicado a la transacción por el adquirente (cobrado/devuelto).
|liquidacion.iva_arancel|Numérico punto flotante|Monto del iva del arancel aplicado a la transacción por el adquirente.
|liquidacion.forma_de_pago|Alfanumérico| Medio por el cual se efectiviza el pago al comercio (ej: Efectivo, Transferencia, Cheque, Retenido, Correo,etc)
|datos_especiales.IDWT|Alfanumérico|Código Aerolineas
|datos_especiales.cashback|Numérico punto flotante|importe del dinero entregado al th por parte del comercio
|datos_especiales.codigo_minorista|Aplica para terminales que manejan Mayoristas
|datos_especiales.código_mayorista|Aplica para terminales que manejan Mayoristas
|datos_especiales.intereses_financiacion|Numérico punto flotante|
|datos_especiales.Iva_intereses_financiacion|Numérico punto flotante|
|datos_especiales.fecha_entrega_cheque|Fecha Unix TimeStamp|
|datos_especiales.nro_pedido|
