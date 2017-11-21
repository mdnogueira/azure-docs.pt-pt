---
title: "Glossário do Azure - dicionário do Azure | Microsoft Docs"
description: "Utilize o glossário do Azure para compreender a terminologia de nuvem na plataforma do Azure. Este dicionário do Azure pequeno fornece definições comuns termos de nuvem do Azure."
keywords: "Dicionário do Azure, terminologia de nuvem, Glossário do Azure, definições terminológicas, os termos de nuvem"
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: 
ms.assetid: d7ac12f7-24b5-4bcd-9e4d-3d76fbd8d297
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: monicar
ms.openlocfilehash: cbc4b8cdb0ff9255d0be02b998e67686921921ea
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Glossário do Microsoft Azure: um dicionário de terminologia de nuvem na plataforma do Azure

O glossário do Microsoft Azure é um dicionário abreviado do terminologia de nuvem para a plataforma do Azure. Veja também:

* [Microsoft Azure e o Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) -serviços de definições do Azure e os seus homólogos AWS.<!-- I propose to link to https://azure.microsoft.com/en-us/services/ instead of this -->
* [Termos de computação da nuvem](https://azure.microsoft.com/overview/cloud-computing-dictionary/) -termos de nuvem da indústria geral.

## <a name="account"></a>Conta
Uma conta que é utilizada para aceder e gerir uma subscrição do Azure. É frequentemente referido como uma conta do Azure embora uma conta pode ser qualquer um dos seguintes: um trabalho existente, escola, ou pessoais conta Microsoft, ou um nome de utilizador do Office 365 e palavra-passe. Também pode criar uma conta para gerir uma subscrição do Azure quando se inscreve no [avaliação gratuita](https://azure.microsoft.com).  
Consulte [inscrever-se para uma subscrição do Azure com a sua conta Office 365](billing/billing-use-existing-office-365-account-azure-subscription.md) e [contas podem utilizar para iniciar sessão](active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>Aplicação API
Outro nome para [aplicação do app Service](#app-service-app).

## <a name="app-service-app"></a>Aplicação do app Service
Os recursos de computação que [App Service do Azure](app-service/app-service-web-overview.md) fornece para alojar uma Web site ou aplicação web, web API, ou [back-end da aplicação móvel](app-service-mobile/app-service-mobile-value-prop.md). Aplicações do App Service também são referidas como *serviços aplicacionais*, *aplicações web*, *das API apps*, e *aplicações móveis*.

## <a name="availability-set"></a>conjunto de disponibilidade
Uma coleção de máquinas virtuais que são geridos em conjunto para fornecer redundância de aplicação e fiabilidade. A utilização de um conjunto de disponibilidade assegura que, durante um evento de manutenção planeado ou não planeado, pelo menos uma máquina virtual está disponível.  
Consulte [gerir a disponibilidade das Windows virtual machines](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [gerir a disponibilidade das máquinas virtuais do Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="classic-model"></a>Modelo de implementação clássica do Azure
Um dos dois [modelos de implementação](resource-manager-deployment-model.md) utilizados para implementar os recursos no Azure (o novo modelo é o Azure Resource Manager). Alguns serviços do Azure suportam apenas o modelo de implementação do Resource Manager, alguns suportam o modelo de implementação clássica e algumas de suportar. A documentação para cada serviço do Azure Especifica quais model(s) suportam.

## <a name="cli"></a>Interface de linha de comandos (CLI) do Azure
Uma interface de linha de comandos que podem ser utilizada para gerir serviços do Azure a partir do Windows, macOS e Linux.  Alguns serviços ou funcionalidades de serviço podem ser geridas apenas através do PowerShell ou a CLI. Consulte [CLI do Azure 2.0](/cli/azure/overview)

## <a name="powershell"></a>O Azure PowerShell
Uma interface de linha de comandos para gerir os serviços do Azure através de uma linha de comandos a partir de Windows PCs. Alguns serviços ou funcionalidades de serviço podem ser geridas apenas através do PowerShell ou a CLI.
Consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview)

## <a name="arm-model"></a>Modelo de implementação do Azure Resource Manager
Um dos dois [modelos de implementação](resource-manager-deployment-model.md) utilizados para implementar os recursos no Microsoft Azure (o outro é o modelo de implementação clássica). Alguns serviços do Azure suportam apenas o modelo de implementação do Resource Manager, alguns suportam o modelo de implementação clássica e algumas de suportar. A documentação para cada serviço do Azure Especifica quais model(s) suportam.

## <a name="fault-domain"></a>Domínio de falhas
A coleção de máquinas virtuais num conjunto de disponibilidade que, possivelmente, podem falhar ao mesmo tempo. Um exemplo é um grupo de computadores num bastidor que partilham um comutador de rede de origem e de energia comum. No Azure, as máquinas virtuais num conjunto de disponibilidade automaticamente estão separadas em vários domínios de falhas.  
Consulte [gerir a disponibilidade das Windows virtual machines](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [gerir a disponibilidade das máquinas virtuais do Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>georreplicação
Um limite definido para residency de dados que contém, geralmente, dois ou mais regiões. Os limites podem estar dentro ou se para além dos limites national e são deve influenciados pelos Regulamento dedução dos impostos. Cada georreplicação tem, pelo menos, uma região. Exemplos de geos são Ásia-Pacífico e Japão. Também denominado *geografia*.  
Consulte [regiões do Azure](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>replicação geográfica
O processo de replicar automaticamente o conteúdo como blobs, tabelas e filas dentro de um par regional.  
Consulte [Georreplicação ativa para a base de dados SQL do Azure](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>Imagem
Um ficheiro que contém o sistema operativo e a configuração da aplicação que pode ser utilizada para criar qualquer número de máquinas virtuais. No Azure, existem dois tipos de imagens: VM imagem e a imagem do SO. Uma imagem de VM inclui um sistema operativo e todos os discos anexados a uma máquina virtual quando é criada a imagem. Imagem do SO contém apenas um sistema operativo generalizado com não configurações de disco de dados.  
Consulte [navegar e selecionadas imagens da máquina virtual Windows no Azure com o PowerShell ou a CLI](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>Limites
O número de recursos que podem ser criados ou o benchmark de desempenho que pode ser alcançado. Os limites associados normalmente subscrições, serviços e ofertas.  
Consulte [subscrição do Azure e limites de serviço, quotas e restrições](azure-subscription-service-limits.md)

## <a name="load-balancer"></a>O Balanceador de carga
Um recurso que distribui o tráfego de entrada entre os computadores numa rede. No Azure, um balanceador de carga distribui o tráfego para máquinas virtuais definido um conjunto de Balanceador de carga. A [Balanceador de carga](load-balancer/load-balancer-overview.md) pode ser o acesso à internet ou pode ser interna.  

## <a name="mobile-app"></a>aplicação móvel
Outro nome para [aplicação do serviço de aplicações](#app-service-app).

## <a name="offer"></a>oferta
Os preços, créditos e os termos relacionados a aplicáveis a uma subscrição do Azure.  
Consulte o [página de detalhes de oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
O portal web seguro utilizado para implementar e gerir serviços do Azure.

## <a name="region"></a>região
Uma área dentro de uma georreplicação limites não cross national e contém um ou mais dos centros de dados. Preços, os serviços regionais e tipos de oferta estão expostos ao nível da região. Uma região normalmente se encontra emparelhada com outro região, o que pode ser até várias centenas quilómetros ausente. Pares regionais podem ser utilizados como um mecanismo para cenários de elevada disponibilidade e recuperação após desastre. Também referido como *localização*.  
Consulte [regiões do Azure](best-practices-availability-paired-regions.md)

## <a name="resource"></a>Recurso
Um item que faz parte da sua solução do Azure. Cada serviço do Azure permite-lhe implementar os diferentes tipos de recursos, tais como bases de dados ou de máquinas virtuais.   
Consulte [descrição geral do Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="resource-group"></a>grupo de recursos
Um contentor no Gestor de recursos que retém recursos relacionados para uma aplicação. O grupo de recursos pode incluir todos os recursos para uma aplicação ou apenas os recursos que estão logicamente agrupados. Pode decidir como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização.  
Consulte [descrição geral do Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="arm-template"></a>Modelo do Resource Manager
Um ficheiro JSON que forma declarativa define um ou mais recursos do Azure e que define dependências entre os recursos implementados. O modelo pode ser utilizado para implementar os recursos de forma consistente e repetida.  
Consulte [modelos Authoring Azure Resource Manager](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>Fornecedor de recursos
Um serviço que fornece os recursos que pode implementar e gerir através do Resource Manager. Cada fornecedor de recursos oferece operações para trabalhar com os recursos implementados. Fornecedores de recursos podem ser acedidos através do portal do Azure, Azure PowerShell e vários SDKs de programação.  
Consulte [descrição geral do Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="role"></a>Função
Um meio para controlar o acesso que pode ser atribuído a utilizadores, grupos e serviços. As funções são capazes de realizar ações tais como criar, gerirem e ler nos recursos do Azure.  
Consulte [RBAC: funções incorporadas](active-directory/role-based-access-built-in-roles.md)

## <a name="sla"></a>contrato de nível de serviço (SLA)
O contrato que descreve os compromissos da Microsoft para o tempo de atividade e conectividade. Cada serviço do Azure tem um SLA específico.  
Consulte [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/)

## <a name="sas"></a>assinatura de acesso partilhado (SAS)
Uma assinatura permite-lhe de conceder acesso limitado a um recurso, sem a chave de conta a exposição. Por exemplo, [Storage do Azure utiliza SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) para conceder acesso de cliente para objetos, tais como os blobs. [IoT Hub utiliza SAS](iot-hub/iot-hub-devguide-security.md#security-tokens) para conceder permissão para enviar a telemetria de dispositivos.

## <a name="storage-account"></a>conta de armazenamento
Uma conta que dá-lhe aceder aos serviços de ficheiro, tabela, fila e Blob do Azure no Storage do Azure. O nome da conta de armazenamento define o espaço de nomes exclusivo para objetos de dados do Storage do Azure.  
Consulte [contas do storage do Azure sobre](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>subscrição
Contrato de um cliente com a Microsoft permite-lhes obter serviços do Azure. O preço de subscrição e os termos relacionados são regidos pela oferta escolhida para a subscrição.
Consulte [contrato de subscrição Online da Microsoft](https://azure.microsoft.com/support/legal/subscription-agreement/) e [subscrições do Azure como estão associadas ao Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>Etiqueta
Um termo de indexação que lhe permite categorizar os recursos de acordo com os requisitos de gestão ou faturação. Quando tiver uma coleção complexa de recursos, pode utilizar etiquetas para visualizar esses elementos da forma que mais adequado. Por exemplo, pode etiquetar recursos que desempenham uma função semelhante na sua organização ou pertencem ao mesmo departamento.  
Consulte [utilizar etiquetas para organizar os recursos do Azure](resource-group-using-tags.md)

## <a name="update-domain"></a>Domínio de atualização
A coleção de máquinas virtuais num conjunto de disponibilidade que são atualizadas ao mesmo tempo. Máquinas virtuais no mesmo domínio de atualização são reiniciadas em conjunto durante a manutenção planeada. O Azure nunca reinicia mais de um domínio de atualização de cada vez. Também referido como um domínio de atualização.  
Consulte [gerir a disponibilidade das Windows virtual machines](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [gerir a disponibilidade das máquinas virtuais do Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vm"></a>máquina virtual
A implementação de software de um computador físico que executa um sistema operativo. Várias máquinas virtuais podem ser executados em simultâneo o mesmo hardware. No Azure, estão disponíveis uma variedade de tamanhos de máquinas virtuais.  
Consulte [documentação de Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="vm-extension"></a>extensão da máquina virtual
Um recurso que implementa comportamentos ou funcionalidades que ajudam a outros programas de trabalho ou fornecem a capacidade de interagir com um computador em execução. Por exemplo, pode utilizar a extensão de acesso de VM para repor ou modificar os valores de acesso remoto numa máquina virtual do Azure.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Consulte [sobre extensões de máquina virtual e funcionalidades (Windows)](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [sobre extensões de máquina virtual e funcionalidades (Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vnet"></a>rede virtual
Uma rede que fornece conectividade entre os recursos do Azure que seja isolado de todos os outros inquilinos do Azure. Um [Gateway de VPN do Azure](vpn-gateway/vpn-gateway-about-vpngateways.md) permite-lhe estabelecer ligações entre redes virtuais e [entre uma rede virtual e uma rede no local](vpn-gateway/vpn-gateway-plan-design.md). Pode controlar totalmente os blocos de endereços IP, definições DNS, as políticas de segurança e as tabelas de rotas dentro de uma rede virtual.  
Consulte [descrição geral da rede Virtual](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Aplicação Web
Outro nome para [aplicação do serviço de aplicações](#app-service-app).

## <a name="see-also"></a>Consultar também

* [Introdução ao Azure](https://azure.microsoft.com/get-started/)
* [Centro de recursos de nuvem](https://azure.microsoft.com/resources/)  
* [Azure para a sua aplicação de negócio](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure no seu centro de dados](https://azure.microsoft.com/overview/business-apps-on-azure/)

