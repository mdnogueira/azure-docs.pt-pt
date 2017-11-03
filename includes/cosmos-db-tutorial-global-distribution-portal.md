
Pode saber sobre a base de dados do Azure Cosmos distribuição global neste Azure sexta-feira vídeo com autoria de Scott Hanselman e o Gestor de engenharia do Principal Karthik Raman.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Planet-Scale-NoSQL-with-DocumentDB/player]  

Para obter mais informações sobre a replicação de base de dados como global funciona do BD Azure Cosmos, consulte [distribuir dados globalmente com Cosmos DB](../articles/cosmos-db/distribute-data-globally.md).

## <a id="addregion"></a>Adicionar regiões de base de dados global através do Portal do Azure
BD do Cosmos do Azure está disponível em todos os [regiões do Azure] [ azureregions] em todo o mundo. Depois de selecionar o nível de consistência predefinida para a sua conta de base de dados, pode associar um ou mais regiões (dependendo da sua escolha de necessidades de distribuição global e nível de consistência predefinida).

1. No [portal do Azure](https://portal.azure.com/), na barra da esquerda, clique em **Azure Cosmos DB**.
2. No **Azure Cosmos DB** painel, selecione a base de dados de conta para modificar.
3. No painel de conta, clique em **replicar dados globalmente** no menu.
4. No **replicar dados globalmente** painel, selecione as regiões para adicionar ou remover, clicando em regiões no mapa e, em seguida, clique em **guardar**. Existe um custo a adição de regiões, consulte o [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) ou [distribuir dados globalmente com a base de dados do Azure Cosmos](../articles/cosmos-db/distribute-data-globally.md) artigo para obter mais informações.
   
    ![Clique em regiões no mapa para adicionar ou removê-las][1]
    
Depois de adicionar uma segunda região, o **ativação pós-falha do Manual** opção está ativada no **replicar dados globalmente** painel no portal. Pode utilizar esta opção para testar o processo de ativação pós-falha ou alterar a região primária escrita. Depois de adicionar uma terceira região, o **prioridades de ativação pós-falha** opção está ativada no mesmo painel, para que possa alterar a ordem de ativação pós-falha para leituras.  

### <a name="selecting-global-database-regions"></a>A seleção de regiões de base de dados globais
Existem dois cenários comuns para configurar dois ou mais regiões:

1. Entrega de latência baixa acesso aos dados para os utilizadores finais, independentemente de onde estão localizados em torno de todo o mundo
2. Adicionar regional resiliência de continuidade do negócio e recuperação após desastre (BCDR)

Para entrega de latência baixa para os utilizadores finais, é recomendado para implementar ambas a aplicação e adicionar BD do Azure do Cosmos nas regiões que correspondem é onde os utilizadores da aplicação estão localizados.

Para BCDR, recomenda-se para adicionar regiões com base nos pares de região descritos a [recuperação de continuidade e desastre de negócio (BCDR): Azure emparelhado regiões] [ bcdr] artigo.

<!--

## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your Cosmos DB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **Azure Cosmos DB** blade, select the database account to modify.
2. In the account blade, click **Replicate data globally** from the menu.
3. In the **Replicate data globally** blade, click **Manual Failover** from the top bar.
    ![Change the write region under Azure Cosmos DB Account > Replicate data globally > Manual Failover][2]
4. Select a read region to become the new write region, click the checkbox to confirm triggering a failover, and click OK
    ![Change the write region by selecting a new region in list under Azure Cosmos DB Account > Replicate data globally > Manual Failover][3]

--->

<!--Image references-->
[1]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-add-region.png
[2]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-1.png
[3]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: ../articles/cosmos-db/consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
