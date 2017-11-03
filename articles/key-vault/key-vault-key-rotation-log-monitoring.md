---
title: "Configurar o Cofre de chaves do Azure com a auditoria e rotação de chave ponto-a-ponto | Microsoft Docs"
description: "Utilize este procedimentos para ajudar a obter configurado com a rotação da chave e monitorização registos do Cofre de chaves."
services: key-vault
documentationcenter: 
author: swgriffith
manager: mbaldwin
tags: 
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: jodehavi;stgriffi
ms.openlocfilehash: f98ba1e2da6924476392948a4d18c807d68e39e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-azure-key-vault-with-end-to-end-key-rotation-and-auditing"></a>Configurar o Cofre de Chaves do Azure com auditoria e rotação de ponto a ponto
## <a name="introduction"></a>Introdução
Depois de criar o seu Cofre de chaves, poderá começar a utilizar esse cofre para armazenar as chaves e segredos. As aplicações já não é necessário manter as chaves ou segredos, mas em vez disso irá solicitá-los do Cofre de chaves conforme necessário. Isto permite-lhe atualizar as chaves e segredos sem afetar o comportamento da sua aplicação, o qual abre um leque de possibilidades em torno da sua chave e o segredo gestão.

Este artigo explica-se um exemplo de como utilizar o Cofre de chaves do Azure para armazenar um segredo, neste caso, uma chave de conta de armazenamento do Azure que é acedida por uma aplicação. -Também demonstra a implementação de uma agendada rotação dessa chave de conta de armazenamento. Por fim,-explica uma demonstração sobre como monitorizar os registos de auditoria do Cofre de chaves e um alerta para quando são efetuados os pedidos inesperados.

> [!NOTE]
> Este tutorial não se destina a explicar em detalhe a configuração inicial do seu Cofre de chaves. Para obter estas informações, consulte o artigo [Introdução ao Cofre de Chaves do Azure](key-vault-get-started.md). Para obter instruções de Interface de linha de comandos de várias plataformas, consulte [gerir o Cofre de chaves ao utilizar a CLI](key-vault-manage-with-cli2.md).
>
>

## <a name="set-up-key-vault"></a>Configurar o Cofre de Chaves
Para ativar uma aplicação obter um segredo do Cofre de chaves, tem primeiro de criar o segredo e carregá-la para o cofre. Isto pode ser feito ao iniciar uma sessão do PowerShell do Azure e o início de sessão na sua conta do Azure com o seguinte comando:

```powershell
Login-AzureRmAccount
```

Na janela pop-up do browser, introduza o seu nome de utilizador da conta do Azure e a palavra-passe. PowerShell irá obter todas as subscrições que estão associadas esta conta. PowerShell utilizará a primeira por predefinição.

Se tiver várias subscrições, terá de especificar o que foi utilizado para criar o seu Cofre de chaves. Introduza o seguinte procedimento para ver as subscrições da sua conta:

```powershell
Get-AzureRmSubscription
```

