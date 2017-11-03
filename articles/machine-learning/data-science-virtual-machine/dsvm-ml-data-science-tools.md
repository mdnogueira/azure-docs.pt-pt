---
title: "Machine learning e dados de ciência ferramentas - Azure | Microsoft Docs"
description: "Ferramentas de ciência de dados e de aprendizagem máquina"
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
ms.openlocfilehash: c772fa16b94d09e0fc0450ce86503553c26f8a24
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="machine-learning-and-data-science-tools"></a>Ferramentas de ciência de dados e de aprendizagem máquina
A Máquina Virtual de ciência de dados (DSVM) tem um conjunto avançado de ferramentas e as bibliotecas do machine learning disponível nos idiomas populares, como o Python, R, Leonor. 

Aqui estão algumas do machine learning ferramentas e bibliotecas no DSVM. 

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| O que é?   |    Fast, portáveis e distribuídos gradação os aumentos (GBDT, GBRT ou GBM) biblioteca, para o Python, R, Java, Scala, C++ e muito mais. É executado num único computador, Hadoop, Spark    |
| Edições de DSVM suportados     | Windows, Linux     |
| Utilizações comuns      | Biblioteca de ML geral      |
| Como é mesmo configurado / instalado o DSVM?      |  Instalado com o suporte GPU   |
| Como utilizar / executá-lo?      | Como biblioteca Python (2.7 e 3.5), pacote de R e na ferramenta de linha de comandos do caminho (`C:\dsvm\tools\xgboost\bin\xgboost.exe` para Windows, `/dsvm/tools/xgboost/xgboost` para Linux)    |
| Ligações para amostras      | Amostras estão incluídas na VM, na `/dsvm/tools/xgboost/demo` no Linux e `C:\dsvm\tools\xgboost\demo` no Windows   |
| Ferramentas relacionadas no DSVM      | LightGBM, MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| O que é?   |   Vowpal Wabbit (também conhecido como "VW") é um open source rápido fora-de-core biblioteca do sistema de aprendizagem    |
| Edições de DSVM suportados     | Windows, Linux     |
| Utilizações comuns      | Biblioteca de ML geral      |
| Como é mesmo configurado / instalado o DSVM?      |  Windows - instalador do msi, Linux - apt get |
| Como utilizar / executá-lo?      | Como uma ferramenta de linha de comandos no caminho (`C:\Program Files\VowpalWabbit\vw.exe` no Windows, `/usr/bin/vw` no Linux)    |
| Ligações para amostras      | [Exemplos de VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Ferramentas relacionadas no DSVM      |XGBoost LightGBM, MXNet,   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| O que é?   |  Weka é uma coleção de algoritmos do machine learning para tarefas de extração de dados. Os algoritmos podem ser aplicados diretamente a um conjunto de dados ou chamados a partir do seu próprio código de Java. Weka contém as ferramentas para o pré- processamento de dados, classificação, regressão, clustering, regras de associação e visualização. |
| Edições de DSVM suportados     | Windows, Linux     |
| Utilizações comuns      | Ferramenta de ML geral     |
| Como utilizar / executá-lo?      | No Windows, procure Weka no Menu Iniciar. No Linux, inicie sessão com X2Go, em seguida, navegue para aplicações -> desenvolvimento -> Weka. |
| Ligações para amostras      | [Exemplos de weka](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Ferramentas relacionadas no DSVM      |XGBooost LightGBM, Rattle,   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| O que é?   |   Uma Interface gráfica para extração de dados através de R   |
| Edições de DSVM suportados     | Windows, Linux     |
| Utilizações comuns      | Ferramenta de extração de dados gerais de IU para R    |
| Como utilizar / executá-lo?      | Ferramenta de UI. No Windows, inicie uma linha de comandos, execute o R e, em seguida, no interior de R executar `rattle()`. No Linux, ligar com X2Go, iniciar um terminal, execute o R e, em seguida, no interior de R executar `rattle()`. |
| Ligações para amostras      | [Rattle](https://togaware.com/onepager/) |
| Ferramentas relacionadas no DSVM      |XGBoost LightGBM, Weka,   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| O que é?   | Gradação rápida, distribuída, elevado desempenho os aumentos framework (GBDT, GBRT, GBM ou MART) com base nos algoritmos de árvore de decisão, utilizados para classificação, classificação e muitas outra aprendizagem tarefas.    |
| Versões suportadas DSVM      | Windows, Linux    |
| Utilizações comuns      | Arquitetura de aumento de gradação para fins gerais      |
| Como é mesmo configurado / instalado o DSVM?      | No Windows, LightGBM é instalado como um pacote do Python. No Linux, o executável da linha de comandos está a ser `/opt/LightGBM/lightgbm`, o pacote de R está instalado e pacotes de Python estão instalados.     |
| Ligações para amostras      | [Guia de LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Ferramentas relacionadas no DSVM      | MXNet, XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| O que é?   | Uma plataforma de AI open source que suporta a aprendizagem dentro da memória, distribuída, rápida e dimensionável  |
| Versões suportadas DSVM      | Linux   |
| Utilizações comuns      | Objetivo geral distribuído, ML dimensionável   |
| Como é mesmo configurado / instalado o DSVM?      | H2O está instalado no `/dsvm/tools/h2o`.      |
| Como utilizar / executá-lo?      | Ligar à VM X2Go a utilizar. Inicie um nova terminal e execute `java -jar /dsvm/tools/h2o/current/h2o.jar`. Em seguida, iniciar um browser e ligue à `http://localhost:54321`.      |
| Ligações para amostras      | Amostras estão disponíveis na VM no Jupyter em `h2o` diretório.      |
| Ferramentas relacionadas no DSVM      | O Apache Spark, MXNet, XGBoost, máximo Sparkling, máximo profundo    |

Existem várias outras bibliotecas de ML no DSVM como o populares `scikit-learn` pacote que vem como parte da distribuição Anaconda Python que está instalada o DSVM. Lembre-se de que veja a lista de pacotes disponíveis no Python, R e Leonor executando os gestores de pacote correspondentes. 
