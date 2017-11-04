---
title: "O meu primeiro runbook do Python na automatização do Azure | Microsoft Docs"
description: "Tutorial orienta-o através da criação, teste e da publicação de um runbook simples do Python."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: eslesar
ms.openlocfilehash: 4e7b3049fff76c86956e08d71b22a0f8dbf55b0e
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="my-first-python-runbook"></a>O meu primeiro runbook do Python

> [!div class="op_single_selector"]
> * [Gráficos](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [python](automation-first-runbook-textual-python2.md)
> 
> 

Este tutorial orienta-o através da criação de um [Python runbook](automation-runbook-types.md#python-runbooks) na automatização do Azure. Começamos com um runbook simples que testamos e publicamos enquanto explicamos como controlar o estado da tarefa de runbook. Em seguida, modificamos o runbook para gerir recursos do Azure, neste caso, iniciar uma máquina virtual do Azure. Por último, tornamos o runbook mais robusto ao adicionar parâmetros do runbook.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-offering-get-started.md) para manter o runbook e autenticar-se nos recursos do Azure.  Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Paramos e iniciamos esta máquina, pelo que não deve ser uma VM de produção.

## <a name="create-a-new-runbook"></a>Criar um novo runbook

Começamos por criar um runbook simples que produz o texto *Hello World*.

1. No portal do Azure, abra a sua conta da Automatização.
   A página da conta da Automatização dá-lhe uma vista rápida dos recursos nesta conta. Já deverá ter alguns recursos. A maioria destes são os módulos que estão incluídos automaticamente na nova conta da Automatização. Também deverá ter o recurso da Credencial que está mencionado nos [Pré-requisitos](#prerequisites).
1. Clique no mosaico **Runbooks** para abrir a lista de runbooks.
   ![RunbooksControl](media/automation-first-runbook-textual-python/runbooks-control-tiles.png)
1. Crie um runbook novo ao clicar no botão **Adicionar um runbook** e, em seguida, **Criar um runbook novo**.
1. Atribua ao runbook o nome *MyFirstRunbook-Python*.
1. Neste caso, vamos criar um [Python runbook](automation-runbook-types.md#python-runbooks) , por isso, selecione **Python 2** para **tipo de Runbook**.
1. Clique em **Criar** para criar o runbook e abra o editor de texto.

## <a name="add-code-to-the-runbook"></a>Adicionar código ao runbook

Agora vamos adicionar um comando para imprimir o texto "Olá mundo" simple:

```python
print("Hello World!")
```

Clique em **guardar** para guardar o runbook.

## <a name="test-the-runbook"></a>Testar o runbook

Antes de publicarmos o runbook para o disponibilizar na produção, queremos testá-lo para garantir que funciona corretamente. Quando testa um runbook, executa a versão de **Rascunho** e veja o resultado de forma interativa.

1. Clique em **Painel de teste** para abrir o Painel de teste.
   ![Painel de Teste](media/automation-first-runbook-textual-python/automation-runbook-edit-controls-test.png)
1. Clique em **Iniciar** para iniciar o teste. Esta deve ser a única opção ativada.
1. Uma [tarefa do runbook](automation-runbook-execution.md) é criada e o respetivo estado é apresentado.
   O estado da tarefa começa como *Em fila* com a indicação de que está à espera que uma função de trabalho de runbook na cloud fique disponível. Em seguida, irá mudar para *A iniciar* quando uma função de trabalho reivindicar a tarefa e, em seguida, *A executar* quando o runbook começar a ser executado.
1. Quando a tarefa de runbook tiver concluído, o resultado é apresentado. No nosso caso, deveremos ver *Olá, Mundo*.
1. Feche o painel de Teste para voltar à tela.

## <a name="publish-and-start-the-runbook"></a>Publicar e iniciar o runbook

O runbook que criamos ainda está em modo de rascunho. Precisamos de o publicar antes de o executarmos na produção.
Quando publica um runbook, substituir a versão publicada existente com a versão de rascunho.
No nosso caso, ainda não temos uma versão publicada porque acabamos de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e, em seguida, em **Sim** quando lhe for pedido.
   ![Botão Publicar](media/automation-first-runbook-textual-python/automation-runbook-edit-controls-publish.png)
1. Se se deslocar para a esquerda para ver o runbook no painel **Runbooks** agora, esta operação irá mostrar o **Estado da Criação de Conteúdos** de **Publicado**.
1. Desloque-se para a direita para ver o painel para **MyFirstRunbook-Python**.
   As opções na parte superior permitem-nos iniciar o runbook, ver o runbook, agendar o seu início num momento futuro ou criar um [webhook](automation-webhooks.md) para que possa ser iniciado através de uma chamada HTTP.
1. Queremos iniciar o runbook, pelo que deverá clicar em **Iniciar** e, em seguida, clique em **Ok** quando o painel Iniciar Runbook for aberto.
1. É aberto um painel de tarefas para a tarefa de runbook que criámos. Podemos fechar este painel, mas neste caso, deixamos aberto para vermos o progresso da tarefa.
1. O estado da tarefa é mostrado no **Resumo da Tarefa** e corresponde aos estados que vimos quando testamos o runbook.
1. Quando o estado de runbook mostrar *Concluído*, clique em **Resultado**. Quando o painel Resultado for aberto e for possível ver *Olá, Mundo*.
1. Feche o painel Resultado.
1. Clique em **Todos os Registos** para abrir o painel Fluxos da tarefa de runbook. Apenas devemos conseguir ver *Olá, Mundo* no fluxo de saída, mas isto pode mostrar outros fluxos de uma tarefa de runbook, tais como Verboso e Erro se o runbook escrever nos mesmos.
1. Feche o painel fluxos e o painel Tarefas para regressar ao painel MyFirstRunbook-Python.
1. Clique em **Tarefas** para abrir o painel Tarefas para este runbook. Isto apresenta uma lista de todas as tarefas criadas por este runbook. Apenas devemos conseguir ver uma tarefa listada, uma vez que apenas executamos a tarefa uma vez.
1. Pode clicar nesta tarefa para abrir o mesmo painel Tarefas que visualizamos quando iniciamos o runbook. Isto permite-lhe voltar atrás no tempo e ver os detalhes de qualquer tarefa que foi criada para um determinado runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Adicionar autenticação para gerir recursos do Azure

Testamos e publicamos o nosso runbook, mas, até ao momento, não faz nada de útil. Queremos que efetue a gestão dos recursos do Azure.
Para gerir recursos do Azure, o script tem de autenticar com as credenciais da sua [conta de automatização](automation-offering-get-started.md).

> [!NOTE]
> A conta de automatização tem de ter sido criada com a funcionalidade principal de serviço para daí ser um certificado de runas.
> Se a sua conta de automatização não foi criada com o principal de serviço, pode autenticar utilizando o método descrito em [autenticar com as bibliotecas de gestão do Azure para Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate).

1. Abra o editor de texto clicando **editar** no painel MyFirstRunbook-Python.
1. Adicione o seguinte código para autenticar para o Azure:
   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Adicionar código para criar o cliente de computação de Python e iniciar a VM

Para trabalhar com as VMs do Azure, criar uma instância do [cliente computação do Azure para Python](https://docs.microsoft.com/python/api/azure.mgmt.compute.compute.computemanagementclient?view=azure-python).

Utilize o cliente de computação para iniciar a VM. Adicione o seguinte código ao runbook:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
azure_credential,
  str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start("MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Onde _MyResourceGroup_ é o nome do grupo de recursos que contém a VM e _TestVM_ é o nome da VM que pretende iniciar. 

Testar e executar o runbook novamente para ver que começa a VM.

## <a name="use-input-parameters"></a>Utilize os parâmetros de entrada

O runbook utiliza atualmente hard-coded valores para os nomes de grupo de recursos e a VM.
Agora vamos adicionar código que obtém estes valores de parâmetros de entrada.

Utilizamos o `sys.argv` variável para obter os valores de parâmetros.
Adicione o seguinte código ao runbook imediatamente após o outro `import` instruções:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Esta ação importa o `sys` módulo e cria duas variáveis para conter os nomes de grupo de recursos e a VM.
Tenha em atenção que o elemento da lista de argumentos, `sys.argv[0]`, é o nome do script e não seja a entrada pelo utilizador.

Agora, pode modificar as últimas duas linhas do runbook utilizará os valores de parâmetro de entrada em vez de utilizar valores hard-coded:

```python
async_vm_start = compute_client.virtual_machines.start(resource_group_name, vm_name)
async_vm_start.wait()
```

Quando inicia um runbook de Python (no **teste** painel ou como um runbook publicado), pode introduzir os valores para parâmetros de **iniciar Runbook** painel em **parâmetros**.

![Caixa de valor de parâmetro](media/automation-first-runbook-textual-python/runbook-python-param-highlight.png)

Depois de iniciar a introdução de um valor na primeira caixa, um segundo irá aparecer e assim sucessivamente, para que pode introduzir valores de parâmetro tantos conforme necessário.

Os valores estão disponíveis ao script como o `sys.argv` matriz como o código que acabou de ser adicionado.

Introduza o nome do grupo de recursos, como o valor para o primeiro parâmetro e o nome da VM para iniciar como o valor do segundo parâmetro.

![Introduza os valores de parâmetros](media/automation-first-runbook-textual-python/runbook-python-params.png)

Clique em **OK** para iniciar o runbook. O runbook é executado e começa a VM que especificou.

## <a name="next-steps"></a>Passos seguintes

* Para começar com runbooks do PowerShell, consulte [O meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md)
* Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
* Para saber mais sobre os tipos de runbook, as vantagens e limitações, consulte o artigo [Tipos de runbook da Automatização do Azure](automation-runbook-types.md)
* Para saber mais sobre como desenvolver para o Azure com o Python, consulte [do Azure para programadores do Python](https://docs.microsoft.com/en-us/python/azure/?view=azure-python).
* Para ver os runbooks do exemplo 2 do Python, consulte o [GitHub de automatização do Azure](https://docs.microsoft.com/en-us/python/azure/?view=azure-python).
