---
title: "Descrição Geral das Máquinas Virtuais do Windows | Microsoft Docs"
description: "Saiba mais sobre a criação e gestão de máquinas virtuais do Windows no Azure."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: fbae9c8e-2341-4ed0-bb20-fd4debb2f9ca
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: 2944021cbaf777137512f4bfe0eb4cf5e6f996dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-windows-virtual-machines-in-azure"></a>Descrição Geral das Máquinas Virtuais do Windows no Azure

As Máquinas Virtuais (VMs) do Azure são um dos vários tipos de [recursos informáticos a pedido](../../app-service/choose-web-site-cloud-service-vm.md), dimensionáveis que o Azure oferece. Normalmente, escolher uma VM, se precisar de mais controlo sobre o ambiente informático que as outras opções oferecem. Este artigo dá-lhe informações sobre o que deve considerar antes de criar uma VM, como criá-la e geri-la.

Uma VM do Azure fornece-lhe a flexibilidade de virtualização sem ter de comprar e manter o hardware físico que executa a VM. No entanto, ainda tem de manter a VM a realizar tarefas, como configurar, aplicar patches e instalar o software que é executado na mesma.

As máquinas virtuais do Azure podem ser utilizadas de várias formas. Alguns exemplos incluem:

* **Desenvolvimento e teste** – as VMs do Azure oferecem uma forma rápida e fácil de criar um computador com as configurações específicas necessárias para codificar e testar uma aplicação.
* **Aplicações na nuvem** – uma vez que a procura da sua aplicação pode variar, em termos económicos, poderá fazer sentido executá-la numa VM no Azure. Paga pelas VMs adicionais quando precisar delas e encerra-as quando não precisar.
* **Datacenter expandido** – as máquinas virtuais numa rede virtual do Azure podem ser facilmente ligadas à rede da sua organização.

O número de VMs que a aplicação utiliza pode ser vertical e horizontalmente aumentado para o valor que for preciso para satisfazer as necessidades.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>O que é preciso ter em conta antes de criar uma VM?
Existem sempre inúmeras [considerações de design](/azure/architecture/reference-architectures/virtual-machines-windows?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) quando está a desenvolver uma infraestrutura de aplicação no Azure. Estes aspetos de uma VM são importantes e devem ser ponderados antes de começar:

* Os nomes dos recursos da aplicação
* A localização onde os recursos são armazenados
* O tamanho da VM
* O número máximo de VMs que podem ser criadas
* O sistema operativo que a VM executa
* A configuração da VM depois de iniciar
* Os recursos relacionados que a VM precisa

### <a name="naming"></a>Atribuição de nomes
Uma máquina virtual tem um [nome](/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) atribuído e tem um nome do computador configurado como parte do sistema operativo. O nome de uma VM pode ter até 15 carateres.

Se utilizar o Azure para criar o disco do sistema operativo, o nome do computador e da máquina virtual é o mesmo. Se [carregar e utilizar a sua própria imagem](upload-generalized-managed.md) que contém um sistema operativo configurado anteriormente e utilizá-lo para criar uma máquina virtual, os nomes podem ser diferentes. Recomendamos que, ao carregar o seu próprio ficheiro de imagem, o nome do computador no sistema operativo e o da máquina virtual seja o mesmo.

