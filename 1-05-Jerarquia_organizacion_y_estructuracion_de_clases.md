# Jerarquía en Python: Herencia y Composición

## 1. Sistema de Gestión de Biblioteca

### 1.1 Implementación con Herencia

```python
from abc import ABC, abstractmethod
from datetime import datetime, timedelta
from typing import List, Optional, Dict
from dataclasses import dataclass

class RecursoBiblioteca(ABC):
    def __init__(self, codigo: str, titulo: str, ubicacion: str):
        self.codigo = codigo
        self.titulo = titulo
        self.ubicacion = ubicacion
        self.prestado = False
        self.fecha_devolucion: Optional[datetime] = None

    @abstractmethod
    def tiempo_prestamo(self) -> timedelta:
        """Retorna el tiempo máximo de préstamo para este recurso."""
        pass

    def prestar(self) -> bool:
        """Intenta prestar el recurso."""
        if not self.prestado:
            self.prestado = True
            self.fecha_devolucion = datetime.now() + self.tiempo_prestamo()
            return True
        return False

    def devolver(self) -> bool:
        """Registra la devolución del recurso."""
        if self.prestado:
            self.prestado = False
            self.fecha_devolucion = None
            return True
        return False

class Libro(RecursoBiblioteca):
    def __init__(self, codigo: str, titulo: str, autor: str, ubicacion: str):
        super().__init__(codigo, titulo, ubicacion)
        self.autor = autor

    def tiempo_prestamo(self) -> timedelta:
        return timedelta(days=14)

class Revista(RecursoBiblioteca):
    def __init__(self, codigo: str, titulo: str, numero: int, ubicacion: str):
        super().__init__(codigo, titulo, ubicacion)
        self.numero = numero

    def tiempo_prestamo(self) -> timedelta:
        return timedelta(days=7)

class RecursoDigital(RecursoBiblioteca):
    def __init__(self, codigo: str, titulo: str, formato: str, url: str):
        super().__init__(codigo, titulo, "digital")
        self.formato = formato
        self.url = url

    def tiempo_prestamo(self) -> timedelta:
        return timedelta(days=3)
```

### 1.2 Implementación con Composición

```python
@dataclass
class Usuario:
    id: str
    nombre: str
    email: str
    tipo: str  # estudiante, profesor, etc.

class HistorialPrestamo:
    def __init__(self):
        self.prestamos: List[Dict] = []

    def registrar_prestamo(
        self, 
        usuario: Usuario, 
        recurso: RecursoBiblioteca
    ) -> None:
        self.prestamos.append({
            'fecha': datetime.now(),
            'usuario': usuario,
            'recurso': recurso,
            'fecha_devolucion': recurso.fecha_devolucion
        })

class Biblioteca:
    def __init__(self):
        self.recursos: List[RecursoBiblioteca] = []
        self.usuarios: List[Usuario] = []
        self.historial = HistorialPrestamo()

    def agregar_recurso(self, recurso: RecursoBiblioteca) -> None:
        self.recursos.append(recurso)

    def agregar_usuario(self, usuario: Usuario) -> None:
        self.usuarios.append(usuario)

    def prestar_recurso(
        self, 
        codigo_recurso: str, 
        id_usuario: str
    ) -> bool:
        recurso = next(
            (r for r in self.recursos if r.codigo == codigo_recurso), 
            None
        )
        usuario = next(
            (u for u in self.usuarios if u.id == id_usuario), 
            None
        )

        if recurso and usuario and recurso.prestar():
            self.historial.registrar_prestamo(usuario, recurso)
            return True
        return False
```

## 2. Ejemplo: Sistema de Notificaciones

