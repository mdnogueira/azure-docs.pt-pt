---
title: Procurar e gerir recursos de armazenamento utilizando o Explorador de servidores | Microsoft Docs
description: "Navegação e gerir recursos de armazenamento utilizando o Explorador de servidores"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 658dc064-4a4e-414b-ae5a-a977a34c930d
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/24/2017
ms.author: kraigb
ms.openlocfilehash: 9f3d564fd844d982838cc6f29af24399801a62bc
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="browse-and-manage-storage-resources-by-using-server-explorer"></a>Procurar e gerir recursos de armazenamento utilizando o Explorador de servidores

[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Descrição geral

Se tiver instalado as ferramentas do Azure para o Microsoft Visual Studio, pode ver os dados da tabela, fila e blob das suas contas do storage do Azure. O Azure **armazenamento** nó no Explorador de servidores mostra os dados que esteja na sua conta do emulador de armazenamento local e as outras contas de armazenamento do Azure.

Para ver o Explorador de servidores no Visual Studio, na barra de menus, selecione **vista** > **Explorador de servidores**. O **armazenamento** nós mostra todas as contas de armazenamento que existem em cada subscrição do Azure ou o certificado que o se estiver ligado à. Se a sua conta de armazenamento não aparecer, pode adicioná-lo ao seguir as instruções [posteriormente neste artigo](#add-storage-accounts-by-using-server-explorer).

A partir do Azure SDK 2.7, também pode utilizar Cloud Explorer para ver e gerir os recursos do Azure. Para obter mais informações, consulte [recursos de gestão do Azure com o Explorador de nuvem](vs-azure-tools-resources-managing-with-cloud-explorer.md).

## <a name="view-and-manage-storage-resources-in-visual-studio"></a>Ver e gerir recursos de armazenamento no Visual Studio

Explorador de servidores automaticamente mostra uma lista de tabelas, filas e blobs na sua conta do emulador de armazenamento. A conta de emulador de armazenamento está listada no Explorador de servidores no **armazenamento** nó como o **desenvolvimento** nós.

Para ver os recursos da conta de emulador de armazenamento, expanda o **desenvolvimento** nós. Se o emulador do storage ainda não foi iniciado quando expande o **desenvolvimento** nós, é iniciada automaticamente. Este processo pode demorar vários segundos. Pode continuar a trabalhar nas outras áreas do Visual Studio enquanto inicia o emulador de armazenamento.

Para ver os recursos numa conta do storage, expanda o nó da conta de armazenamento no Explorador de servidores em que verá **Blobs**, **filas**, e **tabelas** nós.

## <a name="work-with-blob-resources"></a>Trabalhar com recursos do blob

O **Blobs** nó apresenta uma lista de contentores para a conta de armazenamento selecionada. Os contentores de blob contém ficheiros de blob e, pode organizar estas blobs em pastas e subpastas. Para obter mais informações, consulte [como utilizar o Blob storage a partir do .NET](storage/blobs/storage-dotnet-how-to-use-blobs.md).

### <a name="to-create-a-blob-container"></a>Para criar um contentor de blob

1. Abra o menu de atalho para o **Blobs** nó e, em seguida, selecione **criar contentor de Blob**.
1. No **criar contentor de Blob** caixa de diálogo, introduza o nome do novo contentor.  
1. Selecione Enter no teclado, ou pode clique ou toque em fora o campo de nome para guardar o contentor de blob.

   > [!NOTE]
   > O nome do contentor de blob tem de começar com uma letra minúscula (a-z) ou um número (0-9).

### <a name="to-delete-a-blob-container"></a>Para eliminar um contentor do blob

Abra o menu de atalho para o contentor de blob que pretende remover e, em seguida, selecione **eliminar**.

### <a name="to-display-a-list-of-the-items-in-a-blob-container"></a>Para apresentar uma lista dos itens num contentor de blob

Abra o menu de atalho para um nome de contentor do blob na lista e, em seguida, selecione **abra**.

Ao visualizar o conteúdo de um contentor de blob, aparece um separador conhecido como a vista de contentor do blob.

![Vista de contentor do blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)

Pode efetuar as seguintes operações nos blobs utilizando os botões no canto superior direito da vista de contentor do blob:

* Introduza um valor de filtro e aplicá-la.
* Atualize a lista de blobs no contentor.
* Carregar um ficheiro.
* Elimine um blob. (Eliminar um ficheiro a partir de um contentor de blob não elimina o ficheiro subjacente. Apenas remove-lo do contentor do blob.)
* Abra um blob.
* Guarde um blob para o computador local.

### <a name="to-create-a-folder-or-subfolder-in-a-blob-container"></a>Para criar uma pasta ou subpasta num contentor de blob

1. Escolha o contentor do blob no Cloud Explorer. Na janela do contentor, selecione o **carregar Blob** botão.

1. No **carregar o novo ficheiro** caixa de diálogo, selecione o **procurar** botão para especificar o ficheiro que pretende carregar e, em seguida, introduza um nome de pasta o **pasta (opcional)** caixa.

   ![Carregar um ficheiro para uma pasta de blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)

   Pode adicionar subpastas nas pastas de contentor, seguindo o mesmo passo. Se não especificar um nome de pasta, o ficheiro é carregado para o nível de principal de contentor do blob. O ficheiro é apresentada na pasta especificada no contentor.

   ![Pasta adicionada a um contentor de blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)

1. Faça duplo clique na pasta ou selecione Enter para ver o conteúdo da pasta. Quando estiver na pasta do contentor, pode voltar à raiz do contentor, selecionando o **abrir o diretório principal** botão (seta).

### <a name="to-delete-a-container-folder"></a>Para eliminar uma pasta de contentor

Elimine todos os ficheiros na pasta.

Uma vez pastas em contentores de BLOBs são pastas virtuais, não é possível criar uma pasta vazia. Também não é possível eliminar uma pasta para eliminar os conteúdos do ficheiro, mas em vez disso, tem de eliminar todo o conteúdo de uma pasta para eliminar a pasta de si próprio.

### <a name="to-filter-blobs-in-a-container"></a>Para filtrar os blobs num contentor

Pode filtrar os blobs que são apresentados, especificando um prefixo comuns.

Por exemplo, se introduzir o prefixo **Olá** na caixa de texto de filtro e, em seguida, selecione o **executar** (**!**) aparecem do botão, apenas os blobs que começam por "Olá".

![Caixa de texto de filtro](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)

A caixa de texto de filtro é sensível e não suporta a filtragem com carateres universais. É possível filtrar os BLOBs apenas por prefixo. O prefixo pode incluir um delimitador, se estiver a utilizar um delimitador para organizar os blobs numa hierarquia virtual. Filtragem por exemplo, o prefixo "HelloFabric /" devolve todos os blobs que começam com essa cadeia.

### <a name="to-download-blob-data"></a>Para transferir dados de BLOBs

No Explorador de nuvem, utilize qualquer um dos seguintes métodos:

* Abra o menu de atalho para um ou mais blobs e, em seguida, selecione **abra**.
* Escolha o nome de blob e, em seguida, selecione o **abra** botão.
* Faça duplo clique o nome do blob.

O progresso da transferência de um blob é apresentado no **registo de atividade do Azure** janela.

O blob é aberto no editor de predefinido para esse tipo de ficheiro. Se o sistema operativo reconhece o tipo de ficheiro, o ficheiro abre-se numa aplicação instalada localmente. Caso contrário, é-lhe pedido que escolha uma aplicação que é adequada para o tipo de ficheiro do blob. O ficheiro local que é criado quando transferir um blob está marcado como só de leitura.

Dados de BLOBs é colocado em cache localmente e verificados em relação a hora de última modificação do blob no armazenamento de Blobs do Azure. Se tiver sido atualizado o blob, uma vez que esta foi transferida pela última vez, esta é transferida novamente. Caso contrário, o blob é carregado do disco local.

Por predefinição, um blob é transferido para um diretório temporário. Para transferir blobs para um diretório específico, abra o menu de atalho para dos nomes blob selecionado e selecione **guardar como**. Quando guardar um blob desta forma, não é possível abrir o ficheiro de blob e o ficheiro local é criado com atributos de leitura/escrita.

### <a name="to-upload-blobs"></a>Para carregar os blobs

Para carregar os blobs, selecione o **carregar Blob** botão quando o contentor está aberto para visualização na vista de contentor de blob.

Pode escolher um ou mais ficheiros para carregar e pode carregar ficheiros de qualquer tipo. O **registo de atividade do Azure** janela mostra o progresso do carregamento. Para obter mais informações sobre como trabalhar com dados de BLOBs, consulte [como utilizar o Blob storage do Azure no .NET](http://go.microsoft.com/fwlink/p/?LinkId=267911).

### <a name="to-view-logs-transferred-to-blobs"></a>Para ver registos transferidos para os blobs

Se estiver a utilizar o diagnóstico do Azure para registar dados a partir da sua aplicação do Azure e transferiu os registos para a sua conta do storage, verá os contentores que Azure criado para estes registos. Ver estes registos no Explorador de servidores é uma forma fácil de identificar problemas com a sua aplicação, especialmente se for foi implementado no Azure.

Para obter mais informações sobre diagnósticos do Azure, consulte [recolher dados de registo por utilizando o Azure Diagnostics](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="to-get-the-url-for-a-blob"></a>Para obter o URL para um blob

Abra o menu de atalho do blob e, em seguida, selecione **cópia URL**.

### <a name="to-edit-a-blob"></a>Para editar um blob

Selecione o blob e, em seguida, selecione o **Blob abra** botão.

O ficheiro é transferido para uma localização temporária e aberto no computador local. Carregar o blob novamente depois de efetuar alterações.

## <a name="work-with-queue-resources"></a>Trabalhar com recursos da fila

As filas de serviços de armazenamento estão alojadas numa conta de armazenamento do Azure. Pode utilizá-los para permitir que a sua nuvem funções de serviço comunicar entre si e com outros serviços, um mecanismo de transmissão de mensagens. Pode aceder programaticamente a fila através de um serviço em nuvem e através de um serviço web para clientes externos. Também pode aceder a fila diretamente, utilizando o Explorador de servidores no Visual Studio.

Quando desenvolver um serviço em nuvem que utiliza as filas, pode querer utilizar o Visual Studio para criar filas e trabalhar com os mesmos interativamente enquanto desenvolver e testar o seu código.

No Explorador de servidores, pode ver as filas numa conta do storage, criar e eliminar filas, abrir uma fila para ver o respetivas mensagens e adicionar mensagens a uma fila. Quando abrir uma fila de visualização, pode ver as mensagens individuais e pode efetuar as seguintes ações na fila utilizando os botões no canto superior esquerdo:

* Atualize a vista da fila.
* Adicione uma mensagem à fila.
* Anular a mensagem mais superior.
* Limpe a fila de toda.

A imagem seguinte mostra uma fila que contém duas mensagens:

![Visualizar uma fila](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

Para obter mais informações sobre o armazenamento do filas, consulte [introdução ao armazenamento de filas do Azure através do .NET](http://go.microsoft.com/fwlink/?LinkID=264702). Para obter informações sobre o serviço web para as filas de serviços de armazenamento, consulte o artigo [conceitos do serviço fila](http://go.microsoft.com/fwlink/?LinkId=264788). Para obter informações sobre como enviar mensagens para uma fila de serviços de armazenamento utilizando o Visual Studio, consulte [enviar mensagens para uma fila de serviços de armazenamento](https://msdn.microsoft.com/library/azure/jj649344.aspx).

> [!NOTE]
> As filas de serviços de armazenamento são distintas de filas do Service Bus do Azure. Para obter mais informações sobre as filas do Service Bus, consulte [filas do Service Bus, tópicos e subscrições](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-queues-topics-subscriptions).

## <a name="work-with-table-resources"></a>Trabalhar com recursos de tabela

O armazenamento de Tabelas do Azure armazena grandes quantidades de dados estruturados. O serviço é um arquivo de dados NoSQL que aceita chamadas autenticadas de dentro e fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.

### <a name="to-create-a-table"></a>Para criar uma tabela

1. No Explorador de nuvem, selecione o **tabelas** nó da conta de armazenamento e, em seguida, selecione **Create Table**.
1. No **Create Table** caixa de diálogo, introduza um nome para a tabela.

### <a name="to-view-table-data"></a>Para ver os dados da tabela

1. No Cloud Explorer, abra o **Azure** nó e, em seguida, abra o **armazenamento** nós.
1. Abra o nó de conta de armazenamento que estão interessadas nas e, em seguida, abra o **tabelas** nó para ver uma lista de tabelas para a conta de armazenamento.
1. Abra o menu de atalho para uma tabela e, em seguida, selecione **tabela da vista**.

    ![Uma tabela do Azure no Explorador de soluções](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

A tabela está organizada por entidades (apresentadas nas linhas) e propriedades (apresentadas nas colunas). Por exemplo, a ilustração seguinte mostra entidades listadas no estruturador de tabela.

### <a name="to-edit-table-data"></a>Para editar os dados da tabela

No estruturador de tabela, abra o menu de atalho para uma entidade (uma única linha) ou uma propriedade (única célula) e, em seguida, selecione **editar**.

    ![Add or edit a table entity](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)

As entidades de uma única tabela não são necessárias para ter o mesmo conjunto de propriedades (colunas). Tenha em atenção as seguintes restrições em visualizar e editar dados de tabela:

* Não é possível ver ou editar dados binários (`type byte[]`), mas pode armazená-lo numa tabela.
* Não é possível editar o **PartitionKey** ou **RowKey** valores, porque o Table storage no Azure não suporta a essa operação.
* Não é possível criar uma propriedade denominada **Timestamp**. Os serviços de armazenamento do Azure utilizar uma propriedade com esse nome.
* Se introduzir um **DateTime** valor, tem de seguir um formato que é adequado para as definições de idioma e região do seu computador (por exemplo, MM/DD/AAAA HH: mm: [AM | PM] para inglês dos Estados Unidos).

### <a name="to-add-entities"></a>Para adicionar entidades

1. No estruturador de tabela, selecione o **adicionar entidade** botão.

    ![Adicionar o botão de entidade](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)

1. No **adicionar entidade** caixa de diálogo, introduza os valores a **PartitionKey** e **RowKey** propriedades.

    ![Adicionar caixa de diálogo de entidade](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)

    Introduza os valores cuidadosamente. Não é possível alterá-los depois de fechar a caixa de diálogo, exceto se eliminar a entidade e adicioná-la novamente.

### <a name="to-filter-entities"></a>Para filtrar entidades

Pode personalizar o conjunto de entidades que são apresentados numa tabela, se utilizar o construtor de consultas.

1. Para abrir o construtor de consultas, abra uma tabela para visualização.
1. Selecione o **construtor de consultas** botão na barra de ferramentas da tabela da vista.

    O **construtor de consultas** é apresentada a caixa de diálogo. A ilustração seguinte mostra uma consulta que está a ser criada no construtor de consultas.

    ![Construtor de consultas](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)
1. Quando estiver pronto a construir a consulta, feche a caixa de diálogo. O formato de texto resultante da consulta é apresentado numa caixa de texto como um filtro de WCF Data Services.
1. Para executar a consulta, selecione o ícone de triângulo verde.

Também pode filtrar os dados de entidade que aparece no estruturador de tabela se introduzir uma cadeia de filtro do WCF Data Services diretamente na caixa de texto de filtro. Este tipo de cadeia é semelhante a uma cláusula onde SQL, mas é enviado para o servidor como um pedido de HTTP. Para obter informações sobre como construir as cadeias de filtro, consulte [construir cadeias de filtro para o estruturador de tabela](https://msdn.microsoft.com/library/azure/ff683669.aspx).

A ilustração seguinte mostra um exemplo de uma cadeia de filtro válida:

![Cadeia de filtro](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

## <a name="refresh-storage-data"></a>Atualizar os dados de armazenamento

Ao Explorador de servidores liga ao ou obtém dados a partir de uma conta de armazenamento, a operação poderá demorar até um minuto para concluir. Se não conseguir ligar o Explorador de servidores, a operação poderá tempo limite. Enquanto os dados são obtidos, pode continuar a trabalhar na outras partes do Visual Studio. Para cancelar a operação, se estiver a demorar demasiado tempo, selecione o **parar atualizar** botão na barra de ferramentas do Explorador de servidores.

### <a name="to-refresh-blob-container-data"></a>Para atualizar os dados de contentor do blob

* Selecione o **Blobs** nó abaixo **armazenamento**e, em seguida, selecione o **atualizar** botão na barra de ferramentas do Explorador de servidores.
* Para atualizar a lista de blobs que é apresentada, selecione o **executar** botão.

### <a name="to-refresh-table-data"></a>Para atualizar os dados de tabela

* Selecione o **tabelas** nó abaixo **armazenamento**e, em seguida, selecione o **atualizar** botão na barra de ferramentas do Explorador de servidores.
* Para atualizar a lista de entidades que é apresentada no estruturador de tabela, selecione o **executar** botão no estruturador de tabela.

### <a name="to-refresh-queue-data"></a>Para atualizar os dados de fila

Selecione o **filas** nó abaixo **armazenamento**e, em seguida, selecione o **atualizar** botão na barra de ferramentas do Explorador de servidores.

### <a name="to-refresh-all-items-in-a-storage-account"></a>Para atualizar todos os itens de uma conta de armazenamento

Escolha o nome da conta e, em seguida, selecione o **atualizar** botão na barra de ferramentas do Explorador de servidores.

## <a name="add-storage-accounts-by-using-server-explorer"></a>Adicionar contas de armazenamento utilizando o Explorador de servidores

Existem duas formas de adicionar contas de armazenamento utilizando o Explorador de servidores. Pode criar uma conta de armazenamento na sua subscrição do Azure ou, pode anexar uma conta de armazenamento existente.

### <a name="to-create-a-storage-account-by-using-server-explorer"></a>Para criar uma conta de armazenamento utilizando o Explorador de servidores

1. No Explorador de servidores, abra o menu de atalho para o **armazenamento** nó e, em seguida, selecione **criar conta de armazenamento**.

1. No **criar conta de armazenamento** diálogo caixa, selecione ou introduza as seguintes informações:

   * A subscrição do Azure para o qual pretende adicionar a conta de armazenamento.
   * O nome que pretende utilizar para a nova conta de armazenamento.
   * A região ou grupo de afinidade, (por exemplo, EUA Oeste ou Ásia Oriental).
   * O tipo de replicação que pretende utilizar para a conta de armazenamento, tais como localmente redundante.

   ![Criar uma conta do Storage do Azure](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)

1. Selecione **Criar**.

A nova conta de armazenamento é apresentado no **armazenamento** lista no Explorador de soluções.

### <a name="to-attach-an-existing-storage-account-by-using-server-explorer"></a>Para anexar uma conta de armazenamento existente, utilizando o Explorador de servidores

1. No Explorador de servidores, abra o menu de atalho para o Azure **armazenamento** nó e, em seguida, selecione **anexar armazenamento externo**.

    ![Adicionar uma conta de armazenamento existente](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)
1. No **criar conta de armazenamento** diálogo caixa, selecione ou introduza as seguintes informações:

   * O nome da conta de armazenamento existente que pretende ligar.
   * A chave para a conta de armazenamento selecionada. Este valor é, geralmente, fornecido por si ao selecionar uma conta de armazenamento. Se pretender que o Visual Studio lembrar-se a chave de conta de armazenamento, selecione o **Lembre-se a chave da conta** caixa de verificação.
   * O protocolo a utilizar para ligar à conta de armazenamento, como HTTP, HTTPS ou um ponto de final personalizado. Para mais informações sobre os pontos finais personalizados, consulte [como configurar as cadeias de ligação](https://msdn.microsoft.com/library/azure/ee758697.aspx).

### <a name="to-view-the-secondary-endpoints"></a>Para ver os pontos finais secundários

Se tiver criado uma conta de armazenamento utilizando o **acesso de leitura Georreplicação redundante** opção de replicação, pode ver os respetivos pontos finais secundários ao abrir o menu de atalho para o nome da conta e, em seguida, selecione **propriedades**.

![Pontos finais secundários de armazenamento](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### <a name="to-remove-a-storage-account-from-server-explorer"></a>Para remover uma conta de armazenamento no Explorador de servidores

No Explorador de servidores, abra o menu de atalho para o nome da conta e, em seguida, selecione **eliminar**. 

Se eliminar uma conta de armazenamento, as informações de chaves guardadas para essa conta também são removidas.

Se eliminar uma conta do storage no Explorador de servidores, não afeta a conta de armazenamento ou de todos os dados que contém. Remove simplesmente a referência no Explorador de servidores. Para eliminar permanentemente uma conta de armazenamento, utilize o [portal do Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como utilizar os serviços de armazenamento do Azure, consulte [aceder aos serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/ee405490.aspx).
