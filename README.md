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

### CULTURA
- **Nome** (ex: "Soja", "Milho")

### PLANTACAO
- **Área plantada** (em hectares)
- **Data de plantio**

### SENSOR
- **Localização** (ex: "Setor A")

### REFERENCIA_NUTRIENTE
- **Nutriente** (ex: "Nitrogênio", "Fósforo", "Potássio")
- **Faixa ideal mínima** (em mg/kg de solo)
- **Faixa ideal máxima** (em mg/kg de solo)
- **Ação recomendada**  (ex: "Adubação nitrogenada se <20")

### REFERENCIA_PH
- **Ph mínimo ideal** 
- **Ph máximo ideal** 
- **Ação recomendada**  (ex: "Aplicar calcário se pH < 5.5")

### REFERENCIA_UMIDADE
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
- **Descrição** (ex: "Iniciar irrigação no Setor A da plantação de soja, devido à umidade atual de 28%.")
- **Status** (`Pendente`, `Aplicada`, `Cancelada`)
- **Data de criação**

---

## 🔗 Relacionamentos (Cardinalidades)
| Entidade 1       | Relacionamento     | Entidade 2           | Cardinalidade | Observações
|------------------|--------------------|----------------------|---------------|--------------------------------------------------------------------------------------------------------------|
| FAZENDA          | possui             | PLANTACAO            | 1:N           | Uma fazenda pode ter várias plantações                                                                       |
| PLANTACAO        | monitorada por     | SENSOR               | 1:N           | Cada plantação é monitorada por múltiplos sensores                                                           |
| PLANTACAO                   | refere-se a        | CULTURA              | N:1           | Cada plantação tem uma única cultura associada, mas uma cultura pode estar presente em várias plantações.|
| SENSOR           | realiza            | COLETA               | 1:N           | Um sensor faz várias coletas ao longo do tempo                                                               |
| COLETA           | gera             | RECOMENDACAO         | 1:N           | Uma coleta pode originar múltiplas recomendações                                                             |
| COLETA           | 	baseia-se em  | REFERENCIA_NUTRIENTE | N:1           | Opcional, apenas se for coleta de NPK. |
| COLETA           | 	baseia-se em  | REFERENCIA_PH        | N:1           | Opcional, apenas se for coleta de pH.                                            |
| COLETA           | 	baseia-se em  | REFERENCIA_UMIDADE   | N:1           | Opcional, apenas se for coleta de umidade.                                       |
| RECOMENDACAO     | aplica-se a        | PLANTACAO            | 1:N           | Uma plantação pode ter várias recomendações, mas cada recomendação só se refere a uma única plantação.       | 
| REFERENCIA_NUTRIENTE        | é definida para       | CULTURA              | N:1           | Cada referência nutricional é para uma cultura específica. |
| REFERENCIA_PH       | é definida para       | CULTURA              | N:1           | Cada referência de pH é para uma cultura específica.|
| REFERENCIA_UMIDADE      | é definida para       | CULTURA              | N:1           | Cada referência de umidade é para uma cultura específica.|

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
- Um único sensor pode realizar medições de diferentes tipos: Cada sensor pode coletar dados de múltiplos parâmetros (umidade, pH, Nitrogênio, Fósforo, Potássio, etc.), sem a necessidade de sensores especializados para cada tipo de medição.
  - A distinção entre os tipos de medições será feita através do campo Tipo de Medição na tabela COLETA.


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

