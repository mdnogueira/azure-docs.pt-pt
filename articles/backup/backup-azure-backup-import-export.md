---
title: "Cópia de segurança do Azure - cópia de segurança Offline ou propagação inicial através do serviço de importação/exportação do Azure | Microsoft Docs"
description: "Saiba como a cópia de segurança do Azure permite-lhe enviar dados fora da rede utilizando o serviço importar/exportar do Azure. Este artigo explica a propagação offline dos dados de cópia de segurança iniciais, ao utilizar o serviço de exportação de importação do Azure."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: ada19c12-3e60-457b-8a6e-cf21b9553b97
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 4/20/2017
ms.author: saurse;nkolli;trinadhk
ms.openlocfilehash: 074d21269206b243f8b0e8747811544132805229
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Fluxo de trabalho de cópias de segurança offline no Azure Backup
Cópia de segurança do Azure tem vários resulta numa eficiência incorporada que reduzir os custos de armazenamento e de rede durante as cópias de segurança completas iniciais dos dados no Azure. As cópias de segurança completas iniciais normalmente transferem grandes quantidades de dados e necessitam de mais largura de banda quando comparado com as cópias de segurança subsequentes transferir apenas as diferenças/incrementais. Cópia de segurança do Azure comprime as cópias de segurança iniciais. Durante o processo de propagação offline, cópia de segurança do Azure pode utilizar discos para carregar o comprimido inicial cópia de segurança de dados offline para o Azure.  

O processo offline propagação da cópia de segurança do Azure está totalmente integrado com o [serviço importar/exportar do Azure](../storage/common/storage-import-export-service.md) que permite-lhe transferir dados para o Azure através da utilização de discos. Se tiver terabytes (TBs) de dados de cópia de segurança iniciais que têm de ser transferidos através de uma rede de latência alta e baixa largura de banda, pode utilizar o fluxo de trabalho offline propagação para enviar a cópia de segurança inicial num ou mais unidades de disco rígido para um datacenter do Azure. Este artigo fornece uma descrição geral dos passos que conclua este fluxo de trabalho.

## <a name="overview"></a>Descrição geral
Com a capacidade de cópia de segurança do Azure e do Azure para importar/exportar offline seeding, é simple carregar os dados offline para o Azure através da utilização de discos. Em vez de transferir a cópia inicial completa através da rede, os dados de cópia de segurança são escritos um *localização de transição*. Depois de concluída a cópia para a localização de transição com a ferramenta de importação/exportação do Azure, são escritos estes dados para uma ou mais unidades SATA, dependendo da quantidade de dados. Estas unidades, eventualmente, são fornecidas para o datacenter do Azure mais próximo.

