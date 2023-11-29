1.3.4. Caso práctico en Python: ¡Piedra, Papel o Tijeras!
========================================================

> Atribución:

El código utilizado y su explicación es contenido redactado por la Escuela Superior de Informática de Ciudad Real, de la Universidad de Castilla La Mancha, para el curso de formación del profesorado de los ciclos de grado superior de Formación Profesional que imparte docencia en el curso de especialización en Inteligencia Artificial y Big Data en los CIFP de la Conselleria de Educació de les Illes Balears.

Esta sección discute un caso práctico en el que se plantea un desarrollo incremental del clásico juego piedra, papel o tijeras24. Se trata de un juego de manos en el que participan dos jugadores. En cada ronda, cada jugador forma, simultánea- mente, una de estas tres formas con la mano extendida. Estas formas son: i) piedra, representada por un puño cerrado, ii) papel, representada mediante la mano abierta, y iii) tijeras, representada por un puño con los dedos índice y corazón extendidos, formando una V. El juego solo tiene dos resultados posibles. Uno de ellos es el empate, si ambos jugadores eligen la misma forma. El otro se define por una situación en la que un jugador gana y el otro pierde. En este sentido, la piedra vence (o rompe) a las tijeras, el papel vence (o envuelve) a la piedra y, finalmente, las tijeras vencen (o cortan) al papel.

Desde un punto de vista más formal, y en el contexto de la **teoría de juegos no competitivos**, se trata de un juego de **suma cero**∫, ya que una situación de victoria para un jugador se equilibra, o se traduce, en una situación de derrota para el jugador contrario. La suma cero es un caso particular de uno más general, el de **suma constante**∫, donde los beneficios y las pérdidas de los jugadores suman el mismo valor. Esto se debe a que un jugador recibe como beneficio la misma cantidad que pierde el jugador contrario. Por otro lado, los juegos de **suma no nula** son aquellos en los que se producen situaciones en las que los jugadores pueden ganar o perder de manera simultánea.

Debido a que en cada ronda un jugador solo tiene 3 opciones de juego (piedra, papel o tijeras), es posible obtener una buena tasa de victorias jugando de manera puramente aleatoria. No obstante, y a diferencia de los métodos de selección ver- daderamente aleatorios, este juego se puede jugar aplicando una estrategia basada en la detección y aprovechamiento del comportamiento no aleatorio del jugador contrario.

En los apartados que se incluyen a continuación se discute una sencilla implementación de este juego, diseñada de forma incremental mediante el lenguaje de programación Python con el objetivo de ofrecer al lector un punto de partida para modificar y mejorar el código fuente.

### Código base y comportamiento aleatorio de la máquina

El listado 1.13 muestra la implementación de partida de una posible solución que recree el juego piedra, papel o tijeras. Como se describirá más adelante, el comportamiento de la máquina en esta primera versión es totalmente aleatorio.

```python
# Listado 1.13: Implementación base del juego piedra, papel o tijeras.

#!/usr/bin/python3

import random

ROCK = ’rock’
PAPER = ’paper’
SCISSORS = ’scissors’

def assess_game(user_action, computer_action): 
    if user_action == computer_action:
        print(f"User and computer picked {user_action}. Draw game!")

    # You picked Rock
    elif user_action == ROCK:
        if computer_action == SCISSORS:
            print("Rock smashes scissors. You won!") 
        else:
        print("Paper covers rock. You lost!")
    
    # You picked Paper
    elif user_action == PAPER:
        if computer_action == ROCK:
            print("Paper covers rock. You won!")
        else:
            print("Scissors cuts paper. You lost!")
    
    # You picked Scissors
    elif user_action == SCISSORS:
        if computer_action == ROCK:
            print("Rock smashes scissors. You lost!") 
        else:
            print("Scissors cuts paper. You won!")

def main():
    game_actions = [ROCK, PAPER, SCISSORS]
    while True:
        user_action = input("\nPick a choice: rock, paper or scissors: ")
        computer_action = random.choice(game_actions)

        print(f"\nYou picked {user_action}. The computer picked {computer_action}\n")
        assess_game(user_action, computer_action)


if __name__ == "__main__":
    main()
```

Se invita al lector a descargar y probar el código mediante la siguiente instrucción, o directamente desde su editor visual de código.

`$ python3 01_RPS_Basic.py`

En este primera versión de código, solo existen dos funciones: i) `main()`, que comprende las líneas 38-46 y que incluye el código de control para jugar infinitas rondas de juego, y ii) `assess_game()`, que está definida en las líneas 11-34 y que es la responsable de mostrar el resultado de cada ronda de juego tras evaluar las decisiones tomadas por el jugador y por la máquina.

En la función `main()` destacan las siguientes cuestiones:

- Las posibles acciones de juego, modeladas mediante constantes en las líneas 6, 7 y 8, respectivamente, se añaden a la lista `game_actions` (línea 39) para facilitar la generación aleatoria de las decisiones de la máquina. Esta generación se delega en la función `choice()` del módulo `random`, como se muestra en la línea 43.

