---
title: "Estado de funcionamento da descrição geral da monitorização para o Gateway de aplicação do Azure | Microsoft Docs"
description: "Saiba mais sobre as capacidades de monitorização no Gateway de aplicação do Azure"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7eeba328-bb2d-4d3e-bdac-7552e7900b7f
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2016
ms.author: davidmu
ms.openlocfilehash: 83a0b1be1aba48146aa1aaedb36ad9d9d23f17d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="application-gateway-health-monitoring-overview"></a>Gateway estado de funcionamento monitorização descrição geral da aplicação

Gateway de aplicação do Azure por predefinição monitoriza o estado de funcionamento de todos os recursos no seu conjunto de back-end e remove automaticamente qualquer recurso considerado em mau estado de funcionamento do agrupamento. Gateway de aplicação continua a monitorizar as instâncias de mau estado de funcionamento e adiciona-a novamente para o bom conjunto de back-end assim que estes fiquem disponíveis e respondem a sondas de estado de funcionamento. Gateway de aplicação envia que as sondas de estado de funcionamento com a mesma porta que está definida nas definições de HTTP de back-end. Esta configuração assegura que a sonda consiste em testar a mesma porta que os clientes estariam a utilizar para ligar ao back-end.

![exemplo de sonda de gateway de aplicação][1]

Para além de utilizar a monitorização de sonda de estado de funcionamento do predefinida, também pode personalizar a sonda de estado de funcionamento de acordo com os requisitos da sua aplicação. Neste artigo, são abordadas predefinido e sondas de estado de funcionamento personalizados.

> [!NOTE]
> Se existir um NSG na sub-rede de Gateway de aplicação, intervalos de portas 65503 65534 devem ser abertos na sub-rede do Gateway de aplicação para o tráfego de entrada. Estas portas são necessárias para o estado de funcionamento do back-end API para trabalhar.

## <a name="default-health-probe"></a>Sonda de estado de funcionamento predefinida

Um gateway de aplicação é configurada automaticamente uma sonda do Estado de funcionamento predefinida quando não configurada qualquer configuração de sonda personalizada. O comportamento de monitorização funciona efetuando um pedido de HTTP para os endereços IP configurados para o conjunto de back-end. Para as pesquisas de predefinição se as definições de http de back-end são configuradas para HTTPS, a sonda utiliza HTTPS, bem como para testar os back-ends de estado de funcionamento.

Por exemplo: configurar o gateway de aplicação para utilizar servidores de back-end A, B e C para receber o tráfego de rede HTTP na porta 80. A monitorização de estado de funcionamento de predefinição testes os três servidores cada 30 segundos para um bom estado de funcionamento resposta HTTP. Uma resposta HTTP bom estado de funcionamento tem um [código de estado](https://msdn.microsoft.com/library/aa287675.aspx) entre 200 e 399.

Se falhar a verificação de pesquisa predefinida para o servidor A, o gateway de aplicação remove-o conjunto de back-end e deixa de tráfego de rede que fluem para este servidor. A sonda predefinida ainda continua a verificar a existência de servidor um cada 30 segundos. Quando A responde com êxito para um pedido de uma pesquisa de estado de funcionamento predefinida, que é adicionado novamente como bom estado de funcionamento para o conjunto de back-end e tráfego começa a fluir novamente para o servidor.

### <a name="default-health-probe-settings"></a>Predefinições de sonda de estado de funcionamento

| Propriedade de pesquisa | Valor | Descrição |
| --- | --- | --- |
| URL de sonda |http://127.0.0.1:\<porta\>/ |Caminho de URL |
| intervalo |30 |Intervalo de pesquisa em segundos |
| Tempo limite |30 |Sonda de tempo limite em segundos |
| Limiar de mau estado de funcionamento |3 |Sonda de contagem de repetições. O servidor de back-end está marcado como após o número de falhas de sonda consecutivas atinge o limiar de mau estado de funcionamento. |

> [!NOTE]
> A porta é a mesma porta as definições de HTTP de back-end.

A sonda predefinida observa apenas http://127.0.0.1:\<porta\> para determinar o estado de funcionamento. Se precisar de configurar a sonda de estado de funcionamento para ir para um URL personalizado ou modificar todas as outras definições, tem de utilizar das sondas personalizadas conforme descrito nos passos seguintes:

## <a name="custom-health-probe"></a>Sonda de estado de funcionamento personalizado

Das sondas personalizadas permitem-lhe ter um controlo mais granular sobre a monitorização de estado de funcionamento. Quando utilizar das sondas personalizadas, pode configurar o intervalo de pesquisa, o URL e caminho para testar e quantos falhadas as respostas de aceitar antes de marcar a instância de conjunto back-end como estando danificado.

### <a name="custom-health-probe-settings"></a>Definições de pesquisa de estado de funcionamento personalizado

A tabela seguinte fornece definições para as propriedades de uma sonda do Estado de funcionamento personalizados.

| Propriedade de pesquisa | Descrição |
| --- | --- |
| Nome |Nome da sonda. Este nome é utilizado para fazer referência para a sonda nas definições de HTTP de back-end. |
| Protocolo |Protocolo utilizado para enviar a pesquisa. A sonda utiliza o protocolo definido nas definições de HTTP de back-end |
| Anfitrião |Nome de anfitrião para enviar a pesquisa. Aplicável apenas quando vários sites está configurada no Gateway de aplicação, caso contrário, utilize '127.0.0.1'. Este valor é diferente do nome de anfitrião VM. |
| Caminho |Caminho relativo da sonda. O caminho válido começa a partir do '/'. |
| intervalo |Intervalo de pesquisa em segundos. Este valor é o intervalo de tempo entre dois sondas consecutivos. |
| Tempo limite |Sonda de tempo limite em segundos. Se uma resposta válida não foram recebida durante este período de tempo limite, a pesquisa está marcada como falhado.  |
| Limiar de mau estado de funcionamento |Sonda de contagem de repetições. O servidor de back-end está marcado como após o número de falhas de sonda consecutivas atinge o limiar de mau estado de funcionamento. |

> [!IMPORTANT]
> Se o Gateway de aplicação está configurada para um único site, por predefinição, o anfitrião nome deve ser especificado como '127.0.0.1', a menos que caso contrário configurado na sonda personalizada.
> Para referência uma sonda personalizada é enviada para \<protocolo\>://\<anfitrião\>:\<porta\>\<caminho\>. A porta utilizada será a mesma porta, tal como definido nas definições de HTTP de back-end.

## <a name="next-steps"></a>Passos seguintes
Depois de aprendizagem sobre a monitorização de estado de funcionamento do Gateway de aplicação, pode configurar um [sonda do Estado de funcionamento personalizado](application-gateway-create-probe-portal.md) no portal do Azure ou um [sonda do Estado de funcionamento personalizado](application-gateway-create-probe-ps.md) com o PowerShell e o Azure Resource Manager modelo de implementação.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
