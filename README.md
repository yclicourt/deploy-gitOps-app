# 🚀 TodoApp - GitOps Infrastructure

Este repositorio contiene los manifiestos de Kubernetes y la configuración de GitOps utilizando **ArgoCD** para el despliegue automatizado de la aplicación de tareas (TodoApp).

## 🗺️ Arquitectura del Proyecto

El proyecto está dividido en tres componentes principales:
* 🖥️ **[Frontend (React)](git@github.com:yclicourt/frontend-tasks-app.git)**: Cliente web para interactuar con las tareas.
* ⚙️ **[Backend (Django DRF)](git@github.com:yclicourt/backend-tasks-app.git)**: API REST que maneja la lógica de negocio y persistencia.
* ☸️ **[GitOps (ArgoCD)](git@github.com:yclicourt/deploy-gitOps-app.git)**: Estructura en formato Helm Chart que orquesta el despliegue de todos los servicios mediante ArgoCD.


## 📁 Estructura del Repositorio

```text
.
├── my-stack/                  # Helm Chart principal de la aplicación
│   ├── templates/             # Plantillas de los manifiestos de Kubernetes
│   │   ├── backend/           # Deployment, Service y ConfigMaps de la API Django
│   │   ├── db/                # Manifiestos para la Base de Datos (PostgreSQL/SQLite)
│   │   └── frontend/          # Deployment, Service e Ingress del Cliente React
│   ├── Chart.yaml             # Metadatos e información del Helm Chart
│   └── values.yaml            # Variables de configuración globales del entorno
└── README.md
```

## 🐳 Gestión de Imágenes y Container Registry

Las plantillas de Kubernetes en este Helm Chart están diseñadas para ser completamente agnósticas al registro de contenedores. Por defecto, el proyecto apunta a un registro local (`registry.local:5443`), pero **puedes utilizar cualquier Registry compatible** (Docker Hub, Harbor privado, GitHub Packages, GitLab, etc.).

Para cambiar el origen de las imágenes por las tuyas, solo debes abrir el archivo `my-stack/values.yaml` y actualizar la propiedad `repository` de cada componente:

```yaml
# my-stack/values.yaml

frontend_tasks_app:
  # ... otras configuraciones
  image:
    repository: [tu-registry.com/tu-usuario/frontend-react-tasks](https://tu-registry.com/tu-usuario/frontend-react-tasks) # 👈 Cambia esto
    tag: latest

backend_tasks_app:
  # ... otras configuraciones
  image:
    repository: [tu-registry.com/tu-usuario/backend-django-tasks](https://tu-registry.com/tu-usuario/backend-django-tasks)  # 👈 Cambia esto
    tag: latest

tasks_db:
  # ... otras configuraciones
  image:
    repository: [tu-registry.com/oficial-images/postgres](https://tu-registry.com/oficial-images/postgres)          # 👈 O usa 'postgres' directo de Docker Hub
    tag: alpine

```
## 🔑 Uso de Registries Privados con Autenticación
Si tu Harbor o el registro que decidas usar requiere credenciales para descargar (pull) las imágenes, recuerda que debes hacer dos cosas:

1. Crear el secreto en tu clúster dentro del namespace tasks-app:

```
kubectl create secret docker-registry mi-registry-secret \
--docker-server=[https://tu-registry.com](https://tu-registry.com) \
--docker-username=tu_usuario \
--docker-password=tu_contraseña \
--namespace=tasks-app

```
2. Vincular el secreto en tus manifiestos de Kubernetes (asegúrate de que tus plantillas en templates/ lean el secretName o añade la propiedad imagePullSecrets según tengas estructurado tu despliegue).

## 🗺️ Diagrama de infraestructura de aplicación

En este diagrama se muestra como se usa la conexion entre las diferentes herramientas en la app, el uso de los difefentes componentes de kubernetes, para un mejor manejo del cluster y el uso del paradigma de GitOps para un mejor experiencia de DevOps 


![Diagrama de Arquitectura Gitops](./assets/arquitectura-gitops.png)

