# FuzzyLib
FuzzyLib es una librería que incorpora lógica difusa en CircuitPython. Una de las principales ventajas de utilizar CircuitPython es la inclusión de la biblioteca Ulab con Numpy, lo que nos permite manipular vectores sin utilizar memoria a compración de los arreglos y listas.

## Fuzzy Logic 

Para ilustrar cómo opera la lógica difusa en FuzzyLib, consideremos un ejemplo de control de posición de motor.
En primer lugar, es necesario definir las variables a fuzzificar, que en este caso son el setpoint y el error de posición.
Posteriormente, debemos establecer las funciones de membresía de estas variables. En el código de FuzzyLib beta, esta definición se realizaba mediante tuplas:

![Funciones de Membresía de Error de Velocidad y Derivada de Error de Velocidad](img/Membership.png)

Luego, se tienen que definir las Reglas Difusas. Por ejemplo:

- Si el Setpoint es Negativo y el Error es Negativo Alto, entonces la Velocidad es Alta Negativa.
- Si el Setpoint es Positivo y el Error es Negativo Corto, entonces la Velocidad es Baja Negativa.


Finalmente, se aplican las reglas difusas a cada entrada. El resultado es una combinación de trapecios en la salida. Fuzzy Lib identifica los valores fuzzificados de la salida y los defuzzifica utilizando el método del centroide para obtener el valor final de la salida. 

![Defuzzificación](img/Fuzzyfication.png)

Ya con la idea de cómo funciona La lógica Difusa en FuzzyLib, te invito a revisar la documentación sobre las versiones.

## FuzzyLib beta

La versíon Beta de FuzzyLib no usa Circuitpython por lo que se puede utilizar tambien en Micropython. Las funciones de membresía se declaran en tuplas y las reglas difusas en una matriz de listas.

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

# Definir las Reglas
R = [(SPN, ENL, VAN),
     (SPN, ENC, VBN),
     (SPN,  E0,  V0),
     (SPN, EPC, VBP),
     (SPN, EPL, VAP),
     (SP0, ENL, VAN),
     (SP0, ENC, VBN),
     (SP0,  E0,  V0),
     (SP0, EPC, VBP),
     (SP0, EPL, VAP),
     (SPP, ENL, VAN),
     (SPP, ENC, VBN),
     (SPP,  E0,  V0),
     (SPP, EPC, VBP),
     (SPP, EPL, VAP)]
```
Es importante señalar que en esta versión solo se puede utilizar el operador "and".
Luego, se aplica el siguiente código para la salida difusa:
```python
Val = Fuzzy(R, x, (SP, E))
Lines_cut = Proyect(Val)
Trapezoids = Cut(Lines_cut, Val)
Vf = Defuzzy(Trapezoids, v, 100)
```

```python
def Fuzzy(rules, x, variables_input), donde:
```
- *rules*: Matriz de Reglas
- *x*: Vector de entrada del bloque difuso
- *variables_input*: Tupla con las entradas, cada entrada una lista con sus funciones de membresía
- Salida: La función retorna un diccionario cuyas claves eran las tuplas de las funciones de membresía de salida y las claves sus valores de corte.

```python
def Proyect(membership_out_values)
```
- *membership_out_values*: Salida de la función Fuzzy.
- Salida: El código retorna un diccionario cuyas claves son las tuplas de la funciones de membresía de salida y las claves otra tupla con los nuevos puntos de las funciones de membresía cortados por sus valores de proyección.

```python
def Cut(membership_out_proyect, membership_out_values)
```
- *membership_out_values*: Salida de la función Fuzzy.
- *membership_out_proyect*: Salida de la función Proyect.
- Salida: La función retorna un diccionario cuyas tuplas son las tuplas de las funciones de membreía de salida y las claves una lista con lo puntos de las funciones de membresía cortados por sus valores de proyección concatenados con el valor de proyección.

```python
def  Defuzzy(membership_out_proyect_values, universe_out, n)
```
- *membership_out_proyect_values*: Salida de la función Cut.
- *universe out*: Lista de los extremos del universo de salida.
- *n*: Número de divisiones sobre el universo de salida.
- Salida: La función retorna el centroide del poligono resultante de los trapecios de las funcuiones de membresía de salida.

