---
title: "Parâmetros de entrada do Runbook | Microsoft Docs"
description: "Os parâmetros de entrada do Runbook aumentam a flexibilidade de runbooks, permitindo-lhe transmitir dados para um runbook quando é iniciada. Este artigo descreve os diferentes cenários onde os parâmetros de entrada são utilizados em runbooks."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 4d3dff2c-1f55-498d-9a0e-eee497e5bedb
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: sngun
ms.openlocfilehash: e5a2afdc0dbe6171b27c11400f460eac46147f37
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="runbook-input-parameters"></a>Parâmetros de entrada do runbook

Os parâmetros de entrada do Runbook aumentam a flexibilidade de runbooks, permitindo-lhe transmitir dados para o mesmo quando é iniciada. Os parâmetros permitem as ações de runbook que será visada para cenários específicos e ambientes. Neste artigo, iremos irá guiá-lo através de diferentes cenários onde os parâmetros de entrada são utilizados em runbooks.

## <a name="configure-input-parameters"></a>Configurar parâmetros de entrada

Os parâmetros de entrada podem ser configurados no PowerShell, fluxo de trabalho do PowerShell, Python e runbooks gráficos. Um runbook pode ter vários parâmetros com tipos de dados diferente ou não existem parâmetros de todo. Os parâmetros de entrada podem ser obrigatórios ou opcionais e pode atribuir um valor predefinido para os parâmetros opcionais. Pode atribuir valores para os parâmetros de entrada para um runbook quando iniciar através de um dos métodos disponíveis. Estes métodos incluem iniciar um runbook a partir do portal ou de um serviço web. Também pode iniciar um como um runbook subordinado que é chamado inline no outro runbook.

## <a name="configure-input-parameters-in-powershell-and-powershell-workflow-runbooks"></a>Configurar parâmetros de entrada em runbooks do PowerShell e o fluxo de trabalho do PowerShell

PowerShell e [runbooks do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md) na automatização do Azure suporta os parâmetros de entrada que são definidos através dos seguintes atributos.  

| **Propriedade** | **Descrição** |
|:--- |:--- |
| Tipo |Necessário. O tipo de dados esperado para o valor do parâmetro. Qualquer tipo .NET é válido. |
| Nome |Necessário. O nome do parâmetro. Isto tem de ser exclusivo dentro do runbook e pode conter apenas letras, números ou carateres de caráter de sublinhado. Tem de começar com uma letra. |
| Obrigatório |Opcional. Especifica se deve ser fornecido um valor para o parâmetro. Se definir esta opção para **$true**, em seguida, tem de ser fornecido um valor quando o runbook é iniciado. Se definir esta opção para **$false**, em seguida, um valor é opcional. |
| Valor predefinido |Opcional.  Especifica um valor que será utilizado para o parâmetro se não for passado um valor no quando o runbook é iniciado. Um valor predefinido pode ser definido para cada parâmetro e automaticamente fará com que o parâmetro opcional, independentemente da definição obrigatória. |

O Windows PowerShell suporta mais atributos de parâmetros de entrada do que aqueles listados aqui, como validação, aliases, e conjuntos de parâmetros. No entanto, a automatização do Azure atualmente suporta apenas os parâmetros de entrada listados acima.

Uma definição de parâmetro nos runbooks do fluxo de trabalho do PowerShell tem o seguinte formato geral, onde os vários parâmetros são separados por vírgulas.

   ```powershell
     Param
     (
         [Parameter (Mandatory= $true/$false)]
         [Type] Name1 = <Default value>,

         [Parameter (Mandatory= $true/$false)]
         [Type] Name2 = <Default value>
     )
   ```

> [!NOTE]
> Quando estiver a definir parâmetros, se não especificar o **obrigatório** atributo, em seguida, por predefinição, o parâmetro é considerado opcional. Além disso, se definir um valor predefinido para um parâmetro nos runbooks do fluxo de trabalho do PowerShell, será tratado PowerShell como um parâmetro opcional, independentemente do **obrigatório** valor de atributo.
> 
> 

Por exemplo, vamos configurar os parâmetros de entrada para um runbook de fluxo de trabalho do PowerShell que produz os detalhes sobre as máquinas virtuais, uma única VM ou todas as VMs dentro de um grupo de recursos. Este runbook tem dois parâmetros, conforme mostrado na captura de ecrã seguinte: o nome da máquina virtual e o nome do grupo de recursos.

