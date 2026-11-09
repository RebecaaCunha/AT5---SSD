# Pipeline de Dados — Online Retail

## Visão geral
Este projeto implementa um pipeline ETL (*Extract, Transform, Load*) em Python, utilizando Pandas e a base pública **Online Retail**, disponibilizada pelo UCI Machine Learning Repository. O fluxo realiza a extração dos dados, seu tratamento, validações de qualidade e geração de arquivos para análise.

O notebook também incorpora recursos de confiabilidade e governança: testes automatizados, monitoramento do percentual de registros descartados, idempotência, hashes para identificação dos arquivos e registro de linhagem dos dados (*data lineage*).

> **Importante:** a execução ocorre no Google Colab/Jupyter. O armazenamento é feito no sistema de arquivos do ambiente ou em uma pasta do Google Drive, caso configurada. O projeto não utiliza, nesta versão, um serviço de nuvem gerenciado nem um orquestrador como Airflow.

## Fonte dos dados
- **Base:** Online Retail
- **Repositório:** UCI Machine Learning Repository
- **Identificador UCI:** 352
- **Referência:** Chen, D. (2015). *Online Retail*. https://doi.org/10.24432/C5BW33
- **Período coberto:** 1º de dezembro de 2010 a 9 de dezembro de 2011
- **Licença informada pelo repositório:** CC BY 4.0

A base contém registros de transações de uma empresa varejista on-line do Reino Unido.

## Estrutura do projeto
Durante a execução, o notebook cria a seguinte estrutura:

```text
pipeline-dados-nuvem/
├── data/
│   ├── raw/
│   │   └── online_retail_original.csv
│   └── processed/
│       └── online_retail_tratado.csv
└── outputs/
    ├── relatorio_qualidade.json
    └── data_lineage.json
```

- `data/raw`: cópia da base original, antes do tratamento.
- `data/processed`: conjunto de dados tratado, em CSV.
- `outputs`: relatório de qualidade e metadados de rastreabilidade da execução.

## Tecnologias e bibliotecas
- Python
- Pandas
- `ucimlrepo`
- `hashlib` e `json` (bibliotecas padrão do Python)
- Google Colab ou Jupyter Notebook

## Como executar
1. Abra `pipeline_dados_base_real_completo.ipynb` no Google Colab ou em um ambiente Jupyter.
2. Execute as células em ordem, do início ao fim.
3. Na primeira execução, o notebook instala as dependências necessárias e consulta a base no UCI.
4. Ao final, verifique os arquivos gerados nas pastas `data/` e `outputs/`.

### Persistência no Google Drive
Por padrão, o notebook utiliza `/content/pipeline-dados-nuvem`, que é uma área temporária do Colab. Para manter os arquivos após o encerramento da sessão, monte o Google Drive no Colab e altere `BASE_DIR` para uma pasta dentro de `/content/drive/MyDrive/`, conforme o comentário disponível na célula de preparação do ambiente.

## Etapas do pipeline

### 1. Extração
A base Online Retail é obtida diretamente do UCI Machine Learning Repository por meio de `fetch_ucirepo(id=352)`. Uma cópia da fonte é armazenada na camada `raw`.

### 2. Transformação
O tratamento inclui:
- padronização dos nomes das colunas;
- conversão dos campos para os tipos apropriados;
- remoção de duplicatas exatas;
- exclusão de faturas de cancelamento, identificadas pelo prefixo `C`;
- remoção de registros com campos obrigatórios ausentes ou inválidos;
- descarte de quantidades e preços não positivos;
- cálculo do valor total de cada linha (`quantidade × valor unitário`);
- renomeação e organização das colunas para análise.

### 3. Validação e qualidade
O notebook executa testes com uma amostra controlada e verifica condições esperadas na base tratada, como ausência de duplicatas e valores positivos para quantidade e preço. Também gera um relatório com os volumes de entrada, saída e descarte.

### 4. Carga e rastreabilidade
Os dados tratados são gravados em CSV. O relatório `data_lineage.json` registra informações da execução, versão do pipeline, origem, transformações e hashes dos dados/arquivos, permitindo relacionar a saída ao processamento que a gerou.

### 5. Monitoramento
É emitido um alerta no próprio notebook quando o percentual de registros que não chegam à saída atinge ou ultrapassa o limite configurado de 40%. Esse alerta é exibido durante a execução; não representa, nesta versão, notificação automática por e-mail ou mensagem.

### 6. Indicadores
São apresentados indicadores descritivos, como quantidade de registros tratados, faturas e produtos distintos, países presentes e valor total das vendas. O valor monetário da base é expresso em GBP.

## Funcionalidades de confiabilidade
- **Testes automatizados:** verificam regras de negócio e invariantes da saída.
- **Monitoramento:** sinaliza percentual elevado de registros descartados.
- **Idempotência:** compara o conteúdo da saída com o arquivo existente e evita regravação quando não há alteração.
- **Rastreabilidade:** registra metadados e hashes para identificar a execução e seus artefatos.
- **Organização em camadas:** separa dados brutos, tratados e relatórios.

## Limitações e evoluções futuras
A solução é executada em lote e depende da execução manual do notebook. A persistência só é durável se o diretório de saída estiver em armazenamento persistente, como o Google Drive. Não estão implementados agendamento/orquestração, alertas externos, banco de dados ou data warehouse gerenciado, controle de acesso, criptografia configurada pelo projeto ou processamento distribuído com Spark. Essas funcionalidades podem ser consideradas em versões futuras.

## Artefatos gerados
| Arquivo | Descrição |
|---|---|
| `online_retail_original.csv` | Cópia dos dados brutos |
| `online_retail_tratado.csv` | Dados após as regras de tratamento |
| `relatorio_qualidade.json` | Indicadores de qualidade e volumes |
| `data_lineage.json` | Metadados e rastreabilidade da execução |

## Referência
CHEN, D. **Online Retail**. UCI Machine Learning Repository, 2015. DOI: https://doi.org/10.24432/C5BW33.
