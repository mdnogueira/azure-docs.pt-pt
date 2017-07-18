---
title: "Montar uma partilha de Ficheiros do Azure e aceder à partilha no Windows | Microsoft Docs"
description: "Monte uma partilha de Ficheiros do Azure e aceda à partilha no Windows."
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: a6d3a6f6e3457c84c5a7dc7d3601ef9495c060fe
ms.contentlocale: pt-pt
ms.lasthandoff: 07/12/2017

---

# <a name="mount-an-azure-file-share-and-access-the-share-in-windows"></a>Montar uma partilha de Ficheiros do Azure e aceder à partilha no Windows
O [armazenamento de Ficheiros do Azure](storage-dotnet-how-to-use-files.md) é o sistema de ficheiros na cloud e fácil de utilizar da Microsoft. As partilhas de Ficheiros do Azure podem ser montadas no Windows e no Windows Server. Este artigo mostra três formas diferentes de montar uma partilha de Ficheiros do Azure no Windows - com a IU do Explorador de Ficheiros, através do PowerShell e através da Linha de Comandos. 

Para montar uma partilha de Ficheiros do Azure fora da região do Azure na qual está alojada, como, por exemplo, no local ou noutra região do Azure, o SO tem de suportar SMB 3.x. A tabela seguinte mostra a versão do SMB das versões recentes do Windows:

| Versão do Windows | Versão do SMB | Suporta a montagem a partir de VMs do Azure | Suporta a montagem a partir de instalações no local | KB Mínimo Recomendado |
|----|----|----|----|----|
| Windows 10 versão 1703 | SMB 3.1.1 | Sim | Sim | |
| Windows Server 2016 | SMB 3.1.1 | Sim | Sim | [KB4015438](https://support.microsoft.com/help/4015438) |
| Windows 10 versão 1607 | SMB 3.1.1 | Sim | Sim | [KB4015438](https://support.microsoft.com/help/4015438) | 
| Windows 10 versão 1511 | SMB 3.1.1 | Sim | Sim | [KB4013198](https://support.microsoft.com/help/4013198) |
| Windows 10 versão 1507 | SMB 3.1.1 | Sim | Sim | [KB4012606](https://support.microsoft.com/help/4012606) | 
| Windows 8.1 | SMB 3.0.2 | Sim | Sim | [KB4012216](https://support.microsoft.com/help/4012216) |
| Windows Server 2012 R2 | SMB 3.0.2 | Sim | Sim | [KB4012216](https://support.microsoft.com/help/4012216) |
| Windows Server 2012 | SMB 3.0 | Sim | Sim | [KB4012214](https://support.microsoft.com/help/4012214) |
| Windows 7 | SMB 2.1 | Sim | Não | [KB4012215](https://support.microsoft.com/help/4012215) |
| Windows Server 2008 R2 | SMB 2.1 | Sim | Não | [KB4012215](https://support.microsoft.com/help/4012215) |

> [!Note]  
> Recomendamos obter sempre o KB mais recente para a sua versão do Windows. O KB mínimo recomendado tem como objetivo fornecer o pacote mais recente com correções do SMB para administradores de TI avessos a atualizações.

## <a name="aprerequisites-for-mounting-azure-file-share-with-windows"></a></a>Pré-requisitos para Montar a Partilha de Ficheiros do Azure com o Windows 
* **Nome da Conta de Armazenamento**: para montar uma partilha de Ficheiros do Azure, precisa do nome da conta de armazenamento.

* **Chave da Conta de Armazenamento**: para montar uma partilha de Ficheiros do Azure, precisa da chave de armazenamento primária (ou secundária). Atualmente, não são suportadas chaves SAS para a montagem.

* **Confirme que a porta 445 está aberta**: o armazenamento de Ficheiros do Azure utiliza o protocolo SMB. O SMB comunica através da porta TCP 445 - verifique se a firewall não está a bloquear as portas TCP 445 no computador cliente.

## <a name="mount-the-azure-file-share-with-file-explorer"></a>Montar a partilha de Ficheiros do Azure com o Explorador de Ficheiros
> [!Note]  
> Tenha em conta que as instruções seguintes são mostradas no Windows 10 e podem ser ligeiramente diferentes nas versões mais antigas. 

1. **Abra o Explorador de Ficheiros**: pode abri-lo no Menu Iniciar ou ao premir o atalho Win+E.

2. **Navegue para o item "Este PC", no lado esquerdo da janela. Esta ação altera os menus disponíveis no friso. No menu Computador, selecione “Mapear Unidade de Rede”**.
    
    ![Instantâneo do menu pendente “Mapear unidade de rede”](media/storage-file-how-to-use-files-windows/1_MountOnWindows10.png)

3. **Copie o caminho UNC do painel "Ligar" no portal do Azure**: está disponível [aqui](storage-file-how-to-use-files-portal.md#connect-to-file-share) uma descrição detalhada para encontrar esta informação.

    ![Caminho UNC no painel Ligar do armazenamento de Ficheiros do Azure](media/storage-file-how-to-use-files-windows/portal_netuse_connect.png)

4. **Selecione a letra de unidade e introduza o caminho UNC.** 
    
    ![Instantâneo da caixa de diálogo “Mapear Unidade de Rede”](media/storage-file-how-to-use-files-windows/2_MountOnWindows10.png)

5. **Utilize o nome da conta de armazenamento com o prefixo `Azure\` como o nome de utilizador e uma Chave da Conta de Armazenamento como a palavra-passe.**
    
    ![Captura de ecrã da caixa de diálogo de credenciais de rede](media/storage-file-how-to-use-files-windows/3_MountOnWindows10.png)

6. **Utilize a patilha de Ficheiros do Azure como pretendido**.
    
    ![A partilha de Ficheiros do Azure está agora montada](media/storage-file-how-to-use-files-windows/4_MountOnWindows10.png)

7. **Quando estiver pronto para desmontar (ou desligar) a partilha de Ficheiros do Azure, pode fazê-ao clicar com o botão direito do rato na entrada da partilha, em "Localizações de rede" no Explorador de Ficheiros, e selecionar "Desligar"**.

## <a name="mount-the-azure-file-share-with-powershell"></a>Montar a partilha de Ficheiros do Azure com o PowerShell
1. **Utilize o comando seguinte para montar a partilha de Ficheiros do Azure**: não se esqueça de substituir `<storage-account-name>`, `<share-name>`, `<storage-account-key>` e `<desired-drive-letter>` pelas informações corretas.

    ```PowerShell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
    New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\<share-name>" -Credential $credential
    ```

2. **Utilize a partilha de Ficheiros do Azure conforme pretendido**.

3. **Quando tiver terminado, desmonte a partilha de Ficheiros do Azure com o comando seguinte**.

    ```PowerShell
    Remove-PSDrive -Name <desired-drive-letter>
    ```

> [!Note]  
> Pode utilizar o parâmetro `-Persist` em `New-PSDrive` para tornar a partilha de Ficheiros do Azure visível para o resto do SO enquanto estiver montada.

## <a name="mount-the-azure-file-share-with-command-prompt"></a>Montar a partilha de Ficheiros do Azure com a Linha de Comandos
1. **Utilize o comando seguinte para montar a partilha de Ficheiros do Azure**: não se esqueça de substituir `<storage-account-name>`, `<share-name>`, `<storage-account-key>` e `<desired-drive-letter>` pelas informações corretas.

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. **Utilize a partilha de Ficheiros do Azure conforme pretendido**.

3. **Quando tiver terminado, desmonte a partilha de Ficheiros do Azure com o comando seguinte**.

    ```
    net use <desired-drive-letter>: /delete
    ```

> [!Note]  
> Pode configurar a partilha de Ficheiros do Azure para que se ligue novamente de forma automática após o reinício, ao persistir as credenciais no Windows. O comando seguinte persiste as credenciais:
>   ```
>   cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>
>   ```

## <a name="next-steps"></a>Passos seguintes
Consulte as ligações para obter mais informações sobre o Armazenamento de ficheiros do Azure.

* [FAQ](storage-files-faq.md)
* [Resolução de problemas](storage-troubleshoot-file-connection-problems.md)

### <a name="conceptual-articles-and-videos"></a>Artigos e vídeos concetuais
* [Azure File storage: a frictionless cloud SMB file system for Windows and Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/) (Armazenamento de Ficheiros do Azure: um prático sistema de ficheiros SMB na cloud para Windows e Linux)
* [How to use Azure File Storage with Linux (Como utilizar o Armazenamento de Ficheiros do Azure com o Linux)](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-azure-file-storage"></a>Suporte de ferramentas para o armazenamento de Ficheiros do Azure
* [Using Azure PowerShell with Azure Storage (Utilizar o Azure PowerShell com o Armazenamento do Azure)](storage-powershell-guide-full.md)
* [How to use AzCopy with Microsoft Azure Storage (Como utilizar o AzCopy com o Armazenamento do Microsoft Azure)](storage-use-azcopy.md)
* [Using the Azure CLI with Azure Storage (Utilizar a CLI do Azure com o Armazenamento do Azure)](storage-azure-cli.md#create-and-manage-file-shares)
* [Resolver problemas do armazenamento de ficheiros do Azure](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="blog-posts"></a>Publicações no blogue
* [Azure File storage is now generally available (O Armazenamento de Ficheiros do Azure está agora disponível normalmente)](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure File storage](https://azure.microsoft.com/blog/inside-azure-file-storage/) (Por dentro do armazenamento de Ficheiros do Azure)
* [Introducing Microsoft Azure File Service (Introdução ao Serviço de Ficheiros do Microsoft Azure)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Migrating data to Azure File ](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/) (Migrar dados para os Ficheiros do Azure)

### <a name="reference"></a>Referência
* [Storage Client Library for .NET reference (Referência da Biblioteca de Clientes do Armazenamento para .NET)](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [File Service REST API reference (Referência da API REST do Serviço do Ficheiros)](http://msdn.microsoft.com/library/azure/dn167006.aspx)
