# Sistema Inteligente de Priorización de Despacho en Redes de Transporte Urbano
### Modelado de Redes en Grafos Geodésicos y Clasificación Lineal con Perceptrón de Rosenblatt

**Cátedra:** Matemáticas Discretas (MAT 4021)  
**Institución:** Universidad del Norte (Barranquilla, Colombia)  
**Docente Titular:** MSc. Iván Gómez  
**Tema Central:** Optimización del Despacho de Buses de Refuerzo en el Corredor Universitario (Carrera 51B) frente al impacto vial de las obras de *La Gran Vía*.  
**Enfoque:** 100% Matemático y determinista, implementado desde cero en Python/NumPy (**CERO librerías de caja negra**).

---

## 1. Contexto Territorial y Justificación del Problema

El Corredor Universitario de la Carrera 51B (que conecta el norte de Barranquilla con Puerto Colombia) moviliza diariamente a más de **45.000 usuarios**, principalmente estudiantes, docentes y trabajadores de la Universidad del Norte, Universidad del Atlántico (Sede Norte), Universidad Libre, Universidad Antonio Nariño y múltiples colegios del sector.

A partir de la ejecución de las obras civiles de ampliación vial denominadas **"La Gran Vía"** (Gobernación del Atlántico / Área Metropolitana de Barranquilla - AMB):
- Se redujeron las calzadas operativas en tramos neurálgicos (Portoazul, San José, UniNorte).
- Las rutas de transporte público colectivo y masivo (**Sobusa Rutas 1 y 2, Coolitoral y Transmetro U30**) experimentaron demoras críticas de **35 a 50 minutos en horas pico**, con tasas de rechazo por sobrecupo superiores al 65%.

Este proyecto aplica **teoría formal de grafos** y un clasificador lineal discreto (**Perceptrón de Rosenblatt**) para identificar de forma autónoma y determinista cuáles paraderos demandan el despacho urgente de buses de refuerzo en vacío ($y=1$) frente al régimen estándar ($y=0$).

---

## 2. Arquitectura Metodológica de Doble Escala

Para satisfacer a cabalidad tanto la **prueba analítica de escritorio con resultado conocido** (exigida por la rúbrica) como la **demostración interactiva en vivo a escala real**, el proyecto implementa dos niveles de análisis complementarios:

```
┌────────────────────────────────────────────────────────────────────────────────┐
│               ARQUITECTURA DE DOBLE ESCALA DEL PROYECTO                        │
├──────────────────────────────────────┬─────────────────────────────────────────┤
│   ESCALA 1: CASO DE PRUEBA CONOCIDO  │      ESCALA 2: RED ARTERIAL COMPLETA    │
│            (6 NODOS)                 │                (15 NODOS)               │
├──────────────────────────────────────┼─────────────────────────────────────────┤
│ • Subgrafo diamante (ciclo C4)       │ • Corredor Cra 51B completo (Circunvalar│
│ • Cumple rúbrica: resultado manual   │   hasta Entrada Puerto Colombia)        │
│   demostrable paso a paso            │ • Simulación de cierre vial en obras    │
│ • BFS y caminos empatados (0.5)      │ • Recálculo dinámico de geodésicas      │
│ • Traza de épocas 1 y 2 con E = 0    │ • Interfaz web editorial (localhost)    │
│ • Salida por consola en main.py      │ • Simulador de colas Antes vs. Después  │
└──────────────────────────────────────┴─────────────────────────────────────────┘
```

---

## 3. Formulación Matemática Rigurosa

### 3.1. Caso de Prueba Conocido: Subgrafo Diamante (6 Nodos)
Se define un grafo conexo no dirigido $G = (V, E)$ compuesto por $|V| = 6$ vértices y $|E| = 6$ aristas:

