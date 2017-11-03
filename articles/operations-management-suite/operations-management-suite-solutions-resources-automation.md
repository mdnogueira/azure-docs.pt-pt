---
title: "Recursos de automatização do Azure em soluções do OMS | Microsoft Docs"
description: "Soluções na OMS incluem, geralmente, os runbooks na automatização do Azure para automatizar os processos tais como recolher e processar dados de monitorização.  Este artigo descreve como incluir runbooks e os respetivos recursos relacionados numa solução."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c1909183a33ed03d8165671cff25cc8b83b77733
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="adding-azure-automation-resources-to-an-oms-management-solution-preview"></a>A adição de recursos de automatização do Azure para uma solução de gestão do OMS (pré-visualização)
> [!NOTE]
> Esta documentação é de preliminar para criar soluções de gestão no OMS que estão atualmente em pré-visualização. Nenhum esquema descrita abaixo está sujeita a alterações.   


[As soluções de gestão no OMS](operations-management-suite-solutions.md) normalmente incluirá a runbooks na automatização do Azure para automatizar os processos tais como recolher e processar dados de monitorização.  Para além de runbooks, as contas de automatização inclui recursos, tais como as variáveis e agendas que suportam os runbooks utilizados na solução.  Este artigo descreve como incluir runbooks e os respetivos recursos relacionados numa solução.

> [!NOTE]
> Os exemplos neste artigo utilizam parâmetros e variáveis que são necessários ou comuns para soluções de gestão e descrito em [criar soluções de gestão no Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md) 


## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que já estiver familiarizado com as seguintes informações.

- Como [criar uma solução de gestão](operations-management-suite-solutions-creating.md).
- A estrutura de um [ficheiro de solução](operations-management-suite-solutions-solution-file.md).
- Como [criar modelos do Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

## <a name="automation-account"></a>Conta de automatização
Todos os recursos na automatização do Azure estão contidos num [conta de automatização](../automation/automation-security-overview.md#automation-account-overview).  Conforme descrito em [OMS área de trabalho e a conta de automatização](operations-management-suite-solutions.md#oms-workspace-and-automation-account) a conta de automatização não está incluída na solução de gestão, mas tem de existir antes da solução está instalada.  Se não estiver disponível, a solução de instalação irá falhar.

O nome de cada recurso de automatização inclui o nome da sua conta de automatização.  Isto é feito na solução com o **accountName** parâmetro como no seguinte exemplo de um recurso de runbook.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
Deve incluir todos os runbooks utilizados pela solução no ficheiro de solução, de modo a que são criados quando a solução é instalada.  Não pode conter o corpo do runbook no modelo, pelo que deve publicar o runbook para uma localização pública em que podem ser acedido por qualquer utilizador que instalar a sua solução.

[Runbook da automatização do Azure](../automation/automation-runbook-types.md) recursos tem um tipo de **Microsoft.Automation/automationAccounts/runbooks** e ter a seguinte estrutura. Isto inclui os parâmetros e variáveis comuns para que possam copie e cole o fragmento de código no seu ficheiro de solução e alterar os nomes de parâmetro. 

    {
        "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
        "type": "Microsoft.Automation/automationAccounts/runbooks",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "location": "[parameters('regionId')]",
        "tags": { },
        "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
                "uri": "[variables('Runbook').Uri]",
                "version": [variables('Runbook').Version]"
            }
        }
    }


As propriedades para os runbooks são descritas na seguinte tabela.

| Propriedade | Descrição |
|:--- |:--- |
| runbooktype necessária |Especifica os tipos de runbook. <br><br> Script - o script do PowerShell <br>PowerShell – fluxo de trabalho do PowerShell <br> GraphPowerShell - runbook do script do PowerShell gráfica <br> GraphPowerShellWorkflow - runbook de fluxo de trabalho do PowerShell gráfica |
| logProgress |Especifica se [registos de progresso](../automation/automation-runbook-output-and-messages.md) deve ser gerado para o runbook. |
| logVerbose |Especifica se [registos verbosos](../automation/automation-runbook-output-and-messages.md) deve ser gerado para o runbook. |
| descrição |Descrição opcional para o runbook. |
| publishContentLink |Especifica o conteúdo do runbook. <br><br>URI - Uri para o conteúdo do runbook.  Este será um ficheiro. ps1 para runbooks do PowerShell e scripts e um ficheiro de runbook gráfico exportado para um runbook de gráfico.  <br> versão - versão do runbook para o seu próprio controlo. |