Para especificar a subscrição associado ao Cofre de chaves que irá registar, introduza:

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID>
```

Porque este artigo demonstra armazenar uma chave de conta de armazenamento como um segredo, tem de obter essa chave de conta de armazenamento.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Após a obtenção do segredo (neste caso, a chave de conta de armazenamento), tem de converter uma cadeia segura que e, em seguida, criar um segredo com esse valor no seu Cofre de chaves.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
Em seguida, obter o URI para o segredo que criou. Isto é utilizado num passo posterior ao chamar o Cofre de chaves para obter o seu segredo. Execute o seguinte comando do PowerShell e anote o valor de ID, o que é o URI de segredos:

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Configurar a aplicação
Agora que tem um segredo armazenado, pode utilizar o código para obter e utilizá-lo. Existem alguns passos necessários para atingir esse objetivo. O primeiro passo e o mais importante é a registar a aplicação no Azure Active Directory e, em seguida, informar o Cofre de chaves informações da sua aplicação para que pode permitir pedidos a partir da sua aplicação.

> [!NOTE]
> A aplicação tem de ser criada no mesmo inquilino do Azure Active Directory como o seu Cofre de chaves.
>
>

Abra o separador aplicações do Azure Active Directory.

![Aplicações abertas no Azure Active Directory](./media/keyvault-keyrotation/AzureAD_Header.png)

Escolha **adicionar** para adicionar uma aplicação para o Azure Active Directory.

![Escolher adicionar](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

Deixe o tipo de aplicação como **aplicação WEB e/ou API WEB** e atribua um nome de aplicação.

![Nome da aplicação](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

Dê a sua aplicação um **URL de início de sessão no** e um **URI de ID de aplicação**. Estes podem ser qualquer coisa que pretende para esta demonstração, e pode ser alterados posteriormente se for necessário.

![Fornecer URIs necessária](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

Depois da aplicação é adicionada ao Azure Active Directory, será reencaminhado para a página da aplicação. Clique em de **configurar** separador e, em seguida, localize e copie o **ID de cliente** valor. Anote o ID de cliente para os passos seguintes.

Em seguida, gere uma chave para a sua aplicação, para que possa interagir com o Azure Active Directory. Pode criar este sob o **chaves** secção o **configuração** separador. Tome nota da chave recentemente criado da sua aplicação do Azure Active Directory para utilização num passo posterior.

![Chaves de aplicação do Azure Active Directory](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Antes de estabelecer quaisquer chamadas a partir da sua aplicação para o Cofre de chaves, tem de indicar o Cofre de chaves sobre a sua aplicação e as respetivas permissões. O comando seguinte assume o nome do cofre e o ID de cliente da sua aplicação do Azure Active Directory e atribui **obter** acesso ao seu Cofre de chaves para a aplicação.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Neste momento, está pronto para começar a criar as suas chamadas de aplicação. Na sua aplicação, tem de instalar os pacotes de NuGet necessários para interagir com o Cofre de chaves do Azure e o Azure Active Directory. Na consola do Gestor de pacotes do Visual Studio, introduza os seguintes comandos. Na escrita deste artigo, a versão atual do pacote do Azure Active Directory é 3.10.305231913, pelo que pode querer confirmar a versão mais recente e atualizar em conformidade.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

No código da aplicação, crie uma classe para conter o método para a autenticação do Azure Active Directory. Neste exemplo, se denomina dessa classe **Utils**. Adicione o seguinte ao utilizar a instrução:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Em seguida, adicione o seguinte método para obter o token JWT do Azure Active Directory. Para maintainability, poderá querer mover os valores de cadeia hard-coded para a configuração da web ou aplicação.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Adicione o código necessário ligar para o Cofre de chaves e obter o valor secreto. Primeiro tem de adicionar o seguinte utilizando a instrução:

```csharp
using Microsoft.Azure.KeyVault;
```

Adicione as chamadas de método para invocar o Cofre de chaves e obter o seu segredo. Este método, deve fornecer o segredo do URI que guardou no passo anterior. Tenha em atenção a utilização do **GetToken** método o **Utils** classe criada anteriormente.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Quando executar a sua aplicação, deve agora ter autenticação no Azure Active Directory e, em seguida, obter o valor do seu segredo do Cofre de chaves do Azure.

## <a name="key-rotation-using-azure-automation"></a>Rotação da chave através da automatização do Azure
Existem várias opções para implementar uma estratégia de rotação para valores que armazene como segredos do Cofre de chaves do Azure. Podem ser rotação segredos como parte de um processo manual, poderá ser rotação através de programação utilizando chamadas de API ou pode ser rotação a título de um script de automatização. Para efeitos deste artigo, irá utilizar Azure PowerShell combinada com a automatização do Azure para alterar uma chave de acesso da conta de armazenamento do Azure. Em seguida, atualizar um segredo do Cofre de chaves com essa nova chave.

Para permitir a automatização do Azure para definir valores secretos no seu Cofre de chaves, tem de obter o ID de cliente para a ligação com o nome AzureRunAsConnection, que foi criada quando estabelecer a instância de automatização do Azure. Pode encontrar este ID escolhendo **ativos** da sua instância de automatização do Azure. A partir daí, escolha **ligações** e, em seguida, selecione o **AzureRunAsConnection** princípio de serviço. Tome nota do **ID da aplicação**.

![ID de cliente de automatização do Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

No **ativos**, escolha **módulos**. De **módulos**, selecione **galeria**e, em seguida, procure e **importação** atualizado de versões de cada uma das seguintes módulos:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage


> [!NOTE]
> Na escrita deste artigo, só os módulos apontou anteriormente necessárias sejam atualizados para o seguinte script. Se achar que a tarefa de automatização está a falhar, certifique-se de que importou todos os módulos necessários e as respetivas dependências.
>
>

Depois de ter obtido o ID da aplicação para a sua ligação de automatização do Azure, tem de saber seu Cofre de chaves que esta aplicação tem acesso para atualizar os segredos no seu cofre. Isto pode ser conseguido com o seguinte comando do PowerShell:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Em seguida, selecione **Runbooks** na sua instância de automatização do Azure e, em seguida, selecione **adicionar um Runbook**. Selecione **Criação Rápida**. Nome do runbook e selecione **PowerShell** como o tipo de runbook. Tem a opção para adicionar uma descrição. Por fim, clique em **criar**.

![Criar o runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Cole o seguinte script do PowerShell no painel do editor para o seu runbook novo:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

#Optionally you may set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

No painel editor, escolha **painel de teste** para testar o script. Depois do script é executado sem erros, pode selecionar **publicar**, e, em seguida, pode aplicar uma agenda para o runbook no painel de configuração de runbook.

## <a name="key-vault-auditing-pipeline"></a>Pipeline de auditoria do Cofre de chaves
Quando configurar um cofre de chaves, pode ativar a auditoria para recolher registos de pedidos de acesso efetuados ao Cofre de chaves. Estes registos são armazenados numa conta do Storage do Azure designada e podem ser solicitados, monitorizado e analisados. O cenário seguinte utiliza as funções do Azure, as aplicações lógicas do Azure e os registos de auditoria do Cofre de chaves para criar um pipeline para enviar um e-mail quando uma aplicação que corresponde ao ID de aplicação da aplicação web obtém os segredos do cofre.

Em primeiro lugar, tem de ativar o registo no seu Cofre de chaves. Isto pode ser feito através dos seguintes comandos do PowerShell (detalhes completos podem ser vistos em [registo do Cofre de chave](key-vault-logging.md)):

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>'
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Depois de esta opção estiver ativada, os registos de auditoria iniciar recolha para a conta do storage designada. Estes registos contém eventos sobre como e quando os seus cofres de chaves são acedidos e por quem.

> [!NOTE]
> Pode aceder as informações de registo 10 minutos após a operação do Cofre de chaves. Normalmente, será ainda mais rápido.
>
>

O passo seguinte consiste em [criar uma fila do Service Bus do Azure](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Este é onde os registos de auditoria do Cofre de chaves são enviadas por push. Quando as mensagens de registo de auditoria na fila de espera, a aplicação lógica escolherá-los e funciona nos mesmos. Crie um barramento de serviço com os seguintes passos:

1. Crie um espaço de nomes de barramento de serviço (se já tiver um que pretende utilizar para este, avance para o passo 2).
2. Navegue para o service bus no portal do Azure e selecione o espaço de nomes que pretende criar a fila.
3. Selecione **novo** e escolha **Service Bus > fila** e introduza os detalhes necessários.
4. Selecione as informações de ligação do Service Bus de escolher o espaço de nomes e clicando em **informações de ligação**. Estas informações serão necessárias para a secção seguinte.

Em seguida, [criar uma função do Azure](../azure-functions/functions-create-first-azure-function.md) para consultar os registos do Cofre de chaves dentro da conta de armazenamento e processará novos eventos. Esta será uma função que é acionada com base numa agenda.

Para criar uma função do Azure, escolha **novo > aplicação de função** no portal do Azure. Durante a criação, pode utilizar um plano de alojamento existente ou crie um novo. Também pode escolher para o alojamento dinâmico. Obter mais detalhes sobre as opções de alojamento de função podem ser encontrados em [como dimensionar as funções do Azure](../azure-functions/functions-scale.md).

Quando é criada a função do Azure, navegue para a mesma e escolha um temporizador função e C\#. Em seguida, clique em **criar esta função**.

![Painel de início de funções do Azure](./media/keyvault-keyrotation/Azure_Functions_Start.png)

No **desenvolver** separador, substitua o código de run.csx com o seguinte:

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //required to order by time as they may not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```