- El usuario introduce su movimiento mediante una cadena de texto, después de que el programa se lo solicite. Para ello, se hace uso de la función `input()` en la línea 42. El programa espera que el jugador introduzca una de las siguientes cadenas de texto: `rock`, `paper` o `scissors`.

- Como se ha comentado anteriormente, la evaluación de la ronda de juego se delega en la función `assess_game()` en la línea 46, que recibe como argumentos la acción del jugador y la de la máquina. Recuerde que las acciones se han modelado mediante cadenas de texto.

Por otra parte, en la función `assess_game()` destacan los siguientes aspectos a nivel de código:

- La comparación de las decisiones del jugador y de la máquina se lleva a cabo mediante un conjunto de sentencias `if-else` anidadas.
- Al mismo tiempo que se realiza la evaluación, se muestra información por la salida estándar con respecto a dicha evaluación y a su justificación.

### Añadiendo control de errores

El código discutido anteriormente no contemplaba una situación en la que el jugador introdujera, usando el teclado, una entrada de texto diferente a rock, paper o scissors. En este contexto, el listado 1.14 muestra el código extra añadido a la versión anterior para incluir un control de errores básico. A continuación, se listan los aspectos más destacables del nuevo código:

```python
class IncorrectOptionException(Exception):
    pass

def assess_game(user_action, computer_action): 
    # Previous code here
    # ...

def main():
    game_actions = [ROCK, PAPER, SCISSORS]

    while True:
        try:
            user_action = input("\nPick a choice: rock, paper or scissors: ").lower()
            if user_action not in game_actions:
                raise IncorrectOptionException
            computer_action = random.choice(game_actions)

            print(f"\nYou picked {user_action}. The computer picked {computer_action}\n")
            assess_game(user_action, computer_action)
        except IncorrectOptionException:
            print("\nYou can only picked rock, paper or scissors!")
```

### Mejorando la mantenibilidad del código

Hasta ahora, el código discutido estaba más centrado en la lógica de dominio, en un contexto general de prototipar una solución rápida que permitiera jugar a piedra, papel o tijeras con la máquina. En este apartado, se abordan algunos aspectos que aumentarán la calidad y mantenibilidad del código, facilitando así potenciales modificaciones futuras.

En primer lugar, se introduce el uso de los **tipos enumerados** en Python. Una enumeración se puede definir como un conjunto de nombres simbólicos que están asociados, de manera unívoca, a valores constantes. En el contexto de una enumeración, los miembros o elementos de la misma se pueden comparar por su identidad. Además, es posible iterar sobre la propia enumeración.

En el código anterior, se hizo uso de 3 constantes para modelar las 3 acciones posibles: `rock, paper o scissors`. En este punto, se va a optar por el uso de un tipo enumerado, denominado `GameAction`, para representar dichas acciones. El listado 1.15 muestra el tipo enumerado `GameAction`, el cual hereda de la clase `enum.IntEnum`, definida en Python como clase base para crear constantes enumeradas que también son una subclase de `int`.

```python
# Listado 1.15: Tipo enumerado GameAction

from enum import IntEnum

class GameAction(IntEnum): 
    Rock = 0
    Paper = 1
    Scissors = 2
```

Por otro lado, resulta muy aconsejable estructurar el código del apartado anterior en funciones, con el objetivo de acotar responsabilidades y aumentar la mantenibilidad global del mismo. Es por ello que en la tercera versión de la solución planteada en esta sección se han incluido nuevas funciones, que serán llamadas desde la función principal `main()`. Precisamente, el listado 1.16 muestra el nuevo aspecto de dicha función, la cual introduce las siguientes novedades:

- La acción de juego elegida por el jugador se obtiene a través de una llamada a la nueva función `get_user_action()`, tal y como se muestra en la línea 4.

- Se ha generalizado la impresión de las opciones de juego a través de la cadena `range_str` de la línea 6. Particularmente, en la cadena `range_str` se almacenará el texto `[0, 2]`, ya que la expresión `len(GameAction)` devuelve el número de elementos definidos en el tipo enumerado `GameAction`. Si en un futuro se añaden más miembros a `GameAction`, entonces no será necesario modificar la línea 6. En este sentido, se está planteando una solución escalable a nivel de código.

- La función `get_user_action()`, como se discutirá más adelante, arrojará una excepción del tipo `ValueError` si el jugador introduce una acción o valor numérico que no esté comprendido en el rango `[0, 2]`. Esta excepción se captura en el bloque `try` de la línea 3, y se gestiona en el bloque except de la línea 5. Resulta recomendable usar las excepciones que ofrece el propio lenguaje, en lugar de usar excepciones propias, tal y como se discutió en el apartado anterior a través de la excepción `IncorrectOptionException`.

