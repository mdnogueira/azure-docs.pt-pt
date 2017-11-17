---
title: "Pontos finais de serviço de rede virtuais e regras para a SQL Database do Azure | Microsoft Docs"
description: "Marcar uma sub-rede como um ponto final de serviço de rede Virtual. Em seguida, o ponto final como uma regra de rede virtual para a ACL de SQL Database do Azure. A base de dados SQL, em seguida, aceita comunicações de todas as máquinas virtuais e outros nós na sub-rede."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: VNet Service endpoints
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 11/13/2017
ms.author: genemi
ms.openlocfilehash: ba9612eb1a8c57a03ab9d0cb3c16ac5b76891b0e
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-sql-database"></a>Utilizar pontos finais do serviço de rede Virtual e as regras para a SQL Database do Azure

*Regras de rede virtual* são uma funcionalidade de segurança de firewall que controla se o servidor da SQL Database do Azure aceita comunicações que são enviadas do sub-redes específicas em redes virtuais. Este artigo explica por que razão a funcionalidade de regra de rede virtual, por vezes, é a melhor opção em segurança permitir comunicações para a base de dados do SQL do Azure.

Para criar uma regra de rede virtual, tem primeiro de existir um [ponto final do serviço de rede virtual] [ vm-virtual-network-service-endpoints-overview-649d] para a regra para efeitos de referência.


> [!NOTE]
> Para a base de dados SQL do Azure, esta funcionalidade está disponível na pré-visualização para as seguintes regiões do Azure:
>
> - WestCentralUS, WestUS2 e EastUS.


#### <a name="how-to-create-a-virtual-network-rule"></a>Como criar uma regra de rede virtual

