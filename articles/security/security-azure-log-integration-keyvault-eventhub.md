---
title: Integrar os registos do Cofre de chaves do Azure com os Hubs de eventos | Microsoft Docs
description: "Tutorial que fornece os passos necessários para que os registos do Cofre de chaves fique disponível para um SIEM utilizando a integração de registo do Azure"
services: security
author: barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.topic: article
ms.date: 08/07/2017
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 3cd80817bf8b2ef2f66e9942eddc186a3eb5b5e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Tutorial de integração de registo do Azure: eventos de Cofre de chaves do Azure de processo ao utilizar os Hubs de eventos

Pode utilizar a integração de registo do Azure para obter eventos registados e disponibilizá-los ao sistema de gestão (SIEM) de segurança as informações e eventos. Este tutorial mostra um exemplo de como a integração de registo do Azure podem ser utilizada para processar registos que são adquiridos através do Event Hubs do Azure.
 
Utilize este tutorial para obter acquainted com como o registo a integração do Azure e do Event Hubs funcionam em conjunto, seguindo os passos de exemplo e compreender a forma como cada passo suporta a solução. Em seguida, pode tirar o que aprendeu aqui criar os seus próprios passos para suportar necessidades exclusivas da sua empresa.

>[!WARNING]
Os passos e comandos neste tutorial não se destina a ser copiados e colados. Se estiver a apenas exemplos. Não utilize os comandos do PowerShell "tal como está" no seu ambiente em direto. Devem ser personalizadas com base no seu ambiente exclusivo.


Este tutorial explica o processo de assumir registada para um hub de eventos de atividade de Cofre de chaves do Azure e tornando-a disponível como ficheiros JSON para o sistema SIEM. Em seguida, pode configurar o seu sistema SIEM para processar os ficheiros JSON.

>[!NOTE]
>A maior parte dos passos neste tutorial envolve configurar cofres de chaves, contas de armazenamento e dos event hubs. Os passos de integração de registo do Azure específicos são no final deste tutorial. Não execute estes passos num ambiente de produção. Estes destinam-se apenas num ambiente de laboratório. Tem de personalizar os passos antes de os utilizar na produção.

Informações fornecidas ao longo do percurso ajuda-o a compreender os motivos pelos quais atrás de cada passo. Ligações para outros artigos dão-lhe mais detalhes em determinados tópicos.

Para obter mais informações sobre os serviços que este tutorial menciona suportadas, consulte: 

