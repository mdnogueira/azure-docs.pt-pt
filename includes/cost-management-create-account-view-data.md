## <a name="view-cost-data"></a>Ver dados de custos

A Gestão de Custos do Azure pela Cloudyn proporciona acesso a todos os dados dos seus recursos na cloud. A partir dos relatórios do dashboard, pode encontrar relatórios padrão e personalizados numa vista com separadores. Seguem-se alguns exemplos de um dashboard popular e de um relatório que lhe mostram os seus dados de custo de imediato.

![Dashboard de gestão](./media/cost-management-create-account-view-data/mgt-dash.png)

Neste exemplo, o dashboard de Gestão mostra os custos consolidados para a empresa Contoso em todos os seus recursos na cloud. A Contoso utiliza o Azure, o AWS e o Google. Os dashboards fornecem informações resumidas e são uma forma rápida de navegar até os relatórios.  

Se não tiver a certeza do objetivo de um relatório num dashboard, coloque o cursor sobre o símbolo **i** para ver uma explicação. Clique em qualquer relatório num dashboard para ver o relatório completo.

Também pode ver relatórios com o menu de relatórios na parte superior do portal. Vamos ver os gastos da Contoso em recursos do Azure nos últimos 30 dias. Clique em **Custo** > **Análise de Custo** > **Análise de Custo Real**. Limpe todos os valores se existirem definições para etiquetas, grupos ou filtros no relatório.

![Análise de Custo Real](./media/cost-management-create-account-view-data/actual-cost-01.png)

Neste exemplo, $75.970 é o custo total e o orçamento é $130.000.

Agora, vamos modificar o formato do relatório e definir grupos e filtros para restringir os resultados para os custos do Azure. Defina o **Intervalo de Datas** como os últimos 30 dias. No canto superior direito, clique no símbolo de coluna para formatar como um gráfico de barras e, em Grupos, selecione **Fornecedor**. Em seguida, defina um filtro para **Fornecedor** como **Azure**.

![Análise de Custo Real filtrada](./media/cost-management-create-account-view-data/actual-cost-02.png)

Neste exemplo, o custo total de recursos do Azure foi $3.839 nos últimos 30 dias.

Clique com o botão direito do rato na barra Fornecedor (Azure) e desagregue até **Tipos de recursos**.

![desagregar](./media/cost-management-create-account-view-data/actual-cost-03.png)

A imagem seguinte mostra os custos dos recursos do Azure em que a Contoso incorreu. O total foi $3.839. Neste exemplo, cerca de metade dos custos destinou-se a armazenamento localmente redundante e a outra metade dos custos, aproximadamente, destinou-se a várias instâncias de VM.

![tipos de recursos](./media/cost-management-create-account-view-data/actual-cost-04.png)

Clique com o botão direito do rato num tipo de recurso e selecione **Entidades de Custos** para ver as entidades dos custos e os serviços que consumiram o recurso. Os serviços de VM e Workers no DevOps consumiram $486,60 e $435,71 neste exemplo. O total para ambos é $922.

![entidades de custos e serviços](./media/cost-management-create-account-view-data/actual-cost-05.png)
