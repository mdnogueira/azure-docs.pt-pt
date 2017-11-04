---
title: "Ler e escrever ficheiros de dados de grandes dimensões | Microsoft Docs"
description: "Ler e escrever ficheiros grandes no Azure Machine Learning experimentações."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.openlocfilehash: f5c75b95d9019c15bb402313ce7407fa9abb81d4
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="persisting-changes-and-working-with-large-files"></a>A persistência de alterações e trabalhar com ficheiros grandes
Com o serviço Azure Machine Learning experimentação, pode configurar uma variedade de destinos de execução. Alguns destinos são locais, como um computador local ou um contentor de Docker num computador local. Outros recursos são remotos, tais como um contentor de Docker num computador remoto ou um cluster do HDInsight. Para obter mais informações, consulte [descrição geral do Azure Machine Learning experimentação de serviço de execução](experimentation-service-configuration.md). 

Pode executar um destino, tem de copiar a pasta do projeto para o destino de computação. Terá de efetuar, por isso, mesmo com uma execução local que utiliza uma pasta temporária do local para esta finalidade. 

## <a name="execution-isolation-portability-and-reproducibility"></a>Isolamento de execução, a portabilidade e reproducibility
O objetivo do design desta é garantir o isolamento, reproducibility e portabilidade de execução. Se executar o mesmo script duas vezes, no mesmo ou outro computação destino, receber os mesmos resultados. As alterações efetuadas durante a primeira execução não devem afetar os na segunda execução. Com esta conceção, pode tratar destinos de computação como recursos de computação sem monitorização de estado, cada ter sem afinidade para as tarefas que são executados após a sua conclusão.

## <a name="challenges"></a>Desafios
Apesar desta estrutura fornece as vantagens de portabilidade e repetibilidade, implica igualmente alguns desafios únicos.

### <a name="persisting-state-changes"></a>Persistentes as alterações de estado
Se o script modifica o estado do contexto de computação, as alterações não são mantidas para a execução seguinte e, se estiver não a propagadas volta para a máquina cliente automaticamente. 

Mais especificamente, se o script cria uma subpasta ou escreve um ficheiro, esse ficheiro ou pasta não estará presente no diretório do seu projeto após a execução. Os ficheiros são armazenados numa pasta temporária no ambiente de computação de destino. Pode utilizá-los para fins de depuração, mas não é possível confiar nos respetivos existência permanente.

### <a name="working-with-large-files-in-the-project-folder"></a>Trabalhar com ficheiros grandes na pasta do projeto

Se a pasta do projeto contém quaisquer ficheiros grandes, pode implicar latência quando a pasta é copiada para o ambiente de computação de destino no início de uma execução. Mesmo que a execução ocorre localmente, há disco ainda desnecessários tráfego para evitar. Por este motivo, iremos cap atualmente o tamanho máximo do projeto 25 MB.

## <a name="option-1-use-the-outputs-folder"></a>Opção 1: Utilizar o *produz* pasta
Esta opção é preferível se todas as condições seguintes se aplicar:
* O script produz os ficheiros.
* Espera que os ficheiros para alterar com cada experimentação.
* Pretende manter um histórico desses ficheiros. 

Casos de utilização comuns são:
* Um modelo de preparação
* Criar um conjunto de dados
* Um gráfico de desenho como um ficheiro de imagem como parte da sua execução do modelo de formação 

Além disso, que pretende comparar as saídas em execução, selecione um ficheiro de saída (por exemplo, um modelo) que foi produzido por anterior executar e, em seguida, utilizá-lo para uma tarefa subsequente (tal como a classificação).

Pode escrever ficheiros para uma pasta denominada *produz* que é relativo ao diretório de raiz. A pasta recebe um tratamento especial, o serviço de experimentação. Tudo o script cria na pasta durante a execução, tais como um ficheiro de modelo, o ficheiro de dados ou o ficheiro de imagem valor (coletivamente conhecidos como _artefactos_), é copiado para a conta de armazenamento de Blobs do Azure que se encontra associada a conta de experimentação depois de concluída a execução. Os ficheiros passam a fazer parte do seu registo de histórico de execução.

