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
- Se utilizó un Ingress para el balanceo de carga en la capa de aplicación.
- Se configuró el acceso mediante HTTPS con un certificado SSL administrado por Cert-Manager y Let's Encrypt.
- Se implementó un dominio personalizado, **reto2-telematica.me**, utilizando Namecheap.

### 1.2. Aspectos NO cumplidos de la actividad propuesta por el profesor
- En lugar de Moodle, se utilizó Drupal después de numerosos intentos fallidos de implementación con Moodle, debido a la complejidad y las dificultades presentadas en el proceso.
- La alta disponibilidad en la capa de base de datos no se cumplió, ya que se implementó un único pod para MySQL.

## 2. Información general de diseño de alto nivel, arquitectura, patrones, mejores prácticas utilizadas
El diseño se basa en un clúster Kubernetes administrado en AWS (EKS) con un enfoque en la alta disponibilidad para la aplicación. La arquitectura incluye:
- Un clúster EKS con un grupo de 2 nodos.
- Ingress para balanceo de carga y manejo del tráfico entrante.
- Sistema de archivos distribuido con Amazon EFS para almacenar de manera persistente los archivos de la aplicación Drupal.
- Certificado SSL proporcionado por Cert-Manager y Let's Encrypt para habilitar HTTPS en el dominio personalizado.

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