![O fluxo de trabalho do Automation PowerShell](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

Nesta definição do parâmetro, os parâmetros **$VMName** e **$resourceGroupName** são simples parâmetros do tipo cadeia. No entanto, os runbooks do PowerShell e o fluxo de trabalho do PowerShell suportam todos os tipos simples e tipos complexos, tais como **objeto** ou **PSCredential** para parâmetros de entrada.

Se o runbook tiver um parâmetro de entrada de tipo de objeto, em seguida, utilizar uma tabela hash de PowerShell com (nome, valor) pares passem um valor. Por exemplo, se tiver o seguinte parâmetro num runbook:

     [Parameter (Mandatory = $true)]
     [object] $FullName

Em seguida, pode passar o seguinte valor para o parâmetro:

    @{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}


## <a name="configure-input-parameters-in-graphical-runbooks"></a>Configurar parâmetros de entrada em runbooks gráficos

Para [configurar um runbook gráfico](automation-first-runbook-graphical.md) com parâmetros de entrada, vamos criar um runbook gráfico que produz os detalhes sobre as máquinas virtuais, ou uma única VM ou todas as VMs dentro de um grupo de recursos. Configurar um runbook é composta por duas atividades principais, conforme descrito abaixo.

[**Autenticar Runbooks com a conta Run As do Azure** ](automation-sec-configure-azure-runas-account.md) para autenticar com o Azure.

[**Get-AzureRmVm** ](https://msdn.microsoft.com/library/mt603718.aspx) para obter as propriedades de uma máquinas virtuais.

Pode utilizar o [ **Write-Output** ](https://technet.microsoft.com/library/hh849921.aspx) atividade para os nomes das máquinas virtuais de saída. A atividade **Get-AzureRmVm** aceite dois parâmetros, o **nome da máquina virtual** e **nome do grupo de recursos**. Uma vez que estes parâmetros poderão exigir valores diferentes sempre que iniciar o runbook, pode adicionar parâmetros de entrada ao runbook. Eis os passos para adicionar parâmetros de entrada:

1. Selecione o runbook de gráfico a **Runbooks** painel e clique em [ **editar** ](automation-graphical-authoring-intro.md) -lo.
2. No editor de runbooks, clique em **entrada e saída** para abrir o **entrada e saída** painel.
   
    ![Automatização runbook gráfico](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)
3. O **entrada e saída** painel mostra uma lista de parâmetros de entrada definidos para o runbook. Neste painel, pode adicionar um novo parâmetro de entrada ou edite a configuração de um parâmetro de entrada existente. Para adicionar um novo parâmetro do runbook, clique em **Adicionar entrada** para abrir o **parâmetro de entrada do Runbook** painel. Aqui, pode configurar os seguintes parâmetros:
   
   | **Propriedade** | **Descrição** |
   |:--- |:--- |
   | Nome |Necessário.  O nome do parâmetro. Isto tem de ser exclusivo dentro do runbook e pode conter apenas letras, números ou carateres de caráter de sublinhado. Tem de começar com uma letra. |
   | Descrição |Opcional. Descrição sobre a finalidade do parâmetro de entrada. |
   | Tipo |Opcional. O tipo de dados que é esperado para o valor do parâmetro. Tipos de parâmetros suportados são **cadeia**, **Int32**, **Int64**, **Decimal**, **booleano**,  **DateTime**, e **objeto**. Se um tipo de dados não estiver selecionado, assume como **cadeia**. |
   | Obrigatório |Opcional. Especifica se deve ser fornecido um valor para o parâmetro. Se optar por **Sim**, em seguida, tem de ser fornecido um valor quando o runbook é iniciado. Se optar por **não**, em seguida, é necessário um valor não quando o runbook é iniciado e pode ser definido um valor predefinido. |
   | Valor predefinido |Opcional. Especifica um valor que será utilizado para o parâmetro se não for passado um valor no quando o runbook é iniciado. Pode ser definido um valor predefinido para um parâmetro que não é obrigatório. Para definir um valor predefinido, escolher **personalizada**. Este valor é utilizado, a menos que outro valor é fornecido quando o runbook é iniciado. Escolha **nenhum** se não quiser fornecer qualquer valor predefinido. |
   
    ![Adicionar a entrada de novo](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Criar dois parâmetros com as seguintes propriedades que serão utilizadas pelo **Get-AzureRmVm** atividade:
   
   * **Parameter1:**
     
     * Nome – VMName
     * Tipo - cadeia
     * Obrigatório - não
   * **Parameter2:**
     
     * Nome - resourceGroupName
     * Tipo - cadeia
     * Obrigatório - não
     * Valor predefinido - personalizada
     * Valor predefinido personalizado - \<nome do grupo de recursos que contém as máquinas virtuais >
5. Depois de adicionar os parâmetros, clique em **OK**.  Agora pode visualizá-las no **entrada e saída painel**. Clique em **OK** novamente e, em seguida, clique em **guardar** e **publicar** o runbook.

## <a name="configure-input-parameters-in-python-runbooks"></a>Configurar parâmetros de entrada em runbooks do Python

Ao contrário do PowerShell, fluxo de trabalho do PowerShell e runbooks gráficos, Python runbooks não entram parâmetros com nome.
Todos os parâmetros de entrada são analisados como uma matriz de valores de argumento.
Aceder à matriz de importando o `sys` módulo ao seu script de Python e, em seguida, utilizar o `sys.argv` matriz.
É importante ter em atenção que o primeiro elemento da matriz, `sys.argv[0]`, é o nome do script, pelo que é o primeiro parâmetro de entrada real `sys.argv[1]`.

Para obter um exemplo de como utilizar os parâmetros de entrada de um runbook de Python, consulte [o meu primeiro runbook do Python na automatização do Azure](automation-first-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Atribuir valores de parâmetros dos runbooks de entrada

Pode passar valores de parâmetros dos runbooks nos seguintes cenários de entrada.

### <a name="start-a-runbook-and-assign-parameters"></a>Iniciar um runbook e atribuir parâmetros

Um runbook pode ser iniciado várias maneiras: através do portal do Azure, com um webhook, com os cmdlets do PowerShell, com a API REST ou com o SDK. Abaixo, vamos discutir diferentes métodos para iniciar um runbook e atribuir os parâmetros.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Iniciar um runbook publicado através do portal do Azure e atribuir parâmetros

Quando lhe [iniciar o runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal), a **iniciar Runbook** painel abre e pode introduzir valores para os parâmetros que acabou de criar.

![Começar a utilizar o portal](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

Na etiqueta sob a caixa de entrada, pode ver os atributos que definiu para o parâmetro. Incluem os atributos obrigatório ou opcional, tipo e valor predefinido. No balão de ajuda junto ao nome do parâmetro, pode ver todas as informações de chaves, terá de tomar decisões sobre os valores de entrada do parâmetro. Estas informações incluem um parâmetro é obrigatórios ou opcionais. Também inclui o tipo e valor predefinido (se aplicável) e outras notas úteis.

![Balão de ajuda](media/automation-runbook-input-parameters/automation-05-helpbaloon.png)

> [!NOTE]
> Suporte de parâmetros de tipo de cadeia **vazio** valores de cadeia.  Introduzir **[Cadeiavazia]** no parâmetro de entrada caixa passar uma cadeia vazia para o parâmetro. Além disso, os parâmetros de tipo de cadeia não suportam **nulo** valores a ser passados. Se não obtiver qualquer valor para o parâmetro de cadeia, em seguida, PowerShell irá interpretar como nulo.
> 
> 

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Iniciar um runbook publicado utilizando cmdlets do PowerShell e atribuir parâmetros

* **Cmdlets do Gestor de recursos do Azure:** pode iniciar um runbook de automatização que foi criado num grupo de recursos utilizando [início AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx).
  
  **Exemplo:**
  
  ```
  $params = @{“VMName”=”WSVMClassic”;”resourceGroupeName”=”WSVMClassicSG”}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” –ResourceGroupName $resourceGroupName -Parameters $params
  ```
* **Cmdlets de gestão do serviço do Azure:** pode iniciar um runbook de automatização que foi criado num grupo de recursos predefinidos utilizando [início AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690259.aspx).
  
  **Exemplo:**
  
  ```
  $params = @{“VMName”=”WSVMClassic”; ”ServiceName”=”WSVMClassicSG”}
  
  Start-AzureAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” -Parameters $params
  ```

> [!NOTE]
> Quando inicia um runbook utilizando cmdlets do PowerShell, um parâmetro predefinido, **MicrosoftApplicationManagementStartedBy** é criado com o valor **PowerShell**. Pode ver este parâmetro o **detalhes da tarefa** painel.  
> 
> 

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Iniciar um runbook através da utilização de um SDK e atribuir parâmetros

* **Método do Gestor de recursos do Azure:** pode iniciar um runbook através do SDK de um idioma de programação. Segue-se um fragmento de código c# para iniciar um runbook na sua conta de automatização. Pode ver todos os o código no nosso [repositório do GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  
  
  ```
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
  ```
* **Método de gestão do serviço do Azure:** pode iniciar um runbook através do SDK de um idioma de programação. Segue-se um fragmento de código c# para iniciar um runbook na sua conta de automatização. Pode ver todos os o código no nosso [repositório do GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).
  
  ```      
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
  ```
  
  Para iniciar este método, crie um dicionário para armazenar os parâmetros do runbook, **VMName** e **resourceGroupName**e os respetivos valores. Em seguida, inicie o runbook. Segue-se o fragmento de código c# para chamar o método que é definido acima.
  
  ```
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook(“Get-AzureVMGraphical”, RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Iniciar um runbook, utilizando a API REST e atribuir parâmetros
Uma tarefa de runbook pode ser criada e começar a utilizar a API de REST de automatização do Azure utilizando o **colocar** método com o URI do pedido seguinte.

    https://management.core.windows.net/<subscription-id>/cloudServices/<cloud-service-name>/resources/automation/~/automationAccounts/<automation-account-name>/jobs/<job-id>?api-version=2014-12-08`

No URI do pedido, substitua os seguintes parâmetros:

* **id de subscrição:** o ID de subscrição do Azure.  
* **nome do serviço em nuvem:** o nome da nuvem de serviço para que o pedido deve ser enviado.  
* **nome da conta de automatização:** o nome da sua conta de automatização que está alojado no serviço de nuvem especificada.  
* **id da tarefa:** o GUID da tarefa. GUIDs no PowerShell podem ser criados utilizando o **[GUID]::NewGuid(). ToString ()** comando.

Para poder passar os parâmetros para a tarefa de runbook, utilize o corpo do pedido. Demora as duas propriedades seguintes fornecidas no formato JSON:

* **Nome do Runbook:** necessário. O nome do runbook para a tarefa iniciar.  
* **Parâmetros do Runbook:** opcional. Um dicionário de lista de parâmetros de no (nome, valor) formato em que o nome deve ser do tipo cadeia e o valor pode ser qualquer valor JSON válido.

Se pretender iniciar o **Get-AzureVMTextual** runbook que foi criado anteriormente com **VMName** e **resourceGroupName** como parâmetros, utilize o seguinte formato JSON para o corpo do pedido.

   ```
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WSVMClassic",
         "resourceGroupName":”WSCS1”}
        }
    }
   ```

Um código de estado HTTP 201 é devolvido se a tarefa é criada com êxito. Para obter mais informações sobre cabeçalhos de resposta e o corpo da resposta, consulte o artigo sobre como [criar uma tarefa de runbook, utilizando a API REST.](https://msdn.microsoft.com/library/azure/mt163849.aspx)

### <a name="test-a-runbook-and-assign-parameters"></a>Testar um runbook e atribuir parâmetros
Quando lhe [testar a versão de rascunho de runbook](automation-testing-runbook.md) utilizando a opção de teste, o **testar** painel abre e pode configurar os valores para os parâmetros que acabou de criar.

![Testar e atribuir parâmetros](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Associar um agendamento ao runbook e atribuir parâmetros
Pode [associar um agendamento](automation-schedules.md) ao runbook para que o runbook é iniciado num momento específico. Atribuir os parâmetros de entrada quando criar a agenda e o runbook utilizará estes valores quando é iniciada, a agenda. Não é possível guardar a agenda até que todos os valores de parâmetro obrigatório são fornecidos.

![Agendar e atribuir parâmetros](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Criar um webhook de um runbook e atribuir parâmetros
Pode criar um [webhook](automation-webhooks.md) para o runbook e configurar os parâmetros de entrada do runbook. Não é possível guardar o webhook até que todos os valores de parâmetro obrigatório são fornecidos.

![Criar o webhook e atribuir parâmetros](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Quando executar um runbook através da utilização de um webhook, o parâmetro de entrada predefinido  **[Webhookdata](automation-webhooks.md#details-of-a-webhook)**  é enviada, juntamente com os parâmetros de entrada que definiu. Pode clicar para expandir o **WebhookData** parâmetro para obter mais detalhes.

![Parâmetro WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações sobre runbook entrada e saída, consulte [da automatização do Azure: runbook entrada, saída e runbooks aninhados](https://azure.microsoft.com/blog/azure-automation-runbook-input-output-and-nested-runbooks/).
* Para obter detalhes sobre diferentes formas de iniciar um runbook, consulte [iniciar um runbook](automation-starting-a-runbook.md).
* Para editar um runbook de texto, consulte [editar textual runbooks](automation-edit-textual-runbook.md).
* Para editar um runbook gráfico, consulte [a criação de gráficos na automatização do Azure](automation-graphical-authoring-intro.md).

