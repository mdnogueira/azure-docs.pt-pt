---
title: Utilizar o Azure DevTest Labs para ambientes de teste de VM e PaaS | Microsoft Docs
description: "Saiba como utilizar o Azure DevTest Labs para cenários de ambiente de teste VM e PaaS."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: d4e2c334-643a-40c9-9051-625b8f39fc86
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: tarcher
ms.openlocfilehash: 6926fe1be44c078482dd5073788e36d7ae77efef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-devtest-labs-for-vm-and-paas-test-environments"></a>Ambientes de teste de utilização do Azure DevTest Labs para a VM e PaaS

Pode utilizar o Azure DevTest Labs para implementar vários cenários de chaves, mas um cenário primário envolve a utilização de DevTest Labs para máquinas de anfitrião para testers. 

Neste cenário, o DevTest Labs fornece destas vantagens:

- Testers podem testar a versão mais recente da respetiva aplicação por rapidamente aprovisionamento ambientes Windows e Linux utilizando modelos reutilizáveis e artefactos.
- Testers podem dimensionar a sua carga testes por múltiplos agentes de teste de aprovisionamento.
- Os administradores podem controlar os custos, garantindo que:
  - Testers não é possível obter as mais VMs que necessitam.
  - VMs são encerradas quando não em utilização.

![Utilizar DevTest Labs para formação](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

Neste artigo, conheça diversas funcionalidades do Azure DevTest Labs utilizadas para satisfazer os requisitos de tester e os passos detalhados a seguir ao configurar um laboratório.

## <a name="implementing-test-environments-with-azure-devtest-labs"></a>Implementação de ambientes de teste com o Azure DevTest Labs
1. **Crie o laboratório** 
   
    Laboratórios são o ponto de partida no Azure DevTest Labs. Depois de criar um laboratório, pode realizar tarefas, tais como adicionar utilizadores (testers) para o laboratório, definir políticas para controlar os custos, definir imagens VM que podem criar rapidamente e muito mais.  
   
    Saiba mais clicando nas ligações na seguinte tabela:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Criar um laboratório no Azure DevTest Labs](devtest-lab-create-lab.md) |Saiba como criar um laboratório no Azure DevTest Labs no portal do Azure. |
2. **Criar VMs em minutos com imagens do marketplace pronta a utilizar e imagens personalizadas** 
   
    Pode escolher pronta a utilizar imagens a partir de uma ampla variedade de imagens no Azure Marketplace e disponibilizá-los no laboratório. Se as imagens pronta a utilizar não cumprem os requisitos, pode criar uma imagem personalizada através da criação de um laboratório VM com uma imagem pronta a utilizar o Azure Marketplace, instalar o software que precisa, e guardar a VM como uma imagem personalizada no laboratório.

    Se planear utilizar imagens personalizadas, considere utilizar uma fábrica de imagem para criar e distribuir as imagens. Uma fábrica de imagem é uma solução de configuração como código que cria e distribui as imagens configuradas automaticamente de regularmente. Isto poupa tempo necessário para configurar manualmente o sistema depois de criar uma VM com o SO de base.
  
    Saiba mais clicando nas ligações na seguinte tabela:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Configurar imagens do Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Saiba como pode imagens do Azure Marketplace lista branca, efetuar apenas as imagens que pretende para as testers disponíveis para seleção.|
   | [Criar uma imagem personalizada](devtest-lab-create-template.md) |Crie uma imagem personalizada através da pré-instalação do software de que necessita para que testers podem criar rapidamente uma VM utilizando a imagem personalizada.|
   | [Saiba mais sobre a fábrica de imagem](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Ver um vídeo que descreve como configurar e utilizar uma fábrica de imagem.|

3. **Criar modelos reutilizáveis para máquinas de teste** 
   
    Uma fórmula no Azure DevTest Labs se uma lista predefinida de valores de propriedade utilizado para criar uma VM. Pode criar uma fórmula no laboratório, escolha uma imagem, um tamanho VM (uma combinação de CPU e RAM) e uma rede virtual. Cada tester pode ver a fórmula no laboratório e utilizá-la para criar uma VM. 
   
    Saiba mais clicando nas ligações na seguinte tabela:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Gerir fórmulas DevTest Labs para criar VMs](devtest-lab-manage-formulas.md) |Saiba como pode criar uma fórmula por diretriz segurança de uma imagem, tamanho da VM (combinação de CPU e RAM) e uma rede virtual.|

