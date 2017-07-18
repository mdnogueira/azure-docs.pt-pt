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
ms.date: 06/30/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 947ea3c9d789ecf5a754001aafcda6f8bcd41047
ms.contentlocale: pt-pt
ms.lasthandoff: 07/08/2017


---
# <a name="enable-password-synchronization-to-azure-active-directory-domain-services"></a>Ativar a sincronização de palavras-passe para o Azure Active Directory Domain Services
Em tarefas anteriores, ativou o Azure Active Directory Domain Services do seu inquilino do Azure Active Directory (Azure AD). A tarefa seguinte consiste em ativar a sincronização de hashes de credenciais necessários para a autenticação NTLM (NT LAN Manager) e Kerberos para sincronizar com os Serviços de Domínio do Azure AD. Assim que a sincronização de credenciais estiver configurada, os utilizadores podem iniciar sessão no domínio gerido com as credenciais da empresa.

Os passos envolvidos são diferentes para contas de utilizador apenas na cloud vs contas de utilizador que são sincronizadas a partir do seu diretório no local com o Azure AD Connect. Se o seu inquilino do Azure AD tiver uma combinação de utilizadores apenas na cloud e utilizadores do seu AD no local, tem de executar os dois passos.

<br>

> [!div class="op_single_selector"]
> * **Contas de utilizador apenas na cloud**: [sincronizar as palavras-passe para contas de utilizador apenas na cloud com o seu domínio gerido](active-directory-ds-getting-started-password-sync.md)
> * **Contas de utilizador no local**: [sincronizar as palavras-passe para contas de utilizador sincronizadas a partir do seu AD no local com o seu domínio gerido](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

<br>

## <a name="task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad"></a>Tarefa 5: ativar a sincronização de palavras-passe do seu domínio gerido para contas de utilizador sincronizadas com o AD no local
Um inquilino do Azure AD sincronizado é definido para sincronizar com o diretório local da sua organização com o Azure AD Connect. Por predefinição, o Azure AD Connect não faz a sincronização de hashes de credenciais NTLM e Kerberos com o Azure AD. Para utilizar os Serviços de Domínio do Azure AD, tem de configurar o Azure AD Connect para sincronizar os hashes de credenciais necessários para a autenticação NTLM e Kerberos. Os passos seguintes ativam a sincronização dos hashes de credenciais necessários do seu diretório no local com o seu inquilino do Azure AD.

> [!NOTE]
> Se a organização tiver contas de utilizador que são sincronizadas a partir do seu diretório no local, tem de ativar a sincronização de hashes NTLM e Kerberos para poder utilizar o domínio gerido. Uma conta de utilizador sincronizada é uma conta que foi criada no seu diretório no local e está sincronizada com o seu inquilino do Azure AD através do Azure AD Connect.
>
>

### <a name="install-or-update-azure-ad-connect"></a>Instalar ou atualizar o Azure AD Connect
Instale a versão mais recente recomendada do Azure AD Connect num computador associado a um domínio. Se tiver uma instância existente do programa de configuração do Azure AD Connect, terá de atualizá-la para utilizar a versão mais recente do Azure AD Connect. Para evitar problemas/erros conhecidos que possam já ter sido corrigidos, certifique-se de que utiliza sempre a versão mais recente do Azure AD Connect.

**[Transferir o Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Versão recomendada: **1.1.553.0** - publicada em 27 de junho de 2017.

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