> [!NOTE]
> Certifique-se de que substitui as variáveis no código anterior para apontar para a conta do storage onde os registos do Cofre de chaves são escritos, o barramento de serviço que criou anteriormente e o caminho para os registos de armazenamento do Cofre de chaves específico.
>
>

A função escolherá o ficheiro de registo mais recente da conta do storage onde os registos do Cofre de chaves são escritos, grabs dos eventos mais recentes esse ficheiro e envia-os para uma fila do Service Bus. Uma vez que um único ficheiro pode ter vários eventos, deve criar um ficheiro de sync.txt que a função, também observa para determinar o carimbo de hora do último evento que foi captado. Isto garante que não a emitir o mesmo evento várias vezes. Este ficheiro sync.txt contém um carimbo para o último evento foi encontrado. Os registos, quando carregadas, tem de ser ordenada com base no timestamp para se certificar de que estão ordenados corretamente.

Para esta função, podemos fazer referência a algumas das bibliotecas adicionais que não estão disponíveis Box nas funções do Azure. Para incluir estes, precisamos das funções do Azure para solicitar-lhes através do NuGet. Escolha o **ver ficheiros** opção.

![Ver a opção de ficheiros](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

E adicionar um ficheiro chamado project.json com o seguinte conteúdo:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```
Após **guardar**, as funções do Azure irá transferir os binários necessários.

Mudar para o **integrar** separador e dê um nome significativo para utilizar a função de parâmetro do temporizador. No código anterior, espera do temporizador ser chamado *myTimer*. Especifique um [expressão CRON](../app-service/web-sites-create-web-jobs.md#CreateScheduledCRON) da seguinte forma: 0 \* \* \* \* \* para o temporizador que fará com que a função ser executada uma vez um minuto.

No mesmo **integrar** separador, adicione uma entrada do tipo **Blob Storage do Azure**. Isto irá apontar para o ficheiro de sync.txt que contém o carimbo de último evento consultou pela função. Estarão disponível na função pelo nome do parâmetro. No código anterior, a entrada do Blob Storage do Azure espera o nome de parâmetro a ser *inputBlob*. Selecione a conta de armazenamento onde o ficheiro sync.txt irá residir (poderia ser o mesmo ou uma conta de armazenamento diferente). No campo do caminho, forneça o caminho onde o ficheiro se encontra no formato {container-name}/path/to/sync.txt.

Adicionar uma saída do tipo *Blob Storage do Azure* saída. Isto irá apontar para o ficheiro de sync.txt definido na entrada. Isto é utilizado pela função escrever timestamp do último evento consultou. O código anterior espera que este parâmetro denominado *outputBlob*.

Neste momento, a função está pronta. Certifique-se mudar para o **desenvolver** separador e guarde o código. Verifique a janela de saída para quaisquer erros de compilação e corrija-os em conformidade. Se compila o código, em seguida, o código deve agora a cada minuto verificar os registos do Cofre de chaves e enviar quaisquer novos eventos para a fila de barramento de serviço definido. Deverá ver informações de registo escrever para a janela de registo a cada vez que a função é acionada.

### <a name="azure-logic-app"></a>Aplicação lógica do Azure
Em seguida tem de criar uma aplicação de lógica do Azure que seleciona os eventos que a função é enviar para a fila do Service Bus, analisa o conteúdo e envia um e-mail com base numa condição que está a ser correspondida.

[Criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md) acedendo a **novo > aplicação lógica**.

Uma vez criada a aplicação lógica, navegue para a mesma e escolha **editar**. Dentro do editor de aplicação lógica, escolha **fila do Service Bus** e introduza as suas credenciais do Service Bus a ligá-lo para a fila.

![Barramento de serviço de aplicações lógicas do Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Em seguida, escolha **adicionar uma condição**. A condição, mude para o editor de avançadas e introduza o código seguinte, substituindo APP_ID com o APP_ID real da sua aplicação web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Esta expressão essencialmente devolve **falso** se o *appid* partir o evento de entrada (que é o corpo da mensagem de Service Bus) não é o *appid* da aplicação.

Agora, crie uma ação em **se não, não fazer nada**.

![Ação de escolha de aplicação de lógica do Azure](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Para a ação, escolha **Office 365 – enviar e-mail**. Preencha os campos para criar uma mensagem de e-mail para enviar quando regressar à condição definida **falso**. Se não tiver o Office 365, pode observar alternativas para alcançar os mesmos resultados.

Neste momento, tem um pipeline de ponto a ponto que procura novos registos de auditoria do Cofre de chaves, uma vez um minuto. Novos registos para localizar este empurra para uma fila de barramento de serviço. A aplicação lógica é acionada quando seja colocado no fila uma nova mensagem sítio. Se o *appid* dentro do evento não corresponde a aplicação de chamada, o ID da aplicação, envia uma mensagem de e-mail.
