<properties
    pageTitle="Como criar, gerir ou eliminar uma conta do Storage no Portal Clássico do Azure | Microsoft Azure"
    description="Crie uma nova conta do Storage, efetue a gestão das chaves de acesso da conta ou elimine uma conta do Storage no Portal do Azure. Saiba mais sobre contas do Storage standard e premium."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/26/2016"
    ms.author="robinsh"/>


# Acerca das contas do Storage do Azure

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools](../../includes/storage-try-azure-tools.md)]

## Descrição geral

Uma conta do Storage do Azure dá-lhe acesso aos serviços Blob, Fila, Tabela e Ficheiro do Azure no Storage do Azure. A conta do Storage fornece o espaço de nomes exclusivo para os seus objetos de dados do Storage do Azure. Por predefinição, os dados na sua conta só estão disponíveis para si, o proprietário da conta.

Existem dois tipos de contas do Storage:

- Uma conta do Storage standard inclui o armazenamento de Blob, Tabela, Fila e Ficheiro.
- Uma conta de Premium Storage suporta atualmente apenas discos de máquinas virtuais do Azure. Consulte [Premium Storage: Armazenamento de Elevado Desempenho para Cargas de Trabalho de Virtual Machines do Azure](storage-premium-storage.md) para obter uma descrição geral aprofundada do Premium Storage.

## Faturação da conta do Storage

A utilização do Storage do Azure é-lhe cobrada com base na sua conta do Storage. Os custos de armazenamento dependem de quatro fatores: capacidade de armazenamento, esquema de replicação, transações de armazenamento e saída de dados.

- A capacidade de armazenamento refere-se à quantidade da alocação da sua conta do Storage que está a utilizar para armazenar dados. O custo de armazenar simplesmente os dados é determinado pela quantidade de dados armazenados e pela forma como estes são replicados.
- A replicação determina o número de cópias dos dados que são mantidas em simultâneo e em que localizações.
- As transações referem-se a todas as operações de leitura e escrita ao Storage do Azure.
- A saída de dados refere-se aos dados transferidos para fora de uma região do Azure. Quando os dados na sua conta do Storage são acedidos por uma aplicação que não está em execução na mesma região, quer essa aplicação seja um serviço em nuvem ou outro tipo de aplicação, é-lhe cobrada uma taxa pela saída dos dados. (Para os serviços do Azure, pode tomar medidas para agrupar os seus dados e serviços nos mesmo centros de dados, de modo a reduzir ou eliminar os encargos associados à saída de dados.)  

A página [Preços do Storage do Azure](https://azure.microsoft.com/pricing/details/storage) fornece informações de preços detalhadas para a capacidade de armazenamento, a replicação e as transações. A página [Detalhes de Preços das Transferências de Dados](https://azure.microsoft.com/pricing/details/data-transfers/) fornece informações de preços detalhadas para a saída de dados.

Para obter informações sobre a capacidade das contas do Storage e as metas de desempenho, consulte [Metas de Desempenho e Escalabilidade do Storage do Azure](storage-scalability-targets.md).

> [AZURE.NOTE] Quando cria uma máquina virtual do Azure, é criada automaticamente uma conta do Storage na localização de implementação, se ainda não tiver uma conta do Storage nessa localização. Sendo assim, não é necessário seguir os passos abaixo para criar uma conta do Storage para os discos de máquinas virtuais. O nome da conta do Storage será baseado no nome da máquina virtual. Consulte a [documentação de Virtual Machines do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) para obter mais detalhes.

## Criar uma conta do Storage

1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).

2. Clique em **Novo** na barra de tarefas na parte inferior da página. Escolha **Serviços de Dados** | **Storage** e, em seguida, clique em **Criação Rápida**.

    ![NewStorageAccount](./media/storage-create-storage-account-classic-portal/storage_NewStorageAccount.png)

3. Em **URL**, introduza um nome para a sua conta do Storage.

    > [AZURE.NOTE] Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas.
    >  
    > O nome da sua conta do Storage tem de ser exclusivo no Azure. O Portal Clássico do Azure indica se o nome da conta do Storage que selecionou já está a ser utilizado.

    Consulte [Pontos finais da conta do Storage](#storage-account-endpoints) abaixo para obter detalhes sobre a forma como o nome da conta do Storage será utilizado para endereçar os seus objetos no Storage do Azure.

4. Em **Localização/Grupo de Afinidades**, selecione uma localização para a sua conta do Storage que esteja próxima de si ou dos seus clientes. Se os dados na sua conta do Storage forem acedidos a partir de outro serviço do Azure, como uma máquina virtual do Azure ou um serviço em nuvem, poderá pretender selecionar um grupo de afinidades na lista, de modo a agrupar a sua conta do Storage no mesmo datacenter com outros serviços do Azure que está a utilizar para melhorar o desempenho e reduzir os custos.

    Tenha em atenção que tem de selecionar um grupo de afinidades quando é criada a sua conta do Storage. Não é possível mover uma conta existente para um grupo de afinidades. Para obter mais informações sobre os grupos de afinidades, consulte [Colocalização de serviços com um grupo de afinidades](#service-co-location-with-an-affinity-group) abaixo.

    >[AZURE.IMPORTANT] Para determinar que localizações estão disponíveis para a sua subscrição, pode chamar a operação [List all resource providers](https://msdn.microsoft.com/library/azure/dn790524.aspx). Para listar fornecedores a partir do PowerShell, chame [Get-AzureLocation](https://msdn.microsoft.com/library/azure/dn757693.aspx). A partir do .NET, utilize o método [List](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.provideroperationsextensions.list.aspx) da classe ProviderOperationsExtensions.
    >
    >Além disso, consulte [Regiões do Azure](https://azure.microsoft.com/regions/#services) para obter mais informações sobre os serviços que estão disponíveis em cada região.


5. Se tiver mais do que uma subscrição do Azure, é apresentado o campo **Subscrição**. Em **Subscrição**, introduza a subscrição do Azure com a qual pretende utilizar a conta do Storage.

6. Em **Replicação**, selecione o nível de replicação pretendido para a sua conta do Storage. A opção de replicação recomendada é a replicação georredundante, que fornece a máxima durabilidade para os seus dados. Consulte [Replicação do Storage do Azure](storage-redundancy.md) para obter detalhes adicionais sobre as opções de replicação do Storage do Azure.

6. Clique em **Criar Conta do o Storage**.

    A criação da sua conta do Storage poderá demorar alguns minutos. Para verificar o estado, pode monitorizar as notificações na parte inferior do Portal Clássico do Azure. Depois de ter sido criada a conta do Storage, a sua nova conta do Storage apresenta o estado **Online** e está pronta para ser utilizada.

![StoragePage](./media/storage-create-storage-account-classic-portal/Storage_StoragePage.png)


### Pontos finais da conta do Storage

Todos os objetos que armazena no Storage do Azure têm um endereço de URL exclusivo. O nome da conta do Storage compõe o subdomínio desse endereço. A combinação de nome de domínio e subdomínio, que é específica para cada serviço, forma um *ponto final* para a sua conta do Storage.

Por exemplo, se a sua conta do Storage tiver o nome *mystorageaccount*, os pontos finais predefinidos para a mesma são:

- Serviço Blob: http://*mystorageaccount*.blob.core.windows.net

- Serviço Tabela: http://*mystorageaccount*.table.core.windows.net

- Serviço Fila: http://*mystorageaccount*.queue.core.windows.net

- Serviço Ficheiro: http://*mystorageaccount*.file.core.windows.net

Pode ver os pontos finais para a sua conta do Storage no dashboard de armazenamento no [Portal Clássico do Azure](https://manage.windowsazure.com) quando a conta tiver sido criada.

O URL para aceder a um objeto numa conta do Storage é criado ao acrescentar a localização do objeto na conta do Storage ao ponto final. Por exemplo, um endereço de blob pode ter este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Também pode configurar um nome de domínio personalizado para utilizar com a sua conta do Storage. Consulte [Configurar um nome de domínio personalizado para o ponto final do Blob Storage](storage-custom-domain-name.md) para obter detalhes.

### Colocalização de serviços com um grupo de afinidades

Um *grupo de afinidades* consiste num agrupamento geográfico dos seus serviços e VMs do Azure com a sua conta do Storage do Azure. Um grupo de afinidades pode melhorar o desempenho do serviço ao localizar cargas de trabalho do computador no mesmo datacenter ou perto do público-alvo de utilizadores. Além disso, não são cobradas taxas de saída quando os dados numa conta do Storage são acedidos a partir de outro serviço que faz parte do mesmo grupo de afinidades.

> [AZURE.NOTE]  Para criar um grupo de afinidades, abra a área <b>Definições</b> do [Portal Clássico do Azure](https://manage.windowsazure.com), clique em <b>Grupos de Afinidades</b> e, em seguida, clique em <b>Adicionar um grupo de afinidades</b> ou no botão <b>Adicionar</b>. Também pode criar e gerir grupos de afinidades ao utilizar a API de Gestão de Serviços do Azure. Consulte <a href="http://msdn.microsoft.com/library/azure/ee460798.aspx">Operações em grupos de afinidade</a> para obter mais informações.

## Ver, copiar e voltar a gerar chaves de acesso ao armazenamento

Quando cria uma conta do Storage, o Azure gera duas chaves de acesso ao armazenamento de 512 bits, que são utilizadas para autenticação quando a conta do Storage é acedida. Ao fornecer duas chaves de acesso ao armazenamento, o Azure permite que volte a gerar as chaves sem quaisquer interrupções ao seu serviço de armazenamento ou ao acesso a esse serviço.

> [AZURE.NOTE] Recomendamos que evite partilhar as chaves de acesso ao armazenamento com outras pessoas. Para permitir o acesso aos recursos de armazenamento sem fornecer as chaves de acesso, pode utilizar uma *assinatura de acesso partilhado*. Uma assinatura de acesso partilhado fornece acesso a um recurso na sua conta durante um intervalo definido por si e com as permissões que especificar. Consulte [Assinaturas de Acesso Partilhado: Compreender o Modelo SAS](storage-dotnet-shared-access-signature-part-1.md) para mais informações.

No [Portal Clássico do Azure](https://manage.windowsazure.com), utilize **Gerir Chaves** no dashboard ou a página **Armazenamento** para ver, copiar e voltar a gerar as chaves de acesso ao armazenamento utilizadas para aceder aos serviços Blob, Tabela e Fila.

### Copiar uma chave de acesso ao armazenamento  

Pode utilizar **Gerir Chaves** para copiar uma chave de acesso ao armazenamento para utilizar numa cadeia de ligação. A cadeia de ligação requer o nome da conta do Storage e uma chave para utilizar na autenticação. Para obter informações sobre como configurar as cadeias de ligação para aceder aos serviços do Storage do Azure, consulte [Configurar Cadeias de Ligação do Storage do Azure](storage-configure-connection-string.md).

1. No [Portal Clássico do Azure](https://manage.windowsazure.com), clique em **Armazenamento** e, em seguida, clique no nome da conta do Storage para abrir o dashboard.

2. Clique em **Gerir Chaves**.

    **Gerir Chaves de Acesso** é aberto.

    ![Managekeys](./media/storage-create-storage-account-classic-portal/Storage_ManageKeys.png)


3. Para copiar uma chave de acesso ao armazenamento, selecione o texto da chave. Em seguida, clique com o botão direito do rato e clique em **Copiar**.

### Voltar a gerar chaves de acesso ao armazenamento
Recomendamos que altere periodicamente as chaves de acesso da sua conta do Storage para ajudar a manter as ligações de armazenamento seguras. São atribuídas duas chaves de acesso, para que possa manter as ligações à conta do Storage com uma chave de acesso enquanto volta a gerar a outra.

> [AZURE.WARNING] A regeneração das chaves de acesso pode afetar os serviços no Azure, bem como as suas próprias aplicações que dependem da conta do Storage. Todos os clientes que utilizam a chave de acesso para aceder à conta do Storage têm de ser atualizados para utilizar a nova chave.

**Media Services** – se tiver Media Services dependentes da sua conta do Storage, deve sincronizar novamente as chaves de acesso com o serviço de multimédia depois de voltar a gerar as chaves.

**Aplicações** – se tiver Web Apps ou Cloud Services que utilizam a conta do Storage, perderá as ligações se voltar a gerar chaves, a menos que reverta as chaves. 

**Exploradores de Armazenamento** – se estiver a utilizar quaisquer [aplicações de explorador de armazenamento](storage-explorers.md), provavelmente terá de atualizar a chave de armazenamento utilizada por essas aplicações.

Eis o processo para rodar as chaves de acesso ao armazenamento:

1. Atualize as cadeias de ligação no código da aplicação para fazer referência à chave de acesso secundária da conta do Storage.

2. Volte a gerar a chave de acesso primária para a sua conta do Storage. No [Portal Clássico do Azure](https://manage.windowsazure.com), no dashboard ou na página **Configurar**, clique em **Gerir Chaves**. Clique em **Voltar a gerar** sob a chave de acesso primária e, em seguida, clique em **Sim** para confirmar que pretende gerar uma nova chave.

3. Atualize as cadeias de ligação no código para fazer referência à nova chave de acesso primária.

4. Volte a gerar a chave de acesso secundária.

## Eliminar uma conta do Storage

Para remover uma conta do Storage que já não está a utilizar, utilize **Eliminar** no dashboard ou na página **Configurar**. A opção **Eliminar** elimina toda a conta do Storage, incluindo todos os blobs, tabelas e filas na mesma.

> [AZURE.WARNING] Não é possível restaurar uma conta do Storage eliminada ou obter os conteúdos que esta continha antes da eliminação. Certifique-se de que faz uma cópia de segurança de tudo o que pretende guardar antes de eliminar a conta. Isto também se aplica a quaisquer recursos na conta – depois de eliminar um blob, tabela, fila ou ficheiro, este é eliminado permanentemente.
>
> Se a sua conta do Storage contiver ficheiros VHD para uma máquina virtual do Azure, tem de eliminar as imagens e os discos que utilizam esses ficheiros VHD antes de poder eliminar a conta do Storage. Em primeiro lugar, pare a máquina virtual se estiver em execução e, em seguida, elimine-a. Para eliminar discos, navegue até ao separador **Discos** e elimine quaisquer discos existentes. Para eliminar imagens, navegue até ao separador **Imagens** e elimine quaisquer imagens armazenadas na conta.

1. No [Portal Clássico do Azure](https://manage.windowsazure.com), clique em **Armazenamento**.

2. Clique em qualquer parte da entrada da conta do Storage, exceto no nome, e, em seguida, clique em **Eliminar**.

     -Ou-

    Clique no nome da conta do Storage para abrir o dashboard e, em seguida, clique em **Eliminar**.

3. Clique em **Sim** para confirmar que pretende eliminar a conta do Storage.

## Passos seguintes

- Para saber mais sobre o Storage do Azure, consulte a [documentação do Storage do Azure](https://azure.microsoft.com/documentation/services/storage/).
- Aceda ao [Blogue da Equipa do Storage do Azure](http://blogs.msdn.com/b/windowsazurestorage/).
- [Transferir dados com o Utilitário de Linha de Comandos AzCopy](storage-use-azcopy.md)



<!--HONumber=Aug16_HO1-->


