# Abstracción en Programación Orientada a Objetos con Python

## 1. Conceptos Fundamentales

La abstracción es un pilar fundamental de la POO que nos permite:
- Simplificar sistemas complejos
- Reutilizar código eficientemente
- Crear diseños flexibles y escalables
- Mejorar la seguridad del código

## 2. Implementación en Python

### 2.1 Clases Abstractas

```python
from abc import ABC, abstractmethod

class Animal(ABC):
    def __init__(self, nombre: str):
        self.nombre = nombre
    
    @abstractmethod
    def hacer_sonido(self) -> str:
        """Método que debe ser implementado por todas las subclases."""
        pass
    
    @abstractmethod
    def moverse(self) -> str:
        """Define cómo se mueve el animal."""
        pass

class Perro(Animal):
    def hacer_sonido(self) -> str:
        return "¡Guau!"
    
    def moverse(self) -> str:
        return "Caminando en cuatro patas"

class Pajaro(Animal):
    def hacer_sonido(self) -> str:
        return "¡Pío!"
    
    def moverse(self) -> str:
        return "Volando"
```

#### Diagrama de Clases

```mermaid
classDiagram
    class Animal {
        <<abstract>>
        +nombre: str
        +hacer_sonido(): str
        +moverse(): str
    }
    class Perro {
        +hacer_sonido(): str
        +moverse(): str
    }
    class Pajaro {
        +hacer_sonido(): str
        +moverse(): str
    }
    Animal <|-- Perro
    Animal <|-- Pajaro
```

### 2.2 Ejemplo Práctico: Sistema de Pagos

```python
from abc import ABC, abstractmethod
from datetime import datetime
from typing import List, Dict

class MetodoPago(ABC):
    @abstractmethod
    def procesar_pago(self, monto: float) -> bool:
        pass
    
    @abstractmethod
    def verificar_fondos(self, monto: float) -> bool:
        pass

class TarjetaCredito(MetodoPago):
    def __init__(self, numero: str, fecha_exp: str):
        self.numero = numero
        self.fecha_exp = fecha_exp
    
    def procesar_pago(self, monto: float) -> bool:
        if self.verificar_fondos(monto):
            print(f"Procesando pago de ${monto} con tarjeta {self.numero}")
            return True
        return False
    
    def verificar_fondos(self, monto: float) -> bool:
        # Simulación de verificación de fondos
        return True

class PayPal(MetodoPago):
    def __init__(self, email: str):
        self.email = email
    
    def procesar_pago(self, monto: float) -> bool:
        if self.verificar_fondos(monto):
            print(f"Procesando pago de ${monto} con PayPal ({self.email})")
            return True
        return False
    
    def verificar_fondos(self, monto: float) -> bool:
        # Simulación de verificación de fondos
        return True

class Transaccion:
    def __init__(self, metodo_pago: MetodoPago):
        self.metodo_pago = metodo_pago
        self.fecha = datetime.now()
    
    def ejecutar_pago(self, monto: float) -> bool:
        return self.metodo_pago.procesar_pago(monto)
```
#### Diagrama de Clases
```mermaid
classDiagram
    class MetodoPago {
        <<abstract>>
        +procesar_pago(monto: float) bool
        +verificar_fondos(monto: float) bool
    }

    class TarjetaCredito {
        +numero: String
        +fecha_exp: String
        +procesar_pago(monto: float) bool
        +verificar_fondos(monto: float) bool
    }

    class PayPal {
        +email: String
        +procesar_pago(monto: float) bool
        +verificar_fondos(monto: float) bool
    }

    class Transaccion {
        +metodo_pago: MetodoPago
        +fecha: DateTime
        +ejecutar_pago(monto: float) bool
    }

    MetodoPago <|-- TarjetaCredito
    MetodoPago <|-- PayPal
    Transaccion --> MetodoPago
```
#### Diagrama de Secuencia
```mermaid
sequenceDiagram
    participant Cliente
    participant Transaccion
    participant MetodoPago
    participant TarjetaCredito
    participant PayPal

    Cliente->>Transaccion: Crear Transaccion(metodo_pago)
    Transaccion->>MetodoPago: ejecutar_pago(monto)
    MetodoPago->>TarjetaCredito: procesar_pago(monto)
    TarjetaCredito->>TarjetaCredito: verificar_fondos(monto)
    TarjetaCredito->>MetodoPago: return True
    MetodoPago->>Transaccion: return True
    Transaccion->>Cliente: return True

```




#### Diagrama de Clases

