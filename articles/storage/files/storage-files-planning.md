---
title: "Planear uma implementação de ficheiros do Azure | Microsoft Docs"
description: "Saiba o que deve considerar quando planear uma implementação de ficheiros do Azure."
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
ms.date: 09/19/2017
ms.author: wgries
ms.openlocfilehash: c4f997b994bb337ad8a886d7ad09791cb587d4f9
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="planning-for-an-azure-files-deployment"></a>Planear uma implementação dos Ficheiros do Azure
[Ficheiros do Azure](storage-files-introduction.md) oferece completamente geridos partilhas de ficheiros na nuvem que estão acessíveis através do protocolo SMB padrão da indústria. Porque os ficheiros do Azure é totalmente gerido, é muito mais fácil de implementar e gerir um servidor de ficheiros ou um dispositivo NAS implementar a política em cenários de produção. Este artigo aborda os tópicos a ter em consideração quando implementar uma partilha de ficheiros do Azure para utilização em produção dentro da sua organização.

## <a name="management-concepts"></a>Conceitos de gestão
 O diagrama seguinte ilustra as construções de gestão de ficheiros do Azure:

![Estrutura de Ficheiros](./media/storage-files-introduction/files-concepts.png)

* **Conta de Armazenamento**: todos os acessos ao Armazenamento do Azure são feitos através de uma conta de armazenamento. Veja [Metas de Escalabilidade e Desempenho](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obter detalhes sobre a capacidade das contas de armazenamento.

* **Partilha:** uma partilha do Armazenamento de Ficheiros é uma partilha de ficheiros SMB no Azure. Todos os ficheiros e diretórios têm de ser criados numa partilha principal. Uma conta pode conter um número ilimitado de partilhas e uma partilha pode armazenar um número ilimitado de ficheiros, até a capacidade de total de 5 TiB de partilha de ficheiros.

* **Diretório**: uma hierarquia opcional de diretórios.

* **Ficheiro**: um ficheiro na partilha. Um ficheiro pode ter até 1 TiB de tamanho.

* **Formato de URL**: para pedidos a uma partilha de ficheiros de Azure efetuadas com o protocolo de REST de ficheiro, os ficheiros são endereçáveis utilizando o seguinte formato de URL:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/directories>/<file>
    ```

## <a name="data-access-method"></a>Método de acesso de dados
Ofertas de ficheiros do Azure dois, incorporados e convenientes de acesso a dados métodos que pode utilizar em separado ou em combinação entre si, para aceder aos seus dados:

1. **Nuvem acesso direto**: partilha de ficheiros de Azure qualquer pode ser montada por [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md), e/ou [Linux](storage-how-to-use-files-linux.md) com o (Server Message Block padrão da indústria O protocolo SMB) ou através da API de REST do ficheiro. Com o SMB, leituras e escritas em ficheiros na partilha são efetuadas diretamente na partilha de ficheiros no Azure. Para montar por uma VM no Azure, o cliente SMB no SO tem de suportar, pelo menos, o SMB 2.1. Para montar no local, tal como na estação de trabalho de um utilizador, o cliente do SMB suportado pela estação de trabalho tem de suportar, pelo menos, SMB 3.0 (com encriptação). Para além do SMB, novas aplicações ou serviços podem aceder diretamente a partilha de ficheiros através de REST de ficheiro, que fornece uma interface de programação de aplicações fácil e escalável para o desenvolvimento de software.
2. **Sincronização de ficheiros do Azure** (pré-visualização): com sincronização de ficheiros do Azure, partilhas podem ser replicadas para servidores Windows no local ou no Azure. Os utilizadores acederia a partilha de ficheiros através do Windows Server, tal como através de uma partilha SMB ou NFS. Isto é útil para cenários em que dados serão acedidos e modificados até que ponto ausente de um datacenter Azure, tal como um cenário de sucursal. Os dados podem ser replicados entre múltiplos pontos finais com o Windows Server, tal como entre várias sucursais. Por fim, dados podem ser colocado em camadas para ficheiros do Azure, para que todos os dados são continuarão acessíveis através do servidor, mas o servidor não tem uma cópia completa dos dados. Em vez disso, dados é perfeitamente resgatar os quando aberto pelo utilizador.

A tabela seguinte ilustra a forma como os seus utilizadores e aplicações podem aceder a partilha de ficheiros do Azure:

| | Acesso à nuvem direta | Sincronização de ficheiros do Azure |
|------------------------|------------|-----------------|
| Protocolos de que precisa utilizar? | Ficheiros do Azure suporta o SMB 2.1, SMB 3.0 e API de REST de ficheiros. | Aceder à partilha de ficheiros do Azure através de qualquer protocolos suportados no Windows Server (SMB, NFS, FTPS, etc.) |  
| Onde está a executar a carga de trabalho? | **No Azure**: ficheiros do Azure oferece acesso direto aos seus dados. | **No local com a rede lenta**: os clientes Windows, Linux e macOS podem montar uma partilha de ficheiro do Windows local no local como uma cache rápida da Azure da partilha de ficheiros. |
| O nível de ACLs precisa? | Nível de partilha e o ficheiro. | Nível de partilha, o ficheiro e o utilizador. |

## <a name="data-security"></a>Segurança de dados
Ficheiros do Azure tem várias opções incorporadas para garantir a segurança dos dados:

* Suporte para encriptação em ambos os protocolos de ativação pós-falha a transmissão: encriptação SMB 3.0 e REST de ficheiro através de HTTPS. Por predefinição: 
    * Os clientes que suportam encriptação SMB 3.0 enviarem e recebem dados através de um canal encriptado.
    * Os clientes que não suportam o SMB 3.0, podem comunicar intra-Centro de dados através de SMB 2.1 ou SMB 3.0 sem encriptação. Tenha em atenção que os clientes não são permitidos para comunicar o Centro de dados inter-através de SMB 2.1 ou SMB 3.0 sem encriptação.
    * Os clientes podem comunicar através de REST de ficheiros com HTTP ou HTTPS.
* Encriptação em rest ([encriptação do serviço de armazenamento do Azure](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): estamos no processo de ativar a encriptação de serviço de armazenamento (SSE) a plataforma subjacente do Storage do Azure. Isto significa que a encriptação será ativada por predefinição para todas as contas de armazenamento. Se estiver a criar uma nova conta de armazenamento numa região com a encriptação em-rest predefinição, não têm de fazer nada para ativar. Dados em rest está encriptada com chaves completamente gerido. Encriptação em rest não aumentar os custos de armazenamento ou reduzir o desempenho. 
* Requisito opcional de dados encriptados em trânsito: quando selecionada, ficheiros do Azure irá não permitir o acesso aos dados através de canais não encriptados. Mais concretamente, serão permitida apenas HTTPS e SMB 3.0 com ligações de encriptação. 

    > [!Important]  
    > Exigir a transferência segura de dados fará com que clientes SMB antigos sem capacidade de comunicar com o SMB 3.0 com encriptação falhar. Consulte [montar no Windows](storage-how-to-use-files-windows.md), [montar no Linux](storage-how-to-use-files-linux.md), [montar no macOS](storage-how-to-use-files-mac.md) para obter mais informações.

Para segurança máxima, recomendamos vivamente sempre ativar ambas as encriptação em-rest e ativar a encriptação de dados em trânsito sempre que estiver a utilizar clientes modernos para aceder aos seus dados. Por exemplo, se precisar de montar uma partilha numa VM Windows Server 2008 R2, que só suporta o SMB 2.1, terá de permitir o tráfego não encriptado à sua conta de armazenamento, uma vez que o SMB 2.1 não suporta encriptação.

Se estiver a utilizar sincronização de ficheiros do Azure para aceder à partilha de ficheiros do Azure, podemos sempre utilizará HTTPS e o SMB 3.0 com encriptação para sincronizar os dados aos seus servidores do Windows, independentemente se necessitar de encriptação de dados em rest.

## <a name="data-redundancy"></a>Redundância de dados
Ficheiros do Azure suporta duas opções de redundância de dados: armazenamento localmente redundante (LRS) e armazenamento georredundante (GRS). As secções seguintes descrevem as diferenças entre o armazenamento localmente redundante e armazenamento georredundante:

### <a name="locally-redundant-storage"></a>Armazenamento localmente redundante
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="geo-redundant-storage"></a>Armazenamento georredundante
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="data-growth-pattern"></a>Padrão de crescimento de dados
Atualmente, o tamanho máximo para uma partilha de ficheiros do Azure é TiB 5, que inclui os instantâneos de partilha. Devido a esta limitação atual, tem de considerar o crescimento de dados esperado ao implementar uma partilha de ficheiros do Azure. Tenha em atenção que uma conta de armazenamento do Azure, pode armazenar várias partilhas de um total de 500 TiB armazenados em todas as partilhas.

É possível sincronizar múltiplas partilhas de ficheiros do Azure para um único servidor de ficheiros do Windows com sincronização de ficheiros do Azure. Isto permite-lhe garantir que as partilhas de ficheiros de mais antigos, muito grande que pode ter no local podem ser colocadas em sincronização de ficheiros do Azure. Consulte [planear uma implementação de sincronização de ficheiros do Azure](storage-files-planning.md) para obter mais informações.

## <a name="data-transfer-method"></a>Método de transferência de dados
Existem muitas opções fácil em massa a transferência de dados a partir de um ficheiro existente partilharem, tais como uma partilha de ficheiros no local, para ficheiros do Azure. Alguns dos populares incluem (lista não exaustiva):

* **Sincronização de ficheiros do Azure**: como parte de uma sincronização primeiro entre uma partilha de ficheiros do Azure (uma "ponto final da nuvem") e um espaço de nomes de diretório de Windows (um "servidor de ponto final"), sincronização de ficheiros do Azure irá replicar todos os dados da partilha de ficheiros existentes ao Azure Files.
* **[Importar/exportar do Azure](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: o serviço do importar/exportar do Azure permite-lhe transferir de forma segura grandes quantidades de dados para uma partilha de ficheiros do Azure através do envio unidades de disco rígido para um datacenter do Azure. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: Robocopy é uma ferramenta de cópia bem conhecidos que é fornecido com o Windows e Windows Server. Robocopy pode ser utilizado para transferir dados para ficheiros do Azure ao montar a partilha de ficheiros localmente e, em seguida, utilizar a localização montada como o destino no comando Robocopy.
* **[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)**: o AzCopy é um utilitário da linha de comandos concebido para copiar dados e de ficheiros do Azure, bem como o Blob storage do Azure, utilizando os comandos simples com um desempenho ideal. AzCopy está disponível para o Windows e Linux.

## <a name="next-steps"></a>Passos seguintes
* [Planear uma implementação de sincronização de ficheiros do Azure](storage-sync-files-planning.md)
* [Implementar os ficheiros do Azure](storage-files-deployment-guide.md)
* [Implementar a sincronização de ficheiros do Azure](storage-sync-files-deployment-guide.md)