| Vértice | Paradero | Función Territorial y Flujo | Afluencia ($x_2$) | Clase Real ($y$) |
| :---: | :--- | :--- | :---: | :---: |
| **$v_1$** | Club Campestre | Acceso norte al corredor / Villa Campestre | 0.20 | 0 (Estándar) |
| **$v_2$** | Clínica Portoazul | Tramo crítico occidental afectado por La Gran Vía | 0.70 | 0 (Estándar) |
| **$v_3$** | UniNorte Puerta 4 | Tramo oriental / Puente peatonal (máxima afluencia) | 0.95 | **1 (Refuerzo)** |
| **$v_4$** | Makro / Transversal | Nodo de articulación, desvío y conector hacia Cra 53 | 0.80 | **1 (Refuerzo)** |
| **$v_5$** | UniAtlántico / Colegios | Nodo interinstitucional y universitario secundario | 0.55 | 0 (Estándar) |
| **$v_6$** | Retorno Sur 51B | Terminal sur de enlace y retorno hacia Barranquilla | 0.15 | 0 (Estándar) |

Aristas habilitadas:
$$E = \{(v_1, v_2), (v_1, v_3), (v_2, v_4), (v_3, v_4), (v_4, v_5), (v_5, v_6)\}$$

```
                  [v2: Portoazul / Gran Vía]
                  /                        \
    [v1: Campestre]                        [v4: Makro] --- [v5: UniAtlántico] --- [v6: Retorno Sur]
                  \                        /
                  [v3: UniNorte Puerta 4]
```

### 3.2. Matriz de Adyacencia Binaria $A(G)$ y Distancias Geodésicas $D$ (Vía BFS por Capas)

$$A = \begin{pmatrix}
0 & 1 & 1 & 0 & 0 & 0 \\
1 & 0 & 0 & 1 & 0 & 0 \\
1 & 0 & 0 & 1 & 0 & 0 \\
0 & 1 & 1 & 0 & 1 & 0 \\
0 & 0 & 0 & 1 & 0 & 1 \\
0 & 0 & 0 & 0 & 1 & 0
\end{pmatrix}, \qquad
D = \begin{pmatrix}
0 & 1 & 1 & 2 & 3 & 4 \\
1 & 0 & 2 & 1 & 2 & 3 \\
1 & 2 & 0 & 1 & 2 & 3 \\
2 & 1 & 1 & 0 & 1 & 2 \\
3 & 2 & 2 & 1 & 0 & 1 \\
4 & 3 & 3 & 2 & 1 & 0
\end{pmatrix}$$

### 3.3. Centralidades de Freeman y Tratamiento de Empates Geodésicos
- **Fórmula de Freeman:**
  $$C_B(v) = \sum_{s < t,\; s \neq v \neq t} \frac{\sigma_{st}(v)}{\sigma_{st}}$$
- **Caminos Empatados con Peso Fraccionario (Total 8 casos de 0.50):**  
  Entre $v_1$ y $v_4$ existen dos geodésicas de longitud mínima 2:
  $$\text{Ruta } 1: v_1 \to v_2 \to v_4 \quad (\text{longitud } 2), \qquad \text{Ruta } 2: v_1 \to v_3 \to v_4 \quad (\text{longitud } 2)$$
  Como $\sigma_{v_1 v_4} = 2$, el aporte a la intermediación de $v_2$ y $v_3$ es:
  $$\frac{\sigma_{v_1 v_4}(v_2)}{\sigma_{v_1 v_4}} = \frac{1}{2} = \mathbf{0.50}, \qquad \frac{\sigma_{v_1 v_4}(v_3)}{\sigma_{v_1 v_4}} = \frac{1}{2} = \mathbf{0.50}$$
  De igual forma, los pares $(v_1, v_5)$ y $(v_1, v_6)$ aportan $0.50$ a $v_2$ y $v_3$ cada uno, y el par $(v_2, v_3)$ aporta $0.50$ a $v_1$ y a $v_4$. En total, existen exactamente **8 aportes de 0.50** que explican con precisión la suma de $C_B$:
  - $C_B(v_1) = 0.50 \implies C_B' = 0.50 / 10 = 0.05$
  - $C_B(v_2) = 0.50 + 0.50 + 0.50 = 1.50 \implies C_B' = 1.50 / 10 = 0.15$
  - $C_B(v_3) = 0.50 + 0.50 + 0.50 = 1.50 \implies C_B' = 1.50 / 10 = 0.15$
  - $C_B(v_4) = 0.50 + 6 \times 1.0 = 6.50 \implies C_B' = 6.50 / 10 = 0.65$
  - $C_B(v_5) = 4 \times 1.0 = 4.00 \implies C_B' = 4.00 / 10 = 0.40$
  - $C_B(v_6) = 0.00 \implies C_B' = 0.00$
