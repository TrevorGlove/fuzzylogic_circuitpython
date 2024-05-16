# FuzzyLib
FuzzyLib es una librería que implementa una lógica difusa en Circuitpython. Le ventaja de utilizar Circuitpython es la librería Ulab con Numpy lo cual nos permite trabajar con vectores en Numpy que nos permite ahorrar memoria en el microcontrolador.

## Fuzzy Logic

Ahora se presentará visualmente cómo trabaja la lógica Difusa en FuzzyLib como para repasar un poco la teoría. En este ejemplo, se realizará un control de Posición de Motor.
Primeramente, se tiene que definir las variables a fuzzificar. Para este caso, se ha determinado que son el Setpoint y el Error de Posición.
Luego se tiene que definir los universos y las funciones de membresía de las variables. En el código se tiene que definir de esta manera.

```python
# Definir Universos de entrada (setpoint y setpoint) y salida (datacycle)
sp = (-500, 500)
e = (-500, 500)
v = (-65535, 65535)

# Definir las funciones de membresía de entrada

SPN = (-500, -500, -60, -10)  # Setpoint Negativo
SP0 = (-500, 0, 500)          # Setpoint Cero
SPP = (-100, 30, 45, 500)      # Setpoint Positivo

SP = [SPN, SP0, SPP]

ENL = (-500, -500, -300, -15)  # Error Negativo Lejano
ENC = (-100, -50, 0)           # Error Negativo Cercano
E0 = (-40, 0, 40)            # Error Cero
EPC = (-15, 50, 200)             # Error Positivo Cercano
EPL = (15, 300, 500, 500)     # Error Positivo Lejano

E = [ENL, ENC, E0, EPC, EPL]

# Definir las funciones de membresía de salida

VAN = (-65535, -65535, -43690, -21845)   # Velocidad Alta Negativa
VBN = (-43690, -21845, 0)                # Velocidad Baja Negativa
V0 = (-21845, -10000, 10000, 21845)     # Velocidad Cero
VBP = (0, 21845, 43690)                  # Velocidad Baja Negativa
VAP = (21845, 43690, 65535, 65535)       # Velocidad Alta Positiva

V = [VAN, VBN, V0, VBP, VAP]
```

![Funciones de Membresía de Error de Velocidad y Derivada de Error de Velocidad](img/Membership.png)


