# Ponderada_sala01_IN04
Nome: Vinícius Rangel Marques dos Santos
Turma: T18

# Circuito RC / Prática: Carga e Descarga de um Capacitor

Este repositório documenta a prática de laboratório para o estudo do comportamento de um circuito RC (Resistor-Capacitor).  
O experimento analisa a variação da tensão no capacitor (VC) e no resistor (VR) durante o processo de carga, utilizando um Arduino Uno para leitura e Python para visualização.

---

## 1. Componentes do Circuito e Constante de Tempo

O circuito consiste em um capacitor de 10 µF e resistores em série.  
A constante de tempo (τ) é crucial para definir a velocidade de resposta do circuito.

### Componentes Utilizados

| Componente | Valor Nominal | Função no Circuito |
|-------------|----------------|--------------------|
| Capacitor (C) | 10 µF / 25 V | Armazena e libera energia elétrica (o foco do experimento). |
| Resistor de Carga (Re) | 1 MΩ | Controla a velocidade de carga do capacitor. |
| Resistor de Descarga (Rd) | 100 Ω | Controla a velocidade de descarga (limita a corrente). |
| Arduino Uno | N/A | Fornece 5 V e lê a tensão no capacitor (via entrada analógica A0). |
| Constante de Tempo (τcarga) | Re × C = 10 s | Tempo de resposta para a fase de carga. |

---

## 2. Diagramas e Imagens

### 2.1. Simulação / Diagrama Elétrico (Tinkercad)
*(Insira aqui o print do Tinkercad ou do diagrama do circuito - Exemplo: image_1add97.png ou image_1a1281.png)*

### 2.2. Protótipo Físico (Opcional)
*(Insira aqui a foto do protótipo físico montado na protoboard)*

---

## 3. Código Arduino (.ino)

O código realiza a leitura da tensão no pino A0, que corresponde à queda de tensão no resistor (VR), e calcula a tensão no capacitor (VC) pela Lei de Kirchhoff (VC = 5.0V - VR).

```cpp
int pinoNoRC = 0; // Pino Analógico A0
int valorLido = 0;
float tensaoCapacitor = 0, tensaoResistor;
unsigned long time;

void setup() {
  Serial.begin(9600);
}

void loop() {
  time = millis();
  valorLido = analogRead(pinoNoRC);

  // Converte o valor digital (0-1023) para tensão analógica (0.0-5.0V)
  tensaoResistor = (valorLido * 5.0 / 1023);

  // Calcula VC: VC = V_Fonte - VR
  tensaoCapacitor = abs(5.0 - tensaoResistor);

  Serial.print(time);
  Serial.print(" ");
  Serial.print(tensaoResistor);
  Serial.print(" ");
  Serial.println(tensaoCapacitor);

  delay(400); // Intervalo de amostragem
}
```

## 4. Resultados do Monitor Serial (Dados Brutos)

Os dados capturados mostram a fase final da carga lenta do capacitor, onde a tensão VC aumenta em direção a 5.0 V e a tensão VR (corrente) cai a zero.

Tempo (ms) | VR (V) | VC (V)
-----------------------------
36         | 0.12   | 4.88
41838      | 0.12   | 4.88
42241      | 0.11   | 4.89
42643      | 0.11   | 4.89
43045      | 0.10   | 4.90
43448      | 0.10   | 4.90
43849      | 0.09   | 4.91
...
64368      | 0.01   | 4.99
64771      | 0.01   | 4.99

##5. Visualização dos Dados (Python)
### 5.1. Código de Plotagem Python

O script abaixo utiliza os dados reais do Monitor Serial para gerar os gráficos solicitados:
Carga no C, Tensão no R e a Comparação.