- La acción de juego elegida por la máquina se obtiene a través de la nueva función`get_computer_action()`. Como se puede apreciar, y al igual que ocurre con la función `get_user_action()`, se está aplicando el **principio de responsabilidad única (o SRP)**, aumentando el nivel de estructuración y mantenibilidad del código.

- El código ofrece, de manera explícita, la posibilidad de jugar, o no, otra ronda al jugador. Esta lógica se materializa en las líneas 13-14, y se delega en la nueva función `play_another_round()`. Note el uso del operador `not`.

```python
# Listado 1.16: Función main() delegando funcionalidad

def main(): 
    while True:
        try:
            user_action = get_user_action()
        except ValueError as e:
            range_str = f"[0, {len(GameAction) - 1}]"
            print(f"Invalid selection. Pick a choice in range {range_str}!")
            continue

    computer_action = get_computer_action()
    assess_game(user_action, computer_action)

    if not play_another_round():
        break
```

> Principio de responsabilidad única. 
Recuerde que, de acuerdo al concepto SRP (Single-Responsibility Principle), todo módulo, clase o función de su código debería tener una responsabilidad sobre una única parte de la funcionalidad de su programa, encapsulándola de manera adecuada.

La función `get_user_action()` se muestra en el listado 1.17. Destaca, particularmente, el uso de la construcción sintáctica denominada **list comprehension** en la línea 3 que Python ofrece. En esencia, esta característica representa una forma elegante de definir y crear listas basadas en listas existentes. La ventaja de esta característica se deriva de la iteración que se realiza sobre los miembros del tipo enumerado `GameAction`, de forma que si, en el futuro, se añaden más miembros (como en la variante del juego rock paper scissors lizard spock), simplemente sería necesario modificar la definición de `GameAction`.

La lista `game_choices` de la línea 3 se usa para mostrar por pantalla las opciones de juego disponibles, en la línea 6. Posteriormente, la acción escogida por el jugador se crea como tipo enumerado (línea 7) y se devuelve (línea 9).

```python
# Listado 1.17: Función get_user_action()

def get_user_action():
    # Scalable to more options (beyond rock, paper and scissors...)
    game_choices = [f"{game_action.name}[{game_action.value}]" for game_action in GameAction]
    game_choices_str = ", ".join(game_choices)
    user_selection = int(input(f"\nPick a choice ({game_choices_str}): "))
    user_action = GameAction(user_selection)
    
    return user_action
```

El código relativo a la toma de decisiones de la máquina se ha encapsulado, en esta nueva versión del código, en la función `get_computer_action()`, la cual se muestra en el listado 1.18. Simplemente destaca, con respecto a la versión anterior, el uso del tipo enumerado para gestionar la decisión aleatoria tomada por la máquina.

```python
# Listado 1.18: Función get_computer_action()

def get_computer_action():
    computer_selection = random.randint(0, len(GameAction) - 1)
    computer_action = GameAction(computer_selection)
    print(f"Computer picked {computer_action.name}.")
    
    return computer_action
```

La última función que faltaría por discutir es `play_another_round()`, que se muestra en el listado 1.19. Note cómo tanto la comparación de cadenas como la devolución de comparación se efectúan en una única línea, la línea 3.

```python
# Listado 1.19: Función play_another_round()

def play_another_round():
    another_round = input("\nAnother round? (y/n): ")
    return another_round.lower() == ’y’
```

## Código OCP

Por otro lado, en el listado 1.21 se muestran dos estructuras de datos que hay que incorporar a la nueva versión del código. 

La función `assess_game` debe devolver como resultado del juego uno de los valores del diccionario `Victories`.

1. `GameResult`, definida como un **tipo enumerado** para gestionar valores enteros que permite modelar los posibles resultados tras una ronda de juego: victoria (`victory`), derrota (`defeat`) o empate (`tie`).
2. `Victories`, definida como un **diccionario** para almacenar qué acción genera una situación de victoria con respecto a la acción efectuada por un contrincante. Por ejemplo, y como se refleja en la línea 8, `paper` derrota a `rock`.

```python
# Listado 1.21: Estructuras de datos para gestionar resultados y decisiones ganadoras

class GameResult(IntEnum):
    Victory = 0
    Defeat = 1
    Tie = 2

Victories = {
    GameAction.Rock: GameAction.Paper,
    GameAction.Paper: GameAction.Scissors,
    GameAction.Scissors: GameAction.Rock
}
```

Un diccionario se puede entender como un conjunto de pares clave-valor, con el requisito de que las claves son únicas en el contexto de un diccionario. En Python, es posible crear un diccionario vacío con un par de llaves `{}`, o crearlos e inicializarlos con una lista de pares clave-valor separadas por coma, tal y como se puede apreciar en el listado 1.21 (ver líneas 7-11). La indexación de los diccionarios se realiza mediante las claves. En Python, el tipo asociado a la clave debe ser inmutable; por ejemplo, las cadenas de texto o los números pueden actuar como claves.
