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

O primeiro uso é versionar os dados: o bike.csv é grande e não vai para o Git, então o DVC guarda o arquivo no armazenamento dele e o Git guarda só um arquivinho .dvc com o código (md5) daquela versão. Com as tags dados-v1, dados-v2 e dados-v3, consigo voltar para qualquer versão dos dados quando quiser. A segunda foi montar o pipeline em etapas (prepare, split, train, evaluate, plot e compare) no dvc.yaml. Quando rodo o dvc repro, ele só refaz o que mudou, e o dvc.lock guarda o registro do que foi usado, então dá para repetir o mesmo resultado depois.

**Pergunta 02:**
Com suas palavras, para que serve o MLflow nesse projeto?

O MLflow serve para guardar e organizar os modelos. Cada treino fica registrado com suas métricas, e cada modelo novo vira uma versão numerada no Registry (aqui, a versão 5 e a 7). Os nomes @champion e @challenger mostram qual modelo está valendo e qual está sendo testado. Também usei as tags da versão (decisao, motivo e previous_champion) para deixar gravado se o modelo novo foi promovido, por quê, e qual era o champion anterior.