---
title: "Soluções no Operations Management Suite (OMS) | Microsoft Docs"
description: "Soluções de expandam a funcionalidade do Operations Management Suite (OMS), fornecendo os cenários de gestão em pacote que os clientes podem adicionar a sua área de trabalho do OMS.  Este artigo fornece detalhes sobre soluções como personalizadas criadas por clientes e parceiros."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2443dd73fdf441721bd6f6f340da515d9f5a22a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-management-solutions-in-operations-management-suite-oms-preview"></a>Trabalhar com as soluções de gestão no Operations Management Suite (OMS) (pré-visualização)
> [!NOTE]
> Esta documentação é de preliminar para soluções de gestão no OMS que estão atualmente em pré-visualização.    
> 
> 

Soluções de gestão de expandam a funcionalidade do Operations Management Suite (OMS), fornecendo os cenários de gestão em pacote que podem adicionar clientes ao seu respetivo ambiente.  Para além [soluções fornecidas pela Microsoft](../log-analytics/log-analytics-add-solutions.md), parceiros e os clientes podem criar soluções de gestão a ser utilizada no seu próprio ambiente ou disponibilizada para clientes através da Comunidade.

## <a name="finding-and-installing-management-solutions"></a>Localizar e instalar as soluções de gestão
Existem vários métodos para localizar e instalar as soluções de gestão, conforme descrito nas secções seguintes.

### <a name="azure-marketplace"></a>Azure Marketplace
As soluções de gestão fornecido pela Microsoft e parceiros confiadores poderão estar instalados no Azure Marketplace no portal do Azure.

