---
title: "Baseado em atributos filiação dinâmica em grupos no Azure Active Directory | Microsoft Docs"
description: "Como criar regras avançadas para a associação de grupo dinâmico, incluindo operadores de regra de expressão e parâmetros suportados."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: fb434cc2-9a91-4ebf-9753-dd81e289787e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.openlocfilehash: 958ee2f12ebbd46472972a3012ec59aecbc23126
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory"></a>Criar regras baseadas em atributos para filiação dinâmica em grupos no Azure Active Directory
No Azure Active Directory (Azure AD), pode criar regras avançadas para ativar complexas baseadas em atributos filiação dinâmica para grupos. Este artigo fornece detalhes sobre os atributos e a sintaxe para criar regras de associação dinâmica para utilizadores ou dispositivos.

Quando forem efetuadas alterações de atributos de um utilizador ou dispositivo, o sistema avalia todas as regras de grupo dinâmico num diretório para ver se acionaria a alteração de qualquer grupo adiciona ou remove. Se um utilizador ou dispositivo satisfaz uma regra num grupo, eles são adicionados como um membro desse grupo. Se estes já não satisfazem a regra, estes serão removidos.

> [!NOTE]
> - Pode configurar uma regra de filiação dinâmica em grupos de segurança ou grupos do Office 365.
>
> - Esta funcionalidade necessita de uma licença do Azure AD Premium P1 para cada membro do utilizador adicionado a pelo menos um grupo dinâmico. Não é obrigatório para, efetivamente, atribuir licenças aos utilizadores para os mesmos para serem membros de grupos dinâmicos, mas tem de ter o número mínimo de licenças no inquilino para cobrir todos os utilizadores deste tipo. Por exemplo: Se tiver um total de 1000 utilizadores exclusivos em todos os grupos dinâmicos no seu inquilino, tem de ter, pelo menos, 1000 licenças para o Azure AD Premium P1 ou acima, para cumprir o requisito de licença.
>
> - Pode criar um grupo dinâmico para utilizadores ou dispositivos, mas não é possível criar uma regra que contém objetos de dispositivo e utilizador.

> - De momento, não é possível criar um grupo de dispositivos com base nos atributos de utilizador de proprietário. Regras de associação do dispositivo só podem referenciar imediatos atributos de objetos de dispositivo no diretório.

