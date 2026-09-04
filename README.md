# Taller DevOps - Proyecto Grupal (Semana 2)

**UTEC - Licenciatura en Tecnologías de la Información (LTI)**  
**Semestre 8 | 2026 - Melo**  
**Grupo 4:** Digital Disruption  
**Docente:** Gabriel Pereira  

### Integrantes
  - Fabricio Quintana
  - Valentina Dinardi
  - Javier Salvatierra

---

### Requerimientos
Antes de comenzar, es necesario contar con Docker instalado en el sistema

## 1. Instalar Minikube

Para Linux (AMD64): 
```bash
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
```

> Para Windows, macOS u otras arquitecturas, revisá la [Guía Oficial de Instalación de Minikube](https://minikube.sigs.k8s.io/docs/start/).

Una vez instalado, levantamos el clúster local:

```bash
minikube start
```

---

## 2. Despliegue de la página

Para levantar el servicio de Nginx con la página en Kubernetes:

1. **Cargar el HTML como ConfigMap**:
   Inyectamos `index.html` en el clúster para que Nginx pueda servirlo:
   ```bash
   minikube kubectl -- create configmap nginx-html --from-file=index.html
   ```

2. **Aplicar el despliegue y el servicio**:
   Creamos los pods con Nginx y exponemos el servicio:
   ```bash
   minikube kubectl -- apply -f deployment.yml,service.yml
   ```

3. **Abrir la web en el navegador**:
   Obtenemos la URL asignada para ver la página:
   ```bash
   minikube service nginx-service
   ```

---

## 3. Pruebas

### Verificar los recursos creados
Para revisar que los pods, despliegues y servicios se hayan levantado correctamente:

```bash
minikube kubectl -- get pods
minikube kubectl -- get deployments
minikube kubectl -- get services
```

### Ver detalles de los pods (IP y nodo)
Para comprobar qué pods están activos, qué IP tienen y qué nodo tienen asignado:

```bash
minikube kubectl -- get pods -l app=grupo4-nginx -o wide
```

### Probar resiliencia (autocorrección)
Si eliminamos uno de los pods, el Deployment detecta que falta una réplica y levanta uno nuevo automáticamente:

```bash
minikube kubectl -- delete pod <Nombre de uno de los Pods>
```

Si volvemos a comprobar qué pods están activos, nos daremos cuenta de que el Deployment detectó que falta una réplica y levantó una nueva automáticamente (el nuevo pod tendrá un nombre distinto y un tiempo de creación más reciente).

### Comprobar el balanceo de carga
Para ver cómo el servicio reparte las solicitudes entre los pods:

1. En una terminal, seguimos los logs de todos los pods en simultáneo:
   ```bash
   minikube kubectl -- logs -l app=grupo4-nginx -f --prefix
   ```

2. En otra terminal, hacemos peticiones a la URL del servicio para ver cómo van respondiendo:
   ```bash
   curl <URL del servicio>
   ```

### Escalar réplicas en caliente
Para sumar o reducir réplicas del despliegue sin reiniciar nada (por ejemplo, subir a 4):

```bash
minikube kubectl -- scale deployment nginx-deployment --replicas=<Número de réplicas>
```
