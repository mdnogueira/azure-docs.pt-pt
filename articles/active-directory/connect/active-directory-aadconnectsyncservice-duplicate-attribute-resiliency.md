---
title: "Resiliência de atributo de sincronização e duplicado identidade | Microsoft Docs"
description: "Novo comportamento como lidar com objetos com conflitos UPN ou /proxyaddress durante a sincronização de diretórios através do Azure AD Connect."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.openlocfilehash: dc764cd4d6bf5cb199080fd77a450c74bd1d49e4
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Sincronização de identidades e resiliência de atributos duplicados
Resiliência de atributo duplicados é uma funcionalidade do Azure Active Directory irá eliminar friction causado por **UserPrincipalName** e **/proxyaddress** está em conflito ao executar uma das ferramentas de sincronização da Microsoft.

Estes dois atributos geralmente têm de ser exclusivo em todas **utilizador**, **grupo**, ou **contacte** objetos de um determinado inquilino do Azure Active Directory.

> [!NOTE]
> Apenas os utilizadores podem ter UPNs.
> 
> 

O novo comportamento que esta funcionalidade permite que está a ser a parte da nuvem do pipeline de sincronização de, pelo que é cliente agnóstico relativamente e relevante para qualquer produto de sincronização da Microsoft, incluindo o Azure AD Connect, o DirSync e o MIM + o conector. O termo genérico "sync client" é utilizado para representar qualquer um destes produtos neste documento.

## <a name="current-behavior"></a>Comportamento atual
Se houver uma tentativa para aprovisionar um novo objeto com um valor UPN ou /proxyaddress que viola a restrição de exclusividade, o Azure Active Directory bloqueia esse objeto de que está a ser criado. Da mesma forma, se um objeto é atualizado com um UPN ou /proxyaddress exclusivos, a atualização falhará. A tentativa de aprovisionamento ou a atualização é repetida pelo cliente de sincronização após cada ciclo de exportação e continua a falhar até que o conflito é resolvido. Uma mensagem de e-mail do relatório de erro é gerada após cada tentativa e erro é registado pelo cliente de sincronização.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Comportamento com resiliência de atributo duplicado
Em vez de completamente a conseguir aprovisionar ou atualizar um objeto com um atributo duplicado, Azure Active Directory "coloca em quarentena" o atributo duplicado que seria viola a restrição de exclusividade. Se este atributo é necessário para o aprovisionamento, como o UserPrincipalName, o serviço atribui um valor do marcador de posição. O formato destes valores temporário é  
"***<OriginalPrefix>+ < 4DigitNumber > @<InitialTenantDomain>. onmicrosoft.com***".  
Se o atributo não for necessário, como um **/proxyaddress**, Azure Active Directory, basta coloca em quarentena o atributo de conflito e continua com a criação do objeto ou a atualização.

Após a quarentena o atributo, informações sobre o conflito são enviadas o mesmo erro relatório por correio eletrónico utilizados no comportamento antigo. No entanto, estas informações só é apresentada no relatório de erro uma vez, quando a quarentena acontece, não continue a que será registado nos e-mails futuros. Além disso, uma vez que a exportação para este objeto foi bem sucedida, o cliente de sincronização não regista um erro e não repete a criar / atualizar a operação após ciclos de sincronização.

Para suportar este comportamento foi adicionado um novo atributo para as classes de objeto de contacto, grupo e utilizador:  
**DirSyncProvisioningErrors**

Este é um atributo com múltiplos valor que é utilizado para armazenar os atributos em conflito que seriam viola a restrição de exclusividade devem possam ser adicionados normalmente. Uma tarefa de temporizador em segundo plano foi ativada no Azure Active Directory é executada a cada hora para procurar conflitos de atributo duplicado que já tenham sido resolvidos e remove automaticamente os atributos em questão de quarentena.

