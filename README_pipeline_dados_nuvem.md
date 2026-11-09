# Pipeline de Dados em Nuvem — ETL com Online Retail

## 1. Visão geral

Este projeto implementa um pipeline de dados ETL (*Extract, Transform, Load*) em Python, utilizando a base pública **Online Retail**, disponibilizada pelo UCI Machine Learning Repository.

O fluxo realiza a extração dos dados, armazenamento da cópia original, tratamento e validação, testes automatizados de qualidade, carga dos dados tratados, monitoramento, rastreabilidade e persistência em nuvem por meio do Google Drive.

O projeto pode ser executado no Google Colab, com armazenamento persistente no Google Drive, ou em um ambiente Jupyter/local, utilizando o sistema de arquivos local.

## 2. Objetivo

Demonstrar a construção de um pipeline de dados reproduzível, com organização por camadas, regras de qualidade, monitoramento da execução e armazenamento persistente dos resultados.

## 3. Fonte dos dados

- **Base:** Online Retail
- **Repositório:** UCI Machine Learning Repository
- **Identificador:** 352
- **Referência:** Chen, D. (2015). *Online Retail*. UCI Machine Learning Repository.
- **DOI:** https://doi.org/10.24432/C5BW33
- **Licença informada:** CC BY 4.0

A base contém registros de transações de uma empresa varejista on-line do Reino Unido, no período de 1º de dezembro de 2010 a 9 de dezembro de 2011.

## 4. Arquitetura do pipeline

```text
UCI Machine Learning Repository
              |
              v
         EXTRAÇÃO
              |
              v
       CAMADA RAW
   Dados originais em CSV
              |
              v
   TRANSFORMAÇÃO E VALIDAÇÃO
 Padronização, conversões e filtros
              |
              v
     TESTES DE QUALIDADE
              |
              v
   CARGA E MONITORAMENTO
 CSV tratado + relatório + lineage
              |
              v
       GOOGLE DRIVE
 Armazenamento persistente em nuvem
```

## 5. Etapas implementadas

### 5.1. Extração

A base é obtida diretamente do UCI Machine Learning Repository por meio da biblioteca `ucimlrepo`.

### 5.2. Armazenamento RAW

Uma cópia dos dados originais é salva antes das transformações, permitindo auditoria e reprocessamento.

### 5.3. Transformação

O tratamento contempla:
- Padronização dos nomes das colunas;
- Conversão dos campos para os tipos adequados;
- Remoção de duplicatas exatas;
- Exclusão de faturas de cancelamento identificadas pelo prefixo `C`;
- Remoção de registros com campos obrigatórios inválidos;
- Exclusão de quantidades e preços não positivos;
- Cálculo do valor total de cada linha de venda.

### 5.4. Testes automatizados

São executadas verificações das regras de negócio em uma amostra controlada e testes de consistência sobre a base tratada. A execução é interrompida caso alguma condição obrigatória não seja atendida.

### 5.5. Carga, monitoramento e rastreabilidade

O pipeline gera:
- Arquivo CSV com os dados tratados;
- Relatório JSON de qualidade;
- Arquivo JSON de linhagem dos dados (*data lineage*);
- Hashes SHA-256 para identificação dos conteúdos;
- Indicadores de entrada, saída e percentual descartado.

O monitoramento emite um alerta quando o percentual de registros que não chegam à saída atinge ou ultrapassa 40%.

### 5.6. Persistência em nuvem

No Google Colab, o notebook monta o Google Drive e direciona o diretório principal do projeto para `MyDrive/pipeline-dados-nuvem`. Dessa forma, os arquivos são armazenados no Drive e permanecem disponíveis após o encerramento da sessão do Colab.

A etapa de verificação confere a existência dos arquivos principais no diretório configurado.

**Importante:** esta implementação utiliza o Google Drive como armazenamento persistente em nuvem. Não se trata de um data lake ou serviço de armazenamento gerenciado em uma plataforma como AWS, Azure ou Google Cloud Storage.

## 6. Estrutura de diretórios

Após a execução, a organização esperada é:

```text
pipeline-dados-nuvem/
│
├── data/
│   ├── raw/
│   │   └── online_retail_original.csv
│   │
│   └── processed/
│       └── online_retail_tratado.csv
│
└── outputs/
    ├── relatorio_qualidade.json
    └── data_lineage.json
```

No Google Colab, essa estrutura fica dentro de `Meu Drive/pipeline-dados-nuvem`.

## 7. Tecnologias utilizadas

| Tecnologia | Aplicação |
|---|---|
| Python | Linguagem de programação |
| Pandas | Manipulação e tratamento dos dados |
| ucimlrepo | Obtenção da base pública |
| Google Colab | Ambiente de execução em nuvem |
| Google Drive | Armazenamento persistente |
| JSON | Relatórios e rastreabilidade |
| SHA-256 | Verificação de integridade dos arquivos |

## 8. Como executar

### Opção 1 — Google Colab com Google Drive

1. Faça upload do notebook `pipeline_dados_nuvem_com_google_drive.ipynb` no Google Colab.
2. Execute as células na ordem apresentada.
3. Na etapa de preparação, autorize o acesso ao Google Drive quando solicitado.
4. Aguarde a extração, o tratamento e os testes automatizados.
5. Verifique a seção de persistência em nuvem para confirmar que os arquivos foram gravados.
6. Consulte os arquivos na pasta `Meu Drive/pipeline-dados-nuvem`.

A variável `USE_GOOGLE_DRIVE` deve permanecer como `True` para utilizar o Drive no Colab.

### Opção 2 — Jupyter ou ambiente local

Execute o notebook em um ambiente com Python e instale as dependências:

```bash
pip install ucimlrepo openpyxl pandas
```

Fora do Google Colab, o notebook utiliza a pasta local `pipeline-dados-nuvem`. Nesse modo, os arquivos não são enviados automaticamente para um serviço de nuvem.

## 9. Arquivos gerados

| Arquivo | Descrição |
|---|---|
| `online_retail_original.csv` | Cópia original dos dados |
| `online_retail_tratado.csv` | Base após transformação e validação |
| `relatorio_qualidade.json` | Indicadores e status da qualidade |
| `data_lineage.json` | Origem, transformações, hashes e ambiente de execução |

## 10. Limitações e possibilidades de evolução

A solução utiliza o Google Drive para persistência, mas ainda não contempla:
- Orquestração automática com Apache Airflow;
- Armazenamento em banco de dados gerenciado;
- Alertas automáticos por e-mail;
- Controle de acesso por perfis;
- Processamento distribuído;
- Execução agendada independente do Colab.

Esses recursos podem ser incorporados em versões futuras, conforme a necessidade de escalabilidade e automação do projeto.

## 11. Resultado esperado

Ao final da execução, espera-se obter uma base de vendas tratada e validada, acompanhada de relatórios de qualidade e rastreabilidade. Com a integração ao Google Drive, os arquivos passam a ter persistência além da sessão de processamento, completando a etapa de armazenamento em nuvem proposta para o projeto.
