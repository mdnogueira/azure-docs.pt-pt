<properties
    pageTitle="Serviços de Domínio do Azure AD: Ativar a sincronização de palavras-passe | Microsoft Azure"
    description="Introdução aos Serviços de Domínio do Azure Active Directory"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/06/2016"
    ms.author="maheshu"/>

# Serviços de Domínio do Azure AD *(Pré-visualização)* – Ativar a sincronização de palavras-passe para os Serviços de Domínio do Azure AD

## Tarefa 5: Ativar a sincronização de palavras-passe para os Serviços de Domínio do AAD para um inquilino do Azure AD sincronizado
Assim que tiver ativado os Serviços de Domínio do Azure AD para o diretório do Azure AD, a tarefa seguinte consiste em ativar a sincronização de palavras-passe para os Serviços de Domínio do Azure AD. Isto permite aos utilizadores iniciar sessão no domínio utilizando as respetivas credenciais empresariais.

Os passos envolvidos são diferentes, com base se a sua organização tem um diretório do Azure AD apenas na nuvem ou se está definida para sincronizar com o seu diretório no local com o Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Diretório do Azure AD apenas na nuvem](active-directory-ds-getting-started-password-sync.md)
- [Diretório do Azure AD sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### Inquilinos sincronizados – Ativar a sincronização de hashes de credencial de NTLM e Kerberos para o Azure AD
Se o inquilino do Azure AD para a sua organização estiver definido para sincronizar com o seu diretório no local com o Azure AD Connect, terá de configurar o Azure AD Connect, para sincronizar os hashes de credencial necessários para a autenticação NTLM e Kerberos. Estes hashes não estão sincronizados para o Azure AD por predefinição e os passos seguintes irão permitir-lhe ativar a sincronização dos hashes para o seu inquilino do Azure AD.

#### Instalar ou atualizar o Azure AD Connect

Terá de instalar a versão mais recente recomendada do Azure AD Connect num computador associado a um domínio. Se tiver uma instância existente do programa de configuração do Azure AD Connect, terá de atualizá-la para utilizar a compilação do Azure AD Connect GA. Certifique-se de que utiliza a versão mais recente do Azure AD Connect, para evitar problemas/erros conhecidos que já foram corrigidos.

**[Transferir o Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Versão recomendada: **1.1.189.0** - publicada em 3 de junho de 2016.

  > [AZURE.WARNING] TEM de instalar a versão mais recente recomendada do Azure AD Connect para permitir que as credenciais de palavra-passe legadas (necessárias para a autenticação NTLM e Kerberos) para sincronizar com o seu inquilino do Azure AD. Esta funcionalidade não está disponível em versões anteriores do Azure AD Connect ou com a ferramenta DirSync legada.

Instruções de instalação para o Azure AD Connect estão disponíveis no seguinte artigo - [Introdução ao Azure AD Connect](../active-directory/active-directory-aadconnect.md)


#### Forçar a sincronização de palavras-passe completa para o Azure AD

Para forçar a sincronização de palavras-passe completa e ativar os hashes de palavras-passe de todos os utilizadores no local (incluindo os hashes de credencial necessários para a autenticação NTLM/Kerberos) para sincronizar com o seu inquilino do Azure AD, execute o seguinte script do PowerShell em cada floresta do AD.

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

Dependendo do tamanho do seu diretório (número de utilizadores, grupos, etc.), a sincronização de credenciais para o Azure AD irá demorar. As palavras-passe poderão ser usadas no domínio gerido dos Serviços de Domínio do Azure AD pouco tempo depois dos hashes de credencial serem sincronizados para o Azure AD.


<br>

## Conteúdo relacionado

- [Ativar a sincronização de palavras-passe para os Serviços de Domínio do AAD para um diretório do Azure AD apenas na nuvem](active-directory-ds-getting-started-password-sync.md)

- [Administrar um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)

- [Associar uma máquina virtual do Windows a um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)

- [Associar uma máquina virtual do Red Hat Enterprise Linux a um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-rhel-linux-vm.md)



<!--HONumber=Aug16_HO1-->


