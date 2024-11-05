# ST0263: Tópicos Especiales en Telemática, 2024-2

## Estudiantes:
- Vanessa Alexandra Velez Restrepo, vavelezr@eafit.edu.co
- Samuel Oviedo Paz, soviedop@eafit.edu.co

## Profesor: Alvaro, @eafit.edu.co

---

# Reto 2 - Despliegue de Drupal en Kubernetes

## 1. Descripción breve de la actividad
El objetivo de este reto es desplegar un sistema de gestión de contenido (CMS) en un clúster Kubernetes en la nube, utilizando el servicio EKS de AWS. Aunque inicialmente se intentó realizar la actividad con Moodle (como se sugirió en la actividad), se encontró que no era viable después de varios intentos, por lo que se optó por utilizar Drupal como alternativa para implementar la solución.

### 1.1. Aspectos cumplidos de la actividad propuesta por el profesor
- Se desplegó la aplicación en un clúster Kubernetes en AWS utilizando EKS.
- Se configuró un sistema de archivos distribuido utilizando Amazon EFS.
- Se configuró un balanceador de carga mediante un recurso Ingress de Nginx para gestionar el tráfico entrante y ofrecer una única interfaz al usuario.
- Se configuró el acceso mediante HTTPS con un certificado SSL administrado por Cert-Manager y Let's Encrypt.
- Se implementó un dominio personalizado, **reto2-telematica.me**, utilizando Namecheap.

### 1.2. Aspectos NO cumplidos de la actividad propuesta por el profesor
- En lugar de Moodle, se utilizó Drupal después de numerosos intentos fallidos de implementación con Moodle, debido a la complejidad y las dificultades presentadas en el proceso.
- La alta disponibilidad en la capa de base de datos no se cumplió, ya que se implementó un único pod para MySQL.

