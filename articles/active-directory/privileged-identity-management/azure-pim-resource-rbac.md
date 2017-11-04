---
title: "Descrição geral de recursos do Azure PIM RBAC | Microsoft Docs"
description: "Obter uma descrição geral da funcionalidade RBAC no PIM incluindo terminologia e notificações"
services: active-directory
documentationcenter: 
author: barclayn
manager: mbaldwin
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: barclayn
ms.openlocfilehash: 114ef434e6167ef2b25b040e35ab3ce1b85151a9
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="pim-for-azure-resources-preview"></a>PIM para recursos do Azure (pré-visualização)

Do Azure Active Directory Privileged Identity gestão (PIM), já pode gerir, controlar e monitorizar o acesso a recursos do Azure (pré-visualização) dentro da sua organização. Isto inclui as subscrições, grupos de recursos e até mesmo máquinas virtuais. Qualquer recurso no portal do Azure, que aproveita a funcionalidade de controlo de acesso com base do Azure funções (RBAC) pode tirar partido de todas as segurança grande e capacidades de gestão do ciclo de vida do Azure AD PIM tem para oferecer e algumas novas funcionalidades excelentes planeamos colocar Funções do Azure AD em breve. 

## <a name="pim-for-azure-resources-preview-helps-resource-administrators"></a>PIM para recursos do Azure (pré-visualização) ajuda os administradores de recursos

- Ver quais os utilizadores e grupos de atribuição de funções para os recursos do Azure que administrar
- Ativar a pedido, "just in time" acesso para gerir recursos, tais como as subscrições, grupos de recursos e muito mais
- Os utilizadores/grupos atribuídos acesso a recursos automaticamente com novas definições de atribuição de vínculo de tempo de expiração
- Atribuir acesso de recursos temporário para tarefas rápidas ou de agendas na chamada
- Impor o multi-factor Authentication para acesso a recursos em qualquer função incorporada ou personalizada 
- Obter relatórios sobre a atividade de recursos de acesso correlacionado de recursos durante uma sessão de utilizador ativa
- Obtenha alertas quando os novos utilizadores ou grupos atribuídos acesso a recursos e quando que ativam atribuições elegíveis

Pode gerir o Azure AD PIM funções incorporadas de recursos do Azure, bem como as funções (RBAC) personalizadas, incluindo (mas não se limitando):

- Proprietário
- Administrador de Acesso de Utilizador
- Contribuinte
- Administrador de segurança
- Gestor de segurança e muito mais

>[!NOTE]
Os utilizadores ou membros de um grupo atribuídas às funções de proprietário ou administrador de acesso de utilizador e os administradores globais que ativar a gestão de subscrição no Azure AD são administradores de recursos. Estes administradores podem atribuir funções, configure definições de função e rever o acesso a utilizar o PIM para recursos do Azure. Ver a lista de [funções incorporadas para recursos do Azure](../role-based-access-built-in-roles.md).

## <a name="tasks"></a>Tarefas

PIM fornece acesso prático para ativar funções, ver ativações/pedidos pendentes aprovações pendentes (para [funções de diretório do Azure AD](azure-ad-pim-approval-workflow.md)) e revê pendentes a resposta da secção de tarefas do menu de navegação esquerdo.

Quando aceder qualquer um dos itens de menu tarefas a partir do ponto de entrada de descrição geral, a vista resultante contém resultados para as funções de diretório do Azure AD e funções de recursos do Azure (pré-visualização). 

![](media/azure-pim-resource-rbac/role-settings-details.png)

As minhas funções contém uma lista das suas atribuições de funções de Active Directory e elegível para funções de diretório do Azure AD e funções de recursos do Azure (pré-visualização).

## <a name="activate-roles"></a>Ativar funções

Ativar funções para recursos do Azure (pré-visualização) apresenta uma nova experiência que permite que os membros da função elegível agendar ativação para uma data/hora no futuro e selecione uma duração de ativação específico no máximo (configurado por administradores). Saiba mais sobre [ativar aqui a funções do Azure AD](../active-directory-privileged-identity-management-how-to-activate-role.md).

![](media/azure-pim-resource-rbac/contributor.png)

No menu de ativações, introduza a data de início pretendida e a hora para ativar a função. Opcionalmente, reduzir a duração da ativação (o período de tempo a função está ativa) e a introdução de uma justificação se necessário. Clique em ativar.

