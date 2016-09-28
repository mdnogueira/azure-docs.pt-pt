<properties
   pageTitle="Utilizar o Código de VS com modelos do Gestor de Recursos | Microsoft Azure"
   description="Mostra como configurar o Visual Studio Code para criar modelos do Azure Resource Manager."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="cmatskas"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="chmatsk;tomfitz"/>


# Trabalhar com Modelos do Azure Resource Manager no Visual Studio Code

Os modelos do Azure Resource Manager são ficheiros JSON que descrevem um recurso e dependências relacionadas. Estes ficheiros podem, por vezes, ser grandes e complicados, o que significa que o suporte de ferramentas é importante. O Visual Studio Code é um novo editor de código simples, simples, de fonte aberta e plataforma cruzada. Suporta a criação e edição de modelos do Gestor de Recursos através de um [nova extensão](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). O VS Code é executado em qualquer local e não necessita de acesso à Internet, exceto se também pretender implementar os modelos de Gestor de Recursos.

Se ainda não tem o VS Code, pode instalá-lo em [https://code.visualstudio.com/](https://code.visualstudio.com/).

## Instalar a extensão do Gestor de Recursos

Para trabalhar com os modelos JSON no VS Code, tem de instalar uma extensão. Os seguintes passos indicam como transferir e instalar o suporte de idiomas para os modelos JSON do Gestor de Recursos:

1. Iniciar o VS Code 
2. Abrir rápido (Ctrl + P) 
3. Execute o seguinte comando: 

        ext install azurerm-vscode-tools

4. Reinicie o VS Code quando lhe for pedido para ativar a extensão. 

 Tarefa concluída!

## Configurar fragmentos do Gestor de Recursos

Os passos anteriores permitiam instalar o suporte de ferramentas, mas agora temos de configurar o VS Code para utilizar fragmentos do modelo JSON.

1. Copie o conteúdo do ficheiro a partir do repositório [azure-xplat-arm-tooling](https://raw.githubusercontent.com/Azure/azure-xplat-arm-tooling/master/VSCode/armsnippets.json) para a sua área de transferência.
2. Iniciar o VS Code 
3. No VS Code, pode abrir o ficheiro de fragmentos JSON, navegando para **Ficheiro** -> **Preferências** -> **Fragmentos do Utilizador** -> **JSON** ou selecionando **F1** e introduzindo **preferências** até poder selecionar **Preferências: Fragmentos**.

    ![fragmentos de preferência](./media/resource-manager-vs-code/preferences-snippets.png)

    A partir das opções, selecione **JSON**.

    ![selecionar json](./media/resource-manager-vs-code/select-json.png)

4. Cole o conteúdo do ficheiro no passo 1 no ficheiro de fragmentos do utilizador antes do "}" final 
5. Certifique-se de que JSON tem um aspeto correto e não existem falhas. 
6. Guarde e feche o ficheiro de fragmentos de utilizador.

Basta isto para começar a utilizar os fragmentos do Gestor de Recursos. Em seguida, vamos pôr em prática esta configuração.

## Trabalhar com o modelo no VS Code

A maneira mais fácil de começar a trabalhar com um modelo é utilizar um dos Modelos de Início Rápido disponíveis no [Github](https://github.com/Azure/azure-quickstart-templates) ou utilizar um dos seus. Pode facilmente [exportar um modelo](resource-manager-export-template.md) para qualquer um dos seus grupos de recursos através do portal. 

1. Se exportou um modelo de um grupo de recursos, abra os ficheiros extraídos no VS Code.

    ![mostrar os ficheiros](./media/resource-manager-vs-code/show-files.png)

2. Abra o ficheiro template.json para que possa editá-lo e adicionar alguns recursos adicionais. A seguir a **"recursos": [** prima Enter para iniciar uma nova linha. Se escrever **arm**, ser-lhe-ão apresentadas uma lista de opções. Estas opções são os fragmentos de modelo que instalou. Deve ter o seguinte aspeto: 

    ![mostrar fragmentos](./media/resource-manager-vs-code/type-snippets.png)

3. Escolha o fragmento que deseja. Para este artigo, estou a escolher **arm-ip** para criar um novo endereço IP público. Coloque um ponto e vírgula a seguir ao parênteses reto de fecho "}" do recurso recentemente criado para certificar-se de que a sintaxe do modelo é válida.

     ![adicionar vírgula](./media/resource-manager-vs-code/add-comma.png)

4. O VS Code tem o IntelliSense incorporado. À medida que edita os modelos, o VS Code sugere os valores disponíveis. Por exemplo, para adicionar uma secção de variáveis ao seu modelo, adicione **""** (aspas duplas) e selecione **Ctrl + espaço** entre essas aspas. Serão apresentadas opções, incluindo **variáveis**.

    ![adicionar variáveis](./media/resource-manager-vs-code/add-variables.png)

5. O IntelliSense pode também sugerir valores ou funções disponíveis. Para definir uma propriedade para um valor de parâmetro, crie uma expressão com **"[]"** e **Ctrl+espaço**. Pode começar a escrever o nome de uma função. Selecione **Separador** quando encontrar a função pretendida.

    ![adicionar parâmetro](./media/resource-manager-vs-code/select-parameters.png)

6. Selecione **Ctrl+espaço** novamente na função para ver uma lista dos parâmetros disponíveis no seu modelo.

    ![adicionar parâmetro](./media/resource-manager-vs-code/select-avail-parameters.png)

7. Se tiver quaisquer problemas de validação de esquema no seu modelo, verá os efeitos ondulantes familiares no editor. Pode ver a lista de erros e avisos introduzindo **Ctrl + Shift + M** ou selecionando os glifos na barra de estado inferior esquerda.

    ![erros](./media/resource-manager-vs-code/errors.png)

    A validação do seu modelo pode ajudá-lo a detetar problemas de sintaxe. No entanto, também podem aparecer erros que pode ignorar. Em alguns casos, o editor compara o seu modelo com um esquema que não está atualizado e, por conseguinte, apresenta um erro, apesar de o utilizador saber está correto. Por exemplo, suponha que uma função foi adicionada recentemente ao Gestor de Recursos, mas o esquema não foi atualizado. O editor comunica um erro, apesar da função funcionar corretamente durante a implementação.

    ![mensagem de erro](./media/resource-manager-vs-code/unrecognized-function.png)

## Implementar os novos recursos

Quando o modelo estiver pronto, pode implementar os novos recursos de acordo com as instruções abaixo: 

### Windows

1. Abrir uma linha de comandos do PowerShell 
2. Para iniciar sessão, introduza: 

        Login-AzureRmAccount 

3. Se tiver várias subscrições, aparece uma lista de subscrições com:

        Get-AzureRmSubscription

    E selecione a subscrição que pretende utilizar.
   
        Select-AzureRmSubscription -SubscriptionId <Subscription Id>

4. Atualize os parâmetros no ficheiro parameters.json
5. Execute o Deploy.ps1 para implementar o modelo no Azure

### OSX/Linux

1. Abra uma janela de terminal 
2. Para iniciar sessão, introduza:

        azure login 

3. Se tiver várias subscrições, selecione a subscrição correta com:

        azure account set <subscriptionNameOrId> 

4. Atualize os parâmetros no ficheiro parameters.json.
5. Para implementar o modelo, execute:

        azure group deployment create -f <PathToTemplate> 

## Passos seguintes

- Para saber mais sobre modelos, consulte o artigo [Criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md).
- Para saber mais sobre as funções de modelo, veja o artigo [Funções de modelo do Azure Resource Manager](resource-group-template-functions.md).
- Para obter mais exemplos de como trabalhar com o Visual Studio Code, veja o artigo [Criar aplicações na nuvem com o Visual Studio Code](https://github.com/Microsoft/HealthClinic.biz/wiki/Build-cloud-apps-with-Visual-Studio-Code) a partir da [demonstração](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect [HealthClinic.biz](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Para obter mais inícios rápidos a partir da demonstração de HealthClinic.biz, veja [Inícios Rápidos de Ferramentas de Programador do Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).



<!--HONumber=Sep16_HO3-->


