---
title: Resolver problemas de licenciamento para um grupo no Azure Active Directory | Microsoft Docs
description: "Como identificar e resolver problemas de atribuição de licenças quando estiver a utilizar o Azure Active Directory baseadas em grupos de licenciamento"
services: active-directory
keywords: Licenciamento do Azure AD
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa16cf14def7c6b4555d6624b25e267ef01d5adb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identificar e resolver problemas de atribuição de licença para um grupo no Azure Active Directory

Com base no grupo de licenciamento no Azure Active Directory (Azure AD) apresenta o conceito de utilizadores num Estado de erro licenciamento. Neste artigo, vamos explicar os motivos pelos quais motivo pelo qual os utilizadores poderão ficar neste estado.

Quando atribui licenças diretamente a utilizadores individuais, sem utilizar baseadas em grupos de licenciamento, a operação de atribuição poderá falhar. Por exemplo, ao executar o cmdlet do PowerShell `Set-MsolUserLicense` num sistema de utilizador, o cmdlet pode falhar por muitos motivos relacionados com a lógica de negócio. Por exemplo, poderá ser um número insuficiente de licenças ou um conflito entre duas planos de serviço que não pode ser atribuído ao mesmo tempo. O problema imediatamente é comunicado de volta para si.

Quando estiver a utilizar com base no grupo de licenciamento, podem ocorrer os erros do mesmos, mas acontecer em segundo plano enquanto o serviço do Azure AD é atribuir licenças. Por este motivo, os erros não podem ser comunicados a si imediatamente. Em vez disso, serem registadas no objeto user e, em seguida, comunicados através do portal de administração. Nunca se perder-se a intenção original de licença de utilizador, mas é registada em estado de erro para investigação futura e a resolução.

## <a name="how-to-find-license-assignment-errors"></a>Como localizar erros de atribuição de licença
**Para encontrar erros de atribuição de licença**

   1. Para localizar utilizadores num Estado de erro num grupo específico, abra o painel para o grupo. Em **licenças**, é apresentada uma notificação se existirem quaisquer utilizadores num Estado de erro.

   ![Notificação do grupo, erro](media/active-directory-licensing-group-problem-resolution-azure-portal/group-error-notification.png)

   2. Selecione a notificação para abrir uma lista de todos os utilizadores afetados. Pode selecionar cada utilizador individualmente para ver mais detalhes.

   ![Grupo, lista de utilizadores num Estado de erro](media/active-directory-licensing-group-problem-resolution-azure-portal/list-of-users-with-errors.png)

   3. Localizar todos os grupos que contém pelo menos um erro, no **do Azure Active Directory** painel selecione **licenças**e, em seguida, selecione **descrição geral**. É apresentada uma caixa de informações quando grupos necessitam da sua atenção.

   ![Descrição geral, informações sobre os grupos no estado de erro](media/active-directory-licensing-group-problem-resolution-azure-portal/group-errors-widget.png)

   4. Selecione a caixa para ver uma lista de todos os grupos com erros. Pode selecionar cada grupo para obter mais detalhes.

   ![Descrição geral, a lista de grupos de erros](media/active-directory-licensing-group-problem-resolution-azure-portal/list-of-groups-with-errors.png)


As secções seguintes fornecem uma descrição de cada potencial problema e a forma de resolvê-lo.

## <a name="not-enough-licenses"></a>Licenças não suficientes

**Problema:** não sabemos de licenças suficientes disponíveis para um dos produtos que é especificada no grupo. Terá de adquirir mais licenças para o produto ou liberte licenças por utilizar de outros utilizadores ou grupos.

Para ver quantas licenças estão disponíveis, aceda a **do Azure Active Directory** > **licenças** > **todos os produtos**.

Para ver que utilizadores e grupos que estão a consumir licenças, selecione um produto. Em **licenciado a utilizadores**, é apresentada uma lista de todos os utilizadores que tenha sido atribuídas diretamente ou através de um ou mais grupos de licenças. Em **licenciado grupos**, verá todos os grupos que têm essa produtos atribuído.

**PowerShell:** cmdlets do PowerShell comunique este erro como _CountViolation_.

## <a name="conflicting-service-plans"></a>Planos de serviços em conflito

**Problema:** contém um dos produtos que é especificado no grupo de um plano de serviço está em conflito com outro plano de serviço já está atribuído ao utilizador através de um produto diferente. Alguns planos de serviço são configurados de forma a que não é possível atribuir o mesmo utilizador plano de serviço de outra, relacionados.

