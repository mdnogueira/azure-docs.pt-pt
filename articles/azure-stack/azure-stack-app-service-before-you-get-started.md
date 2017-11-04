---
title: "Antes de implementar o serviço de aplicações na pilha do Azure | Microsoft Docs"
description: "Passos para concluir antes de implementar o serviço de aplicações na pilha do Azure"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: anwestg
ms.openlocfilehash: f2e7b5b96b70333ae4ee92d24c354960008c7f00
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Antes de começar com o serviço de aplicações na pilha do Azure

App Service do Azure na pilha do Azure tem um número de passos dos pré-requisitos que devem ser concluídas antes da implementação:

- Transferir o App Service do Azure em Scripts de programa auxiliar de pilha do Azure
- Elevada disponibilidade
- Certificados necessários para o Azure App Service na pilha do Azure
- Preparar o servidor de ficheiros
- Preparar o servidor SQL
- Criar uma aplicação do Azure Active Directory
- Criar uma aplicação de serviços de Federação do Active Directory

## <a name="download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts"></a>Transferir o App Service do Azure no instalador da pilha do Azure e os Scripts de programa auxiliar

1. Transferir o [do serviço de aplicações em scripts de programa auxiliar de implementação do Azure pilha](https://aka.ms/appsvconmashelpers).
2. Transferir o [do serviço de aplicações no instalador do Azure pilha](https://aka.ms/appsvconmasinstaller).
3. Extraia os ficheiros a partir do ficheiro zip de scripts de programa auxiliar. Os seguintes ficheiros e a estrutura da pasta são apresentados:
  - Common.ps1
  - AADIdentityApp.ps1 criar
  - ADFSIdentityApp.ps1 criar
  - AppServiceCerts.ps1 criar
  - Get-AzureStackRootCert.ps1
  - Remover AppService.ps1
  - Módulos
    - GraphAPI.psm1
    
## <a name="high-availability"></a>Elevada disponibilidade

App Service do Azure na pilha do Azure não está atualmente capaz de oferecer elevada disponibilidade porque a pilha do Azure implementa apenas as cargas de trabalho para um único domínio de falhas.

Para preparar o serviço de aplicações do Azure na pilha do Azure para elevada disponibilidade, lembre-se de que implementar o servidor de ficheiros e SQL Server necessários numa configuração altamente disponível. Quando a pilha do Azure suporta vários domínios de falhas, iremos fornecem orientações sobre como ativar o serviço de aplicações do Azure na pilha do Azure numa configuração altamente disponível.


## <a name="certificates-required-for-azure-app-service-on-azure-stack"></a>Certificados necessários para o Azure App Service na pilha do Azure

### <a name="certificates-required-for-the-azure-stack-development-kit"></a>Certificados necessários para o Kit de desenvolvimento de pilha do Azure

Este script primeiro funciona com a autoridade de certificação de pilha do Azure para criar quatro certificados que são necessários pelo App Service.

| Nome de ficheiro | Utilizar |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | Certificado SSL do serviço de aplicações predefinido |
| API.appservice.local.azurestack.external.pfx | Certificado de SSL de API do serviço de aplicações |
| FTP.appservice.local.azurestack.external.pfx | Certificado SSL de publicador do serviço de aplicações |
| SSO.appservice.local.azurestack.external.pfx | Certificado de aplicação de identidade de serviço de aplicações |

Executar o script no anfitrião do Kit de desenvolvimento de pilha do Azure e certifique-se de que está a executar o PowerShell como azurestack\CloudAdmin.

1. Numa sessão do PowerShell em execução como azurestack\CloudAdmin, execute o script de criar AppServiceCerts.ps1 da pasta onde extraiu os scripts de programa auxiliar. O script cria quatro certificados na mesma pasta que o script de certificados de criar necessita do serviço de aplicações.
2. Introduza uma palavra-passe para proteger os ficheiros. pfx e tome nota do mesmo. Tem de a introduzir no serviço de aplicações no instalador da pilha do Azure.

#### <a name="create-appservicecertsps1-parameters"></a>Parâmetros de AppServiceCerts.ps1 criar

| Parâmetro | Necessários/opcionais | Valor predefinido | Descrição |
| --- | --- | --- | --- |
| PfxPassword | Necessário | Valor nulo | Palavra-passe utilizada para proteger a chave privada do certificado |
| domainName | Necessário | local.azurestack.external | Sufixo do Azure de região e o domínio de pilha |

### <a name="certificates-required-for-a-production-deployment-of-azure-app-service-on-azure-stack"></a>Certificados necessários para uma implementação de produção do serviço de aplicações do Azure na pilha do Azure

Operar o fornecedor de recursos na produção, tem de fornecer os seguintes quatro certificados:

#### <a name="default-domain-certificate"></a>Certificado de domínio predefinido

O certificado de domínio predefinido é colocado na função de Front-End. É utilizado por aplicações de utilizador para pedidos de domínio de caráter universal ou predefinição App Service do Azure. O certificado também é utilizado para operações de controlo de origem (KUDU).

O certificado tem de estar no formato. pfx e deve ser um certificado de caráter universal de dois requerente. Isto permite que o domínio predefinido e o ponto final de scm para operações de controlo de origem ser abrangida por um certificado.

| formato | Exemplo |
| --- | --- |
| \*.appservice. \<região\>.\< DomainName\>.\< extensão\> | \*. appservice.redmond.azurestack.external |
| \*. scm.appservice. <region>. <DomainName>.<extension> | \*. appservice.scm.redmond.azurestack.external |

#### <a name="api-certificate"></a>Certificado da API

O certificado da API é colocado na função de gestão e é utilizado pelo fornecedor de recursos para proteger as chamadas de api. O certificado para a publicação tem de conter um assunto que corresponde a entrada DNS de API:

| formato | Exemplo |
| --- | --- |
| API.appservice. \<região\>.\< DomainName\>.\< extensão\> | API.appservice.Redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Certificado de publicação

O certificado para a função de editor protege o tráfego FTPS para os proprietários da aplicação quando estes carregam conteúdo.  O certificado para a publicação tem de conter um assunto que corresponde a entrada FTPS DNS.

| formato | Exemplo |
| --- | --- |
| FTP.appservice. \<região\>.\< DomainName\>.\< extensão\> | API.appservice.Redmond.azurestack.external |

#### <a name="identity-certificate"></a>Certificado de identidade

Permite que o certificado para a aplicação de identidade:
- integração entre o diretório AAD/ADFS, a pilha do Azure e o serviço de aplicações para suportar a integração com o fornecedor de recursos de computação
- Início de sessão único cenários para ferramentas de programador avançadas no App Service do Azure na pilha do Azure.
O certificado para a identidade tem de conter um assunto que corresponde ao formato seguinte:

| formato | Exemplo |
| --- | --- |
| SSO.appservice. \<região\>.\< DomainName\>.\< extensão\> | SSO.appservice.Redmond.azurestack.external |

#### <a name="extract-the-azure-stack-azure-resource-manager-root-certificate"></a>Extraia o certificado de raiz do Gestor de recursos do Azure pilha do Azure

Numa sessão do PowerShell em execução como azurestack\CloudAdmin, execute o script Get-AzureStackRootCert.ps1 da pasta onde extraiu os scripts de programa auxiliar. O script cria quatro certificados na mesma pasta que o script de certificados de criar necessita do serviço de aplicações.

| Parâmetro de GET-AzureStackRootCert.ps1 | Necessários/opcionais | Valor predefinido | Descrição |
| --- | --- | --- | --- |
| PrivelegedEndpoint | Necessário | AzS ERCS01 | Ponto final com privilégios. |
| CloudAdminCredential | Necessário | AzureStack\CloudAdmin | Credencial de conta de domínio de administrador de nuvem de pilha do Azure |


## <a name="prepare-the-file-server"></a>Preparar o servidor de ficheiros

App Service do Azure requer a utilização de um servidor de ficheiros. Para implementações de produção, o servidor de ficheiros tem de ser configurado para elevada disponibilidade e capacidade de processamento de falhas.

Para utilização com apenas implementações do Kit de desenvolvimento de pilha do Azure, pode utilizar este exemplo do modelo de implementação Azure Resource Manager para implementar um servidor de ficheiros de nó único configurado: https://aka.ms/appsvconmasdkfstemplate.

### <a name="provision-groups-and-accounts-in-active-directory"></a>Aprovisionar grupos e contas no Active Directory

>[!NOTE]
> Execute todos os comandos seguintes, quando configurar o servidor de ficheiros, uma sessão de linha de comandos de administrador.  **Não utilize o PowerShell.**

1. Crie os seguintes grupos de segurança global do Active Directory:
    - FileShareOwners
    - FileShareUsers
2. Crie as seguintes contas do Active Directory como contas de serviço:
    - FileShareOwner
    - FileShareUser

    Como procedimento de segurança melhor prática, os utilizadores para estas contas (e para todas as funções da Web) deve ser diferentes entre si e ter nomes de utilizador forte e palavras-passe.
    Defina as palavras-passe com as seguintes condições:
     - Ativar **palavra-passe nunca expira**.
     - Ativar **utilizador não é possível alterar a palavra-passe**.
     - Desativar **o utilizador deve alterar a palavra-passe no próximo início de sessão**.
3. Adicione as contas para as associações da seguinte forma:
    - Adicionar **FileShareOwner** para o **FileShareOwners** grupo.
    - Adicionar **FileShareUser** para o **FileShareUsers** grupo.

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Aprovisionar grupos e contas num grupo de trabalho

No grupo de trabalho, execute net e comandos WMIC para aprovisionar grupos e contas.

1. Execute os seguintes comandos para criar as contas FileShareOwner e FileShareUser. Substitua <password> com os seus próprios valores.
``` DOS
net user FileShareOwner <password> /add /expires:never /passwordchg:no
net user FileShareUser <password> /add /expires:never /passwordchg:no
```
2. Defina as palavras-passe para as contas para nunca expirar, executando os comandos WMIC seguintes:
``` DOS
WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
```
3. Criar os grupos locais FileShareUsers e FileShareOwners e adicione as contas no primeiro passo aos mesmos.
``` DOS
net localgroup FileShareUsers /add
net localgroup FileShareUsers FileShareUser /add
net localgroup FileShareOwners /add
net localgroup FileShareOwners FileShareOwner /add
```

### <a name="provision-the-content-share"></a>Aprovisionar a partilha de conteúdo

A partilha de conteúdo contém conteúdo do web site de inquilino. O procedimento para aprovisionar a partilha de conteúdo num servidor de ficheiros única é o mesmo para ambientes do Active Directory e o grupo de trabalho, mas diferente para um cluster de ativação pós-falha no Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-ad-or-workgroup"></a>Aprovisionar a partilha de conteúdo num servidor único ficheiro (AD ou grupo de trabalho)

No servidor de ficheiros único, execute os seguintes comandos numa linha de comandos elevada. Substitua o valor para < C:\WebSites > os caminhos correspondentes no seu ambiente.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=<C:\WebSites>
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="to-enable-winrm-add-the-fileshareowners-group-to-the-local-administrators-group"></a>Para ativar o WinRM, adicione o grupo de FileShareOwners ao grupo de administradores local

Para a gestão remota do Windows funcionar corretamente, tem de adicionar o grupo de FileShareOwners ao grupo de administradores local.

#### <a name="active-directory"></a>Active Directory

Execute os seguintes comandos numa linha de comandos elevada no servidor de ficheiros ou em cada nó de Cluster de ativação pós-falha do servidor de ficheiros. Substitua o valor para <DOMAIN> com o nome de domínio que pretende utilizar.

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>Grupo de trabalho

Execute o seguinte comando numa linha de comandos elevada no servidor de ficheiros.

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>Configurar o controlo de acesso para as partilhas

Execute os seguintes comandos numa linha de comandos elevada no servidor de ficheiros ou no nó de Cluster de ativação pós-falha do servidor de ficheiros, que é o proprietário atual do recurso de cluster. Substitua os valores na italics com valores específicos ao seu ambiente.

#### <a name="active-directory"></a>Active Directory
```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=<C:\WebSites>
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Grupo de trabalho
```DOS
set WEBSITES_FOLDER=<C:\WebSites>
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server"></a>Preparar o servidor SQL

Para o App Service do Azure em bases de dados de medição e alojamento de pilha do Azure, tem de preparar um servidor de SQL para armazenar as bases de dados do serviço de aplicações do Azure.

Para utilizar com o Kit de desenvolvimento de pilha do Azure, pode utilizar o SQL Server Express 2014 SP2 ou posterior.

Para produção e fins de elevada disponibilidade, deve utilizar uma versão completa do SQL Server 2014 SP2 ou posterior, ativar a autenticação de modo misto e implementar uma [configuração altamente disponíveis](https://docs.microsoft.com/en-us/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

O App Service do Azure no servidor de SQL do Azure pilha tem de ser acessível a partir de todas as funções do serviço de aplicações. SQL Server pode ser implementado dentro da subscrição do fornecedor predefinida na pilha do Azure. Pode ainda utilizar a infraestrutura existente na sua organização (desde que está estabelecida conetividade à pilha do Azure).

Para qualquer uma das funções do SQL Server, pode utilizar uma instância predefinida ou numa instância nomeada. No entanto, se utilizar uma instância nomeada, não se esqueça de que manualmente iniciar o serviço do SQL Server Browser e abrir a porta 1434.

## <a name="create-an-azure-active-directory-application"></a>Criar uma aplicação do Azure Active Directory

Configure um principal de serviço do Azure AD para suportar o seguinte:
- Conjunto de dimensionamento de máquina virtual integração em camadas de trabalho.
- SSO para as ferramentas de Programador de portal e avançadas das funções do Azure.

Estes passos aplicam-se para o Azure AD protegido só a ambientes de pilha do Azure.

Os administradores tem de configurar o SSO para:
- Ative as ferramentas de programador avançadas no serviço de aplicações (Kudu).
- Ative a utilização da experiência de portal das funções do Azure.

Siga estes passos.

1. Abra uma instância do PowerShell como azurestack\cloudadmin.
2. Vá para a localização dos scripts transferiu e extraiu no [passo pré-requisitos](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Instalar a pilha do Azure PowerShell](azure-stack-powershell-install.md).
4. Execute o **criar AADIdentityApp.ps1** script. Quando lhe for pedida para o ID de inquilino do Azure AD, introduza o ID de inquilino do Azure AD que está a utilizar para a implementação de pilha do Azure, por exemplo, myazurestack.onmicrosoft.com.
5. No **credencial** janela, introduza a sua conta de administrador de serviço do Azure AD e a palavra-passe. Clique em **OK**.
6. Introduza o caminho do ficheiro de certificado e a palavra-passe de certificado para o [certificado que criou anteriormente](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). O certificado criado para este passo, por predefinição está sso.appservice.local.azurestack.external.pfx.
7. O script cria uma nova aplicação no inquilino do Azure AD. Anote o ID da aplicação que é devolvido na saída do PowerShell. Terá destas informações durante a instalação.
8. Abra uma nova janela do browser e inicie sessão no portal do Azure (portal.azure.com) como o **Admin de serviço do Azure Active Directory**.
9. Abra o fornecedor de recursos do Azure AD.
10. Clique em **registos de aplicação**.
11. Procure o **ID da aplicação** devolvidas como parte do passo 7. Uma aplicação de serviço de aplicações está listada.
12. Clique em **aplicação** na lista
13. Clique em **as permissões necessárias** > **conceder permissões** > **Sim**.

| Parâmetro AADIdentityApp.ps1 criar | Necessários/opcionais | Valor predefinido | Descrição |
| --- | --- | --- | --- |
| DirectoryTenantName | Necessário | Valor nulo | ID de inquilino do Azure AD Forneça o myazureaaddirectory.onmicrosoft.com GUID ou uma cadeia, por exemplo, |
| AdminArmEndpoint | Necessário | Valor nulo | O Azure Resource Manager ponto final de administração, por exemplo adminmanagement.local.azurestack.external |
| TenantARMEndpoint | Necessário | Valor nulo | Inquilino do Azure Resource Manager Endpoint, por exemplo: management.local.azurestack.external |
| AzureStackAdminCredential | Necessário | Valor nulo | Credencial de administrador de serviço do Azure AD |
| CertificateFilePath | Necessário | Valor nulo | Caminho para o ficheiro de certificado de aplicação de identidade gerado anteriormente. |
| CertificatePassword | Necessário | Valor nulo | Palavra-passe utilizada para proteger a chave privada do certificado. |

## <a name="create-an-active-directory-federation-services-application"></a>Criar uma aplicação de serviços de Federação do Active Directory

Para ambientes de pilha do Azure protegidos pelo AD FS, tem de configurar um principal de serviço do AD FS para suportar o seguinte:
- Conjunto de dimensionamento de máquina virtual integração em camadas de trabalho.
- SSO para as ferramentas de Programador de portal e avançadas das funções do Azure.

Os administradores precisam de configurar o SSO para:
- Configure um principal de serviço para a integração de conjunto de dimensionamento de máquina virtual em camadas de trabalho.
- Ative as ferramentas de programador avançadas no serviço de aplicações (Kudu).
- Ative a utilização da experiência de portal das funções do Azure.

Siga estes passos.

1. Abra uma instância do PowerShell como azurestack\azurestackadmin.
2. Vá para a localização dos scripts transferiu e extraiu no [passo pré-requisitos](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Instalar a pilha do Azure PowerShell](azure-stack-powershell-install.md).
4.  Execute o **criar ADFSIdentityApp.ps1** script.
5.  No **credencial** janela, introduza a sua conta de administrador de nuvem do AD FS e a palavra-passe. Clique em **OK**.
6.  Forneça o caminho do ficheiro de certificado e a palavra-passe de certificado para o [certificado que criou anteriormente](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). O certificado criado para este passo, por predefinição está sso.appservice.local.azurestack.external.pfx.

| Parâmetro ADFSIdentityApp.ps1 criar | Necessários/opcionais | Valor predefinido | Descrição |
| --- | --- | --- | --- |
| AdminArmEndpoint | Necessário | Valor nulo | O ponto final do Azure Resource Manager admin. Por exemplo, adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Necessário | Valor nulo | Ponto final com privilégios. Por exemplo, AzS ERCS01. |
| CloudAdminCredential | Necessário | Valor nulo | Azure pilha cloudadmin domínio credencial da conta. Por exemplo, Azurestack\CloudAdmin. |
| CertificateFilePath | Necessário | Valor nulo | Caminho do ficheiro PFX de certificado da aplicação de identidade. |
| CertificatePassword | Necessário | Valor nulo | Palavra-passe utilizada para proteger a chave privada do certificado. |


## <a name="next-steps"></a>Passos seguintes

[Instalar o fornecedor de recursos do serviço de aplicações](azure-stack-app-service-deploy.md).
