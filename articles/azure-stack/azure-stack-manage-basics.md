---
title: "Noções básicas de administração de pilha do Azure | Microsoft Docs"
description: Saiba o que precisa de saber para administrar a pilha do Azure.
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 04dea8f055eb562455b568c43553a6fefe749467
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-administration-basics"></a>Noções básicas de administração de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Existem várias coisas que precisa de saber se estiver familiarizado com a administração de pilha do Azure. Esta orientação fornece uma descrição geral da sua função como um operador de pilha do Azure e o que precisa dizer aos utilizadores para que estes fiquem produtivos rapidamente.

## <a name="understand-the-builds"></a>Compreender as compilações

### <a name="integrated-systems"></a>Sistemas integrados

Se estiver a utilizar um sistema de pilha do Azure integrado, versões atualizadas da pilha do Azure são distribuídas através de pacotes de atualização. Pode importar estes pacotes e aplicá-las utilizando o mosaico de atualizações no portal do administrador.
 
### <a name="development-kit"></a>Kit de desenvolvimento

Se estiver a utilizar o Kit de desenvolvimento de pilha do Azure, reveja o [Novidades pilha do Azure?](azure-stack-poc.md) artigo para se certificar de que compreende o objetivo do kit de desenvolvimento e o respetivas limitações. Deve utilizar o kit de desenvolvimento como uma "sandbox," onde pode avaliar pilha do Azure e desenvolver e testar as suas aplicações num ambiente de não produção. (Para obter informações de implementação, consulte o [implementação Kit de desenvolvimento de pilha do Azure](azure-stack-deploy-overview.md) início rápido.)

Como o Azure, podemos inovar rapidamente. Lançamos irá regularmente novas compilações. Se estiver a executar o kit de desenvolvimento e de que pretende mover para a compilação mais recente, tem de [Reimplementar a pilha de Azure](azure-stack-redeploy.md). Não é possível aplicar os pacotes de atualização. Este processo demora tempo, mas a vantagem é que pode experimentar funcionalidades mais recentes. A documentação do kit de desenvolvimento no nosso site reflete a compilação da versão mais recente.

## <a name="learn-about-available-services"></a>Saiba mais sobre serviços disponíveis

Irá precisar de uma deteção de quais os serviços que pode disponibilizar aos seus utilizadores. Pilha do Azure suporta um subconjunto de serviços do Azure. A lista de serviços suportados irá continuar a evoluir.

**Serviços fundamentais sobre**

Por predefinição, a pilha de Azure inclui os seguintes "dos serviços" quando implementa a pilha do Azure:

- Computação
- Armazenamento
- Redes
- Cofre de Chaves

Com estes serviços fundamentais sobre, pode oferecer infraestrutura-como-um-serviço (IaaS) aos seus utilizadores com configuração mínima.

**Serviços adicionais**

Suportamos atualmente os seguintes serviços de plataforma-como-um-serviço (PaaS) adicionais:

- Serviço de Aplicações
- Funções do Azure
- Bases de dados do SQL Server e o MySQL

Estes serviços requerem configuração adicional antes de poder torná-los disponíveis para os seus utilizadores. Para obter mais informações, consulte "Tutoriais" e as secções "serviços de procedimentos guides\Offer" da nossa documentação de operador de pilha do Azure.

**Plano de serviço**

Pilha do Azure irá continuar a adicionar suporte para serviços do Azure. Para o plano de prevista, consulte o [pilha do Azure: uma extensão do Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) documento. Também pode monitorizar o [publicações no blogue do Azure pilha](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) para anúncios de novo.

## <a name="what-tools-do-i-use-to-manage"></a>As ferramentas de utilizar para gerir?
 
Pode utilizar o [portal de administrador](azure-stack-manage-portals.md) ou o PowerShell para gerir a pilha do Azure. Conheça os conceitos básicos a forma mais fácil é através do portal. Se pretender utilizar o PowerShell, existem passos de preparação. Tem [instalar](azure-stack-powershell-install.md) PowerShell, [transferir](azure-stack-powershell-download.md) módulos adicionais, e [configurar](azure-stack-powershell-configure-admin.md) PowerShell.

Pilha do Azure utiliza o Azure Resource Manager, como o mecanismo de implementação, gestão e organização subjacente. Se gerir a pilha do Azure e ajudar a suportar os utilizadores, deve saber mais sobre o Gestor de recursos. Consulte o [introdução com o Azure Resource Manager](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) documento.

## <a name="your-typical-responsibilities"></a>Suas responsabilidades típicas

Os seus utilizadores pretendem utilizar os serviços. Perspetiva, a sua função principal é disponibilizar estes serviços aos mesmos. Tem de decidir quais os serviços para oferecer e tornar esses serviços disponíveis através da criação de esquemas, ofertas e quotas. Para obter mais informações, consulte [descrição geral da oferta de serviços na pilha de Azure](azure-stack-offer-services-overview.md). 