### <a name="locations"></a>Localizações
Todos os recursos criados no Azure são distribuídos em várias [regiões geográficas](https://azure.microsoft.com/regions/) em todo o mundo. Normalmente, a região é designada por **localização** ao criar uma VM. Para uma VM, a localização especifica onde os discos rígidos virtuais são armazenados.

Esta tabela apresenta algumas das formas de obter uma lista de localizações disponíveis.

| Método | Descrição |
| --- | --- |
| Portal do Azure |Selecione uma localização da lista ao criar uma VM. |
| Azure PowerShell |Utilize o comando [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation). |
| API REST |Utilize a operação [Listar localizações](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_ListLocations). |

### <a name="vm-size"></a>Tamanho da VM
O [tamanho](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) da VM que utiliza é determinado pela carga de trabalho que pretende executar. O tamanho que escolher determina fatores como o poder de processamento, a memória e capacidade de armazenamento. O Azure disponibiliza uma vasta variedade de tamanhos para suportar muitos tipos de utilizações.

O Azure cobra um [preço por hora](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) com base no tamanho da VM e do sistema operativo. Para horas parciais, o Azure cobra apenas os minutos utilizados. O armazenamento tem um preço à parte e é cobrado separadamente.

### <a name="vm-limits"></a>Limites da VM
A subscrição tem [limites de quota](../../azure-subscription-service-limits.md) predefinidos num local que pode afetar a implementação de muitas VMs para o seu projeto. O limite atual numa base por subscrição é de 20 VMs por região. Os limites podem ser aumentados ao preencher um pedido de suporte a pedir um aumento.

### <a name="operating-system-disks-and-images"></a>Discos do sistema operativo e imagens
As máquinas virtuais utilizam [discos rígidos virtuais (VHDs)](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para armazenar o respetivo sistema operativo (SO) e dados. Os VHDs também são utilizados para as imagens que pode escolher para instalar um SO. 

O Azure oferece muitas [imagens do marketplace](https://azure.microsoft.com/marketplace/virtual-machines/) para utilizar com diversas versões e tipos de sistemas operativos do Windows Server. A imagens do marketplace são identificadas pelo publicador da imagem, oferta, sku e versão (normalmente, a versão especificada é a mais recente). 

Esta tabela mostra algumas formas para encontrar as informações de uma imagem.

| Método | Descrição |
| --- | --- |
| Portal do Azure |Os valores são especificados automaticamente ao selecionar uma imagem a utilizar. |
| Azure PowerShell |[Get-AzureRMVMImagePublisher](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/get-azurermvmimagepublisher) -Location "localização"<BR>[Get-AzureRMVMImageOffer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/get-azurermvmimageoffer) -Location "localização" -Publisher "publisherName"<BR>[Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) -Location "localização" -Publisher "publisherName" -Offer "offerName" |
| APIs REST |[Listar publicadores de imagem](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[Listar ofertas da imagem](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[Listar skus da imagem](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |

Pode optar por [carregar e utilizar a sua própria imagem](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account) e quando o fizer, o nome do publicador, a oferta e o sku não são utilizados.

### <a name="extensions"></a>Extensões
As [extensões](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) da VM fornecem capacidades adicionais à sua VM através da configuração pós-implementação e de tarefas automatizadas.

Estas tarefas comuns podem ser realizadas com extensões:

* **Executar scripts personalizados** – a [Extensão de Script Personalizado](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ajuda-o a configurar as cargas de trabalho na VM ao executar o script quando a VM está aprovisionada.
* **Implementar e gerir configurações** – a [Extensão da Configuração do Estado Pretendido (DSC) do PowerShell](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ajuda-o a configurar o DSC numa VM para gerir configurações e ambientes.
* **Recolher dados de diagnóstico** – a [Extensão do Diagnóstico do Azure](extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ajuda-o a configurar a VM para recolher dados de diagnóstico que podem ser utilizados para monitorizar o estado de funcionamento da aplicação.

### <a name="related-resources"></a>Recursos relacionados
Os recursos nesta tabela são utilizados pela VM e têm de existir ou ser criados quando a VM é criada.

| Recurso | Necessário | Descrição |
| --- | --- | --- |
| [Grupo de recursos](../../azure-resource-manager/resource-group-overview.md) |Sim |A VM tem de estar contida num grupo de recursos. |
| [Conta de armazenamento](../../storage/common/storage-create-storage-account.md) |Sim |A VM precisa da conta de armazenamento para armazenar os respetivos discos rígidos virtuais. |
| [Rede virtual](../../virtual-network/virtual-networks-overview.md) |Sim |A VM tem de ser um membro de uma rede virtual. |
| [Endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |Não |A VM pode ter um endereço IP público atribuído para aceder ao mesmo remotamente. |
| [Interface de rede](../../virtual-network/virtual-network-network-interface.md) |Sim |A VM precisa da interface de rede para comunicar na rede. |
| [Discos de dados](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |Não |A VM pode incluir discos de dados para expandir as capacidades de armazenamento. |

## <a name="how-do-i-create-my-first-vm"></a>Como posso criar a minha primeira VM?
Existem várias opções para criar a sua VM. A opção irá depender do ambiente em que se encontra. 

Esta tabela fornece informações para começar a criar a sua VM.

| Método | Artigo |
| --- | --- |
| Portal do Azure |[Criar uma máquina virtual que executa o Windows com o Portal](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Modelos |[Criar uma máquina virtual do Windows com um modelo do Resource Manager](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure PowerShell |[Criar uma VM do Windows com o PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| SDKs do Cliente |[Implementar Recursos do Azure através do C#](csharp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| APIs REST |[Criar ou atualizar uma VM](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-create-or-update) |

Espera que nunca aconteça mas, ocasionalmente, algo corre mal. Se esta situação acontecer, leia as informações em [Troubleshoot Resource Manager deployment issues with creating a Windows virtual machine in Azure (Resolver problemas de implementação do Gestor de Recursos ao criar uma máquina virtual do Windows no Azure)](troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Como posso gerir a VM que criei?
As VMs podem ser geridas com um portal baseado num browser, ferramentas de linha de comandos com suporte para processamento de scripts ou diretamente através de APs. Algumas tarefas de gestão típicas que pode executar são obter informações sobre uma VM, iniciar sessão numa VM, gerir a disponibilidade e efetuar cópias de segurança.

### <a name="get-information-about-a-vm"></a>Obter informações sobre uma VM
Esta tabela mostra-lhe algumas das formas de obter informações sobre uma VM.

| Método | Descrição |
| --- | --- |
| Portal do Azure |No menu hub, clique em **Máquinas Virtuais** e, em seguida, selecione a VM na lista. No painel para a VM, tem acesso a informações da descrição geral, valores de definição e métricas de monitorização. |
| Azure PowerShell |Para obter mais informações sobre como utilizar o PowerShell para gerir VMs, veja [Create and manage Windows VMs with the Azure PowerShell module](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Criar e gerir VMs do Windows com o módulo Azure PowerShell). |
| API REST |Utilize a operação [Obter informações da VM](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get) para obter informações sobre uma VM. |
| SDKs do Cliente |Para obter informações sobre como utilizar o C# para gerir VMs, consulte [Manage Azure Virtual Machines using Azure Resource Manager and C# (Gerir Máquinas Virtuais do Azure com o Gestor de Recursos do Azure e o C#)](csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |

### <a name="log-on-to-the-vm"></a>Iniciar sessão na VM
Utilize o botão Ligar no Portal do Azure para [iniciar uma sessão de Ambiente de Trabalho Remoto (RDP)](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Por vezes, as coisas podem correr ao tentar utilizar uma ligação remota. Se isso acontecer, verifique as informações de ajuda em [Troubleshoot Remote Desktop connections to an Azure virtual machine running Windows (Resolver problemas de ligações do Ambiente de Trabalho Remoto numa máquina virtual do Azure com o Windows)](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="manage-availability"></a>Gerir a disponibilidade
É importante compreender como [garantir a elevada disponibilidade](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) da sua aplicação. Esta configuração envolve a criação de várias VMs para garantir que pelo menos uma está em execução.

Para a sua implementação se qualificar para o nosso Contrato de Nível de Serviço da VM 99.95, tem de implementar duas ou mais VMs que executam a carga de trabalho num [conjunto de disponibilidade](tutorial-availability-sets.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Esta configuração garante que as VMs são distribuídas em vários domínios de falhas e são implementadas em anfitriões com janelas de manutenção diferentes. O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) completo explica a disponibilidade garantida do Azure em termos globais.

### <a name="back-up-the-vm"></a>Realizar uma cópia de segurança da VM
Para proteger dados e ativos nos serviços do Azure Backup e do Azure Site Recovery é utilizado um [Cofre dos Serviços de Recuperação](../../backup/backup-introduction-to-azure-backup.md). Pode utilizar um cofre dos Serviços de Recuperação para [implementar e gerir cópias de segurança para VMs implementadas pelo Gestor de Recursos com o PowerShell](../../backup/backup-azure-vms-automation.md). 

## <a name="next-steps"></a>Passos seguintes
* Se a sua intenção for trabalhar com VMs do Linux, consulte [Azure e Linux](../linux/overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Saiba mais sobre as diretrizes em torno da configuração da sua infraestrutura em [Example Azure infrastructure walkthrough (Instruções sobre a infraestrutura do Azure de exemplo)](infrastructure-example.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).