---
title: "Implementar a sincronização de ficheiros do Azure (pré-visualização) | Microsoft Docs"
description: "Saiba como implementar a sincronização de ficheiros do Azure, do início ao fim."
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
ms.openlocfilehash: 42a0e7a3816e0f1d96951feac210e5770add4fe1
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="deploy-azure-file-sync-preview"></a>Implementar a sincronização de ficheiros do Azure (pré-visualização)
Utilize sincronização de ficheiros do Azure (pré-visualização) para centralizar o processamento de partilhas de ficheiros da sua organização nos ficheiros do Azure, mantendo o flexibilidade, o desempenho e a compatibilidade de um servidor de ficheiros no local. Sincronização de ficheiros do Azure transforma do Windows Server para uma cache rápida da Azure da partilha de ficheiros. Pode utilizar qualquer protocolo de que está disponível no Windows Server para aceder aos seus dados localmente, incluindo SMB, NFS e FTPS. Pode ter caches tantos conforme necessário por todo o mundo.

Recomendamos vivamente que leia [planear uma implementação de ficheiros do Azure](storage-files-planning.md) e [planear uma implementação de sincronização de ficheiros do Azure](storage-sync-files-planning.md) antes de concluir os passos descritos neste artigo.

## <a name="prerequisites"></a>Pré-requisitos
* Uma conta de armazenamento do Azure e um ficheiro do Azure partilham na mesma região que pretende implementar a sincronização de ficheiros do Azure. Para obter mais informações, consulte:
    - [Disponibilidade de região](storage-sync-files-planning.md#region-availability) para sincronização de ficheiros do Azure.
    - [Criar uma conta de armazenamento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obter uma descrição passo a passo sobre como criar uma conta de armazenamento.
    - [Criar uma partilha de ficheiros](storage-how-to-create-file-share.md) para obter uma descrição passo a passo sobre como criar uma partilha de ficheiros.
* Pelo menos uma instância suportada do Windows Server ou cluster do Windows Server para sincronizar com sincronização de ficheiros do Azure. Para obter mais informações sobre as versões suportadas do Windows Server, consulte [interoperabilidade com o Windows Server](storage-sync-files-planning.md#azure-file-sync-interoperability).

## <a name="deploy-the-storage-sync-service"></a>Implementar o serviço de sincronização de armazenamento 
O serviço de sincronização de armazenamento é o recurso mais superior do Azure para a sincronização de ficheiros do Azure. Para implementar um serviço de sincronização do Storage, vá para o [portal do Azure](https://portal.azure.com/)e, em seguida, procure a sincronização de ficheiros do Azure. Nos resultados da pesquisa, selecione **sincronização de ficheiros do Azure (pré-visualização)**e, em seguida, selecione **criar** para abrir o **implementar sincronização de armazenamento** separador.

No painel que se abre, introduza as seguintes informações:

- **Nome**: um nome exclusivo (por subscrição) para o serviço de sincronização de armazenamento.
- **Subscrição**: A subscrição na qual pretende criar o serviço de sincronização de armazenamento. Dependendo da estratégia de configuração da sua organização, poderá ter acesso a uma ou mais subscrições. Uma subscrição do Azure é o contentor mais básico da faturação para cada serviço em nuvem (por exemplo, ficheiros do Azure).
- **Grupo de recursos**: um grupo de recursos é um grupo lógico de recursos do Azure, tal como uma conta de armazenamento ou um serviço de sincronização de armazenamento. Pode criar um novo grupo de recursos ou utilizar um grupo de recursos existente para a sincronização de ficheiros do Azure. (Recomendamos que utilize grupos de recursos como contentores para isolar os recursos logicamente para a sua organização, tais como agrupamento de recursos de RH ou recursos para um projeto específico.)
- **Localização**: A região na qual pretende implementar a sincronização de ficheiros do Azure. Apenas as regiões suportadas estão disponíveis nesta lista.

Quando tiver terminado, selecione **criar** para implementar o serviço de sincronização de armazenamento.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Preparar o Windows Server para utilizar com sincronização de ficheiros do Azure
Para cada servidor que pretende utilizar com sincronização de ficheiros do Azure, incluindo nós de servidor num Cluster de ativação pós-falha, execute os seguintes passos:

1. Desativar **configuração de segurança avançada do Internet Explorer**. Isto é necessário apenas para o registo de servidor inicial. Pode voltar a ativá-lo Depois do servidor foi registado.
    1. Abra o Gestor de servidor.
    2. Clique em **servidor Local**:  
        !["Servidor local" no lado esquerdo da IU do Gestor de servidor](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. No **propriedades** subpane, selecione a ligação **configuração de segurança avançada do IE**.  
        ![O painel de "Configuração avançada do IE segurança" na IU do Gestor de servidor](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. No **da configuração de segurança avançada do Internet Explorer** caixa de diálogo, selecione **desativar** para **administradores** e **utilizadores**:  
        ![A janela de pop da configuração de segurança avançada do Internet Explorer com "Desligado" selecionado](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. Certifique-se de que está a executar, pelo menos, 5.1 do PowerShell.\* (5.1 do PowerShell é a predefinição no Windows Server 2016). Pode verificar que está a executar 5.1 do PowerShell. \* ao observar o valor da **PSVersion** propriedade o **$PSVersionTable** objeto:

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    Se o valor de PSVersion for inferior a 5.1. \*, como será o caso com a maioria das instalações do Windows Server 2012 R2, pode facilmente atualizar ao descarregar e instalar [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). O pacote adequado para transferir e instalar para o Windows Server 2012 R2 é **Win8.1AndW2K12R2 KB\*\*\*\*\*\*\*-x64.msu**.

3. [Instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Recomendamos que utilize a versão mais recente dos módulos do PowerShell do Azure.

## <a name="install-the-azure-file-sync-agent"></a>Instalar o agente de sincronização de ficheiros do Azure
O agente de sincronização de ficheiros do Azure é um pacote transferível, que permite que o Windows Server ser sincronizados com uma partilha de ficheiros do Azure. Pode transferir o agente do [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Quando a transferência estiver concluída, faça duplo clique o pacote MSI para iniciar a instalação do agente de sincronização de ficheiros do Azure.

> [!Important]  
> Se pretender utilizar a sincronização de ficheiros do Azure com um Cluster de ativação pós-falha, o agente de sincronização de ficheiros do Azure tem de estar instalado em cada nó no cluster.

O pacote de instalação do agente de sincronização de ficheiros do Azure deverá instalar relativamente rapidamente e sem demasiados pedidos adicionais. Recomendamos que efetue o seguinte:
- Deixe o caminho de instalação predefinido (c:\Programas\Microsoft Files\Azure\StorageSyncAgent), para simplificar a manutenção de resolução de problemas e de servidor.
- Ative o Microsoft Update manter a sincronização de ficheiros do Azure atualizada. Todas as atualizações, para o agente de sincronização de ficheiros do Azure, incluindo atualizações de funcionalidades e correções, ocorrerem do Microsoft Update. Recomendamos que instale a atualização mais recente para sincronização de ficheiros do Azure. Para obter mais informações, consulte [política de atualização de sincronização de ficheiros do Azure](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Quando a instalação do agente de sincronização de ficheiros do Azure estiver concluída, a IU do registo de servidor abra automaticamente. Para aprender a registar este servidor de sincronização de ficheiros do Azure, consulte a secção seguinte.

## <a name="register-windows-server-with-storage-sync-service"></a>Registar o servidor do Windows com o serviço de sincronização de armazenamento
Registar o servidor do Windows com um serviço de sincronização de armazenamento estabelece uma relação de confiança entre o servidor (ou o cluster) e o serviço de sincronização de armazenamento. A IU de registo do servidor deve abrir automaticamente após a instalação do agente de sincronização de ficheiros do Azure. Se não, pode abri-lo manualmente da localização do ficheiro: c:\Programas\Microsoft Files\Azure\StorageSyncAgent\ServerRegistration.exe. Quando abre a IU de registo do servidor, selecione **início de sessão** para começar.

Depois de iniciar sessão, serão apresentadas as seguintes informações:

![Uma captura de ecrã da IU do registo de servidor](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Subscrição do Azure**: A subscrição que contém o serviço de sincronização de armazenamento (consulte [implementar o serviço de sincronização de armazenamento](#deploy-the-storage-sync-service)). 
- **Grupo de recursos**: O grupo de recursos que contém o serviço de sincronização de armazenamento.
- **O serviço de sincronização de armazenamento**: O nome do serviço de sincronização de armazenamento com o qual pretende registar.

Depois de selecionar as informações adequadas, selecione **registar** para concluir o registo do servidor. Como parte do processo de registo, é-lhe pedida uma adicional início de sessão.

## <a name="create-a-sync-group"></a>Criar um grupo de sincronização
Um grupo de sincronização define a topologia de sincronização para um conjunto de ficheiros. Pontos finais dentro de um grupo de sincronização são mantidos sincronizados entre si. Um grupo de sincronização tem de conter pelo menos um ponto final da nuvem, que representa uma partilha de ficheiros do Azure, e um ponto final do servidor, que representa um caminho no Windows Server. Para criar um grupo de sincronização, no [portal do Azure](https://portal.azure.com/), aceda ao seu serviço de sincronização de armazenamento e, em seguida, selecione **+ o grupo de sincronização**:

![Criar um novo grupo de sincronização no portal do Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

No painel de que se abre, introduza as seguintes informações para criar um grupo de sincronização com um ponto final da nuvem:

- **Nome do grupo de sincronização**: O nome do grupo de sincronização para ser criado. Este nome tem de ser exclusivo no âmbito do serviço de sincronização de armazenamento, mas pode ser qualquer nome que seja lógica para si.
- **Subscrição**: A subscrição em que implementou o serviço de sincronização de armazenamento no [implementar o serviço de sincronização de armazenamento](#deploy-the-storage-sync-service).
- **Conta de armazenamento**: Se selecionar **Selecionar conta de armazenamento**, outro painel aparece na qual pode selecionar a conta de armazenamento que tenha a partilha de ficheiros do Azure que pretende sincronizar com.
- **Partilha de ficheiros do Azure**: O nome da partilha de ficheiros do Azure com o qual pretende sincronizar.

Para adicionar um ponto final do servidor, vá para o grupo de sincronização recém-criado e, em seguida, selecione **adicionar ponto final do servidor**.

![Adicionar um novo ponto final do servidor no painel do grupo de sincronização](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

No **adicionar ponto final do servidor** painel, introduza as seguintes informações para criar um ponto final do servidor:

- **Registar servidor**: O nome do servidor ou cluster onde pretende criar o ponto final do servidor.
- **Caminho**: caminho do Windows Server para ser sincronizada com êxito como parte do grupo de sincronização.
- **Criação de camadas de nuvem**: um comutador para ativar ou desativar na nuvem em camadas. Com a nuvem em camadas, raramente utilizado ou aceder a ficheiros pode ser colocado em camadas para ficheiros do Azure.
- **Espaço livre no volume**: A quantidade de espaço a reservar em volume no qual o ponto final do servidor está localizado. Por exemplo, se o espaço livre no volume estiver definido como 50%, num volume que tem um único ponto final do servidor, aproximadamente meio a quantidade de dados está em camadas para ficheiros do Azure. Independentemente de se na nuvem em camadas é ativada, a partilha de ficheiros do Azure tem sempre uma cópia completa dos dados no grupo de sincronização.

Para adicionar o ponto final do servidor, selecione **criar**. Os ficheiros são agora mantidos sincronizados em toda a partilha de ficheiros do Azure e o Windows Server. 

> [!Important]  
> Pode efetuar alterações a qualquer ponto final da nuvem ou no grupo de sincronização de ponto final do servidor e os ficheiros foram sincronizadas para os pontos finais no grupo de sincronização. Se fizer uma alteração para o ponto final da nuvem (partilha de ficheiros do Azure) diretamente, alterações primeiro tem de ser detetados por uma tarefa de deteção de alteração de sincronização de ficheiros do Azure. Uma tarefa de deteção de alteração é iniciada, para um ponto final da nuvem, apenas uma vez a cada 24 horas. Para obter mais informações, consulte [ficheiros do Azure perguntas mais frequentes](storage-files-faq.md#afs-change-detection).

## <a name="next-steps"></a>Passos seguintes
- [Adicionar ou remover um Azure sincronização do ponto final do ficheiro](storage-sync-files-server-endpoint.md)
- [Registar ou anular o registo de um servidor com sincronização de ficheiros do Azure](storage-sync-files-server-registration.md)
