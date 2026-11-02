# quotelogClassifier

Classificador de logs e mensagens automáticas geradas por eventos automáticos. O objetivo é, a partir do texto bruto de um log, identificar o motivo pelo qual uma cotação não foi precificada.

> **Status:** fase inicial. Por enquanto o repositório contém uma amostra de dados rotulados; o notebook de exploração e treino ainda está vazio e nenhum modelo foi implementado.

## Estrutura

```
quotelogClassifier/
├── data/
│   └── sample.csv        # amostra rotulada para treino do modelo
└── notebook/
    └── qlogClass.ipynb   # notebook de exploração e treino (em construção)
```

## Dados

`data/sample.csv` usa `;` como separador e codificação UTF-8 com BOM.

| Coluna | Descrição |
|---|---|
| `ds_text` | Texto bruto do log (entrada do modelo) |
| `ds_notpriced` | Motivo detalhado da não precificação |
| `ds_reason` | Motivo agregado |
| `macro_ds_notpriced` | Macro-categoria do motivo |

Na amostra atual, `ds_reason` e `macro_ds_notpriced` têm os mesmos valores. Linhas com os campos de rótulo vazios ainda não foram rotuladas.

Classes presentes na amostra:

| Mensagem (`ds_text`) | `ds_notpriced` | `macro_ds_notpriced` |
|---|---|---|
| `app_filter:Qsa nok penumper …` | QSA nok | Docto nok |
| `app_filter:Api nok penumper …` | API nok | Docto nok |
| `app_filter:Suitability nok penumper …` | Suitability nok | Docto nok |
| `spread not registered penumper …` | spread not registered | spread not registered |
| `counterparty not registered penumper …` | client not registered | client not registered |

Leitura com pandas:

```python
import pandas as pd

df = pd.read_csv("data/sample.csv", sep=";", encoding="utf-8-sig", skipinitialspace=True)
```

`encoding="utf-8-sig"` remove o BOM do nome da primeira coluna, e `skipinitialspace=True` remove o espaço que alguns valores têm depois do `;`.

## Ambiente

Python 3.10 com pandas, numpy, scikit-learn e ipykernel. O ambiente virtual fica em `notebook/.qlogclass` e não é versionado. Para recriá-lo:

```bash
python3.10 -m venv notebook/.qlogclass
source notebook/.qlogclass/bin/activate
pip install pandas==2.3.3 numpy==2.2.6 scikit-learn==1.7.2 ipykernel
```

Depois, selecione `notebook/.qlogclass` como kernel do notebook no editor.