Considere o exemplo seguinte. Um utilizador tenha uma licença para o Office 365 Enterprise *E1* atribuídos diretamente, com todos os planos ativados. O utilizador foi adicionado a um grupo que tem o Office 365 empresa *E3* produto atribuído. O produto E3 contém planos de serviço que não podem sobrepor-se com os planos que estão incluídos no E1, pelo que a atribuição de grupo de licenças falha com o erro "Em conflito os planos de serviço". Neste exemplo, os planos de serviço em conflito são:

-   SharePoint Online (planear 2) está em conflito com o SharePoint Online (planear 1).
-   Exchange Online (planear 2) está em conflito com o Exchange Online (planear 1).

Para resolver este conflito, terá de desativar a dois dos planos. Pode desativar a licença E1 que lhe esteja diretamente atribuída ao utilizador. Em alternativa, tem de modificar a atribuição de licenças do grupo inteiro e desative os planos na licença de E3. Em alternativa, pode optar por remover a licença E1 do utilizador, se for redundante no contexto da licença de E3.

A decisão sobre como resolver sempre as licenças de produtos em conflito pertence ao administrador. Azure AD automaticamente não resolve conflitos de licença.

**PowerShell:** cmdlets do PowerShell comunique este erro como _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Outros produtos dependem esta licença

**Problema:** contém um dos produtos que é especificado no grupo de um plano de serviço que deve estar ativado para outro plano de serviço, no outro produto, para a função. Este erro ocorre quando tenta do Azure AD remover o plano de serviço subjacente. Por exemplo, isto pode acontecer se remover o utilizador do grupo.

Para resolver este problema, tem de certificar-se de que o plano necessário ainda é atribuído aos utilizadores através de algum outro método ou que os serviços dependentes estão desativados para os utilizadores. Depois de fazer, pode remover corretamente a licença de grupo aos utilizadores.

**PowerShell:** cmdlets do PowerShell comunique este erro como _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>Não é permitida a localização de utilização

**Problema:** alguns serviços da Microsoft não estão disponíveis em todas as localizações devido ao locais leis e regulamentos. Antes de pode atribuir uma licença a um utilizador, tem de especificar o **localização de utilização** propriedade para o utilizador. Pode especificar a localização sob o **utilizador** > **perfil** > **definições** secção no portal do Azure.

Quando tenta do Azure AD atribuir uma licença de grupo para um utilizador cuja localização de utilização não é suportada, falhar e regista um erro no utilizador.

Para resolver este problema, remova os utilizadores a partir de localizações nonsupported do grupo licenciada. Em alternativa, se os valores de localização de utilização atuais não representam a localização do utilizador real, pode modificá-los para que as licenças são atribuídas corretamente a próxima vez (se a nova localização é suportada).

**PowerShell:** cmdlets do PowerShell comunique este erro como _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Quando o Azure AD atribui licenças de grupo, os utilizadores sem uma localização de utilização especificado herdam a localização do diretório. Recomendamos que os administradores definir a utilização correta valores da localização em utilizadores antes de utilizar baseadas em grupos de licenciamento para cumprir a legislação local e regulamentos.

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>O que acontece quando existe mais do que uma licença do produto num grupo?

Pode atribuir mais de uma licença do produto para um grupo. Por exemplo, pode atribuir o Office 365 Enterprise E3 e Enterprise Mobility + Security a um grupo para ativar facilmente serviços incluídos todos os utilizadores.

Tenta do Azure AD atribuir a todas as licenças que são especificadas no grupo de cada utilizador. Se do Azure AD não é possível atribuir um dos produtos devido a problemas de lógica de negócio,-não atribuir licenças do grupo de está. Um exemplo é se não existirem licenças suficientes para todos os ou se existirem conflitos com outros serviços que estão ativados no utilizador.

Pode ver os utilizadores que não são atribuídos e verifique os produtos que são afetados por este problema.

## <a name="how-do-you-manage-licenses-for-products-with-prerequisites"></a>Como gerir licenças de produtos com os pré-requisitos?

Alguns produtos do Microsoft Online poderá proprietário são *suplementos*. Suplementos requerem um plano de serviço de pré-requisitos para ser ativada para um utilizador ou um grupo antes de poderem ser atribuídos uma licença. Com baseado no grupo de licenciamento, o sistema requer o pré-requisito e o suplemento planos de serviço presentes no mesmo grupo. Isto é feito para se certificar de que os utilizadores que são adicionados ao grupo podem receber o produto de trabalho completamente. Vamos, considere o exemplo seguinte:

Análise de área de trabalho da Microsoft é um produto de suplemento. Contém um plano de serviço único com o mesmo nome. Só podemos pode atribuir este plano de serviço para um utilizador ou grupo, quando um dos seguintes pré-requisitos também está atribuído:
- Exchange Online (plano 1) 
- Exchange Online (plano 2)