## <a name="automation-jobs"></a>Tarefas de automatização
Quando inicia um runbook na automatização do Azure, cria uma tarefa de automatização.  Pode adicionar um recurso de tarefa de automatização à sua solução para iniciar automaticamente um runbook quando a solução de gestão está instalada.  Este método é normalmente utilizado para iniciar os runbooks que são utilizados para a configuração inicial da solução.  Para iniciar um runbook em intervalos regulares, crie um [agenda](#schedules) e um [agendamento da tarefa](#job-schedules)

Recursos de trabalho tem um tipo de **Microsoft.Automation/automationAccounts/jobs** e ter a seguinte estrutura.  Isto inclui os parâmetros e variáveis comuns para que possam copie e cole o fragmento de código no seu ficheiro de solução e alterar os nomes de parâmetro. 

    {
      "name": "[concat(parameters('accountName'), '/', parameters('Runbook').JobGuid)]",
      "type": "Microsoft.Automation/automationAccounts/jobs",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
      ],
      "tags": { },
      "properties": {
        "runbook": {
          "name": "[variables('Runbook').Name]"
        },
        "parameters": {
          "Parameter1": "[[variables('Runbook').Parameter1]",
          "Parameter2": "[[variables('Runbook').Parameter2]"
        }
      }
    }

As propriedades para as tarefas de automatização são descritas na seguinte tabela.

| Propriedade | Descrição |
|:--- |:--- |
| runbook |Entidade de nome único com o nome do runbook para iniciar. |
| parâmetros |Entidade para cada valor de parâmetro necessário pelo runbook. |

A tarefa inclui o nome do runbook e quaisquer valores de parâmetros para serem enviados para o runbook.  A tarefa deve [dependem](operations-management-suite-solutions-solution-file.md#resources) o runbook que está a iniciar desde o runbook tem de ser criado antes da tarefa.  Se tiver vários runbooks que deverão ser iniciados pode definir a sua ordem, fazendo com que uma tarefa de depender de quaisquer outras tarefas que devem ser executadas primeiro.

O nome de um recurso de tarefa tem de conter um GUID que é normalmente atribuído por um parâmetro.  Pode ler mais sobre os parâmetros GUID das [criar soluções no Operations Management Suite (OMS)](operations-management-suite-solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Certificados
[Certificados de automatização do Azure](../automation/automation-certificates.md) ter um tipo de **Microsoft.Automation/automationAccounts/certificates** e ter a seguinte estrutura. Isto inclui os parâmetros e variáveis comuns para que possam copie e cole o fragmento de código no seu ficheiro de solução e alterar os nomes de parâmetro. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
      "type": "Microsoft.Automation/automationAccounts/certificates",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "base64Value": "[variables('Certificate').Base64Value]",
        "thumbprint": "[variables('Certificate').Thumbprint]"
      }
    }



As propriedades de recursos de certificados são descritas na seguinte tabela.

| Propriedade | Descrição |
|:--- |:--- |
| base64Value |Valor de base 64 para o certificado. |
| impressão digital |Thumbprint do certificado. |



## <a name="credentials"></a>Credenciais
[Credenciais de automatização do Azure](../automation/automation-credentials.md) ter um tipo de **Microsoft.Automation/automationAccounts/credentials** e ter a seguinte estrutura.  Isto inclui os parâmetros e variáveis comuns para que possam copie e cole o fragmento de código no seu ficheiro de solução e alterar os nomes de parâmetro. 


    {
      "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
      "type": "Microsoft.Automation/automationAccounts/credentials",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "userName": "[parameters('credentialUsername')]",
        "password": "[parameters('credentialPassword')]"
      }
    }

As propriedades de recursos de credencial descritas na seguinte tabela.

| Propriedade | Descrição |
|:--- |:--- |
| Nome de utilizador |Nome de utilizador para a credencial. |
| palavra-passe |Palavra-passe para a credencial. |


