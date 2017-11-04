---
title: "Tipos de ponto final do Gestor de tráfego | Microsoft Docs"
description: "Este artigo explica os diferentes tipos de pontos finais que podem ser utilizados com o Gestor de tráfego do Azure"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 4e506986-f78d-41d1-becf-56c8516e4d21
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: kumud
ms.openlocfilehash: 792712e3e529d77ff20a7603b5fbf028ca60f8c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="traffic-manager-endpoints"></a>Pontos finais do Gestor de tráfego
Gestor de tráfego do Microsoft Azure permite-lhe controlar a forma como o tráfego de rede é distribuído para implementações de aplicações em execução nos centros de dados diferentes. Configure cada implementação de aplicação como um ponto 'final' no Traffic Manager. Quando o Gestor de tráfego recebe um pedido DNS, escolhe um ponto final disponível para devolver na resposta DNS. Gestor de tráfego bases de escolha sobre o estado de ponto final atual e o método de encaminhamento de tráfego. Para obter mais informações, consulte [como Gestor de tráfego funciona](traffic-manager-how-traffic-manager-works.md).

Existem três tipos de ponto final suportada pelo Gestor de tráfego:
* **Pontos finais Azure** são utilizadas para serviços alojados no Azure.
* **Pontos finais externos** são utilizadas para serviços alojados fora do Azure, no local ou com um fornecedor de alojamento diferente.
* **Pontos finais de aninhada** são utilizados para combinar perfis do Traffic Manager criar esquemas de encaminhamento de tráfego mais flexíveis para suporte as necessidades de implementações maiores, mais complexas.

Não há nenhuma restrição para como pontos finais de diferentes tipos são combinados num único perfil do Traffic Manager. Cada perfil pode conter qualquer combinação de tipos de ponto final.

As secções seguintes descrevem cada tipo de ponto final mais detalhadamente.

## <a name="azure-endpoints"></a>Pontos finais Azure

Pontos finais do Azure são utilizados para serviços baseados no Azure no Traffic Manager. São suportados os seguintes tipos de recursos do Azure:

* 'Clássico' VMs de IaaS e PaaS serviços em nuvem.
* Aplicações Web
* Recursos PublicIPAddress (que podem ser ligados às VMs diretamente ou através de um balanceador de carga do Azure). O publicIpAddress tem de ter um nome DNS atribuído a ser utilizada num perfil do Traffic Manager.

Recursos PublicIPAddress são recursos do Azure Resource Manager. Não existem no modelo de implementação clássica. Deste modo, são apenas suportados no Gestor de tráfego do Azure Resource Manager experiências. Outros tipos de ponto final são suportados através do modelo de implementação clássica e Resource Manager.

