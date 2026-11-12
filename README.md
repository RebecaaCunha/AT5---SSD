# MVP – Pipeline de Confiabilidade (SCANIA Component X)

Notebook em Python que transforma dados reais de caminhões da SCANIA (desafio IDA 2024) em curvas de confiabilidade, comparação entre especificações e um ranking de risco por veículo. É um MVP de suporte a sistemas de decisão.

## Fluxo

`Entrada (2 CSVs)` → **Bronze** (validação) → **Silver/Gold** (Kaplan-Meier, Weibull com censura, razão de taxa por especificação) → **Decisão** (risco por veículo e reparos esperados) → `saida/`

## Arquivos de entrada

| Arquivo | Colunas |
|---|---|
| `train_tte.csv` | `vehicle_id, length_of_study_time_step, in_study_repair` |
| `train_specifications.csv` | `vehicle_id, Spec_0 … Spec_7` |

Os arquivos de validação, teste e as leituras operacionais não são necessários.

## Como usar

1. Abra `pipeline_confiabilidade.ipynb` (Jupyter, VS Code ou Google Colab).
2. Rode a célula 1 (configuração) e a 1b (upload). Escolha os dois CSVs no botão que aparecer. No Jupyter, depois de escolher, siga para a célula 2, que salva os arquivos.
3. Se preferir, coloque os CSVs na pasta `entrada/` ao lado do notebook (Colab: `/content`).
4. Execute as demais células. Os resultados saem em `saida/`.

O nome dos arquivos não importa: o notebook identifica cada um pelo cabeçalho.

**Nuvem (só leitura):** defina `ENTRADA` com o caminho do bucket (`s3://...`, `gs://...`) e instale `fsspec` e o driver do provedor. Os resultados são sempre gravados localmente em `saida/`.

## Salvar no Google Drive

A última célula do notebook (seção 7) copia a pasta `saida/` para `Meu Drive/confiabilidade_scania/`:

- **Google Colab:** monta o Drive e pede a sua autorização na primeira vez.
- **Jupyter local:** preencha `DRIVE_LOCAL` com a pasta sincronizada do Google Drive para computador (ex.: `G:/Meu Drive`).
- Para desligar: `SALVAR_NO_DRIVE = False`. Os arquivos continuam sempre em `saida/`.

## O que é calculado

- **Censura:** veículos sem reparo (`in_study_repair = 0`) entram como observações censuradas, não como "nunca falham".
- **Frota:** taxa de reparo com IC 95% (Poisson exato), curva de Kaplan-Meier e Weibull por máxima verossimilhança. Resultado: 23.550 veículos, 2.272 reparos, β = 2,40 e η = 695 steps (desgaste).
- **Especificações:** razão de taxa de reparo de cada categoria de `Spec_0` a `Spec_7` contra a frota, com IC 95% e correção de Bonferroni. Resultado: 46 categorias testadas, 8 acima e 9 abaixo da frota.
- **Risco por veículo:** probabilidade de reparo nos próximos 50 time steps, condicionada ao tempo já operado, usando o Weibull da categoria da especificação que mais separa a taxa de reparo (Spec_2).

## Saídas (`saida/`)

`scania_risco_veiculos.csv` (ranking por veículo), `scania_categorias.csv`, `scania_weibull_categoria.csv`, `scania_risco_categoria.csv`, `resumo_scania.json` e 5 gráficos em `graficos/`.

## Parâmetros (célula 1)

`MIN_EVENTOS`, `MIN_VEICULOS`, `HORIZONTE_PASSOS`, `P_AJUSTADO`, `ALFA`.

## Limitações

- O componente é anonimizado, os tempos são *time steps* relativos (não horas ou dias) e há perturbações de escala nos dados: taxas absolutas não devem ser lidas como reais. A comparação entre categorias continua válida.
- Os veículos são um subconjunto que visitou oficinas da Scania (possível viés de seleção) e não há custos, então o risco é de probabilidade, não financeiro.
- O risco por veículo assume que o veículo sobreviveu até a última observação e que o Weibull da categoria vale para o futuro. Além de 510 steps (máximo observado), o Weibull é extrapolação.
- As leituras de sensores não são usadas; seriam o próximo passo para manutenção preditiva.
- Resultados são apoio à decisão e devem ser validados pela equipe de manutenção.

## Dependências

`pandas`, `numpy`, `matplotlib`, `scipy` (e `ipywidgets` para o botão de upload no Jupyter; `fsspec` para nuvem).