## <a name="schedules"></a>Agendas
[Agendas de automatização do Azure](../automation/automation-schedules.md) ter um tipo de **Microsoft.Automation/automationAccounts/schedules** e ter a seguinte estrutura. Isto inclui os parâmetros e variáveis comuns para que possam copie e cole o fragmento de código no seu ficheiro de solução e alterar os nomes de parâmetro. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
      "type": "microsoft.automation/automationAccounts/schedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Schedule').Description]",
        "startTime": "[parameters('scheduleStartTime')]",
        "timeZone": "[parameters('scheduleTimeZone')]",
        "isEnabled": "[variables('Schedule').IsEnabled]",
        "interval": "[variables('Schedule').Interval]",
        "frequency": "[variables('Schedule').Frequency]"
      }
    }

As propriedades de recursos de agenda são descritas na seguinte tabela.

| Propriedade | Descrição |
|:--- |:--- |
| descrição |Descrição opcional para a agenda. |
| startTime |Especifica a hora de início de uma agenda como um objeto de DateTime. Uma cadeia pode ser fornecida se pode ser convertido para um valor DateTime válido. |
| IsEnabled |Especifica se a agenda é ativada. |
| intervalo |O tipo de intervalo para a agenda.<br><br>dia<br>hora |
| frequência |Frequência da agenda deve acionados em número de dias ou horas. |

As agendas tem de ter uma hora de início com um valor maior do que a hora atual.  Não é possível fornecer este valor com uma variável, uma vez que não terá de nenhuma forma de saber quando vai ser instalado.

Utilize uma das seguintes dois estratégias ao utilizar o agendamento recursos numa solução.

- Utilize um parâmetro para a hora de início da agenda.  Esta ação irá solicitar ao utilizador para fornecer um valor ao efetuar a instalação a solução.  Se tiver várias agendas, pode utilizar um valor de parâmetro único de mais do que uma delas.
- Crie os agendamentos com um runbook que inicia quando a solução está instalada.  Esta ação remove o requisito do utilizador para especificar uma hora, mas não pode conter a agenda na sua solução pelo que serão removida quando a solução é removida.


### <a name="job-schedules"></a>Agendas de tarefas
Um runbook com uma agenda de ligação de recursos de agenda da tarefa.  Têm um tipo de **Microsoft.Automation/automationAccounts/jobSchedules** e ter a seguinte estrutura.  Isto inclui os parâmetros e variáveis comuns para que possam copie e cole o fragmento de código no seu ficheiro de solução e alterar os nomes de parâmetro. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
      "type": "microsoft.automation/automationAccounts/jobSchedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
        "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
      ],
      "tags": {
      },
      "properties": {
        "schedule": {
          "name": "[variables('Schedule').Name]"
        },
        "runbook": {
          "name": "[variables('Runbook').Name]"
        }
      }
    }


As propriedades para as agendas de tarefas são descritas na seguinte tabela.

| Propriedade | Descrição |
|:--- |:--- |
| nome da agenda |Único **nome** entidade com o nome da agenda. |
| Nome do Runbook  |Único **nome** entidade com o nome do runbook.  |



## <a name="variables"></a>Variáveis
[Variáveis de automatização do Azure](../automation/automation-variables.md) ter um tipo de **Microsoft.Automation/automationAccounts/variables** e ter a seguinte estrutura.  Isto inclui os parâmetros e variáveis comuns para que possam copie e cole o fragmento de código no seu ficheiro de solução e alterar os nomes de parâmetro.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
      "type": "microsoft.automation/automationAccounts/variables",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Variable').Description]",
        "isEncrypted": "[variables('Variable').Encrypted]",
        "type": "[variables('Variable').Type]",
        "value": "[variables('Variable').Value]"
      }
    }

As propriedades de recursos de variável são descritas na seguinte tabela.

| Propriedade | Descrição |
|:--- |:--- |
| descrição | Descrição opcional para a variável. |
| isEncrypted | Especifica se a variável deve ser encriptada. |
| tipo | Esta propriedade não tem atualmente qualquer efeito.  O tipo de dados da variável será determinado pelo valor inicial. |
| valor | Valor da variável. |

> [!NOTE]
> O **tipo** propriedade atualmente não tem efeito na variável a ser criada.  O tipo de dados para a variável será determinado pelo valor.  

Se definir o valor inicial para a variável, tem de ser configurado como o tipo de dados correto.  A tabela seguinte fornece os respetivos sintaxe e os diferentes tipos de dados permitidos.  Tenha em atenção que são esperados valores no JSON para sempre ser inseridos entre aspas com os caracteres especiais dentro as aspas.  Por exemplo, poderia ser especificado um valor de cadeia por aspas à volta a cadeia (utilizar o caráter de escape (\\)) enquanto teria de ser especificado um valor numérico com um conjunto de aspas.

