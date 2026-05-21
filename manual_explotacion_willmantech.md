## **MANUAL EXPLOTACIÓN WILLMANTECH**
## FASE 1:
Para la fase 1, se ha realizado el diseño y la programación la estructura XML de una plantilla de informe de factura personalizada para la empresa "WillmanTech S.L", la implementación de esta fase está situada en el archivo report_invoice_willmantech.xml

**¿Qué se ha empleado en el xml?**
En este archivo, se hace uso de directivas de iteración como t-foreach para desglosar las líneas de la factura(invoice_line_ids). También, se hace uso de la condición t-if para ocultar Descuento si no hay ninguna linea de la factura que lo tenga. Además, también se ha usado la directiva t-field para que se muestren los campos limpiamente como numero factura(doc.name), fecha emisión(doc.date) y total neto (doc.amount_total)

**¿Como he realizado esta actividad?**
Para empezar, he accedido a odoo con el fin de acceder al código de report_invoice_document. Después, he extraido las cosas importantes.

## FASE 2: Interoperabilidad de Datos (Extracción JSON/XML)
En esta fase nos preparado los formatos de intercambio para que el ERP pueda envie las facturas a aplicaciones externas. 

He creado dos archivos dentro de la carpeta interoperabilidad/:
1. **invoice_export.json:** Este archivo, finalmente no se hacía en esta actividad.

2. **invoice_ubl.xml:** Es una factura electrónica oficial con el estándar internacional UBL. Le he metido los espacios obligatorios (cac y cbc) y los identificadores europeos para que funcione al 100% con la red PEPPOL y no ponga ninguna pega.


## FASE 3: Manual Técnico de Explotación
El sistema implantado para WillmanTech S.L. se basa en el ecosistema ERP Odoo, aprovechando su arquitectura modular para la gestión empresarial.

**Introducción y Arquitectura**

**Guía de Instalación y Reinstalación**
Para levantar el entorno desde cero o realizar una reinstalación limpia, siga los siguientes pasos:

Requisitos Previos: Tener instalados docker y docker-compose en el servidor de explotación.

Configuración del Entorno: Crear un archivo docker-compose.yml con la siguiente estructura y variables de entorno esenciales:
Crea una carpeta para el proyecto y guarda este archivo:

```yaml
version: '3.8'

services:
  db:
    image: postgres:15
    environment:
      - POSTGRES_DB=postgres
      - POSTGRES_USER=odoo
      - POSTGRES_PASSWORD=mi_clave_secreta_db
    volumes:
      - odoo-db-data:/var/lib/postgresql/data

  web:
    image: odoo:16.0
    depends_on:
      - db
    ports:
      - "8069:8069"
    environment:
      - HOST=db
      - USER=odoo
      - PASSWORD=mi_clave_secreta_db
    volumes:
      - odoo-web-data:/var/lib/odoo
      - ./addons:/mnt/extra-addons

volumes:
  odoo-db-data:
  odoo-web-data:
```

Despliegue: Ejecutar el comando en la raíz del directorio: docker-compose up -d

Carga de Módulos: Copiar los archivos desarrollados (report_invoice_willmantech.xml e invoice_ubl.xml) en la ruta mapeada de addons, actualizar la lista de módulos en Odoo e instalarlos. 


**Seguridad y Control de Acceso**
Para proteger la información y que nadie acceda a datos que no le corresponden, he configurado un control de accesos estricto basado en tres perfiles o roles clave:

Administrador: Acceso absoluto. Es el encargado de mantener el servidor, instalar módulos nuevos, configurar los parámetros generales del ERP y retocar las plantillas QWeb.

Contable: Permisos completos de edición, validación y consulta sobre el módulo de Facturación. Puede asentar facturas borrador, gestionar los impuestos y exportar los ficheros XML y JSON de interoperabilidad contable.

Comercial: Permisos restringidos. Solo puede crear nuevos contactos y rellenar presupuestos o pedidos de venta. Puede generar facturas pero se quedan guardadas exclusivamente en modo "Borrador" hasta que un contable las revise y valide.

Política de contraseñas: Se exige de forma obligatoria que las contraseñas tengan un mínimo de 10 caracteres, combinando mayúsculas, minúsculas, números y algún símbolo especial. Las sesiones web expiran automáticamente tras 30 minutos de inactividad.

**Procedimiento de Backup y Restauración**

Para garantizar que nunca se pierda una factura si el sistema falla, he preparado un mecanismo de copias de seguridad en caliente que no obliga a detener el ERP.

Comando para realizar el Backup:
Ejecuta el siguiente comando en la terminal para extraer un volcado completo de la base de datos PostgreSQL con la fecha del día actual:

docker exec -t $(docker ps -qf "name=db") pg_dump -U odoo -F c -b -v -f /var/lib/postgresql/data/backup_willmantech_$(date +%Y%m%d).dump postgres


**Flujo Operativo de Facturación e Informes**
Flujo Operativo de Facturación e Informes

Ciclo en la Interfaz: El usuario va a Facturación -> Crear, elige el cliente (se cargan sus datos de golpe), añade los productos en las líneas (el descuento activa la columna mediante t-if) y guarda el Borrador. Al revisar, pulsa Confirmar para generar el número definitivo (INV/2026/05/0012) y asentar la contabilidad.

Pipeline Técnico del PDF: Al pulsar imprimir, se activa este flujo en tres pasos:

QWeb (HTML): Odoo inyecta los datos (doc.name, doc.amount_total) en la plantilla XML y genera un archivo HTML con estilos Bootstrap.

Conversión (wkhtmltopdf): El motor WebKit de la herramienta procesa ese HTML invisiblemente y calcula los saltos de página y márgenes.

Binario (PDF): Se genera el archivo .pdf definitivo, listo para descargar o mandar por email de forma automática.