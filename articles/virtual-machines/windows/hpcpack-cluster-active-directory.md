---
title: Cluster HPC Pack no Azure Active Directory | Microsoft Docs
description: Saiba como integrar um cluster do Microsoft HPC Pack 2016 no Azure com o Azure Active Directory
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: jeconnoc
ms.assetid: 9edf9559-db02-438b-8268-a6cba7b5c8b7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 11/16/2017
ms.author: danlep
ms.openlocfilehash: bb0e878c4e987d111a535603cede25c639087ca7
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="manage-an-hpc-pack-cluster-in-azure-using-azure-active-directory"></a>Gerir um cluster HPC Pack no Azure utilizando o Azure Active Directory
[Microsoft HPC Pack 2016](https://technet.microsoft.com/library/cc514029) suporta a integração com [do Azure Active Directory](../../active-directory/index.md) (Azure AD) para os administradores que implementar um cluster HPC Pack no Azure.



Siga os passos neste artigo para as seguintes tarefas de nível elevadas: 
* Integrar manualmente o seu cluster HPC Pack com o seu inquilino do Azure AD
* Gerir e agendar tarefas no seu cluster HPC Pack no Azure 

Integrar uma solução de cluster HPC Pack com o Azure AD segue o padrão passos para integrar a outras aplicações e serviços. Este artigo pressupõe que está familiarizado com a gestão de utilizador básico no Azure AD. Para obter mais informações e em segundo plano, consulte o [documentação do Azure Active Directory](../../active-directory/index.md) e a secção seguinte.

## <a name="benefits-of-integration"></a>Vantagens da integração


Azure Active Directory (Azure AD) é um multi-inquilino baseado na nuvem diretório e identidade do serviço de gestão que fornece acesso início de sessão único (SSO) para soluções de nuvem.

Integração de um cluster HPC Pack com o Azure AD pode ajudar a atingir os objetivos seguintes:

* Remove o controlador de domínio do Active Directory tradicional do cluster HPC Pack. Isto pode ajudar a reduzir os custos de manutenção do cluster se isto não é necessário para o seu negócio e speed-up o processo de implementação.
* Tirar partido das vantagens seguintes que são colocadas pelo Azure AD:
    *   Início de sessão único 
    *   Utilizar uma identidade de AD local para o cluster HPC Pack no Azure 

    ![Ambiente do Active Directory do Azure](./media/hpcpack-cluster-active-directory/aad.png)


## <a name="prerequisites"></a>Pré-requisitos
* **Cluster HPC Pack 2016 implementado em máquinas virtuais do Azure** – para obter os passos, consulte [implementar um cluster HPC Pack 2016 no Azure](hpcpack-2016-cluster.md). Tem o nome DNS do nó principal e as credenciais de um administrador de cluster para concluir os passos neste artigo.

  > [!NOTE]
  > Integração do Active Directory do Azure não é suportada em versões do pacote HPC antes do HPC Pack 2016.



* **Computador cliente** -necessita de um computador cliente Windows ou Windows Server para executar utilitários de cliente de HPC Pack. Se apenas pretender utilizar o portal web do HPC Pack ou a REST API para submeter tarefas, pode utilizar qualquer computador cliente à sua escolha.

* **Utilitários de cliente de HPC Pack** -instale os utilitários de cliente HPC Pack no computador cliente, utilizando o pacote de instalação livre disponível do Microsoft Download Center.


## <a name="step-1-register-the-hpc-cluster-server-with-your-azure-ad-tenant"></a>Passo 1: Registar o servidor de cluster HPC no inquilino do Azure AD
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Se a sua conta dá-lhe acesso a mais do que um inquilino do Azure AD, clique em sua conta no canto superior direito. Em seguida, defina a sessão do portal para o inquilino pretendido. Tem de ter permissão para aceder a recursos no diretório. 
3. Clique em **do Azure Active Directory** no painel Serviços de navegação esquerdo, clique em **utilizadores e grupos**e certifique-se de que existem utilizador contas já criados ou configurado.
4. No **do Azure Active Directory**, clique em **registos de aplicação** > **novo registo de aplicação**. Introduza as seguintes informações:
    * **Nome** -HPCPackClusterServer
    * **Tipo de aplicação** - selecione **aplicação Web / API**
    * **Início de sessão URL**-o URL de base para o exemplo que está por predefinição`https://hpcserver`
    * Clique em **Criar**.
5. Depois da aplicação é adicionada, selecione-na **registos de aplicação** lista. Em seguida, clique em **definições** > **propriedades**. Introduza as seguintes informações:
    * Selecione **Sim** para **tenanted de várias**.
    * Alteração **URI de ID de aplicação** para `https://<Directory_name>/<application_name>`. Substitua `<Directory_name`> com o nome completo do seu inquilino do Azure AD, por exemplo, `hpclocal.onmicrosoft.com`e substitua `<application_name>` com o nome que escolheu anteriormente.
6. Clique em **Guardar**. Quando terminar de guardar, na página da aplicação, clique em **manifesto**. Edite o manifesto ao localizar o `appRoles` definição e adicionar a função de aplicação seguinte e, em seguida, clique em **guardar**:

  ```json
  "appRoles": [
     {
     "allowedMemberTypes": [
         "User",
         "Application"
     ],
     "displayName": "HpcAdminMirror",
     "id": "61e10148-16a8-432a-b86d-ef620c3e48ef",
     "isEnabled": true,
     "description": "HpcAdminMirror",
     "value": "HpcAdminMirror"
     },
     {
     "allowedMemberTypes": [
         "User",
         "Application"
     ],
     "description": "HpcUsers",
     "displayName": "HpcUsers",
     "id": "91e10148-16a8-432a-b86d-ef620c3e48ef",
     "isEnabled": true,
     "value": "HpcUsers"
     }
  ],
  ```
7. No **do Azure Active Directory**, clique em **aplicações empresariais** > **todas as aplicações**. Selecione **HPCPackClusterServer** da lista.
8. Clique em **propriedades**e alterar **atribuição de utilizador necessária** para **Sim**. Clique em **Guardar**.
9. Clique em **utilizadores e grupos** > **adicionar utilizador**. Selecione um utilizador, selecione uma função e, em seguida, clique em **atribuir**. Atribua uma das funções disponíveis (HpcUsers ou HpcAdminMirror) para o utilizador. Repita este passo com outros utilizadores no diretório. Para obter informações gerais sobre os utilizadores de cluster, consulte [gerir utilizadores do Cluster](https://technet.microsoft.com/library/ff919335(v=ws.11).aspx).


## <a name="step-2-register-the-hpc-cluster-client-with-your-azure-ad-tenant"></a>Passo 2: Registar o cliente de cluster HPC no inquilino do Azure AD

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Se a sua conta dá-lhe acesso a mais do que um inquilino do Azure AD, clique em sua conta no canto superior direito. Em seguida, defina a sessão do portal para o inquilino pretendido. Tem de ter permissão para aceder a recursos no diretório. 
3. No **do Azure Active Directory**, clique em **registos de aplicação** > **novo registo de aplicação**. Introduza as seguintes informações:

    * **Nome** -HPCPackClusterClient    
    * **Tipo de aplicação** - selecione **nativo**
    * **URI de redirecionamento** - `http://hpcclient`
    * Clique em **Criar**.

4. Depois da aplicação é adicionada, selecione-na **registos de aplicação** lista. Copiar o **ID da aplicação** valor e guarde-o. Poderá precisa deste mais tarde quando configurar a sua aplicação.

5. Clique em **definições** > **as permissões necessárias** > **adicionar** > **selecionar um API**. Procure e selecione a aplicação de HpcPackClusterServer (criada no passo 1).

6. No **ativar o acesso ao** página, selecione **acesso HpcClusterServer**. Em seguida, clique em **Guardar**.


## <a name="step-3-configure-the-hpc-cluster"></a>Passo 3: Configurar o cluster HPC

1. Ligar ao nó principal do HPC Pack 2016 no Azure.

2. Inicie o HPC PowerShell.

3. Execute o seguinte comando:

    ```powershell

    Set-HpcClusterRegistry -SupportAAD true -AADInstance https://login.microsoftonline.com/ -AADAppName HpcPackClusterServer -AADTenant <your AAD tenant name> -AADClientAppId <client ID> -AADClientAppRedirectUri http://hpcclient
    ```
    onde

    * `AADTenant`Especifica o nome de inquilino do Azure AD, como`hpclocal.onmicrosoft.com`
    * `AADClientAppId`Especifica o ID de aplicação para a aplicação que criou no passo 2.

4. Efetue um dos seguintes, dependendo da configuração do nó principal:

    * Num cluster HPC Pack único nó principal, reinicie o serviço de HpcScheduler.

    * Num cluster HPC Pack com vários nós principais, execute os seguintes comandos do PowerShell no nó principal para reiniciar o serviço de HpcSchedulerStateful:

    ```powershell
    Connect-ServiceFabricCluster

    Move-ServiceFabricPrimaryReplica –ServiceName "fabric:/HpcApplication/SchedulerStatefulService"

    ```

## <a name="step-4-manage-and-submit-jobs-from-the-client"></a>Passo 4: Gerir e submeter as tarefas do cliente

Para instalar os utilitários de cliente HPC Pack no seu computador, transfira os ficheiros de configuração de HPC Pack 2016 (instalação completa) do Microsoft Download Center. Quando iniciar a instalação, escolher a opção de configuração para o **utilitários de cliente de HPC Pack**.

Para preparar o computador cliente, instale o certificado utilizado durante [a configuração de cluster HPC](hpcpack-2016-cluster.md) no computador cliente. Utilize os procedimentos padrão da gestão de certificados do Windows para instalar o certificado público para o **certificados-utilizador atual** > **autoridades de certificação de raiz fidedigna** armazenar. 

Pode agora executar os comandos de HPC Pack ou utilize o Gestor de tarefa do pacote HPC GUI para submeter e gerir tarefas de cluster utilizando a conta do Azure AD. Para opções de submissão da tarefa, consulte [cluster HPC submeter as tarefas para um pacote HPC no Azure](hpcpack-cluster-submit-jobs.md#step-3-run-test-jobs-on-the-cluster).

> [!NOTE]
> Ao tentar ligar ao cluster HPC Pack no Azure pela primeira vez, é apresentada uma janela de pop-up. Introduza as credenciais do Azure AD para início de sessão. O token, em seguida, é colocado em cache. Ligações posteriores para o cluster no Azure utilizam o token em cache, a menos que as alterações de autenticação ou cache está desmarcada.
>
  
Por exemplo, depois de concluir os passos anteriores, pode consultar as tarefas a partir de um cliente no local da seguinte forma:

```powershell 
Get-HpcJob –State All –Scheduler https://<Azure load balancer DNS name> -Owner <Azure AD account>
```

## <a name="useful-cmdlets-for-job-submission-with-azure-ad-integration"></a>Cmdlets útil para submissão da tarefa com a integração do Azure AD 

### <a name="manage-the-local-token-cache"></a>Gerir a cache de token local

HPC Pack 2016 fornece os seguintes cmdlets do HPC PowerShell para gerir a cache de token local. Estes cmdlets são úteis para submeter tarefas de forma não interativa. Consulte o exemplo seguinte:

```powershell
Remove-HpcTokenCache

$SecurePassword = "<password>" | ConvertTo-SecureString -AsPlainText -Force

Set-HpcTokenCache -UserName <AADUsername> -Password $SecurePassword -scheduler https://<Azure load balancer DNS name> 
```

### <a name="set-the-credentials-for-submitting-jobs-using-the-azure-ad-account"></a>Defina as credenciais para submeter tarefas utilizando a conta do Azure AD 

Por vezes, poderá executar a tarefa sob o utilizador de cluster HPC (para um associado a um domínio cluster HPC, executar como utilizador de domínio; para um cluster HPC não associados ao domínio, executado como um utilizador local no nó principal).

1. Utilize os seguintes comandos para definir as credenciais:

    ```powershell
    $localUser = "<username>"

    $localUserPassword="<password>"

    $secpasswd = ConvertTo-SecureString $localUserPassword -AsPlainText -Force

    $mycreds = New-Object System.Management.Automation.PSCredential ($localUser, $secpasswd)

    Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name>
    ```

2. Em seguida, da seguinte forma a submeter a tarefa. A tarefa tarefa é executada em $localUser em nós de computação.

    ```powershell
    $emptycreds = New-Object System.Management.Automation.PSCredential ($localUser, (new-object System.Security.SecureString))
    ...
    $job = New-HpcJob –Scheduler https://<Azure load balancer DNS name>

    Add-HpcTask -Job $job -CommandLine "ping localhost" -Scheduler https://<Azure load balancer DNS name>

    Submit-HpcJob -Job $job -Scheduler https://<Azure load balancer DNS name> -Credential $emptycreds
    ```
    
   Se `–Credential` não for especificado com `Submit-HpcJob`, o trabalho ou tarefa, que é executado sob um utilizador local mapeado como a conta do Azure AD. (O cluster HPC cria um utilizador local com o mesmo nome que a conta do Azure AD para executar a tarefa.)
    
3. Definir os dados expandidos para a conta do Azure AD. Isto é útil ao executar uma tarefa MPI em nós do Linux com a conta do Azure AD.

   * Definir os dados expandidos para a conta do Azure AD

      ```powershell
      Set-HpcJobCredential -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data> -AadUser
      ```
      
   * Definir os dados expandidos e executadas como utilizador de cluster HPC
   
      ```powershell
      Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data>
      ```