Eis um breve exemplo de código para armazenar um modelo no *produz* pasta:
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
Pode transferir quaisquer artefactos ao navegar para o **ficheiros de saída** secção da página de detalhes de execução de uma determinada executar no Azure Machine Learning Workbench. Basta selecionar as e, em seguida, selecione o **transferir** botão. Em alternativa, pode introduzir o `az ml asset download` comando na janela de interface de linha de comandos (CLI).

Para obter um exemplo mais completo, consulte o `iris_sklearn.py` script de Python no _classificar Iris_ projeto de exemplo.

## <a name="option-2-use-the-shared-folder"></a>Opção 2: Utilize a pasta partilhada
Se não pretender manter um histórico de ficheiros de cada execução, utilizar uma pasta partilhada que pode ser acedida através de execuções independentes pode ser uma excelente opção para os seguintes cenários: 
- O script tem de carregar os dados de ficheiros locais, tais como ficheiros. csv ou um diretório de ficheiros text ou image, como dados de preparação ou de teste. 
- O script processa dados não processados e escreve os resultados intermédios, tais como os dados de formação featurized dos ficheiros de texto ou de imagem, que são utilizados numa formação subsequente executar. 
- O script spits fora de um modelo e o script de classificação subsequente tem recolherá o modelo e utilizá-lo para avaliação. 

É importante ter em atenção que a pasta partilhada encontre localmente no seu destino de computação que escolheu. Por conseguinte, esta opção só funciona se todas as execuções de script que referenciam esta pasta partilhada são executadas no mesmo destino de computação e o destino de computação não é reciclado entre é executado.

Ao tirar partido da funcionalidade de pasta partilhada, pode ler a partir de ou escrever numa pasta especial que é identificada por uma variável de ambiente, `AZUREML_NATIVE_SHARE_DIRECTORY`. 

### <a name="example"></a>Exemplo
Eis alguns códigos de Python de exemplo para utilizar esta pasta de partilha para ler e escrever um ficheiro de texto:
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'wb') as f:
    f.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'r') as f:
    text = file.read()
