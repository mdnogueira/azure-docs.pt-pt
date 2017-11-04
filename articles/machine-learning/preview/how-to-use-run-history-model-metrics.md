---
title: "Como utilizar executar histórico e métricas de modelo no Azure Machine Learning Workbench | Microsoft Docs"
description: "Guia de funcionalidades de histórico de execuções e métricas de modelo do Azure Machine Learning Workbench de utilização"
services: machine-learning
author: jopela
ms.author: jopela
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 920155b2e70409fe1c7c3569326cc5a1fa01b222
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="how-to-use-run-history-and-model-metrics-in-azure-machine-learning-workbench"></a>Como histórico de execução de utilização e métricas de modelo no Azure Machine Learning Workbench

Azure Workbench de aprendizagem máquina suporta experimentação de ciência de dados através do respetivo **histórico de execuções** e **métricas de modelo** funcionalidades.
**Histórico de execução** fornece um meio para controlar as saídas da sua máquina experimentações de aprendizagem e, em seguida, permite a filtragem e de comparação dos respetivos resultados.
**Métricas de modelo** pode ter sessão iniciada a partir de qualquer ponto dos scripts, qualquer os valores são mais importantes nas suas experimentações de ciência de dados de controlo.
Este artigo descreve como tornar a utilização eficaz destas funcionalidades para aumentar a velocidade e a qualidade da sua experimentação de ciência de dados.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, tem de:
* [Criar e instalar o Azure Machine Learning](quickstart-installation.md)
- [Criar um projeto](quickstart-installation.md)


## <a name="azure-ml-logging-api-overview"></a>Descrição geral da API de registo do Azure ML
O [API de registo do Azure ML](reference-logging-api.md) está disponível através de **azureml.logging** módulo no Python (que é instalado com o Workbench do Azure ML.) Depois de importar este módulo, pode utilizar o **get_azureml_logger** método ao instanciar um **registador** objeto.
Em seguida, pode utilizar o registo **registo** método para armazenar pares chave/valor produzido pelos scripts Python.
Atualmente, as métricas de modelo de escalar, dicionário, lista de registo, dataframe tipos são suportados como mostrado.

```Python
# create a logger instance in already set up environment 
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# log scalar (any integer or floating point type is fine)
logger.log("simple value", 7)


# log list
logger.log("all values", [5, 6, 7])
```
É fácil de utilizar o registo nos seus projetos do Workbench do Azure ML e este artigo mostra-lhe como fazê-lo.

## <a name="create-a-project-in-azure-ml-workbench"></a>Criar um projeto no Azure ML Workbench
Se ainda não tiver um projeto, pode criar um o [criar e instalar o guia de introdução](quickstart-installation.md) do **Dashboard do projeto**, pode abrir o **iris_sklearn.py** script ( conforme mostrado).

![aceder a um script a partir do separador de ficheiros](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-01b.png)

Pode utilizar este script como um guia de implementação esperada de métrica de modelo de registo no Azure ML.

## <a name="parameterize-and-log-model-metrics-from-script"></a>Parametrizar e métricas de modelo de registo de Script
No **iris_sklearn.py** script, o padrão esperado para importação e a construção registador no Python pode ser reduzido para as seguintes linhas de código.

```Python
from azureml.logging import get_azureml_logger
run_logger = get_azureml_logger()
```

Depois de criado, pode invocar o **registo** método com qualquer nome/valor par.

Quando desenvolvimento estiver concluído, muitas vezes, é útil para parametrizar scripts, para que os valores podem ser transmitidos através da linha de comandos.
O exemplo abaixo mostra como aceite parâmetros da linha de comandos (quando presente) utilizando bibliotecas padrão do Python.
Este script assume um parâmetro único para a velocidade de Regularization (*reg*) utilizado para caber um modelo de classificação um esforço para aumentar *precisão* sem overfitting.
Estas variáveis, em seguida, registadas como *Regularization taxa* e *precisão* para que o modelo com os melhores resultados pode ser facilmente identificado.

```Python
# change regularization rate and you will likely get a different accuracy.
reg = 0.01
# load regularization rate from argument if present
if len(sys.argv) > 1:
    reg = float(sys.argv[1])

print("Regularization rate is {}".format(reg))

# log the regularization rate
run_logger.log("Regularization Rate", reg)

# train a logistic regression model on the training set
clf1 = LogisticRegression(C=1/reg).fit(X_train, Y_train)
print (clf1)

# evaluate the test set
accuracy = clf1.score(X_test, Y_test)
print ("Accuracy is {}".format(accuracy))

# log accuracy
run_logger.log("Accuracy", accuracy)
```

Seguir estes passos no seu scripts ativá-los efetuar uma utilização ideal de **histórico de execuções**.