## 2. Información general de diseño de alto nivel, arquitectura, patrones, mejores prácticas utilizadas
El diseño se basa en un clúster Kubernetes administrado en AWS (EKS) con un enfoque en la alta disponibilidad para la aplicación. La arquitectura incluye:
- Un clúster EKS con un grupo de 2 nodos.
  ![image](https://github.com/user-attachments/assets/9fe3b963-33f6-4665-a052-7cabc6f63424)

- Uso de un recurso Ingress de Nginx como balanceador de carga, proporcionando una entrada única para los servicios y gestionando el tráfico hacia los pods de Drupal.
- Sistema de archivos distribuido con Amazon EFS para almacenar de manera persistente los archivos de la aplicación Drupal, configurado a través de un StorageClass, un PersistentVolume (PV) y un PersistentVolumeClaim (PVC).
- Certificado SSL proporcionado por Cert-Manager y Let's Encrypt para habilitar HTTPS en el dominio personalizado.
![image](https://github.com/user-attachments/assets/231d5448-0aae-46e5-a57d-ac0a8948bb48)

### Detalles adicionales sobre los componentes clave:

#### Balanceador de carga con Ingress de Nginx
Para gestionar el tráfico entrante hacia la aplicación Drupal, se configuró un recurso Ingress utilizando Nginx. Este recurso permite:
- Definir reglas de enrutamiento para dirigir el tráfico hacia los servicios adecuados dentro del clúster.
- Proporcionar una única URL de entrada (reto2-telematica.me), simplificando el acceso para los usuarios.
- Integración con Cert-Manager para manejar automáticamente los certificados SSL y habilitar HTTPS en el dominio, garantizando seguridad en las conexiones.

#### Sistema de archivos distribuido con Amazon EFS
Amazon EFS (Elastic File System) se utilizó para proporcionar almacenamiento compartido entre los pods de Drupal, garantizando persistencia en los datos necesarios para la aplicación. La configuración incluyó:
- **StorageClass**: Define el controlador de EFS para gestionar el almacenamiento en AWS y permite crear volúmenes de manera dinámica.
- **PersistentVolume (PV)**: Almacena físicamente los datos en EFS, proporcionando un espacio de almacenamiento persistente que puede ser utilizado por cualquier pod dentro del clúster.
- **PersistentVolumeClaim (PVC)**: Permite a los pods reclamar espacio de almacenamiento del PV, asegurando que el sistema de archivos esté disponible para Drupal. En este caso, el PVC llamado `eks-drupal` fue montado en varias rutas dentro del contenedor Drupal para almacenar módulos, perfiles, sitios y temas, manteniendo así la consistencia en el almacenamiento de datos críticos.

#### Base de datos MySQL
Se implementó una base de datos MySQL en un único pod dentro del clúster Kubernetes, que sirve como backend de datos para Drupal. La configuración incluyó:
- **Service**: Un servicio de tipo ClusterIP que permite la comunicación entre Drupal y MySQL dentro del clúster. La configuración del puerto es el 3306, el puerto predeterminado para MySQL.
- **Deployment**: Un despliegue con un único pod de MySQL. Aunque este pod se reinicia automáticamente si falla, esta configuración no garantiza alta disponibilidad, ya que solo hay una instancia de la base de datos en funcionamiento.
- **Volumen persistente**: La base de datos utiliza un PersistentVolumeClaim llamado `eks-mysql` para almacenar sus datos en EFS, asegurando que la información persista incluso si el pod se reinicia.

#### Drupal
Drupal fue desplegado con las siguientes configuraciones:
- **Service**: Un servicio de tipo ClusterIP para exponer la aplicación Drupal dentro del clúster y permitir el enrutamiento de tráfico desde el Ingress.
- **Deployment**: Un despliegue configurado con 3 réplicas de Drupal para garantizar que la aplicación esté disponible en múltiples instancias, proporcionando así redundancia y mejorando la capacidad de respuesta ante aumentos en la carga.
- **Variables de entorno**: Configuración de variables de entorno que permiten que Drupal se conecte a MySQL. Estas incluyen:
  - `DRUPAL_DB_HOST`: Dirección del servicio MySQL.
  - `DRUPAL_DB_PASSWORD`: Contraseña de acceso a la base de datos.
- **Montaje de volúmenes**: El PVC `eks-drupal` fue montado en varias rutas del contenedor de Drupal para almacenar datos persistentes, como módulos, perfiles, sitios y temas, asegurando así que estos datos estén disponibles de forma consistente en cada instancia del pod.

## 3. Descripción del ambiente de desarrollo y técnico
- **Lenguaje de programación**: No se usó un lenguaje de programación específico; toda la configuración se realizó mediante archivos YAML.
- **Librerías y paquetes**:
  - **Cert-Manager** para la gestión automática de certificados SSL.
  - **AWS EFS** para el almacenamiento persistente.
- **Cómo se compila y ejecuta**:
  - Todos los manifiestos y configuraciones se implementan mediante `kubectl apply` desde la AWS CloudShell.
- **Detalles técnicos**:
  - **Parámetros de configuración**:
    - **Dominio**: reto2-telematica.me
    - **Balanceador de carga**: Implementado mediante Ingress.
    - **Certificado SSL**: Generado con Cert-Manager y Let's Encrypt.
    - **Conexión al sistema de archivos**: EFS de AWS montado en el clúster Kubernetes.

## 3.1. Estado de los Pods y Servicios en el clúster
A continuación se muestra el estado de los pods y servicios en el clúster, donde se incluyen los pods de Drupal, MySQL, el provisionador de EFS y el controlador Nginx para Ingress. También se observa la IP externa asignada al Ingress para el acceso a la aplicación.
### Estado de los Pods
![image](https://github.com/user-attachments/assets/2e09e496-aec8-433b-bfa8-405004efa174)

### Estado de los servicios
![image](https://github.com/user-attachments/assets/fdcb8c56-63ea-4f5b-a199-93ecc27a397a)


## Archivos importantes del proyecto:
En el directorio raíz se encuentran los siguientes archivos YAML clave para la configuración del despliegue:
- `certificate.yaml`
- `clusterissuer.yaml`
- `drupal-ingress.yaml`
- `drupaldeploy.yaml`
- `efsprovisioner.yaml`
- `mysqldeploy.yaml`
- `rolebinding.yaml`
- `storageclass.yaml`

## 5. Otra información relevante para esta actividad
- La implementación de EFS permitió configurar un almacenamiento compartido entre los nodos del clúster.
- Cert-Manager automatiza la renovación de los certificados SSL con Let's Encrypt, facilitando el uso de HTTPS en el dominio personalizado.


## Referencias:
