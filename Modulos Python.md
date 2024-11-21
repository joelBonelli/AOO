# Módulos en Python: Organización y Estructura de Proyectos

## 1. Estructura Básica de un Proyecto Python

### 1.1 Estructura de Carpetas Típica
```
mi_proyecto/
├── README.md
├── setup.py
├── requirements.txt
├── .gitignore
├── .env
├── tests/
│   ├── __init__.py
│   ├── test_modulo1.py
│   └── test_modulo2.py
├── docs/
│   ├── conf.py
│   └── index.rst
└── mi_proyecto/
    ├── __init__.py
    ├── modulo1.py
    ├── modulo2.py
    └── subpaquete/
        ├── __init__.py
        └── modulo3.py
```

### 1.2 Funciones de cada Archivo y Carpeta
- `README.md`: Documentación principal del proyecto
- `setup.py`: Configuración para distribución del paquete
- `requirements.txt`: Dependencias del proyecto
- `.gitignore`: Archivos a ignorar en el control de versiones
- `.env`: Variables de entorno (no se versiona)
- `tests/`: Carpeta con pruebas unitarias
- `docs/`: Documentación detallada
- `mi_proyecto/`: Código fuente principal

## 2. Módulos en Python

### 2.1 ¿Qué es un Módulo?
Un módulo es un archivo Python (`.py`) que contiene código reutilizable. Puede contener funciones, clases y variables.

```python
# modulo1.py
def saludar(nombre):
    return f"Hola, {nombre}!"

class Persona:
    def __init__(self, nombre):
        self.nombre = nombre
```

### 2.2 Importación de Módulos
```python
# Formas de importar
import modulo1
from modulo1 import saludar
from modulo1 import *  # No recomendado
from modulo1 import saludar as saludo
```

### 2.3 Archivo __init__.py
El archivo `__init__.py` convierte una carpeta en un paquete Python. Puede estar vacío o contener código de inicialización.

```python
# mi_proyecto/__init__.py
from .modulo1 import saludar
from .modulo2 import despedir

__version__ = "1.0.0"
__all__ = ['saludar', 'despedir']
```

## 3. Organización de Módulos

### 3.1 Paquetes
Un paquete es una carpeta que contiene módulos y un archivo `__init__.py`.

```python
# mi_proyecto/subpaquete/__init__.py
from .modulo3 import funcionalidad_especial

# Controla qué se expone al importar con *
__all__ = ['funcionalidad_especial']
```

### 3.2 Importaciones Relativas
```python
# Importaciones relativas
from . import modulo1            # Mismo directorio
from .. import otro_modulo      # Directorio padre
from ..subpaquete import modulo # Otro subpaquete
```
