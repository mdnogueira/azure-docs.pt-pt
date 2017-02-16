---
title: "Serviços de Domínio do Azure AD: Ativar a sincronização de palavras-passe | Microsoft Docs"
description: "Introdução aos Serviços de Domínio do Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/20/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 9c43a831768684c6458d5f62557c8a06b2c5ca6b


---
# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Ativar a sincronização de palavras-passe para os Serviços de Domínio do Azure AD
Nas tarefas anteriores, ativou os Serviços de Domínio do Azure AD Domain Services para o seu inquilino do Azure AD. A próxima tarefa é ativar a sincronização de palavras-passe para os Serviços de Domínio do Azure AD. Quando a sincronização de credenciais estiver configurada, os utilizadores podem iniciar sessão no domínio gerido com as credenciais da empresa.

Os passos envolvidos diferem consoante a sua organização tenha um inquilino do Azure AD apenas na nuvem ou se estiver definido para sincronizar com o seu diretório no local com o Azure AD Connect.

<br>

> [!div class="op_single_selector"]
> * [Inquilino do Azure AD apenas na nuvem](active-directory-ds-getting-started-password-sync.md)
> * [Inquilino do Azure AD sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)
> 
> 

<br>

## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-synced-azure-ad-tenant"></a>Tarefa 5: Ativar a sincronização de palavras-passe para os Serviços de Domínio do AAD para um inquilino do Azure AD sincronizado
Um inquilino do Azure AD sincronizado é definido para sincronizar com o diretório local da sua organização com o Azure AD Connect. O Azure AD Connect não faz a sincronização de hashes de credenciais NTLM e Kerberos com o Azure AD por predefinição. Para utilizar os Serviços de Domínio do Azure AD, tem de configurar o Azure AD Connect para sincronizar os hashes de credenciais necessários para a autenticação NTLM e Kerberos. Os passos seguintes ativam a sincronização dos hashes de credenciais necessários com o seu inquilino do Azure AD.

### <a name="install-or-update-azure-ad-connect"></a>Instalar ou atualizar o Azure AD Connect
Instale a versão mais recente recomendada do Azure AD Connect num computador associado a um domínio. Se tiver uma instância existente do programa de configuração do Azure AD Connect, terá de atualizá-la para utilizar a versão mais recente do Azure AD Connect. Para evitar problemas/erros conhecidos que possam já ter sido corrigidos, certifique-se de que utiliza sempre a versão mais recente do Azure AD Connect.

**[Transferir o Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Versão recomendada: **1.1.281.0** - publicada em 7 de setembro de 2016.

> [!WARNING]
> TEM de instalar a versão mais recente recomendada do Azure AD Connect para permitir que as credenciais de palavra-passe legadas (necessárias para a autenticação NTLM e Kerberos) sejam sincronizadas com o seu inquilino do Azure AD. Esta funcionalidade não está disponível em versões anteriores do Azure AD Connect ou com a ferramenta DirSync legada.
> 
> 

Instruções de instalação para o Azure AD Connect estão disponíveis no seguinte artigo - [Introdução ao Azure AD Connect](../active-directory/active-directory-aadconnect.md)

### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Ativar a sincronização de hashes de credencial de NTLM e Kerberos para o Azure AD
Execute o seguinte script do PowerShell em cada floresta do AD para forçar a sincronização de palavras-passe completa e permitir a sincronização de todos os hashes de credenciais dos utilizadores no local com o seu inquilino do Azure AD. Este script permite que os hashes de credenciais necessários para a autenticação NTLM/Kerberos sejam sincronizados com o seu inquilino do Azure AD.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

Dependendo do tamanho do seu diretório (número de utilizadores, grupos, etc.), a sincronização de credenciais com o Azure AD é demorada. As palavras-passe poderão ser usadas no domínio gerido dos Serviços de Domínio do Azure AD pouco tempo depois dos hashes de credencial serem sincronizados para o Azure AD.

<br>

## <a name="related-content"></a>Conteúdo relacionado
* [Ativar a sincronização de palavras-passe para os Serviços de Domínio do AAD para um diretório do Azure AD apenas na nuvem](active-directory-ds-getting-started-password-sync.md)
* [Administrar um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Associar uma máquina virtual do Windows a um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Associar uma máquina virtual do Red Hat Enterprise Linux a um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-rhel-linux-vm.md)




<!--HONumber=Dec16_HO1-->