| Tipo de dados | Descrição | Exemplo | Resolve para |
|:--|:--|:--|:--|
| Cadeia   | Coloque o valor entre aspas duplas.  | "\"Olá mundo\"" | "Olá mundo" |
| um valor numérico  | Valor numérico com plicas.| "64" | 64 |
| Valor booleano  | **Verdadeiro** ou **falso** aspas.  Tenha em atenção que este valor tem de estar em minúsculo. | "true" | VERDADEIRO |
| DateTime | Valor de data serializada.<br>Pode utilizar o cmdlet ConvertTo-Json no PowerShell para gerar este valor para uma data específica.<br>Exemplo: get-data "24/5/2017 13:14:57" \| ConvertTo-Json | "\\/Date(1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Módulos
A solução de gestão não é necessário definir [módulos globais](../automation/automation-integration-modules.md) utilizado pelos seus runbooks porque estes estarão sempre disponíveis na sua conta de automatização.  Tem de incluir um recurso para qualquer outro módulo utilizado pelos seus runbooks.

[Módulos de integração](../automation/automation-integration-modules.md) ter um tipo de **Microsoft.Automation/automationAccounts/modules** e ter a seguinte estrutura.  Isto inclui os parâmetros e variáveis comuns para que possam copie e cole o fragmento de código no seu ficheiro de solução e alterar os nomes de parâmetro.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
      "type": "Microsoft.Automation/automationAccounts/modules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "dependsOn": [
      ],
      "properties": {
        "contentLink": {
          "uri": "[variables('Module').Uri]"
        }
      }
    }


As propriedades de recursos do módulo são descritas na seguinte tabela.

| Propriedade | Descrição |
|:--- |:--- |
| contentLink |Especifica o conteúdo do módulo. <br><br>URI - Uri para o conteúdo do módulo.  Este será um ficheiro. ps1 para runbooks do PowerShell e scripts e um ficheiro de runbook gráfico exportado para um runbook de gráfico.  <br> versão - versão do módulo para o seu próprio controlo. |

O runbook deve dependem do recurso do módulo para se certificar de que foi criado antes do runbook.

### <a name="updating-modules"></a>Módulos de atualização
Se atualizar uma solução de gestão que inclua um runbook que utiliza uma agenda e a nova versão da sua solução tem um novo módulo utilizado por esse runbook, o runbook pode utilizar a versão antiga do módulo.  Deve incluir os seguintes runbooks na sua solução e criar uma tarefa para executá-los antes de quaisquer outros runbooks.  Isto irá garantir que os módulos são atualizados como necessários antes dos runbooks são carregados.

* [Atualização ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) será Certifique-se de que todos os módulos utilizados pelo runbooks na sua solução a versão mais recente.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) irá voltar a registar todos os recursos de agenda para se certificar de que os runbooks ligados aos mesmos com utilização de módulos mais recentes.




## <a name="sample"></a>Exemplo
Segue-se um exemplo de uma solução que incluem que inclui os seguintes recursos:

- Runbook.  Este é um runbook de exemplo armazenado num repositório GitHub público.
- Tarefa de automatização que inicia o runbook quando a solução está instalada.
- Agendamento e agendamento de trabalhos para iniciar o runbook em intervalos regulares.
- Certificado.
- Credencial.
- Variável.
- Módulo.  Este é o [OMSIngestionAPI módulo](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) para escrever dados para análise de registos. 

