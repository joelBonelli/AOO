# Encapsulamiento en Python: Guía Práctica

## 1. Fundamentos del Encapsulamiento

El encapsulamiento en Python se implementa mediante convenciones y decoradores:
- Atributos privados: prefijo `__`
- Atributos protegidos: prefijo `_`
- Properties para control de acceso
- Métodos de acceso controlado

## 2. Implementación Práctica

### 2.1 Sistema Bancario

```python
from typing import Optional, List
from datetime import datetime
from decimal import Decimal

class CuentaBancaria:
    def __init__(self, numero: str, titular: str, saldo_inicial: Decimal = Decimal('0.0')):
        self.__numero = numero
        self.__titular = titular
        self.__saldo = saldo_inicial
        self.__transacciones: List[dict] = []
    
    @property
    def saldo(self) -> Decimal:
        """Obtiene el saldo actual de la cuenta."""
        return self.__saldo
    
    @property
    def titular(self) -> str:
        """Obtiene el nombre del titular."""
        return self.__titular
    
    def depositar(self, monto: Decimal) -> bool:
        """Realiza un depósito en la cuenta."""
        if monto <= Decimal('0'):
            raise ValueError("El monto debe ser positivo")
        
        self.__saldo += monto
        self.__registrar_transaccion("depósito", monto)
        return True
    
    def retirar(self, monto: Decimal) -> bool:
        """Realiza un retiro de la cuenta."""
        if monto <= Decimal('0'):
            raise ValueError("El monto debe ser positivo")
        
        if monto > self.__saldo:
            raise ValueError("Saldo insuficiente")
        
        self.__saldo -= monto
        self.__registrar_transaccion("retiro", monto)
        return True
    
    def __registrar_transaccion(self, tipo: str, monto: Decimal) -> None:
        """Método privado para registrar transacciones."""
        self.__transacciones.append({
            'fecha': datetime.now(),
            'tipo': tipo,
            'monto': monto,
            'saldo_resultante': self.__saldo
        })
    
    def obtener_historial(self) -> List[dict]:
        """Obtiene una copia del historial de transacciones."""
        return self.__transacciones.copy()

```
### Sistema de biblioteca
```python
class Libro:
    def __init__(self, titulo: str, autor: str, anio_publicacion: int):
        self.__titulo = titulo
        self.__autor = autor
        self.__anio_publicacion = anio_publicacion

    @property
    def titulo(self) -> str:
        return self.__titulo

    @property
    def autor(self) -> str:
        return self.__autor

    @property
    def anio_publicacion(self) -> int:
        return self.__anio_publicacion

    def __str__(self) -> str:
        return f"'{self.__titulo}' por {self.__autor}, publicado en {self.__anio_publicacion}"



class GestionDeLibros:
    def __init__(self):
        # Inicializamos la lista de libros y el historial de cambios
        self.__libros: List[Libro] = []
        self.__historial: List[dict] = []

    @property
    def libros(self) -> List[Libro]:
        """Obtiene la lista actual de libros."""
        return self.__libros

    def agregar_libro(self, titulo: str, autor: str, anio_publicacion: int) -> None:
        """Agrega un nuevo libro a la colección."""
        # Verificar si el libro ya existe (por título)
        if any(libro.titulo == titulo for libro in self.__libros):
            raise ValueError(f"El libro '{titulo}' ya está en la colección.")

        libro = Libro(titulo, autor, anio_publicacion)
        self.__libros.append(libro)
        self.__registrar_historial("agregar", titulo)

    def eliminar_libro(self, titulo: str) -> None:
        """Elimina un libro por su título."""
        libro_encontrado = False
        for libro in self.__libros:
            if libro.titulo == titulo:
                self.__libros.remove(libro)
                self.__registrar_historial("eliminar", titulo)
                libro_encontrado = True
                break
        
        if not libro_encontrado:
            raise ValueError(f"El libro '{titulo}' no fue encontrado.")

    def actualizar_libro(self, titulo: str, nuevo_titulo: str = None, nuevo_autor: str = None, nuevo_anio: int = None) -> None:
        """Actualiza un libro existente por su título."""
        libro_encontrado = False
        for libro in self.__libros:
            if libro.titulo == titulo:
                if nuevo_titulo:
                    libro._Libro__titulo = nuevo_titulo
                if nuevo_autor:
                    libro._Libro__autor = nuevo_autor
                if nuevo_anio:
                    libro._Libro__anio_publicacion = nuevo_anio
                self.__registrar_historial("actualizar", titulo)
                libro_encontrado = True
                break
        
        if not libro_encontrado:
            raise ValueError(f"El libro '{titulo}' no fue encontrado.")

    def __registrar_historial(self, accion: str, titulo: str) -> None:
        """Método privado para registrar las operaciones realizadas."""
        self.__historial.append({
            'fecha': datetime.now(),
            'accion': accion,
            'titulo': titulo
        })

    def obtener_historial(self) -> List[dict]:
        """Obtiene una copia del historial de acciones realizadas con los libros."""
        return self.__historial.copy()
```


