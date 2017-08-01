---
title: "Introdução ao armazenamento de Ficheiros do Azure | Microsoft Docs"
description: "Uma descrição geral do armazenamento de Ficheiros do Azure, um serviço que lhe permite criar e utilizar partilhas de ficheiros de rede na Microsoft Cloud através da norma da indústria."
services: storage
documentationcenter: 
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
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: bae2e9825bf158bb015ec0affa56f15ce5baa201
ms.contentlocale: pt-pt
ms.lasthandoff: 07/26/2017

---

# <a name="introduction-to-azure-file-storage"></a>Introdução ao armazenamento de Ficheiros do Azure
O armazenamento de Ficheiros do Azure oferece partilhas de ficheiros de rede na cloud através do [Protocolo SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) padrão da indústria e de [Common Internet File System (CIFS)](https://technet.microsoft.com/library/cc939973.aspx). As partilhas de Ficheiros do Azure podem ser montadas em simultâneo por clientes, como, por exemplo, implementações no local de Windows, macOS, Linux ou Máquinas Virtuais do Azure. Uma conta de armazenamento para fins gerais dá-lhe acesso ao Armazenamento de Ficheiros do Azure e a outros serviços, como Blobs, discos de máquinas virtuais do Azure e Filas em contas individuais.



## <a name="videos"></a>Vídeos
| Introducing Azure File storage (27m) (Introdução ao armazenamento de Ficheiros do Azure [27 min]) | Azure File storage Tutorial (5 minutes) (Tutorial do armazenamento de Ficheiros do Azure [5 minutos])  |
|-|-|
| [![Captura de ecrã do vídeo Apresentação do armazenamento de Ficheiros do Azure - clique para reproduzir!](media/storage-file-storage/azure-files-introduction-video-snapshot1.png)](https://www.youtube.com/watch?v=zlrpomv5RLs) | [![Captura de ecrã do vídeo Tutorial do armazenamento de Ficheiros do Azure - clique para reproduzir!](media/storage-file-storage/azure-files-introduction-video-snapshot2.png)](https://channel9.msdn.com/Blogs/Azure/Azure-File-storage-with-Windows/) |

## <a name="why-azure-file-storage-is-useful"></a>O porquê de o armazenamento de Ficheiros do Azure ser útil
O armazenamento de Ficheiros do Azure permite-lhe substituir servidores do Windows Server, do Linux ou servidores de ficheiros baseados em NAS alojados no local ou na cloud por uma partilha de ficheiros na cloud isenta de SO. Isto traz as vantagens seguintes:

* **Acesso partilhado:**. As partilhas de Ficheiros do Azure suportam o protocolo SMB (Server Message Block) norma da indústria, o que significa que pode substituir facilmente as suas partilhas de ficheiros no local por partilhas de Ficheiros do Azure sem se preocupar com a compatibilidade de aplicações. A capacidade de poder partilhar um sistema de ficheiros entre vários computadores e aplicações/instâncias é uma enorme vantagem com o armazenamento de Ficheiros do Azure para aplicações que requerem partilha. 
* **Totalmente Geridas**. As partilhas de Ficheiros do Azure podem ser criadas sem que seja necessário gerir hardware ou SOs. Isto significa que não tem de lidar com a aplicação de patches ao SO do servidor com atualizações de segurança críticas ou substituir os discos rígidos com falhas.
* **Criação de Scripts e Ferramentas**. Podem ser utilizados cmdlets do PowerShell e a CLI do Azure para criar, montar e gerir partilhas de armazenamento de Ficheiros como parte da administração das aplicações do Azure. Pode utilizar o portal do Azure e o Explorador de Armazenamento do Azure para criar e gerir partilhas de Ficheiros do Azure. 
* **Resiliência**. O armazenamento de Ficheiros do Azure foi criado de raiz para estar sempre disponível. Substituir as partilhas de ficheiros no local pelo armazenamento de Ficheiros do Azure significa que já não tem de se preocupar com falhas de energia ou problemas de rede. 
* **Programação familiar**. As aplicações que são executadas no Azure podem aceder a dados na partilha através das [APIs de E/S de sistema de ficheiros](https://msdn.microsoft.com/library/system.io.file.aspx). Os programadores, por conseguinte, podem tirar partido do respetivo código existente e competências para migrar as aplicações existentes. Para além das APIs de E/S de Sistema, pode utilizar as [Bibliotecas de Cliente do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn261237.aspx) ou da [API REST do Armazenamento do Azure](/rest/api/storageservices/file-service-rest-api).

As partilhas de Ficheiros do Azure podem ser utilizadas para:

* **Substituir os servidores de ficheiros no local**:  
    O armazenamento de Ficheiros do Azure pode ser utilizado para substituir completamente as partilhas de ficheiros em servidores de ficheiros no local ou em dispositivos NAS tradicionais. Os sistemas operativos populares, como Windows, macOS e Linux, podem montar facilmente partilhas de Ficheiros do Azure a partir de qualquer ponto do mundo.

* **Fazer a migração "lift-and-shift" de aplicações**:  
    Com o armazenamento de Ficheiros do Azure, é fácil fazer migrações “lift-and-shift” de aplicações para a cloud que utilizem partilhas de ficheiros no local para partilhar dados entre diferentes partes da aplicação. Para que tal aconteça, cada VM liga-se à partilha de ficheiros e, em seguida, pode ler e escrever ficheiros tal como faria numa partilha no local.

* **Simplificar o Desenvolvimento na Cloud**:  
    O armazenamento de Ficheiros do Azure pode ser utilizado de várias formas para simplificar projetos de desenvolvimento na cloud novos.
    * **Definições de Aplicações Partilhadas**:  
        Um padrão comum para aplicações distribuídas passa por ter ficheiros de configuração numa localização central, onde podem ser acedidos a partir de muitas VMs diferentes. Estes ficheiros de configuração podem agora ser armazenados numa partilha de Ficheiros do Azure e lidos por todas as instâncias das aplicações. Estas definições também podem ser geridas através da interface REST, que permite o acesso em todo o mundo aos ficheiros de configuração.

    * **Partilha de Diagnósticos**:  
        As partilhas de Ficheiros do Azure também podem ser utilizadas para guardar ficheiros de diagnósticos, como registos, métricas e informações de falhas O facto de estes dados estarem disponíveis através de SMB e da interface REST permite às aplicações criar ou tirar partido de diversas ferramentas de análises para processamento e análise dos mesmos.

    * **Dev/Test/Depuração**:  
        Muitas vezes, quando trabalham em VMs na cloud, os programadores ou administradores precisam de um conjunto de ferramentas ou utilitários. Instalar e distribuir estes utilitários em cada máquina virtual onde são precisos pode ser um exercício muito moroso. Com o armazenamento de Ficheiros do Azure, os programadores ou administradores podem armazenar as ferramentas de que mais gostam numa partilha de ficheiros, à qual se podem ligar facilmente a partir de qualquer máquina virtual.
        
## <a name="how-does-it-work"></a>Como funciona?
É muito mais fácil gerir partilhas de Ficheiros do Azure do que partilhas de ficheiros no local. O diagrama seguinte ilustra a construção da gestão do armazenamento de Ficheiros do Azure:

![Estrutura de Ficheiros](../../includes/media/storage-file-concepts-include/files-concepts.png)

* **Conta de Armazenamento**: todos os acessos ao Armazenamento do Azure são feitos através de uma conta de armazenamento. Veja Azure Storage Scalability and Performance Targets (Metas de Escalabilidade e Desempenho do Armazenamento do Azure) para obter detalhes acerca da capacidade das contas de armazenamento.
* **Partilha:** uma partilha do Armazenamento de Ficheiros é uma partilha de ficheiros SMB no Azure. Todos os ficheiros e diretórios têm de ser criados numa partilha principal. Uma conta pode conter um número ilimitado de partilhas e uma partilha pode armazenar um número ilimitado de ficheiros, até à capacidade total de 5 TB da partilha de ficheiros.
* **Diretório**: uma hierarquia opcional de diretórios.
* **Ficheiro**: um ficheiro na partilha. Um ficheiro pode ter um tamanho até 1 TB.
* **Formato do URL**: os ficheiros são endereçáveis através do formato de URL seguinte:  

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory/directories>/<file>
    ```
## <a name="next-steps"></a>Passos Seguintes
* [Create Azure File Share](storage-file-how-to-create-file-share.md) (Criar Partilha de Ficheiros do Azure)
* [Connect and Mount on Windows](storage-file-how-to-use-files-windows.md) (Ligar e Montar no Windows)
* [Connect and Mount on Linux](storage-how-to-use-files-linux.md) (Ligar e Montar no Linux)
* [Connect and Mount on macOS](storage-file-how-to-use-files-mac.md) (Ligar e Montar em macOS)
* [FAQ](storage-files-faq.md)
* [Resolução de problemas](storage-troubleshoot-file-connection-problems.md)

### <a name="conceptual-articles-and-videos"></a>Artigos e vídeos concetuais
* [Azure File storage: a frictionless cloud SMB file system for Windows and Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/) (Armazenamento de Ficheiros do Azure: um prático sistema de ficheiros SMB na cloud para Windows e Linux)

### <a name="tooling-support-for-azure-file-storage"></a>Suporte de ferramentas para o armazenamento de Ficheiros do Azure
* [Using Azure PowerShell with Azure Storage (Utilizar o Azure PowerShell com o Armazenamento do Azure)](storage-powershell-guide-full.md)
* [How to use AzCopy with Microsoft Azure Storage (Como utilizar o AzCopy com o Armazenamento do Microsoft Azure)](storage-use-azcopy.md)
* [Using the Azure CLI with Azure Storage (Utilizar a CLI do Azure com o Armazenamento do Azure)](storage-azure-cli.md#create-and-manage-file-shares)

### <a name="blog-posts"></a>Publicações no blogue
* [Azure File storage is now generally available (O Armazenamento de Ficheiros do Azure está agora disponível normalmente)](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure File storage](https://azure.microsoft.com/blog/inside-azure-file-storage/) (Por dentro do armazenamento de Ficheiros do Azure)
* [Introducing Microsoft Azure File Service (Introdução ao Serviço de Ficheiros do Microsoft Azure)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Migrating data to Azure File ](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/) (Migrar dados para os Ficheiros do Azure)

### <a name="reference"></a>Referência
* [Storage Client Library for .NET reference (Referência da Biblioteca de Clientes do Armazenamento para .NET)](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [File Service REST API reference (Referência da API REST do Serviço do Ficheiros)](http://msdn.microsoft.com/library/azure/dn167006.aspx)

