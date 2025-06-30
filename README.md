# Rasa Assistant - Kubernetes Deployment

Este proyecto implementa un asistente conversacional en español usando Rasa Open Source, desplegado en Google Kubernetes Engine (GKE) usando el **Helm Chart oficial de Rasa**.

## 🚀 Características

- ✅ **Asistente en Español**: Responde en español
- ✅ **Funcionalidad de Fecha**: Proporciona la fecha actual formateada
- ✅ **Despliegue Automatizado**: Usando Helm Chart oficial de Rasa
- ✅ **Escalabilidad**: Desplegado en Kubernetes
- ✅ **API REST**: Disponible para integración con aplicaciones web

## 📋 Prerrequisitos

- [Google Cloud SDK](https://cloud.google.com/sdk/docs/install)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Helm](https://helm.sh/docs/intro/install/) (versión >= 3.5)
- [Docker](https://docs.docker.com/get-docker/)
- [Python 3.8+](https://www.python.org/downloads/)

## 🏗️ Arquitectura

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Web Client    │───▶│  Rasa Server     │───▶│ Action Server   │
│                 │    │  (Helm Chart)    │    │  (Custom)       │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                              │
                              ▼
                       ┌──────────────────┐
                       │   Kubernetes     │
                       │   (GKE)          │
                       └──────────────────┘
```

## 📁 Estructura del Proyecto

```
rasa-1/
├── actions/                 # Acciones personalizadas
│   ├── __init__.py
│   └── actions.py          # Acción para obtener fecha
├── data/                   # Datos de entrenamiento
│   ├── nlu.yml            # Intenciones y entidades
│   ├── stories.yml        # Flujos de conversación
│   └── rules.yml          # Reglas de comportamiento
├── models/                 # Modelos entrenados (generados)
├── rasa-values.yml        # Configuración del Helm Chart
├── deploy-helm.sh         # Script de despliegue
├── Dockerfile.actions     # Dockerfile para action server
├── requirements-actions.txt # Dependencias del action server
├── domain.yml             # Configuración del dominio
├── config.yml             # Configuración del pipeline
└── credentials.yml        # Configuración de canales
```

## 🚀 Despliegue Rápido

### 1. Configurar Google Cloud

```bash
# Autenticarse con Google Cloud
gcloud auth login

# Configurar proyecto
gcloud config set project pruebas-tecnologia

# Obtener credenciales del cluster
gcloud container clusters get-credentials rasa-cluster --zone=us-central1-a
```

### 2. Desplegar con Helm

```bash
# Ejecutar el script de despliegue
./deploy-helm.sh
```

El script automáticamente:
- ✅ Entrena el modelo durante el despliegue
- ✅ Construye y sube la imagen del action server
- ✅ Despliega Rasa usando el Helm Chart oficial
- ✅ Configura servicios y ingress

## 🔧 Configuración

### Helm Chart Configuration (`rasa-values.yml`)

```yaml
applicationSettings:
  trainInitialModel: true  # Entrenar modelo automáticamente

service:
  type: LoadBalancer       # Para acceso externo

actionServer:
  enabled: true            # Habilitar action server personalizado
  image:
    repository: us-central1-docker.pkg.dev/pruebas-tecnologia/rasa-repo/rasa-actions
    tag: latest
```

### Personalización

Para modificar la configuración:

1. **Editar `rasa-values.yml`** para cambiar recursos, servicios, etc.
2. **Modificar `data/`** para cambiar el comportamiento del bot
3. **Actualizar `actions/actions.py`** para nuevas funcionalidades

## 🧪 Testing

### Probar el Asistente

```bash
# Port forwarding para acceso local
kubectl port-forward -n rasa svc/rasa-assistant 5005:5005

# Probar con curl
curl -X POST http://localhost:5005/webhooks/rest/webhook \
  -H "Content-Type: application/json" \
  -d '{"sender": "test", "message": "hola"}'

# Probar funcionalidad de fecha
curl -X POST http://localhost:5005/webhooks/rest/webhook \
  -H "Content-Type: application/json" \
  -d '{"sender": "test", "message": "¿qué fecha es hoy?"}'
```

### Verificar Estado

```bash
# Ver pods
kubectl get pods -n rasa

# Ver servicios
kubectl get svc -n rasa

# Ver logs
kubectl logs -n rasa -l app.kubernetes.io/instance=rasa-assistant
```

## 🔄 Actualizaciones

### Actualizar el Asistente

```bash
# Modificar datos de entrenamiento en data/
# Modificar acciones en actions/
# Re-ejecutar despliegue
./deploy-helm.sh
```

### Actualizar Configuración

```bash
# Modificar rasa-values.yml
# Actualizar despliegue
helm upgrade rasa-assistant --namespace rasa --values rasa-values.yml rasa/rasa
```

## 🗑️ Limpieza

```bash
# Eliminar despliegue
helm uninstall rasa-assistant -n rasa

# Eliminar namespace
kubectl delete namespace rasa
```

## 📚 Documentación

- [Rasa Open Source Documentation](https://rasa.com/docs/rasa/)
- [Rasa Helm Chart](https://github.com/RasaHQ/helm-charts)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Google Kubernetes Engine](https://cloud.google.com/kubernetes-engine)

## 🤝 Contribución

1. Fork el proyecto
2. Crear una rama para tu feature (`git checkout -b feature/AmazingFeature`)
3. Commit tus cambios (`git commit -m 'Add some AmazingFeature'`)
4. Push a la rama (`git push origin feature/AmazingFeature`)
5. Abrir un Pull Request

## 📄 Licencia

Este proyecto está bajo la Licencia MIT. Ver el archivo `LICENSE` para más detalles.

## 🆘 Soporte

Si encuentras problemas:

1. Verificar logs: `kubectl logs -n rasa -l app.kubernetes.io/instance=rasa-assistant`
2. Verificar estado de pods: `kubectl get pods -n rasa`
3. Verificar configuración: `helm get values rasa-assistant -n rasa`

---

**Nota**: Este proyecto usa el [Helm Chart oficial de Rasa](https://legacy-docs-oss.rasa.com/docs/rasa/deploy/deploy-rasa) para un despliegue más robusto y mantenible. # rasa-demo-deploy
