# Jerarquía en Python: Guía Completa de Herencia y Composición

## 1. Introducción a la Jerarquía en Python
La jerarquía en programación orientada a objetos es un concepto fundamental que nos permite organizar y estructurar nuestro código de manera eficiente. En Python, esto se implementa principalmente a través de dos mecanismos:

- **Herencia**: Relación "es-un" (is-a)
- **Composición**: Relación "tiene-un" (has-a)

### 1.1 ¿Por qué es importante?
- Promueve la reutilización de código
- Facilita el mantenimiento
- Mejora la organización del código
- Permite extensibilidad

## 2. Herencia en Python

### 2.1 Conceptos Fundamentales
```python
from abc import ABC, abstractmethod

class Animal(ABC):
    def __init__(self, nombre: str):
        self.nombre = nombre

    @abstractmethod
    def hacer_sonido(self) -> str:
        pass

class Perro(Animal):
    def hacer_sonido(self) -> str:
        return "¡Guau!"

class Gato(Animal):
    def hacer_sonido(self) -> str:
        return "¡Miau!"
```

### 2.2 Tipos de Herencia

#### Herencia Simple
```python
class Vehiculo:
    def __init__(self, marca: str):
        self.marca = marca

class Coche(Vehiculo):
    def __init__(self, marca: str, modelo: str):
        super().__init__(marca)
        self.modelo = modelo
```

#### Herencia Múltiple
```python
class DispositivoElectronico:
    def encender(self):
        print("Encendiendo...")

class Portatil:
    def transportar(self):
        print("Transportando...")

class Laptop(DispositivoElectronico, Portatil):
    pass
```

### 2.3 Métodos Especiales
```python
class Producto:
    def __init__(self, nombre: str, precio: float):
        self.nombre = nombre
        self.precio = precio

    def __str__(self) -> str:
        return f"{self.nombre} (${self.precio})"

    def __repr__(self) -> str:
        return f"Producto(nombre='{self.nombre}', precio={self.precio})"
```

## 3. Composición en Python

### 3.1 Principios Básicos
```python
class Motor:
    def arrancar(self):
        return "Motor arrancado"

class Coche:
    def __init__(self):
        self.motor = Motor()  # Composición
```

### 3.2 Patrones Comunes de Composición

#### Composición con Colecciones
```python
from typing import List

class Biblioteca:
    def __init__(self):
        self.libros: List[Libro] = []

    def agregar_libro(self, libro: Libro):
        self.libros.append(libro)
```

#### Composición con Dependencias
```python
class ServicioNotificacion:
    def enviar(self, mensaje: str):
        print(f"Enviando: {mensaje}")

class Sistema:
    def __init__(self, notificador: ServicioNotificacion):
        self.notificador = notificador
```

## 4. Caso Práctico: Sistema de Gestión de Biblioteca

### 4.1 Implementación con Herencia
```python
from abc import ABC, abstractmethod
from datetime import datetime, timedelta
from typing import Optional

class RecursoBiblioteca(ABC):
    def __init__(self, codigo: str, titulo: str):
        self.codigo = codigo
        self.titulo = titulo
        self.prestado = False

    @abstractmethod
    def tiempo_prestamo(self) -> timedelta:
        pass

    def prestar(self) -> bool:
        if not self.prestado:
            self.prestado = True
            return True
        return False

class Libro(RecursoBiblioteca):
    def __init__(self, codigo: str, titulo: str, autor: str):
        super().__init__(codigo, titulo)
        self.autor = autor

    def tiempo_prestamo(self) -> timedelta:
        return timedelta(days=14)
```

### 4.2 Implementación con Composición
```python
from dataclasses import dataclass
from typing import List

@dataclass
class Usuario:
    id: str
    nombre: str
    email: str

class HistorialPrestamo:
    def __init__(self):
        self.prestamos: List[dict] = []

    def registrar_prestamo(self, usuario: Usuario, recurso: RecursoBiblioteca):
        self.prestamos.append({
            'fecha': datetime.now(),
            'usuario': usuario,
            'recurso': recurso
        })

class Biblioteca:
    def __init__(self):
        self.recursos: List[RecursoBiblioteca] = []
        self.usuarios: List[Usuario] = []
        self.historial = HistorialPrestamo()  # Composición
```

## 5. Mejores Prácticas

### 5.1 Cuándo Usar Herencia
- Cuando existe una verdadera relación "es-un"
- Para compartir comportamiento común
- Cuando la clase base define una interfaz clara

### 5.2 Cuándo Usar Composición
- Cuando existe una relación "tiene-un"
- Para combinar comportamientos
- Cuando se necesita mayor flexibilidad

### 5.3 Principios de Diseño
1. **Favor composición sobre herencia**
   - Más flexible
   - Menor acoplamiento
   - Más fácil de modificar

2. **SOLID**
   - Single Responsibility Principle
   - Open/Closed Principle
   - Liskov Substitution Principle
   - Interface Segregation Principle
   - Dependency Inversion Principle


