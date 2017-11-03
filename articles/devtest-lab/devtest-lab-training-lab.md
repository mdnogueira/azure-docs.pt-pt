---
title: "Utilizar o Azure DevTest Labs para formação | Microsoft Docs"
description: "Saiba como utilizar o Azure DevTest Labs para cenários de preparação."
services: devtest-lab,virtual-machines
documentationcenter: na
author: steved0x
manager: douge
editor: 
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: sdanie
ms.openlocfilehash: 3bad7538121e1049af953e3937fc679b1d9184d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-devtest-labs-for-training"></a>Utilizar o Azure DevTest Labs para formação
Azure DevTest Labs pode ser utilizado para implementar vários cenários de chaves para além de programador/teste. Uma desses cenários é configurar um laboratório para formação. Azure DevTest Labs permite-lhe criar um laboratório onde pode fornecer modelos personalizados que cada trainee pode utilizar para criar ambientes idênticas e isolados para formação. Pode aplicar políticas para garantir que os ambientes de formação estão disponíveis para cada trainee apenas quando precisar delas e conter recursos suficientes - como as máquinas virtuais - necessários para a formação. Por fim, pode partilhar facilmente o laboratório com trainees, que pode aceder a num clique.

![Utilizar DevTest Labs para formação](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure DevTest Labs cumpre os seguintes requisitos que são necessárias para realize formação em qualquer ambiente virtual: 

* Trainees não pode ver as VMs criadas por outros trainees
* Todas as máquinas de formação devem ser idênticas
* Trainees pode aprovisionar rapidamente os seus ambientes de formação
* Controlar os custos, garantindo que trainees não é possível obter as mais VMs que necessitam para os cenários e estimativas também encerramento VMs quando não estão a utilizá-las
* Partilhar facilmente o laboratório de formação com cada trainee
* Reutilizar o laboratório de formação novamente e novamente

Neste artigo, conheça diversas funcionalidades do Azure DevTest Labs que podem ser utilizadas para satisfazer os requisitos de formação descrito anteriormente e os passos detalhados que pode seguir para configurar um laboratório para formação.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Implementar a formação com o Azure DevTest Labs
1. **Crie o laboratório** 
   
    Laboratórios são o ponto de partida no Azure DevTest Labs. Depois de criar um laboratório, pode realizar tarefas, tais como adicionar utilizadores (trainees) para o laboratório, definir políticas para controlar os custos, definir imagens VM que podem criar rapidamente e muito mais.   
   
    Saiba mais clicando nas ligações na seguinte tabela:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Criar um laboratório no Azure DevTest Labs](devtest-lab-create-lab.md) |Saiba como criar um laboratório no Azure DevTest Labs no portal do Azure. |
2. **Criar VMs de formação em minutos com imagens do marketplace pronta a utilizar e imagens personalizadas** 
   
    Pode escolher pronta a utilizar imagens a partir de uma ampla variedade de imagens no Azure Marketplace e disponibilizá-los para trainees no laboratório. Se as imagens pronta a utilizar não cumprem os requisitos, pode criar uma imagem personalizada através da criação de um laboratório VM com uma imagem pronta a utilizar o Azure Marketplace, instalar o software que precisa para a formação e guardar a VM como imagem personalizada no laboratório. 
   
    Saiba mais clicando nas ligações na seguinte tabela:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Configurar imagens do Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Saiba como pode imagens do Azure Marketplace lista branca; pretende disponibilizar para seleção apenas as imagens para a formação. |
   | [Criar uma imagem personalizada](devtest-lab-create-template.md) |Crie uma imagem personalizada previamente instalando o software que precisa para a formação, para que trainees pode criar rapidamente uma VM utilizando a imagem personalizada. |
3. **Criar modelos reutilizáveis para máquinas de formação** 
   
    Uma fórmula no Azure DevTest Labs se uma lista predefinida de valores de propriedade utilizado para criar uma VM. Pode criar uma fórmula no laboratório, escolha uma imagem, um tamanho VM (uma combinação de CPU e RAM) e uma rede virtual. Cada trainee pode ver a fórmula no laboratório e utilizá-la para criar uma VM. 
   
    Saiba mais clicando nas ligações na seguinte tabela:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Gerir fórmulas DevTest Labs para criar VMs](devtest-lab-manage-formulas.md) |Saiba como pode criar uma fórmula por diretriz segurança de uma imagem, tamanho da VM (combinação de CPU e RAM) e uma rede virtual. |
4. **Controlar os custos**
   
    Azure DevTest Labs permite-lhe definir uma política no laboratório para especificar o número máximo de VMs que podem ser criadas por um trainee no laboratório. 
   
    Se estiver a realizar formação de dia multi e pretende parar todas as VMs num determinado momento do dia e, em seguida, automaticamente reiniciá-las ao dia seguinte, pode facilmente realizar que através da definição de encerramento automático e políticas no laboratório de início automático. 
   
    Por fim, depois de concluída a formação pode eliminar todas as VMs em simultâneo, executando um script único do PowerShell. 
   
    Saiba mais clicando nas ligações na seguinte tabela:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Definir políticas de laboratório](devtest-lab-set-lab-policy.md) |Controlar os custos através da definição de políticas no laboratório. |
   | [Eliminar todos os laboratório VMs utilizando um script do PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Elimine todos os laboratórios numa única operação, depois de concluída a formação. |
5. **Partilhar o laboratório com cada trainee**
   
    Laboratórios podem ser acedidos de diretamente através de uma ligação que partilha com o seu trainees. Os trainees não mesmo tem de ter uma conta do Azure, desde tenham um [conta Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Trainees não é possível ver as VMs criadas por outros trainees.  
   
    Saiba mais clicando nas ligações na seguinte tabela:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Adicionar um trainee para um laboratório no Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Utilize o portal do Azure para adicionar trainees para o laboratório de formação. |
   | [Adicionar trainees para o laboratório utilizar um script do PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Utilize o PowerShell para automatizar trainees adicionar para o laboratório de formação. |
   | [Obtenha uma ligação para o laboratório](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Saiba como um laboratório pode ser acedido diretamente através de uma hiperligação. |
6. **Reutilizar o laboratório novamente e novamente** 
   
    Pode automatizar a criação de laboratório, incluindo as definições personalizadas, ao criar um modelo do Resource Manager e utilizar para criar laboratórios idênticos novamente e de novo. 
   
    Saiba mais clicando nas ligações na seguinte tabela:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Criar um laboratório com um modelo do Resource Manager](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Crie laboratórios no Azure DevTest Labs utilizando modelos do Resource Manager. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

