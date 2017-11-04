---
title: "Exploração e visualização as ferramentas do Data - Azure | Microsoft Docs"
description: "Dados exploração e visualização de ferramentas para a máquina de Virtual de ciência de dados."
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
ms.openlocfilehash: d8f0616f17dbaa02082492cc1c68f1d989ea5aae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>Exploração e visualização as ferramentas do data na máquina de Virtual de ciência de dados

Etapa chave de ciência de dados é compreender os dados. Ferramentas de exploração de dados e visualização de ajudam a acelerar a compreensão de dados. Seguem-se algumas ferramentas fornecidas no DSVM esse faciliate esta chave passo. 

## <a name="apache-drill"></a>Apache desagregação
|    |           |
| ------------- | ------------- |
| O que é?   | Motor de consulta SQL de código aberto no macrodados    |
| Versões suportadas DSVM      | Windows, Linux  |
| Como é mesmo configurado / instalado o DSVM?      |  Instalado no `/dsvm/tools/drill*` no apenas no modo incorporado   |
| Utilizações comuns      |  Exploração de dados no situ sem necessidade de ETL. Formatos includign CSV, JSON, relacionais tabelas, Hadoop e origens de dados diferentes de consulta     |
| Como utilizar / executá-lo?      | Atalho do ambiente de trabalho  <br/> [Introdução ao desagregação em 10 minutos](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Ferramentas relacionadas no DSVM      |   Rattle, Weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| O que é?   |  Weka é uma coleção de algoritmos do machine learning para tarefas de extração de dados. Os algoritmos podem ser aplicados diretamente a um conjunto de dados ou chamados a partir do seu próprio código de Java. Weka contém as ferramentas para o pré- processamento de dados, classificação, regressão, clustering, regras de associação e visualização. |
| Edições de DSVM suportados     | Windows, Linux     |
| Utilizações comuns      | Ferramenta de ML geral     |
| Como utilizar / executá-lo?      | No Windows, procure Weka no Menu Iniciar. No Linux, inicie sessão com X2Go, em seguida, navegue para aplicações -> desenvolvimento -> Weka. |
| Ligações para amostras      | [Exemplos de weka](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Ferramentas relacionadas no DSVM      |Xgboost LightGBM, Rattle,   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| O que é?   |   Uma Interface gráfica para extração de dados através de R   |
| Edições de DSVM suportados     | Windows, Linux     |
| Utilizações comuns      | Ferramenta de extração de dados gerais de IU para R    |
| Como utilizar / executá-lo?      | Ferramenta de UI. No Windows, inicie uma linha de comandos, execute o R e, em seguida, no interior de R executar `rattle()`. No Linux, ligar com X2Go, iniciar um terminal, execute o R e, em seguida, no interior de R executar `rattle()`. |
| Ligações para amostras      | [Rattle](https://togaware.com/onepager/) |
| Ferramentas relacionadas no DSVM      |Xgboost LightGBM, Weka,   |

## <a name="powerbi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| O que é?   | Visualização de dados interativa e BI ferramenta    |
| Versões suportadas DSVM      | Windows  |
| Utilizações comuns      |  Visualização de dados e a criação de Dashboards   |
| Como utilizar / executá-lo?      | Atalho do ambiente de trabalho (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Ferramentas relacionadas no DSVM      |   Visual Studio 2017, o código do Visual Studio, Juno      |