Também terá de adicionar os itens para [marketplace](azure-stack-marketplace.md), tais como imagens da máquina virtual. A forma mais fácil é [transferir itens do marketplace do Azure para Azure pilha](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Se pretender testar os planos, ofertas e serviços, deve utilizar o [portal de utilizador](azure-stack-manage-portals.md); não o portal de administrador.

Além de fornecer serviços, tem de efetuar todas as os deveres regulares de um operador para manter a pilha do Azure em execução. Estas tarefas incluem o seguinte:

- Adicionar contas de utilizador (para [do Azure Active Directory](azure-stack-add-new-user-aad.md) implementação ou para [serviços de Federação do Active Directory](azure-stack-add-users-adfs.md) implementação)
- [Atribuir funções (RBAC) do controlo de acesso baseado em funções](azure-stack-manage-permissions.md) (esta não está restrita a administradores.)
- [Infraestrutura de monitor do Estado de funcionamento](azure-stack-monitor-health.md)
- Gerir [rede](azure-stack-viewing-public-ip-address-consumption.md) e [armazenamento](azure-stack-manage-storage-accounts.md) recursos
- Substituir o hardware danificado, por exemplo [substituir um disco com falha](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>O que dizer aos utilizadores

Terá de informar os utilizadores como trabalhar com serviços na pilha do Azure, como estabelecer ligação ao ambiente e como subscrever ofertas. Para além de qualquer documentação personalizada que poderá pretender fornecer os seus utilizadores, pode direcionar os utilizadores para o site de documentação de utilizadores de pilha do Azure.

**Compreender como trabalhar com serviços na pilha do Azure**

Não há informações que os utilizadores têm de compreender antes de poderem utilizam os serviços e criar aplicações na pilha do Azure. Por exemplo, existem requisitos específicos da versão do PowerShell e API. Além disso, existem algumas diferenças de funcionalidade entre um serviço no Azure e o serviço equivalente na pilha do Azure. Certifique-se de que os utilizadores, consulte os seguintes artigos:

- [Considerações da chave: utilizar os serviços ou na criação de aplicações para a pilha do Azure](user/azure-stack-considerations.md)
- [Considerações para máquinas virtuais na pilha do Azure](user/azure-stack-vm-considerations.md)
- [Armazenamento: diferenças e as considerações](user/azure-stack-acs-differences.md)

As informações nestes artigos resume as diferenças entre um serviço no Azure e pilha do Azure. Complementa as informações que estão disponíveis para um serviço do Azure na documentação do global do Azure.

**Ligar a pilha do Azure como um utilizador**

Num ambiente de kit de desenvolvimento, se um utilizador não tem acesso de ambiente de trabalho remoto para o anfitrião de kit de desenvolvimento, tem de configurar uma ligação de rede privada virtual (VPN) antes de poderem aceder pilha do Azure. Consulte [ligar ao Azure pilha](azure-stack-connect-azure-stack.md). 

Os seus utilizadores irão querem saber como [aceder ao portal de utilizador ](user/azure-stack-use-portal.md) ou como ligar através do PowerShell. Num ambiente sistemas integrada, o endereço de portal de utilizador varia por implementação. Terá de fornecer os seus utilizadores com o URL correto.

Se utilizar o PowerShell, os utilizadores podem ter registar fornecedores de recursos antes de poderem utilizar os serviços. (Um fornecedor de recursos gere um serviço. Por exemplo, o fornecedor de recursos de rede gere os recursos, tais como redes virtuais, interfaces de rede e Balanceadores de carga.) Têm [instalar](user/azure-stack-powershell-install.md) PowerShell, [transferir](user/azure-stack-powershell-download.md) módulos adicionais, e [configurar](user/azure-stack-powershell-configure-user.md) PowerShell (que inclui o registo do fornecedor de recursos).

**Subscrever uma oferta**

Antes de um utilizador pode aceder aos serviços, tem [subscrever uma oferta](azure-stack-subscribe-plan-provision-vm.md) que criou como um operador.

## <a name="where-to-get-support"></a>Onde obter suporte

### <a name="integrated-systems"></a>Sistemas integrados

Para um sistema integrado, há uma coordenada de escalamento e o processo de resolução entre a Microsoft e os nossos parceiros de hardware de fabricante de equipamento original (OEM).

Se existir um problema de serviços em nuvem, o suporte é fornecido através da Microsoft ao cliente suportar (CSS). Se clicar no ícone de ajuda e suporte (ponto de interrogação) no canto superior direito do portal de administrador e, em seguida, clique em **novo pedido de suporte**, esta ação abre um site onde diretamente pode abrir um pedido de suporte.

Se existir um problema com a implementação, patch e atualização, (incluindo unidades substituível em campo) de hardware e software qualquer imagem corporativa hardware, como o software em execução no anfitrião de ciclo de vida de hardware, contacte o fornecedor do hardware OEM primeiro.

Para mais nada, contacte o Microsoft CSS.

### <a name="development-kit"></a>Kit de desenvolvimento

Para o kit de desenvolvimento, pode colocar perguntas relacionadas com suporte no [fóruns do Microsoft](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Se clicar no ícone de ajuda e suporte (ponto de interrogação) no canto superior direito do portal de administrador e, em seguida, clique em **novo pedido de suporte**, esta ação abre o site fóruns diretamente. Estes fóruns regularmente são monitorizados. Porque o kit de desenvolvimento é um ambiente de avaliação, não há nenhum oficial de suporte oferecida através do Microsoft CSS.

## <a name="next-steps"></a>Passos seguintes

- [Gestão de região na pilha do Azure](azure-stack-region-management.md)