```

Para obter um exemplo mais completo, consulte o *iris_sklearn_shared_folder.py* ficheiros o _classificar Iris_ projeto de exemplo.

Antes de poder utilizar esta funcionalidade, tem de definir *.compute* ficheiro algumas configurações simples que representam o contexto de execução visados no *aml_config* pasta. O caminho para a pasta real pode variar consoante o destino de computação que escolher e o valor que configura.

### <a name="configure-local-compute-context"></a>Configurar o contexto de computação local

Para ativar esta funcionalidade num contexto de computação local, adicione simplesmente à sua *.compute* ficheiro a linha seguinte, que representa o _local_ ambiente (normalmente designado *local.compute*).
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

O caminho ~/.azureml/share é o caminho de pasta base predefinido. Pode alterá-la para qualquer caminho absoluto local que esteja acessível a executar o script. O nome da conta experimentação, o nome da área de trabalho e o nome de projeto são automaticamente acrescentados ao nome do diretório de base, o que torna-se o caminho completo do diretório partilhado. Por exemplo, os ficheiros podem ser escritos (e obter) o seguinte caminho se utilizar precedente valor predefinido:

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-the-docker-compute-context-local-or-remote"></a>Configurar o contexto de computação do Docker (local ou remoto)
Para ativar esta funcionalidade num contexto de computação Docker, tem de adicionar as seguintes duas linhas para o Docker local ou remoto *.compute* ficheiro.

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>O **sharedVolumes** propriedade tem de ser definida como *verdadeiro* quando utiliza o `AZUREML_NATIVE_SHARE_DIRECTORY` variável de ambiente para aceder à pasta partilhada. Caso contrário, a execução falhará.

O código em execução no contentor do Docker sempre vê esta pasta partilhada como */azureml-share /*. O caminho da pasta, visto pelo contentor de Docker, não é configurável. Não utilize este nome de pasta no seu código. Em alternativa, utilize sempre o nome de variável de ambiente `AZUREML_NATIVE_SHARE_DIRECTORY` para fazer referência a esta pasta. Está mapeado para uma pasta local no anfitrião Docker máquina ou contexto de computação. O diretório base desta pasta local é o valor configurável do `nativeSharedDirectory` definição o *.compute* ficheiro. O caminho local da pasta partilhada no computador anfitrião, se utilizar o valor predefinido é o seguinte:
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!NOTE]
>O caminho da pasta partilhada no disco local é o mesmo se se trata de um contexto de computação local ou num contexto de computação de Docker local. Isto significa que mesmo podem partilhar ficheiros entre uma execução local e uma execução local do Docker.

Pode colocar dados de entrada diretamente nestas pastas e esperar que os locais ou de execuções de Docker na máquina podem recolhê-lo. Também pode escrever ficheiros nesta pasta do seu local ou execuções de Docker e esperar ficheiros obterem persistente nessa pasta reiniciadas o ciclo de vida de execução.

Para obter mais informações, consulte [ficheiros de configuração de execução do Azure Machine Learning Workbench](experimentation-service-configuration-reference.md).

>[!NOTE]
>O `AZUREML_NATIVE_SHARE_DIRECTORY` variável de ambiente não é suportada um contexto de computação do HDInsight. No entanto, é fácil alcançar o mesmo resultado utilizando explicitamente um caminho absoluto do armazenamento de Blobs do Azure para leem e escrevem para o armazenamento de BLOBs anexado.

## <a name="option-3-use-external-durable-storage"></a>Opção 3: Utilizar armazenamento durável externo

Pode utilizar o armazenamento durável externo para manter o estado durante a execução. Esta opção é útil nos seguintes cenários:
- Os dados de entrada já estão armazenados no armazenamento durável que está acessível a partir do ambiente de computação de destino.
- Os ficheiros não precisam de fazer parte dos registos do histórico de execução.
- Os ficheiros tem de ser partilhados por execuções pelos vários ambientes de computação.
- Os ficheiros tem de conseguir continuam a vigorar após o contexto de computação em si.

Uma abordagem essa consiste em utilizar o Blob storage do Azure a partir do código Python ou PySpark. Eis um breve exemplo:

```python
from azure.storage.blob import BlockBlobService
import glob
import os

ACCOUNT_NAME = "<your blob storage account name>"
ACCOUNT_KEY = "<account key>"
CONTAINER_NAME = "<container name>"

blob_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

## Create a new container if necessary, or use an existing one
my_service.create_container(CONTAINER_NAME, fail_on_exist=False, public_access=PublicAccess.Container)

# df is a pandas DataFrame
df.to_csv('mydata.csv', sep='\t', index=False)

# Export the mydata.csv file to Blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

Eis um breve exemplo para anexar qualquer arbitrário Blob storage do Azure para um tempo de execução do HDI Spark:
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>Conclusão
Porque o Azure Machine Learning executa scripts ao copiar a pasta do projeto todo para o contexto de computação de destino, ter especial cuidado com ficheiros intermediário, saída e entrada grande. Transações de ficheiros grandes, pode utilizar a pasta de saídas especial, a pasta partilhada que está acessível através de `AZUREML_NATIVE_SHARE_DIRECTORY` variável de ambiente ou armazenamento durável externo. 

## <a name="next-steps"></a>Passos seguintes
- Reveja o [ficheiros de configuração de execução do Azure Machine Learning Workbench](experimentation-service-configuration-reference.md) artigo.
- Consulte o [classificar Iris](tutorial-classifying-iris-part-1.md) projeto tutorial utiliza a pasta de saídas para manter um modelo preparado.