Se apenas a criar uma regra de rede virtual, pode avançar diretamente para os passos e uma explicação [posteriormente neste artigo](#anchor-how-to-by-using-firewall-portal-59j).






<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologia e descrição

**Rede virtual:** pode fazer com que as redes virtuais associadas à subscrição do Azure.

**Sub-rede:** uma rede virtual contém **sub-redes**. Quaisquer máquinas virtuais (VMs) do Azure que tiver são atribuídas a sub-redes. Uma sub-rede pode conter várias VMs ou outros nós de computação. Computação nós que estão fora da rede virtual não é possível aceder à sua rede virtual, exceto se configurar a segurança para permitir o acesso.

**Ponto final de serviço de rede virtual:** A [ponto final do serviço de rede Virtual] [ vm-virtual-network-service-endpoints-overview-649d] uma sub-rede cujos valores de propriedade incluem um ou mais nomes de tipo de serviço do Azure formal. Neste artigo está interessados no nome do tipo de **Microsoft.Sql**, que se refere ao serviço do Azure com o nome da base de dados do SQL Server.

**Regra de rede virtual:** uma regra de rede virtual para o servidor de base de dados do SQL Server é uma sub-rede que está listada na lista de controlo de acesso (ACL) do seu servidor de base de dados SQL. Para ser na ACL para a base de dados do SQL Server, a sub-rede tem de conter o **Microsoft.Sql** nome de tipo.

Uma regra de rede virtual indica que o servidor de base de dados SQL para aceitar comunicações de cada nó que está na sub-rede.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Vantagens de uma regra de rede virtual

Até que a ação, VMs as sub-redes não consegue comunicar com a base de dados do SQL Server. A lógica por detrás para escolher a abordagem de regra de rede virtual para permitir a comunicação requer um debate comparar e contraste que envolve as opções de segurança concorrentes disponibilizadas pela firewall.

#### <a name="a-allow-access-to-azure-services"></a>A. Permitir o acesso aos serviços do Azure

O painel de firewall tem um **ON/OFF** botão assinalada como **permitir o acesso aos serviços do Azure**. O **ON** definição permite as comunicações de todos os endereços IP do Azure e todas as sub-redes do Azure. Estes Azure IPs sub-redes poderão não ser detidas por si. Isto **ON** definição é provavelmente mais aberta que pretende que a base de dados do SQL Server para ser. A funcionalidade de regra de rede virtual oferece muito controlo finer granular.

#### <a name="b-ip-rules"></a>B. Regras IP

A firewall de base de dados SQL permite-lhe especificar os intervalos de endereços IP do que as comunicações são aceites na base de dados do SQL Server. Esta abordagem é adequada para estáveis endereços IP que estão fora da rede privada do Azure. Mas muitos nós dentro da rede privada do Azure estão configurados com *dinâmica* endereços IP. Endereços IP dinâmicos podem ser alteradas, por exemplo, quando a VM é reiniciada. Seria folly para especificar um endereço IP dinâmico numa regra de firewall, num ambiente de produção.

Pode salvage a opção de IP por obter um *estático* endereço IP para a VM. Para obter mais informações, consulte [configurar endereços IP privados para uma máquina virtual utilizando o portal do Azure][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

No entanto, a abordagem IP estática pode tornar-se difícil gerir, e é dispendioso quando terminar à escala. Regras de rede virtual são mais fáceis de estabelecer e gerir.

#### <a name="c-cannot-yet-have-sql-database-on-a-subnet"></a>C. Ainda não pode ter a base de dados SQL numa sub-rede

Se o servidor de base de dados do Azure SQL era um nó numa sub-rede na sua rede virtual, todos os nós dentro da rede virtual conseguiu comunicar com a base de dados do SQL Server. Neste caso, as suas VMs conseguiu comunicar com a base de dados SQL sem necessidade de quaisquer regras de rede virtual ou das regras IP.

No entanto a partir de Setembro de 2017, o serviço SQL Database do Azure não está ainda entre os serviços que podem ser atribuídos a uma sub-rede.






<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Detalhes sobre regras de rede virtual

Esta secção descreve vários detalhes sobre regras de rede virtual.

#### <a name="only-one-geographic-region"></a>Apenas uma região geográfica

Cada ponto final de serviço de rede Virtual aplica-se apenas uma região do Azure. O ponto final não ativar a outras regiões aceitar comunicações de sub-rede.

É limitada para a região que o seu ponto final subjacente aplica-se a qualquer regra de rede virtual.

#### <a name="server-level-not-database-level"></a>Ao nível do servidor, não ao nível da base de dados

Cada regra de rede virtual aplica-se ao seu servidor de SQL Database do Azure completa, não apenas para uma determinada base de dados no servidor. Por outras palavras, se aplica a regra de rede virtual ao nível do servidor, não ao nível da base de dados.

- Em contrapartida, as regras IP podem aplicar o nível.

#### <a name="security-administration-roles"></a>Funções de administração de segurança

Há uma separação de funções de segurança na administração de pontos finais do serviço de rede Virtual. Tem de fazer nada cada uma das seguintes funções:

- **Administrador de rede:** &nbsp; ativar o ponto final.
- **Administrador de base de dados:** &nbsp; atualizar a lista de controlo de acesso (ACL) para adicionar a sub-rede especificada para o servidor de base de dados SQL.

*Alternativa RBAC:* 

As funções de administrador de rede e administrador de base de dados têm mais capacidades que são necessárias para gerir as regras de rede virtual. É necessário apenas um subconjunto das respetivas capacidades.

Tem a opção de utilizar [controlo de acesso baseado em funções (RBAC)] [ rbac-what-is-813s] no Azure para criar uma função personalizada único que tenha apenas o necessário subconjunto das funcionalidades. A função personalizada pode ser utilizada em vez de envolver o administrador de rede ou o administrador da base de dados. A área de superfície da sua exposição de segurança é inferior se adicionar um utilizador para uma função personalizada, por oposição a adicionar o utilizador para as outras duas funções de administrador principais.






## <a name="limitations"></a>Limitações

Para a base de dados SQL do Azure, a funcionalidade de regras de rede virtual tem as seguintes limitações:

- Atualmente, uma aplicação Web do Azure numa sub-rede que tem **pontos finais de serviço** ativada does não ainda funcione como esperado. Estamos a trabalhar para ativar esta funcionalidade.
    - Até que esta funcionalidade é totalmente implementada, recomendamos que mova a aplicação Web para outra sub-rede que não tem pontos finais de serviço ativados no SQL Server.

- Na firewall para a base de dados do SQL Server, cada regra de rede virtual faz referência a uma sub-rede. Estas sub-redes referenciadas tem de estar alojadas na mesma região geográfica que aloja a base de dados do SQL Server.

- Cada servidor da SQL Database do Azure pode ter até 128 entradas ACL para qualquer determinada rede virtual.

- Regras de rede virtual são aplicadas apenas a redes virtuais do Azure Resource Manager; e não a [modelo de implementação clássica] [ arm-deployment-model-568f] redes.

- Pontos finais do serviço de rede virtual no desativar para a SQL Database do Azure também permite que os pontos finais para os serviços MySQL e PostGres Azure. No entanto, com pontos finais no, as tentativas para ligar a partir de pontos finais para as instâncias de MySQL ou Postgres irão falhar.
    - O motivo subjacente é essa MySQL e PostGres não suportam atualmente a ACL da.

- Na firewall, intervalos de endereços IP aplicam-se aos seguintes itens de rede, mas não as regras de rede virtual:
    - [Rede privada virtual (VPN) site a Site (S2S)][vpn-gateway-indexmd-608y]
    - No local através de [ExpressRoute][expressroute-indexmd-744v]

#### <a name="expressroute"></a>ExpressRoute

Se a sua rede é ligado à rede do Azure através da utilização de [ExpressRoute][expressroute-indexmd-744v], cada circuito está configurado com dois endereços IP públicos no Microsoft Edge. Dois endereços IP são utilizados para ligar a Services da Microsoft, tal como para o Storage do Azure, utilizando o Peering público do Azure.

Para permitir a comunicação entre o seu circuito e SQL Database do Azure, tem de criar regras de rede IP para os endereços IP públicos do seu circuitos. Para localizar os endereços IP públicos do seu circuito do ExpressRoute, abra um pedido de suporte com o ExpressRoute, utilizando o portal do Azure.


<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->





## <a name="errors-40914-and-40615"></a>Erros 40914 e 40615

Erro de ligação 40914 está relacionada com *regras de rede virtual*, conforme especificado no painel de Firewall no portal do Azure. Erro 40615 é semelhante, exceto se relaciona com *regras de endereços IP* na Firewall.

#### <a name="error-40914"></a>Erro 40914

*Mensagem de texto:* não é possível abrir o servidor '*[nome do servidor]*' pedido pelo início de sessão. Cliente não tem permissão para aceder ao servidor.

*Descrição do erro:* o cliente estiver numa sub-rede que tem pontos finais do servidor de rede virtual. Mas o servidor da SQL Database do Azure não tem nenhuma regra de rede virtual que concede à sub-rede à direita para comunicar com a base de dados do SQL Server.

*Resolução do erro:* painel na Firewall do portal do Azure, utilize as regras de rede virtual controlam ao [adicionar uma regra de rede virtual](#anchor-how-to-by-using-firewall-portal-59j) para a sub-rede.

#### <a name="error-40615"></a>Erro 40615

*Mensagem de texto:* não é possível abrir o servidor '{0}' pedido pelo início de sessão. Cliente com o endereço IP '{1}' não tem permissão para aceder ao servidor.

*Descrição do erro:* o cliente está a tentar ligar a partir de um endereço IP que não está autorizado a ligar ao servidor de SQL Database do Azure. A firewall do servidor não tem nenhuma regra de endereço IP que permite que um cliente comunicar do endereço IP fornecido para a base de dados do SQL Server.

*Resolução do erro:* introduza o endereço IP do cliente como uma regra IP. Fazê-lo utilizando o painel de Firewall no portal do Azure.


Uma lista de várias mensagens de erro de base de dados SQL é documentada [aqui][sql-database-develop-error-messages-419g].





<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portal pode criar uma regra de rede virtual

Esta secção ilustra a forma como pode utilizar o [portal do Azure] [ http-azure-portal-link-ref-477t] para criar um *regra de rede virtual* na base de dados SQL do Azure. A regra indica a base de dados do SQL Server para aceitar comunicações de uma sub-rede específica que tenha sido etiquetada como sendo uma *ponto final do serviço de rede Virtual*.

#### <a name="powershell-alternative"></a>PowerShell alternativa

Um script do PowerShell, também pode criar regras de rede virtual. O cmdlet fundamental **New-AzureRmSqlServerVirtualNetworkRule**. Se estiver interessado, consulte [PowerShell para criar um ponto final do serviço de rede Virtual e uma regra para a SQL Database do Azure][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

#### <a name="prerequisites"></a>Pré-requisitos

Já tem de ter uma sub-rede que está marcada com o ponto final de serviço de rede Virtual específico *nome do tipo* relevantes para a SQL Database do Azure.

- É o nome do tipo de ponto final relevante **Microsoft.Sql**.
- Se a sub-rede não pode ser marcada com o nome do tipo, consulte o artigo [Certifique-se de que a sub-rede é um ponto final][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

### <a name="azure-portal-steps"></a>Passos de portais do Azure

1. Inicie sessão no [portal do Azure][http-azure-portal-link-ref-477t].

2. Em seguida, navegue no portal para **servidores SQL** &gt; **Firewall / redes virtuais**.

3. Definir o **permitir o acesso aos serviços do Azure** controlo como OFF.

    > [!IMPORTANT]
    > Se deixar o controlo definido como ON, em seguida, o servidor da SQL Database do Azure aceita comunicações de nenhuma sub-rede, que poderá ser excessivo acesso a partir de um ponto de vista da segurança. Em conjunto, a funcionalidade de ponto final de serviço de rede Virtual do Microsoft Azure, em coordenação com a funcionalidade de regra de rede virtual da base de dados do SQL Server, pode reduzir a área de superfície de segurança.

4. Clique em de **+ adicionar existente** controlar, além de **redes virtuais** secção.

    ![Clique em Adicionar existente (sub-rede ponto final, como uma regra de SQL Server).][image-portal-firewall-vnet-add-existing-10-png]

5. Na nova **Create/Update** painel, preencha os controlos com os nomes dos seus recursos Azure.
 
    > [!TIP]
    > Tem de incluir o correto **prefixo de endereço** para a sub-rede. Pode encontrar o valor no portal. Navegue **todos os recursos** &gt; **todos os tipos de** &gt; **redes virtuais**. O filtro mostra as redes virtuais. Clique na sua rede virtual e clique **sub-redes**. O **intervalo de endereços** coluna tem o prefixo de endereço precisa.

    ![Preencha os campos para a nova regra.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Clique em de **OK** botão perto da parte inferior do painel.

7.  Consulte a regra de rede virtual resultante no painel de firewall.

    ![Consulte a nova regra, no painel de firewall.][image-portal-firewall-vnet-result-rule-30-png]






<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Artigos relacionados

- [Utilizar o PowerShell para criar um ponto final de serviço de rede Virtual e, em seguida, uma regra de rede virtual para a SQL Database do Azure][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Pontos finais do serviço de rede virtual do Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Regras de firewall ao nível do servidor e o nível de base de dados de base de dados SQL do Azure][sql-db-firewall-rules-config-715d]

A funcionalidade de pontos finais do serviço de rede Virtual do Microsoft Azure e a regra de rede virtual da funcionalidade de base de dados do Azure SQL, ambos ficaram disponíveis no enlace tardio de Setembro de 2017.





<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png



<!-- Link references, to text, Within this same Github repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[expressroute-indexmd-744v]: ../expressroute/index.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.md



<!-- Link references, to text, Outside this Github repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/




<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->

