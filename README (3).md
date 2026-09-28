# Pipeline de Dados em Nuvem — MVP

## 1. Sobre o projeto

Este projeto implementa um MVP de pipeline de dados usando Python e Pandas. O objetivo é demonstrar um fluxo ETL (*Extract, Transform, Load*): ler dados de entrada, tratar e validar os registros e salvar os dados processados junto com um relatório de qualidade.

O MVP roda localmente ou no Google Colab. Nesta primeira versão, o armazenamento é feito em arquivos CSV e JSON. Isso simula a lógica de um pipeline, mas **não representa uma implantação real em nuvem**. A arquitetura pode ser ampliada para usar Amazon S3, Google Cloud Storage ou Azure Blob Storage.

## 2. Objetivos

- Ler dados de um arquivo CSV.
- Identificar e remover registros duplicados.
- Tratar campos obrigatórios vazios.
- Padronizar nomes de colunas e tipos de dados.
- Validar registros e calcular indicadores básicos de qualidade.
- Salvar os dados tratados e um relatório de execução.

## 3. Tecnologias

- Python 3.10+
- Pandas
- Google Colab ou Jupyter Notebook
- Git e GitHub

## 4. Como o pipeline funciona

1. **Extract:** lê o arquivo CSV de origem.
2. **Transform:** padroniza colunas, remove duplicatas, converte tipos e trata valores inválidos.
3. **Validate:** verifica campos obrigatórios e calcula métricas de qualidade.
4. **Load:** grava os dados tratados em `data/processed/vendas_tratadas.csv` e o relatório em `outputs/relatorio_qualidade.json`.

### Fluxo

```text
Arquivo CSV
    |
    v
Extração dos dados
    |
    v
Limpeza e transformação
    |
    v
Validação e métricas
    |
    +------> CSV processado
    |
    +------> Relatório JSON
```

## 5. Estrutura do repositório

```text
pipeline-dados-nuvem/
├── data/
│   ├── raw/                 # Dados de entrada
│   └── processed/           # Dados tratados
├── outputs/                 # Relatórios gerados
├── notebooks/
│   └── pipeline_dados.ipynb # Notebook demonstrativo
├── src/
│   └── pipeline.py          # Código reutilizável do pipeline
├── README.md
└── requirements.txt
```

## 6. Como executar

### Opção A — Google Colab

1. Abra o Google Colab.
2. Envie o arquivo `notebooks/pipeline_dados.ipynb` ou abra o notebook pelo GitHub.
3. Execute as células em ordem.
4. O notebook cria dados de exemplo caso nenhum CSV seja enviado e gera os arquivos de saída.

### Opção B — Execução local

1. Clone o repositório:

   ```bash
   git clone https://github.com/SEU-USUARIO/pipeline-dados-nuvem.git
   cd pipeline-dados-nuvem
   ```

2. Instale as dependências:

   ```bash
   pip install -r requirements.txt
   ```

3. Execute o pipeline:

   ```bash
   python src/pipeline.py
   ```

Por padrão, o script cria uma pequena base de demonstração se `data/raw/vendas.csv` não existir.

## 7. Formato esperado dos dados

O exemplo utiliza as colunas:

| Coluna | Descrição |
|---|---|
| `id_venda` | Identificador da venda |
| `data` | Data da venda |
| `produto` | Nome do produto |
| `quantidade` | Quantidade vendida |
| `valor_unitario` | Preço por unidade |

O campo `id_venda` deve estar preenchido. Quantidade e valor unitário precisam ser numéricos e não negativos. O valor total é calculado pelo pipeline.

## 8. Saídas geradas

- `data/processed/vendas_tratadas.csv`: dados limpos e transformados.
- `outputs/relatorio_qualidade.json`: resumo da execução, quantidade de registros de entrada e saída, duplicatas removidas, registros inválidos e percentual de preenchimento.

Os arquivos são recriados a cada execução.

## 9. Limitações do MVP

- O processamento é feito em memória e é adequado apenas para bases pequenas.
- O armazenamento inicial usa arquivos locais, não um serviço de nuvem.
- Não há agendamento automático, monitoramento contínuo, controle de acesso ou criptografia em repouso.
- O tratamento é definido para um conjunto simples de dados de vendas.

## 10. Próximas melhorias

- Integrar o armazenamento com Amazon S3, Google Cloud Storage ou Azure Blob Storage.
- Automatizar a execução com Apache Airflow, Prefect ou serviços nativos de nuvem.
- Criar testes automatizados.
- Adicionar logs, alertas e monitoramento.
- Armazenar os dados em um banco de dados ou data warehouse.
- Implementar controle de acesso e proteção de dados.

## 11. Uso responsável

Utilize dados fictícios ou dados para os quais você tenha autorização. Evite inserir informações pessoais ou confidenciais no repositório público.

## 12. Licença

Este projeto pode ser distribuído sob a licença MIT, caso você adicione um arquivo `LICENSE` com os termos correspondentes.
