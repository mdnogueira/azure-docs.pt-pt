---
title: "Azure Active Directory licenciamento cenários adicionais com base no grupo | Microsoft Docs"
description: "Mais cenários para o Azure Active Directory baseadas em grupos de licenciamento"
services: active-directory
keywords: Licenciamento do Azure AD
documentationcenter: 
author: curtand
manager: femila
editor: piotrci
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/02/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 75cafa6868d54f9d8a7e0dbe9f2a9e85ed43f16f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Cenários, limitações e problemas conhecidos, utilizar grupos para gerir o licenciamento no Azure Active Directory

Utilize as seguintes informações e exemplos para obter uma compreensão mais avançada do Azure Active Directory (Azure AD) com base no grupo de licenciamento.

## <a name="usage-location"></a>Localização de utilização

Alguns serviços da Microsoft não estão disponíveis em todas as localizações. Antes de uma licença pode ser atribuída a um utilizador, o administrador tem de especificar o **localização de utilização** propriedade no utilizador. No [portal do Azure](https://portal.azure.com), pode especificar no **utilizador** &gt; **perfil** &gt; **definições**.

Para atribuição de licença de grupo, os utilizadores sem uma localização de utilização especificada irão herdar a localização do diretório. Se tiver utilizadores em vários locais, certifique-se refletir que corretamente no seu objetos de utilizador antes de adicionar utilizadores para grupos de licenças.

> [!NOTE]
> Atribuição de grupos de licenças nunca irá modificar um valor de localização de utilização existente num utilizador. Recomendamos que sempre definir localização de utilização como parte do seu fluxo de criação de utilizador no Azure AD (por exemplo, através do AAD Connect configuração) - irá garantir o resultado da atribuição de licenças sempre está correto e que os utilizadores não recebem serviços em locais que não são permitidos.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Utilizar o grupo baseado licenciamento com grupos dinâmicos

Pode utilizar o grupo baseado licenciamento com qualquer grupo de segurança, o que significa que pode ser combinado com grupos dinâmicos do Azure AD. Grupos dinâmicos executam regras utilizador atributos de objetos para adicionar e remover utilizadores de grupos automaticamente.

Por exemplo, pode criar um grupo dinâmico para algumas conjunto de produtos que pretende atribuir aos utilizadores. Cada grupo é preenchido por uma regra de adição de utilizadores pelos respetivos atributos e cada grupo é atribuído as licenças que pretende receber. Pode atribuir o atributo no local e sincronizá-lo com o Azure AD, ou pode gerir o atributo diretamente na nuvem.

São atribuídas as licenças ao utilizador pouco tempo depois de serem adicionados ao grupo. Quando o atributo é alterado, o utilizador sai os grupos e as licenças são removidas.

### <a name="example"></a>Exemplo

Considere o exemplo de uma solução de gestão de identidade no local que decide que os utilizadores devem ter acesso a serviços web da Microsoft. Utiliza **extensionAttribute1** para armazenar um valor de cadeia que representa as licenças de utilizador deve ter. O Azure AD Connect sincroniza-se-lo com o Azure AD.

Os utilizadores poderão precisar de uma licença, mas não outra ou poderá ter ambos. Eis um exemplo, no qual está a distribuir Office 365 Enterprise E5 e Enterprise Mobility + licenças de segurança (EMS) aos utilizadores nos grupos:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: serviços de base

![Serviços de base de captura de ecrã do Office 365 Enterprise E5](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: os utilizadores licenciados

![Captura de ecrã do Enterprise Mobility + Security licenciado a utilizadores](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

Para este exemplo, modificar um utilizador e definir as respetivas extensionAttribute1 para o valor de `EMS;E5_baseservices;` se pretender que o utilizador tenha de ambas as licenças. Pode efetuar esta modificação no local. Após a alteração sincroniza-se com a nuvem, o utilizador for adicionado automaticamente a ambos os grupos e são atribuídas as licenças.

![Captura de ecrã que mostra como definir extensionAttribute1 do utilizador](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Tenha cuidado quando modificar a regra de associação de um grupo existente. Quando uma regra é alterada, a associação do grupo será reavaliada e os utilizadores que já não correspondem a nova regra serão removidos (os utilizadores que ainda a nova regra não será afetada durante este processo de correspondência). Os utilizadores terão cujas licenças são removidas durante o processo que poderá resultar na perda de serviço ou em alguns casos, perda de dados.

> Se tiver um grupo dinâmico grande, que dependem de atribuição de licenças, considere a validar alterações significativas num mais pequeno grupo de teste antes de aplicá-las para o grupo principal.

## <a name="multiple-groups-and-multiple-licenses"></a>Vários grupos e várias licenças

Um utilizador pode ser um membro de vários grupos de licenças. Seguem-se alguns aspetos a considerar:

- Várias licenças para o produto mesmo podem sobrepor-se e serem resultam em todos os serviços ativados a ser aplicados ao utilizador. O exemplo seguinte mostra dois grupos de licenciamento: *serviços base E3* contém os serviços de base para implementar em primeiro lugar, todos os utilizadores. E *E3 expandido serviços* contém serviços adicionais (Sway e Planner) para implementar apenas a alguns utilizadores. Neste exemplo, o utilizador foi adicionado a ambos os grupos:

  ![Captura de ecrã dos serviços ativados](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  Como resultado, o utilizador tem 7 12 serviços no produto ativado, ao utilizar apenas uma licença para este produto.

- Selecionar o *E3* licença mostra mais detalhes, incluindo informações sobre que grupos causado que serviços como ativada para o utilizador.

  ![Captura de ecrã dos serviços ativados por grupo](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>Licenças diretas coexistem com licenças de grupo

Quando um utilizador herde uma licença de um grupo, a não é possível remover ou modificar diretamente essa atribuição de licenças nas propriedades do utilizador. As alterações tem de ser efetuadas no grupo e, em seguida, propagadas a todos os utilizadores.

No entanto, é possível atribuir a mesmo licença do produto diretamente para o utilizador, para além da licença herdado. Pode ativar os serviços adicionais do produto apenas para um utilizador, sem afetar outros utilizadores.

Diretamente licenças atribuídas podem ser removidas e não afetam herdado licenças. Considere o utilizador que herda de uma licença do Office 365 Enterprise E3 de um grupo.

1. Inicialmente, o utilizador herde a licença apenas a partir de *serviços básicos E3* grupo, o que lhe permite quatro planos de serviço, conforme mostrado:

  ![Grupo de captura de ecrã de E3 ativado os serviços](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. Pode selecionar **atribuir** para atribuir uma licença E3 diretamente para o utilizador. Neste caso, pretender desativar todos os planos de serviço, exceto Yammer Enterprise:

  ![Captura de ecrã de como atribuir uma licença diretamente a um utilizador](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. Como resultado, o utilizador ainda utiliza apenas uma licença do produto E3. Mas a atribuição direta permite que o serviço de empresa Yammer para apenas esse utilizador. Pode ver quais os serviços estão ativados pela associação a grupos em comparação com a atribuição direta:

  ![Captura de ecrã do herdado por oposição a atribuição direta](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. Quando utilizar a atribuição direta, são permitidas as seguintes operações:

  - Yammer que Enterprise pode ser desativada no objeto user diretamente. O **/desactiva** alternar na ilustração foi ativado para este serviço, por oposição as outros serviço mudanças de modo email. Porque o serviço está ativado diretamente no utilizador, pode ser modificado.
  - Serviços adicionais podem ser ativados bem, como parte da licença de diretamente atribuída.
  - O **remover** botão pode ser utilizado para remover a licença direta do utilizador. Pode ver que o utilizador só tem agora a licença herdado de grupo e apenas os serviços de originais permanecerem ativados:

    ![Captura de ecrã que mostra como remover a atribuição direta](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="managing-new-services-added-to-products"></a>Gestão de novos serviços adicionado produtos
Quando a Microsoft adiciona um novo serviço de um produto, será ativada por predefinição em todos os grupos aos quais foi atribuído a licença do produto. Os utilizadores no seu inquilino que tiver subscrito notificações sobre as alterações do produto irão receber e-mails antecedência que seja notificado sobre as adições futura do serviço.

Como administrador, pode rever todos os grupos afetados pela alteração e tomar medidas, tal como desativar o novo serviço em cada grupo. Por exemplo, se tiver criado grupos de destinos apenas serviços específicos para a implementação, pode revê desses grupos e certifique-se de que quaisquer recentemente adicionadas serviços se encontram desativados.

Eis um exemplo de que este processo pode ter o seguinte aspeto:

1. Inicialmente, atribuído a *Office 365 Enterprise E5* produto para vários grupos. Um desses grupos, chamados *O365 E5 - Exchange apenas* foi concebido para permitir apenas o *Exchange Online (planear 2)* serviço para os seus membros.

2. Recebeu uma notificação da Microsoft que o produto E5 irá ser expandido com um novo serviço - *Microsoft Stream*. Quando o serviço torna-se disponível no seu inquilino, pode efetuar o seguinte:

3. Vá para o [ **do Azure Active Directory > licenças > todos os produtos** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) painel e selecione *Office 365 Enterprise E5*, em seguida, selecione **licenciado grupos** para ver uma lista de todos os grupos com esse produto.

4. Clique no grupo que pretende rever (neste caso, *O365 E5 - Exchange apenas*). Esta ação irá abrir o **licenças** separador. Quando clica a licença E5, abre um painel listar todos os serviços ativados.
> [!NOTE]
> O *Microsoft Stream* serviço foi automaticamente adicionado e ativado neste grupo, para além de *Exchange Online* serviço:

  ![Captura de ecrã do novo serviço adicionado a uma licença de grupo](media/active-directory-licensing-group-advanced/manage-new-services.png)

5. Se pretende desativar o novo serviço neste grupo, clique em de **/desactiva** Ativar/desativar junto do serviço e clique em de **guardar** botão para confirmar a alteração. Azure AD irá agora processar todos os utilizadores no grupo para aplicar a alteração; os novos utilizadores adicionados ao grupo não terão o *Microsoft Stream* serviço ativado.

  > [!NOTE]
  > Os utilizadores ainda podem ter o serviço ativado através de alguns outra atribuição de licença (outro grupo são os membros da ou de uma atribuição de licença direto).

6. Se for necessário, execute os mesmos passos para outros grupos com este produto atribuído.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Utilizar o PowerShell para ver quem tem herdadas e licenças diretas
Pode utilizar um script do PowerShell para verificar se os utilizadores possuem uma licença atribuída diretamente ou herdada a partir de um grupo.

1. Execute o `connect-msolservice` cmdlet para autenticar e ligar ao seu inquilino.

2. `Get-MsolAccountSku`pode ser utilizado para detetar todas as licenças de produto aprovisionado no inquilino.

  ![Captura de ecrã do cmdlet Get-Msolaccountsku](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Utilize o *AccountSkuId* valor para a licença que está interessado em com [este script do PowerShell](./active-directory-licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group). Isto produzirá uma lista de utilizadores com esta licença com as informações sobre a forma como a licença é atribuída.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Utilize registos de auditoria para monitorizar a atividade de licenciamento baseadas em grupos

Pode utilizar [registos de auditoria do Azure AD](./active-directory-reporting-activity-audit-logs.md#audit-logs) ver todas as atividades relacionadas com base no grupo de licenciamento, incluindo:
- que grupos de licenças alterado
- Quando o sistema iniciou o processamento de uma alteração de licença de grupo e, quando concluído
- foram efetuadas as alterações de licença a um utilizador como resultado de uma atribuição do grupo de licenças.

>[!NOTE]
> Os registos de auditoria estão disponíveis na maioria dos painéis na secção do portal do Azure Active Directory. Dependendo de onde pode aceder aos mesmos, filtros podem ser previamente aplicados para mostrar apenas atividade relevantes para o contexto do painel. Se não está a ver os resultados que esperava, analise [as opções de filtragem](./active-directory-reporting-activity-audit-logs.md#filtering-audit-logs) ou aceder os registos de auditoria e não filtradas em [ **do Azure Active Directory > atividade > registos de auditoria** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>Saber quem modificou uma licença de grupo

1. Definir o **atividade** filtrar para *conjunto grupo licença* e clique em **aplicar**.
2. Os resultados incluem todos os casos de licenças que está a ser definido ou modificação dos grupos.
>[!TIP]
> Também pode escrever o nome do grupo no *destino* filtro para definir o âmbito os resultados.

3. Clique num item na vista de lista para ver os detalhes da que foi alterada. Em *propriedades modificadas* antigos e novos valores para a atribuição de licenças estão listados.

Eis um exemplo de alterações recentes de licença de grupo, com detalhes:

![Alterações de licença do grupo de captura de ecrã](media/active-directory-licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Determinar quando a alterações ao grupo foi iniciado e concluiu o processamento

Quando altera uma licença num grupo, do Azure AD será iniciada a aplicar as alterações a todos os utilizadores.

1. Para ver quando grupos iniciou o processamento, defina o **atividade** filtrar para *iniciar aplicar licença de grupo com base nos utilizadores*. Tenha em atenção que o ator para a operação *Microsoft Azure AD com base no grupo de licenciamento* -uma conta de sistema que é utilizada para executar todas as alterações de licença de grupo.
>[!TIP]
> Clique num item na lista para ver o *propriedades modificadas* campo - mostra as alterações de licença que foram captadas para processamento. Isto é útil se efetuadas várias alterações a um grupo e tem a não certeza foi processada ao qual deles.

2. Da mesma forma, para ver em que grupos terminou o processamento, utilize o valor do filtro *concluir aplicar licença de grupo com base nos utilizadores*.
>[!TIP]
> Neste caso, o *propriedades modificadas* campo contém um resumo dos resultados – isto é útil para rapidamente verificar se o processamento resultou em quaisquer erros. Resultado do exemplo:
> ```
Modified Properties
...
Name : Result
Old Value : []
New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
> ```

3. Para ver o registo de completado para como um grupo foi processado, incluindo todas as alterações do utilizador, defina os seguintes filtros:
  - **Iniciado pelo (Ator)**: "Microsoft Azure AD com base no grupo de licenciamento"
  - **Intervalo de datas** (opcional): intervalo personalizado para quando sabe que um grupo específico iniciado e concluiu o processamento

Este resultado de exemplo mostra o início do processamento, resultante todas as alterações de utilizador e a conclusão do processamento.

![Alterações de licença do grupo de captura de ecrã](media/active-directory-licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Ao clicar em itens relacionados com *licença de utilizador de alteração* irá mostrar os detalhes de alterações de licença aplicados a cada utilizador individual.

## <a name="deleting-a-group-with-an-assigned-license"></a>Eliminar um grupo com atribuída uma licença

Não é possível eliminar um grupo com uma licença do Active Directory atribuída. Um administrador pode eliminar um grupo não compreender o que fará com que as licenças a serem removidos da utilizadores - por este motivo, que é necessário qualquer licenças a remoção do grupo em primeiro lugar, antes de ser eliminado.

Ao tentar eliminar um grupo no portal do Azure pode ver uma notificação de erro semelhante isto: ![falha na eliminação do grupo de captura de ecrã](media/active-directory-licensing-group-advanced/groupdeletionfailed.png)

Vá para o **licenças** separador no grupo e ver se existem quaisquer licenças atribuídas. Se Sim, remover as licenças e tente novamente a eliminar o grupo.

Poderá ver erros semelhantes ao tentar eliminar o grupo através do PowerShell ou a Graph API. Se estiver a utilizar um grupo sincronizado no local, o Azure AD Connect também pode comunicar os erros se está a falhar ao eliminar o grupo no Azure AD. Nestes casos, certifique-se verificar se existem quaisquer licenças atribuídas ao grupo e removê-las primeiro.

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

Se utilizar o licenciamento de baseadas em grupos, é boa ideia para se familiarizar com a seguinte lista de limitações e problemas conhecidos.

- Com base no grupo de licenciamento atualmente não suporta grupos que contêm outros grupos (grupos aninhados). Se aplicar uma licença a um grupo aninhado, apenas os membros de imediata de primeiro nível de utilizador do grupo de tem as licenças aplicadas.

- A funcionalidade só pode ser utilizada com grupos de segurança. Grupos do Office não são atualmente suportados e não será possível utilizá-los no processo de atribuição de licença.

- O [portal de administração do Office 365](https://portal.office.com ) não suporta atualmente baseadas em grupos de licenciamento. Se um utilizador herde uma licença de um grupo, esta licença é apresentado no portal de administração do Office como uma licença de utilizador normais. Se tentar modificar esse licença ou tente remover a licença, o portal devolve uma mensagem de erro. Herdado de grupo de licenças não não possível modificar diretamente num utilizador.

- Quando um utilizador é removido de um grupo e perde a licença, os planos de serviço do que licença (por exemplo, SharePoint Online) estão definidos para um **suspenso** estado. Os planos de serviço não estão definidos para um estado final, desativado. Esta precaução pode evitar remoção acidental de dados de utilizador, se um administrador faz com que um erro na gestão de associação de grupo.

- Quando as licenças atribuídas ou modificadas para um grupo de grandes dimensões (por exemplo, 100 000 utilizadores), pode afetar o desempenho. Especificamente, o volume de alterações gerado pela automatização do Azure AD poderá afetar negativamente o desempenho da sua sincronização de diretórios entre o Azure AD e sistemas no local.

- Em determinadas situações de carga elevada, o processamento de licença pode sofrer um atraso e as alterações, tais como adicionar/remover um grupo de licenças ou adicionar/remover utilizadores do grupo, podem demorar muito tempo a ser processado. Se vir as suas alterações demorar mais de 24 horas para processar,. [abrir um pedido de suporte](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) ao permitir-nos investigar. Iremos irá melhorar as características de desempenho desta funcionalidade antes de atingir *disponibilidade geral*.

- Automatização da gestão de licenças não reagir automaticamente para todos os tipos de alterações no ambiente. Por exemplo, poderá ter ficado sem licenças, fazendo com que algumas que os utilizadores num Estado de erro. Para libertar o número de licenças disponíveis, pode remover alguns licenças diretamente atribuídas a outros utilizadores. No entanto, o sistema não reagir a esta alteração e resolver automaticamente os utilizadores com esse Estado de erro.

  Como uma solução para estes tipos de limitações, pode ir para o **grupo** painel no Azure AD e clique em **Reprocessar**. Este comando processa todos os utilizadores nesse grupo e resolve os Estados de erro, se possível.

- Com base no grupo de licenciamento grava erros quando uma licença não foi possível atribuir a um utilizador devido a uma configuração de endereço proxy duplicado no Exchange Online; esses utilizadores são ignorados durante a atribuição de licença. Para obter mais informações sobre como identificar e resolver este problema, consulte [nesta secção](./active-directory-licensing-group-problem-resolution-azure-portal.md#license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre outros cenários para gestão de licenças através de com base no grupo de licenciamento, consulte:

* [O que é o licenciamento no Azure Active Directory com base no grupo?](active-directory-licensing-whatis-azure-portal.md)
* [Atribuir licenças a um grupo no Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identificar e resolver problemas de licenciamento para um grupo no Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Como migrar os utilizadores licenciados individuais para baseadas em grupos de licenciamento no Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
