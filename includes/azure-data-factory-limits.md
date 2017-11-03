Fábrica de dados é um serviço de multi-inquilino que tem os seguintes limites predefinido para se certificar de que as subscrições de cliente estão protegidas de cargas de trabalho entre si. Muitas dos limites podem ser facilmente geradas para a sua subscrição até ao limite máximo contactando o suporte.

### <a name="version-2"></a>Versão 2

| Recurso | Limite Predefinido | Limite Máximo | 
| -------- | ------------- | ------------- | 
| fábricas de dados numa subscrição do Azure | 50 | [Contactar o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| pipelines dentro de uma fábrica de dados | 2500 | [Contactar o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| conjuntos de dados dentro de uma fábrica de dados | 2500 | [Contactar o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Acionadores dentro de uma fábrica de dados | 2500 | [Contactar o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Serviços ligados dentro de uma fábrica de dados | 2500 | [Contactar o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Tempos de execução de integração dentro de uma fábrica de dados <sup>4</sup> | 2500 | [Contactar o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Pipeline em simultâneo é executada por pipeline | 20 | [Contactar o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Atividades de máx. por pipeline | 20 | 30 |
| Parâmetros de máx. por pipeline | 20 | 30 |
| bytes por objeto para objetos de pipeline <sup>1</sup> | 200 KB | 200 KB |
| bytes por objeto para o conjunto de dados e objetos do serviço ligado <sup>1</sup> | 100 KB | 2000 KB |
| Unidades de movimento de dados de nuvem <sup>3</sup> | 32 | [Contactar o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Repetir contagem pipeline para execuções de atividade | 1 day(timeout) | 1 dia (tempo limite) |
| Escrever chamadas de API | 2500/hr<br/><br/> Este limite é imposta pelo Azure Resource Manager, não o Azure Data Factory. | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Chamadas da API de leitura | 12,500/hr<br/><br/> Este limite é imposta pelo Azure Resource Manager, não o Azure Data Factory. | [Contactar o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |


### <a name="version-1"></a>Versão 1

| **Recurso** | **Limite Predefinido** | **Limite Máximo** |
| --- | --- | --- |
| fábricas de dados numa subscrição do Azure |50 |[Contactar o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| pipelines dentro de uma fábrica de dados |2500 |[Contactar o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| conjuntos de dados dentro de uma fábrica de dados |5000 |[Contactar o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| setores em simultâneo por conjunto de dados |10 |10 |
| bytes por objeto para objetos de pipeline <sup>1</sup> |200 KB |200 KB |
| bytes por objeto para o conjunto de dados e objetos do serviço ligado <sup>1</sup> |100 KB |2000 KB |
| Núcleos a pedido no cluster de HDInsight numa subscrição <sup>2</sup> |60 |[Contactar o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Unidades de movimento de dados de nuvem <sup>3</sup> |32 |[Contactar o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Repetir contagem pipeline para execuções de atividade |1000 |MaxInt (32 bits) |

<sup>1</sup> pipeline, conjunto de dados e objetos do serviço ligado de representar um agrupamento lógico da carga de trabalho. Os limites para estes objetos não estão relacionadas com a quantidade de dados, pode mover e processar com o serviço do Azure Data Factory. Fábrica de dados foi concebida para dimensionar para processar petabytes de dados.

<sup>2</sup> HDInsight a pedido núcleos alocados fora da subscrição que contém a fábrica de dados. Como resultado, o limite superior é a fábrica de dados imposta limite núcleos núcleos de HDInsight a pedido e é diferente do que o limite de núcleos associado à subscrição do Azure.

<sup>3</sup> unidade de movimento de dados de nuvem (DMU) está a ser utilizada numa operação de cópia da nuvem para a nuvem. É uma medida que representa a potência (uma combinação de CPU, memória e alocação de recursos de rede) de uma única unidade na fábrica de dados. Pode obter um maior débito de cópia utilizando mais DMUs para alguns cenários. Consulte [unidades de movimento de dados de nuvem](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units) secção em detalhes.

<sup>4</sup> a integração do tempo de execução (IR) é a infraestrutura de computação utilizada pelo Azure Data Factory para fornecer as seguintes capacidades de integração de dados entre diferentes ambientes de rede: movimento de dados, distribuição de atividades para serviços de computação execução de pacotes SSIS. Para obter mais informações, consulte [descrição geral de tempo de execução de integração](../articles/data-factory/concepts-integration-runtime.md).

| **Recurso** | **Predefinição de limite inferior** | **Limite mínimo** |
| --- | --- | --- |
| Intervalo de agendamento |15 minutos |15 minutos |
| Intervalo entre tentativas de repetição |1 segundo |1 segundo |
| Repita o valor de tempo limite |1 segundo |1 segundo |

#### <a name="web-service-call-limits"></a>Limites de chamada de serviço Web
O Azure Resource Manager tem os limites de chamadas à API. Pode efetuar chamadas à API uma taxa dentro de [limita a API do Azure Resource Manager](../articles/azure-subscription-service-limits.md#resource-group-limits).
