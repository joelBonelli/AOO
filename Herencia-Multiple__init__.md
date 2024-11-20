# Herencia Múltiple y Clases Abstractas en Python

## Conceptos Base

### Clases Abstractas
- Utilizan `ABC` (Abstract Base Class) y `@abstractmethod`
- No pueden instanciarse directamente
- Definen interfaces y comportamientos que las clases hijas deben implementar
- Se importan desde el módulo `abc`

### Variables Privadas
- Se declaran con doble guion bajo: `__nombre`
- Solo accesibles dentro de la clase
- Requieren getters/setters o `@property` para acceso controlado

## Implementación

```python
from abc import ABC, abstractmethod

class Vehiculo(ABC):
    def __init__(self, marca):
        self.__marca = marca  # Variable privada
        
    @property
    def marca(self):
        return self.__marca
        
    @abstractmethod  # Método que debe implementarse
    def mover(self):
        pass

class Electrico(ABC):
    def __init__(self, voltaje):
        self.__voltaje = voltaje
        
    @property
    def voltaje(self):
        return self.__voltaje
        
    @abstractmethod
    def cargar(self):
        pass

class AutoElectrico(Vehiculo, Electrico):
    def __init__(self, marca, voltaje, modelo):
        # Inicialización de clases padre
        Vehiculo.__init__(self, marca)
        Electrico.__init__(self, voltaje)
        self.__modelo = modelo
        
    def mover(self):
        return f"{self.marca} en movimiento"
        
    def cargar(self):
        return f"Cargando a {self.voltaje}V"
```

## Puntos Clave

1. **Herencia Múltiple**
   - Una clase puede heredar de múltiples clases base
   - El orden de herencia afecta el MRO (Method Resolution Order)
   - Se deben inicializar todas las clases padre

2. **Métodos Abstractos**
   - Marcados con `@abstractmethod`
   - Deben implementarse en las clases hijas
   - No pueden tener implementación en la clase abstracta

3. **Encapsulamiento**
   - Variables privadas con `__`
   - Uso de `@property` para acceso controlado
   - Protege la integridad de los datos

## Ejemplo de Uso

```python
auto = AutoElectrico("Tesla", 220, "Model 3")
print(auto.marca)      # Accede a variable privada vía property
print(auto.mover())    # Usa método implementado
print(auto.cargar())   # Usa método implementado
```
# Sin constructor abstracto

```python
from abc import ABC, abstractmethod

class Vehiculo(ABC):
    @abstractmethod
    def mover(self):
        pass

class Electrico(ABC):
    @abstractmethod
    def cargar(self):
        pass

class AutoElectrico(Vehiculo, Electrico):
    def __init__(self, marca, voltaje, modelo):
        self.__marca = marca
        self.__voltaje = voltaje
        self.__modelo = modelo
        
    @property
    def marca(self):
        return self.__marca
        
    @property
    def voltaje(self):
        return self.__voltaje
        
    @property
    def modelo(self):
        return self.__modelo
        
    def mover(self):
        return f"{self.marca} {self.modelo} en movimiento"
        
    def cargar(self):
        return f"Cargando a {self.voltaje}V"

# Ejemplo de uso
auto = AutoElectrico("Tesla", 220, "Model 3")
print(auto.marca)      # Tesla
print(auto.mover())    # Tesla Model 3 en movimiento
print(auto.cargar())   # Cargando a 220V
```

**Puntos clave:**
- Clases abstractas solo definen la interfaz (métodos requeridos)
- Variables privadas y propiedades solo en clase concreta
- No es necesario constructor en clases abstractas si solo definen interfaz
- La clase hija debe implementar todos los métodos abstractos
