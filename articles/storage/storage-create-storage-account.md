<properties
    pageTitle="Como criar, gerir ou eliminar uma conta do Storage no Portal do Azure | Microsoft Azure"
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
    ms.author="micurd;robinsh"/>



# Acerca das contas do Storage do Azure

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools](../../includes/storage-try-azure-tools.md)]

## Descrição geral

Uma conta do Storage do Azure fornece um espaço de nomes exclusivo para armazenar e aceder aos seus objetos de dados do Storage do Azure. Todos os objetos numa conta do Storage são faturados em conjunto como um grupo. Por predefinição, os dados na sua conta só estão disponíveis para si, o proprietário da conta.

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

## Faturação da conta do Storage

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

> [AZURE.NOTE] Quando cria uma máquina virtual do Azure, é criada automaticamente uma conta do Storage na localização de implementação, se ainda não tiver uma conta do Storage nessa localização. Sendo assim, não é necessário seguir os passos abaixo para criar uma conta do Storage para os discos de máquinas virtuais. O nome da conta do Storage será baseado no nome da máquina virtual. Consulte a [documentação de Virtual Machines do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) para obter mais detalhes.

## Pontos finais da conta do Storage

Todos os objetos que armazena no Storage do Azure têm um endereço de URL exclusivo. O nome da conta do Storage compõe o subdomínio desse endereço. A combinação de nome de domínio e subdomínio, que é específica para cada serviço, forma um *ponto final* para a sua conta do Storage.

Por exemplo, se a sua conta do Storage tiver o nome *mystorageaccount*, os pontos finais predefinidos para a mesma são:

- Serviço Blob: http://*mystorageaccount*.blob.core.windows.net

- Serviço Tabela: http://*mystorageaccount*.table.core.windows.net

- Serviço Fila: http://*mystorageaccount*.queue.core.windows.net

- Serviço Ficheiro: http://*mystorageaccount*.file.core.windows.net

> [AZURE.NOTE] Uma conta do Blob Storage expõe apenas o ponto final de serviço blob.