- **Factor de Normalización Teórica:**
  $$\binom{n-1}{2} = \binom{5}{2} = \frac{5 \times 4}{2} = 10 \implies C_B'(v) = \frac{C_B(v)}{10} \in [0, 1]$$

#### Tabla Consolidada de Métricas de Red (Resultado Teórico Demostrado):
| Vértice | Paradero | $C_D$ | $\sum d$ | $C_C$ | $C_B$ | $C_B'$ | Afluencia ($x_2$) | Clase Real ($y$) |
| :---: | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **$v_1$** | Club Campestre | 2 | 11 | $5/11 \approx 0.455$ | 0.50 | **0.05** | 0.20 | 0 (Estándar) |
| **$v_2$** | Clínica Portoazul | 2 | 9 | $5/9 \approx 0.556$ | 1.50 | **0.15** | 0.70 | 0 (Estándar) |
| **$v_3$** | UniNorte Puerta 4 | 2 | 9 | $5/9 \approx 0.556$ | 1.50 | **0.15** | 0.95 | **1 (Refuerzo)** |
| **$v_4$** | Makro / Transversal | 3 | 7 | $5/7 \approx 0.714$ | 6.50 | **0.65** | 0.80 | **1 (Refuerzo)** |
| **$v_5$** | UniAtlántico / Colegios | 2 | 9 | $5/9 \approx 0.556$ | 4.00 | **0.40** | 0.55 | 0 (Estándar) |
| **$v_6$** | Retorno Sur 51B | 1 | 13 | $5/13 \approx 0.385$ | 0.00 | **0.00** | 0.15 | 0 (Estándar) |

---

## 4. Modelo en Inteligencia Artificial: Perceptrón de Rosenblatt (1958)

