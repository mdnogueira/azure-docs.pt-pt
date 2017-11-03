---
title: Perguntas mais frequentes do reencaminhamento do Azure | Microsoft Docs
description: Obtenha respostas a algumas perguntas mais frequentes sobre o reencaminhamento do Azure.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2017
ms.author: sethm
ms.openlocfilehash: e8c146f4b6d02449be6ad9e991e52db8dfd58e04
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-relay-faqs"></a>Perguntas mais frequentes do reencaminhamento do Azure

Este artigo responde a algumas perguntas mais frequentes (FAQ) sobre [Azure reencaminhamento](https://azure.microsoft.com/services/service-bus/). Para o Azure preços e suporte informações gerais, consulte [perguntas frequentes do suporte do Azure](http://go.microsoft.com/fwlink/?LinkID=185083).

## <a name="general-questions"></a>Perguntas gerais
### <a name="what-is-azure-relay"></a>O que é o Reencaminhamento do Azure?
O [serviço de reencaminhamento do Azure](relay-what-is-it.md) facilita as aplicações híbridas, ajudando-o a mais segura de expor serviços que se encontram numa rede empresarial na nuvem pública. Pode expor os serviços sem abrindo uma ligação de firewall e sem exigir alterações intrusivas na infraestrutura da rede empresarial.

### <a name="what-is-a-relay-namespace"></a>O que é um espaço de nomes de reencaminhamento?
A [espaço de nomes](relay-create-namespace-portal.md) é um contentor de âmbito que pode utilizar para recursos de reencaminhamento de endereço na sua aplicação. Tem de criar um espaço de nomes para utilizar reencaminhamento. Esta é uma introdução aos passos primeiro.

### <a name="what-happened-to-service-bus-relay-service"></a>O que aconteceu ao serviço de reencaminhamento do Service Bus?
O serviço de reencaminhamento de barramento de serviço anteriormente denominado chama-se agora o reencaminhamento de WCF. Pode continuar a utilizar este serviço como habitualmente. A funcionalidade de ligações híbridas é uma versão atualizada de um serviço que é foi transplanted dos BizTalk Services do Azure. Reencaminhamento de WCF e as ligações híbridas continuam a ser suportada.

## <a name="pricing"></a>Preços
Esta secção responde a algumas perguntas mais frequentes sobre o reencaminhamento do preço de estrutura. Também pode ver [perguntas frequentes do suporte do Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para o Azure geral obter informações sobre preços. Para obter informações completas sobre os preços de reencaminhamento, consulte [detalhes de preços do Service Bus][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Como é cobram para as ligações híbridas e reencaminhamento de WCF
Para obter informações completas sobre os preços de reencaminhamento, consulte o [ligações híbridas e WCF reencaminhamentos] [ Pricing overview] tabela no barramento de serviço a página de detalhes de preços. Para além de que os anotou nessa página preços, são-lhe cobrados para transferências de dados associados de saída fora do datacenter no qual a aplicação está aprovisionada.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Como estou cobrado para as ligações híbridas?
Seguem-se três cenários de faturação de exemplo para as ligações híbridas:

*   Cenário 1:
    *   Tem um serviço de escuta único, tal como uma instância do Gestor de ligações híbridas instalado e em execução contínua para o mês completo.
    *   Enviar 3 GB de dados em toda a ligação durante o mês. 
    *   O custo total é $5.
*   Cenário 2:
    *   Tem um serviço de escuta único, tal como uma instância do Gestor de ligações híbridas instalado e em execução contínua para o mês completo.
    *   Enviar 10 GB de dados em toda a ligação durante o mês.
    *   O custo total é $7.50. É 5 $ para a ligação e os primeiros 5 GB + $2.50 para o adicional 5 GB de dados.
*   Cenário 3:
    *   Ter duas instâncias, A e B, do Gestor de ligações híbridas instalado e em execução contínua para o mês completo.
    *   Enviar 3 GB de dados através de ligação A durante o mês.
    *   Enviar 6 GB de dados através de ligação B durante o mês.
    *   O custo total é $10.50. É 5 $ para ligação D + 5 $ para ligação B + $0.50 (por gigabyte sexto numa ligação B).

Tenha em atenção que os preços utilizados nos exemplos são aplicáveis apenas durante o período de pré-visualização de ligações híbridas. Os preços estão sujeitos a alterações após a disponibilidade geral das ligações híbridas.

### <a name="how-are-hours-calculated-for-relay"></a>Como são calculadas horas de reencaminhamento?

Reencaminhamento de WCF está disponível apenas em espaços de nomes do escalão Standard. Preços e [quotas de ligação](../service-bus-messaging/service-bus-quotas.md) para reencaminhamentos; caso contrário, não foram alteradas. Isto significa que reencaminhamentos continuam a ser cobrados com base no número de mensagens (operações de não) e horas de reencaminhamento. Para obter mais informações, consulte o ["Ligações de híbrida e reencaminhamentos de WCF"](https://azure.microsoft.com/pricing/details/service-bus/) tabela na página de detalhes de preço.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>E se tiver mais do que um serviço de escuta ligado a um reencaminhamento específico?
Em alguns casos, um reencaminhamento único pode ter vários serviços de escuta ligados. Um reencaminhamento é considerado aberto quando o serviço de escuta, pelo menos, um reencaminhamento está ligado ao mesmo. Adicionar serviços de escuta para resultados de um reencaminhamento abrir em horas de reencaminhamento adicionais. O número de remetentes de reencaminhamento (clientes que invocam ou enviam mensagens para reencaminhamentos) que estão ligados a um reencaminhamento não afeta o cálculo das horas de reencaminhamento.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Como é calculada a medição de mensagens para WCF reencaminhamentos?
(**Isto aplica-se apenas a reencaminhamentos WCF. As mensagens não são um custo de ligações híbridas.** )

Em geral, as mensagens de sujeito a faturação de reencaminhamentos são calculadas utilizando o mesmo método que é utilizado para entidades mediadas (filas, tópicos e subscrições), descritas anteriormente. No entanto, existem algumas diferenças importantes.

Enviar uma mensagem para um reencaminhamento do Service Bus é tratado como um "completa" através do envio para o serviço de escuta de reencaminhamento que recebe a mensagem. Não é tratado como uma operação de envio para o reencaminhamento do Service Bus, seguido por uma entrega para o serviço de escuta de reencaminhamento. Um pedido-resposta de estilo invocação de serviço (de até 64 KB) contra um reencaminhamento resultados de serviço de escuta no duas mensagens facturável: uma mensagem sujeito a faturação para o pedido e uma mensagem facturável a resposta (partindo do princípio de resposta também é 64 KB ou inferior). Isto é diferente de utilizar uma fila para mediar entre um cliente e um serviço. Se utilizar uma fila para mediar entre um cliente e um serviço, o mesmo padrão de pedido-resposta necessita de enviar um pedido para a fila, seguida de uma dequeue/entrega da fila para o serviço. Isto é seguido de enviar uma resposta para outra fila e uma dequeue/entrega dessa fila para o cliente. O padrão de fila mediated utilizando os mesmo pressupostos tamanho ao longo (até 64 KB), resulta em 4 mensagens sujeito a faturação. Deverá ser-lhe cobrado duas vezes o número de mensagens para implementar o mesmo padrão realizar através de reencaminhamento. Obviamente, existem vantagens para utilizar filas para alcançar este padrão, tais como durabilidade e nivelamento de carga. Estas vantagens poderão indicar o custo adicional.

Reencaminhamentos abertos utilizando o **netTCPRelay** enlace WCF tratar mensagens, mas não como as mensagens individuais, como um fluxo de dados que fluem através do sistema. Quando utiliza este enlace, apenas o remetente e o serviço de escuta tem visibilidade sobre os pacotes das mensagens individuais enviados e recebidos. Para reencaminha que utilizam o **netTCPRelay** enlace, todos os dados é tratado como uma transmissão em fluxo para calcular mensagens sujeito a faturação. Neste caso, o Service Bus calcula a quantidade total de dados enviado ou recebido através de cada reencaminhamento individuais numa base de 5 minutos. Em seguida, divide essa quantidade total de dados por 64 KB para determinar o número de mensagens Facturável para esse reencaminhamento durante esse período de tempo.

## <a name="quotas"></a>Quotas
| Nome de quota | Âmbito | Tipo | Comportamento quando excedido | Valor |
| --- | --- | --- | --- | --- |
| Serviços de escuta em simultâneo num reencaminhamento |Entidade |Estático |Os pedidos subsequentes para ligações adicionais são rejeitados e uma exceção é recebida pelo código da chamada. |25 |
| Serviços de escuta de reencaminhamento em simultâneo |Systemwide |Estático |Os pedidos subsequentes para ligações adicionais são rejeitados e uma exceção é recebida pelo código da chamada. |2,000 |
| Ligações de reencaminhamento em simultâneo por todos os pontos finais de reencaminhamento num espaço de nomes de serviço |Systemwide |Estático |- |5,000 |
| Pontos finais de reencaminhamento por espaço de nomes de serviço |Systemwide |Estático |- |10,000 |
| Tamanho da mensagem [NetOnewayRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.netonewayrelaybinding.aspx) e [NetEventRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.neteventrelaybinding.aspx) reencaminha |Systemwide |Estático |Mensagens a receber que excedem destas quotas são rejeitadas e uma exceção é recebida pelo código da chamada. |64 KB |
| Tamanho da mensagem [HttpRelayTransportBindingElement](https://msdn.microsoft.com/library/microsoft.servicebus.httprelaytransportbindingelement.aspx) e [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) reencaminha |Systemwide |Estático |- |Ilimitado |

### <a name="does-relay-have-any-usage-quotas"></a>A reencaminhamento tem quaisquer quotas de utilização?
Por predefinição, a qualquer serviço de nuvem, o Microsoft define uma quota de utilização mensal agregado que é calculado em todas as subscrições de um cliente. Compreendemos que por vezes às suas necessidades poderão exceder estes limites. Pode contactar o serviço de cliente em qualquer altura, para que possa compreender as suas necessidades e ajustar estes limites adequadamente. As quotas de utilização de agregação para o Service Bus, são os seguintes:

* mensagens de mil milhões de 5
* horas de reencaminhamento de milhões de 2

Apesar do o direito de desativar uma conta que excede o respetivas quotas de utilização mensais de reserva, fornecemos notificação por correio electrónico, iremos tornar múltiplos tenta contactar o cliente antes de efetuar qualquer ação. Os clientes que excedem destas quotas são ainda responsáveis por eventuais encargos em excesso.

### <a name="naming-restrictions"></a>Restrições de nomenclatura
Um nome de espaço de nomes de reencaminhamento tem de ser entre 6 e 50 carateres de comprimento.

## <a name="subscription-and-namespace-management"></a>Gestão de subscrição e o espaço de nomes
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Como posso migrar um espaço de nomes para outra subscrição do Azure?

Para mover um espaço de nomes de uma subscrição do Azure para outra subscrição, pode utilizar o [portal do Azure](https://portal.azure.com) ou utilizar comandos do PowerShell. Para mover um espaço de nomes para outra subscrição, o espaço de nomes já deve estar ativo. O utilizador a executar os comandos tem de ser um utilizador de administrador em subscrições de origem e de destino.

#### <a name="azure-portal"></a>Portal do Azure

Para utilizar o portal do Azure para migrar espaços de nomes de reencaminhamento do Azure a partir de uma subscrição a outra subscrição, consulte [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md#use-portal). 

#### <a name="powershell"></a>PowerShell

Para utilizar o PowerShell para mover um espaço de nomes de uma subscrição do Azure para outra subscrição, utilize a seguinte sequência de comandos. Para executar esta operação, o espaço de nomes já deve estar Active Directory e o utilizador a executar os comandos do PowerShell tem de ser um utilizador de administrador em subscrições de origem e de destino.

```powershell
# Create a new resource group in the target subscription.
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Quais são algumas das exceções geradas pelo APIs de reencaminhamento do Azure e sugerida as ações que pode tomar?
Para obter uma descrição de exceções comuns e as ações sugeridas que pode tomar, consulte [exceções de reencaminhamento][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>O que é uma assinatura de acesso partilhado, e que idiomas posso utilizar para gerar uma assinatura?
Assinaturas de acesso partilhado (SAS) são um mecanismo de autenticação com base nos hashes segurados SHA-256 ou URI. Para obter informações sobre como gerar as suas próprias assinaturas no nó, PHP, Java, C e c#, consulte [autenticação de Service Bus com assinaturas de acesso partilhado][Shared Access Signatures].

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>É possível com pontos finais de reencaminhamento de lista branca?
Sim. O cliente de reencaminhamento efetua as ligações ao serviço de reencaminhamento do Azure, utilizando nomes de domínio completamente qualificado. Os clientes, podem adicionar uma entrada para `*.servicebus.windows.net` nas firewalls que suportam a listas brancas DNS.

## <a name="next-steps"></a>Passos seguintes
* [Criar um espaço de nomes](relay-create-namespace-portal.md)
* [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao Node](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared access signatures]: ../service-bus-messaging/service-bus-sas.md