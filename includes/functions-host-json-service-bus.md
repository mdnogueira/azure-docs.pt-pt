```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|maxConcurrentCalls|16|O número máximo de chamadas simultâneas para a chamada de retorno que bomba a mensagem deve iniciar. Por predefinição, o tempo de execução de funções processa mensagens múltiplas simultaneamente. Para direcionar o tempo de execução para processar apenas uma fila única ou uma mensagem de tópico simultaneamente, defina `maxConcurrentCalls` para 1. | 
|prefetchCount|n/d|A predefinição PrefetchCount que será utilizado pelo MessageReceiver subjacente.| 
|autoRenewTimeout|00:05:00|A duração máxima no qual o bloqueio da mensagem será renovado a automaticamente.| 