```mermaid
classDiagram
    class MetodoPago {
        <<abstract>>
        +procesar_pago(monto: float): bool
        +verificar_fondos(monto: float): bool
    }
    class TarjetaCredito {
        -numero: str
        -fecha_vencimiento: str
        +procesar_pago(monto: float): bool
        +verificar_fondos(monto: float): bool
    }
    class PayPal {
        -email: str
        +procesar_pago(monto: float): bool
        +verificar_fondos(monto: float): bool
    }
    class Transaccion {	
        -metodo_pago: MetodoPago
        -fecha: datetime
        +ejecutar_pago(monto: float): bool
    }

    MetodoPago <|-- TarjetaCredito
    MetodoPago <|-- PayPal
    Transaccion o-- MetodoPago
```

### 2.3 Pruebas Unitarias

```python
import unittest

class TestMetodosPago(unittest.TestCase):
    def setUp(self):
        self.tarjeta = TarjetaCredito("1234-5678-9012-3456", "12/25")
        self.paypal = PayPal("usuario@email.com")
    
    def test_pago_tarjeta(self):
        transaccion = Transaccion(self.tarjeta)
        self.assertTrue(transaccion.ejecutar_pago(100.0))
    
    def test_pago_paypal(self):
        transaccion = Transaccion(self.paypal)
        self.assertTrue(transaccion.ejecutar_pago(50.0))

if __name__ == '__main__':
    unittest.main()
```

### Ejercicio: Tests con estos valores
- Test 1: Pago con tarjeta de crédito de $100
- Test 2: Pago con PayPal de $50
- Test 3: Pago con tarjeta de crédito de $200
- Test 4: Hacer un listado de transacciones mostrando el método de pago el monto y la fecha

```python
from abc import ABC, abstractmethod
from datetime import datetime
from typing import List, Dict
import random

class MetodoPago(ABC):
    @abstractmethod
    def procesar_pago(self, monto: float) -> bool:
        pass
    
    @abstractmethod
    def verificar_fondos(self, monto: float) -> bool:
        pass

class TarjetaCredito(MetodoPago):
    def __init__(self, numero: str, fecha_vencimiento: str):
        self.numero = numero
        self.fecha_vencimiento = fecha_vencimiento
    
    def procesar_pago(self, monto: float) -> bool:
        if self.verificar_fondos(monto):
            print(f"Procesando pago de ${monto} con tarjeta {self.numero}")
            return True
        return False
    
    def verificar_fondos(self, monto: float) -> bool:
        # Simulación de verificación de fondos
        return True

class PayPal(MetodoPago):
    def __init__(self, email: str):
        self.email = email
    
    def procesar_pago(self, monto: float) -> bool:
        if self.verificar_fondos(monto):
            print(f"Procesando pago de ${monto} con PayPal ({self.email})")
            return True
        return False
    
    def verificar_fondos(self, monto: float) -> bool:
        # Simulación de verificación de fondos
        return True

class Transaccion:
    def __init__(self, metodo_pago, monto, fecha=None):
        self.metodo_pago = metodo_pago
        self.monto = monto
        self.fecha = fecha if fecha else self.generar_fecha_aleatoria()

    def ejecutar_pago(self):
        return self.metodo_pago.procesar_pago(self.monto)

    def mostrar_fecha(self):
        return self.fecha.strftime("%Y-%m-%d %H:%M:%S")

    def generar_fecha_aleatoria(self):
        start_date = datetime(2024, 1, 1)
        end_date = datetime.now()
        random_date = start_date + (end_date - start_date) * random.random()
        return random_date
```
```python
import unittest
from metodoPago import TarjetaCredito, PayPal, Transaccion

class TestMetodosPago(unittest.TestCase):
    def setUp(self):
        self.tarjeta1 = TarjetaCredito("1234-5678-9012-3456", "12/25")
        self.tarjeta2 = TarjetaCredito("9876-5432-1098-7654", "11/24")
        self.paypal = PayPal("usuario@email.com")
        self.transacciones = []

    def test_pago_tarjeta_100(self):
        transaccion = Transaccion(self.tarjeta1, 100.0)
        self.transacciones.append(transaccion)
        self.assertTrue(transaccion.ejecutar_pago())
    
    def test_pago_paypal_50(self):
        transaccion = Transaccion(self.paypal, 50.0)
        self.transacciones.append(transaccion)
        self.assertTrue(transaccion.ejecutar_pago())

    def test_pago_tarjeta_200(self):
        transaccion = Transaccion(self.tarjeta2, 200.0)
        self.transacciones.append(transaccion)
        self.assertTrue(transaccion.ejecutar_pago())

    def test_listado_transacciones(self):
        self.test_pago_tarjeta_100()
        self.test_pago_paypal_50()
        self.test_pago_tarjeta_200()
        
        print("\n")
        print("Listado de transacciones:")
        for transaccion in self.transacciones:
            metodo = transaccion.metodo_pago
            monto = transaccion.monto
            fecha = transaccion.mostrar_fecha()
            if isinstance(metodo, TarjetaCredito):
                metodo_str = "Tarjeta de crédito"
            else:
                metodo_str = "PayPal"
            print(f"{metodo_str}: ${monto} en {fecha}")

if __name__ == '__main__':
    unittest.main()
```


