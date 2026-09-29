# Tres en Raya Web con Minimax y Alfa-Beta

Aplicación web de **Tres en Raya** desarrollada con **Flask, Python, HTML, CSS y JavaScript**, con distintos modos de juego y dos agentes de Inteligencia Artificial basados en **Minimax** y **poda Alfa-Beta**.

El proyecto adapta a una interfaz web los algoritmos de búsqueda adversarial trabajados previamente en una práctica académica de Inteligencia Artificial.

---

## Funcionalidades

La aplicación permite jugar en tres modos:

- **Humano vs Humano**
- **Humano vs Minimax**
- **Humano vs Alfa-Beta**

Desde la interfaz web se puede:

- seleccionar el modo de juego;
- realizar movimientos haciendo clic sobre el tablero;
- visualizar el turno actual;
- detectar victoria o empate;
- reiniciar la partida.

---

## Inteligencia Artificial

La lógica utilizada por la versión web se encuentra en:

```text
Backend/webgame.py
```

### Minimax

El agente Minimax explora los posibles estados futuros del tablero y asigna una utilidad a los estados terminales:

```text
 1  -> victoria de la IA
 0  -> empate
-1  -> derrota de la IA
```

En los turnos propios maximiza la utilidad y en los turnos del oponente la minimiza.

### Poda Alfa-Beta

El segundo agente utiliza el mismo principio de decisión que Minimax, pero incorpora los límites `alpha` y `beta` para evitar explorar ramas que ya no pueden modificar la decisión final.

En un juego pequeño como Tres en Raya, ambos algoritmos pueden analizar completamente el espacio relevante de estados. La principal diferencia entre ellos es la eficiencia de la búsqueda, no un nivel de juego deliberadamente inferior o superior.

---

## Arquitectura

El proyecto está dividido en una parte web y una parte de lógica de juego.

```text
tresenraya_web/
├── Backend/
│   ├── modulos/
│   │   ├── AlfaBeta.py
│   │   ├── Estado.py
│   │   ├── Humano.py
│   │   ├── MinMax.py
│   │   ├── Resultados.py
│   │   ├── Tablero.py
│   │   ├── jugar.py
│   │   ├── main.py
│   │   └── menus.py
│   ├── __init__.py
│   └── webgame.py
├── frontend/
│   ├── index.html
│   ├── script.js
│   └── style.css
├── app.py
├── Procfile
├── requirements.txt
├── .gitignore
└── README.md
```

---

## Backend web

### `app.py`

Es el punto de entrada de la aplicación Flask.

Define tres rutas principales:

### `GET /`

Sirve la interfaz web:

```text
frontend/index.html
```

### `POST /iniciar`

Inicializa una nueva partida y almacena el modo seleccionado.

Ejemplo conceptual de petición:

```json
{
  "modo": "minimax"
}
```

### `POST /movimiento`

Recibe una posición del tablero:

```json
{
  "fila": 0,
  "columna": 2
}
```

El servidor:

1. valida que la casilla esté libre;
2. realiza el movimiento;
3. comprueba si la partida ha terminado;
4. ejecuta el turno de la IA cuando corresponde;
5. devuelve el nuevo estado del tablero.

La respuesta contiene:

- tablero;
- ganador;
- estado de empate;
- turno actual.

---

## Motor de juego

`Backend/webgame.py` contiene las clases utilizadas directamente por la aplicación web.

### `GameState`

Gestiona:

- tablero 3 × 3;
- turno actual;
- movimientos disponibles;
- cambio de turno;
- detección de ganador;
- detección de empate;
- estados terminales;
- copia de estados.

### `Minimax`

Calcula la mejor jugada mediante búsqueda Minimax.

### `AlfaBeta`

Calcula la mejor jugada utilizando poda Alfa-Beta.

---

## Frontend

La interfaz no utiliza frameworks de JavaScript.

Está construida únicamente con:

- HTML;
- CSS;
- JavaScript.

### `frontend/index.html`

Define:

- selector de modo;
- tablero;
- estado de la partida;
- botón de reinicio.

### `frontend/script.js`

Gestiona:

