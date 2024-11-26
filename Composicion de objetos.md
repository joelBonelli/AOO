# Composición en Python

## ¿Qué es la Composición?

La composición es un principio de diseño en programación orientada a objetos donde un objeto contiene otros objetos como sus atributos. En lugar de heredar comportamientos (como en la herencia), la composición permite construir objetos complejos combinando objetos más simples. Esto sigue el principio "tiene un" en lugar de "es un".

## Ventajas de la Composición

1. **Mayor flexibilidad**: Facilita cambiar implementaciones en tiempo de ejecución
2. **Menor acoplamiento**: Los objetos son más independientes entre sí
3. **Mayor reusabilidad**: Los componentes pueden reutilizarse en diferentes contextos
4. **Mejor encapsulamiento**: Los detalles de implementación permanecen ocultos

## Ejemplos Prácticos

### 1. Ejemplo Básico: Composición de una Computadora

```python
class CPU:
    def __init__(self, modelo):
        self.modelo = modelo
    
    def procesar(self):
        return f"CPU {self.modelo} procesando datos..."

class MemoriaRAM:
    def __init__(self, capacidad):
        self.capacidad = capacidad
    
    def cargar_datos(self):
        return f"RAM de {self.capacidad}GB cargando datos..."

class DiscoDuro:
    def __init__(self, capacidad, tipo):
        self.capacidad = capacidad
        self.tipo = tipo
    
    def almacenar(self):
        return f"Disco {self.tipo} de {self.capacidad}TB almacenando información..."

class Computadora:
    def __init__(self, modelo_cpu, ram_gb, disco_capacidad, disco_tipo):
        self.cpu = CPU(modelo_cpu)
        self.ram = MemoriaRAM(ram_gb)
        self.disco = DiscoDuro(disco_capacidad, disco_tipo)
    
    def encender(self):
        return [
            "Iniciando computadora...",
            self.cpu.procesar(),
            self.ram.cargar_datos(),
            self.disco.almacenar()
        ]

# Uso del ejemplo
mi_pc = Computadora("Intel i7", 16, 1, "SSD")
for accion in mi_pc.encender():
    print(accion)
```

### 2. Ejemplo Avanzado: Sistema de Biblioteca

```python
class Autor:
    def __init__(self, nombre, nacionalidad):
        self.nombre = nombre
        self.nacionalidad = nacionalidad

class Libro:
    def __init__(self, titulo, autor, isbn):
        self.titulo = titulo
        self.autor = autor  # Composición: Libro tiene un Autor
        self.isbn = isbn
        self.prestado = False

    def prestar(self):
        if not self.prestado:
            self.prestado = True
            return True
        return False

    def devolver(self):
        if self.prestado:
            self.prestado = False
            return True
        return False

class Miembro:
    def __init__(self, nombre, id_miembro):
        self.nombre = nombre
        self.id_miembro = id_miembro
        self.libros_prestados = []  # Composición: Miembro tiene una lista de Libros

    def tomar_prestado(self, libro):
        if libro.prestar():
            self.libros_prestados.append(libro)
            return True
        return False

    def devolver_libro(self, libro):
        if libro in self.libros_prestados and libro.devolver():
            self.libros_prestados.remove(libro)
            return True
        return False

class Biblioteca:
    def __init__(self, nombre):
        self.nombre = nombre
        self.libros = []      # Composición: Biblioteca tiene Libros
        self.miembros = []    # Composición: Biblioteca tiene Miembros

    def agregar_libro(self, libro):
        self.libros.append(libro)

    def agregar_miembro(self, miembro):
        self.miembros.append(miembro)

    def buscar_libro(self, isbn):
        return next((libro for libro in self.libros if libro.isbn == isbn), None)

# Ejemplo de uso
# Crear una biblioteca
biblioteca_central = Biblioteca("Biblioteca Central")

# Crear autores
autor1 = Autor("Gabriel García Márquez", "Colombiana")
autor2 = Autor("Jorge Luis Borges", "Argentina")

# Crear libros
libro1 = Libro("Cien años de soledad", autor1, "978-0307474728")
libro2 = Libro("El Aleph", autor2, "978-0307950901")

# Agregar libros a la biblioteca
biblioteca_central.agregar_libro(libro1)
biblioteca_central.agregar_libro(libro2)

# Crear y registrar un miembro
miembro1 = Miembro("Ana López", "M001")
biblioteca_central.agregar_miembro(miembro1)

# Realizar un préstamo
libro = biblioteca_central.buscar_libro("978-0307474728")
if libro and miembro1.tomar_prestado(libro):
    print(f"{miembro1.nombre} ha tomado prestado '{libro.titulo}'")
```

## 3. Ejemplo Práctico: Sistema de Notificaciones

```python
class Mensaje:
    def __init__(self, contenido):
        self.contenido = contenido
        self.hora_envio = None

class EmailNotifier:
    def enviar(self, mensaje, destinatario):
        return f"Enviando email a {destinatario}: {mensaje.contenido}"

class SMSNotifier:
    def enviar(self, mensaje, destinatario):
        return f"Enviando SMS a {destinatario}: {mensaje.contenido}"

class PushNotifier:
    def enviar(self, mensaje, destinatario):
        return f"Enviando notificación push a {destinatario}: {mensaje.contenido}"

class SistemaNotificaciones:
    def __init__(self):
        # Composición: el sistema tiene diferentes tipos de notificadores
        self.email_notifier = EmailNotifier()
        self.sms_notifier = SMSNotifier()
        self.push_notifier = PushNotifier()
    
    def notificar(self, mensaje, destinatario, metodos=None):
        if metodos is None:
            metodos = ['email']
        
        resultados = []
        for metodo in metodos:
            if metodo == 'email':
                resultados.append(self.email_notifier.enviar(mensaje, destinatario))
            elif metodo == 'sms':
                resultados.append(self.sms_notifier.enviar(mensaje, destinatario))
            elif metodo == 'push':
                resultados.append(self.push_notifier.enviar(mensaje, destinatario))
        
        return resultados

# Uso del sistema de notificaciones
sistema = SistemaNotificaciones()
mensaje = Mensaje("¡Reunión importante mañana!")
resultados = sistema.notificar(mensaje, "usuario@ejemplo.com", ['email', 'sms'])
for resultado in resultados:
    print(resultado)
```

## Buenas Prácticas en Composición

1. **Preferir Composición sobre Herencia**: Cuando sea posible, usa composición en lugar de herencia para crear objetos complejos.

2. **Mantener la Cohesión**: Cada clase debe tener una única responsabilidad bien definida.

3. **Bajo Acoplamiento**: Las clases deben ser lo más independientes posible entre sí.

4. **Interfaces Claras**: Define interfaces claras para la comunicación entre objetos.

5. **Encapsulamiento**: Oculta los detalles de implementación de los componentes.

## Conclusión

La composición es una herramienta poderosa en el diseño orientado a objetos que permite crear sistemas flexibles y mantenibles. Al combinar objetos más simples para crear otros más complejos, podemos construir aplicaciones modulares y fáciles de modificar.

Los ejemplos mostrados ilustran diferentes niveles de complejidad en el uso de la composición, desde casos básicos hasta sistemas más elaborados. La clave está en identificar las relaciones "tiene un" entre los objetos y estructurar el código de manera que refleje estas relaciones de forma clara y mantenible.
