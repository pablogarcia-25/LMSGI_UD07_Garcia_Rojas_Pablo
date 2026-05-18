## **MANUAL EXPLOTACIÓN**
## FASE 1:
Para la fase 1, se ha realizado el diseño y la programación la estructura XML de una plantilla de informe de factura personalizada para la empresa "WillmanTech S.L", la implementación de esta fase está situada en el archivo report_invoice_willmantech.xml

**¿Qué se ha empleado en el xml?**
En este archivo, se hace uso de directivas de iteración como t-foreach para desglosar las líneas de la factura(invoice_line_ids). También, se hace uso de la condición t-if para ocultar Descuento si no hay ninguna linea de la factura que lo tenga. Además, también se ha usado la directiva t-field para que se muestren los campos limpiamente como numero factura(doc.name), fecha emisión(doc.date) y total neto (doc.amount_total)

**¿Como he realizado esta actividad?**
Para empezar, he accedido a odoo con el fin de acceder al código de report_invoice_document. Después, he extraido las cosas importantes 

## Introducción y Arquitectura

## Guía de Instalación y Reinstalación

## Seguridad y Control de Acceso

## Procedimiento de Backup y Restauración

## Flujo Operativo de Facturación e Informes