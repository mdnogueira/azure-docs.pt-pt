```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|routePrefix|api|O prefixo de rota que aplica-se a todas as rotas. Utilize uma cadeia vazia para remover o prefixo de predefinição. |
|maxOutstandingRequests|-1|O número máximo de pedidos pendentes que será retida em qualquer momento (-1 significa que unbounded). O limite inclui pedidos que são colocados em fila, mas não iniciaram a executar, bem como quaisquer execuções de em curso. Quaisquer pedidos recebidos este limite são rejeitados com uma resposta "Demasiado ocupado" 429. Os chamadores podem utilizar essa resposta recorrer estratégias de repetição baseados no tempo. Controlos esta definição apenas colocação em fila que ocorre no caminho de execução do anfitrião de tarefa. Outras filas, por exemplo, a fila de pedidos do ASP.NET, não são afetadas por esta definição. |
|maxConcurrentRequests|-1|O número máximo de funções HTTP que será executado em paralelo (-1 significa que unbounded). Por exemplo, pode definir um limite, se as suas funções HTTP utilizam demasiados recursos do sistema quando a simultaneidade é elevada. Ou, se as suas funções efetuam pedidos de saída para um serviço de terceiros, essas chamadas poderão ter de ser limitado de taxa.|
|dynamicThrottlesEnabled|False|Faz com que o pipeline de processamento de pedidos verificar periodicamente os contadores de desempenho do sistema. Contadores incluem ligações, threads, processos, memória e cpu. Se qualquer um dos contadores através de um limiar incorporado (80%), os pedidos são rejeitados com uma resposta "Demasiado ocupado" 429 até o counter(s) regressar à níveis normais.|
