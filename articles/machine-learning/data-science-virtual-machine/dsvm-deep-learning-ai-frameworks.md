---
title: Learning profunda e AI estruturas - Azure | Microsoft Docs
description: Arquiteturas de Aprendizagem Profunda e AI
keywords: "ferramentas de ciência de dados, a máquina de virtual de ciência de dados, as ferramentas de ciência de dados, ciência de dados do linux"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 46662c15374f781a6527bb1435d883ae060c63f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deep-learning-and-ai-frameworks"></a>Arquiteturas de Aprendizagem Profunda e AI
O [máquinas de virtuais de ciência de dados](http://aka.ms/dsvm) (DSVM) e o [profunda VM Learning](http://aka.ms/dsvm/deeplearning) suporta um número de estruturas de aprendizagem avançada para o ajudar a criar aplicações Artificial Intelligence (AI) com a Análise Preditiva e capacidades cognitivos como imagem e a compreensão de idiomas. 

Seguem-se os detalhes em todos os avançada disponíveis no DSVM estruturas de aprendizagem.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

|    |           |
| ------------- | ------------- |
| O que é?   | Learning profunda framework      |
| Edições de DSVM suportados      | Windows, Linux     |
| Como é mesmo configurado / instalado o DSVM?  | Microsoft cognitivos Toolkit (CNTK) está instalado no Python 2.7, no _raiz_ ambiente, bem como o Python 3.5, no _py35_ ambiente.   |
| Ligações para amostras      | Notas do Jupyter de exemplo são incluídas.     |
| Ferramentas relacionadas no DSVM      | Keras      |
| Como utilizar / executá-lo?    | Abrir Jupyter, em seguida, procure a pasta CNTK  |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| O que é?   | Learning profunda framework      |
| Edições de DSVM suportados      | Windows, Linux     |
| Como é mesmo configurado / instalado o DSVM?  | No Linux, TensorFlow está instalado no Python 2.7 (_raiz_), bem como o Python 3.5 (_py35_) ambiente. No Windows, Tensorflow está instalado no Python 3.5 (_py35_) ambiente.  |
| Ligações para amostras      | Notas do Jupyter de exemplo são incluídas.     |
| Ferramentas relacionadas no DSVM      | Keras      |
| Como utilizar / executá-lo?    | Abrir Jupyter, em seguida, procure a pasta de TensorFlow.  |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| O que é?   | Learning profunda framework      |
| Edições de DSVM suportados      | Windows, Linux     |
| Como é mesmo configurado / instalado o DSVM?  | Keras está instalado no Python 2.7 (_raiz_), bem como o Python 3.5 (_py35_) ambiente.   |
| Ligações para amostras      | https://github.com/fchollet/keras/Tree/Master/examples      |
| Ferramentas relacionadas no DSVM      | Microsoft cognitivos Toolkit, TensorLlow, Theano      |
| Como utilizar / executá-lo?    | Transfira os exemplos da localização do Github, copie-a para um diretório na ~/notebooks e abri-lo no Jupyter   |




## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| O que é?   | Learning profunda framework      |
| Edições de DSVM suportados      | Linux     |
| Como é mesmo configurado / instalado o DSVM?  | Caffe está instalado no `/opt/caffe`.    |
| Ligações para amostras      | Amostras incluídas no `/opt/caffe/examples`.      |
| Ferramentas relacionadas no DSVM      | Caffe2      |
### <a name="how-to-use--run-it"></a>Como utilizar / executá-lo?  

Utilize X2Go para iniciar sessão na sua VM, em seguida, inicie um novo terminal e introduza

```
cd /opt/caffe/examples
jupyter notebook
```

Abre uma nova janela do browser com blocos de notas do exemplo.

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| O que é?   | Learning profunda framework      |
| Edições de DSVM suportados      | Linux     |
| Como é mesmo configurado / instalado o DSVM?  | Caffe2 está instalado no `/opt/caffe2`. Também está disponível para o Python 2.7 (_raiz_) conda ambiente.     |
| Ligações para amostras      | Notas do Jupyter de exemplo são incluídas     |
| Ferramentas relacionadas no DSVM      | Caffe      |
| Como utilizar / executá-lo?    | Abrir Jupyter, em seguida, navegue para o diretório de Caffe2 para localizar os blocos de notas do exemplo. Algumas notas requerem raiz Caffe2 ser definida no código Python; Introduza /opt/caffe2.   |


## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| O que é?   | Learning profunda framework      |
| Edições de DSVM suportados      | Windows, Linux     |
| Como é mesmo configurado / instalado o DSVM?  | Chainer está instalado no Python 2.7 (_raiz_), bem como o Python 3.5 (_py35_) ambiente. ChainerRL e ChainerCV também são instalados.   |
| Ligações para amostras      | Notas do Jupyter de exemplo são incluídas.      |
| Ferramentas relacionadas no DSVM      | Caffe      |

### <a name="how-to-use--run-it"></a>Como utilizar / executá-lo?  

Num terminal, ativar a versão do Python pretende (_raiz_ ou _py35_), execute _python_, em seguida, importar Chainer. No Jupyter, selecione o Python 2.7 ou 3.5 kernel, em seguida, importar Chainer.


## <a name="deep-water"></a>Máximo de profundo

|    |           |
| ------------- | ------------- |
| O que é?   | Arquitetura de aprendizagem profunda de H2O      |
| Edições de DSVM suportados      | Linux     |
| Como é mesmo configurado / instalado o DSVM?  | Máximo de profundo está instalado no `/dsvm/tools/deep_water`.   |
| Ligações para amostras      | Amostras estão disponíveis através do servidor de níveis máximos profunda.      |
| Ferramentas relacionadas no DSVM      | H2o, Sparkling máximo      |

### <a name="how-to-use--run-it"></a>Como utilizar / executá-lo?  

Ligar à VM X2Go a utilizar. Cada um terminal, inicie o servidor de profunda máximo:

    java -jar /dsvm/tools/deep_water/h2o.jar

Em seguida, abra um browser e ligar ao `http://localhost:54321`.



## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| O que é?   | Learning profunda framework      |
| Edições de DSVM suportados      | Windows, Linux     |
| Como é mesmo configurado / instalado o DSVM?  | MXNet está instalado no `C:\dsvm\tools\mxnet` no Windows e `/dsvm/tools/mxnet` no Linux. Enlaces de Python estão instalados no Python 2.7 (_raiz_), bem como o Python 3.5 (_py35_) ambiente. Enlaces de R são também instalados.   |
| Ligações para amostras      | Notas do Jupyter de exemplo são incluídas.    |
| Ferramentas relacionadas no DSVM      | Keras      |
| Como utilizar / executá-lo?    | Abrir Jupyter, em seguida, procure a pasta de mxnet  |

## <a name="nvidia-digits"></a>NVIDIA DÍGITOS

|    |           |
| ------------- | ------------- |
| O que é?   | Ligação avançada de formação rapidamente modelos learning profunda aprendizagem sistema a partir de NVIDIA      |
| Edições de DSVM suportados      | Linux     |
| Como é mesmo configurado / instalado o DSVM?  | DÍGITOS está instalado no `/dsvm/tools/DIGITS` e está disponível um serviço chamado _dígitos_.   |
### <a name="how-to-use--run-it"></a>Como utilizar / executá-lo?  

Inicie sessão na VM com o X2Go. Cada um terminal, inicie o serviço:

    sudo systemctl start digits

O serviço demora cerca de um minuto para iniciar. Iniciar um browser e navegue para `http://localhost:5000`.



## <a name="nvdia-smi"></a>nvdia smi

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta NVIDIA para consultar a atividade GPU      |
| Edições de DSVM suportados      | Windows, Linux     |
| Como é mesmo configurado / instalado o DSVM?  | _NVIDIA smi_ está disponível no caminho do sistema.   |
| Como utilizar / executá-lo? | Inicie uma linha de comandos (no Windows) ou um terminal (no Linux), em seguida, execute _nvidia smi_.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| O que é?   | Learning profunda framework      |
| Edições de DSVM suportados      | Linux     |
| Como é mesmo configurado / instalado o DSVM?  | Theano está instalado no Python 2.7 (_raiz_), bem como o Python 3.5 (_py35_) ambiente.   |
| Ferramentas relacionadas no DSVM      | Keras      |
| Como utilizar / executá-lo?    | Cada um terminal, ative a versão do Python que pretende (raiz ou py35), execute o python e importar theano. No Jupyter, selecione o Python 2.7 ou 3.5 kernel, em seguida, importar theano.  |



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| O que é?   | Learning profunda framework      |
| Edições de DSVM suportados      | Linux     |
| Como é mesmo configurado / instalado o DSVM?  | Torch está instalado no `/dsvm/tools/torch`. PyTorch está instalado no Python 2.7 (_raiz_), bem como o Python 3.5 (_py35_) ambiente.   |
| Ligações para amostras      | Exemplos de torch estão localizados em `/dsvm/samples/torch`. Exemplos de PyTorch estão localizados em `/dsvm/samples/pytorch`.      |

