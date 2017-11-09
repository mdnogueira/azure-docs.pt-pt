---
title: Como utilizar GPU para o Azure Machine Learning | Microsoft Docs
description: "Este artigo descreve como utilizar unidades de processamento gráfico (GPU) para dar formação sobre as redes neurais profundo no Azure Machine Learning Workbench."
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 2501113fe75f20602059927a4e0a50cecd86b187
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="how-to-use-gpu-in-azure-machine-learning"></a>Como utilizar GPU no Azure Machine Learning
Unidade de processamento gráfico (GPU) em grande escala é utilizada para processar tarefas viáveis intensivas que, normalmente, podem acontecer quando se prepara determinados modelos de rede neuronal profundo. Ao utilizar GPUs, pode reduzir o tempo de preparação dos modelos significativamente. Neste documento, irá aprender a configurar o Azure ML Workbench utilizar [DSVM (máquina de Virtual de ciência de dados)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) equipados com GPUs como destino de execução. 

## <a name="prerequisites"></a>Pré-requisitos
- Para seguir este guia de procedimentos, tem de primeiro [instalar Azure ML Workbench](quickstart-installation.md).
- Tem de ter acesso a computadores equipados com NVidia GPUs.
    - Pode executar os scripts diretamente no computador local (Windows ou macOS) com GPUs.
    - Também pode executar scripts num contentor Docker numa máquina com GPUs.

## <a name="execute-in-local-environment-with-gpus"></a>Executar no _local_ ambiente com GPUs
Pode instalar o Azure ML Workbench num computador equipado com GPUs e executar contra _local_ ambiente. Isto pode ser:
- Um computador Windows ou macOS físico.
- Uma VM do Windows (Máquina Virtual), tais como um Windows DSVM aprovisionados com o modelo de série de Azure NC VMs.

Neste caso, não existem nenhuma configuração especial necessária no Azure ML Workbench. Basta certificar-se de que todos os controladores necessários, toolkits e preparados para a GPU aprendizagem bibliotecas instaladas localmente. Basta executar contra _local_ ambiente em que o tempo de execução do Python pode aceder diretamente ao hardware da GPU local.

1. Abra AML Workbench. Aceda a **ficheiro** e **abra a linha de comandos**. 
2. Linha de comandos, instale o framework de preparados para a GPU profunda learning como o Toolkit de cognitivos, TensorFlow e etc. Por exemplo:

```batch
REM install latest TensorFlow with GPU support
C:\MyProj> pip install tensorflow-gpu

REM install Microsoft Cognitive Toolkit 2.1 (1-bit SGD) with GPU support on Windows
C:\MyProj> pip install https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-win_amd64.whl
```

3. Escreva código Python, que tira partido do avançada bibliotecas de aprendizagem.
4. Escolha _local_ como ambiente de computação e executar o código de Python.

```batch
REM execute Python script in local environment
C:\MyProj> az ml experiment submit -c local my-deep-learning-script.py
```

## <a name="execute-in-docker-environment-on-linux-vm-with-gpus"></a>Executar no _docker_ ambiente na VM com Linux com GPUs
Azure ML Workbench também suporta a execução no Docker no VM Linux do Azure. Aqui tem uma excelente opção para executar tarefas viáveis intensivas numa peça de hardware virtual poderosa e paga apenas a utilização de desativá-lo quando terminar. Enquanto o princípio é possível utilizar GPUs em qualquer máquina virtual do Linux, DSVM baseado no Ubuntu inclui o necessário controladores CUDA e bibliotecas previamente instaladas, facilitando muito a configuração. Siga os passos abaixo:

### <a name="create-a-ubuntu-based-linux-data-science-virtual-machine-in-azure"></a>Criar uma máquina de Virtual de ciência Ubuntu Linux dados no Azure
1. Abra o browser e aceda ao [portal do Azure](https://portal.azure.com)

2. Selecione **+ novo** no lado esquerdo do portal.

3. Procure "Dados ciência Máquina Virtual para Linux (Ubuntu)" no marketplace.

4. Clique em **criar** para criar um DSVM Ubuntu.

5. Preencha o **Noções básicas** formulário com as informações necessárias.
Ao selecionar a localização para a VM, tenha em atenção que as VMs de GPU só estão disponíveis em determinados regiões do Azure, por exemplo, **Sul Central nos**. Consulte [computação produtos disponíveis por região](https://azure.microsoft.com/en-us/regions/services/).
Clique em OK para guardar o **Noções básicas** informações.

6. Escolha o tamanho da máquina virtual. Selecione um dos tamanhos com o prefixo de NC VMs, que estão equipados com chips NVidia GPU.  Clique em **ver tudo** para ver a lista completa, conforme necessário. Saiba mais sobre [GPU-equipado VMs do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes-gpu).

7. Concluir as restantes definições e rever as informações de compra. Clique em compra para criar a VM. Tome nota do endereço IP alocado à máquina virtual. 

### <a name="create-a-new-project-in-azure-ml-workbench"></a>Criar um novo projeto no Azure ML Workbench 
Pode utilizar o _MNIST classificar utilizando TensorFlow_ exemplo, ou o _classificar MNIST conjunto de dados com CNTK_ exemplo.

### <a name="create-a-new-compute-target"></a>Criar um novo destino de computação
Inicie a linha de comandos do Azure ML Workbench. Introduza o seguinte comando. Substitua o texto do marcador do exemplo abaixo os seus próprios valores para o nome, endereço IP, nome de utilizador e palavra-passe. 

```batch
C:\MyProj> az ml computetarget attach --name "my_dsvm" --address "my_dsvm_ip_address" --username "my_name" --password "my_password" --type remotedocker
```

### <a name="configure-azure-ml-workbench-to-access-gpu"></a>Configurar o Azure ML Workbench para acesso GPU
Volte ao projeto e abra **vista do ficheiro**e acessos do **atualizar** botão. Agora verá dois novos ficheiros de configuração `my_dsvm.compute` e `my_dsvm.runconfig`.
 
Abra o `my_dsvm.compute`. Alterar o `baseDockerImage` para `microsoft/mmlspark:plus-gpu-0.7.9` e adicione uma nova linha `nvidiaDocker: true`. Por isso, o ficheiro deve ter estas duas linhas:
 
```yaml
...
baseDockerImage: microsoft/mmlspark:plus-gpu-0.7.91
nvidiaDocker: true
```
 
Agora abra `my_dsvm.runconfig`, alterar `Framework` valor a partir da `PySpark` para `Python`. Se não vir esta linha, adicioná-lo, uma vez que o valor predefinido seria `PySpark`.

```yaml
"Framework": "Python"
```
### <a name="reference-deep-learning-framework"></a>Arquitetura de aprendizagem profunda de referência 
Abra `conda_dependencies.yml` de ficheiros e certifique-se estiver a utilizar a versão GPU avançada learning pacotes de Python do framework. Os projetos de exemplo listam as versões de CPU, por isso terá de efetuar esta alteração.

Exemplo TensorFlow: 
```
name: project_environment
dependencies:
  - python=3.5.2
  # latest TensorFlow library with GPU support
  - tensorflow-gpu
```

Exemplo para o Toolkit cognitivos da Microsoft:
```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip: 
    # use the Linux build of Microsoft Cognitive Toolkit 2.1 with GPU support
    - https://cntk.ai/PythonWheel/GPU/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

Também pode utilizar a versão de bits SGD 1 do Toolkit cognitivos Microsoft que fornece melhorias de desempenho em GPU de várias VMs. Tenha em atenção [o requisito de licença para 1 bit-SGD](https://docs.microsoft.com/en-us/cognitive-toolkit/cntk-1bit-sgd-license).

```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip:    
    # use the Linux build of the Microsoft Cognitive Toolkit 2.1 with 1-bit SGD and GPU support
    - https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

### <a name="execute"></a>Executar
Agora está pronto para executar os scripts Python. Pode executá-los dentro do Azure ML Workbench utilizando o `my_dsvm` contexto, ou pode iniciá-lo na linha de comandos:
 
```batch
C:\myProj> az ml experiment submit -c my_dsvm my_tensorflow_or_cntk_script.py
```
 
Para verificar que é utilizada na GPU, examine a saída de execução para ver algo semelhante a seguinte:

```
name: Tesla K80
major: 3 minor: 7 memoryClockRate (GHz) 0.8235
pciBusID 06c3:00:00.0
Total memory: 11.17GiB
Free memory: 11.11GiB
```

Parabéns! O script harnessed apenas a potência de GPU através de um contentor de Docker!

## <a name="next-steps"></a>Passos seguintes
Ver um exemplo de utilização da GPU para acelerar a formação de rede neuronal profundo na galeria do Azure ML.
