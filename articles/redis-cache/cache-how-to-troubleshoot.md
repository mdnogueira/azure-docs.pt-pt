---
title: Como resolver problemas com a Cache de Redis do Azure | Microsoft Docs
description: Saiba como resolver problemas comuns com a Cache de Redis do Azure.
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: sdanie
ms.openlocfilehash: 2e9d1b644f1e80c7d916a261a6c47fcc11a1ffe0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-troubleshoot-azure-redis-cache"></a>Como resolver problemas com a Cache de Redis do Azure
Este artigo fornece orientações para resolução de problemas seguintes categorias de problemas de Cache de Redis do Azure.

* [Resolução de problemas de lado do cliente](#client-side-troubleshooting) - esta secção fornece orientações sobre a identificar e resolver problemas causados pela aplicação ligar à Cache de Redis do Azure.
* [Resolução de problemas de lado do servidor](#server-side-troubleshooting) - esta secção fornece orientações sobre a identificar e resolver problemas causados no lado do servidor de Cache de Redis do Azure.
* [Exceções de tempo limite de stackexchange. redis](#stackexchangeredis-timeout-exceptions) -esta secção fornece informações sobre como resolver problemas ao utilizar o cliente stackexchange. redis.

> [!NOTE]
> Muitos dos passos de resolução de problemas neste guia incluem instruções para executar comandos de Redis e monitorizar várias métricas de desempenho. Para obter mais informações e instruções, consulte os artigos de [informações adicionais](#additional-information) secção.
> 
> 

## <a name="client-side-troubleshooting"></a>Resolução de problemas de lado do cliente
Esta secção descreve problemas de resolução de problemas que ocorrem devido a uma condição na aplicação de cliente.

* [Pressão de memória no cliente](#memory-pressure-on-the-client)
* [Rajada de tráfego](#burst-of-traffic)
* [Cliente elevada utilização da CPU](#high-client-cpu-usage)
* [Largura de banda do lado de cliente foi excedida](#client-side-bandwidth-exceeded)
* [Tamanho grande de pedido/resposta](#large-requestresponse-size)
* [O que aconteceu Redis os meus dados?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Pressão de memória no cliente
#### <a name="problem"></a>Problema
Pressão de memória no computador cliente leva a todos os tipos de problemas de desempenho que podem atrasar o processamento de dados foi enviados pela instância Redis sem qualquer atrasos. Quando chega a pressão de memória, o sistema, normalmente, tem de dados de página de memória física para virtual memória que está no disco. Isto *página originar falha* faz com que o sistema para significativamente mais lentos.

#### <a name="measurement"></a>Medida
1. Monitorizar a utilização de memória no computador para se certificar de que não exceda memória disponível. 
2. Monitor de `Page Faults/Sec` contador de desempenho. Maioria dos sistemas será tem algumas falhas da página, mesmo durante o funcionamento normal, por isso, observar picos neste contador de desempenho de falhas de página que correspondam com tempos limite.

#### <a name="resolution"></a>Resolução
Atualize o cliente para um cliente maior tamanho da VM com mais memória ou aprofundar no seus padrões de utilização de memória para reduzir consuption de memória.

### <a name="burst-of-traffic"></a>Rajada de tráfego
#### <a name="problem"></a>Problema
Bursts de tráfego combinado com fraco `ThreadPool` definições podem resultar em atrasos no processamento de dados já enviada pelo servidor de Redis, mas ainda não foram consumidos do lado do cliente.

#### <a name="measurement"></a>Medida
Monitor de como o `ThreadPool` estatísticas alteram ao longo do tempo utilizando código [assim](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Também pode ver o `TimeoutException` mensagem do stackexchange. redis. Eis um exemplo:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

A mensagem acima, existem alguns problemas que sejam interessantes:

1. Tenha em atenção que no `IOCP` secção e o `WORKER` secção tem um `Busy` valor é maior do que o `Min` valor. Isto significa que o `ThreadPool` definições necessário ajustar.
2. Também pode ver `in: 64221`. Isto indica que 64211 foram recebidos na camada de socket de kernel, mas ainda não tiver sido lido pela aplicação (por exemplo, stackexchange. redis). Isto significa normalmente que a aplicação não é leitura de dados a partir da rede tão depressa quanto o servidor está a enviar-lhe.

#### <a name="resolution"></a>Resolução
Configurar o seu [ThreadPool definições](https://gist.github.com/JonCole/e65411214030f0d823cb) para se certificar de que o conjunto de threads irão aumentar verticalmente rapidamente em impulsar cenários.

### <a name="high-client-cpu-usage"></a>Cliente elevada utilização da CPU
#### <a name="problem"></a>Problema
Utilização elevada da CPU no cliente é uma indicação de que o sistema não é possível manter-se com o trabalho que foi pedido para executar. Isto significa que o cliente pode não conseguir processar uma resposta de Redis atempadamente, apesar de Redis enviada a resposta muito rapidamente.

#### <a name="measurement"></a>Medida
Monitorize a utilização de CPU Wide sistema através do Portal do Azure ou através do contador de desempenho associado. Seja cuidadoso não monitorizar *processo* CPU porque um único processo pode ter baixa utilização da CPU ao mesmo tempo que geral do sistema CPU pode ser elevada. Procurar picos na utilização da CPU que correspondem com tempos limite. Como resultado elevada da CPU, pode também ver alto `in: XXX` valores na `TimeoutException` mensagens de erro, conforme descrito no [rajada de tráfego](#burst-of-traffic) secção.

> [!NOTE]
> Stackexchange. redis 1.1.603 e mais tarde inclui o `local-cpu` métrica no `TimeoutException` mensagens de erro. Certifique-se de que a versão mais recente do [pacote NuGet stackexchange. redis](https://www.nuget.org/packages/StackExchange.Redis/). Existem erros constantemente fixos no código para tornar mais robusto para tempos limite, para que ter a versão mais recente é importante.
> 
> 

#### <a name="resolution"></a>Resolução
Atualizar para um maior tamanho da VM com mais capacidade de CPU ou investigar o que está a causar os picos de CPU. 

### <a name="client-side-bandwidth-exceeded"></a>Largura de banda do lado de cliente foi excedida
#### <a name="problem"></a>Problema
As máquinas cliente tamanho diferentes têm limitações quanto largura de banda de rede tem disponíveis. Se o cliente excede a largura de banda disponível, em seguida, dados não serão processados no lado do cliente tão depressa quanto o servidor está a enviar. Isto pode levar a tempos limite.

#### <a name="measurement"></a>Medida
Monitorizar como a utilização da largura de banda alteram ao longo do tempo utilizando código [assim](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Tenha em atenção que este código poderá não ser executado com êxito em alguns ambientes com permissões restritas (como sites de web do Azure).

#### <a name="resolution"></a>Resolução
Tamanho da VM do cliente de aumentar ou reduzir o consumo de largura de banda de rede.

### <a name="large-requestresponse-size"></a>Tamanho grande de pedido/resposta
#### <a name="problem"></a>Problema
Uma grande pedido/resposta pode provocar tempos limite. Por exemplo, suponha que o valor de tempo limite configurado no cliente é de 1 segundo. A aplicação solicite duas chaves (por exemplo 'A' e 'B') em simultâneo (utilizando a mesma ligação de rede física). A maioria dos clientes suportam "Pipelining" de pedidos, de forma a que ambos os pedidos '' e 'B' enviados para a transmissão para o servidor um após outro sem aguardar que as respostas. O servidor enviará as respostas de volta pela mesma ordem. Se a resposta 'A' for grande suficientemente-lo pode eat cópias de segurança mais do que o tempo limite para os pedidos subsequentes. 

O exemplo seguinte demonstra neste cenário. Neste cenário, pedido '' e 'B' são enviados rapidamente, o servidor começar rapidamente a enviar as respostas '' e 'B', mas devido a tempos de transferência de dados, 'B' ficar bloqueado por trás do pedido e tempos limite, apesar do servidor respondeu rapidamente.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Medida
Este é um difícil para medir. Basicamente tem instrumentar o código de cliente para controlar pedidos grande e respostas. 

#### <a name="resolution"></a>Resolução
1. Redis está otimizada para um grande número de valores pequenos, em vez de alguns valores grandes. É a solução preferida dividir os dados para os valores mais pequenos relacionados. Consulte o [o que é o intervalo de tamanho do valor ideal para redis? 100KB é demasiado grande? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) publique para obter mais detalhes em torno do motivo pelo qual os valores mais pequenos são recomendados.
2. Aumente o tamanho da VM (para o cliente e servidor de Cache de Redis), para obter capacidades de largura de banda superiores, reduzindo os tempos de transferência de dados para respostas maior. Tenha em atenção que ao obter mais largura de banda no apenas o servidor ou apenas no cliente pode não ser suficiente. Medir a utilização da largura de banda e compare-as com as capacidades do tamanho da VM tem atualmente.
3. Aumentar o número de `ConnectionMultiplexer` objetos os pedidos de round robin e de utilização através de ligações diferentes.

### <a name="what-happened-to-my-data-in-redis"></a>O que aconteceu Redis os meus dados?
#### <a name="problem"></a>Problema
Posso para determinados esperado de dados na minha instância da Cache de Redis do Azure, mas não tenha aparentemente, não existe.

#### <a name="resolution"></a>Resolução
Consulte [o que aconteceu os meus dados Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) para possíveis causas e soluções.

## <a name="server-side-troubleshooting"></a>Resolução de problemas de lado do servidor
Esta secção descreve problemas de resolução de problemas que ocorrem devido a uma condição no servidor de cache.

* [Pressão de memória no servidor](#memory-pressure-on-the-server)
* [Utilização elevada da CPU / carregamento de servidor](#high-cpu-usage-server-load)
* [Largura de banda do lado servidor excedida](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Pressão de memória no servidor
#### <a name="problem"></a>Problema
Pressão de memória no lado do servidor leva a todos os tipos de problemas de desempenho que podem atrasar o processamento de pedidos. Quando chega a pressão de memória, o sistema, normalmente, tem de dados de página de memória física para virtual memória que está no disco. Isto *página originar falha* faz com que o sistema para significativamente mais lentos. Existem várias causas possíveis deste pressão de memória: 

1. Ter preenchida a cache a capacidade total com dados. 
2. Redis está a ver fragmentação de memória elevada - frequentemente causada por armazenamento de objetos grandes (Redis está otimizada para um pequeno objetos - consulte o [o que é o intervalo de tamanho do valor ideal para redis? 100KB é demasiado grande? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) publique para obter detalhes). 

#### <a name="measurement"></a>Medida
Redis expõe duas métricas que podem ajudar a identificar este problema. O primeiro é `used_memory` e o outro é `used_memory_rss`. [Estas métricas](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) estão disponíveis no Portal do Azure ou através de [Redis informações](http://redis.io/commands/info) comando.

#### <a name="resolution"></a>Resolução
Existem várias alterações possíveis que pode efetuar para ajudar a manter a utilização de memória bom estado de funcionamento:

1. [Configure uma política de memória](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) e definir as horas a expiração nas suas chaves. Tenha em atenção que isto não pode ser suficiente se tiver de fragmentação.
2. [Configura o valor maxmemory-reservado](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) que é suficientemente grande para compensar fragmentação de memória.
3. Divida os seus grandes em cache objetos em mais pequeno objetos relacionados.
4. [Escala](cache-how-to-scale.md) para um maior tamanho da cache.
5. Se estiver a utilizar um [cache premium com o cluster de Redis ativada](cache-how-to-premium-clustering.md) pode [aumentar o número de partições horizontais](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Utilização elevada da CPU / carregamento de servidor
#### <a name="problem"></a>Problema
Utilização elevada da CPU pode significar que no lado do cliente pode não conseguir processar uma resposta de Redis atempadamente, apesar de Redis enviada a resposta muito rapidamente.

#### <a name="measurement"></a>Medida
Monitorize a utilização de CPU Wide sistema através do Portal do Azure ou através do contador de desempenho associado. Seja cuidadoso não monitorizar *processo* CPU porque um único processo pode ter baixa utilização da CPU ao mesmo tempo que geral do sistema CPU pode ser elevada. Procurar picos na utilização da CPU que correspondem com tempos limite.

#### <a name="resolution"></a>Resolução
[Escala](cache-how-to-scale.md) a uma cache de maior escalão com mais capacidade de CPU ou investigar o que está a causar os picos de CPU. 

### <a name="server-side-bandwidth-exceeded"></a>Largura de banda do lado servidor excedida
#### <a name="problem"></a>Problema
Instâncias de tamanho de cache diferente têm limitações quanto largura de banda de rede tem disponíveis. Se o servidor excede a largura de banda disponível, em seguida, dados não serão enviados ao cliente como rapidamente. Isto pode levar a tempos limite.

#### <a name="measurement"></a>Medida
Pode monitorizar o `Cache Read` métrica, que é a quantidade de dados de leitura da cache em Megabytes por segundo (MB/s) durante o intervalo de relatórios especificado. Este valor corresponde à largura de banda de rede utilizada pela cache deste. Se pretender configurar alertas para os limites de largura de banda de rede do lado servidor, pode criá-los utilizando este `Cache Read` contador. Comparar o seu as leituras com os valores existentes na [esta tabela](cache-faq.md#cache-performance) para os limites de largura de banda observado para a cache de várias camadas e tamanhos de preço.

#### <a name="resolution"></a>Resolução
Se for consistentemente quase a largura de banda máxima observada para o tamanho da cache e o escalão preço, considere [dimensionamento](cache-how-to-scale.md) para um escalão de preço ou um tamanho que tem maior largura de banda de rede, utilizando os valores existentes na [esta tabela](cache-faq.md#cache-performance)como guia.

## <a name="stackexchangeredis-timeout-exceptions"></a>Exceções de tempo limite de stackexchange. redis
Stackexchange. redis utiliza uma configuração de definição denominada `synctimeout` para operações síncronas que tem um valor predefinido de 1000 ms. Se uma chamada síncrona não concluída no tempo stipulated, o cliente stackexchange. redis emite um erro de tempo limite semelhante ao seguinte exemplo.

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Esta mensagem de erro contém métricas que podem ajudar a orientar a causa e possíveis resolução do problema. A tabela seguinte contém detalhes sobre as métricas de mensagem de erro.

| Métrica de mensagem de erro | Detalhes |
| --- | --- |
| Inst |No último setor de tempo: 0 comandos foram emitidos |
| Gestor de MP |O Gestor de socket está a efetuar `socket.select` que significa está a pedir o SO para indicar um socket que tenha algo fazer; basicamente: o leitor é não ativamente ao ler a partir da rede porque não considerar a há nada a fazer |
| fila |Existem 73 operações em curso totais |
| qu |6 das operações em curso estão na fila enviada e ainda não foi escrito para a rede de saída |
| Qs |67 operações em curso de putador terem sido enviados para o servidor, mas uma resposta ainda não está disponível. A resposta pode ser `Not yet sent by the server` ou`sent by the server but not yet processed by the client.` |
| QC |0 das operações em curso ter visto responde, mas ainda não foram marcadas como concluídas devido a aguardar o ciclo de conclusão |
| wR |Há um bytes/activewriters escritor Active Directory (isto é, que não estão a ser ignorados os 6 pedidos não enviados) |
| No |Não existem nenhum leitor de Active Directory e estão disponíveis para ser continue a ler os bytes NIC/activereaders zero bytes |

### <a name="steps-to-investigate"></a>Passos para investigar
1. Como melhor prática Certifique-se de que está a utilizar o seguinte padrão para ligar ao utilizar o cliente stackexchange. redis.

    ```c#
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ````

    Para obter mais informações, consulte [ligar à cache a utilizar o stackexchange. redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Certifique-se de que a Cache de Redis do Azure e a aplicação de cliente estão na mesma região no Azure. Por exemplo, poderá ser obter tempos limite quando a cache está nos EUA leste, mas o cliente está nos EUA oeste e o pedido não concluir dentro do `synctimeout` intervalo ou poderá ser obter tempos limite quando está a depurar a partir do seu computador de desenvolvimento local. 
   
    É altamente recomendado para que a cache e no cliente na mesma região do Azure. Se tiver um cenário que inclui entre várias chamadas, deverá definir o `synctimeout` intervalo para um valor superior ao intervalo de 1000 ms predefinido, incluindo um `synctimeout` propriedade na cadeia de ligação. O exemplo seguinte mostra um fragmento de cadeia de ligação do stackexchange. redis cache com um `synctimeout` de 2000 ms.
   
        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
2. Certifique-se de que a versão mais recente do [pacote NuGet stackexchange. redis](https://www.nuget.org/packages/StackExchange.Redis/). Existem erros constantemente fixos no código para tornar mais robusto para tempos limite, para que ter a versão mais recente é importante.
3. Se existirem pedidos que estão a obter vinculados por limitações de largura de banda no servidor ou cliente, irá demorar mais longo para os mesmos seja concluída e, deste modo, provocar tempos limite. Para ver se o tempo limite é devido a largura de banda de rede no servidor, consulte [largura de banda do lado servidor excedida](#server-side-bandwidth-exceeded). Para ver se o tempo limite é devido a largura de banda de rede de cliente, consulte [largura de banda do lado cliente excedida](#client-side-bandwidth-exceeded).
4. A obtenção da CPU vinculada no servidor ou no cliente?
   
   * Verifique se que está a obter vinculado à CPU no cliente que pode provocar o pedido não fosse processada dentro de `synctimeout` intervalo, fazendo com que um tempo limite. Mover para um tamanho maior de cliente ou distribuir a carga pode ajudar a controla este procedimento. 
   * Verifique se está a obter CPU vinculado no servidor através da monitorização de `CPU` [métrica de desempenho da cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Pedidos futuras do enquanto Redis está vinculado de CPU podem provocar esses pedidos para o tempo limite. Para resolver isto pode distribuir a carga em várias shards cache premium ou atualizar para um tamanho maior ou um escalão de preço. Para obter mais informações, consulte [excedido de largura de banda do lado do servidor](#server-side-bandwidth-exceeded).
5. Existem comandos demorar muito tempo a processar no servidor? Execução longa comandos que estão a demorar muito tempo a processar no servidor de redis, pode provocar tempos limite. Alguns exemplos de comandos de execução longa são `mget` com grandes quantidades de chaves, `keys *` ou mal escrito lua scripts. Pode ligar à sua instância da Cache de Redis do Azure utilizando o cliente redis-cli ou utilizar o [consola Redis](cache-configure.md#redis-console) e execute o [SlowLog](http://redis.io/commands/slowlog) comando para ver se existem pedidos demorar mais do que o esperado. Servidor de redis e stackexchange. redis estão otimizados para pequeno número de pedidos, em vez de menos pedidos grandes. Dividir os dados em segmentos mais pequenos, pode melhorar coisas aqui. 
   
    Para obter informações sobre a ligação ao ponto final do SSL de Cache de Redis do Azure utilizando a cli de redis e stunnel, consulte o [anunciar ASP.NET sessão fornecedor de estado para a versão de pré-visualização Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogue. Para obter mais informações, consulte [SlowLog](http://redis.io/commands/slowlog).
6. Elevada carga de servidor de Redis pode provocar tempos limite. Pode monitorizar a carga de servidor através da monitorização de `Redis Server Load` [métrica de desempenho da cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Uma carga do servidor de 100 (valor máximo) significa que o servidor de redis foi ocupado, com sem tempo de inatividade, processamento de pedidos. Para ver se determinados pedidos ocupar todas a capacidade de servidor, execute o comando de SlowLog, conforme descrito no parágrafo anterior. Para obter mais informações, consulte [utilização elevada da CPU / servidor carregar](#high-cpu-usage-server-load).
7. Houve quaisquer outros eventos do lado do cliente que pode ter causado blip uma rede? No cliente (web, função de trabalho ou uma VM do Iaas), verifique se Ocorreu um evento como aumentar o número de instâncias de cliente ou para baixo, ou implementar uma nova versão do cliente ou dimensionamento automático está ativado? No nosso teste que ter Descobrimos que dimensionamento automático ou dimensionamento para cima/para baixo pode fazer com que pode ser perdida conectividade de rede de saída para vários segundos. Código de stackexchange. redis é resiliente para esses eventos e irá voltar a ligar. Durante este tempo de ligação a quaisquer pedidos na fila podem tempo limite.
8. Houve um grande pedido anterior vários pedidos pequenos para a Cache de Redis que excedeu o tempo? O parâmetro `qs` o erro mensagem indica o número de pedidos foram enviado do cliente para o servidor, mas ainda não processados uma resposta. Este valor pode continuam a crescer porque stackexchange. redis utiliza uma única ligação de TCP e só pode ler uma resposta a uma hora. Apesar da primeira operação excedeu o tempo limite, não a interromperá se os dados que está a ser enviados do servidor e outros pedidos são bloqueados até que esta esteja concluída, fazendo com que outs de tempo. É uma solução minimizar a possibilidade de tempos limite de se certificar de que a cache é suficientemente grande para a sua carga de trabalho e a divisão de valores grandes em segmentos mais pequenos. Outra solução possível é utilizar um agrupamento de `ConnectionMultiplexer` objetos no seu cliente e, escolha menos carregado `ConnectionMultiplexer` ao enviar um pedido de novo. Isto deve impedir que um tempo limite único fazendo com que outros pedidos para também o tempo limite.
9. Se estiver a utilizar `RedisSessionStateprovider`, certifique-se de que definiu corretamente o tempo limite de repetição. `retrytimeoutInMilliseconds`deve ser superior ao `operationTimeoutinMilliseonds`, caso contrário, ocorrerá não repetições. No exemplo seguinte `retrytimeoutInMilliseconds` está definido como 3000. Para obter mais informações, consulte [fornecedor de estado de sessão do ASP.NET para a Cache de Redis do Azure](cache-aspnet-session-state-provider.md) e [como utilizar os parâmetros de configuração do fornecedor de estado de sessão e fornecedor de Cache de saída](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />


1. Verifique a utilização de memória no servidor de Cache de Redis do Azure por [monitorização](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` e `Used Memory`. Se uma política de expulsão está no local, Redis começa expulsa as chaves quando `Used_Memory` atinge o tamanho da cache. Idealmente, `Used Memory RSS` deve apenas ser ligeiramente superior ao `Used memory`. Uma grande diferença significa que existe fragmentação de memória (internos ou externos. Quando `Used Memory RSS` é inferior a `Used Memory`, significa que parte da memória cache tem foram alternado pelo sistema operativo. Se isto ocorrer, que pode contar com algumas latências significativas. Porque Redis não têm controlo sobre como as respetivas alocações são mapeadas para páginas de memória elevadas `Used Memory RSS` é, muitas vezes, o resultado de um pico de pedidos de utilização de memória. Quando Redis liberta memória, a memória é dado de volta para o alocador e o alocador poderá ou poderá não retornar a memória novamente no sistema. Pode existir uma discrepância entre o `Used Memory` consumo de memória e o valor conforme comunicado pelo sistema operativo. Pode ser devido ao facto da memória foi utilizada e lançada pelo Redis, mas não fornecida anterior para o sistema. Para ajudar a mitigar os problemas de memória pode executar os seguintes passos.
   
   * Atualize a cache para um tamanho maior para que não estão em execução com limitações de memória no sistema.
   * Definir as horas a expiração nas chaves para que os valores mais antigos são proativamente expulso.
   * Monitor do `used_memory_rss` métrica da cache. Quando este valor se aproxima o tamanho da respetiva cache, o que é provável que começar a ver problemas de desempenho. Distribua os dados através de várias partições horizontais se estiver a utilizar uma cache premium ou atualizar para um maior tamanho da cache.
   
   Para obter mais informações, consulte [pressão de memória no servidor](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Informações adicionais
* [Que tamanho e oferta da Cache de Redis devo utilizar? (What Redis Cache offering and size should I use?)](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)
* [Como pode avaliar e testar o desempenho da minha cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Como pode executar os comandos de Redis?](cache-faq.md#how-can-i-run-redis-commands)
* [Como monitorizar a Cache de Redis do Azure](cache-how-to-monitor.md)