3. **Criar ambientes de teste de várias VMS** 
   
    Pode utilizar os modelos Azure Resource Manager para definir a infraestrutura e a configuração da sua solução do Azure e implementar repetidamente o teste de várias VMs num estado consistente.

    Recursos de PaaS do Azure podem ser aprovisionados num ambiente de um modelo do Resource Manager e são apresentados no rastreamento de custos. No entanto, o encerramento automático VM não é aplicável a recursos de PaaS.

    Saiba mais clicando nas ligações na seguinte tabela:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Criar ambientes multi-VM e recursos PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md) |Saiba como pode implementar várias VMs num estado consistente para o seu ambiente de teste.|

4. **Criar artefactos para ativar a personalização de VM flexível**

   Artefactos são utilizados para implementar e configurar a sua aplicação depois de uma VM está aprovisionada. Os artefactos podem ser:

   - Ferramentas que pretende instalar na VM - como agentes, o Fiddler e o Visual Studio.
   - Ações que pretende executar na VM - como clonar um repositório.
   - Aplicações que pretende testar.

   Muitos artefactos já estão disponível out-of-a-box. Mas se pretender mais personalização para as suas necessidades específicas, pode criar os seus artefactos personalizados.

   Saiba mais clicando nas ligações na seguinte tabela:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Criar artefactos personalizados para a VM do DevTest Labs](devtest-lab-artifact-author.md) |Crie os seus artefactos personalizados para as máquinas virtuais no laboratório.|
   | [Adicione um repositório de Git para armazenar artefactos personalizados e modelos do Azure Resource Manager para utilização no Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Saiba como armazenar os artefactos personalizados no seu próprio repositório de Git privado.|

5. **Controlar os custos**
   
    Azure DevTest Labs permite-lhe definir uma política no laboratório para especificar o número máximo de VMs que podem ser criadas por um recurso de teste no laboratório. 
   
    Se a sua equipa de teste tem um conjunto de trabalho agenda e de pretende parar todas as VMs num determinado momento do dia e, em seguida, automaticamente reiniciá-las ao dia seguinte, pode realizar facilmente que através da definição de políticas de encerramento automático e de início automático no laboratório. 
   
    Por fim, quando o desenvolvimento de aplicações estiver concluído, pode eliminar todas as VMs em simultâneo, executando um script único do PowerShell. 
   
    Saiba mais clicando nas ligações na seguinte tabela:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Definir políticas de laboratório](devtest-lab-set-lab-policy.md) |Controlar os custos através da definição de políticas no laboratório. |
   | [Eliminar todos os laboratório VMs utilizando um script do PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Elimine todos os laboratórios numa única operação quando o teste está concluído.|

1. **Adicionar uma rede virtual para um laboratório** 
   
    DevTest Labs cria uma nova rede virtual (VNET) sempre que é criado um laboratório. Se tiver configurado o seus próprios VNET – por exemplo, utilizando o ExpressRoute ou VPN de site para site – pode adicionar esta VNET para definições de rede virtual do seu laboratório, de modo a que esteja disponível durante a criação de VMs.

    Além disso, não há do Azure Active Directory domínio associação artefacto disponível que associa uma VM a um domínio quando a VM está a ser criada. 
   
    Saiba mais clicando nas ligações na seguinte tabela:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Configurar uma rede virtual no Azure DevTest Labs](devtest-lab-configure-vnet.md) |Saiba como configurar uma rede virtual no Azure DevTest Labs no portal do Azure.|

6. **Partilhar o laboratório com cada recurso de teste**
   
    Laboratórios podem ser acedidos de diretamente através de uma ligação que partilha com o seu testers. Não, mesmo têm de ter uma conta do Azure, desde tenham um [conta Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Testers não é possível ver as VMs criadas por outros testers.  
   
    Saiba mais clicando nas ligações na seguinte tabela:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Adicione um recurso de teste para um laboratório no Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Utilize o portal do Azure para adicionar testers para o laboratório.|
   | [Adicionar testers para o laboratório utilizar um script do PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Utilize o PowerShell para automatizar testers adicionar para o laboratório. |
   | [Obtenha uma ligação para o laboratório](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Saiba como testers podem aceder diretamente a um laboratório através de uma hiperligação.|

7. **Automatizar a criação de laboratório para mais equipas** 
   
    Pode automatizar a criação de laboratório, incluindo as definições personalizadas, ao criar um modelo do Resource Manager e utilizar para criar laboratórios idênticos novamente e de novo. 
   
    Saiba mais clicando nas ligações na seguinte tabela:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Criar um laboratório com um modelo do Resource Manager](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Crie laboratórios no Azure DevTest Labs utilizando modelos do Resource Manager. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