## 3. Ejercicio Práctico: Sistema de Biblioteca

```python
from abc import ABC, abstractmethod
from typing import List, Optional
from datetime import datetime, timedelta

class MaterialBiblioteca(ABC):
    def __init__(self, codigo: str, titulo: str):
        self.codigo = codigo
        self.titulo = titulo
        self.prestado = False
        self.fecha_devolucion: Optional[datetime] = None
    
    @abstractmethod
    def calcular_fecha_devolucion(self) -> datetime:
        pass
    
    def prestar(self) -> bool:
        if not self.prestado:
            self.prestado = True
            self.fecha_devolucion = self.calcular_fecha_devolucion()
            return True
        return False

class Libro(MaterialBiblioteca):
    def calcular_fecha_devolucion(self) -> datetime:
        return datetime.now() + timedelta(days=14)

class Revista(MaterialBiblioteca):
    def calcular_fecha_devolucion(self) -> datetime:
        return datetime.now() + timedelta(days=7)

class DVD(MaterialBiblioteca):
    def calcular_fecha_devolucion(self) -> datetime:
        return datetime.now() + timedelta(days=3)
```
## 3.1 Tarea
### Hacer diagrama de clases
### Hacer Diagrama de secuencia
### Hacer pruebas unitarias

- Diagrama de clases
```mermaid
classDiagram
    class MaterialBiblioteca {
        -String codigo
        -String titulo
        -boolean prestado
        -Optional~datetime~ fecha_devolucion
        +calcular_fecha_devolucion() datetime
        +prestar() boolean
    }
    class Libro {
        +calcular_fecha_devolucion() datetime
    }
    class Revista {
        +calcular_fecha_devolucion() datetime
    }
    class DVD {
        +calcular_fecha_devolucion() datetime
    }
    MaterialBiblioteca <|-- Libro
    MaterialBiblioteca <|-- Revista
    MaterialBiblioteca <|-- DVD
```
- Diagrama de secuencia
```mermaid
sequenceDiagram
    participant Cliente
    participant MaterialBiblioteca
    participant Libro
    participant Revista
    participant DVD

    Cliente->>MaterialBiblioteca: Prestar()
    MaterialBiblioteca->>Libro: calcular_fecha_devolucion()
    Libro-->>MaterialBiblioteca: devuelve fecha (14 días)
    MaterialBiblioteca-->>Cliente: asigna fecha_devolucion y préstamo exitoso
    
    Cliente->>MaterialBiblioteca: Prestar()
    MaterialBiblioteca->>Revista: calcular_fecha_devolucion()
    Revista-->>MaterialBiblioteca: devuelve fecha (7 días)
    MaterialBiblioteca-->>Cliente: asigna fecha_devolucion y préstamo exitoso
    
    Cliente->>MaterialBiblioteca: Prestar()
    MaterialBiblioteca->>DVD: calcular_fecha_devolucion()
    DVD-->>MaterialBiblioteca: devuelve fecha (3 días)
    MaterialBiblioteca-->>Cliente: asigna fecha_devolucion y préstamo exitoso


```




## 4. Mejores Prácticas

1. **Diseño**:
   - Usar clases abstractas para definir interfaces comunes
   - Mantener la abstracción en el nivel adecuado
   - Separar responsabilidades claramente

2. **Implementación**:
   - Utilizar type hints para mejor documentación
   - Implementar métodos abstractos en todas las subclases
   - Mantener la cohesión alta y el acoplamiento bajo

3. **Testing**:
   - Probar cada implementación concreta
   - Verificar el comportamiento polimórfico
   - Cubrir casos especiales y errores

## 5. Ejercicios Propuestos

1. Implementar un sistema de notificaciones con diferentes medios (email, SMS, push)
2. Crear un simulador de vehículos con distintos tipos de transporte
3. Diseñar un sistema de procesamiento de archivos para diferentes formatos

## Conclusión

La abstracción en Python permite crear sistemas flexibles y mantenibles. La clave está en identificar las características comunes y modelarlas de manera que permitan extensibilidad y reutilización del código.
