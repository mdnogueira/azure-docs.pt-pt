---
title: Controlo de acesso de suporte de firewall da BD do Cosmos & IP Azure | Microsoft Docs
description: "Saiba como utilizar políticas de controlo de acesso IP para o suporte de firewall em contas de base de dados de base de dados do Azure Cosmos."
keywords: Controlo de acesso IP, o suporte de firewall
services: cosmos-db
author: shahankur11
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: c1b9ede0-ed93-411a-ac9a-62c113a8e887
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: ankshah
ms.openlocfilehash: 1ceaa834ff68d5dca4abce561f9185e89af582af
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="azure-cosmos-db-firewall-support"></a>Suporte de firewall do Cosmos BD do Azure
Para proteger os dados armazenados numa conta de base de dados do Azure Cosmos DB, base de dados do Azure Cosmos tem suporte fornecido para um segredo com base [modelo de autorização](https://msdn.microsoft.com/library/azure/dn783368.aspx) que utiliza um código de autenticação de mensagem com base em Hash seguro (HMAC). Agora, para além do modelo de autorização baseada em segredo BD do Cosmos do Azure suporta a política orientadas por controlos de acesso baseado em IP para o suporte de firewall de entrada. Este modelo é semelhante para as regras de firewall de um sistema de bases de dados tradicionais e fornece um nível adicional de segurança para a conta de base de dados de base de dados do Azure Cosmos. Com este modelo, pode agora configurar uma conta de base de dados de base de dados do Azure Cosmos para ser acessível apenas a partir de um conjunto de máquinas aprovado e/ou serviços em nuvem. Acesso a recursos de base de dados do Azure Cosmos destes conjuntos de serviços e máquinas aprovados ainda requerem o autor da chamada apresentar um token de autorização válida.

## <a name="ip-access-control-overview"></a>Descrição geral de controlo de acesso de IP
Por predefinição, uma conta de base de dados de base de dados do Azure Cosmos é acessível a partir da internet pública, desde que o pedido é acompanhado por um token de autorização válida. Para configurar o controlo de acesso baseado em política IP, o utilizador tem de fornecer o conjunto de endereços IP ou intervalos de endereços IP no formato CIDR ser incluído como uma lista de permitidos do cliente IPs de uma conta de base de dados específica. Depois desta configuração é aplicada, todos os pedidos provenientes de máquinas fora desta lista de permitidos serão bloqueados pelo servidor.  A ligação ao processamento de fluxo para o controlo de acesso baseado em IP é descrita no diagrama seguinte:

![Diagrama que mostra o processo de ligação para o controlo de acesso baseado em IP](./media/firewall-support/firewall-support-flow.png)

## <a name="connections-from-cloud-services"></a>Ligações a partir de serviços cloud
No Azure, serviços em nuvem são uma forma comum para o alojamento de lógica de serviço de camada média utilizando a BD do Cosmos do Azure. Para ativar o acesso a uma conta de base de dados de base de dados do Azure Cosmos de um serviço em nuvem, o endereço IP público do serviço de nuvem tem de ser adicionado à lista permitida de endereços IP associados à sua conta de base de dados de base de dados do Azure Cosmos por [configurar a política de controlo de acesso IP](#configure-ip-policy).  Isto garante que todas as instâncias de função dos serviços em nuvem tem acesso à sua conta de base de dados de base de dados do Azure Cosmos. Pode obter endereços IP para os seus serviços em nuvem no portal do Azure, conforme mostrado na captura de ecrã seguinte:

![Captura de ecrã que mostra o endereço IP público para um serviço em nuvem apresentado no portal do Azure](./media/firewall-support/public-ip-addresses.png)

Quando ampliar o seu serviço em nuvem, adicionando as instâncias de função adicionais, as novas instâncias terão automaticamente acesso à conta de base de dados do Azure Cosmos DB, uma vez que fazem parte do serviço em nuvem do mesmo.

## <a name="connections-from-virtual-machines"></a>Ligações a partir de máquinas virtuais
[Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) ou [conjuntos de dimensionamento de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) também podem ser utilizados para alojar os serviços de camada média utilizando a BD do Cosmos do Azure.  Para configurar a conta de base de dados de base de dados do Azure Cosmos para permitir o acesso a partir de máquinas virtuais, os endereços IP públicos de máquina virtual e/ou conjunto de dimensionamento da máquina virtual tem de ser configurados como um dos endereços IP permitidos para a sua conta de base de dados de base de dados do Azure Cosmos por [configurar a política de controlo de acesso IP](#configure-ip-policy). Pode obter endereços IP para máquinas virtuais no portal do Azure, conforme mostrado na captura de ecrã seguinte.

![Captura de ecrã com um endereço IP público para uma máquina virtual apresentado no portal do Azure](./media/firewall-support/public-ip-addresses-dns.png)

Quando adicionar as instâncias de máquina virtual adicional para o grupo, são automaticamente fornecidos acesso à sua conta de base de dados de base de dados do Azure Cosmos.

## <a name="connections-from-the-internet"></a>Ligações a partir da internet
Ao aceder a uma conta de base de dados de base de dados do Azure Cosmos de um computador com a internet, o endereço IP do cliente ou um intervalo de endereços IP da máquina tem de ser adicionado à lista de permitidos do endereço IP para a conta de base de dados de base de dados do Azure Cosmos. 

## <a name="connections-from-azure-paas-service"></a>Ligações do serviço de Azure PaaS 
No Azure, PaaS serviços como o Azure Stream analytics, as funções do Azure são utilizadas em conjunto com a base de dados do Azure Cosmos. Para ativar o acesso à conta de base de dados de base de dados do Azure Cosmos destes tipos de serviços cujo endereço IP não está disponível de imediato, o endereço IP do 0.0.0.0 tem de ser adicionado à lista permitida de endereços IP associados a sua conta de base de dados de base de dados do Azure Cosmos por [configurar a política de controlo de acesso IP](#configure-ip-policy).  Isto garante que os serviços do Azure PaaS podem aceder a uma conta de base de dados do Azure Cosmos com esta regra. 

 ## <a id="configure-ip-policy"></a>Configurar a política de controlo de acesso IP
A política de controlo de acesso IP pode ser definida no portal do Azure ou através de programação através de [CLI do Azure](cli-samples.md), [Azure Powershell](powershell-samples.md), ou o [REST API](/rest/api/documentdb/) atualizando o `ipRangeFilter` propriedade. Endereços/intervalos IP tem de ser vírgula separados e não pode conter quaisquer espaços. Exemplo: "13.91.6.132,13.91.6.1/24". Ao atualizar a sua conta de base de dados através destes métodos, não se esqueça de preencher todas as propriedades para impedir que a reposição das predefinições.

> [!NOTE]
> Ao ativar uma política de controlo de acesso IP para a sua conta de base de dados de base de dados do Azure Cosmos, todo o acesso à sua conta de base de dados de base de dados do Azure Cosmos máquinas fora configurada permitido lista de intervalos de endereços IP são bloqueados. Em virtude neste modelo, a operação de plane de dados do portal de navegação será também bloqueado para assegurar a integridade do controlo de acesso.

Para simplificar o desenvolvimento, o portal do Azure ajuda-o a identificar e adicione o IP do seu computador cliente à lista de permitidos, para que as aplicações com o seu computador possam aceder a conta de base de dados do Azure Cosmos. O endereço IP do cliente é detetado como visto pelo portal. Poderá ser o endereço IP do cliente do seu computador, mas também poderia ser o endereço IP do gateway de rede. Não se esqueça de removê-lo antes de ir para produção.

Para definir a política de controlo de acesso IP no portal do Azure, navegue para o painel de conta de base de dados do Azure Cosmos, clique em **Firewall** no menu de navegação, em seguida, clique em **ON** 

![Captura de ecrã que mostra como abrir o painel de Firewall no portal do Azure](./media/firewall-support/azure-portal-firewall.png)

No painel de novo, especifique se o portal do Azure pode aceder à conta de e adicionar outros intervalos conforme adequado, em seguida, clique em e endereços **guardar**.  

> [!NOTE]
> Quando ativar uma política de controlo de acesso IP, terá de adicionar o endereço IP para o portal do Azure manter o acesso. Os endereços IP portais são:
> |Região|Endereço IP|
> |------|----------|
> |Todas as regiões exceto às especificadas abaixo|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|
> |Alemanha|51.4.229.218|
> |China|139.217.8.252|
> |Governo dos EUA|52.244.48.71|
>

![Captura de ecrã que mostra um como para configurar as definições da firewall no portal do Azure](./media/firewall-support/azure-portal-firewall-configure.png)

## <a name="troubleshooting-the-ip-access-control-policy"></a>A política de controlo de acesso IP de resolução de problemas
### <a name="portal-operations"></a>Operações de portais
Ao ativar uma política de controlo de acesso IP para a sua conta de base de dados de base de dados do Azure Cosmos, todo o acesso à sua conta de base de dados de base de dados do Azure Cosmos máquinas fora configurada permitido lista de intervalos de endereços IP são bloqueados. Por conseguinte, se pretender permitir operações de plane dados portal como navegação coleções e consultar documentos, terá de permitir explicitamente o acesso ao portal do Azure utilizando o **Firewall** painel no portal. 

![Captura de ecrã que mostra um como para permitir o acesso ao portal do Azure](./media/firewall-support/azure-portal-access-firewall.png)

### <a name="sdk--rest-api"></a>SDK & Rest API
Para motivos de segurança, o acesso através do SDK ou da REST API do máquinas não na lista de permitidos irão devolver uma resposta não foi encontrado genérico 404 com sem detalhes adicionais. Certifique-se o IP permitido lista configurada para a conta de base de dados de base de dados do Azure Cosmos para garantir que a configuração da política correto é aplicada à sua conta de base de dados de base de dados do Azure Cosmos.

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre a rede sugestões de desempenho relacionados, consulte [sugestões de desempenho](performance-tips.md).