## <a name="launch-runs-from-project-dashboard"></a>Iniciar execução a partir do Dashboard do projeto
Regressar ao **Dashboard do projeto**, pode iniciar uma **executar controlado** selecionando o **iris_sklearn.py** script e introduzindo o **taxa regularization**  parâmetro o **argumentos** caixa editar.

![introduzir parâmetros e executa a iniciar](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-05.png)

Uma vez que iniciar executa controlada não bloqueia o Workbench do Azure ML, vários podem ser executados em paralelo.
O estado de cada execução controlado está visível no **painel de tarefas** conforme mostrado.

![controlo é executado no painel de tarefas](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-06.png)

Isto permite que uma melhor utilização dos recursos, sem necessidade de cada tarefa para ser executada em série.

## <a name="view-results-in-run-history"></a>Ver os resultados no histórico de execução
Progresso e os resultados da execução controlado estão disponíveis para uma análise do Azure ML Workbench **histórico de execuções**.
**Histórico de execução** fornece três vistas distintas:
- Dashboard
- Detalhes
- Comparação

O **Dashboard** vista apresenta dados em todas as execuções de um determinado script, composto em formulários de gráficos tanto em tabela.
O **detalhes** vista apresenta todos os dados gerados a partir de uma execução específica de um script específico, incluindo métricas com sessão iniciada e ficheiros de saída (tais como composto rastreia.) O **comparação** vista permite que os resultados da executa duas ou três para ser visualizado lado lado a lado, incluindo também registados métricas e os ficheiros de saída.

Em oito controlados execuções do **iris_sklearn.py**, valores para o **taxa regularization** parâmetro e **precisão** resultado foram registados para ilustrar como utilizar a Run Vistas de histórico.

### <a name="run-history-dashboard"></a>Dashboard de histórico de execução
Os resultados de todas as execuções de oito são visíveis no **Dashboard do histórico de execução**.
Como **iris_sklearn.py** registos *Regularization taxa* e *precisão*, a **Dashboard do histórico de execução** apresenta gráficos para estes valores por predefinição.

![Dashboard de histórico de execução](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-07.png)

O **Dashboard do histórico de execução** pode ser personalizado para que os valores com sessão iniciada também aparecem na grelha.  Ao clicar no **personalizar** ícone apresenta o **personalização da lista de vista** diálogo conforme mostrado.

![Personalizar a grelha de dashboard do histórico de execução](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-08.png)

Quaisquer valores que tem sessão iniciadas durante executa controlada estão disponíveis para apresentar e selecionar **Regularization taxa** e **precisão** adiciona-os à grelha.

![valores com sessão iniciada na grelha personalizada](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-09.png)

É fácil localizar executa interessantes ao passar por cima de pontos de gráficos.  Neste caso, execute 7 geraram um bom precisão conjugado com uma duração baixa.

![localizar um interessantes executar](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-10.png)

Ao clicar num ponto associado 7 executar em qualquer gráfico ou a ligação para executar 7 na grelha apresenta o **detalhes de histórico de execução**.

### <a name="run-history-details"></a>Detalhes de histórico da execução
Desta vista, são apresentados os resultados completos do 7 executar juntamente com quaisquer artefactos produzidos por executar 7.

![detalhes de histórico da execução](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-11.png)

O **detalhes de histórico de execução** vista também fornece a capacidade de **transferir** quaisquer ficheiros escritos para o **. / produz** pasta (estes ficheiros são apoiados por do Workbench do Azure ML armazenamento na nuvem para o histórico de execução, que é o assunto do outro artigo.)

Por fim, **detalhes de histórico de execução** fornece o estado de um meio para restaurar o seu projeto no momento da execução.
Ao clicar no **restaurar** botão apresenta um diálogo de confirmação, conforme mostrado.

![Confirmar restauro executar](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-13.png)

Se a confirmação, os ficheiros poderão ser substituídos ou removido, para utilizam esta funcionalidade cuidadosamente a.

### <a name="run-history-comparison"></a>Comparação de histórico de execução
A seleção de dois ou três é executado no **Dashboard do histórico de execução** e clicando em **comparar** proporciona o **comparação do histórico de execução** vista.
Em alternativa, clicando em **comparar** e selecionando uma execução dentro do **detalhes de histórico de execução** vista proporciona também o **comparação do histórico de execução** vista.
Em ambos os casos, o **comparação do histórico de execução** vista fornece um meio para ver os resultados com sessão iniciada e artefactos de duas ou três é executado lado a lado.

![comparação de histórico de execução](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-12.png)

Esta vista é especialmente útil para comparação das rastreia mas, em geral, as propriedades de execução podem ser comparado com aqui.

