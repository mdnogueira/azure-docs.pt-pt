---
title: "Localizar é executado com o melhor exatidão e duração mais baixa no Azure Machine Learning Workbench | Microsoft Docs"
description: "Caso de utilização de um ponto-a-ponto para localizar melhor exatidão através da CLI através do Azure Machine Learning Workbench"
services: machine-learning
author: totekp
ms.author: kefzhou
manager: akannava
ms.reviewer: akannava, haining, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: aaadf526577b9b6c254204aae90200661d40f325
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="find-runs-with-the-best-accuracy-and-lowest-duration"></a>Localizar é executado com o melhor exatidão e duração mais baixa
Tendo em conta vários é executado, um caso de utilização é encontrar é executado com o melhor exatidão. Uma abordagem é utilizar a interface de linha de comandos (CLI) com um [JMESPath](http://jmespath.org/) consulta. Para obter mais informações sobre como utilizar JMESPath na CLI do Azure, consulte [JMESPath utilizar consultas com o Azure CLI 2.0](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest). No exemplo seguinte, é quatro executado é criados com valores de precisão de 0, 0.98, 1 e 1. Executa é filtradas se estão no intervalo `[MaxAccuracy-Threshold, MaxAccuracy]` onde `Threshold = .03`.

## <a name="sample-data"></a>Dados de exemplo
Se não tiver executa existente com uma `Accuracy` valor, os seguintes passos geram executa para consultas.

Em primeiro lugar, crie um ficheiro de Python no Azure Machine Learning Workbench, nome `log_accuracy.py`e cole o seguinte código:
```python
from azureml.logging import get_azureml_logger

logger = get_azureml_logger()

accuracy_value = 0.5

if len(sys.argv) > 1:
     accuracy_value = float(sys.argv[1])

logger.log("Accuracy", accuracy_value)
```

Em seguida, crie um ficheiro `run.py`e cole o seguinte código:
```python
import os

accuracy_values = [0, 0.98, 1.0, 1.0]
for value in accuracy_values:
    os.system('az ml experiment submit -c local ./log_accuracy.py {}'.format(value))
```

Por último, abra a CLI do AZURE através do Workbench e execute o comando `python run.py` para submeter quatro experimentações. Quando o script estiver concluído, deverá ver quatro executa mais no `Run History` separador.

## <a name="query-the-run-history"></a>Consultar o histórico de execução
O primeiro comando localiza o valor de precisão máxima.
```powershell
az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
```

Utilizar este valor de precisão máxima de `1` e um valor de limiar de `0.03`, os filtros de comando segundo for executado utilizando `Accuracy` e, em seguida, ordena é executado por `duration` ascendente.
```powershell
az ml history list --query '@[?Accuracy >= sum(`[1, -0.03]`)] | sort_by(@, &duration)'
```
> [!NOTE]
> Se pretender que uma verificação de limite superior strict, o formato de consulta é``@[?Accuracy >= sum(`[$max_accuracy_value, -$threshold]`) && Accuracy <= `$max_accuracy_value`]``

Se utilizar o PowerShell, o seguinte código utiliza variáveis locais para armazenar o limiar e a precisão máxima:
```powershell
$threshold = 0.03
$max_accuracy_value = az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
$find_runs_query = '@[?Accuracy >= sum(`[{0}, -{1}]`)] | sort_by(@, &duration)' -f $max_accuracy_value, $threshold
az ml history list --query $find_runs_query
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o registo, consulte [como utilizar o histórico de execução e métricas de modelo no Azure Machine Learning Workbench](how-to-use-run-history-model-metrics.md).    
