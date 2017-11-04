---
title: Migrar VMs para o Resource Manager utilizando a CLI do Azure | Microsoft Docs
description: "Este artigo explica a migração de plataforma suportada de recursos do clássico para o Azure Resource Manager utilizando a CLI do Azure"
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d6f5a877-05b6-4127-a545-3f5bede4e479
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: fe0446b986ff73cce66a961c1c8aa1b01ef493a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrar recursos IaaS do clássico para o Azure Resource Manager, utilizando a CLI do Azure
Estes passos mostram como utilizar comandos de interface de linha de comandos do Azure (CLI) para migrar a infraestrutura como um recursos de serviço (IaaS) do modelo de implementação clássica para o modelo de implementação Azure Resource Manager. O artigo requer o [CLI do Azure 1.0](../../cli-install-nodejs.md). Uma vez que o Azure CLI 2.0 só é aplicável para recursos do Azure Resource Manager, não pode ser utilizado para esta migração.

> [!NOTE]
> Todas as operações descritas aqui são idempotent. Se tiver um problema que não seja uma funcionalidade não suportada ou um erro de configuração, recomendamos que repita preparar, abortar ou consolidar a operação. A plataforma será, em seguida, repita a ação.
> 
> 

<br>
Eis uma fluxograma para identificar a ordem na qual passos têm de ser executada durante um processo de migração