Este exemplo utiliza [parâmetros de solução padrão](operations-management-suite-solutions-solution-file.md#parameters) variáveis que frequentemente deverá ser utilizadas numa solução, por oposição a valores de codificar nas definições de recursos.


    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Log Analytics workspace."
          }
        },
        "accountName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Automation account."
          }
        },
        "workspaceregionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Log Analytics workspace."
          }
        },
        "regionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Automation account."
          }
        },
        "pricingTier": {
          "type": "string",
          "metadata": {
            "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account."
          }
        },
        "certificateBase64Value": {
          "type": "string",
          "metadata": {
            "Description": "Base 64 value for certificate."
          }
        },
        "certificateThumbprint": {
          "type": "securestring",
          "metadata": {
            "Description": "Thumbprint for certificate."
          }
        },
        "credentialUsername": {
          "type": "string",
          "metadata": {
            "Description": "Username for credential."
          }
        },
        "credentialPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Password for credential."
          }
        },
        "scheduleStartTime": {
          "type": "string",
          "metadata": {
            "Description": "Start time for shedule."
          }
        },
        "scheduleTimeZone": {
          "type": "string",
          "metadata": {
            "Description": "Time zone for schedule."
          }
        },
        "scheduleLinkGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the schedule link to runbook.",
            "control": "guid"
          }
        },
        "runbookJobGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the runbook job.",
            "control": "guid"
          }
        }
      },
      "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
    
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31",
    
        "Runbook": {
          "Name": "MyRunbook",
          "Description": "Sample runbook",
          "Type": "PowerShell",
          "Uri": "https://raw.githubusercontent.com/user/myrepo/master/samples/MyRunbook.ps1",
          "JobGuid": "[parameters('runbookJobGuid')]"
        },
    
        "Certificate": {
          "Name": "MyCertificate",
          "Base64Value": "[parameters('certificateBase64Value')]",
          "Thumbprint": "[parameters('certificateThumbprint')]"
        },
    
        "Credential": {
          "Name": "MyCredential",
          "UserName": "[parameters('credentialUsername')]",
          "Password": "[parameters('credentialPassword')]"
        },
    
        "Schedule": {
          "Name": "MySchedule",
          "Description": "Sample schedule",
          "IsEnabled": "true",
          "Interval": "1",
          "Frequency": "hour",
          "StartTime": "[parameters('scheduleStartTime')]",
          "TimeZone": "[parameters('scheduleTimeZone')]",
          "LinkGuid": "[parameters('scheduleLinkGuid')]"
        },
    
        "Variable": {
          "Name": "MyVariable",
          "Description": "Sample variable",
          "Encrypted": 0,
          "Type": "string",
          "Value": "'This is my string value.'"
        },
    
        "Module": {
          "Name": "OMSIngestionAPI",
          "Uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
        }
      },
      "resources": [
        {
          "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
          "location": "[parameters('workspaceRegionId')]",
          "tags": { },
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "[variables('LogAnalyticsApiVersion')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
            "referencedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
            ],
            "containedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]"
            ]
          },
          "plan": {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
            "Version": "[variables('SolutionVersion')]",
            "product": "[variables('ProductName')]",
            "publisher": "[variables('SolutionPublisher')]",
            "promotionCode": ""
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
              "uri": "[variables('Runbook').Uri]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').JobGuid)]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('Runbook').Name]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
          "type": "Microsoft.Automation/automationAccounts/certificates",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "Base64Value": "[variables('Certificate').Base64Value]",
            "Thumbprint": "[variables('Certificate').Thumbprint]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
          "type": "Microsoft.Automation/automationAccounts/credentials",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "userName": "[variables('Credential').UserName]",
            "password": "[variables('Credential').Password]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
          "type": "microsoft.automation/automationAccounts/schedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Schedule').Description]",
            "startTime": "[variables('Schedule').StartTime]",
            "timeZone": "[variables('Schedule').TimeZone]",
            "isEnabled": "[variables('Schedule').IsEnabled]",
            "interval": "[variables('Schedule').Interval]",
            "frequency": "[variables('Schedule').Frequency]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
          "type": "microsoft.automation/automationAccounts/jobSchedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
          ],
          "tags": {
          },
          "properties": {
            "schedule": {
              "name": "[variables('Schedule').Name]"
            },
            "runbook": {
              "name": "[variables('Runbook').Name]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
          "type": "microsoft.automation/automationAccounts/variables",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Variable').Description]",
            "isEncrypted": "[variables('Variable').Encrypted]",
            "type": "[variables('Variable').Type]",
            "value": "[variables('Variable').Value]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
          "type": "Microsoft.Automation/automationAccounts/modules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "properties": {
            "contentLink": {
              "uri": "[variables('Module').Uri]"
            }
          }
        }
    
      ],
      "outputs": { }
    }




## <a name="next-steps"></a>Passos seguintes
* [Adicionar uma vista à sua solução](operations-management-suite-solutions-resources-views.md) para visualizar os dados recolhidos.
