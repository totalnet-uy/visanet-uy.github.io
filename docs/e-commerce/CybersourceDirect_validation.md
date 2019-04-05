---
layout: page
title: CybersourceDirect Validation
parent: e-commerce
nav_order: 2
---


# Validación conexión directa a Cybersource

Este documento aplica a terceros con conectividad directa a Cybersource para transacciones con adquirencia de Visanet Uruguay.

Referencia: https://visanet-uy.github.io/VisanetDigital/CybersourceDirect


# Pruebas
Se deben realizar las siguientes operaciones en el día coordinado

|ref.|Categoria|Operacion|cuotas|monto|comentarios|
|---|---------|---------|------|-----|-----------|
|1.1|Validacion de cuenta|Solicitud de autorización por monto cero ($0) sin CVV2|-|0||
|1.2|Validacion de cuenta|Solicitud de autorización por monto cero ($0) con CVV correcto|-|0||
|1.3|Validacion de cuenta|Solicitud de autorización por monto cero ($0) con CVV incorrecto|-|0||
|2.1|Compras Tarjeta Credito|Solicitud de autorización + Captura en misma llamada|1|$U 21|   |
|2.2|Compras Tarjeta Credito|Solicitud de autorización|1|$U 22|   |
|2.3|Compras Tarjeta Credito|Captura ref. 2.2 ||$U 22|   |
|2.4|Compras Tarjeta Credito|Solicitud de autorización|1|$U 24|   |
|2.5|Compras Tarjeta Credito|Reverso de ref. 2.4|   |   |   |
|3.1|Cancelacion Tarjeta Credito|Solicitud de autorizacioń + Captura en misma llamada|1|$U 31|   |
|3.2|Cancelacion Tarjeta Credito|Void +  Reverso de ref. 3.1 |   |   |   |
|4.1|Compras Tarjeta Credito Cuotas|Solicitud de autorización + Captura en misma llamada|2|$U 41|   |
|4.2|Compras Tarjeta Credito Cuotas|Solicitud de autorización + Captura en misma llamada|6|$U 42|   |
|5.1|Compras Tarjeta Credito Dolares|Solicitud de autorización + Captura en misma llamada|1|USD 0.51|
|6.1|Devoluciones Tarjeta Credito|Solicitud de autorizacioń+ Captura en misma llamada|1|$U 61|   |
|6.2|Devoluciones Tarjeta Credito|Credito de ref. 6.1||$U 14|al dia siguiente de ref. 6.1|
|7.1 |Compras Tarjeta Debito/Prepago|Solicitud de autorización + Captura en misma llamada|1|$U 71|INDI=0|
|7.2 |Compras Tarjeta Debito/Prepago|Solicitud de autorización + Captura en misma llamada|1|$U 72|INDI=1|
|7.3 |Compras Tarjeta Debito/Prepago|Solicitud de autorización + Captura en misma llamada|1|$U 73|INDI=6|
|8.1 |Devolucion Tarjeta Debito/Prepago|Solicitud de autorización + Captura en misma llamada|1|$U 20000|INDI=6 / Validar maximos de devolución según ley 19.210|
|8.2 |Devolucion Tarjeta Debito/Prepago|Void + Reverso de ref. 8.1||||


# Reporte
Deberan ser enviados los siguientes recursos
* Descripción operativa
  - Modos de integración con Cybersource (Secure Aceptance, API, etc)
  - Descripción de la política de re-intentos implementada
  - Descripción de los MDDs que serán utilizados adicionales a los ya definidos en https://visanet-uy.github.io/VisanetDigital/CybersourceDirect#datos-adicionales-a-la-transacci%C3%B3n
  - Estrategias de prevención de fraude, uso de Desicion Manager (ej. detalles de implementación de Device Fingerprint, etc)
* Información de cuenta utilizada para las pruebas (MID, Número de Comercio, etc)
* Archivo conteniendo los siguientes campos para cada operación de las pruebas, o justificación en caso de no aplicar.
  - Datos monetarios de la transacción (Monto, Monto Gravado, Monto devolucion, INDI)
  - Mascara del PAN (ej. 411111xxxxxx1234)
  - Cybersource ID
  - MDDs de la transacción
