---
title: Criar um pipeline de CI/CD no Azure com o Team Services | Microsoft Docs
description: "Saiba como criar um pipeline do Visual Studio Team Services para a integração contínua e entrega que implementa uma aplicação web IIS numa VM do Windows"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/12/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 006cac5606c411c9d86b36d0069021094fcdb1db
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="create-a-continuous-integration-pipeline-with-visual-studio-team-services-and-iis"></a>Criar um pipeline de integração contínua com o Visual Studio Team Services e o IIS
Para automatizar a criação, teste e fases de implementação de desenvolvimento de aplicações, pode utilizar uma integração contínua e o pipeline de implementação (CI/CD). Neste tutorial, vai criar um pipeline de CI/CD com o Visual Studio Team Services e uma máquina virtual (VM) do Windows no Azure que executa o IIS. Saiba como:

> [!div class="checklist"]
> * Publicar uma aplicação web do ASP.NET para um projeto de equipa de serviços
> * Criar uma definição de compilação que é acionada pela consolidações de código
> * Instalar e configurar o IIS numa máquina virtual no Azure
> * Adicionar a instância do IIS a um grupo de implementação nos serviços de equipa
> * Criar uma versão definição para publicar o novo web implementar pacotes em IIS
> * Testar o pipeline de CI/CD

Este tutorial requer a versão do módulo 3.6 ou posterior do Azure PowerShell. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-azurerm-ps).


