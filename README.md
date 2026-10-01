# Cloud StockFlow K8s

Aplicación demo de banca por internet con frontend estático, backend en Flask y despliegue en Kubernetes.

## Descripción

El proyecto simula un portal bancario con:

- consulta de saldo y movimientos recientes
- transferencias entre cuentas
- modo local con SQLite
- modo Kubernetes con PostgreSQL
- interfaz web ligera servida con Nginx

## Arquitectura

- **Backend**: Flask en `backend/app.py`
- **Frontend**: HTML estático en `frontend/index.html`
- **Base de datos local**: SQLite (`backend/banco_local.db`)
- **Base de datos en K8s**: PostgreSQL
- **Orquestación**: manifiestos en `k8s/`

## Requisitos

- Python 3.10+
- Docker
- Kubernetes y `kubectl` para el despliegue en clúster

## Ejecución local

### 1. Backend

```bash
cd backend
pip install -r requirements.txt
python app.py
```

El backend queda disponible en `http://localhost:3000`.

### 2. Frontend

Abre `frontend/index.html` en el navegador o sírvelo con Nginx/Docker.

## Docker

### Backend

```bash
cd backend
docker build -t mi-banco-backend:v1 .
```

### Frontend

```bash
cd frontend
docker build -t mi-banco-frontend:v1 .
```

## Kubernetes

Los manifiestos están en `k8s/`:

- `1-postgres.yaml`
- `2-backend.yaml`
- `3-frontend.yaml`

Aplicación:

```bash
kubectl apply -f k8s/1-postgres.yaml
kubectl apply -f k8s/2-backend.yaml
kubectl apply -f k8s/3-frontend.yaml
```

### Acceso

- **Frontend**: NodePort `30080`
- **Backend**: Service interno en `3000`
- **PostgreSQL**: Service interno en `5432`

## API

### `GET /api/cuenta`

Devuelve:

- saldo actual
- últimos movimientos
- nombre del pod que respondió
- motor de base de datos en uso

### `POST /api/transfer`

Body:

```json
{
  "destino": "123456789",
  "monto": 100
}
```

### `GET /api/stress`

Ejecuta una carga de CPU para pruebas de escalado.

## Comportamiento por entorno

- Si no existe `DB_HOST`, el backend usa **SQLite local**
- Si existe `DB_HOST`, el backend usa **PostgreSQL**

## Notas

- El frontend llama al backend en `http://localhost:3000`
- En Kubernetes, las imágenes usan `imagePullPolicy: Never`, así que deben construirse localmente con los tags definidos en los manifiestos
