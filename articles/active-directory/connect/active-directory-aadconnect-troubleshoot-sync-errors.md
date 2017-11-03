---
title: "O Azure AD Connect: Resolução de problemas de erros durante a sincronização | Microsoft Docs"
description: "Explica como resolver erros encontrados durante a sincronização com o Azure AD Connect."
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: billmath
ms.openlocfilehash: 5a319de69c4e142414ab8f2be980a6576acbf8bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-errors-during-synchronization"></a>Resolução de problemas de erros durante a sincronização
Poderão ocorrer erros quando os dados de identidade são sincronizados a partir do Windows Server Active Directory (AD DS) para o Azure Active Directory (Azure AD). Este artigo fornece uma descrição geral das diferentes tipos de erros de sincronização, alguns dos possíveis cenários que fazer com que os erros e formas possíveis para corrigir os erros. Este artigo inclui os tipos de erro comuns e não pode abranger todos os erros possíveis.

 Este artigo assume que o leitor está familiarizado com o subjacente [design conceitos do Azure AD e o Azure AD Connect](active-directory-aadconnect-design-concepts.md).

Com a versão mais recente do Azure AD Connect \(Agosto de 2016 ou superior\), um relatório de erros de sincronização está disponível no [Portal do Azure](https://aka.ms/aadconnecthealth) como parte do Azure AD Connect Health para sincronização.

A partir de 1 de Setembro de 2016 [do Azure Active Directory duplicado atributo resiliência](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) funcionalidade será ativada por predefinição para todas as *novo* inquilinos do Azure Active Directory do. Esta funcionalidade será ativada automaticamente para os inquilinos existentes nos meses futuros.

O Azure AD Connect efetua 3 tipos de operações do diretórios mantém sincronizada: Import, sincronização e exportação. Erros podem ocorrer em todas as operações. Este artigo incida principalmente no erros durante a exportação para o Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Erros durante a exportação para o Azure AD
A seguinte secção descreve os diferentes tipos de erros de sincronização que podem ocorrer durante a operação de exportação para o Azure AD através do conector do Azure AD. Este conector pode ser identificado pelo formato de nome que está a ser "contoso. *onmicrosoft.com*".
Erros durante a exportação para o Azure AD indicam que a operação \(adicionar, atualizar, eliminar etc.\) tentada pelo Azure AD Connect \(motor de sincronização\) no Azure Active Directory falhou.

![Descrição geral de erros de exportação](./media/active-directory-aadconnect-troubleshoot-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Erros de falta de correspondência de dados
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Descrição
* Quando do Azure AD Connect \(motor de sincronização\) dá instruções ao Azure Active Directory para adicionar ou atualizar objetos, do Azure AD corresponde ao objeto de entrada, utilizando o **sourceAnchor** atributo para o **immutableId**  atributo dos objetos no Azure AD. Esta correspondência é chamada um **corresponde ao disco rígido**.
* Quando do Azure AD **não encontre** qualquer objeto que corresponda a **immutableId** atributo com o **sourceAnchor** atributo do objeto recebido, antes do aprovisionamento de um novo o objeto, retrocede para utilizar os atributos ProxyAddresses e UserPrincipalName para encontrar uma correspondência. Esta correspondência é chamada um **corresponder recuperável**. Corresponder recuperável foi concebido para corresponder aos objetos já está presentes no Azure AD (que são obtidas as no Azure AD) com os novos objetos a adicionar/atualizar durante a sincronização que representam a mesma entidade (utilizadores, grupos) no local.
* **InvalidSoftMatch** erro ocorre quando a correspondência de disco rígida não encontrar quaisquer objetos correspondentes **e** correspondência de forma recuperável localiza um objeto correspondente, mas esse objeto tem um valor diferente de *immutableId* que o objeto de entrada *SourceAnchor*, sugerindo que o objeto correspondente foi sincronizado com outro objeto no local do Active Directory.

Por outras palavras, por ordem para a correspondência de forma recuperável funcionar, o objeto ser correspondido de forma recuperável com não deve ter qualquer valor para o *immutableId*. Se qualquer um objeto com *immutableId* conjunto com um valor está a falhar a disco rígido correspondência, mas que satisfaçam os critérios de correspondência de forma recuperável, a operação iria resultar num erro InvalidSoftMatch sincronização.

Esquema do Active Directory do Azure não permite duas ou mais objetos têm o mesmo valor dos seguintes atributos. \(Não se trata de uma lista exaustiva.\)

* ProxyAddresses
* userPrincipalName
* onPremisesSecurityIdentifier
* objectId

> [!NOTE]
> [Azure AD atributo duplicado atributo resiliência](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) funcionalidade também está a ser implementada como comportamento predefinido do Azure Active Directory.  Isto irá reduzir o número de erros de sincronização visto pelo Azure AD Connect (bem como outros clientes de sincronização) fazendo do Azure AD mais resiliente da forma que processa duplicados atributos ProxyAddresses e UserPrincipalName presente nos ambientes no local o AD. Esta funcionalidade não corrigir os erros de duplicação. Por isso, os dados ainda tem de ser corrigidos. Mas, permite o aprovisionamento de novos objetos que caso contrário, estão bloqueados devido a valores duplicados, que está a ser aprovisionado no Azure AD. Isto também irá reduzir o número de erros de sincronização devolvido para o cliente de sincronização.
> Se esta funcionalidade está ativada para o seu inquilino, não verá os erros de sincronização InvalidSoftMatch vistos durante o aprovisionamento de novos objetos.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Cenários de exemplo para InvalidSoftMatch
1. Dois ou mais objetos com o mesmo valor do atributo ProxyAddresses existe no local do Active Directory. Apenas um é obter aprovisionado no Azure AD.
2. Dois ou mais objetos com o mesmo valor de userPrincipalName existe no local do Active Directory. Apenas um é obter aprovisionado no Azure AD.
3. Um objeto foi adicionado no local do Active Directory com o mesmo valor do atributo ProxyAddresses que um objeto existente no Azure Active Directory. O objeto adicionado no local não está a obter aprovisionado no Azure Active Directory.
4. Um objeto foi adicionado no local do Active Directory com o mesmo valor do atributo userPrincipalName que uma conta no Azure Active Directory. O objeto não está a obter aprovisionado no Azure Active Directory.
5. Uma conta sincronizada tiver sido movida de floresta para a floresta B. Azure AD Connect (motor de sincronização) estava a utilizar o atributo de ObjectGUID para calcular o SourceAnchor. Após a mudança de floresta, o valor de SourceAnchor é diferente. O novo objeto (a partir da floresta B) está a conseguir sincronizar com o objeto existente no Azure AD.
6. Um objeto sincronizado obteve sejam eliminado acidentalmente no local do Active Directory e um novo objeto foi criado no Active Directory para a mesma entidade (por exemplo, o utilizador) sem eliminar a conta no Azure Active Directory. A nova conta não consegue sincronizar com o objeto existente do Azure AD.
7. O Azure AD Connect foi desinstalado e reinstalado. Durante a instalação nova, um atributo diferente foi escolhido como a SourceAnchor. Todos os objetos que tinham anteriormente sincronizado paragem a sincronizar com o erro de InvalidSoftMatch.

#### <a name="example-case"></a>Cenário de exemplo:
1. **Bernardo Santos** é um utilizador sincronizado no Azure Active Directory de no local do Active Directory de *contoso.com*
2. Do Bernardo Santos **UserPrincipalName** está definido como  **bobs@contoso.com** .
3. **"abcdefghijklmnopqrstuv = ="** é o **SourceAnchor** calculado pelo Azure AD Connect através do Bernardo Santos **objectGUID** no local do Active Directory, que é o  **immutableId** para Bernardo Santos no Azure Active Directory.
4. João também tem os seguintes valores para o **proxyAddresses** atributo:
   * smtp:bobs@contoso.com
   * smtp:bob.smith@contoso.com
   * **smtp:bob@contoso.com**
5. Um novo utilizador **Bernardo Taylor**, é adicionado no local do Active Directory.
6. De Bernardo Taylor **UserPrincipalName** está definido como  **bobt@contoso.com** .
7. **"abcdefghijkl0123456789 = =" "** é o **sourceAnchor** calculado pelo Azure AD Connect através de Bernardo Taylor **objectGUID** no local do Active Directory. Objeto do de Bob Taylor não tiver sincronizado ainda ao Azure Active Directory.
8. Bernardo Taylor com os seguintes valores para o atributo proxyAddresses
   * smtp:bobt@contoso.com
   * smtp:bob.taylor@contoso.com
   * **smtp:bob@contoso.com**
9. Durante a sincronização, o Azure AD Connect irá reconhecer a adição de Bernardo Taylor no local do Active Directory e solicite ao Azure AD para que a alteração do mesma.
10. Azure AD pela primeira vez irá efetuar a correspondência de disco rígida. Ou seja, pesquisará se existir qualquer objeto com o immutableId igual a "abcdefghijkl0123456789 = =". Correspondência de disco rígida irá falhar, nenhum outro objeto no Azure AD terão que immutableId.
11. Azure AD, em seguida, irá tentar Bernardo Taylor de correspondência de forma recuperável. Ou seja, pesquisará se existir qualquer objeto com proxyAddresses igual a três valores, incluindosmtp:bob@contoso.com
12. Do Azure AD irá encontrar objeto do Bernardo Santos para fazer corresponder os critérios de correspondência de forma recuperável. Mas este objeto tem o valor de immutableId = "abcdefghijklmnopqrstuv = =". indica que este objeto foi sincronizado a partir de outro objeto no local do Active Directory. Assim, do Azure AD não é possível configuração soft-match estes objetos e os resultados num **InvalidSoftMatch** erro de sincronização.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Saber como corrigir InvalidSoftMatch erro
A razão mais comum para o erro InvalidSoftMatch é dois objetos com diferentes SourceAnchor \(immutableId\) têm o mesmo valor para os atributos ProxyAddresses e/ou UserPrincipalName, que são utilizados durante a configuração soft-correspondência processar sobre o Azure AD. Para corrigir a correspondência de forma recuperável inválido

1. Identifique o proxyAddresses duplicado, userPrincipalName ou outro valor de atributo que está a causar o erro. Também identificar que dois \(ou mais\) objetos envolvidos no conflito. O relatório gerado pelo [do Azure AD Connect Health para sincronização](https://aka.ms/aadchsyncerrors) podem ajudar a identificar os dois objetos.
2. Identifica o objeto deve continuar a ter o valor de duplicados e não deve o objeto.
3. Remova o valor de duplicação do objeto que não deve ter esse valor. Tenha em atenção que deve efetuar a alteração no diretório onde o objeto de origem. Em alguns casos, poderá ter de eliminar um dos objetos em conflito.
4. Se efetuou a alteração no local no AD, permitir que o Azure AD Connect, a alteração de sincronização.

Tenha em atenção que o relatório de erros de sincronização no Azure AD Connect Health para sincronização é atualizado a cada 30 minutos e inclui os erros de tentativa de sincronização mais recente.

> [!NOTE]
> ImmutableId, por definição, não deve alterar na duração do objeto. Se o Azure AD Connect não foi configurado com alguns dos cenários em mente da lista acima, pode acabar numa situação em que o Azure AD Connect calcula um valor diferente de SourceAnchor para o objecto do AD que representa a mesma entidade (mesmo utilizador/grupo / contacto, etc.) que tenha um objeto do AD do Azure existente que pretende continuar a utilizar.
>
>

#### <a name="related-articles"></a>Artigos relacionados
* [Atributos duplicados ou é inválidos impedem a sincronização de diretórios no Office 365](https://support.microsoft.com/en-us/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Descrição
Quando tenta corresponder soft dois objetos do Azure AD, é possível que dois objetos de diferentes "tipo de objeto" (por exemplo, o utilizador, grupo, etc. de contacto) ter os mesmos valores para os atributos utilizados para efetuar a correspondência de forma recuperável. Como duplicação destes atributos não é permitida no Azure AD, a operação pode resultar num erro de sincronização de "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Cenários de exemplo para ObjectTypeMismatch erro
* É criado um grupo de segurança de correio ativado no Office 365. Administrador adiciona um novo utilizador ou o contacto no local AD (que não está sincronizado com o Azure AD ainda) com o mesmo valor para o atributo ProxyAddresses que o grupo do Office 365.

#### <a name="example-case"></a>Cenário de exemplo
1. O administrador cria um novo grupo de segurança de correio ativado no Office 365 para o departamento de dedução dos impostos e fornece um endereço de e-mail como tax@contoso.com. Esta ação atribui o atributo de ProxyAddresses para este grupo com o valor do**smtp:tax@contoso.com**
2. Um novo utilizador associa Contoso.com e é criada uma conta para o utilizador no local com o /proxyaddress como**smtp:tax@contoso.com**
3. Quando o Azure AD Connect irá sincronizar a nova conta de utilizador, obterá o erro "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Saber como corrigir ObjectTypeMismatch erro
A razão mais comum para o erro ObjectTypeMismatch é dois objetos de tipo diferente (utilizador, grupo, etc. de contacto) têm o mesmo valor para o atributo ProxyAddresses. Para corrigir o ObjectTypeMismatch:

1. Identificar o proxyAddresses duplicado (ou outro atributo) valor que está a causar o erro. Também identificar que dois \(ou mais\) objetos envolvidos no conflito. O relatório gerado pelo [do Azure AD Connect Health para sincronização](https://aka.ms/aadchsyncerrors) podem ajudar a identificar os dois objetos.
2. Identifica o objeto deve continuar a ter o valor de duplicados e não deve o objeto.
3. Remova o valor de duplicação do objeto que não deve ter esse valor. Tenha em atenção que deve efetuar a alteração no diretório onde o objeto de origem. Em alguns casos, poderá ter de eliminar um dos objetos em conflito.
4. Se efetuou a alteração no local no AD, permitir que o Azure AD Connect, a alteração de sincronização. Relatório de erros de sincronização no Azure AD Connect Health para sincronização for atualizado a cada 30 minutos e inclui os erros de tentativa de sincronização mais recente.

## <a name="duplicate-attributes"></a>Atributos duplicados
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Descrição
Esquema do Active Directory do Azure não permite duas ou mais objetos têm o mesmo valor dos seguintes atributos. É que cada objeto no Azure AD é forçado a ter um valor exclusivo destes atributos, uma instância especificada.

* ProxyAddresses
* userPrincipalName

Se o Azure AD Connect tenta adicionar um novo objeto ou atualizar um objeto existente com um valor para os atributos acima, que já está atribuído a outro objeto no Azure Active Directory, a operação resulta num erro de sincronização "AttributeValueMustBeUnique".

#### <a name="possible-scenarios"></a>Os possíveis cenários:
1. Valor de duplicados está atribuído a um objeto já sincronizado, que está em conflito com outro objeto sincronizado.

#### <a name="example-case"></a>Cenário de exemplo:
1. **Bernardo Santos** é um utilizador sincronizado no Azure Active Directory de no local do Active Directory de contoso.com
2. Do Bernardo Santos **UserPrincipalName** no local está definido como  **bobs@contoso.com** .
3. João também tem os seguintes valores para o **proxyAddresses** atributo:
   * smtp:bobs@contoso.com
   * smtp:bob.smith@contoso.com
   * **smtp:bob@contoso.com**
4. Um novo utilizador **Bernardo Taylor**, é adicionado no local do Active Directory.
5. De Bernardo Taylor **UserPrincipalName** está definido como  **bobt@contoso.com** .
6. **Bernardo Taylor** com os seguintes valores para o **ProxyAddresses** atributo i. smtp:bobt@contoso.comII. smtp:bob.taylor@contoso.com
7. Objeto do de Bob Taylor está sincronizado com o Azure AD com êxito.
8. Decidiu Admin atualizar de Bernardo Taylor **ProxyAddresses** atributo com o seguinte valor: Posso. **smtp:bob@contoso.com**
9. Azure AD irá tentar atualizar o objeto do de Bob Taylor no Azure AD com o valor acima, mas que a operação falhará como que ProxyAddresses valor já foi atribuído à Bernardo Santos, resultando no erro de "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Saber como corrigir AttributeValueMustBeUnique erro
A razão mais comum para o erro AttributeValueMustBeUnique é dois objetos com diferentes SourceAnchor \(immutableId\) têm o mesmo valor para os atributos ProxyAddresses e/ou UserPrincipalName. Para corrigir o erro de AttributeValueMustBeUnique

1. Identifique o proxyAddresses duplicado, userPrincipalName ou outro valor de atributo que está a causar o erro. Também identificar que dois \(ou mais\) objetos envolvidos no conflito. O relatório gerado pelo [do Azure AD Connect Health para sincronização](https://aka.ms/aadchsyncerrors) podem ajudar a identificar os dois objetos.
2. Identifica o objeto deve continuar a ter o valor de duplicados e não deve o objeto.
3. Remova o valor de duplicação do objeto que não deve ter esse valor. Tenha em atenção que deve efetuar a alteração no diretório onde o objeto de origem. Em alguns casos, poderá ter de eliminar um dos objetos em conflito.
4. Se efetuou a alteração no local no AD, permitir que o Azure AD Connect, a alteração para o erro obter fixo de sincronização.

#### <a name="related-articles"></a>Artigos relacionados
-[Atributos duplicados ou é inválidos impedem a sincronização de diretórios no Office 365](https://support.microsoft.com/en-us/kb/2647098)

## <a name="data-validation-failures"></a>Falhas de validação de dados
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Descrição
Active Directory do Azure impõe restrições vários nos dados antes de permitir que os dados ser escrito para o diretório. Isto é para garantir que os utilizadores finais podem obter as melhores experiências possíveis ao utilizar as aplicações que dependem destes dados.

#### <a name="scenarios"></a>Cenários
a. O valor do atributo UserPrincipalName tem carateres inválidos/não suportado.
b. O atributo UserPrincipalName não segue o formato requerido.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Saber como corrigir IdentityDataValidationFailed erro
a. Certifique-se de que o atributo userPrincipalName tem suportados carateres e o formato requerido.

#### <a name="related-articles"></a>Artigos relacionados
* [Preparar a aprovisionar utilizadores através da sincronização de diretórios para Office 365](https://support.office.com/en-us/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Descrição
Este é um cenário específico que resulte num **"FederatedDomainChangeError"** erro de sincronização, quando o sufixo de UserPrincipalName um utilizador é alterado de um domínio federado para outro domínio federado.

#### <a name="scenarios"></a>Cenários
Para um utilizador sincronizado, o sufixo de UserPrincipalName foi alterado de um domínio federado para outro domínio federado no local. Por exemplo, *UserPrincipalName = bob@contoso.com*  foi alterado para *UserPrincipalName = bob@fabrikam.com* .

#### <a name="example"></a>Exemplo
1. Bernardo Santos, uma conta para Contoso.com, obtém adicionado como um novo utilizador no Active Directory com o UserPrincipalNamebob@contoso.com
2. Bernardo é movido para uma divisão de Contoso.com denominada Fabrikam.com diferente e o UserPrincipalName é alterado parabob@fabrikam.com
3. Domínios contoso.com e fabrikam.com são domínios federados com o Azure Active Directory.
4. UserPrincipalName do de Bob não for atualizado e resulta num erro de sincronização "FederatedDomainChangeError".

#### <a name="how-to-fix"></a>Saber como corrigir
Se o sufixo de UserPrincipalName de um utilizador foi atualizado do Bernardo @**contoso.com** para bob @**fabrikam.com**, em que ambos **contoso.com** e **fabrikam.com** são **federado domínios**, em seguida, siga estes passos para corrigir o erro de sincronização

1. Atualizar o UserPrincipalName do utilizador no Azure AD de bob@contoso.com para bob@contoso.onmicrosoft.com. Pode utilizar o seguinte comando do PowerShell com o módulo Azure AD PowerShell:`Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Permitir que o próximo ciclo de sincronização para a tentativa de sincronização. Esta sincronização de hora será bem sucedida e irá atualizar o UserPrincipalName da Bernardo para bob@fabrikam.com conforme esperado.

#### <a name="related-articles"></a>Artigos relacionados
* [As alterações não são sincronizadas pela ferramenta de sincronização de diretórios do Azure Active Directory depois de alterar o UPN de uma conta de utilizador para utilizar um diferente domínio federado](https://support.microsoft.com/en-us/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Descrição
Quando um atributo excede o limite de tamanho permitido, o limite de comprimento ou o limite de contagem definido pelo esquema do Active Directory do Azure, a operação de sincronização resulta no **LargeObject** ou **ExceededAllowedLength**erro de sincronização. Normalmente, este erro ocorre para os seguintes atributos

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Cenários possíveis
1. Atributo de userCertificate do de Bob está a armazenar demasiados certificados atribuídos para Bernardo. Estes podem incluir certificados mais antigos, expirados. O limite de disco rígido é 15 certificados. Para obter mais informações sobre como processar erros LargeObject com o atributo userCertificate, consulte artigo [erros de processamento LargeObject causados por atributo userCertificate](active-directory-aadconnectsync-largeobjecterror-usercertificate.md).
2. Atributo de userSMIMECertificate do de Bob está a armazenar demasiados certificados atribuídos para Bernardo. Estes podem incluir certificados mais antigos, expirados. O limite de disco rígido é 15 certificados.
3. ThumbnailPhoto do de Bob definida no Active Directory é demasiado grande para ser sincronizada com êxito no Azure AD.
4. Durante a população automática do atributo ProxyAddresses do Active Directory, um objeto tem demasiadas ProxyAddresses atribuídos.

### <a name="how-to-fix"></a>Saber como corrigir
1. Certifique-se de que o atributo causando o erro está dentro de limitação permitida.

## <a name="related-links"></a>Ligações relacionadas
* [Localize os objetos do Active Directory no Centro de administração do Active Directory](https://technet.microsoft.com/library/dd560661.aspx)
* [Como consultar o Azure Active Directory para um objeto utilizando o Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
