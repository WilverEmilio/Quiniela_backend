<div align="center">

# 🏆 Quiniela Backend

### API REST para plataforma de quinielas deportivas

[![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)](https://www.postgresql.org)
[![SQLAlchemy](https://img.shields.io/badge/SQLAlchemy-D71F00?style=for-the-badge&logo=sqlalchemy&logoColor=white)](https://www.sqlalchemy.org)

---

</div>

## 📋 Tabla de Contenidos

- [Descripción](#-descripción)
- [Características](#-características)
- [Tecnologías](#-tecnologías)
- [Arquitectura del Sistema](#-arquitectura-del-sistema)
- [Modelo de Datos](#-modelo-de-datos)
- [Estructura del Proyecto](#-estructura-del-proyecto)
- [Instalación](#-instalación)
- [Configuración](#-configuración)
- [Comandos Disponibles](#-comandos-disponibles)
- [API Endpoints](#-api-endpoints)
- [Flujo de Datos](#-flujo-de-datos)
- [Roadmap](#-roadmap)
- [Autor](#-autor)

---

## 🎯 Descripción

**Quiniela Backend** es una API REST desarrollada con FastAPI que gestiona toda la lógica de negocio para una plataforma de quinielas deportivas. Actúa como intermediario entre el frontend (Astro) y servicios externos de datos deportivos, proporcionando endpoints para gestión de usuarios, quinielas, predicciones y cálculo de resultados.

Este backend está optimizado para alto rendimiento, con validación automática de datos, documentación interactiva y una arquitectura escalable.

---

## ✨ Características

- 🔐 **Sistema de Autenticación** - JWT para gestión segura de sesiones
- 🎮 **Gestión de Quinielas** - CRUD completo de quinielas por liga/temporada
- 🌐 **Integración API Externa** - Consumo eficiente de datos deportivos
- 📊 **Sistema de Predicciones** - Registro y validación de pronósticos
- 🏆 **Cálculo de Puntajes** - Algoritmo automático de puntuación
- 💾 **Base de Datos Optimizada** - PostgreSQL con SQLAlchemy ORM
- 📝 **Documentación Automática** - Swagger UI y ReDoc integrados
- ✅ **Validación de Datos** - Schemas Pydantic para tipo seguro
- 🔄 **Migraciones DB** - Control de versiones con Alembic
- ⚡ **Alto Rendimiento** - Servidor ASGI con Uvicorn

---

## 🚀 Tecnologías

| Tecnología | Versión | Descripción |
|------------|---------|-------------|
| **FastAPI** | Latest | Framework backend moderno y de alto rendimiento |
| **Python** | 3.10+ | Lenguaje de programación principal |
| **PostgreSQL** | 14+ | Base de datos relacional robusta |
| **SQLAlchemy** | 2.0+ | ORM para mapeo objeto-relacional |
| **SQLModel** | Latest | Integración SQLAlchemy + Pydantic |
| **Alembic** | Latest | Sistema de migraciones de base de datos |
| **Pydantic** | 2.0+ | Validación y serialización de datos |
| **JWT** | - | Tokens para autenticación segura |
| **Uvicorn** | Latest | Servidor ASGI de alto rendimiento |
| **API-Football** | v3 | Fuente externa de datos deportivos |

---

## 🏗️ Arquitectura del Sistema
```mermaid
graph TB
    subgraph "Cliente"
        A[Frontend Astro]
    end
    
    subgraph "API Layer"
        B[FastAPI]
        C[Routers]
        D[Middlewares]
    end
    
    subgraph "Business Logic"
        E[Services]
        F[Schemas Pydantic]
        G[Validators]
    end
    
    subgraph "Data Layer"
        H[SQLAlchemy ORM]
        I[Models]
        J[PostgreSQL]
    end
    
    subgraph "External Services"
        K[API-Football]
        L[Cache Redis]
    end
    
    A --> B
    B --> C
    C --> D
    D --> E
    E --> F
    F --> G
    G --> H
    H --> I
    I --> J
    E --> K
    E -.-> L
    
    style B fill:#009688
    style J fill:#316192
    style K fill:#FF6B6B
```

### Arquitectura en Capas
```mermaid
graph LR
    subgraph "Presentation Layer"
        A1[API Endpoints]
        A2[Request Validation]
        A3[Response Serialization]
    end
    
    subgraph "Business Layer"
        B1[Services]
        B2[Business Rules]
        B3[Scoring Algorithm]
    end
    
    subgraph "Data Layer"
        C1[Repository Pattern]
        C2[ORM Models]
        C3[Database]
    end
    
    A1 --> B1
    A2 --> B1
    B1 --> B2
    B2 --> B3
    B3 --> C1
    C1 --> C2
    C2 --> C3
    A3 --> A1
    
    style A1 fill:#009688
    style B2 fill:#4CAF50
    style C3 fill:#316192
```

---

## 🗄️ Modelo de Datos
```mermaid
erDiagram
    USER ||--o{ PREDICTION : creates
    USER ||--o{ QUINIELA_PARTICIPANT : "participates in"
    
    QUINIELA ||--|{ QUINIELA_PARTICIPANT : has
    QUINIELA ||--|{ MATCH : contains
    
    MATCH ||--o{ PREDICTION : "receives"
    MATCH ||--o| RESULT : has
    
    QUINIELA ||--|| QUINIELA_SEASON : "belongs to"
    
    USER {
        int id PK
        string username UK
        string email UK
        string hashed_password
        datetime created_at
        boolean is_active
    }
    
    QUINIELA {
        int id PK
        string name
        int league_id
        int season
        int round
        int creator_id FK
        datetime created_at
        boolean is_public
    }
    
    QUINIELA_SEASON {
        int id PK
        int quiniela_id FK
        int season_year
        string league_name
        string country
    }
    
    MATCH {
        int id PK
        int quiniela_id FK
        int fixture_id
        string home_team
        string away_team
        datetime match_date
        string status
    }
    
    PREDICTION {
        int id PK
        int user_id FK
        int match_id FK
        int home_score
        int away_score
        datetime created_at
        int points_earned
    }
    
    RESULT {
        int id PK
        int match_id FK
        int home_score
        int away_score
        string status
        datetime updated_at
    }
    
    QUINIELA_PARTICIPANT {
        int id PK
        int user_id FK
        int quiniela_id FK
        int total_points
        int rank
        datetime joined_at
    }
```

### Entidades Principales

| Entidad | Descripción | Relaciones |
|---------|-------------|------------|
| **User** | Usuarios registrados | 1:N con Predictions |
| **Quiniela** | Competencias creadas | 1:N con Matches |
| **QuinielaSeason** | Info de temporada/liga | 1:1 con Quiniela |
| **Match** | Partidos de una quiniela | 1:N con Predictions |
| **Prediction** | Pronósticos de usuarios | N:1 con User, Match |
| **Result** | Resultados oficiales | 1:1 con Match |
| **Standing** | Tabla de posiciones | N:1 con Quiniela |

---

## 📁 Estructura del Proyecto
```
/
├── 📂 app/
│   ├── 📄 main.py                    # Punto de entrada FastAPI
│   │
│   ├── 📂 core/                      # Configuración central
│   │   ├── config.py                 # Variables de entorno
│   │   ├── security.py               # JWT, hashing
│   │   └── dependencies.py           # Inyección de dependencias
│   │
│   ├── 📂 db/                        # Base de datos
│   │   ├── session.py                # Sesión SQLAlchemy
│   │   ├── base.py                   # Base declarativa
│   │   └── init_db.py                # Inicialización
│   │
│   ├── 📂 models/                    # Modelos ORM
│   │   ├── user.py
│   │   ├── quiniela.py
│   │   ├── match.py
│   │   ├── prediction.py
│   │   └── result.py
│   │
│   ├── 📂 schemas/                   # Schemas Pydantic
│   │   ├── user.py
│   │   ├── quiniela.py
│   │   ├── match.py
│   │   └── prediction.py
│   │
│   ├── 📂 routers/                   # Endpoints API
│   │   ├── auth.py                   # Autenticación
│   │   ├── users.py                  # Usuarios
│   │   ├── quinielas.py              # Quinielas
│   │   ├── matches.py                # Partidos
│   │   └── predictions.py            # Predicciones
│   │
│   ├── 📂 services/                  # Lógica de negocio
│   │   ├── sports_api.py             # Cliente API externa
│   │   ├── scoring.py                # Cálculo de puntos
│   │   ├── quiniela_service.py       # Lógica de quinielas
│   │   └── user_service.py           # Lógica de usuarios
│   │
│   ├── 📂 repositories/              # Capa de datos
│   │   ├── base.py
│   │   ├── user_repo.py
│   │   └── quiniela_repo.py
│   │
│   └── 📂 utils/                     # Utilidades
│       ├── validators.py
│       └── helpers.py
│
├── 📂 alembic/                       # Migraciones
│   ├── versions/
│   └── env.py
│
├── 📂 tests/                         # Tests unitarios
│   ├── test_auth.py
│   ├── test_quinielas.py
│   └── test_predictions.py
│
├── 📄 .env.example                   # Ejemplo variables entorno
├── 📄 requirements.txt               # Dependencias Python
├── 📄 alembic.ini                    # Config Alembic
├── 📄 .gitignore
└── 📄 README.md                      # Este archivo
```

---

## 🔧 Instalación

### Prerequisitos

- Python 3.10 o superior
- PostgreSQL 14+
- pip o poetry
- Cuenta en API-Football (para datos deportivos)

### Pasos de instalación
```bash
# Clonar el repositorio
git clone https://github.com/tu-usuario/quiniela-backend.git

# Navegar al directorio
cd quiniela-backend

# Crear entorno virtual
python -m venv venv

# Activar entorno virtual
# En Windows:
venv\Scripts\activate
# En Linux/Mac:
source venv/bin/activate

# Instalar dependencias
pip install -r requirements.txt

# Configurar variables de entorno
cp .env.example .env
# Editar .env con tus credenciales

# Crear base de datos
createdb quiniela_db

# Ejecutar migraciones
alembic upgrade head

# Iniciar servidor
uvicorn app.main:app --reload
```

La API estará disponible en `http://localhost:8000`

---

## ⚙️ Configuración

### Variables de Entorno

Crea un archivo `.env` en la raíz del proyecto:
```env
# Database
DATABASE_URL=postgresql://user:password@localhost:5432/quiniela_db

# API Externa
SPORTS_API_KEY=your_api_football_key
SPORTS_API_BASE_URL=https://v3.football.api-sports.io

# Security
SECRET_KEY=your-super-secret-key-change-this-in-production
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=60

# CORS
ALLOWED_ORIGINS=http://localhost:4321,http://localhost:3000

# App Settings
DEBUG=True
PROJECT_NAME=Quiniela API
VERSION=1.0.0
```

### Obtener API Key de API-Football

1. Regístrate en [API-Football](https://www.api-football.com/)
2. Ve a tu dashboard
3. Copia tu API Key
4. Agrégala a tu archivo `.env`

---

## 🎮 Comandos Disponibles

| Comando | Descripción |
|---------|-------------|
| `pip install -r requirements.txt` | Instala todas las dependencias |
| `uvicorn app.main:app --reload` | Inicia el servidor en modo desarrollo |
| `alembic revision --autogenerate -m "mensaje"` | Crea nueva migración |
| `alembic upgrade head` | Aplica migraciones pendientes |
| `alembic downgrade -1` | Revierte última migración |
| `pytest` | Ejecuta tests unitarios |
| `pytest --cov=app` | Tests con coverage |
| `black .` | Formatea código con Black |
| `flake8` | Verifica estilo de código |

---

## 🔌 API Endpoints

### 📚 Documentación Interactiva

Una vez iniciado el servidor, accede a:

- **Swagger UI**: `http://localhost:8000/docs`
- **ReDoc**: `http://localhost:8000/redoc`
- **OpenAPI JSON**: `http://localhost:8000/openapi.json`

### Endpoints Principales

#### 🔐 Autenticación
```http
POST   /api/v1/auth/register          # Registrar nuevo usuario
POST   /api/v1/auth/login             # Iniciar sesión (retorna JWT)
POST   /api/v1/auth/refresh           # Renovar token
GET    /api/v1/auth/me                # Obtener usuario actual
```

#### 👤 Usuarios
```http
GET    /api/v1/users                  # Listar usuarios
GET    /api/v1/users/{id}             # Obtener usuario por ID
PUT    /api/v1/users/{id}             # Actualizar usuario
DELETE /api/v1/users/{id}             # Eliminar usuario
```

#### 🏆 Quinielas
```http
GET    /api/v1/quinielas              # Listar quinielas
POST   /api/v1/quinielas              # Crear nueva quiniela
GET    /api/v1/quinielas/{id}         # Obtener quiniela específica
PUT    /api/v1/quinielas/{id}         # Actualizar quiniela
DELETE /api/v1/quinielas/{id}         # Eliminar quiniela
GET    /api/v1/quinielas/{id}/participants  # Participantes
POST   /api/v1/quinielas/{id}/join    # Unirse a quiniela
```

#### ⚽ Partidos
```http
GET    /api/v1/quinielas/{id}/matches        # Partidos de una quiniela
GET    /api/v1/matches/{id}                  # Detalle de un partido
GET    /api/v1/leagues                       # Ligas disponibles
GET    /api/v1/leagues/{id}/seasons          # Temporadas de liga
GET    /api/v1/leagues/{id}/rounds           # Jornadas disponibles
```

#### 🔮 Predicciones
```http
POST   /api/v1/predictions                   # Crear predicción
GET    /api/v1/predictions/{id}              # Obtener predicción
PUT    /api/v1/predictions/{id}              # Actualizar predicción
DELETE /api/v1/predictions/{id}              # Eliminar predicción
GET    /api/v1/users/{id}/predictions        # Predicciones de usuario
GET    /api/v1/matches/{id}/predictions      # Predicciones de partido
```

#### 📊 Resultados y Posiciones
```http
GET    /api/v1/quinielas/{id}/standings      # Tabla de posiciones
GET    /api/v1/quinielas/{id}/results        # Resultados de la quiniela
POST   /api/v1/admin/update-results          # Actualizar resultados (admin)
```

### Ejemplos de Uso

#### Crear una Quiniela
```bash
curl -X POST "http://localhost:8000/api/v1/quinielas" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Quiniela Premier League",
    "league_id": 39,
    "season": 2024,
    "round": 15,
    "is_public": true
  }'
```

#### Registrar una Predicción
```bash
curl -X POST "http://localhost:8000/api/v1/predictions" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "match_id": 123,
    "home_score": 2,
    "away_score": 1
  }'
```

---

## 🔄 Flujo de Datos
```mermaid
sequenceDiagram
    participant F as Frontend
    participant A as API (FastAPI)
    participant S as Service Layer
    participant D as Database
    participant E as API-Football

    F->>A: POST /quinielas
    A->>A: Valida JWT
    A->>S: QuinielaService.create()
    S->>E: GET /fixtures (liga, temporada)
    E-->>S: Lista de partidos
    S->>S: Filtra y normaliza datos
    S->>D: Guarda quiniela + partidos
    D-->>S: Confirmación
    S-->>A: QuinielaSchema
    A-->>F: 201 Created + datos

    Note over F,E: Usuario hace predicción
    
    F->>A: POST /predictions
    A->>A: Valida JWT + permisos
    A->>S: PredictionService.create()
    S->>D: Verifica match existe
    D-->>S: Match data
    S->>S: Valida reglas de negocio
    S->>D: Guarda predicción
    D-->>S: Confirmación
    S-->>A: PredictionSchema
    A-->>F: 201 Created

    Note over F,E: Actualización de resultados
    
    A->>S: ResultService.update()
    S->>E: GET /fixtures/results
    E-->>S: Resultados oficiales
    S->>D: Actualiza resultados
    S->>S: Calcula puntos
    S->>D: Actualiza standings
    D-->>S: Confirmación
```

---

## 🧠 Concepto de Negocio

### ¿Cómo funciona una Quiniela?

Una quiniela se define por:

- 🏆 **Liga** - Ej: Premier League, La Liga
- 📅 **Temporada** - Ej: 2024/2025
- 📊 **Jornada** - Ej: Jornada 15

### Proceso

1. **Creación**: Un usuario crea una quiniela seleccionando liga/temporada/jornada
2. **Consulta API**: El backend consulta los partidos desde API-Football
3. **Almacenamiento**: Se guardan SOLO los partidos de esa jornada específica
4. **Predicciones**: Los usuarios registran sus pronósticos
5. **Cálculo**: Cuando los partidos finalizan, se calculan puntos automáticamente

### Sistema de Puntuación
```python
def calculate_points(prediction, result):
    """
    Resultado exacto: 5 puntos
    Ganador correcto: 3 puntos
    Empate correcto: 3 puntos
    Incorrecto: 0 puntos
    """
    if prediction.home_score == result.home_score and \
       prediction.away_score == result.away_score:
        return 5  # Resultado exacto
    
    pred_winner = get_winner(prediction)
    real_winner = get_winner(result)
    
    if pred_winner == real_winner:
        return 3  # Ganador correcto
    
    return 0  # Incorrecto
```

---

## 🗺️ Roadmap

### ✅ Fase 1 - Fundamentos
- [x] Estructura base del proyecto
- [x] Modelos de datos
- [x] Configuración PostgreSQL
- [x] Integración API-Football
- [x] Endpoints básicos

### 🚧 Fase 2 - Autenticación
- [ ] Sistema de registro/login
- [ ] JWT tokens
- [ ] Refresh tokens
- [ ] Protección de endpoints
- [ ] Roles y permisos

### 🔮 Fase 3 - Lógica de Negocio
- [ ] CRUD completo de quinielas
- [ ] Sistema de predicciones
- [ ] Cálculo automático de puntos
- [ ] Tablas de posiciones
- [ ] Validaciones de negocio

### 🚀 Fase 4 - Optimización
- [ ] Cache con Redis
- [ ] Background tasks (Celery)
- [ ] Rate limiting
- [ ] Logging avanzado
- [ ] Monitoring (Prometheus)
- [ ] Tests unitarios (80%+ coverage)

### 💎 Fase 5 - Features Avanzadas
- [ ] WebSockets para actualizaciones real-time
- [ ] Sistema de notificaciones
- [ ] Estadísticas avanzadas
- [ ] Exportación de datos (CSV, PDF)
- [ ] API versioning (v2)

---

## 📊 Estado del Proyecto
```
🚧 En Desarrollo Activo
🏗️ Arquitectura Robusta
📈 Escalable y Mantenible
🔐 Seguridad Implementada
⚡ Alto Rendimiento
```

---

## 🧪 Testing
```bash
# Ejecutar todos los tests
pytest

# Con coverage
pytest --cov=app --cov-report=html

# Tests específicos
pytest tests/test_auth.py

# Modo verbose
pytest -v

# Ver print statements
pytest -s
```

---

## 🔗 Relación con el Frontend

Este backend está diseñado para trabajar junto con:

### 👉 [Quiniela Frontend – Astro](https://github.com/tu-usuario/quiniela-frontend)

Ambos proyectos forman parte del mismo ecosistema y comparten el modelo de negocio.
```mermaid
graph LR
    A[Frontend Astro] -->|HTTP Requests| B[Backend FastAPI]
    B -->|JSON Responses| A
    B -->|Consume| C[API-Football]
    B -->|Persiste| D[PostgreSQL]
    
    style A fill:#FF5D01
    style B fill:#009688
    style C fill:#FF6B6B
    style D fill:#316192
```

---

## 👨‍💻 Autor

<div align="center">

### Wilver Ixcot

**Backend Developer & Software Architect**

Proyecto personal enfocado en:
- Arquitectura de APIs REST modernas
- Desarrollo backend con FastAPI
- Diseño de bases de datos relacionales
- Integración con APIs externas
- Patrones de diseño y mejores prácticas

[![GitHub](https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white)](https://github.com/tu-usuario)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/tu-perfil)

</div>

---

## 📄 Licencia

Este proyecto está bajo la Licencia MIT - ver el archivo [LICENSE](LICENSE) para más detalles.

---

<div align="center">

**⭐ Si te gusta este proyecto, considera darle una estrella en GitHub ⭐**

Hecho con ❤️ y ⚽ usando FastAPI

</div>
