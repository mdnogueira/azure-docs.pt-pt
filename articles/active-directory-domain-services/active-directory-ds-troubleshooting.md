---
title: "Serviços de domínio do Azure Active Directory: Guia de resolução de problemas | Microsoft Docs"
description: "Guia de resolução de problemas para os serviços de domínio do Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: maheshu
ms.openlocfilehash: 34335db77a5e414af4cfa77d6223ab5290bae614
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Serviços de domínio do Azure AD - guia de resolução de problemas
Este artigo fornece sugestões de resolução de problemas para problemas que poderão surgir quando configurar ou administração dos serviços de domínio do Azure Active Directory (AD).

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Não é possível ativar os serviços de domínio do Azure AD para o diretório do Azure AD
Esta secção ajuda-o a resolver erros quando tenta ativar os serviços de domínio do Azure AD para o seu diretório e este falha ou obtém tivada/desativada para 'Desativado'.

Escolha os passos de resolução de problemas que correspondem à mensagem de erro que ocorrerem.

| **Mensagem de erro** | **Resolução** |
| --- |:--- |
| *O nome contoso100.com já está a ser utilizado nesta rede. Especifique um nome que não esteja a ser utilizado.* |[Conflito de nomes de domínio na rede virtual](active-directory-ds-troubleshooting.md#domain-name-conflict) |
| *Não foi possível ativar os Serviços de Domínio neste inquilino do Azure AD. O serviço não tem as permissões adequadas para a aplicação designada “Sincronização do Azure AD Domain Services”. Elimine a aplicação designada “Sincronização do Azure AD Domain Services” e, em seguida, tente ativar os Serviços de Domínio do inquilino do Azure AD.* |[Serviços de domínio não tem permissões adequadas para a aplicação de sincronização de serviços de domínio do Azure AD](active-directory-ds-troubleshooting.md#inadequate-permissions) |
| *Não foi possível ativar os Serviços de Domínio neste inquilino do Azure AD. A aplicação Serviços de Domínio do seu inquilino do Azure AD não tem as permissões necessárias para ativar os Serviços de Domínio. Elimine a aplicação com o identificador de aplicação d87dcbc6-a371-462e-88e3-28ad15ec4e64 e, em seguida, tente ativar os Serviços de Domínio do seu inquilino do Azure AD.* |[A aplicação de serviços de domínio não está configurada corretamente no seu inquilino](active-directory-ds-troubleshooting.md#invalid-configuration) |
| *Não foi possível ativar os Serviços de Domínio neste inquilino do Azure AD. A aplicação do Microsoft Azure AD está desativada no seu inquilino do Azure AD. Ative a aplicação com o identificador de aplicação 00000002-0000-0000-c000-000000000000 e, em seguida, tente ativar os Serviços de Domínio do seu inquilino do Azure AD.* |[A aplicação Microsoft Graph está desativada no seu inquilino do Azure AD](active-directory-ds-troubleshooting.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Conflito de nomes de domínio
**Mensagem de erro:**

*O nome contoso100.com já está a ser utilizado nesta rede. Especifique um nome que não esteja a ser utilizado.*

**Remediação:**

Certifique-se de que não dispõe de um domínio existente com o mesmo nome de domínio disponível na rede virtual. Por exemplo, suponha que tem um domínio chamado 'contoso.com' já disponível na rede virtual selecionada. Posteriormente, tenta ativar um domínio gerido dos serviços de domínio do Azure AD com o mesmo nome de domínio (ou seja, ' contoso.com') na rede virtual. Detetar uma falha ao tentar ativar os serviços de domínio do Azure AD.

Esta falha é devido a conflitos de nome para o nome de domínio na rede virtual. Nesta situação, tem de utilizar um nome diferente para configurar o seu domínio gerido dos Serviços de Domínio do Azure AD. Em alternativa, pode anular o aprovisionamento do domínio existente e, em seguida, prosseguir para ativar os Serviços de Domínio do Azure AD.

### <a name="inadequate-permissions"></a>Permissões inadequadas
**Mensagem de erro:**

*Não foi possível ativar os Serviços de Domínio neste inquilino do Azure AD. O serviço não tem as permissões adequadas para a aplicação designada “Sincronização do Azure AD Domain Services”. Elimine a aplicação designada “Sincronização do Azure AD Domain Services” e, em seguida, tente ativar os Serviços de Domínio do inquilino do Azure AD.*

**Remediação:**

Verifique se é uma aplicação com o nome 'Sync de serviços de domínio do Azure AD' no diretório do Azure AD. Se esta aplicação existir, elimine-o e, em seguida, reativar os serviços de domínio do Azure AD.

Execute os seguintes passos para verificar a presença da aplicação e eliminá-la, se existir a aplicação:

1. Navegue para o **Portal Clássico do Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Selecione o nó **Active Directory** no painel da esquerda.
3. Selecione o inquilino do Azure AD (diretório) para o qual gostaria de ativar os Serviços de Domínio do Azure AD.
4. Navegue para o **aplicações** separador.
5. Selecione o **aplicações minha empresa detém** opção na lista pendente.
6. Verifique a existência de uma aplicação de chamada **sincronização de serviços de domínio do Azure AD**. Se a aplicação, avance para eliminá-lo.
7. Depois de ter eliminado a aplicação, tente ativar novamente os serviços de domínio do Azure AD.

### <a name="invalid-configuration"></a>Configuração inválida
**Mensagem de erro:**

*Não foi possível ativar os Serviços de Domínio neste inquilino do Azure AD. A aplicação Serviços de Domínio do seu inquilino do Azure AD não tem as permissões necessárias para ativar os Serviços de Domínio. Elimine a aplicação com o identificador de aplicação d87dcbc6-a371-462e-88e3-28ad15ec4e64 e, em seguida, tente ativar os Serviços de Domínio do seu inquilino do Azure AD.*

**Remediação:**

Verifique se tem uma aplicação com o nome AzureActiveDirectoryDomainControllerServices (com um identificador de aplicação de d87dcbc6-a371-462e-88e3-28ad15ec4e64) no diretório do Azure AD. Se esta aplicação, terá de eliminar e, em seguida, reativar os serviços de domínio do Azure AD.

Utilize o seguinte script do PowerShell para localizar a aplicação e eliminá-lo.

> [!NOTE]
> Este script utiliza **versão 2 do Azure AD PowerShell** cmdlets. Para ver uma lista completa de todos os cmdlets disponíveis e para transferir o módulo, leia o [documentação de referência do AzureAD PowerShell](https://msdn.microsoft.com/library/azure/mt757189.aspx).
>
>

```
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph desativada
**Mensagem de erro:**

Não foi possível ativar os serviços de domínio neste inquilino do Azure AD. A aplicação do Microsoft Azure AD está desativada no seu inquilino do Azure AD. Ativar a aplicação com o 00000002-0000-0000-c000-000000000000 de identificador de aplicação e, em seguida, tente ativar os serviços de domínio para o seu inquilino do Azure AD.

**Remediação:**

Verifique se tiver desativado a uma aplicação com o identificador 00000002-0000-0000-c000-000000000000. Esta aplicação é a aplicação do Microsoft Azure AD e fornece acesso à API de gráfico com o seu inquilino do Azure AD. Serviços de domínio do AD do Azure tem de estar ativado para sincronizar o seu inquilino do Azure AD para o seu domínio gerido esta aplicação.

Para resolver este erro, ative esta aplicação e, em seguida, tente ativar os serviços de domínio para o seu inquilino do Azure AD.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Os utilizadores não conseguem iniciar sessão no domínio gerido pelo Azure AD Domain Services
Se um ou mais utilizadores no seu inquilino do Azure AD não conseguem iniciar sessão no domínio gerido criado recentemente, execute os seguintes passos de resolução de problemas:

* **Inicie sessão com o formato UPN:** tentar iniciar sessão utilizando o formato UPN (por exemplo, 'joeuser@contoso.com') em vez do formato de SAMAccountName ('CONTOSO\joeuser'). De SAMAccountName pode ser gerado automaticamente para os utilizadores cujo prefixo UPN é demasiado longa ou é igual a outro utilizador no domínio gerido. O formato UPN é garantido ser exclusivo dentro de um inquilino do Azure AD.

> [!NOTE]
> Recomendamos que utilize o formato UPN para iniciar sessão no domínio gerido dos serviços de domínio do Azure AD.
>
>

* Certifique-se de que [ativou a sincronização de palavras-passe](active-directory-ds-getting-started-password-sync.md), em conformidade com os passos descritos no Guia de Introdução.
* **Contas externas:** Certifique-se de que a conta de utilizador afectado não é uma conta externa no inquilino do Azure AD. Contas externas exemplos de contas Microsoft (por exemplo, 'joe@live.com') ou contas de utilizador de um externo diretório do Azure AD. Uma vez que os serviços de domínio do Azure AD não tem as credenciais para essas contas de utilizador, estes utilizadores não é possível iniciar sessão no domínio gerido.
* **Sincronizar contas:** se as contas de utilizador afectado são sincronizadas a partir de um diretório no local, certifique-se de que:

  * Ter implementado ou atualizado para o [mais recente recomendado versão do Azure AD Connect](https://www.microsoft.com/en-us/download/details.aspx?id=47594).
  * A configuração do Azure AD Connect para [efetuar uma sincronização completa](active-directory-ds-getting-started-password-sync.md).
  * Dependendo do tamanho do seu diretório, este poderá demorar algum tempo para contas de utilizador e para estar disponível nos serviços de domínio do Azure AD os hashes de credencial. Certifique-se de que aguardar suficientemente longa antes de repetir a autenticação.
  * Se o problema persistir depois de verificar os passos anteriores, experimente reiniciar o serviço do Microsoft Azure AD Sync. Do seu computador de sincronização, inicie uma linha de comandos e execute os seguintes comandos:

    1. net stop 'Microsoft Azure AD Sync'
    2. net start 'Microsoft Azure AD Sync'
* **Contas apenas na nuvem**: se a conta de utilizador afectado é uma conta de utilizador apenas na nuvem, certifique-se de que o utilizador foi alterado a palavra-passe depois de ativado os serviços de domínio do Azure AD. Este passo faz com que sejam gerados os hashes de credencial necessários para o Azure AD Domain Services.

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Utilizadores removidos do seu inquilino do Azure AD não são removidos do seu domínio gerido
O Azure AD impede a eliminação acidental de objetos de utilizador. Quando elimina uma conta de utilizador do inquilino do Azure AD, o objeto de utilizador correspondente é movido para a Reciclagem. Quando esta operação de eliminação é sincronizada com o seu domínio gerido, faz com que a conta de utilizador correspondente ser marcado como desativado. Esta funcionalidade ajuda-o a recuperar ou a conta de utilizador de anular eliminação mais tarde.

A conta de utilizador permanece no estado desativado no seu domínio gerido, mesmo voltar a criar uma conta de utilizador com o mesmo UPN no diretório do Azure AD. Para remover a conta de utilizador do seu domínio gerido, tem de forçar eliminá-la a partir do seu inquilino do Azure AD.

Para remover completamente a conta de utilizador do seu domínio gerido, elimine permanentemente o utilizador do seu inquilino do Azure AD. Utilize o cmdlet Remove-MsolUser PowerShell com a opção “-RemoveFromRecycleBin”, conforme descrito neste [artigo da MSDN](https://msdn.microsoft.com/library/azure/dn194132.aspx).

## <a name="contact-us"></a>Contacte-nos
Contacte a equipa de produto do Azure Active Directory Domain Services para [partilhar comentários ou para suporte](active-directory-ds-contact-us.md).