## <a name="create-project-in-team-services"></a>Criar projeto na equipa de serviços
Visual Studio Team Services permite a fácil colaboração e desenvolvimento sem manter uma solução de gestão de código no local. Serviços da equipa fornece o teste de código de nuvem, compilação e o application insights. Pode escolher um repositório de controlo de versão e IDE que melhor se adequa o desenvolvimento de código. Para este tutorial, pode utilizar uma conta gratuita para criar uma aplicação básica de web do ASP.NET e o pipeline de CI/CD. Se ainda não tiver uma conta de serviços de equipa, [criar um](http://go.microsoft.com/fwlink/?LinkId=307137).

Para gerir o processo de consolidação de código, definições de compilação e definições de versão, crie um projeto na equipa de serviços da seguinte forma:

1. Abra o dashboard de serviços da equipa num web browser e escolha **novo projeto**.
2. Introduza *myWebApp* para o **nome do projeto**. Deixe todos os outros valores predefinidos para utilizar *Git* controlo de versão e *Agile* processo do item de trabalho.
3. Escolha a opção de **partilhar com** *os membros da equipa*, em seguida, selecione **criar**.
5. Depois de criar o projeto, escolha a opção de **inicializar com um ficheiro Leia-me ou gitignore**, em seguida, **inicializar**.
6. No interior do seu novo projeto, escolha **Dashboards** na parte superior, em seguida, selecione **abrir no Visual Studio**.


## <a name="create-aspnet-web-application"></a>Criar uma aplicação web ASP.NET
No passo anterior, criou um projeto de equipa de serviços. O passo final é aberto o seu novo projeto no Visual Studio. Gerir o seu consolidações de código no **equipa Explorer** janela. Criar uma cópia local do seu novo projeto, em seguida, criar uma aplicação web ASP.NET a partir de um modelo da seguinte forma:

1. Selecione **Clone** para criar um repositório de local git do seu projeto de equipa de serviços.
    
    ![Clonar o repositório do projeto de equipa de serviços](media/tutorial-vsts-iis-cicd/clone_repo.png)

2. Em **soluções**, selecione **novo**.

    ![Criar a solução de aplicação web](media/tutorial-vsts-iis-cicd/new_solution.png)

3. Selecione **Web** modelos e, em seguida, escolha o **aplicação Web ASP.NET** modelo.
    1. Introduza um nome para a sua aplicação, tais como *myWebApp*e desmarque a caixa **criar diretórios para a solução**.
    2. Se a opção está disponível, desmarque a caixa para **adicionar o Application Insights ao projeto**. Application Insights requer a autorizar a aplicação web com o Azure Application Insights. Para manter simples este tutorial, ignore este processo.
    3. Selecione **OK**.
4. Escolha **MVC** na lista de modelo.
    1. Selecione **alterar autenticação**, escolha **sem autenticação**, em seguida, selecione **OK**.
    2. Selecione **OK** para criar a sua solução.
5. No **equipa Explorer** janela, escolha **alterações**.

    ![Consolidar alterações locais para o repositório de git Team Services](media/tutorial-vsts-iis-cicd/commit_changes.png)

6. Na caixa de texto de consolidação, introduza uma mensagem como *consolidação inicial*. Escolha **consolidar todas as e sincronização** no menu pendente.


## <a name="create-build-definition"></a>Criar definição de compilação
Equipa de serviços, utiliza uma definição de compilação para descrevem como a aplicação deve ser criada. Neste tutorial, iremos criar uma definição básica que demora nosso código de origem, compila a solução, em seguida, cria web implementar o pacote que pode utilizar para executar a aplicação web num servidor de IIS.

1. No seu projeto de equipa de serviços, escolher **compilar & versão** na parte superior, em seguida, selecione **compilações**.
3. Selecione **+ nova definição**.
4. Escolha o **ASP.NET (pré-visualização)** modelo e selecione **aplicar**.
5. Deixe os valores das tarefas de todos os a predefinição. Em **obter origens**, certifique-se de que o *myWebApp* repositório e *mestre* ramo estão selecionadas.

    ![Criar a definição de compilação do projeto de equipa de serviços](media/tutorial-vsts-iis-cicd/create_build_definition.png)

6. No **Acionadores** separador, mova o controlo de deslize para **ativar este acionador** para *ativado*.
7. Guardar a definição de compilação e a fila de uma nova compilação selecionando **Guardar & fila** , em seguida, **Guardar & fila** novamente. Deixe as predefinições e selecione **fila**.

Veja como a compilação está agendada num agente alojado, em seguida, começa a criar. O resultado é semelhante ao seguinte exemplo:

![Compilação efetuada com êxito do projeto de equipa de serviços](media/tutorial-vsts-iis-cicd/successful_build.png)


## <a name="create-virtual-machine"></a>Criar a máquina virtual
Para fornecer uma plataforma para executar a aplicação web do ASP.NET, precisa de uma máquina virtual do Windows que executa o IIS. Serviços da equipa utiliza um agente para interagir com a instância do IIS como consolidar o código e compilações são acionadas.

Criar uma VM do Windows Server 2016 utilizando [este script de exemplo](../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json). Demora alguns minutos para que o script executar e criar a VM. Quando tiver sido criada a VM, abra a porta 80 para o tráfego web com [adicionar AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.resources/new-azurermresourcegroup) da seguinte forma:

```powershell
Get-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $resourceGroup `
  -Name "myNetworkSecurityGroup" | `
Add-AzureRmNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWeb" `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority "1001" `
  -SourceAddressPrefix "*" `
  -SourcePortRange "*" `
  -DestinationAddressPrefix "*" `
  -DestinationPortRange "80" `
  -Access "Allow" | `
Set-AzureRmNetworkSecurityGroup
```

Para ligar à VM, obter o endereço IP público com [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) da seguinte forma:

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup | Select IpAddress
```

Crie uma sessão de ambiente de trabalho remoto para a VM:

```cmd
mstsc /v:<publicIpAddress>
```

Na VM, abra uma **do PowerShell de administrador** linha de comandos. Instale o IIS e funcionalidades necessárias do .NET da seguinte forma:

```powershell
Install-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features
```


## <a name="create-deployment-group"></a>Criar grupo de implementação
Emitir web implementar o pacote para o servidor IIS, definir um grupo de implementação nos serviços de equipa. Este grupo permite-lhe especificar os servidores estão o destino de novas compilações como consolidar o código para a equipa de serviços e compilações são concluídas.

1. Na equipa de serviços, escolha **compilar & versão** e, em seguida, selecione **grupos de implementação**.
2. Escolha **grupo adicionar implementação**.
3. Introduza um nome para o grupo, como *myIIS*, em seguida, selecione **criar**.
4. No **registar máquinas** secção, certifique-se *Windows* está selecionado, em seguida, selecione a caixa para **utilizam um token de acesso pessoal no script para a autenticação**.
5. Selecione **copie o script para a área de transferência**.


### <a name="add-iis-vm-to-the-deployment-group"></a>Adicionar a VM de IIS para o grupo de implementação
Com o grupo de implementação criado, adicione cada instância IIS para o grupo. Serviços da equipa gera um script que transfere e configura um agente na VM que recebe o novo web implementar pacotes, em seguida, aplica-se ao IIS.

1. Volta a **do PowerShell de administrador** sessão na sua VM, colar e execute o script copiado da equipa de serviços.
2. Quando lhe for pedido para configurar as etiquetas para o agente, escolher *Y* e introduza *web*.
3. Quando lhe for pedido para a conta de utilizador, prima *devolver* para aceitar as predefinições.
4. Aguarde que o script para concluir uma mensagem *vstsagent.account.computername de serviço foi iniciado com êxito*.
5. No **grupos de implementação** página do **compilar & versão** menu, abra o *myIIS* grupo de implementação. No **máquinas** separador, certifique-se de que a VM está listada.

    ![Foi adicionado com êxito ao grupo de serviços da equipa de implementação de VM](media/tutorial-vsts-iis-cicd/deployment_group.png)


## <a name="create-release-definition"></a>Criar a definição da versão
Para publicar as compilações, crie uma definição de versão no Team Services. Esta definição é acionada automaticamente por uma compilação efetuada com êxito da sua aplicação. Escolha o grupo de implementação para emitir web implementar o pacote e definir as definições do IIS adequadas.

1. Escolha **compilar & versão**, em seguida, selecione **compilações**. Escolha a definição de compilação que criou no passo anterior.
2. Em **concluído recentemente**, escolha a compilação mais recente, em seguida, selecione **versão**.
3. Escolha **Sim** para criar uma definição de versão.
4. Escolha o **vazio** modelo, em seguida, selecione **seguinte**.
5. Certifique-se de que a definição de compilação do projeto e origem estão preenchidos com o seu projeto.
6. Selecione o **a implementação contínua** caixa de verificação, em seguida, selecione **criar**.
7. Selecione a caixa de lista pendente junto a **+ adicionar tarefas** e escolha *adicionar uma fase de grupo de implementação*.
    
    ![Adicionar a tarefa de lançamento de definição nos serviços de equipa](media/tutorial-vsts-iis-cicd/add_release_task.png)

8. Escolha **adicionar** junto a **Deploy(Preview) de aplicação de Web IIS**, em seguida, selecione **fechar**.
9. Selecione o **executar no grupo de implementação** tarefa principal.
    1. Para **grupo de implementação**, selecione o grupo de implementação que criou anteriormente, tais como *myIIS*.
    2. No **etiquetas de computador** caixa, selecione **adicionar** e escolha o *web* tag.
    
    ![Tarefa de grupo de implementação de definição da versão do IIS](media/tutorial-vsts-iis-cicd/release_definition_iis.png)
 
11. Selecione o **implementar: implementação de aplicação Web do IIS** tarefas para configurar as definições de instância do IIS, da seguinte forma:
    1. Para **nome do Web site**, introduza *Web Site predefinido*.
    2. Deixe todas as outras predefinições.
12. Escolha **guardar**, em seguida, selecione **OK** duas vezes.


## <a name="create-a-release-and-publish"></a>Criar uma versão e publicar
Agora, pode emitir web implementar o pacote como uma nova versão. Este passo comunica com o agente em cada instância que faz parte do grupo de implementação, pushes web implementar o pacote, em seguida, configura o IIS para executar a aplicação web atualizadas.

1. Na sua definição de versão, selecione **+ versão**, em seguida, escolha **criar versão**.
2. Certifique-se de que a compilação mais recente está selecionada na lista pendente, juntamente com **automatizada de implementação: após a criação de versão**. Selecione **Criar**.
3. Uma faixa pequena aparece na parte superior da sua definição de versão, tais como *versão 'Versão 1' foi criado*. Selecione a ligação de versão.
4. Abra o **registos** separador para ver o progresso de versão.
    
    ![Versão Team Services e web implementar push de pacote](media/tutorial-vsts-iis-cicd/successful_release.png)

5. Depois de concluída a versão, abra um browser e introduza o endereço IIP público da sua VM. A aplicação web do ASP.NET está em execução.

    ![Aplicação web do ASP.NET em execução numa VM do IIS](media/tutorial-vsts-iis-cicd/running_web_app.png)


## <a name="test-the-whole-cicd-pipeline"></a>Testar o pipeline de CI/CD todo
Com a sua aplicação web em execução no IIS, experimente agora o pipeline de CI/CD todo. Depois de efetuar uma alteração no Visual Studio e consolidar o código, uma compilação é acionado, que, em seguida, aciona uma versão do seu web atualizadas implemente o pacote ao IIS:

1. No Visual Studio, abra o **Explorador de soluções** janela.
2. Navegue para e abra *myWebApp | Vistas | Home page | Index. cshtml*
3. Edite linha 6 para leitura:

    `<h1>ASP.NET with VSTS and CI/CD!</h1>`

4. Guarde o ficheiro.
5. Abra o **equipa Explorer** janela, selecione o *myWebApp* do projeto, em seguida, escolha **alterações**.
6. Introduza uma mensagem de confirmação, tais como *testar CI/CD pipeline*, em seguida, escolha **todos os consolidação e a sincronização** no menu pendente.
7. Na área de trabalho do Team Services, é acionada uma nova compilação da consolidação de código. 
    - Escolha **compilar & versão**, em seguida, selecione **compilações**. 
    - Escolha a definição da compilação, em seguida, selecione o **em fila & execução** compilação para ver como avança de ser a compilação.
9. Assim que a compilação for concluída com êxito, é acionada uma nova versão.
    - Escolha **compilar & versão**, em seguida, **versões** para ver o web implementar pacote enviada para a VM do IIS. 
    - Selecione o **atualizar** ícone para atualizar o estado. Quando o *ambientes* coluna mostra uma marca de verificação verde, a versão foi implementado com êxito ao IIS.
11. Para ver as suas alterações aplicadas, atualize o seu Web site do IIS num browser.

    ![Aplicação web do ASP.NET em execução no IIS VM a partir do pipeline de CI/CD](media/tutorial-vsts-iis-cicd/running_web_app_cicd.png)


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma aplicação web ASP.NET no Team Services e configurado a compilação e definições de versão para implementar o novo web implementar pacotes de IIS em cada consolidação de código. Aprendeu a:

> [!div class="checklist"]
> * Publicar uma aplicação web do ASP.NET para um projeto de equipa de serviços
> * Criar uma definição de compilação que é acionada pela consolidações de código
> * Instalar e configurar o IIS numa máquina virtual no Azure
> * Adicionar a instância do IIS a um grupo de implementação nos serviços de equipa
> * Criar uma versão definição para publicar o novo web implementar pacotes em IIS
> * Testar o pipeline de CI/CD

Avançar para o próximo tutorial para saber como instalar um SQL Server &#92; IIS &#92;. Pilha de rede num par de VMs do Windows.

> [!div class="nextstepaction"]
> [SQL &#92; IIS &#92;. Pilha de rede](tutorial-iis-sql.md)