### 2.2 Sistema de Gestión de Empleados

```python
from dataclasses import dataclass
from datetime import date
from decimal import Decimal
from typing import Optional

@dataclass
class DatosPersonales:
    """Clase para encapsular datos personales sensibles."""
    __slots__ = ['__nombre', '__fecha_nacimiento', '__dni']
    
    def __init__(self, nombre: str, fecha_nacimiento: date, dni: str):
        self.__nombre = nombre
        self.__fecha_nacimiento = fecha_nacimiento
        self.__dni = dni
    
    @property
    def nombre(self) -> str:
        return self.__nombre
    
    @property
    def edad(self) -> int:
        hoy = date.today()
        return hoy.year - self.__fecha_nacimiento.year - (
            (hoy.month, hoy.day) < 
            (self.__fecha_nacimiento.month, self.__fecha_nacimiento.day)
        )

class Empleado:
    def __init__(self, datos: DatosPersonales, salario: Decimal):
        self.__datos = datos
        self.__salario = salario
        self.__evaluaciones: List[dict] = []
    
    @property
    def nombre(self) -> str:
        return self.__datos.nombre
    
    @property
    def salario(self) -> Decimal:
        return self.__salario
    
    def ajustar_salario(self, nuevo_salario: Decimal, motivo: str) -> None:
        """Ajusta el salario del empleado con justificación."""
        if nuevo_salario <= Decimal('0'):
            raise ValueError("El salario debe ser positivo")
        
        self.__registrar_cambio_salario(self.__salario, nuevo_salario, motivo)
        self.__salario = nuevo_salario
    
    def __registrar_cambio_salario(
        self, 
        anterior: Decimal, 
        nuevo: Decimal, 
        motivo: str
    ) -> None:
        """Registro privado de cambios salariales."""
        self.__evaluaciones.append({
            'fecha': date.today(),
            'salario_anterior': anterior,
            'salario_nuevo': nuevo,
            'motivo': motivo
        })
```

## 3. Pruebas Unitarias

```python
import unittest
from decimal import Decimal
from datetime import date

class TestCuentaBancaria(unittest.TestCase):
    def setUp(self):
        self.cuenta = CuentaBancaria("123", "Juan Pérez", Decimal('1000.0'))
    
    def test_deposito_valido(self):
        self.assertTrue(self.cuenta.depositar(Decimal('500.0')))
        self.assertEqual(self.cuenta.saldo, Decimal('1500.0'))
    
    def test_retiro_valido(self):
        self.assertTrue(self.cuenta.retirar(Decimal('500.0')))
        self.assertEqual(self.cuenta.saldo, Decimal('500.0'))
    
    def test_retiro_invalido(self):
        with self.assertRaises(ValueError):
            self.cuenta.retirar(Decimal('1500.0'))

class TestEmpleado(unittest.TestCase):
    def setUp(self):
        datos = DatosPersonales("Ana López", date(1990, 5, 15), "12345678")
        self.empleado = Empleado(datos, Decimal('50000.0'))
    
    def test_ajuste_salario(self):
        self.empleado.ajustar_salario(Decimal('55000.0'), "Promoción")
        self.assertEqual(self.empleado.salario, Decimal('55000.0'))

```

## 4. Mejores Prácticas

1. **Diseño**:
   - Usar properties en lugar de getters/setters tradicionales
   - Minimizar la exposición de atributos
   - Separar datos sensibles en clases específicas

2. **Implementación**:
   - Validar datos en setters y constructores
   - Usar type hints para mejor documentación
   - Emplear dataclasses cuando sea apropiado

3. **Seguridad**:
   - Proteger datos sensibles con atributos privados
   - Controlar acceso mediante métodos específicos
   - Validar todos los inputs

## 5. Ejercicios Propuestos

1. **Sistema de Expedientes Médicos**:
   - Implementar una clase para gestionar historiales médicos
   - Asegurar privacidad de datos sensibles
   - Permitir acceso controlado según roles

2. **Gestor de Contraseñas**:
   - Crear sistema para almacenar credenciales
   - Implementar encriptación de datos
   - Gestionar acceso mediante autenticación

## Conclusión

El encapsulamiento en Python, aunque menos estricto que en otros lenguajes, es fundamental para crear código seguro y mantenible. La clave está en usar las convenciones y herramientas disponibles de manera efectiva.