```
import numpy as np
import matplotlib.pyplot as plt
from io import StringIO

# --- Dados REAIS do Monitor Serial (Formato String) ---
dados_serial = """
36 0.12 4.88
41838 0.12 4.88
42241 0.11 4.89
42643 0.11 4.89
43045 0.10 4.90
43448 0.10 4.90
43849 0.09 4.91
44252 0.09 4.91
44654 0.09 4.91
45057 0.08 4.92
45459 0.08 4.92
45861 0.08 4.92
46264 0.07 4.93
46666 0.07 4.93
47069 0.07 4.93
47471 0.07 4.93
47873 0.06 4.94
48275 0.06 4.94
48677 0.06 4.94
49080 0.06 4.94
49482 0.05 4.95
49885 0.05 4.95
50287 0.05 4.95
50690 0.05 4.95
51092 0.04 4.96
51494 0.04 4.96
51896 0.04 4.96
52298 0.04 4.96
52701 0.04 4.96
53103 0.04 4.96
53506 0.03 4.97
53908 0.03 4.97
54310 0.03 4.97
54713 0.03 4.97
55115 0.03 4.97
55518 0.03 4.97
55919 0.03 4.97
56322 0.03 4.97
56724 0.02 4.98
57126 0.02 4.98
57529 0.02 4.98
57931 0.02 4.98
58334 0.02 4.98
58736 0.02 4.98
59139 0.02 4.98
59541 0.02 4.98
59942 0.02 4.98
60345 0.02 4.98
60747 0.02 4.98
61150 0.02 4.98
61552 0.01 4.99
61955 0.01 4.99
62357 0.01 4.99
62759 0.01 4.99
63162 0.01 4.99
63563 0.01 4.99
63966 0.01 4.99
64368 0.01 4.99
64771 0.01 4.99
"""

# Carrega e processa os dados
data = np.loadtxt(StringIO(dados_serial))
tempo_ms_bruto = data[:, 0]
VR = data[:, 1]
VC = data[:, 2]
tempo_ms = tempo_ms_bruto - tempo_ms_bruto[0]  # Normaliza o tempo para t=0

plt.style.use('seaborn-v0_8-whitegrid')

# 1. Gráfico da Carga no C (VC)
plt.figure(figsize=(7, 4))
plt.plot(tempo_ms, VC, color='blue', linewidth=2)
plt.title('1. Tensão de Carga no Capacitor (VC)')
plt.xlabel('Tempo (ms)')
plt.ylabel('Tensão (V)')
plt.ylim(min(VC) - 0.01, 5.05)
plt.show()

# 2. Gráfico da Queda de Tensão no R (VR)
plt.figure(figsize=(7, 4))
plt.plot(tempo_ms, VR, color='red', linewidth=2)
plt.title('2. Tensão no Resistor (VR) durante a Carga')
plt.xlabel('Tempo (ms)')
plt.ylabel('Tensão (V)')
plt.ylim(-0.01, max(VR) + 0.01)
plt.show()

# 3. Gráfico de Comparação
plt.figure(figsize=(10, 6))
plt.plot(tempo_ms, VC, color='blue', linewidth=2, label='Tensão no Capacitor (VC)')
plt.plot(tempo_ms, VR, color='red', linewidth=2, label='Tensão no Resistor (VR)')
plt.title('3. Comparação Carga RC - VC e VR')
plt.xlabel('Tempo (ms)')
plt.ylabel('Tensão (V)')
plt.legend(loc='center right')
plt.show()
```

### 5.2. Prints dos Gráficos Gerados

Gráfico 1: Tensão de Carga no Capacitor (VC)
(Insira aqui o print do gráfico 1 - Curva azul ascendente que se estabiliza em 5V)

Gráfico 2: Tensão no Resistor (VR) durante a Carga
(Insira aqui o print do gráfico 2 - Curva vermelha descendente que se aproxima de 0V)

Gráfico 3: Comparação Carga RC - VC e VR
(Insira aqui o print do gráfico 3 - Comparação VC vs VR)

## 6. Análise Final

Os gráficos gerados com os dados reais mostram a fase final de carga.
A tensão no capacitor (VC) se aproxima da tensão de alimentação (5V), enquanto a tensão no resistor (VR), que é proporcional à corrente, decai a zero.
Esse comportamento está de acordo com a teoria do circuito RC.