1. Inicie sessão no portal do Azure.
2. No painel esquerdo, selecione **mais serviços**.
3. O desloque para baixo até **soluções** ou tipo *soluções* para o **filtro** caixa de diálogo.
4. Clique em de **+ adicionar** botão.
5. Procurar soluções que está interessado no navegando, clicando no **filtro** botão ou escrever o **Everthing de pesquisa** caixa.
6. Clique num item do marketplace para ver informações detalhadas.
7. Clique em **criar** para abrir o **Adicionar solução** painel.
8. Será solicitado para as informações necessárias, tais como o [OMS área de trabalho e a conta de automatização](#oms-workspace-and-automation-account) para além de valores para os parâmetros na solução.
9. Clique em **criar** para instalar a solução.

### <a name="oms-portal"></a>Portal do OMS
Soluções de gestão fornecidas pela Microsoft podem ser instaladas a partir da Galeria de soluções no portal do OMS.

1. Inicie sessão no portal do OMS.
2. Clique em de **soluções galeria** mosaico.
3. Na página da Galeria de soluções do OMS, saiba mais sobre cada solução disponível. Clique no nome da solução que pretende adicionar ao OMS.
4. Na página para a solução que escolheu, são apresentadas a obter informações detalhadas sobre a solução. Clique em **Adicionar**.
5. Um novo mosaico para a solução que adicionou aparece na descrição geral da página no OMS e pode começar a utilizá-lo Depois do serviço do OMS processa os dados.

### <a name="azure-quickstart-templates"></a>Modelos de Início Rápido do Azure
Os membros da Comunidade podem submeter soluções de gestão de modelos de início rápido do Azure.  Pode transferir estes modelos para a posterior instalação ou Inspecione-los para saber como [criar as suas próprias soluções](#creating-a-solution).

1. Siga o processo descrito no [OMS área de trabalho e a conta de automatização](#oms-workspace-and-automation-account) para ligar uma área de trabalho e a conta.
2. Aceda a [modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/).  
3. Procurar uma solução que lhe interessa.
4. Selecione a solução na lista de resultados para ver os detalhes.
5. Clique em de **implementar no Azure** botão.
6. Será solicitado para fornecer informações tais como o grupo de recursos e a localização para além de valores de quaisquer parâmetros na solução.
7. Clique em **Compra** para instalar a solução.

### <a name="deploy-azure-resource-manager-template"></a>Implementar a modelo Azure Resource Manager
Soluções que obtém a partir da Comunidade ou [de criar manualmente](#creating-a-solution) são implementados como um modelo do Resource Manager, e pode utilizar qualquer um dos métodos padrão de [implementar um modelo](../azure-resource-manager/resource-group-template-deploy-portal.md).  Tenha em atenção que antes de instalar a solução, tem de criar e associar o [OMS área de trabalho e a conta de automatização](#oms-workspace-and-automation-account).

## <a name="oms-workspace-and-automation-account"></a>Área de trabalho OMS e conta de automatização
A maioria das soluções de gestão requerem um [área de trabalho OMS](../log-analytics/log-analytics-manage-access.md) para conter vistas e um [conta de automatização](../automation/automation-security-overview.md#automation-account-overview) para conter os runbooks e recursos relacionados. A área de trabalho e a conta tem de cumprir os seguintes requisitos.

* Só pode utilizar uma solução de uma área de trabalho do OMS e uma conta de automatização.  
* A área de trabalho OMS e a conta de automatização utilizado por uma solução tem de ser ligadas a outro. Uma área de trabalho do OMS só pode ser associada a uma conta de automatização e uma conta de automatização só pode ser associada a uma área de trabalho do OMS.
* Estar ligado, a área de trabalho OMS e a conta de automatização devem estar no mesmo grupo de recursos e região.  A exceção é uma área de trabalho do OMS na região EUA leste e e conta de automatização no Leste dos E.U.A. 2.

### <a name="creating-a-link-between-an-oms-workspace-and-automation-account"></a>Criar uma ligação entre uma conta de automatização e área de trabalho do OMS
Como especificar a área de trabalho do OMS e conta de automatização depende o método de instalação para a sua solução.

* Quando instala uma solução da Microsoft através do portal do OMS, está instalado na área de trabalho OMS atual e não é necessária nenhuma conta de automatização.
* Quando instala uma solução através do Azure Marketplace, lhe for pedido para uma área de trabalho OMS e a conta de automatização e a ligação entre eles é criada para si.  
* Para soluções fora do Azure Marketplace, tem de ligar a área de trabalho OMS e a conta de automatização antes de instalar a solução.  Pode fazê-lo a selecionar qualquer solução no Azure Marketplace e selecionando a área de trabalho OMS e a conta de automatização.  Não tem de instalar, na verdade, a solução porque a ligação será criada assim que a área de trabalho OMS e a conta de automatização estão selecionadas.  Depois da ligação for criada, em seguida, pode utilizar essa área de trabalho OMS e a conta de automatização para qualquer solução. 

### <a name="verifying-the-link-between-an-oms-workspace-and-automation-account"></a>Verificar a ligação entre uma conta de automatização e área de trabalho do OMS
Pode verificar a ligação entre uma área de trabalho do OMS e uma conta de automatização utilizando o procedimento seguinte.

1. Selecione a conta de automatização no portal do Azure.
2. Desloque-se para a parte inferior do **definições** painel.
3. Se houver uma secção denominada **OMS recursos** no **definições** painel, em seguida, esta conta está ligado a uma área de trabalho do OMS.
4. Selecione **área de trabalho** ver os detalhes da área de trabalho OMS associado a esta conta de automatização.

## <a name="listing-management-solutions"></a>Listar as soluções de gestão
Utilize o procedimento seguinte para ver as soluções de gestão nas áreas de trabalho ligadas à sua subscrição do Azure.

1. Inicie sessão no portal do Azure.
2. No painel esquerdo, selecione **mais serviços**.
3. O desloque para baixo até **soluções** ou tipo *soluções* para o **filtro** caixa de diálogo.
4. Soluções instaladas em todas as áreas de trabalho serão apresentadas.

Tenha em atenção que pode ver as soluções do Microsoft instaladas na área de trabalho atual através do portal do OMS.

## <a name="removing-a-management-solution"></a>Remover uma solução de gestão
Quando uma solução de gestão for removida, todos os recursos na solução também são removidos.  

1. Localize a solução no portal do Azure utilizando o procedimento no [listagem soluções](#listing-solutions).
2. Selecione a solução que pretende remover.
3. Clique em de **eliminar** botão.

## <a name="creating-a-management-solution"></a>Criar uma solução de gestão
Orientação completa sobre a criação de soluções de gestão estão disponíveis em [criar soluções no Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md). 

## <a name="next-steps"></a>Passos seguintes
* Pesquisa [modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates) exemplos de modelos de Gestor de recursos diferentes.
* Crie o seu próprio [soluções de gestão](operations-management-suite-solutions-creating.md).