![Captura de ecrã que mostra os passos da migração](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Passo 1: Preparar a migração
Seguem-se algumas melhores práticas que recomendamos como avaliar migrar recursos IaaS do clássico para o Gestor de recursos:

* Leia o [lista de configurações não suportadas ou funcionalidades](../windows/migration-classic-resource-manager-overview.md). Se tiver máquinas virtuais que utilizam configurações não suportadas ou funcionalidades, recomendamos que aguarde para o suporte a funcionalidade/Configuração comunicadas. Em alternativa, pode remover essa funcionalidade ou mover-se fora do que a configuração para permitir a migração se se adapta às suas necessidades.
* Se tiver automatizada scripts que implementar a sua infraestrutura e aplicações hoje em dia, tente criar uma configuração de teste semelhantes utilizando estes scripts para a migração. Em alternativa, pode configurar ambientes de exemplo através do portal do Azure.

> [!IMPORTANT]
> Gateways de aplicação não são atualmente suportados para migração do clássico para o Gestor de recursos. Para migrar uma rede virtual clássica com um gateway de aplicação, remova o gateway antes de executar uma operação de preparar para mover a rede. Depois de concluir a migração, restabeleça a ligação do gateway no Gestor de recursos do Azure. 
>
>Ligar para circuitos do ExpressRoute na outra subscrição gateways do ExpressRoute não podem ser migrados automaticamente. Nestes casos, remova o gateway do ExpressRoute, migrar a rede virtual e recrie o gateway. Consulte [migrar ExpressRoute circuitos e associadas redes virtuais do clássico para o modelo de implementação Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md) para obter mais informações.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Passo 2: Definir a sua subscrição e registar o fornecedor
Para cenários de migração, terá de configurar o ambiente para ambos os clássica e Resource Manager. [Instalar a CLI do Azure](../../cli-install-nodejs.md) e [selecionar a sua subscrição](../../xplat-cli-connect.md).

Início de sessão à sua conta.

    azure login

Selecione a subscrição do Azure utilizando o seguinte comando.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> O registo é um passo de tempo, mas tiver de ser efetuada uma vez antes de tentar efetuar a migração. Sem registar, verá a seguinte mensagem de erro 
> 
> *BadRequest: Subscrição não está registada para migração.* 
> 
> 

Registar o fornecedor de recursos de migração utilizando o seguinte comando. Tenha em atenção que em alguns casos, este comando exceder o tempo limite. No entanto, o registo será bem sucedido.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Aguarde cinco minutos para concluir o registo. Pode verificar o estado da aprovação utilizando o seguinte comando. Certifique-se de que RegistrationState `Registered` antes de continuar.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Agora comutador CLI para o `asm` modo.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Passo 3: Certifique-se de que tem suficiente núcleos de Máquina Virtual do Azure Resource Manager na região do Azure da sua implementação atual ou o VNET
Para este passo, terá de mudar para `arm` modo. Fazê-lo com o seguinte comando.

```
azure config mode arm
```

Pode utilizar o seguinte comando da CLI para verificar a quantidade atual de núcleos que tiver no Gestor de recursos do Azure. Para saber mais sobre as quotas de núcleos, consulte o artigo [limites e o Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Depois de concluir a verificar este passo, pode voltar a mudar para `asm` modo.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Passo 4: Opção 1 - migrar máquinas virtuais de um serviço em nuvem
Obter a lista de serviços em nuvem, utilizando o comando seguinte e, em seguida, escolha o serviço em nuvem que pretende migrar. Tenha em atenção que se as VMs no serviço em nuvem numa rede virtual ou se tiver web/funções de trabalho, receberá uma mensagem de erro.

    azure service list

Execute o seguinte comando para obter o nome da implementação para o serviço em nuvem da saída verbosa. Na maioria dos casos, o nome da implementação é o mesmo que o nome do serviço em nuvem.

    azure service show <serviceName> -vv

Em primeiro lugar, confirme se é possível migrar o serviço em nuvem através dos seguintes comandos:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Prepare as máquinas virtuais no serviço em nuvem para a migração. Tem duas opções à sua escolha.

Se pretender migrar as máquinas virtuais a uma rede virtual criada de plataforma, utilize o seguinte comando.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Se pretender migrar para uma rede virtual existente no modelo de implementação Resource Manager, utilize o seguinte comando.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

Depois da operação de prepare for bem sucedida, pode ver através de uma saída verbosa para obter o estado de migração das VMs e certifique-se de que estão no `Prepared` estado.

    azure vm show <vmName> -vv

Verifique a configuração para os recursos preparadas utilizando o CLI ou o portal do Azure. Se não estiver preparada para migração e pretender voltar para o estado antigo, utilize o seguinte comando.

    azure service deployment abort-migration <serviceName> <deploymentName>

Se a configuração preparada procura boa, pode avançar e consolidar os recursos utilizando o seguinte comando.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Passo 4: Opção 2 - migrar máquinas virtuais numa rede virtual
Escolha a rede virtual que pretende migrar. Tenha em atenção que, se a rede virtual contém web/funções de trabalho ou VMs com configurações não suportadas, receberá uma mensagem de erro de validação.

Obter todas as redes virtuais na subscrição utilizando o seguinte comando.

    azure network vnet list

O resultado será algo semelhante ao seguinte:

![Captura de ecrã da linha de comandos com o nome de toda a rede virtual realçado.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

No exemplo acima, o **virtualNetworkName** é o nome completo **"Grupo classicubuntu16 classicubuntu16"**.

Em primeiro lugar, confirme se é possível migrar a rede virtual com o seguinte comando:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Prepare a rede virtual à sua escolha para a migração, utilizando o comando seguinte.

    azure network vnet prepare-migration <virtualNetworkName>

Verifique a configuração para as máquinas virtuais preparadas utilizando o CLI ou o portal do Azure. Se não estiver preparada para migração e pretender voltar para o estado antigo, utilize o seguinte comando.

    azure network vnet abort-migration <virtualNetworkName>

Se a configuração preparada procura boa, pode avançar e consolidar os recursos utilizando o seguinte comando.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Passo 5: Migrar uma conta de armazenamento
Depois de concluir a migração de máquinas virtuais, recomendamos que migre a conta de armazenamento.

Preparar a conta de armazenamento para migração utilizando o seguinte comando

    azure storage account prepare-migration <storageAccountName>

Verifique a configuração da conta do storage preparada utilizando o CLI ou o portal do Azure. Se não estiver preparada para migração e pretender voltar para o estado antigo, utilize o seguinte comando.

    azure storage account abort-migration <storageAccountName>

Se a configuração preparada procura boa, pode avançar e consolidar os recursos utilizando o seguinte comando.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral da migração de plataforma suportada dos recursos IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Técnica descrição profunda sobre a migração de plataforma suportada do clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Utilizar o PowerShell para migrar os recursos IaaS do clássico para o Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Ferramentas de Comunidade para prestar assistência com a migração de recursos IaaS do clássico para o Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Rever as perguntas mais frequentes sobre a migração de IaaS de recursos do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
