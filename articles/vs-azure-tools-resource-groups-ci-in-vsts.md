---
title: "Integração contínua nos serviços de equipa de VS com projetos de grupo de recursos do Azure | Microsoft Docs"
description: "Descreve como configurar a integração contínua no Visual Studio Team Services utilizando projetos de implementação do grupo de recursos do Azure no Visual Studio."
services: visual-studio-online
documentationcenter: na
author: mlearned
manager: erickson-doug
editor: 
ms.assetid: b81c172a-be87-4adc-861e-d20b94be9e38
ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: mlearned
ms.openlocfilehash: e7d98ca3fa281a136595c37ed9b7e71de0cf7bff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="continuous-integration-in-visual-studio-team-services-using-azure-resource-group-deployment-projects"></a>Integração contínua no Visual Studio Team Services com projetos de implementação do grupo de recursos do Azure
Para implementar um modelo do Azure, executar tarefas em várias etapas: cópia de compilação, teste, para o Azure (também denominada "Testes") e implementar o modelo. Existem duas formas diferentes de implementar modelos Visual Studio Team Services (VS equipa de serviços). Ambos os métodos de fornecer os mesmos resultados, por isso, escolha a que melhor se adequa o fluxo de trabalho.

1. Adicione um único passo para a definição de compilação que executa o script do PowerShell que está incluído no projeto de implementação de grupo de recursos do Azure (implementar AzureResourceGroup.ps1). O script copia artefactos e, em seguida, implementa o modelo.
2. Adicione que múltiplos serviços da equipa de VS criar passos, cada um da executar uma tarefa de fase.

Este artigo demonstra ambas as opções. A primeira opção tem a vantagem de utilizar o mesmo script utilizado por programadores no Visual Studio e fornecer consistência em todo o ciclo de vida. A segunda opção oferece uma alternativa conveniente para o script incorporado. Ambos os procedimentos partem do princípio de que já tem um projeto de implementação do Visual Studio verificado no VS Team Services.

## <a name="copy-artifacts-to-azure"></a>Copiar artefactos para o Azure
Independentemente do cenário, se tiver quaisquer artefactos necessários para a implementação do modelo, deve fornecer o acesso do Azure Resource Manager aos mesmos. Destes artefactos podem incluir ficheiros, tais como:

* Modelos aninhados
* Scripts de configuração e os scripts de DSC
* Binários da aplicação

