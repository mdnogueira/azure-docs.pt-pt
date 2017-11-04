A tabela seguinte apresenta uma lista de quotas e limites específica [Event Hubs do Azure](https://azure.microsoft.com/services/event-hubs/). Para obter informações sobre os preços de Hubs de eventos, consulte [preços de Hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

| Limite | Âmbito | Tipo | Comportamento quando excedido | Valor |
| --- | --- | --- | --- | --- |
| Número dos event hubs por espaço de nomes |Espaço de nomes |Estático |Os pedidos subsequentes para a criação de um novo hub de eventos serão rejeitados. |10 |
| Número de partições por hub de eventos |Entidade |Estático |- |32 |
| Número de grupos de consumidores por hub de eventos |Entidade |Estático |- |20 |
| Número de ligações AMQP por espaço de nomes |Espaço de nomes |Estático |Os pedidos subsequentes para ligações adicionais serão rejeitados e uma exceção é recebida pelo código da chamada. |5,000 |
| Tamanho máximo do evento de Event Hubs|Em todo o sistema |Estático |- |256 KB |
| Tamanho máximo de um nome de hub de eventos |Entidade |Estático |- |50 carateres |
| Número de não-época recetores por grupo de consumidores |Entidade |Estático |- |5 |
| Período de retenção máximo de dados de eventos |Entidade |Estático |- |1-7 dias |
| Unidades de débito máximas |Espaço de nomes |Estático |Exceder o limite de unidade de débito faz com que os dados ser limitada e gera um  **[ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)**. Pode pedir um grande número de unidades de débito para um padrão camada pelo arquivo um [pedido de suporte](/azure/azure-supportability/how-to-create-azure-support-request). [Unidades de débito adicionais](../articles/event-hubs/event-hubs-auto-inflate.md) estão disponíveis em blocos de 20 numa base de compra consolidada. |20 |
| Número de regras de autorização por espaço de nomes |Espaço de nomes|Estático |Os pedidos subsequentes para a criação de regra de autorização serão rejeitados.|12 |
