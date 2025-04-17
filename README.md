# farm-sense-db
Fase 2 (Cap 1 - Um mapa do tesouro)

## 📌 Objetivo
Este banco de dados armazena e analisa dados coletados por sensores em plantações, permitindo:
- Ajuste automatizado de irrigação e fertilização.
- Histórico de medições (umidade, pH, nutrientes).
- Geração de recomendações para otimização de recursos.

---

## 🔍 Entidades e Atributos

### FAZENDA
- **Nome** (ex: "Fazenda Girassol")
- **Área total** (em hectares)

### PLANTACAO
- **Tipo de cultura** (ex: "Soja", "Milho")
- **Área plantada** (em hectares)
- **Data de plantio**

### SENSOR
- **Tipo** (`Umidade`, `pH` ou `NPK`)
- **Localização** (ex: "Setor A")

### REFERENCIA_NUTRIENTE
- **Cultura** (ex: "Soja", "Milho")
- **Nutriente** (ex: "Nitrogênio", "Fósforo", "Potássio")
- **Faixa ideal mínima** (em mg/kg de solo)
- **Faixa ideal máxima** (em mg/kg de solo)
- **Ação recomendada**  (ex: "Adubação nitrogenada se <20")

### REFERENCIA_PH
- **Cultura** (ex: "Soja", "Milho")
- **Ph mínimo ideal** 
- **Ph máximo ideal** 
- **Ação recomendada**  (ex: "Aplicar calcário se pH < 5.5")

### REFERENCIA_UMIDADE
- **Cultura** (ex: "Soja", "Milho")
- **Umidade mínima ideal** (em % da Capacidade de Campo)
- **Umidade máxima ideal** (em % da Capacidade de Campo)
- **Ação recomendada**  (ex: "Suspender irrigação se umidade > 70%")

### COLETA
- **Tipo de medição**:  
  - `Umidade` (%): 0 a 100  
  - `pH`: 0 a 14  
  - `Nitrogênio` (N), `Fósforo` (P), `Potássio` (K) *(em mg/kg de solo)*  
- **Valor registrado** (Número decimal conforme o tipo. ex: 6.5 para pH)
- **Data e hora** (Quando a medição foi feita.)

### RECOMENDACAO
- **Tipo** (`Irrigação`, `Fertilizante`, `Ajuste de pH`)
- **Descrição** (ex: "Aumentar irrigação em 20%")
- **Status** (`Pendente`, `Aplicada`, `Cancelada`)
- **Data de criação**

---

## 🔗 Relacionamentos (Cardinalidades)
| Entidade 1       | Relacionamento     | Entidade 2           | Cardinalidade | Observações
|------------------|--------------------|----------------------|---------------|--------------------------------------------------------------------------------------------------------------|
| FAZENDA          | possui             | PLANTACAO            | 1:N           | Uma fazenda pode ter várias plantações                                                                       |
| PLANTACAO        | monitorada por     | SENSOR               | 1:N           | Cada plantação é monitorada por múltiplos sensores                                                           |
| SENSOR           | realiza            | COLETA               | 1:N           | Um sensor faz várias coletas ao longo do tempo                                                               |
| COLETA           | baseia             | RECOMENDACAO         | 1:N           | Uma coleta pode originar múltiplas recomendações                                                             |
| RECOMENDACAO     | aplica-se a        | PLANTACAO            | 1:N           | Uma plantação pode ter várias recomendações, mas cada recomendação só se refere a uma única plantação.       |                                                     |
| COLETA           | usa referência de  | REFERENCIA_NUTRIENTE | N:1           | Coletas de nutrientes (NPK) devem obrigatoriamente usar uma referência nutricional correspondente à cultura. |
| COLETA           | usa referência de  | REFERENCIA_PH        | N:1           | Coletas de pH podem usar uma referência, se associada à cultura.                                             |
| COLETA           | usa referência de  | REFERENCIA_UMIDADE   | N:1           | Coletas de umidade podem usar uma referência, se associada à cultura.                                        |
---

# 📜 Regras de Negócio

## 1. 🌱 Umidade do Solo
A umidade ideal varia por cultura. Ações são recomendadas conforme a cultura associada à plantação.

| Cultura | Umidade Ideal (% da Capacidade de Campo) | Ação Recomendável |
|---------|------------------------------------------|-------------------|
| Soja    | 45% - 65%                                | ⚠️ Aumentar irrigação se: < 45% em 2 coletas consecutivas ou < 30% em uma única coleta |
| Milho   | 50% - 70%                                | ⚠️ Aumentar irrigação se: < 50% em 2 coletas consecutivas ou < 30% em uma única coleta |

