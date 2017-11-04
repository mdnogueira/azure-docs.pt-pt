---
title: "Introdução às máquinas virtuais de pilha do Azure"
description: "Saiba mais sobre as máquinas virtuais de pilha do Azure"
services: azure-stack
author: anjayajodha
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: 68da653052d0e3dfd66d6b65958046e42cefce73
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Introdução às máquinas virtuais de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

## <a name="overview"></a>Descrição geral
Uma Máquina Virtual de pilha do Azure (VM) é um tipo de recurso de computação a pedido, dimensionável que oferece a pilha do Azure. Normalmente, escolher uma VM, se precisar de mais controlo sobre o ambiente informático que as outras opções oferecem. Este artigo dá-lhe informações sobre o que deve considerar antes de criar uma VM, como criá-la e geri-la.

Uma VM de pilha do Azure dá-lhe a flexibilidade de Virtualização sem a necessidade de gerir individuais clusters ou máquinas. No entanto, ainda tem de manter a VM a realizar tarefas, como configurar, aplicar patches e instalar o software que é executado na mesma.

Máquinas virtuais de pilha do Azure pode ser utilizadas de várias formas. Por exemplo:

* **Desenvolvimento e teste** – VMs de pilha do Azure oferecem uma rápida e necessário forma fácil de criar um computador com uma configuração específica de código e testar uma aplicação.

* **Aplicações na nuvem** – porque pode flutuam, a pedido para a sua aplicação, poderá ser aconselhável económico executá-la numa VM na pilha do Azure. Paga pelas VMs adicionais quando precisar delas e encerra-as quando não precisar.

* **Expandido datacenter** – máquinas virtuais numa rede virtual do Azure pilha podem facilmente ser ligados à rede ou do Azure da sua organização.

O número de VMs que a aplicação utiliza pode ser vertical e horizontalmente aumentado para o valor que for preciso para satisfazer as necessidades.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>O que é preciso ter em conta antes de criar uma VM?

Há sempre um sem-número de considerações de design quando criar fora de uma infraestrutura de aplicação na pilha do Azure. Estes aspetos de uma VM são importantes e devem ser ponderados antes de começar:

- Os nomes dos recursos da aplicação
- O tamanho da VM
- O número máximo de VMs que podem ser criadas
- O sistema operativo que a VM executa
- A configuração da VM depois de iniciar 
- Os recursos relacionados que a VM precisa

### <a name="naming"></a>Atribuição de nomes

Uma máquina virtual tem um nome atribuído à mesma e ter um nome de computador configurado como parte do sistema operativo. O nome de uma VM pode ter até 15 carateres.

Se utilizar a pilha do Azure para criar o disco de sistema operativo, o nome do computador e o nome da máquina virtual são os mesmos. Se carregar e utilizar a sua própria imagem que contém um sistema operativo configurado anteriormente e utilizá-la para criar uma máquina virtual, os nomes poderão ser diferentes. Ao carregar o seu próprio ficheiro de imagem, certifique-o nome do computador no sistema operativo e a máquina virtual nome os mesmos, como melhor prática.

### <a name="vm-size"></a>Tamanho da VM

O tamanho da VM que utiliza é determinado pela carga de trabalho que pretende executar. O tamanho que escolher determina fatores como o poder de processamento, a memória e capacidade de armazenamento. Pilha do Azure oferece uma variedade de tamanhos para suportar muitos tipos de utilizações.

### <a name="vm-limits"></a>Limites VM

A subscrição tem limites de quota predefinidos que pode afetar a implementação de várias VMs para o seu projeto. O limite atual numa base por subscrição é de 20 VMs por região.

### <a name="operating-system-disks-and-images"></a>Discos do sistema operativo e imagens

As máquinas virtuais utilizam discos rígidos virtuais (VHDs) para armazenar o respetivo sistema operativo (SO) e dados. Os VHDs também são utilizados para as imagens que pode escolher para instalar um SO.
Pilha do Azure fornece um mercado para utilizar com várias versões e tipos de sistemas operativos. A imagens do marketplace são identificadas pelo publicador da imagem, oferta, sku e versão (normalmente, a versão especificada é a mais recente).

A tabela seguinte mostra algumas formas que pode encontrar as informações de uma imagem:


|Método|Descrição|
|---------|---------|
|Portal de pilha do Azure|Os valores são especificados automaticamente ao selecionar uma imagem a utilizar.|
|Azure Stack do PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|APIs REST     |[Listar publicadores de imagem](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<br>[Listar ofertas da imagem](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[Listar SKUs de imagem](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

Pode escolher carregar e utilizar a sua própria imagem. Se o fizer, o nome do publicador, oferta e sku não são utilizados.

### <a name="extensions"></a>Extensões

Extensões VM fornecer capacidades adicionais sua VM, através da configuração de implementação de post e tarefas automatizadas.
Estas tarefas comuns podem ser realizadas com extensões:

* Executar scripts personalizados – a extensão de Script personalizado ajuda-o a configurar as cargas de trabalho na VM, executando o script quando a VM está aprovisionada.
* Implementar e gerir configurações – extensão o PowerShell pretendido Estado Configuration (DSC) ajuda-o a configurar DSC numa VM para gerir configurações e ambientes.
* Dados de diagnóstico recolher – a extensão de diagnóstico do Azure ajuda-o a configurar a VM para recolher dados de diagnóstico que podem ser utilizados para monitorizar o estado de funcionamento da sua aplicação.

### <a name="related-resources"></a>Recursos relacionados

Os recursos na tabela seguinte são utilizados pela VM e tem de existir ou ser criados quando é criada a VM.


|Recurso|Necessário|Descrição|
|---------|---------|---------|
|Grupo de recursos|Sim|A VM tem de estar contida num grupo de recursos.|
|Conta de armazenamento|Sim|A VM precisa da conta de armazenamento para armazenar os respetivos discos rígidos virtuais.|
|Rede virtual|Sim|A VM tem de ser um membro de uma rede virtual.|
|Endereço IP público|Não|A VM pode ter um endereço IP público atribuído para aceder ao mesmo remotamente.|
|Interface de rede|Sim|A VM precisa da interface de rede para comunicar na rede.|
|discos de dados|Não|A VM pode incluir discos de dados para expandir as capacidades de armazenamento.|

## <a name="how-do-i-create-my-first-vm"></a>Como posso criar a minha primeira VM?

Existem várias opções para criar uma VM. À sua escolha depende do seu ambiente.
A tabela seguinte fornece informações para ajudá-lo ao criar a VM.


|Método|Artigo|
|---------|---------|
|Portal de pilha do Azure|Criar uma máquina virtual do Windows com o portal de pilha do Azure<br>[Criar uma máquina virtual de Linux através do portal de pilha do Azure](azure-stack-quick-linux-portal.md)|
|Modelos|Modelos de início rápido de pilha do Azure estão localizados em:<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[Criar uma máquina virtual do Windows utilizando o PowerShell na pilha do Azure](azure-stack-quick-create-vm-windows-powershell.md)<br>[Criar uma máquina virtual Linux utilizando o PowerShell na pilha do Azure](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[Criar uma máquina virtual do Windows com o CLI na pilha do Azure](azure-stack-quick-create-vm-windows-cli.md)<br>[Criar uma máquina virtual Linux ao utilizar a CLI na pilha do Azure](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Como posso gerir a VM que criei?

As VMs podem ser geridas com um portal baseado num browser, ferramentas de linha de comandos com suporte para processamento de scripts ou diretamente através de APs. Algumas tarefas de gestão típicas que pode executar são obter informações sobre uma VM, iniciar sessão numa VM, gerir a disponibilidade e efetuar cópias de segurança.

### <a name="get-information-about-a-vm"></a>Obter informações sobre uma VM

A tabela seguinte mostra algumas das formas como pode obter informações sobre uma VM.


|Método|Descrição|
|---------|---------|
|Portal de pilha do Azure|No hub menu, clique em máquinas virtuais e, em seguida, selecione a VM na lista. Na página para a VM, tem acesso a informações de descrição geral, os valores de definição e métricas de monitorização.|
|Azure PowerShell|Gerir VMs é semelhante no Azure e pilha do Azure. Para obter mais informações sobre como utilizar o PowerShell, consulte o tópico seguinte do Azure:<br>[Criar e gerir VMs do Windows com o módulo Azure PowerShell](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|SDKs do Cliente|Utilizar c# para gerir VMs é semelhante no Azure e pilha do Azure. Para obter mais informações, consulte o tópico seguinte do Azure:<br>[Criar e gerir VMs do Windows no Azure com c#](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-the-vm"></a>Ligar à VM

Pode utilizar o **Connect** botão no portal do Azure pilha para ligar à VM.

## <a name="next-steps"></a>Passos seguintes
* [Considerações para máquinas virtuais na pilha do Azure](azure-stack-vm-considerations.md)