Quando utilizar pontos finais do Azure, o Gestor de tráfego Deteta quando uma VM do IaaS 'Clássico', o serviço em nuvem ou uma aplicação Web está parada e iniciada. Este estado é apresentado no estado de ponto final. Consulte [monitorização de pontos finais do Gestor de tráfego](traffic-manager-monitoring.md#endpoint-and-profile-status) para obter mais detalhes. Quando o serviço subjacente estiver parado, o Gestor de tráfego não efetua verificações de estado de funcionamento do ponto final ou tráfego direto para o ponto final. Não há eventos de faturação do Gestor de tráfego ocorrerem para a instância de paragem. Quando o serviço for reiniciado, a retoma de faturação e o ponto final é elegível para receber tráfego. Esta deteção não é aplicável aos pontos finais de PublicIpAddress.

## <a name="external-endpoints"></a>Pontos finais externos

Pontos finais externos são utilizados para serviços fora do Azure. Por exemplo, um serviço alojado no local ou com um fornecedor diferente. Pontos finais externos podem ser utilizados individualmente ou combinados com pontos finais do Azure no perfil do Traffic Manager do mesmo. Combinar pontos finais do Azure com pontos finais externos permite vários cenários:

* Um modelo de ativação pós-falha ativo-ativo ou ativo-passivo, utilize o Azure para fornecer redundância de aumento de existente no local aplicação.
* Para reduzir a latência de aplicação para utilizadores em todo o mundo, expanda uma aplicação no local existente a localizações geográficas adicionais no Azure. Para obter mais informações, consulte [Gestor de tráfego 'Desempenho' Encaminhamento de tráfego](traffic-manager-routing-methods.md#performance).
* Azure de utilização para fornecer capacidade adicional para existente no local aplicação, continuamente ou como uma solução de 'rajada para a nuvem' para satisfazer um pico de pedidos de pedido.

Em certos casos, é útil utilizar pontos finais externos para fazer referência a serviços do Azure (para obter exemplos, consulte o [FAQ](traffic-manager-faqs.md#traffic-manager-endpoints)). Neste caso, as verificações de estado de funcionamento são cobradas à taxa de pontos finais do Azure, não é a taxa de pontos finais externos. No entanto, ao contrário dos pontos finais do Azure, se parar ou eliminar o serviço subjacente, funcionamento verifique faturação continua até desativar ou eliminar o ponto final no Traffic Manager.

## <a name="nested-endpoints"></a>Pontos finais aninhados

Pontos finais aninhados combinam vários perfis do Traffic Manager criar os esquemas de encaminhamento de tráfego flexíveis e suporte as necessidades de implementações maiores, complexas. Com pontos finais de aninhados, um perfil de 'child' é adicionado como um ponto final para um perfil de 'parent'. Perfis de subordinados e principais podem conter outros pontos finais de qualquer tipo, incluindo outros perfis aninhados. Para obter mais informações, consulte [aninhada perfis do Traffic Manager](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Web Apps como pontos finais

São aplicáveis algumas considerações adicionais quando configurar as aplicações Web, como pontos finais no Traffic Manager:

1. Apenas as aplicações Web a SKU "Padrão" ou acima são elegíveis para utilização com o Gestor de tráfego. As tentativas de adicionar uma aplicação Web de um SKU inferior falharem. Desatualização de SKU de uma aplicação Web existente resulta no Gestor de tráfego já não enviam tráfego para essa aplicação Web.
2. Quando um ponto final recebe um pedido de HTTP, utiliza o cabeçalho "anfitrião" no pedido para determinar a aplicação Web deve processar o pedido. O cabeçalho de anfitrião contém o nome DNS utilizado para iniciar o pedido, por exemplo 'contosoapp.azurewebsites.net'. Para utilizar um nome DNS diferente com a sua aplicação Web, o nome DNS tem de ser registado como um nome de domínio personalizado para a aplicação. Ao adicionar um ponto final da aplicação Web como um ponto final do Azure, o nome DNS de perfil do Gestor de tráfego é automaticamente registado para a aplicação. Este registo é removido automaticamente quando o ponto final é eliminado.
3. Cada perfil de Gestor de tráfego pode ter no máximo uma aplicação Web ponto final de cada região do Azure. Para contornar esta restrição, pode configurar uma aplicação Web como um ponto final externo. Para obter mais informações, consulte o [FAQ](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Ativar e desativar pontos finais

Desativar um ponto final no Traffic Manager pode ser útil para remover temporariamente o tráfego de um ponto final que esteja no modo de manutenção ou que vai ser implementado. Depois do ponto final está novamente em execução, pode ser ativada novamente.

Pontos finais podem ser ativados e desativados através do portal do Gestor de tráfego, do PowerShell, a CLI ou a API REST, todos os que são suportados no modelo de implementação clássica e Resource Manager.

> [!NOTE]
> Desativar um ponto final do Azure tem nada a ver com o estado de implementação no Azure. Um serviço do Azure (tal como uma VM ou aplicação Web permanece em execução e podem receber tráfego, mesmo quando desativado no Traffic Manager. Tráfego pode ser resolvido diretamente para a instância de serviço, em vez de através do nome DNS de perfil do Traffic Manager. Para obter mais informações, consulte [como funciona o Gestor de tráfego](traffic-manager-how-traffic-manager-works.md).

A elegibilidade atual de cada ponto final para receber tráfego depende dos seguintes fatores:

* O estado do perfil (ativado/desativado)
* O estado de ponto final (ativado/desativado)
* Verifica os resultados do Estado de funcionamento para esse ponto final

Para obter mais informações, consulte [monitorização de pontos finais do Gestor de tráfego](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Uma vez que o Gestor de tráfego funciona ao nível do DNS, é possível a influenciar as ligações existentes para qualquer ponto final. Quando um ponto final não estiver disponível, o Gestor de tráfego direciona novas ligações com outro ponto final disponível. No entanto, o anfitrião atrás o ponto final desativado ou mau estado de funcionamento pode continuar a receber tráfego através de ligações existentes até que essas sessões estão terminadas. As aplicações devem limitar a duração de sessão para permitir o tráfego drenar de ligações existentes.

Se todos os pontos finais num perfil estiverem desativados ou se o próprio perfil estiver desativado, o Gestor de tráfego envia uma resposta de 'NXDOMAIN' para uma nova consulta DNS.


## <a name="next-steps"></a>Passos seguintes

* Saiba [como funciona o Gestor de tráfego](traffic-manager-how-traffic-manager-works.md).
* Saiba mais sobre o Gestor de tráfego [ativação pós-falha automática e monitorização do ponto final](traffic-manager-monitoring.md).
* Saiba mais sobre o Gestor de tráfego [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md).