🔄 **Suspender irrigação se:** umidade > 70% (qualquer cultura)

---

## 2. 🧪 pH do Solo
O ph ideal varia por cultura. Ações são recomendadas conforme a cultura associada à plantação.

| Cultura | Faixa Ideal de pH | Ação Recomendável |
|---------|-------------------|-------------------|
| Soja    | 5.5 - 6.5         | ⚠️ Aplicar calcário se pH < 5.5 em 2 coletas consecutivas |
| Milho   | 5.5 - 7.0         | ⚠️ Aplicar calcário se pH < 5.5 em 2 coletas consecutivas |

⚠️ **Se pH > 7.5**, considerar medidas corretivas (alcalinidade excessiva)

---

## 3. 🌿 Nutrientes (NPK)
As faixas ideais também variam por cultura.

### Nitrogênio (N)
| Cultura | Faixa Ideal (mg/kg) | Ação Recomendável |
|---------|---------------------|-------------------|
| Soja    | > 20                | ⚠️ Adubação nitrogenada se < 15 |
| Milho   | > 30                | ⚠️ Adubação nitrogenada se < 20 |

### Fósforo (P)
| Cultura | Faixa Ideal (mg/kg) | Ação Recomendável |
|---------|---------------------|-------------------|
| Soja    | > 12                | ⚠️ Adubação fosfatada se < 8 |
| Milho   | > 15                | ⚠️ Adubação fosfatada se < 10 |

### Potássio (K)
| Cultura | Faixa Ideal (mg/kg) | Ação Recomendável |
|---------|---------------------|-------------------|
| Soja    | > 50                | ⚠️ Adubação potássica se < 35 |
| Milho   | > 60                | ⚠️ Adubação potássica se < 40 |

---

## 💡 Regras Complementares
- A ação recomendada será gerada somente quando houver referência definida para a cultura da plantação associada.
- Uma **RECOMENDAÇÃO** será criada sempre que uma **COLETA** estiver fora da faixa ideal definida para a cultura.
- Se a mesma anomalia for detectada em duas coletas consecutivas, a prioridade da recomendação será aumentada.


## 🕰️ Histórico
   - Todas as coletas são armazenadas por 5 anos para análise temporal.

---

## 🛠️ Implementação Técnica (DER) - em construção
- **Diagrama Visual**: Disponível em [`diagrama.png`](diagrama.png).
- **Arquivo de Modelagem**: [`modelo.dmd`](modelo.dmd) (SQL Developer Data Modeler).

---

## 📚 Referências

Este projeto utiliza parâmetros técnicos validados pelas seguintes fontes:


   - ALBUQUERQUE, P. E. P. de; FARIA, C. M. de; COELHO, E. A. **Utilização do software IrrigaFácil para manejo de irrigação**. Embrapa Instrumentação, 2011. Disponível em: <https://www.embrapa.br/busca-de-publicacoes/-/publicacao/919820/utilizacao-do-software-irrigafacil-para-manejo-de-irrigacao>.
     
   - BASTOS, E. A.; ANDRADE JUNIOR, A. S. de; SOUSA, V. F. de. **Manejo de irrigação**. Embrapa Meio-Norte, 2005. Disponível em: <https://www.embrapa.br/meio-norte/busca-de-publicacoes/-/publicacao/1095212/manejo-de-irrigacao>.
     
   - FAO. **Crop evapotranspiration - Guidelines for computing crop water requirements (FAO 56)**. Rome: Food and Agriculture Organization of the United Nations, 1998. Disponível em: <http://www.fao.org/3/x0490e/x0490e00.htm>.
     
   - RAIJ, B. van et al. Boletim 100: Recomendações de adubação e calagem para o Estado de São Paulo. Campinas: Instituto Agronômico (IAC), 2022. Disponível em: <[https://pt.scribd.com/document/402526033/MANUAL-DE-ADUBACAO-E-CALAGEM-pdf](https://pt.scribd.com/document/599705478/Boletim-100-Recomendac-o-es-de-Adubac-a-o-e-Calagem-para-o-Estado)>.

  
---

## 👥 Equipe
- Amanda Vieira Pires (RM565045)
- Ana Gabriela Soares Santos (RM565235)
- Bianca Nascimento de Santa Cruz Oliveira (RM561390)
- Milena Pereira dos Santos Silva (RM565464)
- Nayana Mehta Miazaki (RM565045)

