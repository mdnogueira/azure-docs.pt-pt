A tabela seguinte mostra os enlaces que são suportados nas duas versões principais do tempo de execução das funções do Azure.

| Tipo | 1. x | 2. x | Acionador | Input | Saída |  
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Armazenamento de Blobs](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|  
| [BD do Cosmos](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔<sup>1</sup>|✔|✔|✔|  
| [Hubs de Eventos](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|  
| [Ficheiro externo](../articles/azure-functions/functions-bindings-external-file.md)<sup>2</sup>    |✔|| |✔|✔|  
| [Tabela externa](../articles/azure-functions/functions-bindings-external-table.md)<sup>2</sup>  |✔|| |✔|✔|  
| [HTTP](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [Microsoft Graph<br/>tabelas do Excel](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔<sup>1</sup>| |✔|✔|
| [Microsoft Graph<br/>ficheiros do OneDrive](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔<sup>1</sup>| |✔|✔|
| [Microsoft Graph<br/>e-mail do Outlook](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔<sup>1</sup>| | |✔|
| [Microsoft Graph<br/>eventos](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔<sup>1</sup>|✔|✔|✔|
| [Microsoft Graph<br/>tokens de autenticação](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔<sup>1</sup>| |✔| |
| [Aplicações Móveis](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔|✔<sup>1</sup>| |✔|✔|  
| [Hubs de Notificação](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Armazenamento de filas](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔|✔| |✔|  
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔<sup>1</sup>| | |✔|
| [Service Bus](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔<sup>1</sup>|✔| |✔|  
| [Armazenamento de tabelas](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|  
| [Temporizador](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔<sup>1</sup>| | |✔|
| [Webhooks](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔||✔| |✔|
  
<sup>1</sup> tem de ser registado como uma extensão de enlace em 2. x. Consulte [problemas no 2 conhecidos](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).

<sup>2</sup> Experimental &mdash; não suportados e poderão ser abandonada no futuro.

