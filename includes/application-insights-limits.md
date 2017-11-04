Existem alguns limites no número de métricas e eventos por aplicação (ou seja, por chave de instrumentação). Os limites dependem do [plano de preços](https://azure.microsoft.com/pricing/details/application-insights/) que escolher.

| **Recurso** | **Limite predefinido** | **Nota**
| --- | --- | --- |
| Total de dados por dia | 500 GB | Pode reduzir os dados ao definir um limite. Se precisar de mais, envie um e-mail para AIDataCap@microsoft.com.
| Dados gratuitos por mês<br/> (Plano de preços Básico) | 1 GB | Os dados adicionais cobrados por gigabyte.
| Limitação | 32 k de eventos/segundo | O limite é mediso ao longo de um minuto.
| Retenção de dados | 90 dias | Este recurso é para [Pesquisa](../articles/application-insights/app-insights-diagnostic-search.md), [Análise](../articles/application-insights/app-insights-analytics.md) e [Explorador de métricas](../articles/application-insights/app-insights-metrics-explorer.md).
| [Teste de disponibilidade de vários passos](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests) com retenção de resultados detalhados | 90 dias | Este recurso fornece resultados detalhados de cada passo.
| Tamanho máximo do evento | 64 KB | 
| Comprimento do nome da propriedade e da métrica | 150 | Consulte [escreva esquemas](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)
| Comprimento da cadeia de valor da propriedade | 8,192 | Consulte [escreva esquemas](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)
| Comprimento da mensagem de exceção e de rastreio | 10 k | Consulte [escreva esquemas](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)
| [Testes de disponibilidade](../articles/application-insights/app-insights-monitor-web-app-availability.md) com contagem por aplicação  | 10 |
| [O gerador de perfis](../articles/application-insights/app-insights-profiler.md) retenção de dados | 5 dias |
| [O gerador de perfis](../articles/application-insights/app-insights-profiler.md) dados enviados por dia | 10GB |

Para mais informações, consulte [About pricing and quotas in Application Insights (Acerca de preços e quotas no Application Insights)](../articles/application-insights/app-insights-pricing.md).

