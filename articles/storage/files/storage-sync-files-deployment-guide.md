---
title: "Como implementar a sincronização de ficheiros do Azure (pré-visualização) | Microsoft Docs"
description: "Saiba como implementar a sincronização de ficheiros do Azure do início ao fim."
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
ms.openlocfilehash: b31b6ae413f72c626e2601ba860aad44ddaa29cd
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="how-to-deploy-azure-file-sync-preview"></a>Como implementar a sincronização de ficheiros do Azure (pré-visualização)
O Azure File Sync (pré-visualização) permite-lhe centralizar as partilhas de ficheiros da sua organização nos Ficheiros do Azure sem abdicar da flexibilidade, do desempenho e da compatibilidade de um servidor de ficheiros no local. Isto é feito ao transformar os Servidores do Windows numa cache rápida da partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter o número de caches que precisar em todo o mundo.

Recomendamos vivamente a leitura [planear uma implementação de ficheiros do Azure](storage-files-planning.md) e [planear uma implementação de sincronização de ficheiros do Azure](storage-sync-files-planning.md) antes de seguir os passos neste guia.

## <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Storage e um ficheiro de Azure partilham na mesma região que pretende implementar a sincronização de ficheiros do Azure. Para obter mais informações, consulte:
    - [Disponibilidade de região](storage-sync-files-planning.md#region-availability) de sincronização de ficheiros do Azure,
    - [Criar uma conta de armazenamento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para instruções passo a passo sobre como criar uma conta de armazenamento, e
    - [Criar uma partilha de ficheiros](storage-how-to-create-file-share.md) para instruções passo a passo sobre como criar uma partilha de ficheiros.
* Pelo menos um servidor Windows suportada ou cluster do Windows Server para sincronizar com sincronização de ficheiros do Azure. Consulte [interoperabilidade com o Windows Server](storage-sync-files-planning.md#azure-file-sync-interoperability) para obter mais informações sobre as versões suportadas do Windows Server.

## <a name="deploy-the-storage-sync-service"></a>Implementar o serviço de sincronização de armazenamento 
O serviço de sincronização de armazenamento é o recurso mais superior do Azure, que representa a sincronização de ficheiros do Azure. Para implementar um serviço de sincronização de armazenamento, navegue para o [portal do Azure](https://portal.azure.com/)e procure a sincronização de ficheiros do Azure. Depois de selecionar "Sincronização do Azure ficheiro (pré-visualização)" de resultados da pesquisa, selecione "Criar", a extrair o separador de abrir o "implementar armazenamento sincronizar".

O painel resultante pede-lhe as seguintes informações:

- **Nome**: um nome exclusivo (por subscrição) para o serviço de sincronização de armazenamento.
- **Subscrição**: da subscrição na qual pretende criar o serviço de sincronização de armazenamento. Dependendo da estratégia de configuração da sua organização, poderá ter acesso a uma ou mais subscrições. Uma subscrição do Azure é o contentor mais básico da faturação para cada serviço em nuvem (por exemplo, ficheiros do Azure).
- **Grupo de recursos**: um grupo de recursos é um grupo lógico de recursos do Azure, tal como uma conta de armazenamento ou um serviço de sincronização de armazenamento. Pode criar um novo grupo de recursos ou utilizar um grupo de recursos existente para a sincronização de ficheiros do Azure (Recomendamos que utilize grupos de recursos como contentores utilizadas para isolar os recursos logicamente para a sua organização, tais como agrupamento de recursos de RH ou recursos de um projeto específico) .
- **Localização**: A região na qual gostaria de implementar a sincronização de ficheiros do Azure. Apenas as regiões suportadas estão disponíveis nesta lista.

Depois do formulário de "A implementar o armazenamento sincronizar" foi concluído, clique em "Criar" para implementar o serviço de sincronização de armazenamento.

## <a name="prepare-windows-servers-for-use-with-azure-file-sync"></a>Preparar servidores do Windows para utilização com sincronização de ficheiros do Azure
Nunca por servidor com o qual pretende utilizar a sincronização de ficheiros do Azure, incluindo nós de servidor num Cluster de ativação pós-falha, execute os seguintes passos:

Para cada servidor, incluindo nós de servidor num Cluster de ativação pós-falha, que pretende utilizar com sincronização de ficheiros do Azure, execute os seguintes passos:

1. Desative a configuração de segurança avançada do Internet Explorer. Isto é apenas necessário para o registo do servidor inicial e pode ser reenabled depois do servidor tem foi registado.
    1. Abra o Gestor de servidor.
    2. Clique em **servidor Local**:  
        !["Servidor local" no lado esquerdo da IU do Gestor de servidor](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. Selecione a ligação **configuração de segurança avançada do IE** no painel propriedades secundárias:  
        ![A "i/e configuração de segurança avançada" na IU do Gestor de servidor](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. Selecione **desativar** para administradores e utilizadores na janela de pop-up de configuração de segurança avançada do Internet Explorer:  
        ![A janela de pop da configuração de segurança avançada do Internet Explorer com "Desligado" selecionado](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. Certifique-se de que está a executar, pelo menos, 5.1 do PowerShell.\* (5.1 do PowerShell é a predefinição no Windows Server 2016). Pode verificar que está a executar o PowerShell 5.1. \* ao observar o valor da propriedade do objeto $PSVersionTable PSVersion:

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    - Se o seu PSVersion é inferior a 5.1. \*, como será o caso com a maioria das instalações do Windows Server 2012 R2, pode facilmente atualizar ao descarregar e instalar [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). O pacote adequado para transferir e instalar para o Windows Server 2012 R2 é **Win8.1AndW2K12R2 KB\*\*\*\*\*\*\*-x64.msu**.

3. [Instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Sempre Recomendamos que utilize a versão mais recente dos módulos do PowerShell do Azure.

## <a name="install-the-azure-file-sync-agent"></a>Instalar o agente de sincronização de ficheiros do Azure
O agente de sincronização de ficheiros do Azure é um pacote transferível, que permitem que um servidor do Windows sejam sincronizados com um ficheiro de Azure partilha. O agente pode ser transferido a partir de [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Depois de transferido, faça duplo clique no pacote MSI para iniciar a instalação do agente de sincronização de ficheiros do Azure.

> [!Important]  
> Se pretender utilizar a sincronização de ficheiros do Azure com um Cluster de ativação pós-falha, o agente de sincronização de ficheiros do Azure terá de ser instalado em cada nó no cluster.

O pacote de instalação do agente de sincronização de ficheiros do Azure deve instalar com relativamente rapidamente sem demasiados pedidos adicionais. Recomendamos o seguinte:
- Caminho de instalação predefinido deixe `C:\Program Files\Azure\StorageSyncAgent`) para simplificar a manutenção de resolução de problemas e de servidor.
- Ativar o Microsoft Update para manter a sincronização de ficheiros do Azure atualizada. Todas as atualizações, incluindo atualizações de funcionalidades e correções para o agente de sincronização de ficheiros do Azure irão ocorrer a partir do Microsoft Update. Recomendamos sempre efetuando a atualização mais recente para sincronização de ficheiros do Azure. Consulte [política de atualização de sincronização de ficheiros do Azure](storage-sync-files-planning.md#azure-file-sync-agent-update-policy) para obter mais informações.

Na conclusão da instalação do agente de sincronização de ficheiros do Azure, a IU de registo do servidor será início automático. Consulte a secção seguinte para saber como registar este servidor de sincronização de ficheiros do Azure.

## <a name="register-windows-server-with-storage-sync-service"></a>Registar o servidor do Windows com o serviço de sincronização de armazenamento
Registar o servidor do Windows com um serviço de sincronização de armazenamento estabelece uma relação de confiança entre o servidor (ou o cluster) e o serviço de sincronização de armazenamento. A IU de registo do servidor deve início automático após a instalação do agente de sincronização de ficheiros do Azure, mas se este não, pode abri-lo manualmente da sua localização: `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`. Depois da IU do registo de servidor estiver aberta, clique em **início de sessão** para começar.

Após o início de sessão, serão apresentadas as seguintes informações:

![Uma captura de ecrã da IU do registo de servidor](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Subscrição do Azure**: A subscrição que contém o serviço de sincronização de armazenamento (consulte [implementar o serviço de sincronização de armazenamento](#deploy-the-storage-sync-service) acima). 
- **Grupo de recursos**: O grupo de recursos que contém o serviço de sincronização de armazenamento.
- **O serviço de sincronização de armazenamento**: O nome do serviço de sincronização de armazenamento com o qual pretende registar.

Assim que tiver selecionado as informações adequadas do pendentes a largar, clique em **registar** para concluir o registo do servidor. Como parte do processo de registo, é-lhe pedida uma adicional início de sessão.

## <a name="create-a-sync-group"></a>Criar um grupo de sincronização
Um grupo de sincronização define a topologia de sincronização para um conjunto de ficheiros. Pontos finais dentro de um grupo de sincronização serão mantidos sincronizados entre si. Um grupo de sincronização tem de conter pelo menos um ponto final da nuvem, que representa uma partilha de ficheiros do Azure, e um ponto final do servidor, que representa um caminho num servidor do Windows. Para criar um grupo de sincronização, navegue até ao seu serviço de sincronização de armazenamento na [portal do Azure](https://portal.azure.com/)e clique em **+ o grupo de sincronização**:

![Criar novo grupo de sincronização no portal do Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

O painel resultante pede-lhe as informações seguintes criar um grupo de sincronização com um ponto final da nuvem:

- **Nome do grupo de sincronização**: O nome do grupo de sincronização para ser criado. Este nome tem de ser exclusivo no âmbito do serviço de sincronização de armazenamento, mas pode ser qualquer nome que seja lógica para si.
- **Subscrição**: A subscrição que implementou o serviço de sincronização de armazenamento no [implementar o serviço de sincronização de armazenamento](#deploy-the-storage-sync-service) acima.
- **Conta de armazenamento**: clicar em "Selecionar a conta de armazenamento" pop abrirá um painel adicional que permite selecionar a conta de armazenamento que contém a partilha de ficheiros do Azure com o qual pretende sincronizar.
- **Partilha de ficheiros do Azure**: O nome da partilha de ficheiros do Azure com o qual pretende sincronizar.

Para adicionar um ponto final do servidor, navegue para o grupo de sincronização recém-criado e clique em "Adicionar ponto final do servidor".

![Adicionar um novo ponto final do servidor no painel do grupo de sincronização](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

Painel "Adicionar ponto final do servidor" resultante requer as seguintes informações para criar um ponto final do servidor:

- **Registar servidor**: O nome do servidor ou cluster no qual pretende criar o ponto final do servidor.
- **Caminho**: O caminho no servidor do Windows sejam sincronizados como parte do grupo de sincronização.
- **Criação de camadas de nuvem**: um comutador para ativar ou desativar a criação de camadas de nuvem que permitem que raramente utilizado ou aceder a ficheiros a camadas para ficheiros do Azure.
- **Espaço livre no volume**: a quantidade de espaço a reservar em volume no qual reside o ponto final do servidor. Por exemplo, se o espaço livre de Volume é definido como 50%, num volume com um único ponto final do servidor, aproximadamente meio a quantidade de dados vai ser colocado em camadas para ficheiros do Azure. Tenha em atenção que independentemente de nuvem de camadas estiver ativada, a partilha de ficheiros do Azure tem sempre uma cópia completa dos dados no grupo de sincronização.

Clique em "Criar" para adicionar o ponto final do servidor. Os ficheiros serão agora ser mantidos sincronizados entre a partilha de ficheiros do Azure e o Windows Server. 

> [!Important]  
> Pode efetuar alterações a qualquer nuvem ou no grupo de sincronização de ponto final do servidor e tem sincronizado de ficheiros para os pontos finais no grupo de sincronização. Se fizer uma alteração para o ponto final da nuvem (partilha de ficheiros do Azure) diretamente, tenha em atenção de que as alterações primeiro tem de ser detetados por uma tarefa de deteção de alteração de sincronização de ficheiros do Azure, que é apenas initatiated para um ponto final da nuvem, uma vez a cada 24 horas. Consulte o [FAQ de ficheiros do Azure](storage-files-faq.md#afs-change-detection) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes
- [Adicionar/remover um ponto de final do servidor de sincronização do ficheiro do Azure](storage-sync-files-server-endpoint.md)
- [Registar/anular o registo num servidor com sincronização de ficheiros do Azure](storage-sync-files-server-registration.md)