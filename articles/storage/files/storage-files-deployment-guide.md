---
title: Como implementar os ficheiros do Azure | Microsoft Docs
description: "Saiba como implementar os ficheiros do Azure do início ao fim."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: a594f31c002556f9a5fddaa17fb19273065eed47
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-deploy-azure-files"></a>Como implementar os Ficheiros do Azure
[Ficheiros do Azure](storage-files-introduction.md) oferece completamente geridos partilhas de ficheiros na nuvem que estão acessíveis através do protocolo SMB padrão da indústria. Este artigo irá mostrar como implementar praticamente ficheiros do Azure dentro da sua organização.

Recomendamos vivamente a leitura [planear uma implementação de ficheiros do Azure](storage-files-planning.md) antes de seguir os passos neste artigo.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que já tenha concluído os passos seguintes:

- Criar uma conta de armazenamento do Azure com as opções de encriptação e de resiliência pretendidas, na região que pretendidos ao nível. Consulte [criar uma conta de armazenamento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para instruções passo a passo sobre como criar uma conta de armazenamento.
- Criar uma partilha de ficheiros do Azure com a sua quota pretendido na sua conta de armazenamento. Consulte [criar uma partilha de ficheiros](storage-how-to-create-file-share.md) para instruções passo a passo sobre como criar uma partilha de ficheiros.

## <a name="transfer-data-into-azure-files"></a>Transferência de dados em ficheiros do Azure
Pode pretender migrar as partilhas de ficheiros existentes, tais como esse armazenada no local, para a nova partilha de ficheiros do Azure. Esta secção irá mostrar-lhe como mover a partilham de dados para um ficheiro do Azure através de vários métodos populares detalhados do [guia de planeamento](storage-files-planning.md#data-transfer-method)

### <a name="azure-file-sync-preview"></a>Sincronização de ficheiros do Azure (pré-visualização)
Sincronização de ficheiros do Azure (pré-visualização) permite que centralizar partilhas de ficheiros da sua organização nos ficheiros de Azure sem fornecer a flexibilidade, o desempenho e a compatibilidade de um servidor de ficheiros no local. Isto é feito ao transformar os Servidores do Windows numa cache rápida da partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter o número de caches que precisar em todo o mundo.

Sincronização de ficheiros do Azure pode ser utilizada para migrar dados para uma partilha de ficheiros do Azure, mesmo que o mecanismo de sincronização não está pretendido para utilização de longo prazo. Podem encontrar mais informações sobre como utilizar a sincronização de ficheiros do Azure para transferir dados para a partilha de ficheiros do Azure no [planear uma implementação de sincronização de ficheiros do Azure](storage-sync-files-planning.md) e [como implementar a sincronização de ficheiros do Azure](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Importar/exportar do Azure
O serviço importar/exportar do Azure permite-lhe transferir de forma segura grandes quantidades de dados para uma partilha de ficheiros do Azure por envio unidades de disco rígido para um datacenter do Azure. Consulte [utilizar o serviço de importação/exportação do Microsoft Azure para transferir dados para o storage do Azure](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obter uma descrição mais detalhada do serviço.

> [!Note]  
> O serviço importar/exportar do Azure não suporta a exportação de ficheiros a partir de uma partilha de ficheiros de Azure neste momento.

Os passos seguintes irão importar dados de uma localização no local para a partilha de ficheiros do Azure.

1. Obter o número necessário de discos rígidos a capacidade de correio no Azure. Discos rígidos podem ser de qualquer tamanho de disco, mas tem de ser ambos um 2,5" ou 3.5" SSD ou HDD suportar a norma SATA II ou SATA III. 

2. Ligue e cada disco de montagem no servidor/PC para efetuar a transferência de dados. Para otimizar o desempenho, recomendamos que executar a tarefa de exportação no local localmente no servidor que contém os dados. Em alguns casos, como quando o servidor de ficheiros que serve os dados de um dispositivo NAS, isto poderá não ser possível. Nesse caso, é perfeitamente aceitável para montar a cada disco num PC.

3. Certifique-se de que cada disco está online, inicializado e está atribuído uma letra de unidade. Para colocar uma unidade online, inicializar e atribuir uma letra de unidade, abra o snap-in MMC de gestão de discos (diskmgmt.msc).

    - Para colocar um disco online (se ainda não estiver online), faça duplo clique no disco, no painel inferior da MMC de gestão de disco e selecione "Online".
    - Para inicializar um disco, faça duplo clique no disco, no painel (depois do disco está online) inferior e selecione "Initialize". Lembre-se de que selecione "GPT" quando lhe for pedido.

        ![Uma captura de ecrã do menu Inicializar disco in da MMC de gestão do disco](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Para atribuir uma letra de unidade para o disco, faça duplo clique no espaço de "não alocado" do disco online e inicializar e clique em "Novo Volume simples". Isto irá permitir-lhe atribuir letra de unidade. Tenha em atenção que não tem de formatar o volume como isto será feito mais tarde.

        ![Uma captura de ecrã do Assistente de novo Volume simples in da MMC de gestão do disco](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Crie o ficheiro CSV de conjunto de dados. O ficheiro CSV de conjunto de dados é um mapeamento entre o caminho para os dados no local e a partilha de ficheiros do Azure pretendida, que os dados devem ser copiados para. Por exemplo, o conjunto de dados seguinte um ficheiro CSV mapeia uma partilha de ficheiros no local ("F:\shares\scratch") para uma partilha de ficheiros do Azure ("MyAzureFileShare"):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Podem ser especificadas múltiplas partilhas com uma conta de armazenamento. Consulte [preparar o ficheiro CSV de conjunto de dados](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-the-dataset-csv-file) para obter mais informações.

5. Crie o ficheiro CSV driveset. O ficheiro CSV driveset lista os discos disponíveis para o agente de exportação no local. Por exemplo, o seguinte driveset CSV ficheiro listas `X:`, `Y:`, e `Z:` tarefa de exportação de unidades para ser utilizado no local:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Consulte [preparar ficheiro CSV driveset](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-initialdriveset-or-additionaldriveset-csv-file) para obter mais informações.

6. Utilize o [WAImportExport ferramenta](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) para copiar os dados para um ou mais unidades de disco rígido.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Não modifique os dados em unidades de disco rígido ou o ficheiro de diário depois de concluir a preparação de disco.

7. [Criar uma tarefa de importação](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-an-export-job).
    
### <a name="robocopy"></a>Robocopy
Robocopy é uma ferramenta de cópia bem conhecidos que é fornecido com o Windows e Windows Server. Robocopy pode ser utilizado para transferir dados para ficheiros do Azure ao montar a partilha de ficheiros localmente e, em seguida, utilizar a localização montada como o destino no comando Robocopy. Utilizar o Robocopy é bastante simple:

1. [Montar a partilha de ficheiros de Azure](storage-how-to-use-files-windows.md). Para otimizar o desempenho, recomendamos que montar a partilha de ficheiros do Azure localmente no servidor que contém os dados. Em alguns casos, como quando o servidor de ficheiros que serve os dados de um dispositivo NAS, isto poderá não ser possível. Nesse caso, é perfeitamente aceitável para montar a partilha de ficheiros do Azure num PC. Neste exemplo, `net use` é utilizado na linha de comandos para montar a partilha de ficheiros:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Utilize `robocopy` na linha de comandos para mover dados para a partilha de ficheiros do Azure:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Robocopy tem um número significativo de opções para modificar o comportamento de cópia conforme pretendido. Para obter mais informações, veja o [Robocopy](https://technet.microsoft.com/library/cc733145.aspx) página manual.

### <a name="azcopy"></a>AzCopy
O AzCopy é um utilitário da linha de comandos concebido para copiar dados e de ficheiros do Azure, bem como o Blob storage do Azure, utilizando os comandos simples com um desempenho ideal. Utilizar o AzCopy é fácil:

1. Transferir o [versão mais recente do AzCopy no Windows](http://aka.ms/downloadazcopy) ou [Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#download-and-install-azcopy).
2. Utilize `azcopy` na linha de comandos para mover dados para a partilha de ficheiros do Azure. A sintaxe no Windows é o seguinte: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    No Linux, a sintaxe do comando é ligeiramente diferente:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy tem um número significativo de opções para modificar o comportamento de cópia conforme pretendido. Para obter mais informações, veja [AzCopy no Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) e [AzCopy no Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Montar automaticamente em PCs/servidores necessários
Para substituir uma partilha de ficheiros no local, é útil montar previamente as partilhas nas máquinas que será utilizado no. Isto pode ser feito automaticamente uma lista de máquinas.

> [!Note]  
> Montar uma partilha de ficheiros do Azure requer a utilização de chave de conta do storage como a palavra-passe, por conseguinte só Recomendamos montar em ambientes fidedignas. 

### <a name="windows"></a>Windows
PowerShell pode ser utilizado, execute o comando de montagem em vários computadores. No exemplo seguinte, `$computers` manualmente é preenchida, mas pode gerar a lista de computadores para montar automaticamente. Por exemplo, pode preencher esta variável com resultados do Active Directory.

```PowerShell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Um script de bash simples combinado com SSH, pode produzir o mesmo resultado no exemplo seguinte. O `$computer` variável for deixada da mesma forma para ser preenchido pelo utilizador:

```PowerShell
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${dur[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Passos seguintes
- [Planear uma implementação de sincronização de ficheiros do Azure](storage-sync-files-planning.md)
- [Resolver problemas de ficheiros do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Resolver problemas de ficheiros do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)