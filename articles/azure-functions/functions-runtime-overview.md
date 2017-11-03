---
title: "Descrição geral de tempo de execução das funções do Azure | Microsoft Docs"
description: "Descrição geral sobre a pré-visualização de tempo de execução de funções do Azure"
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/08/2017
ms.author: anwestg
ms.openlocfilehash: cb98d5f2aaa526555820c15ba5a32fb7e78ffc5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-runtime-overview"></a>Descrição geral de tempo de execução das funções do Azure

O tempo de execução de funções do Azure fornece uma nova forma para tirar partido das razões de simplicidade e a flexibilidade das funções do Azure no local do modelo de programação. Incorporado nas raízes de código aberto mesmo como as funções do Azure, o tempo de execução de funções de Azure é implementado no local para fornecer uma experiência de desenvolvimento praticamente idênticos, como o serviço em nuvem.

![Portal de pré-visualização do tempo de execução de funções do Azure][1]

O tempo de execução de funções do Azure fornece uma forma para que a experiência das funções do Azure antes de consolidar para a nuvem. Desta forma, os elementos de código que cria podem, em seguida, ser tomados consigo para a nuvem quando migra.  O tempo de execução também abre-se novas opções para si, tais como utilizar a capacidade de computação dos componentes de reserva dos computadores no local para executar os processos de batch noite para o dia. Também pode utilizar dispositivos na sua organização condicionalmente enviar dados para outros sistemas, no local e na nuvem.

O tempo de execução de funções do Azure é composto por duas partes:
* Função de gestão do tempo de execução de funções do Azure
* Função de trabalho de Runtime de funções do Azure

## <a name="azure-functions-management-role"></a>Função de gestão de funções do Azure

A função de gestão de funções do Azure fornece um anfitrião para a gestão de funções no local. Esta função efetua as seguintes tarefas:

* A alojar o Portal de gestão de funções do Azure, que é o o mesmo que vê no [portal do Azure](https://portal.azure.com). Isto permite-lhe desenvolver as suas funções da mesma forma que faria no portal do Azure.
* Distribui as funções em vários trabalhadores de funções.
* Fornecer um ponto final de publicação para que possam publicar o seu direta de funções do Microsoft Visual Studio.

## <a name="azure-functions-worker-role"></a>Função de trabalho de funções do Azure

As funções de trabalho de funções do Azure são implementadas nos contentores do Windows e isto é, em que executa o código de função.  Pode implementar várias funções de trabalho em toda a organização e é uma forma de chave em que os clientes podem tornar a utilização de capacidade de reserva de computação.

## <a name="minimum-requirements"></a>Requisitos mínimos

Para começar com o tempo de execução de funções do Azure tem de ter uma máquina com **Windows Server 2016 ou o Windows 10 criadores Update** com acesso a um **do SQL Server** instância.

## <a name="next-steps"></a>Passos Seguintes

Instalar o [pré-visualização de Runtime de funções do Azure](https://aka.ms/azafr)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