```python
from abc import ABC, abstractmethod
from typing import List, Protocol
from dataclasses import dataclass

class Destinatario(Protocol):
    def obtener_direccion(self) -> str:
        ...

@dataclass
class Cliente:
    email: str
    telefono: str
    
    def obtener_direccion(self) -> str:
        return self.email

class Notificacion(ABC):
    @abstractmethod
    def enviar(self, destinatario: Destinatario, mensaje: str) -> bool:
        pass

class NotificacionEmail(Notificacion):
    def enviar(self, destinatario: Destinatario, mensaje: str) -> bool:
        print(f"Enviando email a {destinatario.obtener_direccion()}: {mensaje}")
        return True

class NotificacionSMS(Notificacion):
    def enviar(self, destinatario: Cliente, mensaje: str) -> bool:
        print(f"Enviando SMS a {destinatario.telefono}: {mensaje}")
        return True

class ServicioNotificaciones:
    def __init__(self):
        self.notificadores: List[Notificacion] = []

    def agregar_notificador(self, notificador: Notificacion) -> None:
        self.notificadores.append(notificador)

    def notificar_todos(
        self, 
        destinatario: Destinatario, 
        mensaje: str
    ) -> None:
        for notificador in self.notificadores:
            notificador.enviar(destinatario, mensaje)
```
### Diagrama
```mermaid
classDiagram
    class Destinatario {
        <<Protocol>>
        +obtener_direccion() str
    }
    
    class Cliente {
        +email: str
        +telefono: str
        +obtener_direccion() str
    }
    
    class Notificacion {
        <<Abstract>>
        +enviar(destinatario: Destinatario, mensaje: str) bool
    }
    
    class NotificacionEmail {
        +enviar(destinatario: Destinatario, mensaje: str) bool
    }
    
    class NotificacionSMS {
        +enviar(destinatario: Cliente, mensaje: str) bool
    }
    
    class ServicioNotificaciones {
        -notificadores: List~Notificacion~
        +agregar_notificador(notificador: Notificacion) void
        +notificar_todos(destinatario: Destinatario, mensaje: str) void
    }
    
    Destinatario <|.. Cliente
    Notificacion <|-- NotificacionEmail
    Notificacion <|-- NotificacionSMS
    ServicioNotificaciones o--> Notificacion
    NotificacionEmail ..> Destinatario : uses
    NotificacionSMS ..> Cliente : uses
```
## 3. Pruebas Unitarias

```python
import unittest
from datetime import datetime, timedelta

class TestBiblioteca(unittest.TestCase):
    def setUp(self):
        self.biblioteca = Biblioteca()
        self.libro = Libro("L1", "Python Basics", "John Doe", "A1")
        self.usuario = Usuario("U1", "Ana Smith", "ana@email.com", "estudiante")
        
        self.biblioteca.agregar_recurso(self.libro)
        self.biblioteca.agregar_usuario(self.usuario)

    def test_prestamo_libro(self):
        # Prueba préstamo exitoso
        self.assertTrue(
            self.biblioteca.prestar_recurso("L1", "U1")
        )
        self.assertTrue(self.libro.prestado)

        # Prueba préstamo de libro ya prestado
        self.assertFalse(
            self.biblioteca.prestar_recurso("L1", "U1")
        )

    def test_devolucion_libro(self):
        self.biblioteca.prestar_recurso("L1", "U1")
        self.assertTrue(self.libro.devolver())
        self.assertFalse(self.libro.prestado)

class TestNotificaciones(unittest.TestCase):
    def setUp(self):
        self.servicio = ServicioNotificaciones()
        self.cliente = Cliente("user@email.com", "123456789")

    def test_notificacion_multiple(self):
        self.servicio.agregar_notificador(NotificacionEmail())
        self.servicio.agregar_notificador(NotificacionSMS())
        
        # Debería enviar tanto email como SMS
        self.servicio.notificar_todos(
            self.cliente, 
            "Prueba de notificación"
        )
```

## 4. Mejores Prácticas

1. **Herencia**:
   - Usar cuando existe una relación "es-un"
   - Mantener jerarquías poco profundas
   - Favorecer composición sobre herencia

2. **Composición**:
   - Usar cuando existe una relación "tiene-un"
   - Permite mayor flexibilidad
   - Facilita cambios en tiempo de ejecución

3. **Diseño**:
   - Seguir el principio SOLID
   - Documentar relaciones entre clases
   - Usar type hints para claridad

## 5. Ejercicio Propuesto

Implementar un sistema de gestión de empleados con:
1. Jerarquía de empleados (gerentes, desarrolladores, etc.)
2. Sistema de permisos basado en roles
3. Registro de actividades y reportes
4. Notificaciones personalizadas por rol

## Conclusión

La jerarquía en Python puede implementarse eficientemente usando tanto herencia como composición. La clave está en elegir el enfoque adecuado según las necesidades del sistema.
