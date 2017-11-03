Coleção de medidas personalizadas. Utilize esta coleção para o relatório com o nome de medida associada ao item de telemetria. Casos de utilização típicos são:
- o tamanho do payload de telemetria de dependência
- o número de itens da fila processados pelo pedido de telemetria
- tempo desse cliente demorou a concluir o passo na conclusão do passo do Assistente para eventos de telemetria.

Pode consultar [medidas personalizadas](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) na análise de aplicação:

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Medidas personalizadas estão associadas a do item de telemetria que pertencem a. Estão sujeitos a amostragem com o item de telemetria que contém as medidas. Para monitorizar uma medida que tem um valor independente dos outros tipos de telemetria, utilize [telemetria métrica](../articles/application-insights/app-insights-api-custom-events-metrics.md).

Comprimento de chave máximo: 150
