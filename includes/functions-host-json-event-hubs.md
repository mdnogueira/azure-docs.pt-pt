```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|maxBatchSize|64|A contagem máxima do evento recebida por ciclo de recepção.|
|prefetchCount|n/d|A predefinição PrefetchCount que será utilizado pelo EventProcessorHost subjacente.| 
|batchCheckpointFrequency|1|O número de eventos de lotes para processar antes de criar um ponto de verificação de cursor de EventHub.| 