A data de início e a hora é se não forem modificados, a função será ativada em segundos. Verá que uma função em fila para mensagem de faixa de ativação na página My funções. Clique no botão de atualização para limpar esta mensagem.

![](media/azure-pim-resource-rbac/my-roles.png)

Se a ativação estiver agendada para uma data futura hora, o pedido pendente aparecerá no separador de pedidos pendentes do menu de navegação esquerdo. Caso a ativação de função já não é necessária, o utilizador poderá cancelar o pedido ao clicar no botão Cancelar no lado direito da página.

![](media/azure-pim-resource-rbac/pending-requests.png)

## <a name="discover-and-manage-azure-resources"></a>Detetar e gerir recursos do Azure

Para localizar e gerir funções para um recurso do Azure, selecione os recursos do Azure (pré-visualização) no separador de gerir, no menu de navegação esquerdo. Utilize os filtros ou uma barra de pesquisa na parte superior da página para localizar um recurso.

![](media/azure-pim-resource-rbac/azure-resources.png)

## <a name="resource-dashboards"></a>Dashboards de recursos

O dashboard de vista de administrador tem quatro componentes principais. Uma representação gráfica de ativações de função de recursos nos últimos sete dias. Estes dados tem um âmbito para o recurso selecionado e mostra ativações para as funções mais comuns (proprietário, contribuinte, administrador de acesso de utilizador) e todas as funções combinadas.

À direita do gráfico ativações, são dois gráficos que apresentam a distribuição das atribuições de funções por tipo de atribuição de utilizadores e grupos. Selecionar um setor do gráfico altera o valor para uma percentagem (ou vice-versa).

![](media/azure-pim-resource-rbac/admin-view.png)

Abaixo os gráficos, consulte o número de utilizadores e grupos com atribuições de funções de novo ao longo dos últimos 30 dias (esquerdos) e uma lista de funções ordenados por atribuições totais (descendente).

![](media/azure-pim-resource-rbac/role-settings.png)

## <a name="manage-role-assignments"></a>Gerir atribuições de funções

Os administradores podem gerir atribuições de função, selecionando funções ou membros do painel de navegação esquerdo. Selecionar funções permite aos administradores para definir o âmbito das respetivas tarefas de gestão para uma função específica, enquanto membros apresenta todas as atribuições de função de utilizador e grupo para o recurso.

![](media/azure-pim-resource-rbac/roles.png)

![](media/azure-pim-resource-rbac/members.png)

>[!NOTE]
Se tiver uma função pendentes de ativação, uma faixa de notificação é apresentada na parte superior da página, ao visualizar associação.

## <a name="assign-roles"></a>Atribuir funções

Para atribuir um utilizador ou grupo a uma função, selecione a função (se visualizar funções) ou clique em Adicionar na barra de ação (se na vista de membros).

![](media/azure-pim-resource-rbac/members2.png)

>[!NOTE]
Se adicionar um utilizador ou grupo a partir do separador Membros, terá de selecionar uma função a partir do menu Adicionar para poder selecionar um utilizador ou grupo.

![](media/azure-pim-resource-rbac/select-role.png)

Escolha um utilizador ou grupo a partir do diretório.

![](media/azure-pim-resource-rbac/choose.png)

Escolha o tipo de atribuição adequada no menu de lista pendente. 

**Apenas na atribuição de tempo:** fornece os membros do grupo ou utilizador com acesso elegível, mas não persistente para a função para um determinado período de tempo ou indefinidamente (se configurada nas definições de função). 

**A atribuição direta:** que não é necessário que os membros de utilizador ou grupo ativar a atribuição de função (conhecida como acesso persistente). A Microsoft recomenda utilizando a atribuição direta para utilização de curto prazo, como no chamada desvia ou atividades confidenciais do tempo, em que não irá ser necessário acesso quando a tarefa estiver concluída.

![](media/azure-pim-resource-rbac/membership-settings.png)

Uma caixa de verificação abaixo da lista pendente Tipo de atribuição permite-lhe especificar se a atribuição de deve ser permanente (permanentemente elegível para ativar imediatamente no tempo atribuição/permanentemente Active Directory para a atribuição direta). Para especificar uma duração de atribuição específico, desmarque a caixa de verificação e modificar o início e/ou campos de data e hora de fim.

