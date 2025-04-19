# FIAP - Inteligência Artificial
Fase 2 (Cap 1 - Um Mapa do Tesouro)

## FarmTech Solutions - Database


## 📌 Objetivo
O objetivo deste projeto é desenvolver um sistema de armazenamento e análise dos dados coletados por sensores utilizados na agricultura de precisão. O sistema deve ser capaz de armazenar e processar informações em tempo real, geradas por sensores de umidade, pH e nutrientes (nitrogênio, fósforo e potássio), aplicados em plantações agrícolas para otimizar a irrigação e a aplicação de nutrientes.

A partir desses dados, o sistema irá:

- **Ajustar a irrigação:** com base nas informações sobre a umidade do solo, o sistema sugerirá a quantidade de água a ser aplicada na plantação.
- **Ajustar a aplicação de nutrientes:** com base nas leituras de pH, umidade e nutrientes (NPK), o sistema recomendará a quantidade de fertilizantes e/ou ajuste necessários para otimizar a produção agrícola.
- **Prever necessidades futuras:** utilizando os dados históricos, o sistema será capaz, por exemplo, de prever as necessidades futuras de água e nutrientes para as culturas, otimizando os recursos e melhorando a produtividade.

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

### SENSOR_UMIDADE
- **Localização** (localização do sensor dentro da plantação)

### SENSOR_PH
- **Localização** (localização do sensor dentro da plantação)

### SENSOR_NPK
- **Localização** (localização do sensor dentro da plantação)

### REFERENCIA_NPK
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

### COLETA_UMIDADE
- **Valor da medição** (Número decimal conforme o tipo. ex: 40.5%)  
  - `Umidade` (%): 0 a 100
- **Data e hora** (Quando a medição foi feita)

### COLETA_PH
- **Valor da medição** (Número decimal conforme o tipo. ex: 6.5)  
  - `pH`: 0 a 14
- **Data e hora** (Quando a medição foi feita)

### COLETA_NPK
- **Nitrogênio** (Número decimal conforme o tipo. ex: 26.5 mg/kg) 
- **Fósforo** (Número decimal conforme o tipo. ex: 26.5 mg/kg)
- **Potássio** (Número decimal conforme o tipo. ex: 26.5 mg/kg) 
- **Data e hora** (Quando a medição foi feita)

### RECOMENDACAO
- **Tipo de Recomendação** (`Irrigação`, `Fertilizante`, `Ajuste de pH`)
- **Descrição** (ex: "Iniciar irrigação no Setor A da plantação de soja, devido à umidade atual de 28%.")
- **Status** (`Pendente`, `Aplicada`, `Cancelada`)
- **Data e Hora**

### APLICACAO
- **Tipo de Recomendação** (`Irrigação`, `Fertilizante_N`, `Fertilizante_P`,`Fertilizante_K`,`Ajuste de pH`)
- **Quantidade** (Quantidade aplicada)
- **Unidade** (Unidade de medida da aplicação)
- **Data e Hora INÍCIO**
- **Data e Hora FIM**
- **Eficácia** (`Baixa`, `Média`, `Alta`)

### PREVISAO
- **Tipo de Recomendação** (`Umidade`, `PH`, `Nitrogênio`,`Fósforo`,`Potássio`)
- **Valor previsto** (Quantidade aplicada)
- **Data da previsão** (Data e hora da previsão)
- **Data prevista** (Data em que o valor previsto se aplica)
- **Acurácia** (Número decimal que representa o nível de eficácia após aplicação, com base na resposta observada. ex: 95.75 - representando 95,75% de acurácia na previsão)

---

