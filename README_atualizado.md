# Pipeline de Dados — ETL com base real

## 1. Sobre o projeto
Este projeto implementa um MVP de pipeline de dados usando Python e Pandas. O fluxo contempla extração, transformação, validação e carga (ETL) com dados reais de transações de comércio eletrônico.

**Importante:** o MVP executa no Google Colab/Jupyter e grava arquivos CSV/JSON no ambiente de execução. Ainda não constitui uma implantação efetiva em nuvem.

## 2. Base de dados
Foi adotada a base **Online Retail**, do UCI Machine Learning Repository (Chen, 2015). O conjunto reúne 541.909 registros de transações de uma varejista on-line do Reino Unido, entre 01/12/2010 e 09/12/2011. A base contém identificador de fatura, código e descrição do produto, quantidade, data da fatura, preço unitário, identificador do cliente e país.

- Página oficial: https://archive.ics.uci.edu/dataset/352/online+retail
- DOI: https://doi.org/10.24432/C5BW33
- Licença: CC BY 4.0 (atribuição obrigatória).

## 3. Objetivos
- Obter uma base pública e real;
- Padronizar nomes de colunas e tipos;
- Remover duplicatas exatas;
- Identificar e excluir cancelamentos;
- Tratar registros sem informações essenciais ou com quantidade/preço inválidos;
- Calcular o valor bruto por linha;
- Gerar arquivo CSV tratado e relatório de qualidade JSON.

## 4. Tecnologias
- Python 3.10+
- Pandas
- ucimlrepo
- Google Colab ou Jupyter Notebook

## 5. Como o pipeline funciona
1. **Extract:** obtém a base Online Retail por meio do pacote `ucimlrepo` (identificador UCI 352).
2. **Transform:** padroniza colunas e tipos, remove duplicatas, identifica cancelamentos e aplica regras de validade.
3. **Validate:** contabiliza duplicatas, cancelamentos, registros inválidos e percentual retido.
4. **Load:** grava os dados tratados em CSV e o relatório em JSON.

## 6. Como executar no Google Colab
1. Abra o arquivo `pipeline_dados_base_real.ipynb` no Google Colab.
2. Execute as células em ordem.
3. A primeira célula instala as dependências necessárias.
4. A etapa de extração consulta o repositório UCI; portanto, é necessária conexão com a internet.
5. Ao final, serão gerados `online_retail_tratado.csv` e `relatorio_qualidade.json`.

## 7. Regras de tratamento
- Remoção de duplicatas integralmente iguais;
- Exclusão de faturas cujo identificador começa com `C` (cancelamentos);
- Exclusão de linhas sem identificador de fatura, código/descrição de produto ou data;
- Exclusão de linhas com quantidade ou preço unitário nulos, iguais a zero ou negativos;
- Cálculo de `valor_total = quantidade × valor_unitario`.

O identificador da fatura é mantido como texto, pois pode conter letras. O identificador do cliente é preservado quando disponível, mas não é obrigatório para manter a linha.

## 8. Saídas
- `data/processed/online_retail_tratado.csv`
- `outputs/relatorio_qualidade.json`

O relatório contém os registros de entrada e saída, duplicatas removidas, cancelamentos removidos, registros inválidos, percentual retido, intervalo de datas e valor bruto total.

## 9. Limitações
- Processamento em memória;
- Armazenamento em arquivos no ambiente de execução;
- Sem integração efetiva com Amazon S3, Google Cloud Storage ou Azure Blob Storage;
- Sem agendamento, monitoramento contínuo, controle de acesso ou criptografia em repouso;
- Os resultados quantitativos do tratamento são calculados durante a execução e registrados no JSON.

## 10. Referência da base
CHEN, Daqing. **Online Retail**. UCI Machine Learning Repository, 2015. DOI: https://doi.org/10.24432/C5BW33.
