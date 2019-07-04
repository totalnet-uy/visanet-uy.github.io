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

Dentro de [acceso.vnet.uy](https://acceso.vnet.uy) hay un link hacia un entorno [Kibana](https://www.elastic.co/es/products/kibana) que permite visualizar y hacer análisis con la información disponible.

![Link a kibana](/assets/img/acceso_link_cupones.png)

En Kibana de Visanet también se tiene acceso a la [consola para desarrolladores](https://acceso-web.vnet.uy/s/comercio/app/kibana#/dev_tools/console); herramienta importante durante la etapa de integración y la forma de obtener credenciales para acceder al endpoint publico (https://acceso-api.vnet.uy).

Por ejemplo, ejecutando `GET _security/_authenticate` ([ver documentación](https://www.elastic.co/guide/en/elasticsearch/reference/master/security-api-authenticate.html)) se obtiene información de las credenciales del usuario.

![Link a kibana](/assets/img/acceso_devtools.png)

En particular el contenido del array `saml_groups` indica a que comercios se tiene acceso.

```json
    "saml_groups" : [
      "02000043"
    ],
```

## API REST
Para poder usar la API desde un sistema externo se debe solicitar una `api_key` a Visanet y recibirá al e-mail registrado en https://acceso.vnet.uy la siguiente información:

```json
{
  "id" : "iiiiiiiiiiiiiiiiiiii",
  "name" : "email@dominio_YYYMMDD-HHMMSS",
  "api_key" : "kkkkkkkkkkkkkkkkkkkkkk"
}
```

De [acuerdo a la documentación](https://www.elastic.co/guide/en/elasticsearch/reference/current/security-api-create-api-key.html) el valor de `api_key` retornado puede usarse luego enviando un _request_ con un _Authorization header_ con un valor que tiene el prefijo `ApiKey` seguido de `credentials`, donde `credentials` es la codificación base64 de `id` y `api_key` unidas por dos puntos (`:`).

```bash
credentials=$(echo -n 'idididididididididid:kkkkkkkkkkkkkkkkkkkkkk' | openssl base64)
```

Luego se puede invocar la API por ejempo con [curl](https://curl.haxx.se/); en este caso invocando al [metodo count de la API de Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-count.html)

```bash
curl -s -H "Authorization: ApiKey $credentials" https://acceso-api.vnet.uy/cupones\*/_count | jq
```
Con el siguiente resultado:

```json
{
  "count": 7550,
  "_shards": {
    "total": 25,
    "successful": 25,
    "skipped": 0,
    "failed": 0
  }
}
```

## Formato cupones
La información transaccional de cupones se almacena en documentos en _indices_ mensuales llamados `cupones-YYYY-MM` y los _terceros_ pueden consultarlos de la forma que mejor entiendan usando la API de Elasticsearch.

La siguiente consulta devuelve únicamente un cupon:

```bash
curl -s -H "Authorization: ApiKey $credentials" https://acceso-api.vnet.uy/cupones\*/_search\?size\=1| jq
{
  "took": 33,
  "timed_out": false,
  "_shards": {
    "total": 25,
    "successful": 25,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 7550,
      "relation": "eq"
    },
    "max_score": 1,
    "hits": [
      {
        "_index": "cupones-2019-01",
        "_type": "doc",
        "_id": "_________",
        "_score": 1,
        "_source": {
          "@timestamp": "2019-01-09T03:00:00.000Z",
          "indice": "cupones",
          "transaccion": {
            "importe_devolucion_impuesto": 5.48,
            "autorizacion": "6____2",
            "ticket": 545,
            "lote": 72,
            "propina": 0,
            "producto": "VISA DEBITO",
            "terminal": "________",
            "origen": "Local",
            "importe": 167,
            "tipo_transaccion": "Compra",
            "numero_factura": 5____1,
            "devolucion_impuesto": 6,
            "fecha_proceso": "2019-01-09T03:00:00.000Z",
            "fecha_cupon": "2019-01-09T03:00:00.000Z",
            "moneda": 858,
            "sello": "visa",
            "canal": "POS ONLINE",
            "cuotas": 1,
            "modo": "modo_51"
          },
          "comercio": {
            "tipo_documento": "RUT Uruguay",
            "numero_de_documento": 21__________,
            "razon_social": "RAZON SOCIAL",
            "codigo_comercio": "________",
            "nombre_fantasia": "NOMBRE COMERCIO"
          },
          "@version": "1",
          "datos_especiales": {
            "iva_intereses_financiacion": 0,
            "cupon": __________,
            "intereses_financiacion": 0,
            "nro_pedido": "",
            "cashback": 0
          },
          "liquidacion": {
            "retencion_leyes_equivalente_$": 0,
            "arancel": 2.51,
            "porcentaje_beneficio_leyes": 0,
            "banco_acreditacion": "",
            "retencion_leyes": 0,
            "forma_pago": "D/E Std. Loc, 1 d háb, 1,50 %",
            "propina_excedente": 0,
            "fecha_pago": "2019-01-10T03:00:00.000Z",
            "iva_arancel": 0.55,
            "fecha_liquidacion": "2019-01-09T03:00:00.000Z",
            "adquirente": "visanet",
            "id": "E0109-P-1-00-00004606004-00",
            "plan_venta": "DEBITO / ELECTRON"
          }
        }
      }
    ]
  }
}
```

### Campos

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
