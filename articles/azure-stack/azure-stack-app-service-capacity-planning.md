---
title: "Planeamento da capacidade para funções de servidor do App Service do Azure na pilha do Azure | Microsoft Docs"
description: "Planeamento da capacidade para funções de servidor do App Service do Azure na pilha do Azure"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: anwestg
ms.openlocfilehash: 4ad91def00ca73f91f0ffd8e57afa442a93176f6
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Planeamento da capacidade para funções de servidor do App Service do Azure na pilha do Azure

Para Aprovisionar uma implementação de pronto de produção do serviço de aplicações do Azure na pilha do Azure, terá de planear a capacidade de que espera que o sistema para suportar.  Eis as orientações sobre o número mínimo de instâncias e computação SKUs deve utilizar para qualquer implementação de produção.

Planear a estratégia de capacidade do serviço de aplicações, utilizando estas diretrizes. Versões futuras do pilha do Azure irão fornecer opções de elevada disponibilidade para o App Service.

| Função de servidor do serviço de aplicações | Mínimo recomendado número de instâncias | SKU de computação recomendada|
| --- | --- | --- |
| Controlador | 2 | A1 |
| Front-End | 2 | A1 |
| Gestão | 2 | A3 |
| Publicador | 2 | A1 |
| Funcionários de Web - partilhados | 2 | A1 |
| Funcionários de Web - dedicados | 2 por camada | A1 |

## <a name="controller-role"></a>Função de controlador

**Mínimo recomendado**: duas instâncias do padrão de A1

O controlador de serviço de aplicações do Azure normalmente ocorre com baixo consumo de CPU, memória e recursos de rede. No entanto, para elevada disponibilidade, tem de ter dois controladores. Dois controladores são também o número máximo de controladores permitido. Pode criar o controlador de Web Sites segundo direta do programa de instalação durante a implementação.

## <a name="front-end-role"></a>Função do Front-End

**Mínimo recomendado**: duas instâncias do padrão de A1

O Front-End encaminha os pedidos Web Workers, dependendo da disponibilidade do trabalho Web. Para elevada disponibilidade, deve ter mais do que um Front-End e pode ter mais de dois. Para efeitos de planeamento da capacidade, considere a que cada principal consegue processar aproximadamente 100 pedidos por segundo.

## <a name="management-role"></a>Função de gestão

**Mínimo recomendado**: duas instâncias do A3

A função de gestão do serviço de aplicações do Azure é responsável pelo App Service do Azure Resource Manager e pontos finais da API, extensões portais (administrador inquilino, portal das funções) e o serviço de dados. A função de servidor de gestão requer, normalmente, apenas sobre 4 GB de RAM num ambiente de produção. No entanto, podem ter elevados níveis de CPU quando forem efetuadas muitas tarefas de gestão (por exemplo, a criação de web site). Para elevada disponibilidade, deve ter mais de um servidor atribuído a esta função e, pelo menos dois núcleos por servidor.

## <a name="publisher-role"></a>Função de editor

**Mínimo recomendado**: duas instâncias do A1

Se o número de utilizadores que estiverem a publicar em simultâneo, a função de fabricante pode ter pesada de utilização da CPU. Para elevada disponibilidade, disponibilize mais de uma função de editor.  O publicador processa apenas o tráfego FTP/FTPS.

## <a name="web-worker-role"></a>Função de trabalho Web

**Mínimo recomendado**: duas instâncias do A1

Para elevada disponibilidade, deve ter, pelo menos, quatro funções de trabalho Web, dois para o modo de web sites partilhados e dois para cada camada de trabalho dedicado que planeia disponibilizar. Os modos de cálculo dedicado e partilhados fornecem diferentes níveis de serviço aos inquilinos. Se tiver muitos clientes, poderá ser necessário mais trabalhadores da Web:
 - utilização de camadas de trabalho de modo de cálculo dedicado (que são intensivas em recursos)
 - em execução no modo de computação partilhados.

Depois de um utilizador ter criado um plano do App Service para o modo de cálculo dedicado de SKU e o número de Worker(s) Web especificado em que o plano do App Service deixará de estar disponível para os utilizadores.

Para fornecer funções do Azure para os utilizadores no modelo de plano de consumo, tem de implementar partilhado trabalhadores de Web.

Quando decidir sobre o número de funções de trabalho Web partilhadas a utilizar, consulte estas considerações:

- **Memória**: memória é o recurso mais crítico, para uma função de trabalho Web. Memória insuficiente tem impacto no desempenho do web site quando a memória virtual é alternada do disco. Cada servidor necessita de cerca de 1.2 GB de RAM para o sistema operativo. RAM acima deste limiar pode ser utilizado para executar web sites.
- **Percentagem de web sites Active Directory**: normalmente, cerca de 5 por cento das aplicações num serviço de aplicações do Azure na implementação de pilha do Azure estão ativos. No entanto, a percentagem de aplicações que estão ativos a qualquer momento de determinado pode ser superior ou inferior. Com uma velocidade de aplicação ativa de 5 porcento, o número máximo de aplicações para colocar um serviço de aplicações do Azure na implementação de pilha do Azure deve ser inferior a:
    - 20 vezes o número de ativos web sites (5 x 20 = 100).
- **Requisitos de espaço de memória médio**: os requisitos de espaço de memória médio para aplicações observados em ambientes de produção está prestes a 70 MB. Por conseguinte, a memória alocada em todos os computadores de função de trabalho Web ou VMs pode ser calculada da seguinte forma:

    *Número de aplicações aprovisionado * 70 MB * 5% - (número de funções de trabalho Web * 1044 MB)*

   Por exemplo, se existirem 5000 aplicações no ambiente que esteja a executar 10 funções de trabalho Web, cada função de trabalho Web VM deve ter 7060 MB de RAM:

   5 000 * 70 * 0,05 – (10 * 1044) = 7060 (= cerca de 7 GB)

   Para obter informações sobre como adicionar mais instâncias de trabalho, consulte [adicionar mais funções de trabalho](azure-stack-app-service-add-worker-roles.md).

## <a name="file-server-role"></a>Função de servidor de ficheiros

Para a função de servidor de ficheiros, pode utilizar um servidor de ficheiros autónomo para desenvolvimento e teste, por exemplo, quando implementar o serviço de aplicações do Azure no Kit de desenvolvimento de pilha do Azure pode utilizar este modelo - https://aka.ms/appsvconmasdkfstemplate. Para fins de produção, deve utilizar um servidor de ficheiros pré-configurada do Windows ou um servidor de ficheiros do Windows não previamente configurada.

Em ambientes de produção, a função de servidor de ficheiros ocorre com e/s de disco intensiva. Porque que aloja todos os ficheiros de conteúdo e de aplicações de utilizador web sites, deve configurar um dos seguintes para esta função previamente:
- um servidor de ficheiros do Windows
- Cluster de servidor de ficheiros
- um servidor de ficheiros não sejam Windows
- cluster de servidor de ficheiros
- Dispositivo NAS (armazenamento de ligação de rede) para obter mais informações, consulte [aprovisionar um servidor de ficheiros](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Passos seguintes

[Antes de começar com o serviço de aplicações na pilha do Azure](azure-stack-app-service-before-you-get-started.md)
