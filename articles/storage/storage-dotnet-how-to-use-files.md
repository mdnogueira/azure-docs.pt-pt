---
title: "Introdução ao Armazenamento de ficheiros do Azure no Windows | Microsoft Docs"
description: "Armazene dados de ficheiros na nuvem com o Armazenamento de ficheiros do Azure e monte a sua partilha de ficheiros na nuvem a partir de uma máquina virtual do Azure (VM) ou de uma aplicação no local com o Windows."
services: storage
documentationcenter: .net
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6a889ee1-1e60-46ec-a592-ae854f9fb8b6
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
/ms.date: 1/18/2017
ms.author: renash
translationtype: Human Translation
ms.sourcegitcommit: 550db52c2b77ad651b4edad2922faf0f951df617
ms.openlocfilehash: b4f13f1b5469ea3d3b2ab69e6435d3e7beb6ace8


---
# <a name="get-started-with-azure-file-storage-on-windows"></a>Introdução ao Armazenamento de ficheiros do Azure no Windows
[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

[!INCLUDE [storage-file-overview-include](../../includes/storage-file-overview-include.md)]

Para obter informações sobre a utilização do Armazenamento de ficheiros com o Linux, veja [How to use Azure File Storage with Linux (Como utilizar o File Storage do Azure com o Linux)](storage-how-to-use-files-linux.md).

Para obter informações sobre metas de desempenho e escalabilidade do Armazenamento de ficheiros, veja [Azure Storage Scalability and Performance Targets (Metas de Desempenho e Escalabilidade do Armazenamento do Azure)](storage-scalability-targets.md#scalability-targets-for-blobs-queues-tables-and-files).

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[!INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

## <a name="video-using-azure-file-storage-with-windows"></a>Vídeo: utilizar o Armazenamento de ficheiros do Azure com o Windows
Eis um vídeo que demonstra como criar e utilizar Partilhas de ficheiros do Azure no Windows.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Azure-File-Storage-with-Windows/player]
> 
> 

## <a name="about-this-tutorial"></a>Acerca deste tutorial
Este tutorial de introdução demonstra as noções básicas da utilização do Armazenamento de ficheiros do Microsoft Azure. Neste tutorial, iremos:

* Utilizar o Portal do Azure ou o PowerShell para criar uma nova Partilha de ficheiros do Azure, adicionar um diretório, carregar um ficheiro local para a partilha e listar os ficheiros no diretório.
* Montar a partilha de ficheiros, tal como montaria qualquer partilha SMB.
* Utilizar a Biblioteca de Clientes do Armazenamento do Azure para .NET para aceder à partilha de ficheiros a partir de uma aplicação no local. Criar uma aplicação de consola e realizar estas ações com a partilha de ficheiros:
  * Escrever o conteúdo de um ficheiro na partilha para a janela da consola.
  * Definir a quota (de tamanho máximo) para a partilha de ficheiros.
  * Criar uma assinatura de acesso partilhado para um ficheiro que utiliza uma política de acesso partilhado definida na partilha.
  * Copiar um ficheiro para outro ficheiro na mesma conta de armazenamento.
  * Copiar um ficheiro para um blob na mesma conta de armazenamento.
* Utilizar as Métricas do Storage do Azure para a resolução de problemas

O Armazenamento de ficheiros é agora suportado em todas as contas de armazenamento, pelo que pode utilizar uma conta de armazenamento já existente ou criar uma nova. Veja [Como criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account) para obter informações sobre como criar uma conta de armazenamento nova.

## <a name="use-the-azure-portal-to-manage-a-file-share"></a>Utilizar o Portal do Azure para gerir uma partilha de ficheiros
O [Portal do Azure](https://portal.azure.com) fornece uma interface de utilizador para os clientes gerirem partilhas de ficheiros. A partir do portal, pode:

* Criar a partilha de ficheiros
* Carregar e transferir os ficheiros de e para a partilha de ficheiros
* Monitorizar a utilização real de cada partilha de ficheiros
* Ajustar a quota de tamanho da partilha
* Obter o comando `net use` a utilizar para montar a partilha de ficheiros a partir de um cliente Windows

### <a name="create-file-share"></a>Criar a partilha de ficheiros
1. Inicie sessão no Portal do Azure.
2. No menu de navegação, clique em **Contas de armazenamento** ou **Contas de armazenamento (clássicas)**.
   
    ![Captura de ecrã que mostra como criar a partilha de ficheiros no portal](./media/storage-dotnet-how-to-use-files/files-create-share-0.png)
3. Escolha a sua conta de armazenamento.
   
    ![Captura de ecrã que mostra como criar a partilha de ficheiros no portal](./media/storage-dotnet-how-to-use-files/files-create-share-1.png)
4. Escolha o serviço “Ficheiros”.
   
    ![Captura de ecrã que mostra como criar a partilha de ficheiros no portal](./media/storage-dotnet-how-to-use-files/files-create-share-2.png)
5. Clique em “Partilhas de ficheiros” e siga a ligação para criar a sua primeira partilha de ficheiros.
   
    ![Captura de ecrã que mostra como criar a partilha de ficheiros no portal](./media/storage-dotnet-how-to-use-files/files-create-share-3.png)
6. Preencha o nome da partilha de ficheiros e o tamanho da partilha de ficheiros (até 5120 GB) para criar a primeira partilha de ficheiros. Quando a partilha de ficheiros tiver sido criada, é possível montá-la a partir de qualquer sistema de ficheiros que suporte o SMB 2.1 ou SMB 3.0.
   
    ![Captura de ecrã que mostra como criar a partilha de ficheiros no portal](./media/storage-dotnet-how-to-use-files/files-create-share-4.png)

### <a name="upload-and-download-files"></a>Carregar e transferir os ficheiros
1. Escolha uma partilha de ficheiros que já criou.
   
    ![Captura de ecrã que mostra como carregar e transferir os ficheiros do portal](./media/storage-dotnet-how-to-use-files/files-upload-download-1.png)
2. Clique em **Carregar** para abrir a interface de utilizador para o carregamento de ficheiros.
   
    ![Captura de ecrã que mostra como carregar ficheiros do portal](./media/storage-dotnet-how-to-use-files/files-upload-download-2.png)
3. Clique com o botão direito do rato num ficheiro e escolha **Transferir** para o transferir localmente.
   
    ![Captura de ecrã que mostra como transferir ficheiros do portal](./media/storage-dotnet-how-to-use-files/files-upload-download-3.png)

### <a name="manage-file-share"></a>Gerir a partilha de ficheiros
1. Clique em **Quota** para alterar o tamanho da partilha de ficheiros (até 5120 GB).
   
    ![Captura de ecrã que mostra como configurar a quota da partilha de ficheiros](./media/storage-dotnet-how-to-use-files/files-manage-1.png)
2. Clique em **Ligar** para obter a linha de comandos para montar a partilha de ficheiros a partir do Windows.
   
    ![Captura de ecrã que mostra como montar a partilha de ficheiros](./media/storage-dotnet-how-to-use-files/files-manage-2.png)
   
    ![Captura de ecrã que mostra como montar a partilha de ficheiros](./media/storage-dotnet-how-to-use-files/files-manage-3.png)
   
   > [!TIP]
   > Para localizar a chave de acesso da conta de armazenamento para a montagem, clique nas **Definições** da conta de armazenamento e, em seguida, clique em **Chaves de acesso**.
   > 
   > 
   
    ![Captura de ecrã que mostra como encontrar a chave de acesso da conta de armazenamento](./media/storage-dotnet-how-to-use-files/files-manage-4.png)
   
    ![Captura de ecrã que mostra como encontrar a chave de acesso da conta de armazenamento](./media/storage-dotnet-how-to-use-files/files-manage-5.png)

## <a name="use-powershell-to-manage-a-file-share"></a>Utilizar o PowerShell para gerir uma partilha de ficheiros
Em alternativa, pode utilizar o Azure PowerShell para criar e gerir partilhas de ficheiros.

### <a name="install-the-powershell-cmdlets-for-azure-storage"></a>Instalar os cmdlets do PowerShell para o Storage do Azure
Para preparar para utilizar o PowerShell, transfira e instale os cmdlets do Azure PowerShell. Veja [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para obter as instruções de instalação e do ponto de instalação.

> [!NOTE]
> É recomendado que transfira e instale ou atualize para o módulo do Azure PowerShell mais recente.
> 
> 

Abra uma janela do Azure PowerShell ao clicar em **Iniciar** e escrever **Windows PowerShell**. A janela do PowerShell carrega o módulo do Azure Powershell.

### <a name="create-a-context-for-your-storage-account-and-key"></a>Criar um contexto para a conta de armazenamento e a chave
Agora, crie o contexto da conta de armazenamento. O contexto contém a chave de conta e o nome da conta de armazenamento. Para obter instruções sobre a cópia da chave de conta a partir do [Portal do Azure](https://portal.azure.com), veja [Ver e copiar chaves de acesso de armazenamento](storage-create-storage-account.md#view-and-copy-storage-access-keys).

Substitua `storage-account-name` e `storage-account-key` pelo nome da conta de armazenamento e a chave no exemplo seguinte.

```powershell
# create a context for account and key
$ctx=New-AzureStorageContext storage-account-name storage-account-key
```

### <a name="create-a-new-file-share"></a>Criar uma nova partilha de ficheiros
Em seguida, crie a nova partilha, com o nome `logs`.

```powershell
# create a new share
$s = New-AzureStorageShare logs -Context $ctx
```

Tem agora uma partilha de ficheiros no Armazenamento de ficheiros. Em seguida, vamos adicionar um diretório e um ficheiro.

> [!IMPORTANT]
> O nome da partilha de ficheiros tem de ser todo em minúsculas. Para obter detalhes completos sobre a nomenclatura de partilhas de ficheiros e ficheiros, veja [Naming and Referencing Shares, Directories, Files, and Metadata (Nomenclatura e Referência de Partilhas, Diretórios, Ficheiros e Metadados)](https://msdn.microsoft.com/library/azure/dn167011.aspx).
> 
> 

### <a name="create-a-directory-in-the-file-share"></a>Criar um diretório na partilha de ficheiros
Em seguida, crie um diretório na partilha. No exemplo seguinte, o diretório chama-se `CustomLogs`.

```powershell
# create a directory in the share
New-AzureStorageDirectory -Share $s -Path CustomLogs
```

### <a name="upload-a-local-file-to-the-directory"></a>Carregar um ficheiro local para o diretório
Agora, carregue um ficheiro local para o diretório. O exemplo seguinte carrega um ficheiro de `C:\temp\Log1.txt`. Edite o caminho do ficheiro para que aponte para um ficheiro válido no computador local.

```powershell
# upload a local file to the new directory
Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs
```

### <a name="list-the-files-in-the-directory"></a>Listar os ficheiros no diretório
Para ver o ficheiro no diretório, pode listar todos os ficheiros do diretório. Este comando devolve os ficheiros e subdiretórios (se existirem) no diretório CustomLogs.

```powershell
# list files in the new directory
Get-AzureStorageFile -Share $s -Path CustomLogs | Get-AzureStorageFile
```

Get-AzureStorageFile devolve uma lista de ficheiros e diretórios para qualquer diretório no qual o objeto passa. “Get AzureStorageFile-partilhar $s” devolve uma lista de ficheiros e diretórios no diretório de raiz. Para obter uma lista de ficheiros num subdiretório, tem de passar o subdiretório para Get-AzureStorageFile. É isso que isto faz – a primeira parte do comando até ao pipe devolve uma instância de diretório do subdiretório CustomLogs. Em seguida, é passado para o Get-AzureStorageFile, que devolve os ficheiros e os diretórios no CustomLogs.

### <a name="copy-files"></a>Copiar ficheiros
A partir da versão 0.9.7 do Azure PowerShell, pode copiar um ficheiro para outro ficheiro, um ficheiro para um blob ou um blob para um ficheiro. Abaixo, demonstramos como fazer estas operações de cópia com cmdlets do PowerShell.

```powershell
# copy a file to the new directory
Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx

# copy a blob to a file directory
Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx
```

## <a name="mount-the-file-share"></a>Montar a partilha de ficheiros
Com suporte para o SMB 3.0, o Armazenamento de ficheiros suporta agora a encriptação e os identificadores persistentes de clientes SMB 3.0. O suporte para a encriptação significa que os clientes SMB 3.0 podem montar uma partilha de ficheiros a partir de qualquer local, incluindo a partir de:

* Uma máquina virtual do Azure na mesma região (também suportada pelo SMB 2.1)
* Uma máquina virtual do Azure numa região diferente (apenas para o SMB 3.0)
* Uma aplicação de cliente no local (apenas para o SMB 3.0)

Quando um cliente acede ao Armazenamento de ficheiros, a versão do SMB utilizada depende da versão do SMB suportada pelo sistema operativo. A tabela abaixo fornece um resumo de suporte para clientes Windows. Consulte este blogue para obter mais detalhes sobre as [versões do SMB](http://blogs.technet.com/b/josebda/archive/2013/10/02/windows-server-2012-r2-which-version-of-the-smb-protocol-smb-1-0-smb-2-0-smb-2-1-smb-3-0-or-smb-3-02-you-are-using.aspx).

| Cliente Windows | Versão do SMB suportada |
|:--- |:--- |
| Windows 7 |SMB 2.1 |
| Windows Server 2008 R2 |SMB 2.1 |
| Windows 8 |SMB 3.0 |
| Windows Server 2012 |SMB 3.0 |
| Windows Server 2012 R2 |SMB 3.0 |
| Windows 10 |SMB 3.0 |

### <a name="mount-the-file-share-from-an-azure-virtual-machine-running-windows"></a>Montar a partilha de ficheiros a partir de uma máquina virtual do Azure em execução no Windows
Para demonstrar como montar uma partilha de ficheiros do Azure, vamos agora criar uma máquina virtual do Azure a executar o Windows e aceder remotamente ao mesmo para montar a partilha.

1. Primeiro, crie uma nova máquina virtual do Azure, seguindo as instruções em [Criar uma máquina virtual do Windows no Portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Em seguida, siga as instruções em [Iniciar sessão numa máquina virtual do Windows através do Portal do Azure](../virtual-machines/virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para aceder remotamente à máquina virtual.
3. Abra uma janela do PowerShell na máquina virtual.

### <a name="persist-your-storage-account-credentials-for-the-virtual-machine"></a>Manter as suas credenciais da conta de armazenamento para a máquina
Antes da montagem para a partilha de ficheiros, primeiro mantenha as credenciais da conta de armazenamento na máquina virtual. Este passo permite ao Windows restabelecer automaticamente ligação à partilha de ficheiros quando a máquina virtual reiniciar. Para manter as suas credenciais de conta, execute o comando `cmdkey` a partir da janela do PowerShell na máquina virtual. Substitua `<storage-account-name>` pelo nome da sua conta de armazenamento e `<storage-account-key>` pela chave da conta de armazenamento.

```
cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>
```

Em seguida, o Windows irá restabelecer ligação à partilha de ficheiros quando a máquina virtual reiniciar. Pode verificar se a ligação à partilha foi restabelecida ao executar o comando `net use` a partir de uma janela do PowerShell.

Tenha em atenção que as credenciais são apenas mantidas no contexto no qual `cmdkey` é executado. Se está a desenvolver uma aplicação que é executada como um serviço, também terá de manter as suas credenciais nesse contexto.

### <a name="mount-the-file-share-using-the-persisted-credentials"></a>Montar a partilha de ficheiros com as credenciais persistentes
Assim que tiver uma ligação remota para a máquina virtual, pode executar o comando `net use` para montar a partilha de ficheiros, utilizando a seguinte sintaxe. Substitua `<storage-account-name>` pelo nome da sua conta de armazenamento e `<share-name>` pelo nome da partilha do Armazenamento de ficheiros.

```
net use <drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>

example :
net use z: \\samples.file.core.windows.net\logs
```

Uma vez que manteve as credenciais da conta de armazenamento no passo anterior, não tem de os fornecer com o comando `net use`. Se ainda não manteve as suas credenciais, inclua-as como um parâmetro passado ao comando `net use`, conforme mostrado no exemplo seguinte.

```
net use <drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /u:<storage-account-name> <storage-account-key>

example :
net use z: \\samples.file.core.windows.net\logs /u:samples <storage-account-key>
```

Agora, pode trabalhar com a partilha do File Storage a partir da máquina virtual como o faria com qualquer outra unidade. Pode emitir comandos de ficheiros padrão a partir da linha de comandos ou visualizar a partilha montada e o respetivo conteúdo a partir do Explorador de Ficheiros. De igual modo, pode executar código na máquina virtual que acede à partilha de ficheiros através das APIs de E/S dos ficheiros do Windows padrão, tais como as fornecidas pelos [espaços de nomes System.IO](http://msdn.microsoft.com/library/gg145019.aspx) no .NET Framework.

De igual modo, pode montar a partilha de ficheiros a partir de uma função executada num serviço em nuvem do Azure ao aceder remotamente à função.

### <a name="mount-the-file-share-from-an-on-premises-client-running-windows"></a>Montar a partilha de ficheiros a partir de um cliente no local a executar o Windows
Para montar a partilha de ficheiros a partir de um cliente no local, primeiro tem de seguir estes passos:

* Instalar a versão do Windows que suporta o SMB 3.0. O Windows irá tirar partido da encriptação do SMB 3.0 para transferir de forma segura os dados entre o cliente no local e a partilha de ficheiros do Azure na nuvem.
* Abra o acesso à Internet para a porta 445 (Saída de TCP) na sua rede local, conforme é necessário pelo protocolo SMB.

> [!NOTE]
> Alguns fornecedores de serviços de Internet podem bloquear a porta 445, pelo que terá de verificar junto do seu fornecedor de serviços.
> 
> 

## <a name="develop-with-file-storage"></a>Desenvolver com o Armazenamento de ficheiros
Para escrever código que chama o Armazenamento de ficheiros, pode utilizar as bibliotecas de cliente do armazenamento para o .NET e o Java ou a API REST do Storage do Azure. O exemplo nesta secção demonstra como trabalhar com uma partilha de ficheiros com [Azure Storage Client Library for .NET (Biblioteca de Clientes do Storage do Azure para .NET)](https://msdn.microsoft.com/library/mt347887.aspx) a partir de uma aplicação de consola simples em execução no ambiente de trabalho.

### <a name="create-the-console-application-and-obtain-the-assembly"></a>Criar a aplicação de consola e obter a assemblagem
Para criar uma nova aplicação de consola no Visual Studio e instalar o pacote NuGet que contém a Biblioteca de Clientes do Storage do Azure:

1. No Visual Studio, selecione **Ficheiro > Novo Projeto** e, em seguida, escolha **Windows > Aplicação de Consola** na lista de modelos do Visual C#.
2. Forneça um nome para a aplicação de consola e, em seguida, clique em **OK**.
3. Depois de o projeto ter sido criado, clique com o botão direito do rato no projeto no Explorador de Soluções e escolha **Gerir Pacotes NuGet**. Procure online por “WindowsAzure.Storage” e clique em **Instalar** para instalar dependências e pacotes da Biblioteca de Clientes do Armazenamento do Azure para .NET.

Os exemplos de código neste artigo também utilizam a [Biblioteca do Gestor de Configuração do Microsoft Azure](https://msdn.microsoft.com/library/azure/mt634646.aspx) para obter a cadeia de ligação do armazenamento a partir de um ficheiro app.config na aplicação de consola. Com o Gestor de Configuração do Azure, pode obter a cadeia de ligação no runtime, quer a aplicação esteja em execução no Microsoft Azure ou a partir de um ambiente de trabalho, telemóvel ou aplicação Web.

Para instalar o pacote do Gestor de Configuração do Azure, clique com o botão direito do rato no Explorador de Soluções e escolha **Gerir Pacotes NuGet**. Procure online por “ConfigurationManager” e clique em **Instalar** para instalar o pacote.

A utilização do Gestor de Configuração do Azure é opcional. De igual modo, pode utilizar uma API, tal como a [classe ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) do .NET Framework.

### <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Guardar as suas credenciais da conta de armazenamento no ficheiro app.config
Em seguida, guarde as suas credenciais no ficheiro app.config do seu projeto. Edite o ficheiro app.config para que seja apresentado de forma semelhante ao seguinte exemplo, substituindo `myaccount` pelo seu nome da sua conta de armazenamento e `mykey` pela chave da mesma.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> A versão mais recente do emulador de armazenamento do Azure não suporta o Armazenamento de ficheiros. A sua cadeia de ligação tem de visar uma conta de armazenamento do Azure na nuvem para funcionar com o Armazenamento de ficheiros.
> 
> 

### <a name="add-namespace-declarations"></a>Adicionar declarações de espaço de nomes
Abra o ficheiro `program.cs` a partir do Explorador de Soluções e adicione as seguintes declarações de espaço de nomes à parte superior do ficheiro.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.WindowsAzure.Storage; // Namespace for Storage Client Library
using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage
using Microsoft.WindowsAzure.Storage.File; // Namespace for File storage
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="access-the-file-share-programmatically"></a>Aceder à partilha de ficheiros programaticamente
Em seguida, adicione o seguinte código ao método `Main()` (depois do código mostrado acima) para obter a cadeia de ligação. Este código obtém uma referência para o ficheiro que criámos anteriormente e produz o respetivo conteúdo para a janela da consola.

```csharp
// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

Execute a aplicação de consola para ver o resultado.

### <a name="set-the-maximum-size-for-a-file-share"></a>Definir o tamanho máximo para uma partilha de ficheiros
A partir da versão 5. x da Biblioteca de Clientes do Storage do Azure, pode definir a quota (ou o tamanho máximo) para uma partilha de ficheiros, em gigabytes. De igual modo, pode verificar a quantidade de dados atualmente armazenada na partilha.

Ao definir a quota para uma partilha, pode limitar o tamanho total dos ficheiros armazenados na partilha. Se o tamanho total dos ficheiros na partilha exceder a quota definida na partilha, os clientes não poderão aumentar o tamanho dos ficheiros existentes ou criar novos ficheiros, a menos que esses ficheiros estejam vazios.

O exemplo abaixo mostra como verificar a utilização atual para uma partilha e como configurar a quota para a partilha.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.WindowsAzure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Gerar uma assinatura de acesso partilhado para um ficheiro ou partilha de ficheiros
A partir da versão 5. x da Biblioteca de Clientes do Storage do Azure, pode gerar uma assinatura de acesso partilhado (SAS) para uma partilha de ficheiros ou para um ficheiro individual. De igual modo, pode criar uma política de acesso partilhado numa partilha de ficheiros para gerir assinaturas de acesso partilhado. Recomenda-se a criação de uma política de acesso partilhado, uma vez que fornece um meio de revogação do SAS se este estiver comprometido.

O exemplo seguinte cria uma política de acesso partilhado numa partilha e, em seguida, utiliza essa política para fornecer as restrições para um SAS num ficheiro dentro da partilha.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new shared access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authenticated via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

Para obter mais informações sobre a criação e utilização de assinaturas de acesso partilhado, veja [Utilizar Assinaturas de Acesso Partilhado (SAS)](storage-dotnet-shared-access-signature-part-1.md) e [Criar e utilizar um SAS com armazenamento de Blobs](storage-dotnet-shared-access-signature-part-2.md).

### <a name="copy-files"></a>Copiar ficheiros
A partir da versão 5. x da Biblioteca de Clientes do Storage do Azure, pode copiar um ficheiro para outro ficheiro, um ficheiro para um blob ou um blob para um ficheiro. Nas secções seguintes, iremos demonstrar como realizar estas operações de cópia programaticamente.

Pode também utilizar o AzCopy para copiar um ficheiro para outro, copiar um blob para um ficheiro ou vice-versa. Veja [Transferir dados com o Utilitário de Linha de Comandos AzCopy](storage-use-azcopy.md).

> [!NOTE]
> Se estiver a copiar um blob para um ficheiro ou um ficheiro para um blob, tem de utilizar uma assinatura de acesso partilhado (SAS) para autenticar o objeto de origem, mesmo se estiver a copiar dentro da mesma conta de armazenamento.
> 
> 

**Copiar um ficheiro para outro ficheiro**

O exemplo seguinte copia um ficheiro para outro ficheiro na mesma partilha. Uma vez que esta operação de cópia copia entre ficheiros na mesma conta de armazenamento, pode utilizar a autenticação de Chave Partilhada para efetuar a cópia.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

**Copiar um ficheiro para um blob**

O exemplo seguinte cria um ficheiro e copia-o para um blob na mesma conta de armazenamento. O exemplo cria um SAS para o ficheiro de origem, que o serviço utiliza para autenticar o acesso ao ficheiro de origem durante a operação de cópia.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authenticate access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

Pode copiar um blob para um ficheiro da mesma forma. Se o objeto de origem for um blob, crie um SAS para autenticar o acesso a esse blob durante a operação de cópia.

## <a name="troubleshooting-file-storage-using-metrics"></a>Resolução de problemas com o Armazenamento de ficheiros através de métricas
Agora, a Análise do Storage do Azure suporta métricas para o Armazenamento de ficheiros. Com os dados de métricas, pode rastrear pedidos e diagnosticar problemas.

Pode ativar métricas para o Armazenamento de ficheiros a partir do [Portal do Azure](https://portal.azure.com). Pode também ativar as métricas programaticamente chamando a operação Definir Propriedades do Serviço do Ficheiro através da API REST ou de um dos respetivos análogos na Biblioteca de Clientes do Storage.

O exemplo de código seguinte mostra como utilizar a Biblioteca de Clientes do Storage para o .NET para ativar as métricas para o Armazenamento de ficheiros.

Primeiro, adicione as seguintes declarações `using` ao ficheiro program.cs, para além das que adicionou acima:

```csharp
using Microsoft.WindowsAzure.Storage.File.Protocol;
using Microsoft.WindowsAzure.Storage.Shared.Protocol;
```

Tenha em atenção que enquanto o armazenamento de Blobs, Tabela e Filas utiliza o tipo `ServiceProperties` partilhado no espaço de nomes `Microsoft.WindowsAzure.Storage.Shared.Protocol`, o Armazenamento de ficheiros utiliza o seu próprio tipo, o tipo `FileServiceProperties` no espaço de nomes `Microsoft.WindowsAzure.Storage.File.Protocol`. Contudo, ambos os espaços de nomes têm de ser referenciados a partir do seu código para o seguinte código compilar.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.WindowsAzure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

Além disso, pode consultar o [Azure Files Troubleshooting Article (Artigo de Resolução de Problemas de Ficheiros do Azure)](storage-troubleshoot-file-connection-problems.md) para obter documentação de orientação de resolução de problemas de ponto a ponto. 

## <a name="file-storage-faq"></a>FAQ sobre o Armazenamento de ficheiros
1. **O Armazenamento de ficheiros suporta a autenticação baseada no Active Directory?**
   
    Atualmente, não suportamos a autenticação baseada no AD ou em ACLs, mas é uma funcionalidade que está presente na nossa lista de pedidos de funcionalidades. Por agora, as chaves da conta de armazenamento do Azure são utilizadas para fornecer a autenticação à partilha de ficheiros. Oferecemos uma solução através de assinaturas de acesso partilhado (SAS) através da API REST ou de bibliotecas de cliente. Através de SAS, pode gerar tokens com permissões específicas que são válidas ao longo de um intervalo de tempo especificado. Por exemplo, pode gerar um token de acesso só de leitura para um determinado ficheiro. Quem tiver este token enquanto for válido tem acesso só de leitura a esse ficheiro.
   
    A SAS só é suportada através da API REST ou de bibliotecas cliente. Ao montar a partilha de ficheiros através do protocolo SMB, não pode utilizar uma SAS para delegar o acesso ao seu conteúdo.
2. **As Partilhas de ficheiros do Azure são visíveis publicamente através da Internet ou são apenas acessíveis a partir do Azure?**
   
    Desde que a porta 445 (Saída de TCP) esteja aberta e o cliente suportar o protocolo SMB 3.0 (*por exemplo,*, Windows 8 ou Windows Server 2012), a partilha de ficheiros está disponível através da Internet.  
3. **O tráfego de rede entre uma máquina virtual do Azure e uma partilha de ficheiros conta como largura de banda externa que é cobrada na subscrição?**
   
    Se a partilha de ficheiros e a máquina virtual estiverem em regiões diferentes, o tráfego entre os mesmos será cobrado como largura de banda externa.
4. **Se o tráfego de rede estiver entre uma máquina virtual e uma partilha de ficheiros na mesma região, é gratuito?**
   
    Sim. É gratuito se o tráfego estiver na mesma região.
5. **Ligar ao Armazenamento de Ficheiros do Azure a partir de máquinas virtuais no local depende do Azure ExpressRoute?**
   
    Não. Se não tiver o ExpressRoute, pode continuar a aceder à partilha de ficheiros no local, desde que tenha a porta 445 (Saída de TCP) aberta para o acesso à Internet. No entanto, pode utilizar o ExpressRoute com o Armazenamento de ficheiros se assim o desejar.
6. **Um “Testemunho de Partilha de Ficheiros” de um cluster de ativação pós-falha é um dos casos de utilização do Armazenamento de Ficheiros do Azure?**
   
    Esta utilização não é suportada de momento.
7. **Neste momento, o Armazenamento de ficheiros é apenas replicado através do LRS ou do GRS, certo?**  
   
    Planeamos suportar o RA-GRS, mas ainda não podemos avançar uma data de quando a vamos disponibilizar.
8. **Quando posso utilizar contas de armazenamento existentes do Armazenamento de Ficheiros do Azure?**
   
    O File Storage do Azure está agora ativado para todas as contas de armazenamento.
9. **Vai ser adicionada à API REST uma operação de Mudança de Nome?**
   
    A Mudança de nome ainda não é suportada na nossa API REST.
10. **É possível ter partilhas aninhadas, por outras palavras, uma partilha numa partilha?**
    
    Não. A partilha de ficheiros é o controlador virtual que é possível montar, pelo que não são suportadas partilhas aninhadas.
11. **É possível especificar permissões só de leitura ou só de escrita em pastas dentro da partilha?**
    
    Não tem este nível de controlo sobre as permissões se montar a partilha de ficheiros através do SMB. No entanto, pode conseguir isto ao criar uma assinatura de acesso partilhado (SAS) através da API REST ou das bibliotecas de cliente.  
12. **O meu desempenho ficou lento ao tentar deszipar ficheiros para o Armazenamento de ficheiros. O que devo fazer?**
    
    Para transferir um grande número de ficheiros para o Armazenamento de ficheiros, recomendamos que utilize o AzCopy, o Azure Powershell (Windows) ou a CLI do Azure (Linux/Unix), uma vez que estas ferramentas foram otimizadas para transferência de rede.
13. **Foi lançado o patch para corrigir o problema do desempenho lento com os Ficheiros do Azure**
    
    A equipa do Windows lançou recentemente uma correção para resolver um problema de desempenho lento quando o cliente acede ao File Storage do Azure a partir do Windows 8.1 ou Windows Server 2012 R2. Para mais informações, consulte o artigo KB associado [Desempenho lento ao aceder ao File Storage do Azure a partir do Windows 8.1 ou Server 2012 R2](https://support.microsoft.com/en-us/kb/3114025).
14. **Utilizar o Armazenamento de Ficheiros do Azure com o IBM MQ**
    
    A IBM lançou um documento para guiar os clientes do IBM MQ na configuração do File Storage do Azure com o respetivo serviço. Para mais informações, consulte [Como configurar o gestor de filas de várias instâncias do IBM MQ com o Serviço do Ficheiro do Microsoft Azure](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).
15. **Como posso resolver erros de Armazenamento de Ficheiros do Azure?**
    
    Pode consultar o [Azure Files Troubleshooting Article (Artigo de Resolução de Problemas de Ficheiros do Azure)](storage-troubleshoot-file-connection-problems.md) para obter documentação de orientação de resolução de problemas de ponto a ponto.               

## <a name="next-steps"></a>Passos seguintes
Consulte as ligações para obter mais informações sobre o Armazenamento de ficheiros do Azure.

### <a name="conceptual-articles-and-videos"></a>Artigos e vídeos concetuais
* [Azure Files Storage: a frictionless cloud SMB file system for Windows and Linux (Armazenamento de Ficheiros do Azure: um sistema de ficheiros SMB na nuvem sem incómodos para o Windows e Linux)](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [How to use Azure File Storage with Linux (Como utilizar o Armazenamento de Ficheiros do Azure com o Linux)](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Suporte de ferramentas para o Armazenamento de ficheiros
* [Using Azure PowerShell with Azure Storage (Utilizar o Azure PowerShell com o Armazenamento do Azure)](storage-powershell-guide-full.md)
* [How to use AzCopy with Microsoft Azure Storage (Como utilizar o AzCopy com o Armazenamento do Microsoft Azure)](storage-use-azcopy.md)
* [Using the Azure CLI with Azure Storage (Utilizar a CLI do Azure com o Armazenamento do Azure)](storage-azure-cli.md#create-and-manage-file-shares)

### <a name="reference"></a>Referência
* [Storage Client Library for .NET reference (Referência da Biblioteca de Clientes do Armazenamento para .NET)](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [File Service REST API reference (Referência da API REST do Serviço do Ficheiros)](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### <a name="blog-posts"></a>Publicações no blogue
* [Azure File storage is now generally available (O Armazenamento de Ficheiros do Azure está agora disponível normalmente)](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure File Storage (Dentro do Armazenamento de Ficheiros do Azure)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introducing Microsoft Azure File Service (Introdução ao Serviço de Ficheiros do Microsoft Azure)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Persisting connections to Microsoft Azure Files (Ligações persistentes aos Ficheiros do Microsoft Azure)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)



<!--HONumber=Dec16_HO1-->


