Pode criar vários serviços numa subscrição, cada um deles aprovisionados a nenhum escalão específico, limitado apenas pelo número de serviços permitidos em cada camada. Por exemplo, pode criar até 12 serviços na camada básica e outro serviços 12 na camada S1 dentro da mesma subscrição. Para obter mais informações sobre as camadas, consulte [escolha um SKU ou a camada para a Azure Search](../articles/search/search-sku-tier.md).

Limites de serviço máximo podem desencadeados após pedido. Se precisar de mais serviços dentro da mesma subscrição, contacte o suporte do Azure.

| Recurso | Gratuito | Básica | S1 | S2 | S3 | S3 HD <sup>1</sup> |
| --- | --- | --- | --- | --- | --- | --- |
| Serviços máximos |1 |12 |12 |6 |6 |6 |
| Dimensionamento máximo no SU <sup>2</sup> |N/D <sup>3</sup> |3 SU <sup>4</sup> |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> S3 HD não suporta [indexadores](../articles/search/search-indexer-overview.md) neste momento. 

<sup>2</sup> unidades de pesquisa (SU) são faturação unidades, atribuídas como um *réplica* ou um *partição*. Terá de ambos os recursos de armazenamento, a indexação e as operações de consulta. Para obter mais informações sobre como são calculadas unidades de pesquisa, mais um gráfico de combinações válidos que permanecem sob os limites máximos, consulte o artigo [Dimensionar níveis de recursos de cargas de trabalho de consulta e índice](../articles/search/search-capacity-planning.md). 

<sup>3</sup> livres é baseada em recursos partilhados utilizados por vários subscritores. Nesta camada, existem não existem recursos dedicados para um subscritor individuais. Por este motivo, o dimensionamento máximo é marcado como não aplicável.

<sup>4</sup> basic tem uma partição fixa. Nesta camada SUs adicionais são utilizados para alocar mais réplicas para cargas de trabalho de consulta de aumento.

