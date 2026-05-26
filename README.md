# Microservicios Backend - EV2 Innovatech 

Este repositorio contiene los microservicios de **Ventas** y **Despachos** para la infraestructura de Innovatech Chile, desplegados mediante una estrategia de microservicios contenerizados en AWS EC2.

## 🏗️ Arquitectura de Servicios
El repositorio está dividido en dos directorios principales, cada uno con su propio ciclo de vida y configuración:
- `/Springboot-API-REST-VENTA`: Microservicio de gestión de ventas.
- `/Springboot-API-REST-DESPACHO`: Microservicio de gestión de despachos.

Ambos utilizan **Docker multi-stage builds** para optimizar el despliegue y garantizar seguridad mediante ejecución con usuario no root (`appuser`).

## 🚀 Automatización CI/CD
El pipeline (`deploy-back.yml`) está configurado para gestionar ambos servicios de forma independiente en una sola ejecución:
1. **Detección de Cambios**: El pipeline construye y publica imágenes de forma separada para cada carpeta.
2. **Publicación**: Las imágenes se versionan y almacenan en **Amazon ECR**.
3. **Despliegue Dinámico**: Se utiliza **AWS Systems Manager (SSM)** para inyectar configuraciones (`docker-compose.yml`) de forma segura y actualizar los servicios en sus respectivas instancias EC2.

## Arquitectura de Contenedores y Base de Datos
La solución utiliza `docker-compose.yml` para orquestar los servicios, logrando un despliegue cohesivo:
- **Orquestación de Stack**: Al ejecutar `docker-compose up -d`, el sistema levanta automáticamente dos contenedores:
    - **Microservicio Backend**: La API Java Spring Boot.
    - **Base de Datos**: Un contenedor independiente de **MySQL 8.0**.


## 🔐 Seguridad y Persistencia
- **Gestión de Secrets**: Credenciales de AWS, tokens y variables de despliegue se manejan mediante *GitHub Repository Secrets*.
- **Persistencia de Datos**: Se han configurado **Named Volumes** (`despacho_db_data` / `ventas_db_data`) vinculados al directorio `/var/lib/mysql` dentro de los contenedores.
- **Aislamiento**: Los microservicios operan en subredes privadas, siendo inaccesibles desde internet, manteniendo el acceso restringido según las políticas de seguridad de AWS.

## 🛠️ Despliegue y Operación
Esta sección detalla los procedimientos para la puesta en marcha de los servicios, diferenciando entre el entorno de desarrollo local y la infraestructura de producción en AWS.

### Requisitos Previos
Para asegurar la correcta ejecución del stack, asegúrese de contar con:
- **Docker Engine** (v20.10+) y **Docker Compose** (v2.0+).
- Variables de entorno cargadas en el host (o configuradas en el archivo `.env`):
  - `DB_ENDPOINT`, `DB_PORT`, `DB_NAME`, `DB_USERNAME`, `DB_PASSWORD`.

### Procedimiento de Despliegue
El stack de servicios (Backend + Base de Datos MySQL) se orquesta de manera modular:

1. **Acceso al directorio del servicio**:
   ```bash
   cd Springboot-API-REST-VENTA  # O Springboot-API-REST-DESPACHO
2. **Ejecución del stack**:
   ```bash
   docker-compose up -d
