```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|maxPollingInterval|60000|O intervalo máximo em milissegundos entre inquéritos de fila de mensagens em fila.| 
|visibilityTimeout|0|O intervalo de tempo entre tentativas durante o processamento de uma mensagem de falha.| 
|batchSize|16|O número de mensagens de fila para obter e processar em paralelo. O valor máximo é 32.| 
|maxDequeueCount|5|O número de vezes para tentar processar uma mensagem antes de passar para a fila nocivas.| 
|newBatchThreshold|batchSize/2|O limiar em que um novo lote de mensagens em fila são obtidas.| 
