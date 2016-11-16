Existem dois tipos de contas do armazenamento:

### <a name="generalpurpose-storage-accounts"></a>Contas de Armazenamento para fins gerais
Uma conta de armazenamento para fins gerais dá-lhe acesso aos serviços do Storage do Azure, como Tabelas, Filas, Ficheiros, Blobs e discos da máquina virtual do Azure numa conta única. Este tipo de conta de armazenamento tem dois escalões de desempenho:

* Um escalão de desempenho de armazenamento Standard que lhe permite armazenar as Tabelas, as Filas, os Ficheiros, os Blobs e os discos da máquina virtual do Azure.
* Um escalão de desempenho de armazenamento Premium que atualmente apenas suporta discos de máquinas virtuais do Azure. Veja [Armazenamento Premium: Armazenamento de Elevado Desempenho para Cargas de Trabalho de Máquinas Virtuais do Azure](../articles/storage/storage-premium-storage.md) para obter uma descrição geral aprofundada do armazenamento Premium.

### <a name="blob-storage-accounts"></a>Contas do Blob Storage
Uma conta de armazenamento de Blobs é uma conta de armazenamento especializada para armazenar os seus dados não estruturados como blobs (objetos) no Storage do Azure. As contas de armazenamento de Blobs são semelhantes às contas de armazenamento para fins gerais existentes e partilham todas as excelentes características de durabilidade, disponibilidade, escalabilidade e desempenho que utiliza atualmente, incluindo 100% de consistência com a API dos blobs de blocos e dos blobs de acréscimo. Para aplicações que requerem apenas armazenamento de blobs de blocos ou de blobs de acréscimo, recomendamos a utilização das contas de armazenamento de Blobs.

> [!NOTE]
> As contas de armazenamento de Blobs suportam apenas blobs de blocos e blobs de acréscimo e não suportam blobs de páginas.
> 
> 

As contas de armazenamento de Blobs expõem o atributo **Camada de Acesso**, que pode ser especificado durante a criação da conta e modificado mais tarde, conforme necessário. Existem dois tipos de camadas de acesso que podem ser especificados com base no seu padrão de acesso a dados:

* Uma camada de acesso **Frequente**, que indica que os objetos na conta de armazenamento serão acedidos com mais frequência. Esta opção permite-lhe armazenar dados a um custo mais baixo de acesso.
* Uma camada de acesso **Esporádico**, que indica que os objetos na conta de armazenamento serão acedidos com menos frequência. Esta opção permite-lhe armazenar dados a um custo mais baixo de armazenamento.

Se o padrão de utilização dos dados sofrer uma alteração, pode também alternar entre estas camadas de acesso em qualquer altura. A alteração da camada de acesso poderá resultar em encargos adicionais. Veja [Preços e faturação das contas de armazenamento de Blobs ](../articles/storage/storage-blob-storage-tiers.md#pricing-and-billing) para obter mais detalhes.

Para obter mais detalhes sobre as contas de armazenamento de Blobs, veja [ Blob Storage do Azure: camadas de acesso Frequente e Esporádico](../articles/storage/storage-blob-storage-tiers.md).

Para poder criar uma conta de armazenamento, terá de ter uma subscrição do Azure, que é um plano que lhe dá acesso a vários serviços do Azure. Pode começar com o Azure com uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/). Quando decidir comprar um plano de subscrição, pode escolher entre uma variedade de [opções de compra](https://azure.microsoft.com/pricing/purchase-options/). Se for um [Subscritor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), obterá créditos mensais gratuitos que pode utilizar com os serviços do Azure, incluindo o Storage do Azure. Veja [Preços de Armazenamento do Azure ](https://azure.microsoft.com/pricing/details/storage/) para obter informações sobre os preços de volumes.

Para saber como criar uma conta de armazenamento, veja [Criar uma conta de armazenamento](../articles/storage/storage-create-storage-account.md#create-a-storage-account) para obter mais detalhes. Pode criar até 100 contas de armazenamento com nomes exclusivos com uma única subscrição. Veja [Metas de Desempenho e Escalabilidade do Armazenamento do Azure](../articles/storage/storage-scalability-targets.md) para obter detalhes sobre os limites das contas de armazenamento.



<!--HONumber=Nov16_HO2-->