Se estamos a tentar atribuir este produto no seu próprio a um grupo, o portal devolve um erro. Selecionar a notificação de erro mostra os seguintes detalhes:

![Grupo, pré-requisitos em falta](media/active-directory-licensing-group-problem-resolution-azure-portal/group-prerequisite-required.png)

Se, selecione os detalhes, mostra a seguinte mensagem de erro:

>Falha na operação de licença. Certifique-se de que o grupo tem os serviços necessários antes de adicionar ou remover um serviço dependente. **O serviço de análise de área de trabalho da Microsoft necessita de Exchange Online (planear 2), bem como ativado.**

Para atribuir este licença de suplemento para um grupo, iremos tem de garantir que o grupo contém também o plano de serviço de pré-requisitos. Por exemplo, vamos poderá atualizar um grupo existente que já contém a Office 365 E3 completa do produto e, em seguida, adicione-lhe o produto de suplemento.

Também é possível criar um grupo de autónomo que contém apenas os produtos mínimo necessários para tornar o suplemento do trabalho. Pode ser utilizado para os utilizadores selecionados para o produto de suplemento de licença. Neste exemplo, vamos atribuiu os produtos seguintes para o mesmo grupo:
- Office 365 Enterprise E3 com apenas o plano de serviço Exchange Online (plano 2) ativado
- Análise de área de trabalho da Microsoft

![Grupo, pré-requisito incluído](media/active-directory-licensing-group-problem-resolution-azure-portal/group-addon-with-prerequisite.png)

De agora em todos os utilizadores adicionados a este grupo consumam uma licença do produto E3 e uma licença do produto à área de trabalho de análise. Ao mesmo tempo, os utilizadores podem ser membros de outro grupo que lhes concede a plano E3 completa do produto e consumirem ainda apenas uma licença do produto.

> [!TIP]
> Pode criar vários grupos para cada plano do serviço de pré-requisitos. Por exemplo, se utilizar o Office 365 Enterprise E1 e do Office 365 Enterprise E3 para os seus utilizadores, pode criar dois grupos de licença Microsoft à área de trabalho Analytics: um que utiliza E1 como um pré-requisito e o outro que utiliza o plano E3. Isto permite-lhe distribuir o suplemento aos utilizadores E1 e E3 sem consumir licenças adicionais.

## <a name="license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online"></a>Atribuição de licenças falha automaticamente por um utilizador devido a endereços proxy duplicado no Exchange Online

Se utilizar o Exchange Online, alguns utilizadores no seu inquilino podem estar incorretamente configurados com o mesmo valor de endereço do proxy. Quando baseadas em grupos de licenciamento tenta atribuir uma licença para essa um utilizador, falhar e grava um erro. Falha ao registar o erro nesta instância é uma limitação na versão de pré-visualização desta funcionalidade e, vamos abordá-lo antes de *disponibilidade geral*.

> [!TIP]
> Se reparar em que alguns utilizadores não recebeu uma licença e que não existe nenhum erro registado para esses utilizadores, verifique primeiro se têm um endereço de proxy duplicado.
> Para ver se existe um endereço de proxy duplicado, execute o seguinte cmdlet do PowerShell no Exchange Online:
```
Run Get-Recipient | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
```
> Para obter mais informações sobre este problema, consulte [mensagem de erro "endereço Proxy já está a ser utilizado" no Exchange Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). O artigo também inclui informações no [como ligar ao Exchange Online com o PowerShell remoto](https://technet.microsoft.com/library/jj984289.aspx).

Depois de resolver quaisquer problemas de endereço de proxy para os utilizadores afetados, certifique-se forçar o processamento de licença no grupo de certificar-se de que as licenças agora podem ser aplicadas.

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>Como forçar processamento de licença de um grupo para resolver erros

Consoante os passos, tiver direcionado para resolver os erros, poderá ser necessário acionar manualmente o processamento de um grupo para atualizar o estado do utilizador.

Por exemplo, se Liberte alguns licenças removendo as atribuições de licenças direta de utilizadores, terá de acionar o processamento dos grupos que tenha falhado anteriormente licença completamente todos os membros de utilizador. Para reprocessar um grupo, aceda ao painel de grupo, abra **licenças**e, em seguida, selecione o **Reprocessar** botão na barra de ferramentas.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre outros cenários para gestão de licenças através de grupos, consulte o seguinte:

* [Atribuir licenças a um grupo no Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [O que é o licenciamento no Azure Active Directory com base no grupo?](active-directory-licensing-whatis-azure-portal.md)
* [Como migrar os utilizadores licenciados individuais para baseadas em grupos de licenciamento no Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory baseadas em grupos licenciamento cenários adicionais](active-directory-licensing-group-advanced.md)
