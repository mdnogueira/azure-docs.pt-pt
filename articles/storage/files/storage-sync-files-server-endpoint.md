---
title: "Adicionar/remover uma sincronização de ficheiros do Azure (pré-visualização) Endpoint de servidor | Microsoft Docs"
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
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 92ac80953623a5a94d3104f30787c9636308c707
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="addremove-an-azure-file-sync-preview-server-endpoint"></a>Adicionar/remover uma sincronização de ficheiros do Azure (pré-visualização) ponto final do servidor
O Azure File Sync (pré-visualização) permite-lhe centralizar as partilhas de ficheiros da sua organização nos Ficheiros do Azure sem abdicar da flexibilidade, do desempenho e da compatibilidade de um servidor de ficheiros no local. Isto é feito ao transformar os Servidores do Windows numa cache rápida da partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter o número de caches que precisar em todo o mundo.

A *ponto final do servidor* representa uma localização específica num *servidor registado*, tais como uma pasta no volume do servidor ou na raiz do volume. Vários pontos finais de servidor pode existir no mesmo volume se os espaços de nomes não são sobreposição (por exemplo, F:\sync1 e F:\sync2). Pode configurar políticas de camadas na nuvem individualmente para cada ponto final do servidor. Se adicionar uma localização do servidor com um conjunto de ficheiros como um ponto final do servidor a um grupo de sincronização, os ficheiros serão Unidos com quaisquer outros ficheiros já em execução no grupo de sincronização de outros pontos finais.

Consulte [como implementar a sincronização de ficheiros do Azure (pré-visualização)](storage-sync-files-deployment-guide.md) para obter informações sobre como implementar a sincronização de ficheiros do Azure ponto-a-ponto.

## <a name="prerequisites"></a>Pré-requisitos
Para criar um ponto final do servidor, deve primeiro garantir que são cumpridos os seguintes critérios: 
- O servidor tem instalado o agente de sincronização de ficheiros do Azure e foi registado. Podem encontrar instruções para instalar o agente de sincronização de ficheiros do Azure a [registar/anular o registo de um servidor com sincronização de ficheiros do Azure (pré-visualização)](storage-sync-files-server-registration.md) artigo. 
- Certifique-se de que o serviço de sincronização de armazenamento foi implementado. Consulte [como implementar a sincronização de ficheiros do Azure (pré-visualização)](storage-sync-files-deployment-guide.md) para obter detalhes sobre como implementar um serviço de sincronização de armazenamento. 
- Certifique-se de que o grupo de sincronização foi implementado. Saiba como [criar um grupo de sincronização](storage-sync-files-deployment-guide.md#create-a-sync-group).
- Certifique-se de que o servidor está ligado à internet e de que o Azure está acessível.

## <a name="add-a-server-endpoint"></a>Adicionar um ponto final do servidor
Para adicionar um ponto final do servidor, navegue para o grupo de sincronização pretendido e selecione "Adicionar ponto final do servidor".

![Adicionar um novo ponto final do servidor no painel do grupo de sincronização](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

As seguintes informações são necessárias em **adicionar ponto final do servidor**:

- **Registar servidor**: O nome do servidor ou o cluster para criar o ponto final do servidor.
- **Caminho**: O caminho no servidor do Windows sejam sincronizados como parte do grupo de sincronização.
- **Criação de camadas de nuvem**: um comutador para ativar ou desativar a criação de camadas de nuvem que permitem que raramente utilizado ou aceder a ficheiros a camadas para ficheiros do Azure.
- **Espaço livre no volume**: a quantidade de espaço livre para de reserva no volume que reside o ponto final do servidor. Por exemplo, se o espaço livre de volume é definido como 50%, num volume com um único ponto final do servidor, aproximadamente meio a quantidade de dados vai ser colocado em camadas para ficheiros do Azure. Independentemente de se na nuvem em camadas é ativada, a partilha de ficheiros do Azure tem sempre uma cópia completa dos dados no grupo de sincronização.

Selecione **criar** para adicionar o ponto final do servidor. Os ficheiros dentro de um espaço de nomes de um grupo de sincronização irão agora ser mantidos sincronizados. 

## <a name="remove-a-server-endpoint"></a>Remover um ponto final do servidor
Quando ativada para um ponto final do servidor, na nuvem será camada *camada* ficheiros para as partilhas de ficheiros do Azure. Isto permite que as partilhas de ficheiros no local agir como uma cache, em vez de uma cópia completa de conjunto de dados, para utilizar de forma eficaz de espaço no servidor de ficheiros. No entanto, se um ponto final do servidor é removido com ficheiros em camadas ainda localmente no servidor, ficará unaccessible esses ficheiros. Por conseguinte, se continuaram acesso ao ficheiro for pretendido, tem de recuperar todos os ficheiros em camadas de ficheiros do Azure antes de continuar com deregistration. 

Isto pode ser feito com o cmdlet do PowerShell conforme mostrado abaixo:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> Se o volume local que aloja o servidor não tem espaço livre suficiente para recuperar todos os dados em camadas, o `Invoke-StorageSyncFileRecall` cmdlet falhar.  

Para remover o ponto final do servidor:

1. Navegue para o serviço de sincronização de armazenamento em que o servidor está registado.
2. Navegue para o grupo de sincronização pretendido.
3. Remova o ponto final do servidor pretendidos ao nível do grupo de sincronização no serviço de sincronização de armazenamento. Isto pode ser conseguido clicando com o ponto final do servidor relevantes no painel do grupo de sincronização.

    ![Remover um ponto final do servidor de um grupo de sincronização](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Passos seguintes
- [Registar/anular o registo de um servidor com sincronização de ficheiros do Azure (pré-visualização)](storage-sync-files-server-registration.md)
- [Planear uma implementação de sincronização de ficheiros do Azure](storage-sync-files-planning.md)