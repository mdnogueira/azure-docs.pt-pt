---
title: Conceitos de DevTest Labs | Microsoft Docs
description: "Conheça os conceitos básicos do DevTest Labs e como este pode facilitar a criar, gerir e monitorizar máquinas virtuais do Azure"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 105919e8-3617-4ce3-a29f-a289fa608fb2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
ms.openlocfilehash: 7028cdacf7d597c238bf29bd26bbd44705d66224
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="devtest-labs-concepts"></a>Conceitos de Laboratórios DevTest
## <a name="overview"></a>Descrição geral
A lista seguinte contém os principais conceitos DevTest Labs e definições:

## <a name="labs"></a>Laboratórios
Um laboratório é a infraestrutura que abrange a um grupo de recursos, tais como máquinas virtuais (VMs), que lhe permite um melhor gerir esses recursos, especificando os limites e quotas.

## <a name="virtual-machine"></a>Máquina virtual
VM do Azure é um dos vários tipos de [recursos informáticos a pedido, dimensionáveis](https://docs.microsoft.com/azure/app-service/choose-web-site-cloud-service-vm) que o Azure oferece. As VMs do Azure dão-lhe a flexibilidade de Virtualização sem ter de comprar e manter o hardware físico que executa, apesar de ainda tem de manter a VM efetuando determinadas tarefas, tais como configurar, aplicar patches e instalar o software que é executado no mesmo.

[Descrição geral de máquinas virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-overview) fornecem informações sobre o que deve considerar antes de criar uma VM, como criar e como a geri-lo.

## <a name="claimable-vm"></a>Claimable VM
VM Claimable do Azure é uma máquina virtual que está disponível para utilização por qualquer utilizador de laboratório com permissões. Um administrador do laboratório, pode preparar as VMs com imagens base específicas e artefactos e guardá-las a um agrupamento partilhado. Um utilizador do laboratório, em seguida, pode reclamá um VM do conjunto de trabalho quando precisar de uma com que a configuração específica.

Uma VM que é claimable inicialmente não está atribuída a qualquer utilizador específico, mas irá aparecer na lista de todos os utilizadores em "Claimable virtual machines". Depois de uma VM foi reclamada por um utilizador, é movido até as respetivas "Os meus máquinas virtuais" área e já não consta claimable por qualquer outro utilizador.

## <a name="environment"></a>Ambiente
No DevTest Labs, um ambiente refere-se a uma coleção de recursos do Azure num laboratório. [Esta mensagem de blogue](https://blogs.msdn.microsoft.com/devtestlab/2016/11/16/connect-2016-news-for-azure-devtest-labs-azure-resource-manager-template-based-environments-vm-auto-shutdown-and-more/) descreve como criar ambientes de várias VMS a partir de modelos Azure Resource Manager.

## <a name="base-images"></a>Imagens de base
Imagens de base são imagens VM com as ferramentas e definições pré-instalado e configurado para criar rapidamente uma VM. Pode aprovisionar uma VM ao escolher uma base de existente e adicionar um artefacto para instalar o agente de teste. Em seguida, pode guardar a VM aprovisionada como base para que a base pode ser utilizada sem ter de reinstalar o agente de teste para cada aprovisionamento da VM.

## <a name="artifacts"></a>Artefactos
Artefactos são utilizados para implementar e configurar a sua aplicação depois de uma VM está aprovisionada. Os artefactos podem ser:

* Ferramentas que pretende instalar na VM - como agentes, o Fiddler e o Visual Studio.
* Ações que pretende executar na VM - como clonar um repositório.
* Aplicações que pretende testar.

Os artefactos [do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) ficheiros JSON que contém instruções para efetuar a implementação e aplicar a configuração.

## <a name="artifact-repositories"></a>Repositórios de artefactos
Repositórios de artefacto são repositórios de git onde os artefactos são verificados no. Repositórios de artefacto podem ser adicionados a vários laboratórios na sua organização partilha e reutilização de ativação.

## <a name="formulas"></a>Fórmulas
As fórmulas, para além das imagens de base, fornecem um mecanismo para aprovisionamento rápido de VM. Uma fórmula no DevTest Labs se uma lista predefinida de valores de propriedade utilizado para criar um laboratório de VM.
Com as fórmulas, VMs com o mesmo conjunto de propriedades, tais como a imagem de base, tamanho VM, rede virtual e artefactos - podem ser criados sem necessidade de especificar as propriedades de cada vez. Quando criar uma VM a partir de uma fórmula, os valores predefinidos podem ser utilizados como-é ou modificado.

## <a name="policies"></a>Políticas
Políticas de ajudam a controlar o custo no laboratório. Por exemplo, pode criar uma política para automaticamente encerrado VMs com base numa agenda definida.

## <a name="caps"></a>Caps
Caps é um mecanismo para minimizar as perdas no laboratório. Por exemplo, pode definir um limite para restringir o número de VMs que podem ser criadas por utilizador ou num laboratório.

## <a name="security-levels"></a>Níveis de segurança
Acesso de segurança é determinado pelo controlo de acesso em funções do Azure (RBAC). Para compreender como funciona o acesso, ajuda a compreender as diferenças entre uma permissão, uma função e um âmbito conforme definido pelo RBAC.

* Permissão - uma permissão é um acesso definidas para uma ação específica (por exemplo, acesso de leitura para todas as máquinas virtuais).
* Função - uma função é um conjunto de permissões que podem ser agrupados e atribuído a um utilizador. Por exemplo, o *proprietário da subscrição* função tem acesso a todos os recursos numa subscrição.
* O âmbito - um âmbito é um nível na hierarquia de um recurso do Azure, como um grupo de recursos, um laboratório único ou a subscrição completa.

No âmbito da DevTest Labs, existem dois tipos de funções para definir as permissões de utilizador: proprietário de laboratório e de utilizador do laboratório.

* Proprietário do laboratório - um proprietário de laboratório tem acesso a quaisquer recursos no laboratório. Por conseguinte, um proprietário de laboratório pode modificar as políticas, ler e escrever quaisquer VMs, altere a rede virtual e assim sucessivamente.
* Utilizador do laboratório - um utilizador do laboratório, pode ver todos os recursos de laboratório, como VMs, políticas e redes virtuais, mas não é possível modificar as políticas ou VMs criados por outros utilizadores.

Para ver como criar funções personalizadas no DevTest Labs, consulte o artigo, [conceder permissões de utilizador para políticas de laboratório específico](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Uma vez que âmbitos são hierárquicos, quando um utilizador tem permissões num determinado âmbito, automaticamente lhes foram concedidas essas permissões a cada âmbito de nível inferior englobados. Por exemplo, se um utilizador está atribuído à função de proprietário da subscrição, em seguida, têm acesso a todos os recursos numa subscrição, que incluem todas as máquinas virtuais, todas as redes virtuais e todos os laboratórios. Por conseguinte, um proprietário da subscrição herda automaticamente a função de proprietário do laboratório. No entanto, o oposto não é verdadeiro. Um proprietário de laboratório tem acesso a um laboratório, que é um âmbito inferior ao nível da subscrição. Por conseguinte, um proprietário do laboratório não será capaz de ver as máquinas virtuais ou redes virtuais ou quaisquer recursos que estão fora do laboratório.

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager
Todos os conceitos discutidos neste artigo podem ser configurados utilizando os modelos Azure Resource Manager, que permitem-lhe definir a infraestrutura de configuração da sua solução do Azure e implementar repetidamente num estado consistente.

[Compreender a estrutura e a sintaxe de modelos Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates#template-format) descreve a estrutura de um modelo Azure Resource Manager e as propriedades que estão disponíveis nas secções de um modelo diferentes.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
[Criar um laboratório no DevTest Labs](devtest-lab-create-lab.md)