>[!NOTE]
A caixa de verificação pode ser unmodifiable se outro administrador tiver especificado a duração máxima de atribuição para cada tipo de atribuição nas definições de função.

![](media/azure-pim-resource-rbac/calendar.png)

## <a name="view-activation-and-azure-resource-activity"></a>Ver a ativação e a atividade de recursos do Azure

No caso de necessitar de ver as ações que um utilizador específico demorou em vários recursos, pode rever a atividade de recursos do Azure associada um período de activação fornecido (para os utilizadores elegíveis). Comece por selecionar um utilizador da vista de membros ou da lista de membros de uma função específica. O resultado mostra uma visualização gráfica de ações do utilizador nos recursos do Azure por data e as ativações de função recente esse mesmo período de tempo.

![](media/azure-pim-resource-rbac/user-details.png)

A ativação de uma função específica a seleção irá mostrar os detalhes de ativação de função e atividade correspondente de recursos do Azure que ocorreram durante esse utilizador Active Directory.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="modify-existing-assignments"></a>Altere as atribuições existentes

Para modificar atribuições existentes da vista de detalhes de utilizador/grupo, selecione a alterar as definições na barra de ação na parte superior da página. Altere o tipo de atribuição para apenas na hora de atribuição ou atribuição direta.

![](media/azure-pim-resource-rbac/change-settings.png)

## <a name="review-who-has-access-in-a-subscription"></a>Reveja quem tem acesso de uma subscrição

Para rever as atribuições de funções na sua subscrição, selecione o separador de membros do painel de navegação esquerdo, ou selecione funções e escolha uma função específica para rever os membros. 

Selecione a revisão da barra de ação para ver as revisões de acesso existentes e selecione Adicionar para criar uma nova revisão.

![](media/azure-pim-resource-rbac/owner.png)

[Saiba mais sobre revisões de acesso](../active-directory-privileged-identity-management-how-to-perform-security-review.md)

>[!NOTE]
Revisões só são suportadas para tipos de recursos de subscrição neste momento.

## <a name="configure-role-settings"></a>Configurar definições de função

Configurar definições de função definem as predefinições aplicadas a atribuições no ambiente do PIM. Para definir estas para o seu recurso, selecione o separador de definições da função a partir do painel de navegação esquerdo ou no botão de definições de função na barra de ação em qualquer função para ver as opções atuais.

Ao clicar em edição na barra de ação na parte superior da página permite-lhe modificar a cada definição.

![](media/azure-pim-resource-rbac/owner.png)

![](media/azure-pim-resource-rbac/owner02.png)

Alterações às definições são registadas na página de definições de função, incluindo a última data atualizado e o administrador alterar as definições.

![](media/azure-pim-resource-rbac/role-settings-02.png)

## <a name="resource-audit"></a>Auditoria de recursos

Auditoria de recurso dá-lhe uma vista de todas as atividades de função para o recurso. Pode filtrar as informações utilizando uma data predefinida ou um intervalo personalizado.
![](media/azure-pim-resource-rbac/last-day.png)Auditoria de recursos também fornece acesso rápido para ver detalhes de atividade do utilizador. Na vista de todas as ações "Ativar a função" estão ligações para atividade de recurso específico do requerente.
![](media/azure-pim-resource-rbac/resource-audit.png)

## <a name="just-enough-administration"></a>Administração just enough

A utilização de apenas suficiente melhores práticas de administração (JEA) com a suas atribuições de função de recursos é simple com PIM para recursos do Azure. Os utilizadores e os membros do grupo com atribuições de subscrições do Azure ou os grupos de recursos, podem ativar a atribuição de função existente num âmbito reduzida. 

A página de pesquisa, localize o recurso subordinado que tem de gerir.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Selecione as minhas funções no menu de navegação esquerdo e escolha a função adequada para o ativar. Tenha em atenção o tipo de atribuição é herdada, uma vez que a função foi atribuída à subscrição, em vez do grupo de recursos, conforme mostrado abaixo.

![](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>Passos seguintes

- [Funções incorporadas para recursos do Azure](../role-based-access-built-in-roles.md)
- Saiba mais sobre [ativar aqui a funções do Azure AD](../active-directory-privileged-identity-management-how-to-activate-role.md)
- [Fluxos de trabalho de aprovação de PIM](azure-ad-pim-approval-workflow.md)