### <a name="enabling-duplicate-attribute-resiliency"></a>Ativar a resiliência de atributo duplicado
Resiliência de atributo duplicado será o novo comportamento predefinido em todos os inquilinos do Azure Active Directory. Estarão em por predefinição para todos os inquilinos ativada sincronização pela primeira vez no 22 de Agosto de 2016 ou posterior. Inquilinos que ativar a sincronização antes desta data terão a funcionalidade ativada em lotes. Esta implementação começará Setembro de 2016, e uma notificação de correio eletrónico será enviada para o contacto de notificação técnica de cada inquilino com a data específico quando a funcionalidade será ativada.

> [!NOTE]
> Depois de resiliência de atributo duplicado foi ativada não pode ser desativado.

Para verificar se a funcionalidade está ativada para o seu inquilino, pode fazê-por transferir a versão mais recente do módulo do PowerShell do Active Directory do Azure e em execução:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Já não pode utilizar o cmdlet Set-MsolDirSyncFeature proativamente ativar a funcionalidade de resiliência de atributo duplicado antes que está ativado para o seu inquilino. Para poder-se de que a funcionalidade de teste, terá de criar um novo inquilino do Azure Active Directory.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identificação de objetos com DirSyncProvisioningErrors
Existem atualmente dois métodos para identificar objetos que têm estes erros devido a conflitos de propriedade duplicados, Azure Active Directory PowerShell e o Portal de administração do Office 365. Existem planeia alargar adicionais portal baseado em relatórios no futuro.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory do PowerShell
Para os cmdlets do PowerShell neste tópico, o seguinte é verdadeiro:

* Todos os cmdlets seguintes são sensíveis a maiúsculas e minúsculas.
* O **– ErrorCategory PropertyConflict** sempre tem de ser incluída. Não existem atualmente não existem outros tipos de **ErrorCategory**, mas isto pode ser expandido no futuro.

Em primeiro lugar, começar ao executar **Connect MsolService** e introduzir as credenciais de administrador inquilino.

Em seguida, utilize os seguintes cmdlets e operadores para ver os erros de formas diferentes:

