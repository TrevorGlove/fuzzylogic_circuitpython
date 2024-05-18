# FuzzyLib
FuzzyLib es una librería que incorpora lógica difusa en CircuitPython. Una de las principales ventajas de utilizar CircuitPython es la inclusión de la biblioteca Ulab con Numpy, lo que nos permite manipular vectores sin utilizar memoria a compración de los arreglos y listas.

## Fuzzy Logic

Para ilustrar cómo opera la lógica difusa en FuzzyLib, consideremos un ejemplo de control de posición de motor.
En primer lugar, es necesario definir las variables a fuzzificar, que en este caso son el setpoint y el error de posición.
Posteriormente, debemos establecer las funciones de membresía de estas variables. En el código, esta definición se realiza de la siguiente manera:

```python
# Definir Universos de entrada (error y setpoint) y salida (datacycle)
sp = (-500, 500)
e = (-500, 500)
v = (-65535, 65535)

# Definir las funciones de membresía de entrada
SPN = (-500, -500, -100, -10)  # Setpoint Negativo
SP0 = (-100, 0, 100)            # Setpoint Cero
SPP = (-10, 100, 500, 500)      # Setpoint Positivo
SP = [SPN, SP0, SPP]

ENL = (-500, -500, -300, -15)  # Error Negativo Lejano
ENC = (-300, -50, 15)          # Error Negativo Cercano
E0 = (-100, 0, 100)            # Error Cero
EPC = (-15, 50, 300)           # Error Positivo Cercano
EPL = (15, 300, 500, 500)      # Error Positivo Lejano
E = [ENL, ENC, E0, EPC, EPL]

# Definir las funciones de membresía de salida
VAN = (-65535, -65535, -43690, -21845)   # Velocidad Alta Negativa
VBN = (-43690, -21845, 0)                # Velocidad Baja Negativa
V0 = (-10000, 0, 10000)                  # Velocidad Cero
VBP = (0, 21845, 43690)                  # Velocidad Baja Negativa
VAP = (21845, 43690, 65535, 65535)       # Velocidad Alta Positiva
V = [VAN, VBN, V0, VBP, VAP]
```

![Funciones de Membresía de Error de Velocidad y Derivada de Error de Velocidad](img/Membership.png)

Si utilizaramos listas y arreglos, com la funcion gc.mem_free() nos sale que consume una memoria de 848 bytes. Con Ulab Numpy, esto de reduce a 576 bytes.

Luego, se tienen que definir las Reglas Difusas. La forma de declarar las Reglas Difusas dependerá de la versión de la librería. En la versión 1.0, la forma de declarar era mediante esta matriz:

```python
R = [[SPN, ENA, VAN],
     [SPN, ENB, VBN],
     [SPN, E0, V0],
     [SPN, EPB, VBP],
     [SPN, EPA, VAP],
     [SP0, ENA, VAN],
     [SP0, ENB, VBN],
     [SP0, E0, V0],
     [SP0, EPB, VBP],
     [SP0, EPA, VAP],
     [SPP, ENA, VAN],
     [SPP, ENB, VBN],
     [SPP, E0, V0],
     [SPP, EPB, VBP],
     [SPP, EPA, VAP]]
```
La interpretación de esta matriz se basa en que cada regla difusa se aplica por fila. Por ejemplo:

Si el Setpoint es Negativo y el Error es Negativo Alto, entonces la Velocidad es Alta Negativa.
Si el Setpoint es Positivo y el Error es Negativo Corto, entonces la Velocidad es Baja Negativa.

Es importante señalar que en esta versión solo se puede utilizar el operador "and". Sin embargo, con las actualizaciones, se están agregando más operadores. Puedes consultar la documentación en este repositorio para más detalles.

Finalmente, se aplican las reglas difusas a cada entrada. El resultado es una combinación de trapecios en la salida. Fuzzy Lib identifica los valores fuzzificados de la salida y los defuzzifica utilizando el método del centroide (otros métodos en próximas actualizaciones) para obtener el valor final de la salida. 

![Defuzzificación](img/Fuzzyfication.png)

Ya con la idea de cómo funciona La lógica Difusa en FuzzyLib, te invito a revisar la documentación sobre las versiones.