- [Cofre de Chaves do Azure](../key-vault/key-vault-whatis.md)
- [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Integração de registos do Azure](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>Configuração inicial

Antes de concluir os passos neste artigo, precisa do seguinte:

1. Uma subscrição do Azure e a conta que subscrição com direitos de administrador. Se não tiver uma subscrição, pode criar um [conta gratuita](https://azure.microsoft.com/free/).
 
2. Um sistema com acesso à internet que cumpra os requisitos para instalar a integração de registo do Azure. O sistema pode ser num serviço em nuvem ou alojado no local.

3. [Integração de registo do Azure](https://www.microsoft.com/download/details.aspx?id=53324) instalado. Para instalá-lo:

   a. Utilize o ambiente de trabalho remoto para ligar ao sistema mencionado no passo 2.   
   b. Copie o instalador de integração de registo do Azure para o sistema. Pode [transferir os ficheiros de instalação](https://www.microsoft.com/download/details.aspx?id=53324).   
   c. Iniciar o instalador e aceite os termos de licenciamento de Software Microsoft.   
   d. Se irá fornecer informações de telemetria, deixe a caixa de verificação selecionada. Se em vez disso não enviaria informações de utilização à Microsoft, desmarque a caixa de verificação.
   
   Para obter mais informações sobre a integração de registo do Azure e como instalá-lo, consulte [a integração de registo do Azure com o registo de diagnóstico do Azure e o reencaminhamento de eventos do Windows](security-azure-log-integration-get-started.md).

4. A versão mais recente do PowerShell.
 
   Se tiver instalado o Windows Server 2016, em seguida, tiver, pelo menos, PowerShell 5.0. Se estiver a utilizar qualquer outra versão do Windows Server, poderá ter uma versão anterior do PowerShell instalada. Pode verificar a versão introduzindo ```get-host``` numa janela do PowerShell. Se não tiver PowerShell 5.0 instalado, pode [transferi-lo](https://www.microsoft.com/download/details.aspx?id=50395).

   Depois de ter, pelo menos, PowerShell 5.0, pode avançar para instalar a versão mais recente:
   
   a. Na janela do PowerShell, introduza o ```Install-Module Azure``` comando. Conclua os passos de instalação.    
   b. Introduza o ```Install-Module AzureRM``` comando. Conclua os passos de instalação.

   Para obter mais informações, consulte [instalar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0).


## <a name="create-supporting-infrastructure-elements"></a>Criar suporte de infraestrutura de elementos

1. Abra uma janela elevada do PowerShell e aceda a **C:\Program Files\Microsoft Azure registo integração**.
2. Importe os cmdlets de AzLog executando o script LoadAzLogModule.ps1. Introduza o `.\LoadAzLogModule.ps1` comando. (Tenha em atenção a ". \ \" nesse comando.) Deverá ver algo semelhante ao seguinte:</br>

   ![Lista de módulos carregados](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

3. Introduza o `Login-AzureRmAccount` comando. Na janela do início de sessão, introduza as informações de credenciais para a subscrição que irá utilizar para este tutorial.

   >[!NOTE]
   >Se esta for a primeira vez que está a iniciar sessão no Azure desta máquina, verá uma mensagem sobre Microsoft para recolher dados de utilização do PowerShell. Recomendamos que ative a recolha de dados porque será utilizado para melhorar o Azure PowerShell.

4. Após a autenticação com êxito, tem sessão iniciada e ver as informações na captura de ecrã seguinte. Tome nota do ID de subscrição e do nome da subscrição, porque terá-los para concluir os passos posteriores.

   ![Janela do PowerShell](./media/security-azure-log-integration-keyvault-eventhub/login-azurermaccount.png)
5. Crie variáveis para armazenar os valores que serão utilizados mais tarde. Introduza cada um dos seguintes linhas de PowerShell. Poderá ter de ajustar os valores para corresponder ao seu ambiente.
    - ```$subscriptionName = ‘Visual Studio Ultimate with MSDN’```(O nome da sua subscrição poderão ser diferente. Pode vê-lo como parte do resultado do comando anterior.)
    - ```$location = 'West US'```(Esta variável será utilizada para transmitir a localização onde os recursos deverá ser criados. Pode alterar esta variável para ser de qualquer localização da sua escolha.)
    - ```$random = Get-Random```
    - ``` $name = 'azlogtest' + $random```(O nome pode ser qualquer coisa, mas deve incluir apenas letras minúsculas e números).
    - ``` $storageName = $name```(Esta variável será utilizada para o nome da conta de armazenamento).
    - ```$rgname = $name ```(Esta variável será utilizada para o nome do grupo de recursos).
    - ``` $eventHubNameSpaceName = $name```(Este é o nome do espaço de nomes de hub de eventos.)
6. Especifique a subscrição que irá ser-lhe trabalhar com:
    
    ```Select-AzureRmSubscription -SubscriptionName $subscriptionName```
7. Criar um grupo de recursos:
    
    ```$rg = New-AzureRmResourceGroup -Name $rgname -Location $location```
    
   Se introduzir `$rg` neste momento, deverá ver um resultado semelhante a esta captura de ecrã:

   ![Saída após a criação de um grupo de recursos](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
8. Crie uma conta de armazenamento que será utilizada para controlar as informações de estado:
    
    ```$storage = New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
9. Crie o espaço de nomes do hub de eventos. Isto é necessário para criar um hub de eventos.
    
    ```$eventHubNameSpace = New-AzureRmEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
10. Obter o ID da regra que irá ser utilizado com o fornecedor de informações:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey' ```
11. Obter todas as localizações do Azure possíveis e adicione os nomes a uma variável que pode ser utilizada num passo posterior:
    
    a. ```$locationObjects = Get-AzureRMLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Se introduzir `$locations` neste momento, pode ver os nomes de localização sem informações adicionais devolvidas pelo Get-AzureRmLocation.
12. Crie um perfil de registo do Gestor de recursos do Azure: 
    
    ```Add-AzureRmLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Para obter mais informações sobre o perfil de registos do Azure, consulte [descrição geral do registo de atividade do Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

> [!NOTE]
> Poderá receber uma mensagem de erro ao tentar criar um perfil de registo. Em seguida, poderá consultar a documentação para o Get-AzureRmLogProfile e Remove-AzureRmLogProfile. Se executar Get-AzureRmLogProfile, pode ver informações sobre o perfil de registo. Pode eliminar o perfil de registo existente, introduzindo o ```Remove-AzureRmLogProfile -name 'Log Profile Name' ``` comando.
>
>![Erro de perfil do Gestor de recursos](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

1. Crie o Cofre de chaves:

   ```$kv = New-AzureRmKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location ```

2. Configure o registo para o Cofre de chaves:

   ```Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true ```

## <a name="generate-log-activity"></a>Gerar a atividade de registo

Pedidos têm de ser enviadas para o Cofre de chaves para gerar a atividade de registo. Ações, como a geração de chaves, armazenar segredos, ou ao ler os segredos do Cofre de chaves, irá criar entradas de registo.

1. Apresenta as chaves de armazenamento atual:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
2. Gerar um novo **key2**:
    
   ```New-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
3. Voltar a apresentar as chaves e ver que **key2** contém um valor diferente:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
4. Defina e ler um segredo para gerar entradas de registo adicionais:
    
   a. ```Set-AzureKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzureKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Devolveu secreta](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Configurar a integração de registos do Azure

Agora que configurou todos os elementos necessários para que o Cofre de chave de registo para um hub de eventos, terá de configurar a integração de registo do Azure:

1. ```$storage = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename```
2. ```$eventHubKey = Get-AzureRmEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
3. ```$storagekeys = Get-AzureRmStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
4. ``` $storagekey = $storagekeys[0].Value```

Execute o comando AzLog para cada hub de eventos:

1. ```$eventhubs = Get-AzureRmEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
2. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Depois de um minuto ou executar os dois últimos comandos, deve ver ficheiros JSON que está a ser gerados. Pode confirmar que, ao monitorizar o diretório **C:\users\AzLog\EventHubJson**.

## <a name="next-steps"></a>Passos seguintes

- [Integração de registos do Azure FAQ](security-azure-log-integration-faq.md)
- [Introdução à integração do registo do Azure](security-azure-log-integration-get-started.md)
- [Integrar o seu sistemas SIEM registos de recursos do Azure](security-azure-log-integration-overview.md)