## 🔗 Relacionamentos (Cardinalidades)
| Entidade 1         | Relacionamento      | Entidade 2           | Cardinalidade | Observações                                                                 |
|---------------------|---------------------|----------------------|---------------|-----------------------------------------------------------------------------|
| FAZENDA             | possui              | PLANTACAO            | 1:N           | Uma fazenda pode ter várias plantações                                     |
| PLANTACAO           | tem                 | CULTURA              | N:1           | Cada plantação tem uma cultura, mas uma cultura pode estar em várias plantações |
| SENSOR_UMIDADE      | monitora            | PLANTACAO            | N:1           | Vários sensores de umidade podem monitorar uma plantação                   |
| SENSOR_NPK          | monitora            | PLANTACAO            | N:1           | Vários sensores NPK podem monitorar uma plantação                          |
| SENSOR_PH           | monitora            | PLANTACAO            | N:1           | Vários sensores de pH podem monitorar uma plantação                        |
| SENSOR_UMIDADE      | realiza             | COLETA_UMIDADE       | 1:N           | Um sensor de umidade pode realizar várias coletas                          |
| SENSOR_NPK          | realiza             | COLETA_NPK           | 1:N           | Um sensor NPK pode realizar várias coletas                                 |
| SENSOR_PH           | realiza             | COLETA_PH            | 1:N           | Um sensor de pH pode realizar várias coletas                               |
| CULTURA             | define              | REFERENCIA_NPK       | 1:1           | Uma cultura define um referencial de NPK                                   |
| CULTURA             | define              | REFERENCIA_PH        | 1:1           | Uma cultura define um referencial de pH                                    |
| CULTURA             | define              | REFERENCIA_UMIDADE   | 1:1           | Uma cultura define um referencial de umidade                               |
| REFERENCIA_NPK      | baseia              | RECOMENDACAO         | 1:N           | Um referencial de NPK pode basear várias recomendações                     |
| REFERENCIA_PH       | baseia              | RECOMENDACAO         | 1:N           | Um referencial de pH pode basear várias recomendações                      |
| REFERENCIA_UMIDADE  | baseia              | RECOMENDACAO         | 1:N           | Um referencial de umidade pode basear várias recomendações                 |
| COLETA_UMIDADE      | gera                | RECOMENDACAO         | 1:N           | Uma coleta de umidade pode gerar várias recomendações                      |
| COLETA_PH           | gera                | RECOMENDACAO         | 1:N           | Uma coleta de pH pode gerar várias recomendações                           |
| COLETA_NPK          | gera                | RECOMENDACAO         | 1:N           | Uma coleta de NPK pode gerar várias recomendações                          |
| RECOMENDACAO        | aplica-se em        | PLANTACAO            | N:1           | Várias recomendações podem ser aplicadas em uma plantação                 |
| RECOMENDACAO        | resulta em          | APLICACAO            | 1:1           | Cada recomendação pode resultar em uma aplicação (ação aplicada)          |
| APLICACAO           | atua em             | PLANTACAO            | N:1           | Várias aplicações podem atuar em uma plantação                  |
| PREVISAO            | prevê para          | PLANTACAO            | N:1           | Várias previsões podem ser feitas para uma mesma plantação |

---

# 📜 Regras de Negócio

## 1. Monitoramento Contínuo 

Há necessidade de monitoramento contínuo por tipo de cultura.

#### 📊 Dados Necessários

- CULTURA

- PLANTACAO (relaciona cultura e sensores)

- SENSOR_UMIDADE, SENSOR_PH, SENSOR_NPK

- COLETA_UMIDADE, COLETA_PH, COLETA_NPK

#### 💡 Como funciona

> Cada cultura está associada a uma ou mais plantações, e cada plantação é monitorada por sensores. As medições coletadas são armazenadas com data e hora, permitindo o monitoramento contínuo.

<br>

## 2. Recebimento de dados de sensores 

O sistema deve receber dados de sensores específicos, em tempo real.

#### 📊 Dados Necessários

- SENSOR_UMIDADE, SENSOR_PH, SENSOR_NPK

- COLETA_UMIDADE, COLETA_PH, COLETA_NPK (com data_hora)

- Relacionamento entre sensores e plantações

#### 💡 Como funciona

> A estrutura COLETA_* com campo data_hora permite armazenar dados por sensor específico no momento da coleta, possibilitando o processamento em tempo real.

<br>

## 3. Geração de recomendações

O sistema deve ser capaz de interpretar dados e gerar recomendações com base neles.

#### 📊 Dados Necessários

- COLETA_*

- REFERENCIA_* (valores ideais por cultura)

- RECOMENDACAO (ligação entre coleta, referência e plantação)

#### 💡 Como funciona

> A tabela RECOMENDACAO cruza as coletas com referências ideais (por cultura) para sugerir ações como irrigação, fertilização e correção de pH.

<br>

## 4. Análise preditiva 

O sistema deve utilizar análise preditiva para otimização de recursos.

#### 📊 Dados Necessários

- PREVISAO

- PLANTACAO, COLETA_*, RECOMENDACAO

- Campos: tipo, valor_previsto, data_previsao, acuracia

#### 💡 Como funciona

> A tabela PREVISAO armazena valores previstos por tipo de variável (umidade, pH, nutrientes), com acurácia e datas. Isso permite planejar ações futuras de forma otimizada.

<br>

## 5. Exemplo de Implementação de Regras de Aplicação:

### 🌱 Umidade do Solo
A umidade ideal varia por cultura. Ações são recomendadas conforme a cultura associada à plantação.

| Cultura | Umidade Ideal (% da Capacidade de Campo) | Ação Recomendável |
|---------|------------------------------------------|-------------------|
| Soja    | 45% - 65%                                | ⚠️ Aumentar irrigação se < 30%  |
| Milho   | 50% - 70%                                | ⚠️ Aumentar irrigação se < 30% |

---

### 🧪 pH do Solo
O ph ideal varia por cultura. Ações são recomendadas conforme a cultura associada à plantação.

| Cultura | Faixa Ideal de pH | Ação Recomendável |
|---------|-------------------|-------------------|
| Soja    | 5.5 - 6.5         | ⚠️ Aplicar calcário se pH < 5.5 |
| Milho   | 5.5 - 7.0         | ⚠️ Aplicar calcário se pH < 5.5 |


---

### 🌿 Nutrientes (NPK)
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


<br>

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

