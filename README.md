# PINN — Solução da Equação de Difusão de Calor com Redes Neurais

Projeto de Iniciação Científica (PIBIC) desenvolvido na **UNESP — Campus Presidente Prudente**.

**Aluno:** Henrique Dantas Quintana  
**Orientadora:** Analice Costacurta Brandi

---

## Sobre o projeto

Este repositório implementa uma **Rede Neural Informada pela Física (PINN — Physics-Informed Neural Network)** para resolver a equação de difusão de calor unidimensional:

$$\frac{\partial T}{\partial t} = \alpha \frac{\partial^2 T}{\partial x^2}$$

onde `T` é a temperatura, `x` é a posição ao longo de uma barra de comprimento `L = 1 m` e `α` é o coeficiente de difusividade térmica.

**Condições de contorno e inicial:**
- `T(x, 0) = 0 °C` (temperatura inicial)
- `T(0, t) = 0 °C` (extremidade esquerda)
- `T(1, t) = 100 °C` (extremidade direita)

A solução analítica exata é obtida via separação de variáveis e série de Fourier em senos:

$$T(x, t) = 100x + \sum_{n=1}^{\infty} (-1)^n \frac{200}{n\pi} \sin(n\pi x)\, e^{-\alpha (n\pi)^2 t}$$

---

## Estrutura do repositório

```
.
├── IC.py           # Script principal (treinamento da PINN)
├── IC.ipynb        # Notebook com o mesmo código (Google Colab)
└── README.md
```

---

## Metodologia

A PINN é treinada com dois termos de custo simultâneos:

| Componente | Descrição |
|---|---|
| **MSE dados** | Erro quadrático médio entre a predição da rede e os pontos da solução analítica |
| **MSE físico** | Resíduo da EDP: penaliza a rede quando `α · ∂²T/∂x²` não é satisfeito |

A função de perda total é `loss = MSE_dados + k · MSE_físico`, otimizada com o algoritmo **Adam**.

### Arquitetura da rede

```
Entrada (1) → Dense(32, tanh) → Dense(32, tanh) → Saída (1, linear)
```

### Hiperparâmetros

| Parâmetro | Valor |
|---|---|
| Taxa de aprendizado | 0.0005 |
| Épocas | 30 000 |
| Pontos de treino (dados) | 10 |
| Pontos de collocação (EDP) | 20 |
| Semente aleatória | 5 |

---

## Resultados

A rede converge satisfatoriamente a partir de ~15 000 épocas. Os resultados foram validados para dois cenários:

- `α = 1.304348`, `t = 0.01`
- `α = 1.412294`, `t = 0.11`

O erro absoluto máximo observado foi de **0.12**, compatível com uma aproximação de primeira ordem, demonstrando que PINNs são uma alternativa viável a métodos numéricos tradicionais para EDPs parabólicas.

---

## Como executar

### Pré-requisitos

```bash
pip install tensorflow numpy matplotlib
```

### Execução local

```bash
python IC.py
```

### Google Colab

Abra `IC.ipynb` diretamente no [Google Colab](https://colab.research.google.com/) para aproveitar GPU gratuita.

---

## Referências

- Raissi, M., Perdikaris, P., & Karniadakis, G. E. (2019). Physics-informed neural networks. *Journal of Computational Physics*, 378, 686–707.
- Faceli, K. et al. *Inteligência Artificial: uma abordagem de aprendizado de máquina*. LTC, 2011.
- Fortuna, A. O. *Técnicas computacionais para dinâmica dos fluidos*. EDUSP, 2012.
- Mohri, M., Rostamizadeh, A., & Talwalkar, A. *Foundations of Machine Learning*. MIT Press, 2012.

---

## Agradecimentos

Agradecemos ao **PIBIC/Reitoria** pelo auxílio financeiro no desenvolvimento deste projeto.