### <a name="nested-templates-and-configuration-scripts"></a>Modelos aninhados e Scripts de configuração
Ao utilizar os modelos fornecidos pelo Visual Studio (ou criadas com fragmentos do Visual Studio), o script do PowerShell prepara não apenas os artefactos, também parameterizes o URI para os recursos para diferentes implementações. O script, em seguida, copia os artefactos para um contentor seguro no Azure, cria um token SaS para esse contentor e, em seguida, transfere a informação para a implementação do modelo. Consulte [criar uma implementação de modelo](https://msdn.microsoft.com/library/azure/dn790564.aspx) para saber mais sobre modelos aninhados.  Quando utilizar tarefas no VS Team Services, tem de selecionar as tarefas adequadas para a implementação do modelo e, se necessário, passar valores de parâmetros do passo de transição para a implementação do modelo.

## <a name="set-up-continuous-deployment-in-vs-team-services"></a>Configurar a implementação contínua nos serviços de equipa do VS
Para chamar o script do PowerShell no VS Team Services, terá de atualizar a definição de compilação. No brief, os passos são: 

1. Edite a definição de compilação.
2. Configure a autorização do Azure no VS Team Services.
3. Adicione um passo de compilação do Azure PowerShell que referencia o script do PowerShell no projeto de implementação de grupo de recursos do Azure.
4. Defina o valor da *- ArtifactsStagingDirectory* parâmetro para trabalhar com um projeto foi compilado no VS Team Services.

### <a name="detailed-walkthrough-for-option-1"></a>Instruções detalhadas para a opção 1
Os procedimentos seguintes descrevem os passos necessários para configurar a implementação contínua no VS Team Services utilizando uma tarefa simples que executa o script do PowerShell no seu projeto. 

1. Editar a definição da compilação VS Team Services e adicione um passo de compilação do Azure PowerShell. Escolha a definição de compilação sob o **criar definições** categoria e, em seguida, escolha o **editar** ligação.
   
   ![Editar definições de compilação][0]
2. Adicione um novo **Azure PowerShell** compilar passo para a definição de compilação e, em seguida, escolha o **passo de compilação de adicionar...** Adicionar...
   
   ![Adicione o passo de compilação][1]
3. Escolha o **tarefas implementar** categoria, selecione o **Azure PowerShell** de tarefas e, em seguida, escolha o **adicionar** botão.
   
   ![Adicionar tarefas][2]
4. Escolha o **Azure PowerShell** compilar passo e, em seguida, preencha os respetivos valores.
   
   1. Se já tiver um ponto final de serviço do Azure adicionado à VS Team Services, selecione a subscrição no **subscrição do Azure** caixa de lista pendente e, em seguida, avance para a secção seguinte. 
      
      Se não tiver um ponto final de serviço do Azure no VS Team Services, terá de adicionar um. Esta subsecção leva-o ao longo do processo. Se a sua conta do Azure utiliza uma conta Microsoft (por exemplo, Hotmail), tem de colocar os seguintes passos para obter uma autenticação Principal de serviço.
   2. Escolha o **gerir** ligação junto a **subscrição do Azure** caixa de lista pendente.
      
      ![Gerir as subscrições do Azure][3]
   3. Escolha **Azure** no **ponto final de serviço novo** caixa de lista pendente.
      
      ![Novo ponto final de serviço][4]
   4. No **adicionar subscrição do Azure** caixa de diálogo, selecione o **Principal de serviço** opção.
      
      ![Opção de principal de serviço][5]
   5. Adicionar informações da sua subscrição do Azure para o **adicionar subscrição do Azure** caixa de diálogo. Tem de fornecer os seguintes itens:
      
      * Id de subscrição
      * Nome da subscrição
      * Id de Principal de serviço
      * Chave de Principal de serviço
      * Id do inquilino
   6. Adicionar um nome à sua escolha para a **subscrição** caixa nome. Este valor apareçam mais à frente no **subscrição do Azure** na lista pendente no VS Team Services. 
   7. Se não souber o ID de subscrição do Azure, pode utilizar um dos seguintes comandos para obtê-lo.
      
      Para scripts do PowerShell, utilize:
      
      `Get-AzureRmSubscription`
      
      Para a CLI do Azure, utilize:
      
      `azure account show`
   8. Para obter uma ID de Principal de serviço, a chave de Principal de serviço e o ID do inquilino, siga o procedimento de [aplicação criar do Active Directory e um principal de serviço através do portal](resource-group-create-service-principal-portal.md) ou [autenticar um principal de serviço com o Azure Gestor de recursos](resource-group-authenticate-service-principal.md).
   9. Adicione os valores de ID de Principal de serviço, a chave de Principal de serviço e o ID do inquilino para o **adicionar subscrição do Azure** diálogo caixa e, em seguida, escolha o **OK** botão.
      
      Tem agora um Principal de serviço válido para utilizar para executar o script do PowerShell do Azure.
5. Edite a definição de compilação e escolha o **Azure PowerShell** passo de compilação. Selecione a subscrição no **subscrição do Azure** caixa de lista pendente. (Se a subscrição não aparecer, escolha o **atualizar** no botão seguinte do **gerir** ligação.) 
   
   ![Configurar tarefas de compilação do Azure PowerShell][8]
6. Forneça um caminho para o script do PowerShell AzureResourceGroup.ps1 implementar. Para tal, escolha o botão de reticências (…) junto ao **caminho do Script** caixa, navegue para o script do PowerShell de implementar AzureResourceGroup.ps1 no **Scripts** pasta do projeto, selecione-o e, em seguida, Escolha o **OK** botão.    
   
   ![Selecione o caminho para o script][9]
7. Depois de selecionar o script, atualize o caminho para o script para que este é executado a partir de Build.StagingDirectory (mesmo diretório que *ArtifactsLocation* está definido como). Pode fazê-lo ao adicionar "$(Build.StagingDirectory)/"para o início do caminho do script.
   
    ![Editar o caminho para o script][10]
8. No **argumentos do Script** box, introduza os seguintes parâmetros (numa única linha,). Quando executar o script no Visual Studio, pode ver como VS utiliza os parâmetros de **saída** janela. Pode utilizar isto como um ponto de partida para definir os valores de parâmetro o passo de compilação.
   
   | Parâmetro | Descrição |
   | --- | --- |
   | -ResourceGroupLocation |O valor de geolocalização onde está localizado, tal como o grupo de recursos **eastus** ou **'EUA Leste'**. (Adicionar plicas se não existir espaço no nome.) Consulte [regiões do Azure](https://azure.microsoft.com/en-us/regions/) para obter mais informações. |
   | -ResourceGroupName |O nome do grupo de recursos utilizado para esta implementação. |
   | -UploadArtifacts |Este parâmetro, quando presente, especifica que os artefactos que precisam de ser carregado para o Azure a partir do sistema local. Só tem de definir este parâmetro se a implementação do modelo exige artefactos adicionais que pretende testar a utilizar o script do PowerShell (como scripts de configuração ou modelos aninhados). |
   | -StorageAccountName |O nome da conta de armazenamento utilizado para artefactos fase para esta implementação. Este parâmetro só é utilizado se estão a transição artefactos para implementação. Se este parâmetro for fornecido, é criada uma nova conta de armazenamento se o script não tiver criado um durante uma implementação anterior. Se o parâmetro for especificado, a conta de armazenamento tem de existir. |
   | -StorageAccountResourceGroupName |O nome do grupo de recursos associado à conta de armazenamento. Este parâmetro só é necessário se fornecer um valor para o parâmetro StorageAccountName. |
   | -TemplateFile |O caminho para o ficheiro de modelo no projeto de implementação de grupo de recursos do Azure. Para melhorar a flexibilidade, utilize um caminho para este parâmetro que é relativo à localização do script do PowerShell em vez de um caminho absoluto. |
   | -TemplateParametersFile |O caminho para o ficheiro de parâmetros no projeto de implementação de grupo de recursos do Azure. Para melhorar a flexibilidade, utilize um caminho para este parâmetro que é relativo à localização do script do PowerShell em vez de um caminho absoluto. |
   | -ArtifactStagingDirectory |Este parâmetro permite que o script deve saber a pasta onde devem ser copiados os ficheiros binários do projeto do PowerShell. Este valor substitui o valor predefinido utilizado pelo script do PowerShell. Para utilizar o VS Team Services, defina o valor: - ArtifactStagingDirectory $(Build.StagingDirectory) |
   
   Eis um exemplo de argumentos de script (quebrada para facilitar a leitura de linha):
   
   ```    
   -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
   -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
   –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'    
   ```
   
   Quando tiver terminado, a **argumentos do Script** caixa deve assemelhar-se a lista seguinte:
   
   ![Argumentos do script][11]
9. Depois de adicionar os itens requeridos para o passo de compilação do Azure PowerShell, escolha o **fila** criar botão para compilar o projeto. O **criar** ecrã mostra a saída do script do PowerShell.

### <a name="detailed-walkthrough-for-option-2"></a>Instruções detalhadas para a opção 2
Os procedimentos seguintes descrevem os passos necessários para configurar a implementação contínua no VS Team Services utilizando as tarefas incorporadas.

1. Edite a definição da compilação VS Team Services para adicionar a que criar novo dois passos. Escolha a definição de compilação sob o **criar definições** categoria e, em seguida, escolha o **editar** ligação.
   
   ![Editar defintion de compilação][12]
2. Adicionar os passos de criação de novo para a definição de compilação utilizando o **passo de compilação de adicionar...** Adicionar...
   
   ![Adicione o passo de compilação][13]
3. Escolha o **implementar** categoria da tarefa, selecione o **a cópia de ficheiros do Azure** de tarefas e, em seguida, escolha o **adicionar** botão.
   
   ![Adicionar tarefas de cópia de ficheiros do Azure][14]
4. Escolha o **implementação do grupo de recursos do Azure** de tarefas, em seguida, escolha o **adicionar** botão e, em seguida, **fechar** o **tarefas catálogo**.
   
   ![Adicionar tarefas de implementação de grupo de recursos do Azure][15]
5. Escolha o **a cópia de ficheiros do Azure** de tarefas e preencha os respetivos valores.
   
   Se já tiver um ponto final de serviço do Azure adicionado à VS Team Services, selecione a subscrição no **subscrição do Azure** caixa de lista pendente. Se não tiver uma subscrição, consulte [opção 1](#detailed-walkthrough-for-option-1) para obter instruções sobre como configurar um no VS Team Services.
   
   * Origem - introduza **$(Build.StagingDirectory)**
   * Tipo de ligação do Azure - selecione **do Azure Resource Manager**
   * Subscrição do Azure RM - selecione a subscrição para a conta de armazenamento que pretende utilizar o **subscrição do Azure** caixa de lista pendente. Se a subscrição não aparecer, escolha o **atualizar** no botão seguinte do **gerir** ligação.
   * Tipo de destino - selecione **Blob do Azure**
   * Conta de armazenamento ao RM - selecione o armazenamento de conta gostaria de utilizar para artefactos de teste
   * Nome do contentor - introduza o nome do contentor que pretende utilizar para transição; -pode ser qualquer nome de contentor válido, mas utilizam um dedicado para esta definição de compilação
   
   Para os valores de saída:
   
   * Introduza o contentor de armazenamento URI - **artifactsLocation**
   * Token de SAS do contentor de armazenamento - introduza **artifactsLocationSasToken**
   
   ![Configurar tarefas de cópia de ficheiros do Azure][16]
6. Escolha o **implementação do grupo de recursos do Azure** compilar passo e, em seguida, preencha os respetivos valores.
   
   * Tipo de ligação do Azure - selecione **do Azure Resource Manager**
   * Subscrição do Azure RM - selecione a subscrição para a implementação no **subscrição do Azure** caixa de lista pendente. Normalmente, será a mesma subscrição utilizada no passo anterior
   * Ação - selecione **criar ou atualizar o grupo de recursos**
   * Grupo de recursos – selecione um grupo de recursos ou introduza o nome de um novo grupo de recursos para a implementação
   * Localização - selecionar a localização para o grupo de recursos
   * Modelo - introduza o caminho e nome do modelo de implementação de prefixação **$(Build.StagingDirectory)**, por exemplo: **$(Build.StagingDirectory/DSC-CI/azuredeploy.json)**
   * Os parâmetros do modelo - introduza o caminho e nome dos parâmetros para ser utilizado, prefixação **$(Build.StagingDirectory)**, por exemplo: **$(Build.StagingDirectory/DSC-CI/azuredeploy.parameters.json)**
   * Substituir os parâmetros do modelo - introduza ou copie e cole o seguinte código:
     
     ```    
     -_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken (ConvertTo-SecureString -String "$(artifactsLocationSasToken)" -AsPlainText -Force)
     ```
     ![Configurar tarefas de implementação do grupo de recursos do Azure][17]
7. Depois de adicionar os itens requeridos, guardar a definição de compilação e escolha **fila nova compilação** na parte superior.

## <a name="next-steps"></a>Passos seguintes
Leitura [descrição geral do Azure Resource Manager](azure-resource-manager/resource-group-overview.md) para saber mais sobre o Azure Resource Manager e os grupos de recursos do Azure.

[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png
[12]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough13.png
[13]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough14.png
[14]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough15.png
[15]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough16.png
[16]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough17.png
[17]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough18.png
