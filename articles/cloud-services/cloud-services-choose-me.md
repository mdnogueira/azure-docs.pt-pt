---
title: "Opções de computação do Azure - serviços em nuvem | Microsoft Docs"
description: "Saiba mais sobre as opções e como funcionam de alojamento de computação do Azure: serviço de aplicações, serviços em nuvem e máquinas virtuais"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
ms.assetid: ed7ad348-6018-41bb-a27d-523accd90305
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: d27a4be968dc12818f7031b59ed40fbc9f9d88d3
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="should-i-choose-cloud-services-or-something-else"></a>Devo escolher serviços em nuvem ou outra coisa?
É a escolha de Cloud Services do Azure para si? O Azure oferece diferentes modelos de alojamento para a execução de aplicações. Cada um deles fornece um conjunto diferente de serviços, para o qual deles escolher depende exatamente o que está a tentar fazer.

[!INCLUDE [compute-table](../../includes/compute-options-table.md)]

<a name="tellmecs"></a>

## <a name="tell-me-about-cloud-services"></a>Informar-me sobre serviços cloud
Serviços cloud é um exemplo de [plataforma-como-um-serviço](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Como [do serviço de aplicações](../app-service/app-service-web-overview.md), esta tecnologia foi concebida para suportar aplicações que são dimensionáveis, fiáveis e cheap operar. Tal como um serviço de aplicações está alojado em VMs, por isso, são demasiado serviços em nuvem, no entanto, pode ter mais controlo sobre as VMs. Pode instalar o seu próprio software em VMs de serviço de nuvem e pode remoto em-los.

![cs_diagram](./media/cloud-services-choose-me/diagram.png)

Mais controlo também significa que menos facilidade de utilização. A menos que terá das opções de controlo adicionais, normalmente, é mais rápida e mais fácil de obter uma aplicação web e em execução nas Web Apps no App Service comparado com serviços em nuvem.

Existem dois tipos de funções de serviço em nuvem. A única diferença entre as duas é a forma como a função está alojada na máquina virtual.

* **Função da Web**  
Implementa e aloja a aplicação através do IIS automaticamente.

* **Função de trabalho**  
Não utilizar o IIS e executa a aplicação autónoma.

Por exemplo, uma aplicação simples pode utilizar apenas uma única função da web, que servem um Web site. Uma aplicação mais complexa poderá utilizar uma função da web para processar pedidos de entrada de utilizadores, em seguida, passar esses pedidos para uma função de trabalho para processamento. (Pode utilizar esta comunicação [Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md) ou [filas do Azure](../storage/common/storage-introduction.md).)

Como sugere a ilustração anterior, todas as VMs numa única aplicação é executado no mesmo serviço em nuvem. Os utilizadores acedam a aplicação através de um único endereço IP público, com pedidos de carga equilibrada nos VMs da aplicação. A plataforma [dimensiona e implementa](cloud-services-how-to-scale-portal.md) as VMs numa aplicação de serviços em nuvem de uma forma que evita a um ponto único de falha de hardware.

Apesar das aplicações são executadas nas máquinas virtuais, é importante compreender que serviços em nuvem fornece PaaS, não IaaS. Eis uma forma de pensar em: com a IaaS, tais como máquinas de virtuais do Azure, primeiro criar e configurar o ambiente da sua aplicação é executada, em seguida, implementar a sua aplicação para este ambiente. Está responsável por gerir muito este mundo, fazer as coisas como a implementação de novas versões do sistema operativo em cada VM aplicadas. PaaS, por outro lado, é como se o ambiente já existe. Tudo o que tem de fazer é implementar a sua aplicação. Gestão da plataforma que é executada, incluindo a implementação de novas versões do sistema operativo, é processada por si.

## <a name="scaling-and-management"></a>Dimensionamento e gestão
Com os serviços em nuvem, não crie máquinas virtuais. Em vez disso, é fornecer um ficheiro de configuração que diz ao Azure quantos de cada gostaria, tais como **web três instâncias de função** e **duas instâncias de função de trabalho**, e a plataforma cria por si.  Escolher ainda [que tamanho](cloud-services-sizes-specs.md) as VMs de segurança devem ser, mas não explicitamente criá-los por si. Se a aplicação tem de processar uma carga maior, pode pedir para as VMs mais e Azure cria nessas instâncias. Se a carga diminui, pode encerrar essas instâncias e parar a pagar para os mesmos.

Uma aplicação de serviços em nuvem é normalmente disponibilizada aos utilizadores através de um processo de dois passos. Um programador primeiro [carrega a aplicação](cloud-services-how-to-create-deploy-portal.md) para área de transição da plataforma. Quando o programador está pronto para disponibilizar a aplicação em direto, que utilizam o portal do Azure para trocar com a produção de teste. Isto [alternar entre transição e produção](cloud-services-nodejs-stage-application.md) pode ser efetuada sem período de indisponibilidade, que permite uma aplicação em execução ser atualizado para uma nova versão sem preventiva os seus utilizadores.

## <a name="monitoring"></a>Monitorização
Serviços cloud também fornece a monitorização. Como máquinas de virtuais do Azure, este Deteta um servidor físico com falhas e reinicia as VMs que estavam em execução nesse servidor num computador novo. Mas serviços Cloud também Deteta VMs com falhas e de aplicações, não apenas as falhas de hardware. Ao contrário das máquinas virtuais, tem um agente dentro de cada função da web e de trabalho e, por isso, é possível iniciar novas VMs e instâncias da aplicação quando ocorrem falhas.

A natureza PaaS dos Cloud Services tem outras implicações demasiado. Uma das mais importante é que as aplicações incorporadas nesta tecnologia devem ser escritas para ser executada corretamente quando ocorre uma falha de qualquer instância de função web ou de trabalho. Para atingir esse objetivo, uma aplicação de serviços em nuvem não deve manter o estado no sistema de ficheiros do seus próprio VMs. Ao contrário das VMs criados com máquinas virtuais do Azure, não são escritas efetuadas para VMs de serviços na nuvem persistentes; Não há nothing como um disco de dados de máquinas virtuais. Em vez disso, uma aplicação de serviços em nuvem, deve escrever todos os Estados explicitamente a base de dados SQL, blobs, tabelas ou alguns outro armazenamento externo. Criar aplicações desta forma torna-los mais fáceis de escala e mais resistente a falhas, ambos os objetivos importantes dos serviços em nuvem.

## <a name="next-steps"></a>Passos seguintes
[Criar uma aplicação de serviço em nuvem no .NET](cloud-services-dotnet-get-started.md)  
[Criar uma aplicação de serviço em nuvem no Node.js](cloud-services-nodejs-develop-deploy-app.md)  
[Criar uma aplicação de serviço em nuvem no PHP](../cloud-services-php-create-web-role.md)  
[Criar uma aplicação de serviço em nuvem no Python](cloud-services-python-ptvs.md)