### <a name="command-line-interface"></a>Interface de Linha de Comandos
Workbench de aprendizagem máquina do Azure também fornece acesso ao histórico de execuções através do respetivo **Interface de linha de comandos**.
Para o acesso a **Interface de linha de comandos**, clique em de **abra a linha de comandos** menu conforme mostrado.

![Abra a linha de comandos](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-14.png)

Os comandos disponíveis para o histórico de execução são acedidos através de `az ml history`, com a ajuda online, disponível através da adição de `-h` sinalizador.
```
$ az ml history -h

Group
    az ml history: View run history of machine learning experiments.

Commands:
    compare : Compare two runs.
    download: Download all the artifacts from a run into the destination path.
    info    : Details about one run.
    last    : Get detail about most recent run.
    list    : List runs.
    promote : Promote Artifacts.
```
Estes comandos fornecer as mesmas funcionalidades e devolver os mesmos dados mostrados o **vistas do histórico de execução**.
Por exemplo, os resultados da última execução podem ser apresentados como um objeto JSON.
```
$ az ml history last
{
  "Accuracy": 0.6792452830188679,
  "Regularization Rate": 0.078125,
  "attachments": "control_log, control_log.txt, driver_log, driver_log.txt, pid.txt, dataprep/backgroundProcess.log, dataprep/backgroundProcess_Engine.log, dataprep/backgroundProcess_EngineHost.log, dataprep/backgroundProcess_ProjectProvider.log, dataprep/backgroundProcess_Sampling.log, dataprep/backgroundProcess_Telemetry.log, outputs/cm.png, outputs/model.pkl, outputs/sdk_debug.txt, outputs/roc.png",
  "created_utc": "2017-09-08T00:58:01.611105+00:00",
  "description": null,
  "duration": "0:00:04.892389",
  "end_time_utc": "2017-09-08T00:58:07.951403+00:00",
  "experiment_id": "ce92d0a9-3e2c-4d51-85de-93ef22249ce1",
  "heartbeat_enabled": true,
  "hidden": false,
  "name": null,
  "parent_run_id": null,
  "properties": {
    "CommitId": "e77a5f0df2af1a482bbe39b70bfbb16b62228cb3"
  },
  "run_id": "IrisDemo_1504832281116",
  "run_number": 8,
  "script_name": "iris_sklearn.py",
  "start_time_utc": "2017-09-08T00:58:03.059014+00:00",
  "status": "Completed",
  "target": "local",
  "user_id": "e9fafe06-b0e4-4154-8374-aae34f9977b2"
}
```
Além disso, pode ser apresentada a lista de todas as execuções em formato tabular.
```
$ az ml history list -o table
  Accuracy    Regularization Rate  Duration        Run_id                  Script_name      Start_time_utc                    Status
----------  ---------------------  --------------  ----------------------  ---------------  --------------------------------  ---------
  0.679245               0.078125  0:00:04.892389  IrisDemo_1504832281116  iris_sklearn.py  2017-09-08T00:58:03.059014+00:00  Completed
  0.679245               0.15625   0:00:04.734956  IrisDemo_1504832255198  iris_sklearn.py  2017-09-08T00:57:38.507196+00:00  Completed
  0.660377               0.3125    0:00:04.913762  IrisDemo_1504832234904  iris_sklearn.py  2017-09-08T00:57:16.849881+00:00  Completed
  0.660377               0.625     0:00:06.107764  IrisDemo_1504832210767  iris_sklearn.py  2017-09-08T00:56:54.602813+00:00  Completed
  0.641509               1.25      0:00:04.742727  IrisDemo_1504832171373  iris_sklearn.py  2017-09-08T00:56:13.879280+00:00  Completed
  0.660377               2.5       0:00:04.915401  IrisDemo_1504832148526  iris_sklearn.py  2017-09-08T00:55:52.937083+00:00  Completed
  0.641509               5         0:00:04.730627  IrisDemo_1504832127172  iris_sklearn.py  2017-09-08T00:55:29.612382+00:00  Completed
  0.641509              10         0:00:06.059082  IrisDemo_1504832109906  iris_sklearn.py  2017-09-08T00:55:14.739806+00:00  Completed

```
O **Interface de linha de comandos** é um caminho alternativo para aceder a potência do Workbench do Azure Machine Learning.

## <a name="next-steps"></a>Passos Seguintes
Estas funcionalidades estão disponíveis para ajudá-lo com o processo de experimentação de ciência de dados.
Esperamos que encontrá-los para ser útil e significativamente seria Agradecemos os seus comentários.
Trata-se apenas a nossa implementação inicial e, temos um elevado grau de melhoramentos planeada.
Vamos ver reencaminhar à entrega-los continuamente para o Workbench do Azure Machine Learning. 
