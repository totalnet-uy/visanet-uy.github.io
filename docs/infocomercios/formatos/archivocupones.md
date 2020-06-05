---
layout: page
title: ArchivoCupones
parent: formatos
nav_exclude: true
nav_order: 1
---


# Formato archivo cupones liquidados por sucursal


* Nombre de archivo: `CUPONES_ccccccc.aaaammdd`
  - `ccccccc` -> número de comercio sin sucursal
  - `aaaammdd` -> fecha de liquidacion de los cupones

* Formato: ASCII

* Largo de registros:	280

* Formatos de registros:
  - Registro cabezal (primer registro del archivo)
  - Registros detalle   (registros detalle)
  - Registro totales   (último registro del archivo)

## Descripción Registro cabezal

Dos líneas de textos fijos, el primero con cabezales descriptivos y el segundo con subrayado.


## Descripción Registro detalle

|Nombre campo|Tipo|Posición|Largo|Formato/Valores fijos|
|------------|----|--------|-----|---------------------|
|Sucursal|Numérico|8-10|3
|Fecha compra|Numérico|15-22|8|AAAAMMDD
|Fecha liquidación|Numérico|33-40|8|AAAAMMDD
|Fecha proceso 1|Numérico|47-54|8|AAAAMMDD
|Fecha proceso 2|Numérico|56-63|8|DDMMAAAA (Ídem valor anterior)
|Numero sobre|Numérico|69-74|6
|Id.  Cupon|Numérico|77-85|9
|Moneda|Numérico|94-96|3|858 = pesos / 840 = dólares
|Importe cupon|Numérico|98-113|16|-999999999999.99
|Tarjeta enmascarada|Alfanum.|115-133|19
|Plan venta|Numérico|142-144|3
|Devolución|Alfanum.|146|1|S = dev. / blanco = compra
|Autorización|Alfanum.|148-153|6
|Nro_pedido|Alfanum.|155-179|25
|Nro. Factura|Numérico|186 -191|6
|Propina|Numérico|193-208|16|-999999999999.99
|Propina excedente|Numérico|211-226|16|-999999999999.99
|Cash back|Numérico|228-243|16|-999999999999.99
|Id. Liquidación Visa|Alfanum|245-274|30
|Nro. POS|Alfanum|276-283|8
|Nro. Lote|Numerico|289-294|6
|Boca de Entrada|Numerico|306-307|2


Ejemplo de un registro de detalle correspondiente a una devolución:
```
         1    20170511          20170518      20170511 11052017     899999  277794612        840            -7.01    455189------2240          1 S 780518                                   444             0.00              0.00             0.00 G0518-D-1-00-02009043001-00
```

Ejemplo de un registro de detalle correspondiente a una compra:
```
         1    20170511          20170518      20170516 16052017     500000  277794628        858            15.00    481521------1769        300   780524                                    55             0.00              0.00             0.00 G0518-P-1-00-02009043001-00
```

## Descripción Registro totales

Dos líneas, una en blanco y otra con texto conteniendo el número de registros contenidos.

Ejemplo:
```
 1393 rows selected.
```