- llamadas a la API mediante `fetch`;
- representación dinámica del tablero;
- interacción mediante clics;
- actualización de turnos;
- visualización de victoria y empate;
- reinicio de partidas.

### `frontend/style.css`

Contiene los estilos visuales y una adaptación básica del tamaño del tablero para pantallas mayores.

---

## Código académico original

La carpeta:

```text
Backend/modulos/
```

contiene la implementación anterior del Tres en Raya realizada para consola, con clases y módulos para:

- Minimax;
- Alfa-Beta;
- estado del juego;
- tablero;
- jugador humano;
- resultados;
- menús;
- enfrentamientos Humano vs IA e IA vs IA.

Esta carpeta sirve como referencia de la versión académica previa.

La aplicación Flask actual **no utiliza directamente estos módulos**. Para el funcionamiento web se emplea la implementación simplificada de:

```text
Backend/webgame.py
```

---

## Tecnologías

### Backend

- **Python**
- **Flask**
- **Gunicorn**

### Frontend

- **HTML5**
- **CSS3**
- **JavaScript**
- Fetch API

### Inteligencia Artificial

- Minimax
- Poda Alfa-Beta
- árboles de estados
- funciones de utilidad
- búsqueda adversarial

---

## Instalación

Clona el repositorio:

```bash
git clone https://github.com/Nico3246/tresenraya_web.git
cd tresenraya_web
```

Opcionalmente, crea un entorno virtual:

```bash
python -m venv .venv
```

En Windows:

```powershell
.\.venv\Scripts\activate
```

En Linux o macOS:

```bash
source .venv/bin/activate
```

Instala las dependencias:

```bash
pip install -r requirements.txt
```

---

## Ejecución local

Ejecuta:

```bash
python app.py
```

Por defecto Flask iniciará el servidor de desarrollo y la aplicación podrá abrirse desde la dirección local indicada en la terminal, normalmente:

```text
http://127.0.0.1:5000
```

---

## Ejecución con Gunicorn

El repositorio incluye:

```text
Procfile
```

con la configuración:

```text
web: gunicorn app:app
```

También puede iniciarse manualmente con:

```bash
gunicorn app:app
```

---

## Flujo de una partida

1. El usuario abre la aplicación.
2. Selecciona un modo.
3. El frontend llama a `/iniciar`.
4. Flask crea un nuevo `GameState`.
5. El jugador selecciona una casilla.
6. El frontend envía el movimiento a `/movimiento`.
7. El backend actualiza el tablero.
8. Si el modo utiliza IA y la partida sigue activa, Minimax o Alfa-Beta calcula su jugada.
9. El backend devuelve el estado actualizado.
10. El frontend vuelve a dibujar el tablero.

---

## Estado y limitaciones

El proyecto es una aplicación funcional de carácter académico y demostrativo.

Actualmente:

- el tablero es siempre de 3 × 3;
- el jugador humano comienza con `X`;
- la interfaz es sencilla y está orientada a demostrar el funcionamiento de los algoritmos;
- no existe autenticación;
- no existe base de datos;
- las partidas no se conservan al reiniciar el servidor;
- el estado de la partida se almacena en variables globales del proceso Flask.

Este último punto implica que la implementación actual está pensada para demostraciones o uso individual. En un entorno con varios usuarios simultáneos sería necesario mantener un estado independiente por sesión o partida.

---

## Relación con la práctica de IA

Este proyecto reutiliza y adapta conceptos de una práctica universitaria sobre **Minimax y poda Alfa-Beta**.

La versión original por consola permanece en `Backend/modulos/`, mientras que la versión web separa:

- presentación en el navegador;
- API Flask;
- estado del juego;
- algoritmos de decisión.

Esto permite trasladar los conceptos estudiados en la práctica a una aplicación web interactiva.

---

## Conceptos trabajados

Con este proyecto se trabajan:

- búsqueda adversarial;
- Minimax;
- poda Alfa-Beta;
- árboles de juego;
- funciones de utilidad;
- generación de sucesores;
- desarrollo backend con Flask;
- endpoints HTTP;
- comunicación cliente-servidor con JSON;
- Fetch API;
- manipulación dinámica del DOM;
- integración entre Python y una interfaz web.