O [da cópia de segurança do Azure (e mais tarde) de atualização de Agosto de 2016](http://go.microsoft.com/fwlink/?LinkID=229525) inclui o *ferramenta de preparação de disco do Azure*, com o nome AzureOfflineBackupDiskPrep, que:

* Ajuda-o a preparar a unidades de importação do Azure utilizando a ferramenta de importação/exportação do Azure.
* Cria automaticamente uma tarefa de importação do Azure para o serviço importar/exportar do Azure no [portal clássico do Azure](https://manage.windowsazure.com) , por oposição a criação da mesma manualmente com versões anteriores do Backup do Azure.

Após a conclusão do carregamento dos dados de cópia de segurança para o Azure, cópias de segurança do Azure copia os dados de cópia de segurança para o Cofre de cópia de segurança e as cópias de segurança incrementais.

> [!NOTE]
> Para utilizar a ferramenta de preparação de disco do Azure, certifique-se de que instalou a atualização de Agosto de 2016 da cópia de segurança do Azure (ou posterior) e executar todos os passos do fluxo de trabalho com o mesmo. Se estiver a utilizar uma versão antiga do Backup do Azure, pode preparar a unidade SATA utilizando a ferramenta de importação/exportação de Azure conforme detalhado nas secções posteriores deste artigo.
>
>

## <a name="prerequisites"></a>Pré-requisitos
* [Familiarize-se com o fluxo de trabalho do Azure para importar/exportar](../storage/common/storage-import-export-service.md).
* Antes de iniciar o fluxo de trabalho, certifique-se do seguinte:
  * Foi criado um cofre de cópia de segurança do Azure.
  * As credenciais do cofre tiverem sido transferidas.
  * O agente do Backup do Azure foi instalado no cliente do Windows Server/Windows ou servidor do System Center Data Protection Manager e o computador está registado no cofre do Backup do Azure.
* [Transferir as definições de ficheiros de publicação do Azure](https://manage.windowsazure.com/publishsettings) no computador a partir do qual planeia efetuar uma cópia dos seus dados.
* Prepare uma localização de transição, que pode ser uma partilha de rede ou a unidade adicional no computador. A localização de transição é armazenamento transitório e é utilizada temporariamente durante este fluxo de trabalho. Certifique-se de que a localização de transição tem espaço em disco suficiente para conter a cópia inicial. Por exemplo, se estiver a tentar fazer uma cópia de segurança de um servidor de ficheiros de 500 GB, certifique-se de que a área de transição é pelo menos de 500 GB. (Uma quantidade menor é utilizada devido a compressão.)
* Certifique-se de que está a utilizar uma unidade suportada. Apenas 2,5 polegada SSD ou 2,5 ou 3.5 polegadas SATA II/III interno unidades de disco rígido são suportados para utilização com o serviço de importação/exportação. Pode utilizar unidades de disco rígido até 10 TB. Verifique o [documentação do serviço do Azure para importar/exportar](../storage/common/storage-import-export-service.md#hard-disk-drives) para o conjunto de discos com o serviço suporta mais recente.
* Ative o BitLocker no computador para que o escritor de unidade SATA está ligado.
* [Transferir a ferramenta do Azure para importar/exportar](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) para o computador para que o escritor de unidade SATA está ligado. Este passo não é necessário se tiver transferido e instalado a atualização de Agosto de 2016 da cópia de segurança do Azure (ou posterior).

## <a name="workflow"></a>Fluxo de trabalho
As informações nesta secção ajuda-o a concluir o fluxo de trabalho de cópia de segurança offline, para que os dados podem ser fornecidos para um datacenter do Azure e carregados para o armazenamento do Azure. Se tiver dúvidas sobre o serviço de importação ou em qualquer aspeto do processo, consulte o [descrição geral do serviço de importação](../storage/common/storage-import-export-service.md) documentação referenciada anteriormente.

### <a name="initiate-offline-backup"></a>Iniciar a cópia de segurança offline
1. Quando agendar uma cópia de segurança, verá o ecrã seguinte (no Windows Server, o cliente do Windows ou o System Center Data Protection Manager).

    ![Ecrã de importação](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Eis o ecrã correspondente no System Center Data Protection Manager: <br/>
    ![Ecrã de importação do DPM](./media/backup-azure-backup-import-export/dpmoffline.png)

    A descrição das entradas é o seguinte:

    * **Localização de transição**: A localização de armazenamento temporário para o qual a cópia de segurança inicial é escrita. Isto pode ser num computador local ou uma partilha de rede. Se o computador de cópia e o computador de origem são diferentes, é recomendado que especifique o caminho de rede completa de localização de transição.
    * **Nome da tarefa de importação do Azure**: O nome exclusivo que importar do Azure Backup do Azure e do serviço controlam a transferência de dados enviados em discos para o Azure.
    * **Definições de publicação do Azure**: ficheiro um XML que contém informações sobre o perfil de subscrição. Também contém as credenciais seguras que estão associadas a sua subscrição. Pode [transfira o ficheiro](https://manage.windowsazure.com/publishsettings). Forneça o caminho local para o ficheiro de definições de publicação.
    * **ID de subscrição do Azure**: ID de subscrição do Azure para a subscrição em que pretende iniciar a tarefa de importação do Azure. Se tiver várias subscrições do Azure, utilize o ID da subscrição que pretende associar a tarefa de importação.
    * **Conta de armazenamento do Azure**: A conta de armazenamento de tipo clássico fornecida subscrição do Azure que será associada a tarefa de importação do Azure.
    * **Contentor de armazenamento do Azure**: O nome do blob de armazenamento de destino na conta do storage do Azure onde os dados desta tarefa são importados.

    > [!NOTE]
    > Se tiver registado o servidor para um cofre dos serviços de recuperação do Azure a partir de [portal do Azure](https://portal.azure.com) para as suas cópias de segurança e não existem numa subscrição do fornecedor de solução em nuvem (CSP), pode ainda criar uma conta de armazenamento de tipo clássico do portal do Azure e utilizá-lo para o fluxo de trabalho de cópia de segurança offline.
    >
    >

     Guarde todas as informações neste porque tem de introduzi-lo novamente nos seguintes passos. Apenas o *localização de transição* é necessário se tiver utilizado a ferramenta de preparação de disco do Azure para preparar os discos.    
2. Conclua o fluxo de trabalho e, em seguida, selecione **cópia de segurança agora** na consola de gestão de cópia de segurança do Azure para iniciar a cópia da cópia de segurança offline. A cópia de segurança inicial é escrita para a área de transição como parte deste passo.

    ![Cópia de segurança agora](./media/backup-azure-backup-import-export/backupnow.png)

    Para concluir o fluxo de trabalho correspondente no System Center Data Protection Manager, clique com botão direito do **grupo de proteção**e, em seguida, escolha o **criar ponto de recuperação** opção. Em seguida, escolha o **proteção Online** opção.

    ![Agora cópia de segurança do DPM](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Depois de concluída a operação, a localização de transição está pronta para ser utilizado para a preparação do disco.

    ![Progresso da cópia de segurança](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-a-sata-drive-and-create-an-azure-import-job-by-using-the-azure-disk-preparation-tool"></a>Preparar uma unidade SATA e criar uma tarefa de importação do Azure utilizando a ferramenta de preparação de disco do Azure
A ferramenta de preparação de disco do Azure está disponível no diretório de instalação do agente dos serviços de recuperação (atualizar de Agosto de 2016 e posterior) no caminho seguinte.

   *\Microsoft* *azure* *recuperação* *serviços* * Agent\Utils\*

1. Vá para o diretório e copie o **AzureOfflineBackupDiskPrep** diretório para um computador de cópia no qual estão montadas as unidades para estar preparado. Certifique-se o seguinte no que se refere o computador de cópia:

    * O computador de cópia pode aceder a localização de transição para o fluxo de trabalho offline seeding utilizando o mesmo caminho de rede que foi fornecido o **iniciar cópia de segurança offline** fluxo de trabalho.
    * O BitLocker está ativado no computador.
    * O computador pode aceder ao portal do Azure.

    Se necessário, o computador de cópia pode ser o mesmo que o computador de origem.
2. Abra uma linha de comandos elevada no computador de cópia com o diretório da ferramenta de preparação de disco do Azure como o diretório atual e execute o seguinte comando:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to PublishSettingsFile*>]`

    | Parâmetro | Descrição |
    | --- | --- |
    | s:&lt;*caminho de localização de transição*&gt; |Entrada obrigatória que é utilizada para fornecer o caminho para a localização de transição que introduziu no **iniciar cópia de segurança offline** fluxo de trabalho. |
    | p:&lt;*caminho PublishSettingsFile*&gt; |Entrada opcional que é utilizada para fornecer o caminho para o **definições de publicação do Azure** ficheiro que introduziu no **iniciar cópia de segurança offline** fluxo de trabalho. |

    > [!NOTE]
    > O &lt;caminho PublishSettingFile&gt; valor é obrigatório quando o computador de cópia e o computador de origem são diferentes.
    >
    >

    Quando executar o comando, a ferramenta de pedidos a seleção da tarefa de importação do Azure que corresponde às unidades que precisam para estar preparado. Se apenas uma tarefa de importação único está associada a com a localização de transição fornecida, verá um ecrã de como a que se segue.

    ![Entrada de ferramenta de preparação de disco do Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
3. Introduza a letra de unidade da vírgula decimal para o disco montado que pretende preparar para a transferência para o Azure. Indique confirmação para a formatação da unidade quando lhe for pedido.

    Em seguida, inicia a ferramenta preparar o disco com os dados de cópia de segurança. Terá de anexar discos adicionais quando lhe for pedido pela ferramenta no caso do disco fornecido não tem espaço suficiente para os dados de cópia de segurança. <br/>

    No final da execução bem sucedida da ferramenta, um ou mais discos que forneceu estão preparados para envio para o Azure. Além disso, uma tarefa de importação com o nome que forneceu durante a **iniciar cópia de segurança offline** fluxo de trabalho é criado no portal clássico do Azure. Por fim, a ferramenta apresenta o endereço de envio para o datacenter do Azure em que os discos têm de ser fornecidos e a ligação para localizar a tarefa de importação no portal clássico do Azure.

    ![Preparação de disco do Azure completa](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

4. São enviados os discos para o endereço que a ferramenta fornecida e mantenha o número de controlo para consulta futura.<br/>

5. Quando acede a hiperligação que apresentada a ferramenta, consulte a conta de armazenamento do Azure que especificou no **iniciar cópia de segurança offline** fluxo de trabalho. Aqui, pode ver a tarefa de importação criado recentemente no **importar/EXPORTAR** separador da conta de armazenamento.

    ![Tarefa de importação criada](./media/backup-azure-backup-import-export/ImportJobCreated.png)<br/>

6. Clique em **informações de envio** na parte inferior da página para atualizar os seus detalhes de contacto conforme mostrado no ecrã seguinte. A Microsoft utiliza estas informações para enviar os discos para, depois de concluída a tarefa de importação.

    ![Informações de contacto](./media/backup-azure-backup-import-export/contactInfoAddition.PNG)<br/>

7. Introduza os detalhes de envio no ecrã seguinte. Forneça o **operadora de entrega** e **controlo número** detalhes que correspondem aos discos enviadas para o datacenter do Azure.

    ![Informações de envio](./media/backup-azure-backup-import-export/shippingInfoAddition.PNG)<br/>

### <a name="complete-the-workflow"></a>Concluir o fluxo de trabalho
Após a conclusão da tarefa de importação, os dados de cópia de segurança iniciais estão disponíveis na sua conta de armazenamento. O agente dos serviços de recuperação, em seguida, copia o conteúdo dos dados desta conta para o Cofre de cópia de segurança ou os serviços de recuperação cofre, optando-se aplicável. Na próxima hora cópia de segurança agendada, o agente de cópia de segurança do Azure efetua a cópia de segurança incremental através de cópia de segurança inicial.

> [!NOTE]
> As secções seguintes aplicam-se aos utilizadores de versões anteriores do Backup do Azure, que não têm acesso para a ferramenta de preparação de disco do Azure.
>
>

### <a name="prepare-a-sata-drive"></a>Preparar uma unidade SATA
1. Transferir o [ferramenta de importação/exportação do Microsoft Azure](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) para o computador de cópia. Certifique-se de que a localização de transição está acessível ao computador em que pretende executar o seguinte conjunto de comandos. Se necessário, o computador de cópia pode ser o mesmo que o computador de origem.

2. Deszipe o ficheiro de WAImportExport.zip. Execute a ferramenta de WAImportExport que formata a unidade SATA, escreve os dados de cópia de segurança para a unidade SATA e encripta-lo. Antes de executar o seguinte comando, certifique-se de que o BitLocker estiver ativado no computador. <br/>

    `*.\WAImportExport.exe PrepImport /j:<*JournalFile*>.jrn /id: <*SessionId*> /sk:<*StorageAccountKey*> /BlobType:**PageBlob** /t:<*TargetDriveLetter*> /format /encrypt /srcdir:<*staging location*> /dstdir: <*DestinationBlobVirtualDirectory*>/*`

    > [!NOTE]
    > Se instalou a atualização de Agosto de 2016 da cópia de segurança do Azure (ou posterior), certifique-se de que a localização de transição que introduziu é idêntica no **cópia de segurança agora** ecrã e contém ficheiros AIB e BLOBs de Base.
    >
    >

| Parâmetro | Descrição |
| --- | --- |
| /j: <*JournalFile*> |O caminho para o ficheiro do diário de alterações. Cada unidade tem de ter exatamente um ficheiro de diário de alterações. O ficheiro do diário de alterações não deve ser na unidade de destino. A extensão de ficheiro do diário de alterações é .jrn e é criada como parte da execução deste comando. |
| formado: <*SessionId*> |O ID de sessão identifica uma sessão de cópia. É utilizado para garantir a recuperação exata de uma sessão de cópia interrompida. Os ficheiros que são copiados numa sessão de cópia são armazenados num diretório com o nome depois do ID de sessão na unidade de destino. |
| /SK: <*StorageAccountKey*> |A chave de conta para a conta de armazenamento ao qual os dados são importados. A chave tem de ser os mesmos que foi entrou durante a criação do grupo de proteção/política de cópia de segurança. |
| / BlobType |O tipo de blob. Este fluxo de trabalho for bem sucedida apenas se **PageBlob** está especificado. Isto não é a opção predefinida e deve ser mencionado neste comando. |
| /t: <*TargetDriveLetter*> |A letra de unidade da vírgula decimal da unidade de disco rígido de destino para a sessão atual da cópia. |
| /Format |A opção para formatar o disco. Especificar este parâmetro, quando a unidade tem de ser formatado; caso contrário, omita-lo. Antes da ferramenta formata a unidade, pede ao utilizador uma confirmação a partir da consola. Para suprimir a confirmação, especifique o parâmetro /silentmode. |
| / encriptar |A opção para encriptar a unidade. Especifique este parâmetro, quando a unidade ainda não foi encriptada com BitLocker e tem de ser encriptados pela ferramenta. Se a unidade já foi encriptada com BitLocker, omita este parâmetro, especifique o parâmetro /bk e fornecer a chave do BitLocker existente. Se especificar o parâmetro /format, também tem de especificar o / encriptar o parâmetro. |
| /srcdir: <*SourceDirectory*> |O diretório de origem que contém os ficheiros a ser copiado para a unidade de destino. Certifique-se de que o nome do diretório especificado tem um caminho completo em vez de relativo. |
| /dstdir: <*DestinationBlobVirtualDirectory*> |O caminho para o diretório virtual de destino na sua conta do storage do Azure. Lembre-se de que os nomes dos contentores válido de utilizar ao especificar os diretórios virtuais de destino ou os blobs. Tenha em atenção que os nomes dos contentores têm de estar em minúsculas.  Este nome de contentor deve ser o que introduziu durante a criação do grupo de proteção/política de cópia de segurança. |

> [!NOTE]
> É criado um ficheiro do diário de alterações na pasta WAImportExport que capture as informações do fluxo de trabalho completos. É necessário este ficheiro quando cria uma tarefa de importação no portal do Azure.
>
>

  ![Saída do PowerShell](./media/backup-azure-backup-import-export/psoutput.png)

### <a name="create-an-import-job-in-the-azure-portal"></a>Criar uma tarefa de importação no portal do Azure
1. Aceda à sua conta de armazenamento na [portal clássico do Azure](https://manage.windowsazure.com/), clique em **importar/exportar**e, em seguida, **criar tarefa de importação** no painel de tarefas.

    ![Separador no portal do Azure para importar/exportar](./media/backup-azure-backup-import-export/azureportal.png)

2. No passo 1 do assistente, indicam que preparou a unidade e de que possui o ficheiro de diário de alterações de unidade disponível.

3. No passo 2 do assistente, forneça as informações de contacto para a pessoa que é responsável por esta tarefa de importação.

4. No passo 3, carregar os ficheiros do diário de alterações de unidade que obteve na secção anterior.

5. Passo 4, introduza um nome descritivo para a tarefa de importação que introduziu durante a criação do grupo de proteção/política de cópia de segurança. O nome que introduziu pode conter apenas letras minúsculas, números, hífenes e carateres de sublinhado, tem de começar com uma letra e não pode conter espaços. O nome que escolheu é utilizado para controlar as tarefas enquanto estiverem em curso e depois de serem resolvidos.

6. Em seguida, selecione a região de centro de dados da lista. A região de centro de dados indica o Centro de dados e o endereço para o qual tem são enviados o pacote.

    ![Selecione a região do Centro de dados](./media/backup-azure-backup-import-export/dc.png)

7. No passo 5, selecione o retorno carrier da lista e introduza o seu número de conta operadora. A Microsoft utiliza esta conta para enviar a unidades de volta para a sua tarefa de importação esteja concluída.

8. Envie o disco e introduza o número de rastreio para controlar o estado do shipment. Depois do disco chega no Centro de dados, que é copiado para a conta de armazenamento e a atualização do Estado.

    ![Estado concluído](./media/backup-azure-backup-import-export/complete.png)

### <a name="complete-the-workflow"></a>Concluir o fluxo de trabalho
Depois dos dados de cópia de segurança iniciais estão disponíveis na sua conta de armazenamento, o agente dos serviços de recuperação do Microsoft Azure copia o conteúdo dos dados a partir desta conta para o Cofre de cópia de segurança ou do cofre dos serviços de recuperação, optando-se aplicável. Na próxima hora cópia de segurança agendada, o agente de cópia de segurança do Azure efetua a cópia de segurança incremental através de cópia de segurança inicial.

## <a name="next-steps"></a>Passos seguintes
* Para quaisquer perguntas sobre o fluxo de trabalho de importação/exportação do Azure, consulte [utilizar o serviço de importação/exportação do Microsoft Azure para transferir dados para o Blob storage](../storage/common/storage-import-export-service.md).
* Consulte a secção de cópia de segurança offline da cópia de segurança do Azure de [FAQ](backup-azure-backup-faq.md) para quaisquer perguntas sobre o fluxo de trabalho.