1. [Ver todos os](#see-all)
2. [Por tipo de propriedade](#by-property-type)
3. [Por valor em conflito](#by-conflicting-value)
4. [Utilizar uma cadeia de procura](#using-a-string-search)
5. [Ordenados](#sorted)
6. [Uma quantidade limitada ou todos os](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Ver tudo
Assim que estiver ligado, para ver uma lista geral do aprovisionamento de atributo erros no inquilino executam:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Isto produz um resultado como o seguinte:  
 ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "Get MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Por tipo de propriedade
Para ver os erros por tipo de propriedade, adicionar o **- PropertyName** sinalizador com o **UserPrincipalName** ou **ProxyAddresses** argumento:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Ou

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Por valor em conflito
Para ver os erros relacionados com uma propriedade específica, adicione o **- PropertyValue** sinalizador (**- PropertyName** tem de ser utilizado, bem como quando adicionar este sinalizador):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Utilizar uma pesquisa de cadeia
Fazer uma utilização de pesquisa de cadeia abrangente de **- SearchString** sinalizador. Isto pode ser utilizado independentemente de todos os sinalizadores acima, com exceção do **- ErrorCategory PropertyConflict**, sempre que é necessário:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>Uma quantidade limitada ou todos os
1. **MaxResults <Int>**  pode ser utilizado para limitar a consulta para um número específico de valores.
2. **Todos os** pode ser utilizado para garantir que todos os resultados são obtidos no caso de que existe um grande número de erros.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="office-365-admin-portal"></a>Portal de administração do Office 365
Pode ver os erros de sincronização de diretório no Centro de administração do Office 365. O relatório no portal do Office 365 só apresenta **utilizador** objetos que têm estes erros. Não mostra informações sobre conflitos entre **grupos** e **contactos**.

![Utilizadores ativos](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "utilizadores ativos")

Para obter instruções sobre como ver os erros de sincronização de diretórios no Centro de administração do Office 365, consulte [identificar erros de sincronização de diretórios no Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Relatório de erros de sincronização de identidade
Quando é processado um objeto com um conflito de atributo duplicado com este novo comportamento uma notificação está incluída no e-mail de relatório de erros de sincronização de identidade padrão que é enviado para a notificação técnica contactar para o inquilino. No entanto, é uma alteração importante neste comportamento. No passado, as informações sobre um conflito de atributo duplicado seriam incluídas em cada relatório de erro subsequentes até que o conflito que foi resolvido. Com este novo comportamento, a notificação de erro para um determinado conflito só aparecer uma vez - o do momento que o atributo em conflito foi colocado em quarentena.

Eis um exemplo do aspeto a notificação por e-mail para um conflito de /proxyaddress:  
    ![Utilizadores ativos](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "utilizadores ativos")  

## <a name="resolving-conflicts"></a>Resolução de conflitos
Resolução de problemas de resolução e estratégia táticas de TI para estes erros deve não ser diferente da forma que foram processados os erros de atributo duplicado no passado. A única diferença é que a tarefa de temporizador sweeps através do inquilino no lado do serviço para adicionar automaticamente o atributo em questão para o objeto correto depois do conflito é resolvido.

O seguinte artigo descreve várias estratégias de resolução de problemas e de resolução: [duplicado ou atributos inválidos impedem a sincronização de diretórios no Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Problemas conhecidos
Nenhum destes problemas conhecidos faz com que degradação de serviço ou perda de dados. Vários deles estão aesthetic, causam outros padrão "*pré-resiliência*" erros de atributo duplicado para ser emitida em vez de quarentena o atributo de conflito e outra faz com que determinados erros exigir extra manual de segurança de correção.

**Comportamento de núcleo:**

1. Objetos com configurações de atributo específico continuam a receber erros de exportação, por oposição os atributos duplicados que está a ser colocados em quarentena.  
   Por exemplo:
   
    a. Novo utilizador é criado no AD com um UPN de  **Joe@contoso.com**  e /proxyaddress**smtp:Joe@contoso.com**
   
    b. As propriedades deste objeto estão em conflito com um grupo existente, onde é /proxyaddress  **SMTP:Joe@contoso.com** .
   
    c. No momento da exportação, uma **/proxyaddress conflito** erro é apresentado em vez de ter os atributos de conflito colocados em quarentena. A operação é repetida após cada ciclo de sincronização, tal como faria foram antes da funcionalidade de resiliência foi ativada.
2. Se dois grupos são criados no local com o mesmo endereço de SMTP, uma falha ao aprovisionar na primeira tentativa com um padrão duplicado **/proxyaddress** erro. No entanto, o valor duplicada corretamente foi colocado em quarentena após o próximo ciclo de sincronização.

**Relatório de Portal do Office**:

1. A mensagem de erro detalhadas para dois objetos de um conjunto de conflito UPN é o mesmo. Isto indica que tem ambos tinham o UPN alterado / colocados em quarentena, quando na realidade apenas deles tinham quaisquer dados alterados.
2. A mensagem de erro detalhado para um conflito UPN mostra o displayName errado para um utilizador que tenha tido o UPN alterado/em quarentena. Por exemplo:
   
    a. **O utilizador A** sincroniza-se a cópia de segurança primeiro com **UPN = User@contoso.com** .
   
    b. **O utilizador B** tentou ser sincronizado a cópia de segurança seguinte com **UPN = User@contoso.com** .
   
    c. **Utilizador do B** UPN é alterado para  **User1234@contoso.onmicrosoft.com**  e  **User@contoso.com**  é adicionado à **DirSyncProvisioningErrors**.
   
    d. A mensagem de erro para **User B** deve indicar que **utilizador A** já  **User@contoso.com**  como um UPN, mas mostra **User B** displayName próprio.

**Relatório de erros de sincronização de identidade**:

A ligação para *passos sobre como resolver este problema* está incorreto:  
    ![Utilizadores ativos](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "utilizadores ativos")  

Este deve apontar para [https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency).

## <a name="see-also"></a>Consultar também
* [Sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)
* [Identificar erros de sincronização de diretórios no Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