O URL para aceder a um objeto numa conta do Storage é criado ao acrescentar a localização do objeto na conta do Storage ao ponto final. Por exemplo, um endereço de blob pode ter este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Também pode configurar um nome de domínio personalizado para utilizar com a sua conta do Storage. Para contas de armazenamento clássicas, veja [Configure a custom domain Name for your Blob Storage Endpoint (Configurar um Nome de domínio personalizado para o Ponto final do Armazenamento de Blobs)](storage-custom-domain-name.md) para obter detalhes. Para contas do Resource Manager, esta capacidade ainda não foi adicionada ao [Portal do Azure](https://portal.azure.com), mas pode configurá-la com o PowerShell. Para obter mais informações, consulte o cmdlet [Set-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607146.aspx).  

## Criar uma conta de armazenamento

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. No menu Hub, selecione **Novo** -> **Dados + Armazenamento** -> **Conta do Storage**.

3. Introduza um nome para a conta do Storage. Consulte [Pontos finais da conta do Storage](#storage-account-endpoints) para obter detalhes sobre a forma como o nome da conta do Storage será utilizado para endereçar os seus objetos no Storage do Azure.

    > [AZURE.NOTE] Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas.
    >  
    > O nome da sua conta do Storage tem de ser exclusivo no Azure. O portal do Azure indica se o nome da conta de armazenamento que selecionou já está a ser utilizado.

4. Especifique o modelo de implementação a utilizar: **Resource Manager** ou **Clássico**. O modelo de implementação recomendado é **Resource Manager**. Para obter mais informações, consulte [Compreender a implementação do Resource Manager e a implementação clássica](../resource-manager-deployment-model.md).

    > [AZURE.NOTE] Só é possível criar contas do Blob Storage ao utilizar o modelo de implementação Resource Manager.

5. Selecione o tipo da conta do Storage: **Fins gerais** ou **Blob Storage**. A predefinição é **Fins gerais**.

    Se tiver selecionado **Fins gerais**, especifique a camada de desempenho: **Standard** ou **Premium**. A predefinição é **Standard**. Para obter mais detalhes sobre as contas do Storage standard e premium, consulte [Introdução ao Storage do Microsoft Azure](storage-introduction.md) e [Premium Storage: Armazenamento de Elevado Desempenho para Cargas de Trabalho de Virtual Machines do Azure](storage-premium-storage.md).

    Se tiver selecionado **Blob Storage**, especifique a camada de acesso: **Frequente** ou **Esporádico**. A predefinição é **Frequente**. Consulte [Blob Storage do Azure: camadas de acesso frequente e esporádico](storage-blob-storage-tiers.md) para obter mais detalhes.

6. Selecione a opção de replicação para a conta do Storage: **LRS**, **GRS**, **RA-GRS** ou **ZRS**. A predefinição é **RA-GRS**. Consulte [Replicação do Storage do Azure](storage-redundancy.md) para obter detalhes adicionais sobre as opções de replicação do Storage do Azure.

7. Selecione a subscrição na qual pretende criar a nova conta do Storage.

8. Especifique um novo grupo de recursos ou selecione um grupo de recursos existente. Para obter mais informações sobre os grupos de recursos, veja [Descrição geral do Azure Resource Manager](../resource-group-overview.md).

9. Selecione a localização geográfica para a conta do Storage. Veja [Regiões do Azure](https://azure.microsoft.com/regions/#services) para obter mais informações sobre os serviços que estão disponíveis em cada região.

10. Clique em **Criar** para criar a conta do Storage.

## Gerir a conta do Storage

### Alterar a configuração da conta

Depois de criar a sua conta do Storage, pode modificar a respetiva configuração, como, por exemplo, ao alterar a opção de replicação utilizada para a conta ou alterar a camada de acesso para uma conta do Blob Storage. No [Portal do Azure](https://portal.azure.com), navegue até à sua conta de armazenamento, clique em **Todas as definições** e clique depois em **Configuração** para ver e/ou alterar a configuração da conta.

> [AZURE.NOTE] Consoante a camada de desempenho que escolheu ao criar a conta do Storage, algumas opções de replicação podem não estar disponíveis.

A alteração da opção de replicação irá alterar também os preços que lhe são cobrados. Para obter mais detalhes, consulte a página [Preços do Storage do Azure](https://azure.microsoft.com/pricing/details/storage/).

Para as contas do Blob Storage, a alteração da camada de acesso poderá resultar em encargos adicionais, para além de alterar os seus preços. Consulte [Contas do Blob Storage – Preços e Faturação](storage-blob-storage-tiers.md#pricing-and-billing) para obter mais detalhes.

### Gerir as chaves de acesso ao armazenamento

Quando cria uma conta do Storage, o Azure gera duas chaves de acesso ao armazenamento de 512 bits, que são utilizadas para autenticação quando a conta do Storage é acedida. Ao fornecer duas chaves de acesso ao armazenamento, o Azure permite que volte a gerar as chaves sem quaisquer interrupções ao seu serviço de armazenamento ou ao acesso a esse serviço.

> [AZURE.NOTE] Recomendamos que evite partilhar as chaves de acesso ao armazenamento com outras pessoas. Para permitir o acesso aos recursos de armazenamento sem fornecer as chaves de acesso, pode utilizar uma *assinatura de acesso partilhado*. Uma assinatura de acesso partilhado fornece acesso a um recurso na sua conta durante um intervalo definido por si e com as permissões que especificar. Veja [Utilizar Assinaturas de Acesso Partilhado (SAS)](storage-dotnet-shared-access-signature-part-1.md) para obter mais informações.

#### Ver e copiar chaves de acesso ao armazenamento

No [Portal do Azure](https://portal.azure.com), navegue até à sua conta de armazenamento, clique em **Todas as definições** e clique depois em **Chaves de acesso** para ver, copiar e voltar a gerar as chaves de acesso da conta. O painel **Chaves de Acesso** também inclui cadeias de ligação pré-configuradas com as chaves primárias e secundárias que pode copiar para utilizar nas suas aplicações.

#### Voltar a gerar chaves de acesso ao armazenamento

Recomendamos que altere periodicamente as chaves de acesso da sua conta do Storage para ajudar a manter as ligações de armazenamento seguras. São atribuídas duas chaves de acesso, para que possa manter as ligações à conta do Storage com uma chave de acesso enquanto volta a gerar a outra.

> [AZURE.WARNING] A regeneração das chaves de acesso pode afetar os serviços no Azure, bem como as suas próprias aplicações que dependem da conta do Storage. Todos os clientes que utilizam a chave de acesso para aceder à conta do Storage têm de ser atualizados para utilizar a nova chave.

**Media Services** – se tiver Media Services dependentes da sua conta do Storage, deve sincronizar novamente as chaves de acesso com o serviço de multimédia depois de voltar a gerar as chaves.

**Aplicações** – se tiver Web Apps ou Cloud Services que utilizam a conta do Storage, perderá as ligações se voltar a gerar chaves, a menos que reverta as chaves.

**Exploradores de Armazenamento** – se estiver a utilizar quaisquer [aplicações de explorador de armazenamento](storage-explorers.md), provavelmente terá de atualizar a chave de armazenamento utilizada por essas aplicações.

Eis o processo para rodar as chaves de acesso ao armazenamento:

1. Atualize as cadeias de ligação no código da aplicação para fazer referência à chave de acesso secundária da conta do Storage.

2. Volte a gerar a chave de acesso primária para a sua conta do Storage. No painel **Chaves de Acesso**, clique em **Voltar a gerar Chave1** e, em seguida, clique em **Sim** para confirmar que pretende gerar uma nova chave.

3. Atualize as cadeias de ligação no código para fazer referência à nova chave de acesso primária.

4. Volte a gerar a chave de acesso secundária da mesma forma.

## Eliminar uma conta do Storage

Para remover uma conta de armazenamento que já não está a utilizar, navegue até à mesma no [Portal do Azure](https://portal.azure.com) e clique em **Eliminar**. A eliminação de uma conta do Storage elimina toda a conta, incluindo todos os dados na mesma.

> [AZURE.WARNING] Não é possível restaurar uma conta do Storage eliminada ou obter os conteúdos que esta continha antes da eliminação. Certifique-se de que faz uma cópia de segurança de tudo o que pretende guardar antes de eliminar a conta. Isto também se aplica a quaisquer recursos na conta – depois de eliminar um blob, tabela, fila ou ficheiro, este é eliminado permanentemente.

Para eliminar uma conta do Storage que está associada uma máquina virtual do Azure, primeiro tem de se certificar de que todos os discos da máquina virtual foram eliminados. Se não eliminar primeiro os discos da máquina virtual, quando tentar eliminar a conta do Storage, verá uma mensagem de erro semelhante a esta:

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

Se a conta de armazenamento utilizar o modelo de implementação Clássica, pode remover o disco da máquina virtual ao seguir estes passos no [Portal do Azure](https://manage.windowsazure.com):

1. Navegue até ao [Portal clássico do Azure](https://manage.windowsazure.com).
2. Navegue até ao separador Virtual Machines.
3. Clique no separador Discos.
4. Selecione o disco de dados e, em seguida, clique em Eliminar Disco.
5. Para eliminar imagens do disco, navegue até ao separador Imagens e elimine quaisquer imagens armazenadas na conta.

Para obter mais informações, consulte a [documentação de Virtual Machines do Azure](http://azure.microsoft.com/documentation/services/virtual-machines/).

## Passos seguintes

- [Blob Storage do Azure: camadas de acesso frequente e esporádico](storage-blob-storage-tiers.md)
- [Replicação do Storage do Azure](storage-redundancy.md)
- [Configurar Cadeias de Ligação do Storage do Azure](storage-configure-connection-string.md)
- [Transferir dados com o Utilitário de Linha de Comandos AzCopy](storage-use-azcopy.md)
- Aceda ao [Blogue da Equipa do Storage do Azure](http://blogs.msdn.com/b/windowsazurestorage/).



<!--HONumber=Sep16_HO3-->