## <a name="to-create-an-advanced-rule"></a>Para criar uma regra avançada
1. Iniciar sessão para o [Centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta que seja um administrador global ou um administrador de conta de utilizador.
2. Selecione **utilizadores e grupos**.
3. Selecione **todos os grupos**e selecione **novo grupo**.

   ![Adicionar novo grupo](./media/active-directory-groups-dynamic-membership-azure-portal/new-group-creation.png)

4. No **grupo** painel, introduza um nome e descrição para o novo grupo. Selecione um **tipo de associação** do **utilizador dinâmica** ou **dispositivos dinâmica**, consoante se pretende criar uma regra para utilizadores ou dispositivos e, em seguida, selecione **Consulta dinâmica adicionar**. Pode utilizar o construtor de regra para criar uma regra simples ou escrever uma regra de avançadas por si. Este artigo contém mais informações sobre os atributos de utilizador e dispositivo disponíveis, bem como exemplos de regras avançadas.

   ![Adicionar regra de associação dinâmica](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)

5. Depois de criar a regra, selecione **adicionar consulta** na parte inferior do painel.
6. Selecione **criar** no **grupo** painel para criar o grupo.

> [!TIP]
> Criação do grupo pode falhar se a regra avançada introduzida estava incorreta. Será apresentada uma notificação no canto superior direito do portal; contém uma explicação sobre o motivo pelo qual a regra não foi aceite pelo sistema. Leia cuidadosamente para compreender como terá de ajustar a regra para torná-lo válido.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Construir o corpo de uma regra de avançadas
A regra avançada que pode criar para a filiação dinâmica para grupos é, essencialmente, uma expressão binária que consiste em três partes e resulta num resultado de true ou false. As três partes são:

* Parâmetro esquerdo
* Operador binário
* Constante direita

Uma regra avançada concluída semelhante a isto: (leftParameter binaryOperator "RightConstant"), onde a abertura e parênteses de fecho são opcionais para a expressão binária completa, aspas duplas são opcionais, apenas necessário para a direita constante Quando é cadeia e a sintaxe para o parâmetro esquerda é user.property. Uma regra de avançadas pode consistir em mais do que um binárias expressões separadas por- e- ou e - operadores lógicos não.

Seguem-se exemplos de uma regra avançada construído corretamente:
```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```
Para obter a lista completa de parâmetros suportados e operadores de regra de expressão, consulte as secções abaixo. Para os atributos utilizados para as regras de dispositivos, consulte [utilizar atributos para criar regras para objetos de dispositivo](#using-attributes-to-create-rules-for-device-objects).

O comprimento total do corpo da sua regra avançada não pode exceder 2048 carateres.

> [!NOTE]
> As operações de cadeia e regex não são sensíveis a maiúsculas e minúsculas. Também pode executar verificações de Null, utilizando $null como uma constante, por exemplo, user.department - eq $null.
> As cadeias que contenham aspas "escape utilizando ' caráter, por exemplo, user.department - eq \`"Vendas".

## <a name="supported-expression-rule-operators"></a>Operadores de regra de expressão suportados
A tabela seguinte apresenta uma lista de todos os operadores de regra de expressão suportados e os respetivos sintaxe a ser utilizado no corpo da regra avançada:

| operador | Sintaxe |
| --- | --- |
| Não é igual a |-ne |
| igual a |-eq |
| Não começa com |-notStartsWith |
| Começa por |-startsWith |
| Não contém |-notContains |
| Contains |-contém |
| Não corresponde |-notMatch |
| Correspondência |-corresponder |
| No | -no |
| Não | -notIn |

## <a name="operator-precedence"></a>Precedência de operador

Todos os operadores estão listados abaixo por precedência inferior de para superior. Operadores na mesma linha são em igual precedência:
````
-any -all
-or
-and
-not
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
````
Todos os operadores podem ser utilizados com ou sem o prefixo de hífen. Parênteses são necessárias apenas quando a precedência não cumpre os requisitos.
Por exemplo:
```
   user.department –eq "Marketing" –and user.country –eq "US"
```
é equivalente ao:
```
   (user.department –eq "Marketing") –and (user.country –eq "US")
```
## <a name="using-the--in-and--notin-operators"></a>Utilizar-na - notIn operadores e

Se pretender comparar o valor de um atributo de utilizador em relação a um número de valores diferentes pode utilizar-na ou - notIn operadores. Eis um exemplo utilizando-no operador:
```
    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]
```
Tenha em atenção a utilização de "[" e "]" no início e no fim da lista de valores. Esta condição for avaliada como True do valor de user.department igual a um dos valores existentes na lista.


## <a name="query-error-remediation"></a>Remediação de erro de consulta
A tabela seguinte lista erros comuns e como corrigir as entradas

| Erro de análise de consulta | Utilização de erro | Utilização corrigida |
| --- | --- | --- |
| Erro: O atributo não suportado. |(user.invalidProperty - eq "Value") |(user.department - eq "value")<br/><br/>Certifique-se o atributo for o [suportado lista de propriedades](#supported-properties). |
| Erro: O operador não é suportado no atributo. |(user.accountEnabled-contém VERDADEIRO) |(user.accountEnabled - eq verdadeiro)<br/><br/>O operador utilizado não é suportado para o tipo de propriedade (neste exemplo,-contém não pode ser utilizado no tipo Booleano). Utilize os operadores corretos para o tipo de propriedade. |
| Erro: Erro de compilação de consulta. |1. (user.department - eq "Vendas") (user.department - eq "Marketing")<br/><br/>2. (user.userPrincipalName-corresponder "*@domain.ext") |1. Operador em falta. Utilize o - e ou - ou dois associe predicados<br/><br/>(user.department - eq "Vendas")- ou (user.department - eq "Marketing")<br/><br/>2 erro de numa expressão regular utilizada com - corresponder<br/><br/>(user.userPrincipalName-corresponder a ". *@domain.ext"), em alternativa: (user.userPrincipalName-corresponder "@domain.ext$")|

## <a name="supported-properties"></a>Propriedades suportadas
Seguem-se todas as propriedades de utilizador que pode utilizar na sua regra avançada:

### <a name="properties-of-type-boolean"></a>Propriedades do tipo Booleano
Operadores permitidos

* -eq
* -ne

| Propriedades | Valores permitidos | Utilização |
| --- | --- | --- |
| accountEnabled |VERDADEIRO false |user.accountEnabled - eq verdadeiro |
| DirSyncEnabled |VERDADEIRO false |user.dirSyncEnabled - eq verdadeiro |

### <a name="properties-of-type-string"></a>Propriedades do tipo cadeia
Operadores permitidos

* -eq
* -ne
* -notStartsWith
* -StartsWith
* -contém
* -notContains
* -corresponder
* -notMatch
* -no
* -notIn

| Propriedades | Valores permitidos | Utilização |
| --- | --- | --- |
| city |Qualquer valor de cadeia ou $null |(user.city - eq "value") |
| País |Qualquer valor de cadeia ou $null |(User. Country - eq "value") |
| companyName | Qualquer valor de cadeia ou $null | (user.companyName - eq "value") |
| Departamento |Qualquer valor de cadeia ou $null |(user.department - eq "value") |
| displayName |Qualquer valor de cadeia |(user.displayName - eq "value") |
| facsimileTelephoneNumber |Qualquer valor de cadeia ou $null |(user.facsimileTelephoneNumber - eq "value") |
| givenName |Qualquer valor de cadeia ou $null |(user.givenName - eq "value") |
| jobTitle |Qualquer valor de cadeia ou $null |(user.jobTitle - eq "value") |
| capacidade de correio |Qualquer valor de cadeia ou $null (endereço SMTP do utilizador) |(user.mail - eq "value") |
| mailNickName |Qualquer valor de cadeia (alias de correio do utilizador) |(user.mailNickName - eq "value") |
| Mobile |Qualquer valor de cadeia ou $null |(user.mobile - eq "value") |
| objectId |GUID de objeto de utilizador |(user.objectId - eq "1111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | No local o identificador de segurança (SID) para os utilizadores que foram sincronizadas no local para a nuvem. |(user.onPremisesSecurityIdentifier - eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |Nenhum DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies - eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Qualquer valor de cadeia ou $null |(user.physicalDeliveryOfficeName - eq "value") |
| PostalCode |Qualquer valor de cadeia ou $null |(user.postalCode - eq "value") |
| preferredLanguage |Código do ISO 639-1 |(user.preferredLanguage - eq "en-US") |
| sipProxyAddress |Qualquer valor de cadeia ou $null |(user.sipProxyAddress - eq "value") |
| state |Qualquer valor de cadeia ou $null |(user.state - eq "value") |
| StreetAddress |Qualquer valor de cadeia ou $null |(user.streetAddress - eq "value") |
| Apelido |Qualquer valor de cadeia ou $null |(user.surname - eq "value") |
| telephoneNumber |Qualquer valor de cadeia ou $null |(user.telephoneNumber - eq "value") |
| usageLocation |Indicativo de país lettered dois |(user.usageLocation - eq "US") |
| userPrincipalName |Qualquer valor de cadeia |(user.userPrincipalName - eq "alias@domain") |
| UserType |convidado de membro $null |(user.userType - eq "Membro") |

### <a name="properties-of-type-string-collection"></a>Propriedades de coleção de cadeia de tipo
Operadores permitidos

* -contém
* -notContains

| Propriedades | Valores permitidos | Utilização |
| --- | --- | --- |
| otherMails |Qualquer valor de cadeia |(user.otherMails-contém "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp:alias@domain |(user.proxyAddresses-contém "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>Propriedades de valores múltiplos
Operadores permitidos

* -qualquer (satisfeitos quando, pelo menos, um item na coleção corresponde a condição)
* -todos os (satisfeitos quando todos os itens na coleção correspondem à condição)

| Propriedades | Valores | Utilização |
| --- | --- | --- |
| assignedPlans |Cada objeto na coleção expõe as seguintes propriedades de cadeia: capabilityStatus, serviço, servicePlanId |user.assignedPlans-qualquer (assignedPlan.servicePlanId - eq "efb87545-963c-4e0d-99df-69c6916d9eb0"- e assignedPlan.capabilityStatus - eq "Ativado") |

Propriedades de valores múltiplos são coleções de objetos do mesmo tipo. Pode utilizar - os e - todos os operadores para aplicar uma condição a um ou todos os itens na coleção, respetivamente. Por exemplo:

assignedPlans é uma propriedade de valor múltiplos apresenta uma lista de todos os planos de serviço atribuídos ao utilizador. O abaixo expressão irá selecionar os utilizadores que possuem o plano de serviço Exchange Online (plano 2), que também está num Estado ativado:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(O identificador de Guid identifica o plano de serviço Exchange Online (plano 2).)

> [!NOTE]
> Isto é útil se pretender identificar todos os utilizadores para o qual um Office 365 (ou outro serviço Online da Microsoft) capacidade tiver sido ativada, por exemplo, para dirigi-las um determinado conjunto de políticas.

A seguinte expressão seleciona todos os utilizadores com qualquer plano de serviço que estão associado com o serviço do Intune (identificado pelo nome do serviço "SCO"):
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

## <a name="use-of-null-values"></a>Utilização de valores nulos

Para especificar um valor nulo numa regra, pode utilizar "null" ou $null. Exemplo:
```
   user.mail –ne null
```
é equivalente ao
```
   user.mail –ne $null
   ```

## <a name="extension-attributes-and-custom-attributes"></a>Atributos de extensões e atributos personalizados
São suportados atributos de extensões e atributos personalizados nas regras de associação dinâmica.

Atributos de extensão são sincronizados a partir do servidor de janela no local AD e ter o formato de "ExtensionAttributeX", em que X é igual a 1-15.
Um exemplo de uma regra que utiliza um atributo de extensão seria
```
(user.extensionAttribute15 -eq "Marketing")
```
Atributos personalizados são sincronizados no local do Windows Server AD ou a uma aplicação SaaS ligada e o formato de "user.extension_[GUID]\__ [atributo]", em que [GUID] é o identificador exclusivo no AAD para a aplicação que criou o atributo no AAD e [atributo] é o nome do atributo que foi criado.
É um exemplo de uma regra que utiliza um atributo personalizado
```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  
```
O nome do atributo personalizado pode ser encontrado no diretório através da consulta de um utilizador do atributo através do Explorador do gráfico e procurar o nome de atributo.

## <a name="direct-reports-rule"></a>Regra de "Relatórios diretos"
Pode criar um grupo que contém todos os relatórios de direta de um gestor. Quando direta os o Gestor relatórios são alterados no futuro, associação a grupo será ajustada automaticamente.

> [!NOTE]
> 1. Para a regra funcionar, certifique-se de que o **Manager ID** propriedade está definida corretamente os utilizadores no seu inquilino. Pode verificar o valor atual para um utilizador no respetivo **separador perfil**.
> 2. Esta regra só suporta **direta** relatórios. Atualmente não é possível criar um grupo para uma hierarquia aninhada, por exemplo, um grupo que inclua os seus relatórios e relatórios diretos.

**Para configurar o grupo**

1. Siga os passos 1 a 5 da secção [para criar a regra avançada](#to-create-the-advanced-rule)e selecione um **tipo de associação** de **utilizador dinâmica**.
2. No **regras de associação dinâmica** painel, introduza a regra com a seguinte sintaxe:

    *Relatórios diretos para "{obectID_of_manager}"*

    Um exemplo de uma regra válida:
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. Depois de guardar a regra, todos os utilizadores com o valor de ID do Manager especificado serão adicionados ao grupo.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>Utilizar atributos para criar regras para objetos de dispositivo
Também pode criar uma regra que seleciona objetos de dispositivo para a associação num grupo. Os seguintes atributos do dispositivo podem ser utilizados.

 Atributo de dispositivo  | Valores | Exemplo
 ----- | ----- | ----------------
 accountEnabled | VERDADEIRO false | (device.accountEnabled - eq verdadeiro)
 displayName | Qualquer valor de cadeia |(device.displayName - eq "Rob Iphone")
 DeviceOSType | Qualquer valor de cadeia | (device.deviceOSType - eq "iPad")- ou (device.deviceOSType - eq "iPhone")
 DeviceOSVersion | Qualquer valor de cadeia | (o dispositivo. OSVersion - eq "9.1")
 deviceCategory | um nome de categoria de dispositivo válido | (device.deviceCategory - eq "BYOD")
 DeviceManufacturer | Qualquer valor de cadeia | (device.deviceManufacturer - eq "Samsung")
 DeviceModel | Qualquer valor de cadeia | (device.deviceModel - eq "iPad ondas Eletromagnéticas")
 deviceOwnership | Pessoal, empresa, desconhecido | (device.deviceOwnership - eq "Empresa")
 domainName | Qualquer valor de cadeia | (device.domainName - eq "contoso.com")
 enrollmentProfileName | Nome do perfil de inscrição de dispositivos Apple | (device.enrollmentProfileName - eq "DEP iPhones")
 isRooted | VERDADEIRO false | (device.isRooted - eq verdadeiro)
 managementType | MDM (para dispositivos móveis)<br>PC (para computadores geridos pelo agente de PC do Intune) | (device.managementType - eq "MDM")
 OrganizationalUnit | qualquer valor de cadeia correspondente ao nome da unidade organizacional definido por um Active Directory no local | (device.organizationalUnit - eq "E.U.A. PCs")
 deviceId | um ID de dispositivo do Azure AD | (device.deviceId - eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | ID de objeto de um válido do Azure AD |  (device.objectId - eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")



## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>A alteração de associação dinâmica para estático e vice versa
É possível alterar como é gerida a associação num grupo. Isto é útil quando pretender manter o mesmo nome de grupo e ID no sistema, todas as referências existentes para o grupo ainda são válidas; criar um novo grupo seria necessários atualizar essas referências.

Estamos no processo de atualização do portal do Azure para suportar esta funcionalidade. Entretanto, pode utilizar o [portal clássico do Azure](https://manage.windowsazure.com) (siga as instruções [aqui](active-directory-accessmanagement-groups-with-advanced-rules.md#changing-dynamic-membership-to-static-and-vice-versa)) ou utilizar cmdlets do PowerShell, conforme mostrado abaixo.

> [!WARNING]
> Quando alterar um grupo estático existente para um grupo dinâmico, todos os membros existentes serão removidos do grupo e, em seguida, será processada a regra de associação para adicionar novos membros. Se o grupo é utilizado para controlar o acesso a aplicações ou recursos, os membros originais podem perder o acesso até que a regra de associação é totalmente processada.
>
> É uma prática recomendada para testar a nova regra de associação previamente para se certificar de que a nova associação no grupo é o esperado.

**Utilizar o PowerShell para gestão de associação num grupo de alterações**

> [!NOTE]
> Para alterar as propriedades de grupo dinâmico, terá de utilizar os cmdlets da **a versão de pré-visualização do** [versão 2 do Azure AD PowerShell](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Pode instalar a pré-visualização do [aqui](https://www.powershellgallery.com/packages/AzureADPreview).

Eis um exemplo de funções que mudam de gestão de associação de um grupo existente. Tenha em atenção que o máximo cuidado corretamente manipular a propriedade GroupTypes e preservar os valores que podem existir existe, não relacionado com a filiação dinâmica.

```
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Para tornar um grupo estático:
```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```
Para tornar um grupo dinâmico:
```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```
## <a name="next-steps"></a>Passos seguintes
Estes artigos fornecem informações adicionais sobre os grupos no Azure Active Directory.

* [Consulte os grupos existentes](active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionar membros](active-directory-groups-create-azure-portal.md)
* [Gerir as definições de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerir membros de um grupo](active-directory-groups-membership-azure-portal.md)
* [Gerir regras dinâmicas para os utilizadores de um grupo](active-directory-groups-dynamic-membership-azure-portal.md)
