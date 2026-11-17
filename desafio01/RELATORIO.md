# Desafio 1: relatório

**Aluno(a):** Gabriel

## 1. Versões dos dados

Rode o comando abaixo e complete a tabela com os 8 primeiros caracteres de cada md5:

```bash
for tag in dados-v1 dados-v2 dados-v3; do
  echo "$tag $(git show $tag:data/raw/bike.csv.dvc | awk '/md5/{print $3}')"
done
```

| Tag | md5 (8 primeiros caracteres) | Período |
|---|---|---|
| `dados-v1` | `fc7d423a` | 01/01/2011 a 31/12/2011 |
| `dados-v2` | `11e40c60` | 01/01/2011 a 30/06/2012 |
| `dados-v3` | `7747b2ef` | 01/01/2011 a 31/12/2012 |

## 2. Comparação no mesmo teste

Copie os números de `reports/comparacao.json`.

**Período do teste:** 04/12/2012 a 31/12/2012 (670 linhas)

| Alias | Versão | RMSE | MAE | R² |
|---|---|---|---|---|
| `@champion` | 5 | 91,88 | 63,96 | 0,675 |
| `@challenger` | 7 | 64,60 | 43,85 | 0,839 |

## 3. Decisão

**Promovido ou rejeitado?** Promovido. O `@champion` passou da versão 5 para a versão 7 (a anterior ficou registrada na tag `previous_champion`).

**Justificativa (2 a 3 frases):**
Os dois modelos foram avaliados no mesmo conjunto de teste (04/12/2012 a 31/12/2012, 670 linhas), então a comparação é justa. O challenger (v7) teve RMSE de 64,60 contra 91,88 do champion (v5), uma redução de cerca de 30%, e também ficou melhor em MAE (43,85 contra 63,96) e em R² (0,839 contra 0,675). Pela regra definida (vence quem tiver menor RMSE), ele foi promovido. A diferença em `metrics.json` frente ao commit anterior não serve de base para a decisão, porque aquele valor foi medido em outro período de teste.

## 4. O que cada ferramenta fez nesse projeto?

**Pergunta 01:**
Com suas palavras, quais os dois usos do DVC nesse projeto?

O primeiro uso é versionar os dados: o arquivo `bike.csv` fica no armazenamento do DVC, e o Git guarda só o arquivo `.dvc` com o md5. Assim cada tag (`dados-v1`, `dados-v2`, `dados-v3`) aponta para uma versão exata dos dados. O segundo uso é orquestrar o pipeline em estágios (prepare, split, train, evaluate, plot e compare) pelo `dvc.yaml`. O `dvc repro` só reexecuta o que mudou e o `dvc.lock` registra com quais entradas cada resultado foi gerado, o que deixa o processo reproduzível.

**Pergunta 02:**
Com suas palavras, para que serve o MLflow nesse projeto?

O MLflow registra os treinos (parâmetros, métricas e artefatos) e guarda os modelos no Model Registry, com versões numeradas. Os aliases `@champion` e `@challenger` indicam qual versão está em produção e qual está sendo avaliada, e as tags (`decisao`, `motivo`, `previous_champion`) deixam gravada, na própria versão, a decisão de promoção e o motivo dela.