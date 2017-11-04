---
title: Submeter tarefas para um pacote HPC de cluster no Azure | Microsoft Docs
description: Saiba como configurar um computador no local para submeter as tarefas para um cluster HPC Pack no Azure
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 78f6833c-4aa6-4b3e-be71-97201abb4721
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 10/14/2016
ms.author: danlep
ms.openlocfilehash: d5953f1e1dd2deb4d871bd67352a6a5b2ae13dbf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>Submeter tarefas HPC de um computador no local para um cluster HPC Pack implementado no Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Configurar um computador de cliente no local para submeter tarefas a um [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) cluster no Azure. Este artigo mostra como configurar um computador local com ferramentas de cliente ao submeter a tarefa através de HTTPS para o cluster no Azure. Desta forma, vários utilizadores do cluster podem submeter tarefas ao cluster HPC Pack baseado na nuvem, mas sem ligar diretamente ao nó principal do VM ou aceder a uma subscrição do Azure.

![Submeter uma tarefa para um cluster no Azure][jobsubmit]

## <a name="prerequisites"></a>Pré-requisitos
* **Nó principal HPC Pack implementado na VM do Azure** -é recomendável que utilize ferramentas automatizadas, tais como um [modelo de início rápido do Azure](https://azure.microsoft.com/documentation/templates/) ou um [script do PowerShell do Azure](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) para implementar o nó principal e cluster. Tem o nome DNS do nó principal e as credenciais de um administrador de cluster para concluir os passos neste artigo.
* **Computador cliente** -necessita de um computador cliente Windows ou Windows Server que pode executar utilitários de cliente de HPC Pack (consulte [requisitos de sistema](https://technet.microsoft.com/library/dn535781.aspx)). Se apenas pretender utilizar o portal web do HPC Pack ou a REST API para submeter tarefas, pode utilizar qualquer computador cliente à sua escolha.
* **Suporte de instalação do HPC Pack** - para instalar os utilitários de cliente de HPC Pack, o pacote de instalação livre para a versão mais recente do HPC Pack (pacote HPC 2012 R2) está disponível a partir do [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=328024). Certifique-se de que transfira a mesma versão do pacote HPC que está instalado no nó principal do VM.

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>Passo 1: Instalar e configurar os componentes web no nó principal
Para ativar uma interface REST submeter tarefas ao cluster através de HTTPS, certifique-se de que os componentes web do HPC Pack são configurados no nó principal do HPC Pack. Se ainda não estiverem instalados, primeiro de instalar os componentes web executando o ficheiro de instalação HpcWebComponents.msi. Em seguida, configure os componentes executando o script do HPC PowerShell **conjunto HPCWebComponents.ps1**.

Para obter procedimentos detalhados, consulte [instalar os componentes Web do Microsoft HPC Pack](http://technet.microsoft.com/library/hh314627.aspx).

> [!TIP]
> Determinados modelos de início rápido do Azure para HPC Pack instalar e configurar os componentes web automaticamente. Se utilizar o [script de implementação de HPC Pack IaaS](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) para criar o cluster, opcionalmente, pode instalar e configurar os componentes web como parte da implementação.
> 
> 

**Para instalar os componentes web**

1. Liga ao nó principal do VM utilizando as credenciais de um administrador de cluster.
2. A partir da pasta de configuração de pacote HPC, execute HpcWebComponents.msi no nó principal.
3. Siga os passos no Assistente para instalar os componentes web

**Para configurar os componentes web**

1. No nó principal, inicie o HPC PowerShell como administrador.
2. Para alterar o diretório para a localização do script de configuração, escreva o seguinte comando:
   
    ```powershell
    cd $env:CCP_HOME\bin
    ```
3. Para configurar a interface REST e iniciar o serviço Web de HPC, escreva o seguinte comando:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```
4. Quando lhe for pedido para selecionar um certificado, escolha o certificado que corresponde ao nome DNS público do nó principal. Por exemplo, se implementar nó principal do VM utilizando o modelo de implementação clássica, o nome do certificado aspeto CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net. Se utilizar o modelo de implementação Resource Manager, o nome do certificado aspeto CN =&lt;*HeadNodeDnsName*&gt;.&lt; *região*&gt;. cloudapp.azure.com.
   
   > [!NOTE]
   > Selecione este certificado mais tarde quando submeter tarefas ao nó principal de um computador local. Não selecione ou configurar um certificado que corresponde ao nome do computador de nó principal no domínio do Active Directory (por exemplo, CN =*MyHPCHeadNode.HpcAzure.local*).
   > 
   > 
5. Para configurar o portal web para submissão da tarefa, escreva o seguinte comando:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. Após a conclusão do script, pare e reinicie o serviço de agendador de trabalho HPC, escrevendo os seguintes comandos:
   
    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>Passo 2: Instalar os utilitários de cliente HPC Pack num computador local
Se pretende instalar os utilitários de cliente HPC Pack no seu computador, transferir os ficheiros de configuração de HPC Pack (instalação completa) a partir de [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=328024). Quando iniciar a instalação, escolher a opção de configuração para o **utilitários de cliente de HPC Pack**.

Para utilizar as ferramentas de cliente do pacote HPC para submeter tarefas ao nó principal do VM, também terá de exportar um certificado a partir do nó principal e instalá-lo no computador cliente. O certificado necessário. Formato CER.

**Para exportar o certificado do nó principal**

1. No nó principal, adicione o snap-in de certificados para uma consola de gestão da Microsoft para a conta de computador Local. Para obter os passos adicionar o snap-in, consulte [adicionar o Snap-in de certificados para uma MMC](https://technet.microsoft.com/library/cc754431.aspx).
2. Na árvore da consola, expanda **certificados-Computador Local** > **pessoais**e, em seguida, clique em **certificados**.
3. Localize o certificado que foram configurados para os componentes web HPC Pack [passo 1: instalar e configurar os componentes web no nó principal](#step-1:-install-and-configure-the-web-components-on-the-head-node) (por exemplo, CN =&lt;*HeadNodeDnsName* &gt;. cloudapp.net).
4. O certificado com o botão direito e clique em **todas as tarefas** > **exportar**.
5. No Assistente para exportar certificados, clique em **seguinte**e certifique-se de que **não exportar a chave privada** está selecionada.
6. Siga os passos restantes do Assistente para exportar o certificado no codificado DER x. 509 binário (. Formato CER).

**Para importar o certificado no computador cliente**

1. Copie o certificado que exportou do nó principal para uma pasta no computador cliente.
2. No computador cliente, execute certmgr.msc.
3. No Gestor de certificados, expanda **certificados-utilizador atual** > **autoridades de certificação de raiz fidedigna**, faça duplo clique **certificados**e, em seguida, Clique em **todas as tarefas** > **importação**.
4. No Assistente para importar certificados, clique em **seguinte** e siga os passos para importar o certificado que exportou do nó principal para o arquivo de autoridades de certificação de raiz fidedigna.

> [!TIP]
> Poderá ver um aviso de segurança, porque a autoridade de certificação no nó principal não é reconhecida pelo computador cliente. Para fins de teste, pode ignorar este aviso e concluir a importação de certificados.
> 
> 

## <a name="step-3-run-test-jobs-on-the-cluster"></a>Passo 3: Executar tarefas de teste no cluster
Para verificar a configuração, tente tarefas em execução no cluster do Azure do computador local. Por exemplo, pode utilizar ferramentas de HPC Pack GUI ou comandos da linha de comandos para submeter tarefas ao cluster. Também pode utilizar um portal baseado na web para submeter tarefas.

**Para executar comandos de submissão de tarefa no computador cliente**

1. Num computador cliente onde estão instalados os utilitários de cliente HPC Pack, inicie uma linha de comandos.
2. Escreva um comando de exemplo. Por exemplo, para listar todas as tarefas no cluster, escreva um comando semelhante a um dos seguintes, dependendo o nome DNS completo do nó principal:
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
   
    ou
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```
   
   > [!TIP]
   > Utilize o nome DNS completo de nó principal, não é o endereço IP, o URL de programador. Se especificar o endereço IP, aparece um erro semelhante a "o certificado de servidor tem de ter uma cadeia de confiança válida ou ser colocados no arquivo de raiz fidedigna."
   > 
   > 
3. Quando lhe for pedido, escreva o nome de utilizador (no formato &lt;DomainName&gt;\\&lt;UserName&gt;) e palavra-passe de administrador de cluster HPC ou outro utilizador de cluster que configurou. Pode optar por armazenar localmente as credenciais mais operações de tarefa.
   
    É apresentada uma lista de tarefas.

**Para utilizar o Gestor de tarefas HPC no computador cliente**

1. Se não armazenar as credenciais do domínio para um utilizador de cluster anteriormente quando submete uma tarefa, pode adicionar as credenciais no Gestor de credenciais.
   
    a. No painel de controlo do computador cliente, inicie o Gestor de credenciais.
   
    b. Clique em **credenciais do Windows** > **adicionar uma credencial genérica**.
   
    c. Especifique o endereço Internet (por exemplo, https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler ou https://&lt;HeadNodeDnsName&gt;.&lt; região&gt;.cloudapp.azure.com/HpcScheduler) e o nome de utilizador (&lt;DomainName&gt;\\&lt;UserName&gt;) e palavra-passe de administrador do cluster ou outro utilizador de cluster que configurou.
2. No computador cliente, inicie o Gestor de tarefas HPC.
3. No **selecionar nó Head** caixa de diálogo, introduza o URL para o nó principal no Azure (por exemplo, https://&lt;HeadNodeDnsName&gt;. cloudapp.net ou https://&lt;HeadNodeDnsName&gt;.&lt; região&gt;. cloudapp.azure.com).
   
    Gestor de tarefas HPC abre-se e mostra uma lista de tarefas no nó principal.

**Para utilizar o portal web em execução no nó principal**

1. Iniciar um browser no computador cliente e introduza um dos seguintes endereços, consoante o nome DNS completo do nó principal:
   
    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
   
    ou
   
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. Na caixa de diálogo segurança que aparece, escreva as credenciais de domínio de administrador de cluster HPC. (Também pode adicionar outros utilizadores de cluster em diferentes funções. Consulte [gestão de utilizadores de Cluster](https://technet.microsoft.com/library/ff919335.aspx).)
   
    O portal web abre-se para a vista de lista de tarefas.
3. Para submeter uma tarefa de exemplo que devolve a cadeia "Olá mundo" do cluster, clique em **nova tarefa** no painel de navegação esquerdo.
4. No **nova tarefa** página **de páginas de submissão**, clique em **Olámundo**. É apresentada a página de submissão da tarefa.
5. Clique em **submeter**. Se lhe for solicitado, forneça as credenciais de domínio de administrador de cluster HPC. A tarefa é submetida e o ID da tarefa é apresentado no **tarefas My** página.
6. Para ver os resultados da tarefa que é submetido, clique o ID da tarefa e, em seguida, clique em **tarefas vista** para ver o resultado do comando (em **saída**).

## <a name="next-steps"></a>Passos seguintes
* Também pode submeter tarefas ao cluster do Azure com o [API de REST do HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).
* Se pretende submeter as tarefas de cluster de um cliente Linux, consulte o exemplo de Python no [SDK do HPC Pack 2012 R2 e o código de exemplo](https://www.microsoft.com/download/details.aspx?id=41633).

<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png
