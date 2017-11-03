---
title: "Organizar os recursos com grupos de gestão do Azure - Azure | Microsoft Docs"
description: "Saiba mais sobre os grupos de gestão e como utilizá-los."
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.openlocfilehash: 18541c68b02ae1b59ae4a6a85122dff614c9978c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organizar os recursos com grupos de gestão do Azure 

Se tiver várias subscrições, pode organizá-los em contentores chamados "grupos de gestão" para o ajudar a gerir o acesso, política, os custos e a conformidade nas suas subscrições. Por exemplo, pode de aplicar políticas para um grupo de gestão esse limite que tipos de recursos podem ser criados.

> [!Note]
> Esta funcionalidade está atualmente a ser uma pré-visualização privada. [Inscrever-se aqui](https://aka.ms/MGPreviewSignup) ter a associar a pré-visualização a inscrição.   
 


Grupos de gestão podem ser organizados para uma hierarquia. A estrutura mostrada é uma representação de exemplo de uma hierarquia de grupo de gestão que pode existir:


![Árvore de hierarquia](media/billing-enterprise-mgmt-groups/tree.png)



## <a name="how-management-groups-are-related-to-your-azure-enterprise-enrollment"></a>Como grupos de gestão relacionados com a inscrição Enterprise do Azure

A introdução de grupos de gestão é um passo de journey maior de transição [portal da empresa](https://ea.azure.com) funcionalidades para o [portal do Azure](https://portal.azure.com).

A estrutura de grupo de gestão é criada ou foi definido no portal da empresa. Toda a hierarquia consiste em contas, departamentos e inscrição é mapeada para grupos de gestão correspondente. 

Eis como a estrutura EA atual mapeia à hierarquia de grupo de gestão. 

![Árvore de hierarquia](media/billing-enterprise-mgmt-groups/tree2.png)

A tabela abaixo mostra como os utilizadores a partir do portal da empresa são mapeados para a hierarquia de grupo de gestão.

|    Função EA                                       |    Função no nó do grupo de gestão mapeada    |    Permissões no nó do grupo de gestão                                                          |
|--------------------------------------------------|--------------------------------------------------|----------------------------------------------------------------------------------------------------|
|    EA administrador                              |    Contribuinte de política de recurso                   |    Pode ver os custos, gerir recursos de política e vista de hierarquia em e abaixo do nó de inscrição    |
|    Administrador de EA no modo só de leitura            |    Leitor de faturação                                |    Pode ler os custos e ver a hierarquia em e abaixo do nó de inscrição                              |
|    Administrador do departamento                      |    Leitor de faturação                                |    Pode ler os custos e ver a hierarquia e abaixo do nó de departamento                                 |
|    Administrador do departamento no modo só de leitura    |    Leitor de faturação                                |    Pode ler os custos e ver a hierarquia e abaixo do nó de departamento                                 |
|    Proprietário da conta                                 |    Contribuinte de política de recurso                   |    Pode ver os custos, gerir recursos de política e vista de hierarquia em e abaixo do nó de conta       |




## <a name="view-management-groups-in-the-azure-portal"></a>Vista de grupos de gestão no portal do Azure

Para ver uma inscrição, departamento ou uma conta na pré-visualização, inicie sessão no portal do Azure com a ligação no e-mail de boas-vindas.   

![hierarquia](media/billing-enterprise-mgmt-groups/hierarchy.png)

### <a name="viewing-costs"></a>Visualizar os custos 
Em ecrãs de detalhe de grupos de gestão, consulte os custos de mês a data atuais. Estes custos baseiam-se em utilização e não conta quantidades pré-pago, excedentes, quantidades incluídas, ajustes e taxas. Para o grupo de gestão correspondente para a inscrição, a secção de custos mostra-lhe o compromisso restante.  

![detalhes de inscrição](media/billing-enterprise-mgmt-groups/enrollment.png)

 "Custos cobrados em separado" são a acumulação mensal de alterações separadas como marketplace, excedentes e outros custos que não podem passar contra o compromisso da inscrição.  Para mais informações sobre a divisão de custo, consulte o [portal da empresa](https://ea.azure.com). 

### <a name="enabling-access-to-costs"></a>Ativar o acesso aos custos
Se não existem custos, consulte a nossa [resolver problemas empresariais custo vistas](https://aka.ms/enableazurecosts) documento para obter ajuda.  

### <a name="delays-between-the-enterprise-portal-and-azure-portal"></a>Atrasos entre o portal da empresa e o portal do Azure 
* Durante a pré-visualização, quantidades apresentadas no portal do Azure podem sofrer um atraso compared para valores no portal da empresa. Este problema é temporário e está a ser trabalhado.
* As definições atualizadas no portal da empresa têm um atraso de vários minutos antes das atualizações são refletidas no portal do Azure. 

## <a name="management-groups-have-a-relationship-with-your-directory"></a>Grupos de gestão tem uma relação com o seu diretório   
Como as subscrições, grupos de gestão também têm uma relação de fidedignidade com o Azure AD. Uma hierarquia de grupo de gestão confianças único diretório para autenticar os utilizadores. Todos os administradores associados a uma hierarquia de grupo de gestão têm de pertencer ao mesmo diretório. 

Como a hierarquia de inscrição está mapeada para grupos de gestão, uma relação de confiança é estabelecida com um único diretório. Sempre que possível, está selecionado um diretório existente associado a contas de utilizador a inscrição. Em alguns casos, é criado um novo diretório e todos os utilizadores existentes de inscrição são convidados no diretório. Diretórios associados às subscrições de inscrição não são afetados. Por conseguinte, a hierarquia poderão ser criada num diretório diferente das subscrições. [Saiba mais](billing-enterprise-mgmt-grp-find.md) sobre a forma como este processo tem impacto sobre a experiência de navegar entre a hierarquia e respetivas subscrições.

## <a name="administering-your-management-groups"></a>Administrar os grupos de gestão
Grupos de gestão no portal do Azure estão em pré-visualização e são só de leitura a esta versão inicial. Para efetuar quaisquer atualizações, aceda ao portal da empresa. As atualizações são refletidas automaticamente no portal do Azure. Consulte a documentação no portal da empresa para obter ajuda sobre tornar as edições e adições.   

## <a name="policy-management"></a>Gestão de políticas
O Resource Manager permite criar políticas personalizadas para gerir os seus recursos. Com os grupos de gestão, as políticas podem ser atribuídos a qualquer nível na hierarquia e os recursos herdam as políticas.  [Saiba mais](https://go.microsoft.com/fwlink/?linkid=858942)

> [!Note]
> Não é imposta em diretórios. 