- **Vector de entrada:** $\mathbf{x} = [x_1, x_2]^T = [C_B', \text{Afluencia}_{norm}]^T \in [0, 1]^2$.
- **Combinación lineal y función Heaviside:**
  $$z = \mathbf{w}^T \mathbf{x} + b = w_1 x_1 + w_2 x_2 + b, \qquad \phi(z) = \begin{cases} 1, & \text{si } z \ge 0 \\ 0, & \text{si } z < 0 \end{cases}$$
- **Parámetros de aprendizaje:** $\eta = 0.5$, $\mathbf{w}^{(0)} = [0.0, 0.0]^T$, $b^{(0)} = -1.0$ (umbral de inercia operativa $\theta = 1.0$).
- **Regla de actualización:**
  $$e = y - \hat{y}, \qquad \Delta \mathbf{w} = \eta \cdot e \cdot \mathbf{x}, \qquad \Delta b = \eta \cdot e$$

### Traza Aritmética de la Prueba de Escritorio (Convergencia en Época 2 con Error = 0)

#### ÉPOCA 1 (Inicialización: $\mathbf{w} = [0, 0]^T$, $b = -1.0$)
| Muestra | $\mathbf{x} = [x_1, x_2]$ | $y$ | $z$ | $\hat{y}$ | $e$ | $\Delta \mathbf{w}$ | $\mathbf{w}_{\text{nuevo}}$ | $b_{\text{nuevo}}$ |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **$p_1$** ($v_1$) | $[0.05, 0.20]$ | 0 | $-1.000$ | 0 | 0 | $[0.000, 0.000]$ | $[0.000, 0.000]$ | $-1.000$ |
| **$p_2$** ($v_2$) | $[0.15, 0.70]$ | 0 | $-1.000$ | 0 | 0 | $[0.000, 0.000]$ | $[0.000, 0.000]$ | $-1.000$ |
| **$p_3$** ($v_3$) | $[0.15, 0.95]$ | 1 | $-1.000$ | 0 | **+1** | $[+0.075, +0.475]$ | $[0.075, 0.475]$ | $-0.500$ |
| **$p_4$** ($v_4$) | $[0.65, 0.80]$ | 1 | $-0.071$ | 0 | **+1** | $[+0.325, +0.400]$ | $[0.400, 0.875]$ | $0.000$ |
| **$p_5$** ($v_5$) | $[0.40, 0.55]$ | 0 | $+0.641$ | 1 | **-1** | $[-0.200, -0.275]$ | $[0.200, 0.600]$ | $-0.500$ |
| **$p_6$** ($v_6$) | $[0.00, 0.15]$ | 0 | $-0.410$ | 0 | 0 | $[0.000, 0.000]$ | $[0.200, 0.600]$ | $-0.500$ |

*Fin de Época 1:* $\mathbf{w} = [0.200, 0.600]^T$, $b = -0.500$, Error Acumulado = 3.

#### ÉPOCA 2 (Pesos de entrada: $\mathbf{w} = [0.200, 0.600]^T$, $b = -0.500$)
| Muestra | $\mathbf{x} = [x_1, x_2]$ | $y$ | $z = 0.2x_1 + 0.6x_2 - 0.5$ | $\hat{y}$ | $e$ | $\Delta \mathbf{w}$ | $\mathbf{w}$ | $b$ |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **$p_1$** ($v_1$) | $[0.05, 0.20]$ | 0 | $-0.370$ | 0 | 0 | $[0.000, 0.000]$ | $[0.200, 0.600]$ | $-0.500$ |
| **$p_2$** ($v_2$) | $[0.15, 0.70]$ | 0 | $-0.050$ | 0 | 0 | $[0.000, 0.000]$ | $[0.200, 0.600]$ | $-0.500$ |
| **$p_3$** ($v_3$) | $[0.15, 0.95]$ | 1 | $+0.100$ | 1 | 0 | $[0.000, 0.000]$ | $[0.200, 0.600]$ | $-0.500$ |
| **$p_4$** ($v_4$) | $[0.65, 0.80]$ | 1 | $+0.110$ | 1 | 0 | $[0.000, 0.000]$ | $[0.200, 0.600]$ | $-0.500$ |
| **$p_5$** ($v_5$) | $[0.40, 0.55]$ | 0 | $-0.090$ | 0 | 0 | $[0.000, 0.000]$ | $[0.200, 0.600]$ | $-0.500$ |
| **$p_6$** ($v_6$) | $[0.00, 0.15]$ | 0 | $-0.410$ | 0 | 0 | $[0.000, 0.000]$ | $[0.200, 0.600]$ | $-0.500$ |

*Fin de Época 2:* **Error Total = 0. CONVERGENCIA EXACTA ALCANZADA.**

### Frontera de Decisión Lineal:
$$0.2000 \cdot C_B' + 0.6000 \cdot \text{Afluencia} - 0.5000 = 0 \implies \text{Afluencia} = -0.3333 \cdot C_B' + 0.8333$$

---

## 5. Red Escalada del Corredor (15 Nodos) y Simulación de Cierres

La red se extiende a **15 paraderos reales** a lo largo de la Carrera 51B:
1. `P01`: Circunvalar / Cra 51B (Acceso Sur)
2. `P02`: Makro / Transversal 51B
3. `P03`: Le Champ / Villa Santos
4. `P04`: Clínica Portoazul
5. `P05`: Colegio San José
6. `P06`: UniNorte Puerta 2
7. `P07`: UniNorte Puerta 4 (Puente Peatonal)
8. `P08`: Colegio Británico
9. `P09`: Universidad Libre (Sede Norte)
10. `P10`: Universidad Antonio Nariño (UAN)
11. `P11`: Club Campestre
12. `P12`: Universidad del Atlántico (Km 7)
13. `P13`: Villa Campestre Bulevar
14. `P14`: Corredor Caujaral
15. `P15`: Entrada Puerto Colombia / La Playa

### Efecto Topológico del Cierre Vial en La Gran Vía:
Al cerrar la arista $(P04, P07)$ por obras civiles:
- **Colegio San José ($P05$)** experimenta un salto en intermediación de $C_B' = 0.016$ a $\mathbf{0.240}$ ($+0.224$).
- **Le Champ ($P03$)** asciende de $0.022$ a $0.090$ ($+0.068$).
- El clasificador detecta la saturación y reasigna los buses de refuerzo a los nuevos cuellos de botella viales.

---

## 6. Métricas de Impacto Operativo (Antes vs. Después)

| Métrica de Evaluación (Rúbrica) | Despacho Estático Tradicional | Despacho Dinámico con IA Discreta | Impacto Logrado |
| :--- | :---: | :---: | :---: |
| **Tiempo Medio de Espera en Paradero** | 28.8 minutos | **12.4 minutos** | **$\downarrow$ 56.8%** |
| **Tiempo de Espera en UniNorte P4** | 44.5 minutos | **14.8 minutos** | **$\downarrow$ 66.7%** |
| **Tasa de Rechazo por Sobrecupo** | 39.4% | **20.3%** | **$\downarrow$ 48.6%** |
| **Rechazo en Paraderos Críticos ($y=1$)** | 68.2% | **15.5%** | **$\downarrow$ 77.3%** |
| **Pasajeros Protegidos (10h Pico)** | Base saturada | **+2,526 usuarios abordando** | **Alivio crítico** |
| **Horas-Hombre de Espera Ahorradas** | 0 h | **3,394.5 horas-pasajero** | **Productividad urbana** |

---

## 7. Instrucciones de Instalación y Ejecución

### 7.1. Requisitos del Sistema
- Python 3.10 o superior.
- Instalar dependencias científicas estándar:
  ```bash
  pip install numpy matplotlib
  ```

### 7.2. Ejecución Unificada con un Solo Comando
Para ejecutar la verificación matemática completa por consola, exportar los gráficos de alta resolución y levantar la aplicación web académica interactiva:
```bash
python main.py
```
*El sistema imprimirá las tablas en la terminal, exportará las figuras en fondo blanco (`grafo_red_51b.png` y `frontera_decision_perceptron.png`) e iniciará automáticamente el servidor HTTP abriendo `http://localhost:8000` en el navegador.*

#### Navegación en la Interfaz Web Académica:
- **Pestaña 1 (Mapa del Corredor - 15 paradas):** Mapa interactivo en modo claro con Leaflet.js (`CartoDB Positron`), con selector para simular el cierre de obras en La Gran Vía, recálculo dinámico de intermediación $C_B'$ y popups limpios.
- **Pestaña 2 (Cálculo de Centralidades - Caso 6 nodos):** Lienzo SVG interactivo con exploración de capas BFS al hacer clic sobre cualquier nodo, tabla maestra con fracciones y decimales en KaTeX, desglose de los 8 caminos empatados ($0.50$) e identidad de conservación de Freeman demostrada.
- **Pestaña 3 (Entrenamiento del Perceptrón):** Ajuste de hiperparámetros ($\eta, b^{(0)}, w_1^{(0)}, w_2^{(0)}$) mediante controles deslizantes, visualización de la frontera de decisión en Canvas/Chart.js y traza aritmética paso a paso por época.
- **Pestaña 4 (Simulación de Impacto):** Comparativa del modelo dinámico frente a la operación tradicional estática, evaluando tiempos de espera, tasas de sobrecupo y horas-hombre ahorradas.

---

## 8. Estructura de Archivos del Repositorio

```
Proyecto-Final-MDiscretas/
│
├── motor_discreto.py                 # Núcleo matemático puro (BFS, Freeman, Perceptrón y Simulación)
├── main.py                           # Punto de entrada unificado (Consola + Servidor web en localhost:8000)
├── web/                              # Aplicación web editorial académica (Light Theme)
│   ├── index.html                    # Estructura limpia y semántica (KaTeX, Leaflet, Chart.js)
│   ├── styles.css                    # Estilos modo claro inspirados en Distill.pub / Observable
│   └── app.js                        # Lógica interactiva cliente y renderizado dinámico
├── README.md                         # Documentación técnica exhaustiva del proyecto
├── informe.tex                       # Artículo científico formal en LaTeX (normas académicas)
├── presentacion.tex                  # Presentación Beamer en LaTeX (11 diapositivas)
├── diapositivas_guion.md             # Guión de sustentación oral (10 minutos / 3 estudiantes)
├── grafo_red_51b.png                 # Gráfico exportado: Topología diamante Cra 51B (Fondo blanco)
└── frontera_decision_perceptron.png  # Gráfico exportado: Espacio R^2 y Frontera Lineal (Fondo blanco)
```

---

## 9. Créditos Académicos

- **Asignatura:** Matemáticas Discretas (MAT 4021)
- **Docente:** MSc. Iván Gómez
- **Institución:** Universidad del Norte, Barranquilla, Colombia
- **Año:** 2026
