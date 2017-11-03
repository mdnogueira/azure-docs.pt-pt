---
title: Cache de Redis do Azure FAQ | Microsoft Docs
description: "Aprender as respostas a perguntas comuns, padrões e melhores práticas para a Cache de Redis do Azure"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: c2c52b7d-b2d1-433a-b635-c20180e5cab2
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: sdanie
ms.openlocfilehash: dcabdb789489af1996276d8838afde410473738d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-redis-cache-faq"></a>FAQ da Cache de Redis do Azure
Saiba as respostas a perguntas comuns, padrões e melhores práticas para a Cache de Redis do Azure.

## <a name="what-if-my-question-isnt-answered-here"></a>E se a minha pergunta não é atendida aqui?
Se a sua pergunta não está listada aqui, informe-nos e vamos ajudá-lo a encontrar uma resposta.

* Pode colocar uma pergunta nos comentários no fim destas perguntas mais frequentes e interagir com a equipa de Cache do Azure e outros membros da Comunidade sobre neste artigo.
* Para aceder uma vasta público-alvo, pode publique uma pergunta no [fórum MSDN do Azure Cache](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) e interagir com a equipa de Cache do Azure e outros membros da Comunidade.
* Se pretender efetuar um pedido de funcionalidade, pode submeter os pedidos e ideias para [voz do utilizador do Azure Redis Cache](https://feedback.azure.com/forums/169382-cache).
* Também pode enviar uma mensagem de e-mail para-nos [comentários externos do Azure Cache](mailto:azurecache@microsoft.com).

## <a name="azure-redis-cache-basics"></a>Noções básicas de Cache de Redis do Azure
As perguntas mais frequentes esta secção abrangem alguns das noções básicas da Cache de Redis do Azure.

* [O que é a Cache de Redis do Azure?](#what-is-azure-redis-cache)
* [Como pode começar a utilizar a Cache de Redis do Azure?](#how-can-i-get-started-with-azure-redis-cache)

As perguntas mais frequentes seguintes abrangem os conceitos básicos e perguntas sobre a Cache de Redis do Azure e são respondidas numa das outras secções FAQ.

* [Que tamanho e oferta da Cache de Redis devo utilizar? (What Redis Cache offering and size should I use?)](#what-redis-cache-offering-and-size-should-i-use)
* [Os clientes de cache de Redis devo utilizar?](#what-redis-cache-clients-can-i-use)
* [Existe um emulador local para a Cache de Redis do Azure?](#is-there-a-local-emulator-for-azure-redis-cache)
* [Como monitorizar o estado de funcionamento e desempenho da minha cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Perguntas mais frequentes sobre planeamento
* [Que tamanho e oferta da Cache de Redis devo utilizar? (What Redis Cache offering and size should I use?)](#what-redis-cache-offering-and-size-should-i-use)
* [Desempenho de Cache de Redis do Azure](#azure-redis-cache-performance)
* [Que região de localizar os meus cache?](#in-what-region-should-i-locate-my-cache)
* [Como estou cobrado para a Cache de Redis do Azure?](#how-am-i-billed-for-azure-redis-cache)
* [Pode utilizar a Cache de Redis do Azure com o Azure Government Cloud, Azure China Cloud ou Datacenters do Microsoft Azure?](#can-i-use-azure-redis-cache-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Perguntas mais frequentes de desenvolvimento
* [O que fazer as opções de configuração stackexchange. redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Os clientes de cache de Redis devo utilizar?](#what-redis-cache-clients-can-i-use)
* [Existe um emulador local para a Cache de Redis do Azure?](#is-there-a-local-emulator-for-azure-redis-cache)
* [Como pode executar os comandos de Redis?](#how-can-i-run-redis-commands)
* [Por que motivo é que a Cache de Redis do Azure não tem uma referência da biblioteca MSDN classe como alguns dos outros serviços do Azure?](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Pode utilizar a Cache de Redis do Azure como uma cache de sessão do PHP?](#can-i-use-azure-redis-cache-as-a-php-session-cache)
* [Quais são as bases de dados de Redis?](#what-are-redis-databases)

## <a name="security-faqs"></a>Perguntas mais frequentes de segurança
* [Quando deve a ativar a porta não SSL para ligar ao Redis?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Perguntas mais frequentes de produção
* [Quais são algumas melhores práticas de produção?](#what-are-some-production-best-practices)
* [Quais são algumas das considerações sobre quando utilizar comandos de Redis comuns?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Como pode avaliar e testar o desempenho da minha cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Detalhes importantes sobre ThreadPool crescimento](#important-details-about-threadpool-growth)
* [Ativar GC do servidor obter mais débito no cliente ao utilizar o stackexchange. redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Considerações de desempenho em torno de ligações](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Monitorização e resolução de problemas de perguntas mais frequentes
As perguntas mais frequentes nesta secção incluem monitorização e resolução de problemas de perguntas comuns. Para obter mais informações sobre monitorização e resolução de problemas as instâncias de Cache de Redis do Azure, consulte [como monitorizar a Cache de Redis do Azure](cache-how-to-monitor.md) e [como resolver problemas com a Cache de Redis do Azure](cache-how-to-troubleshoot.md).

* [Como monitorizar o estado de funcionamento e desempenho da minha cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Por que razão estou a ver os tempos limite?](#why-am-i-seeing-timeouts)
* [Por que motivo o meu cliente foi desligado da cache?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Perguntas mais frequentes de oferta de Cache anterior
* [Que oferta de Cache do Azure é mais adequada para mim?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-redis-cache"></a>O que é a Cache de Redis do Azure?
Cache de Redis do Azure baseia-se de open source popular [a cache de Redis](http://redis.io). Proporciona acesso para uma segura, dedicada cache de Redis e gerida pela Microsoft e acessível a partir de qualquer aplicação no Azure. Para obter uma descrição mais detalhada, consulte o [a Cache de Redis do Azure](https://azure.microsoft.com/services/cache/) página de produto no Azure.com.

### <a name="how-can-i-get-started-with-azure-redis-cache"></a>Como pode começar a utilizar a Cache de Redis do Azure?
Existem várias formas, que pode começar a utilizar a cache de Redis do Azure.

* Pode consultar um dos nossos tutoriais disponíveis para [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md), e [Python](cache-python-get-started.md).
* Pode ver [como compilar elevado desempenho aplicações utilizando o Microsoft Azure Redis Cache](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Pode consultar a documentação do cliente para os clientes que correspondem a linguagem de programação do seu projeto para ver como utilizar o Redis. Existem muitos clientes de Redis que podem ser utilizados com a Cache de Redis do Azure. Para obter uma lista de clientes de Redis, consulte [http://redis.io/clients](http://redis.io/clients).

Se ainda não tiver uma conta do Azure, pode:

* [Criar uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Receberá créditos que podem ser utilizados para experimentar os serviços pagos do Azure. Mesmo depois de gastar todos os créditos, pode manter a conta e utilizar os serviços e recursos gratuitos do Azure.
* [Ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). A sua subscrição do MSDN dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure.

<a name="cache-size"></a>

### <a name="what-redis-cache-offering-and-size-should-i-use"></a>Que tamanho e oferta de Cache da Redis devo utilizar?
Cada oferta de Cache de Redis do Azure fornece diferentes níveis de **tamanho**, **largura de banda**, **elevada disponibilidade**, e **SLA** opções.

As seguintes são considerações para escolher uma oferta de Cache.

* **Memória**: camadas o básico e padrão oferecem 250 MB – 53 GB. O escalão Premium oferece 530 GB. Para obter mais informações, consulte [preços da Cache de Redis do Azure](https://azure.microsoft.com/pricing/details/cache/).
* **Desempenho de rede**: Se tiver uma carga de trabalho que necessita de débito elevado, o escalão Premium oferece mais largura de banda em comparação comparada Standard ou básico. Em cada camada superiores de tamanho de caches tem também mais largura de banda devido a VM subjacente que aloja a cache. Consulte o [seguintes tabela](#cache-performance) para obter mais informações.
* **Débito**: escalão Premium o oferece o débito máximo disponível. Se o cliente ou servidor de cache atingir os limites de largura de banda, poderá receber tempos limite do lado do cliente. Para obter mais informações, consulte a tabela seguinte.
* **Elevada disponibilidade/SLA**: a Cache de Redis do Azure garante que uma cache de padrão/Premium está disponível, pelo menos, 99,9% do tempo. Para saber mais sobre o nosso SLA, consulte [preços da Cache de Redis do Azure](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). O SLA abrange apenas a conectividade para os pontos finais de Cache. O SLA não engloba a proteção contra perda de dados. Recomendamos que utilize a funcionalidade de persistência de dados de Redis no escalão Premium para aumentar a resistência contra a perda de dados.
* **A persistência de dados de redis**: escalão Premium o permite-lhe manter os dados da cache numa conta do Storage do Azure. Cache Basic/Standard, todos os dados são armazenados apenas na memória. Se existirem problemas de infraestrutura subjacente existe podem ser potencial perda de dados. Recomendamos que utilize a funcionalidade de persistência de dados de Redis no escalão Premium para aumentar a resistência contra a perda de dados. Cache de Redis do Azure oferece RDB e AOF (brevemente) opções no persistência de Redis. Para obter mais informações, consulte [como configurar a persistência para uma Cache de Redis do Azure Premium](cache-how-to-premium-persistence.md).
* **Cluster de redis**: criar caches superiores a 53 GB ou para dividir os dados em vários nós de Redis, pode utilizar clustering de Redis, que está disponível no escalão Premium. Cada nó é composta por um par de cache primário/réplica para elevada disponibilidade. Para obter mais informações, veja [Como configurar o clustering de uma Cache de Redis do Azure Premium](cache-how-to-premium-clustering.md).
* **Melhorado isolamento de rede e segurança**: implementação de rede Virtual do Azure (VNET) proporciona maior segurança e isolamento para a Cache de Redis do Azure, bem como sub-redes, políticas de controlo de acesso e outras funcionalidades adicionais para restringem o acesso. Para obter mais informações, veja [Como configurar o suporte da Rede Virtual de uma Cache de Redis do Azure Premium](cache-how-to-premium-vnet.md).
* **Configure Redis**: em camadas Standard e Premium, pode configurar Redis para notificações Keyspace.
* **Número máximo de ligações de cliente**: escalão Premium o oferece o número máximo de clientes que possam ligar à Redis, com um número mais alto de ligações de caches de tamanho maior. Para obter mais informações, consulte [preços da Cache de Redis do Azure](https://azure.microsoft.com/pricing/details/cache/).
* **Dedicado núcleo para o servidor de Redis**: o escalão Premium, todos os tamanhos de cache de ter um núcleo dedicado para Redis. Os escalões básico/padrão, o tamanho de C1 em acima tem um núcleo dedicado para o servidor de Redis.
* **Redis é o único thread** , de modo a ter mais de dois núcleos não fornece vantagem adicional com apenas dois núcleos, mas os tamanhos de VM maiores têm normalmente mais largura de banda que tamanhos mais pequenos. Se o cliente ou servidor de cache atingir os limites de largura de banda, em seguida, receberá tempos limite do lado do cliente.
* **Melhorias de desempenho**: Caches no escalão Premium são implementadas no hardware que tenha processadores mais rápidos, fornecer um melhor desempenho em comparação comparado o escalão básico ou padrão. Caches de escalão Premium têm um maior débito e latências inferiores.

<a name="cache-performance"></a>

### <a name="azure-redis-cache-performance"></a>Desempenho de Cache de Redis do Azure
A tabela seguinte mostra os valores de largura de banda máxima observados ao testar vários tamanhos de Standard e Premium coloca em cache utilizando `redis-benchmark.exe` de uma VM do Iaas relativamente ao ponto final a Cache de Redis do Azure. 

>[!NOTE] 
>Não são garantidos que estes valores e não existe nenhum SLA para estes números, mas deve ser típica. Deve carregar testar a sua própria aplicação para determinar o tamanho da cache de direita para a sua aplicação.
>
>

Esta tabela, iremos pode desenhar os conclusões seguintes:

* Débito para as caches que são o mesmo tamanho for superior no escalão Premium em comparação com o escalão Standard. Por exemplo, com uma Cache de 6 GB, o débito de P1 é 180,000 RPS em comparação com 49,000 para C3.
* Com Redis clustering, débito de forma linear aumenta como aumentar o número de partições horizontais (nós) no cluster. Por exemplo, se criar um cluster de P4 de 10 shards, em seguida, o débito disponível é 400,000 * 10 = milhões de 4 RPS.
* Débito para tamanhos de chaves superior é superior no escalão Premium em comparação com o escalão Standard.

| Escalão de preço | Tamanho | Núcleos de CPU | Largura de banda disponível | Tamanho do valor de 1 KB |
| --- | --- | --- | --- | --- |
| **Tamanhos de cache padrão** | | |**Megabits por segundo (Mb/s) / Megabytes por segundo (MB/s)** |**Pedidos por segundo (RPS)** |
| C0 |250 MB |Partilhado |5 / 0.625 |600 |
| C1 |1 GB |1 |100 / 12.5 |12,200 |
| C2 |2,5 GB |2 |200 / 25 |24,000 |
| C3 |6 GB |4 |400 / 50 |49,000 |
| C4 |13 GB |2 |500 / 62.5 |61,000 |
| C5 |26 GB |4 |1,000 / 125 |115,000 |
| C6 |53 GB |8 |2,000 / 250 |150,000 |
| **Tamanhos de cache Premium** | |**Núcleos de CPU por ID de partição horizontal** | **Megabits por segundo (Mb/s) / Megabytes por segundo (MB/s)** |**Pedidos por segundo (RPS), por ID de partição horizontal** |
| P1 |6 GB |2 |1,500 / 187.5 |180,000 |
| P2 |13 GB |4 |3,000 / 375 |360,000 |
| P3 |26 GB |4 |3,000 / 375 |360,000 |
| P4 |53 GB |8 |6,000 / 750 |400,000 |

Para obter instruções sobre como a transferir as ferramentas de Redis `redis-benchmark.exe`, consulte o [como executar comandos de Redis?](#cache-commands) secção.

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>Que região de localizar os meus cache?
Para obter o melhor desempenho e a latência mais baixa, localize a Cache de Redis do Azure na mesma região que a aplicação de cliente de cache.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-redis-cache"></a>Como estou cobrado para a Cache de Redis do Azure?
Preços da Cache de Redis do Azure é [aqui](https://azure.microsoft.com/pricing/details/cache/). A página de preços apresenta uma lista de preços como uma taxa por hora. Caches são faturadas numa base por minuto desde o momento em que a cache é criada até o momento em que uma cache é eliminada. Não há nenhuma opção para parar ou colocar em pausa a faturação de uma cache.

### <a name="can-i-use-azure-redis-cache-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Pode utilizar a Cache de Redis do Azure com o Azure Government Cloud, Azure China Cloud ou Datacenters do Microsoft Azure?
Sim, a Cache de Redis do Azure está disponível na nuvem do Azure Government, Azure China nuvem e na Alemanha do Microsoft Azure. Os URLs para aceder e gerir a Cache de Redis do Azure são diferentes destas nuvens comparadas com a nuvem pública do Azure. 

| Nuvem   | Sufixo DNS de Redis            |
|---------|---------------------------------|
| Público  | *. redis.cache.windows.net       |
| Governo dos EUA  | *. redis.cache.usgovcloudapi.net |
| Alemanha | *. redis.cache.cloudapi.de       |
| China   | *. redis.cache.chinacloudapi.cn  |

Para obter mais informações sobre considerações quando utilizar a Cache de Redis do Azure com outras nuvens, consulte as hiperligações seguintes.

- [Bases de dados do Azure Government - Cache de Redis do Azure](../azure-government/documentation-government-services-database.md#azure-redis-cache)
- [Nuvem do Azure China - Cache de Redis do Azure](https://www.azure.cn/documentation/services/redis-cache/)
- [Datacenters de Microsoft Azure](https://azure.microsoft.com/overview/clouds/germany/)

Para obter informações sobre como utilizar a Cache de Redis do Azure com o PowerShell na nuvem do Azure Government, Azure China nuvem e na Alemanha do Microsoft Azure, consulte [como ligar a outras nuvens - PowerShell de Cache de Redis do Azure](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>O que fazer as opções de configuração stackexchange. redis?
Stackexchange. redis tem muitas opções. Esta secção aborda-se algumas das definições comuns. Para obter mais informações sobre as opções de stackexchange. redis, consulte [configuração stackexchange. redis](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Descrição | Recomendação |
| --- | --- | --- |
| AbortOnConnectFail |Quando definido como VERDADEIRO, a ligação será não restabelece ligação após uma falha de rede. |Definido como false e permita que stackexchange. redis voltar a ligar automaticamente. |
| ConnectRetry |O número de vezes para repetir as tentativas de ligação durante inicial ligar. |Consulte as notas seguintes para obter orientações. |
| ConnectTimeout |Tempo limite em ms para estabelecer a ligação de operações. |Consulte as notas seguintes para obter orientações. |

Normalmente, os valores predefinidos do cliente são suficientes. Pode ajustar as opções, com base na sua carga de trabalho.

* **Repetições**
  * ConnectRetry e ConnectTimeout, as orientações gerais é falhar rápido e tente novamente. Esta orientação é com base na sua carga de trabalho e quanto tempo no médio demora para o cliente emitir um comando de Redis e receber uma resposta.
  * Permita stackexchange. redis restabelece ligação automaticamente em vez de a verificar o estado de ligação e restabelecer a ligação por si. **Evitar utilizar a propriedade ConnectionMultiplexer.IsConnected**.
  * Snowballing - por vezes, poderá ter um problema onde são repetir e as repetições snowball e nunca a recuperar. Se snowballing ocorrer, deve considerar a utilização de um algoritmo de repetição de término exponencial conforme descrito em [Repita orientações gerais](../best-practices-retry-general.md) publicados pelo grupo de Microsoft Patterns & práticas.
* **Valores de tempo limite**
  * Considere a carga de trabalho e definir os valores em conformidade. Se estiver a armazenar valores grandes, defina o tempo limite para um valor superior.
  * Definir `AbortOnConnectFail` para FALSO e permita que stackexchange. redis restabelecer a ligação para si.
  * Utilize uma única instância de ConnectionMultiplexer para a aplicação. Pode utilizar um LazyConnection para criar uma instância única que é devolvida por uma propriedade de ligação, conforme mostrado no [ligar à cache utilizando a classe de ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Definir o `ConnectionMultiplexer.ClientName` propriedade para um nome exclusivo de instância de aplicação para fins de diagnóstico.
  * Utiliza vários `ConnectionMultiplexer` instâncias para cargas de trabalho personalizadas.
      * Pode seguir este modelo, se tiver carga variando na sua aplicação. Por exemplo:
      * Pode ter um multiplexer para lidar com as chaves de grandes dimensões.
      * Pode ter um multiplexer para lidar com chaves pequenas.
      * Pode definir valores diferentes para tempos limite de ligação e repetir lógica para cada ConnectionMultiplexer que utilizar.
      * Definir o `ClientName` propriedade em cada multiplexer a ajudar no diagnóstico.
      * Esta orientação pode levar a que mais está mais simples de latência por `ConnectionMultiplexer`.

### <a name="what-redis-cache-clients-can-i-use"></a>Os clientes de cache de Redis devo utilizar?
Uma das ações excelente sobre Redis é que existem muitos clientes que suportam várias linguagens de programação diferentes. Para obter uma lista atual de clientes, consulte [Redis clientes](http://redis.io/clients). Para tutoriais que abrangem vários idiomas diferentes e os clientes, consulte [como utilizar a Cache de Redis do Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e clicar no idioma pretendido do alternador de idioma na parte superior do artigo.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-redis-cache"></a>Existe um emulador local para a Cache de Redis do Azure?
Não existe nenhum emulador local para a Cache de Redis do Azure, mas pode executar a versão de MSOpenTech do redis-server.exe do [Redis ferramentas da linha de comandos](https://github.com/MSOpenTech/redis/releases/) no local do computador e ligue à-o para obter uma experiência semelhante para um emulador de local cache conforme mostrado no exemplo seguinte:

    private static Lazy<ConnectionMultiplexer>
          lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });

        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


Opcionalmente, pode configurar um [Conf](http://redis.io/topics/config) ficheiro para corresponder a mais detalhadamente o [predefinições de cache](cache-configure.md#default-redis-server-configuration) para a sua online Azure Redis Cache se assim o desejar.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Como pode executar os comandos de Redis?
Pode utilizar qualquer um dos comandos listados em [Redis comandos](http://redis.io/commands#) , exceto para os comandos listados em [Redis comandos não suportados na Cache de Redis do Azure](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Tem várias opções para executar comandos de Redis.

* Se tiver uma cache Standard ou Premium, pode executar comandos de Redis utilizando o [consola Redis](cache-configure.md#redis-console). A consola de Redis fornece uma forma segura para executar comandos de Redis no portal do Azure.
* Também pode utilizar as ferramentas de linha de comandos do Redis. A utilizá-los, execute os seguintes passos:
* Transferir o [Redis ferramentas da linha de comandos](https://github.com/MSOpenTech/redis/releases/).
* Ligar ao utilizar a cache `redis-cli.exe`. Transmita o ponto final de cache utilizando que o -h mudar e a chave utilizando - a conforme mostrado no exemplo seguinte:
* `redis-cli -h <your cache="" name="">
  .redis.cache.windows.net -a <key>
  `

> [!NOTE]
> As ferramentas de linha de comandos de Redis não funcionam com a porta SSL, mas pode utilizar um utilitário como `stunnel` ligar de forma segura as ferramentas para a porta SSL, seguindo as indicações de [anunciar ASP.NET sessão fornecedor de estado para a pré-visualização de Redis Versão](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogue.
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Por que motivo é que a Cache de Redis do Azure não tem uma referência da biblioteca MSDN classe como alguns dos outros serviços do Azure?
Cache de Redis do Microsoft Azure baseia-se de open source popular Cache de Redis e podem ser acedido por uma grande variedade de [Redis clientes](http://redis.io/clients) para muitas linguagens de programação. Cada cliente tem a suas próprias API que faz com que chamadas para a cache de Redis instância utilizando [Redis comandos](http://redis.io/commands).

Porque cada cliente for diferente, não há não uma referência de classe centralizado no MSDN e cada cliente mantém a sua própria documentação de referência. Para além de documentação de referência, existem várias tutoriais que mostra como começar com idiomas diferentes e os clientes de cache a utilizar a Cache de Redis do Azure. Para aceder a estes tutoriais, consulte [como utilizar a Cache de Redis do Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e clicar no idioma pretendido do alternador de idioma na parte superior do artigo.

### <a name="can-i-use-azure-redis-cache-as-a-php-session-cache"></a>Pode utilizar a Cache de Redis do Azure como uma cache de sessão do PHP?
Sim, para utilizar a Cache de Redis do Azure como uma cache de sessão do PHP, especifique a cadeia de ligação à sua instância da Cache de Redis do Azure no `session.save_path`.

> [!IMPORTANT]
> Ao utilizar a Cache de Redis do Azure como uma cache de sessão do PHP, tem de URL codificar a chave de segurança utilizada para ligar à cache, conforme mostrado no exemplo seguinte:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Se a chave não é URL codificado, poderá receber uma excepção com uma mensagem como:`Failed to parse session.save_path`
>
>

Para obter mais informações sobre como utilizar a Cache de Redis como uma cache de sessão do PHP com o cliente PhpRedis, consulte [processador PHP sessão](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Quais são as bases de dados de Redis?

Bases de dados de redis são apenas uma lógica separação de dados dentro da mesma instância de Redis. A memória da cache é partilhada entre todas as bases de dados e memória real consumo de uma determinada base de dados depende de chaves/valores armazenados nessa base de dados. Por exemplo, uma cache de C6 tem 53 GB de memória. Pode optar por colocar todos os 53 GB para uma base de dados ou pode dividir cópias de segurança entre várias bases de dados. 

> [!NOTE]
> Ao utilizar uma Cache de Redis do Azure Premium com clustering ativada, apenas base de dados 0 está disponível. Esta limitação é uma limitação de Redis intrínseca e não é específica para a Cache de Redis do Azure. Para obter mais informações, consulte [é necessário efetuar quaisquer alterações à minha aplicação de cliente para utilizar clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Quando deve a ativar a porta não SSL para ligar ao Redis?
Redis server não suportam nativamente o SSL, mas não de Cache de Redis do Azure. Se estiver a ligar à Cache de Redis do Azure e o cliente suportar SSL, como stackexchange. redis, em seguida, deve utilizar SSL.

>[!NOTE]
>A porta não SSL está desativada por predefinição para novas instâncias de Cache de Redis do Azure. Se o cliente não suporta SSL, em seguida, tem de ativar a porta não SSL, seguindo as indicações o [aceder às portas](cache-configure.md#access-ports) secção o [configurar uma cache na Cache de Redis do Azure](cache-configure.md) artigo.
>
>

Redis ferramentas, tal como `redis-cli` não funcionam com a porta SSL, mas pode utilizar um utilitário como `stunnel` ligar de forma segura as ferramentas para a porta SSL, seguindo as indicações de [anunciar ASP.NET sessão fornecedor de estado para Redis Versão de pré-visualização](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogue.

Para obter instruções sobre como transferir as ferramentas de Redis, veja o [como executar comandos de Redis?](#cache-commands) secção.

### <a name="what-are-some-production-best-practices"></a>Quais são algumas melhores práticas de produção?
* [Melhores práticas de stackexchange. redis](#stackexchangeredis-best-practices)
* [Conceitos e configuração](#configuration-and-concepts)
* [Teste de desempenho](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Melhores práticas de stackexchange. redis
* Definir `AbortConnect` como false, em seguida, permitem ConnectionMultiplexer voltar a ligar automaticamente. [Consulte aqui para obter detalhes](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Reutilizar o ConnectionMultiplexer - não criar um novo para cada pedido. O `Lazy<ConnectionMultiplexer>` padrão [mostrados aqui](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) é recomendada.
* Redis funciona melhor com valores mais pequenos, por isso considere chopping dos dados de maiores para várias chaves. No [este debate Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb é considerado grande. Leitura [neste artigo](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) para um problema de exemplo que pode ser causado por valores grandes.
* Configurar o seu [ThreadPool definições](#important-details-about-threadpool-growth) para evitar tempos limite.
* Utilize, pelo menos, o connectTimeout predefinida de 5 segundos. Este intervalo seria dar tempo suficiente ao stackexchange. redis para restabelecer a ligação, em caso de um blip de rede.
* Tenha em atenção os custos de desempenho associados operações diferentes, que estão em execução. Por exemplo, o `KEYS` comando é uma operação O(n) e devem ser evitado. O [redis.io site](http://redis.io/commands/) tenha detalhes à volta a complexidade de tempo para cada operação que suporta. Clique em cada comando para ver a complexidade para cada operação.

#### <a name="configuration-and-concepts"></a>Conceitos e configuração
* Utilize Standard ou Premium camada para sistemas de produção. A camada básica é um sistema de nó único com nenhuma replicação de dados e nenhum SLA. Além disso, utilize, pelo menos, uma cache de C1. C0 caches são normalmente utilizadas para cenários de desenvolvimento/teste simples.
* Lembre-se de que o Redis está um **dentro da memória** arquivo de dados. Leitura [neste artigo](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) , de modo a que está ciente dos cenários em que pode ocorrer perda de dados.
* Desenvolver o seu sistema de forma a que pode processar blips ligação [devido a aplicação de patches e ativação pós-falha](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Teste de desempenho
* Iniciar utilizando `redis-benchmark.exe` perceber para débito possíveis antes de escrever o seu próprio desempenho os testes. Porque `redis-benchmark` não suporta SSL, tem de [ativar a porta não SSL através do portal do Azure](cache-configure.md#access-ports) antes de executar o teste. Para obter exemplos, consulte [como posso avaliar e testar o desempenho da minha cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* O cliente VM utilizado para fins de teste deve ser na mesma região que a instância da cache de Redis.
* Recomendamos que utilize Dv2 série de VM para o cliente ter hardware melhor e deve dar os melhores resultados.
* Certifique-se de que o cliente VM que escolher tem, pelo menos, a maior quantidade de computação e capacidade de largura de banda, como a cache estiver a testar.
* Ative o VRSS no computador cliente se forem no Windows. [Consulte aqui para obter detalhes](https://technet.microsoft.com/library/dn383582.aspx).
* Instâncias de Redis do escalão Premium têm melhor rede latência e débito porque estão a executar no hardware melhor para a CPU e da rede.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Quais são algumas das considerações sobre quando utilizar comandos de Redis comuns?
* Não deve ser determinados comandos de Redis que demorar muito tempo a concluir, sem compreender o impacto destes comandos.
  * Por exemplo, não execute o [chaves](http://redis.io/commands/keys) comando na produção, como pode demorar muito tempo a devolver dependendo do número de chaves. Redis é um servidor único thread e processa comandos um cada vez. Se tiver outros comandos emitidos depois de chaves, eles não serão processados até Redis processar o comando de chaves. O [redis.io site](http://redis.io/commands/) tenha detalhes à volta a complexidade de tempo para cada operação que suporta. Clique em cada comando para ver a complexidade para cada operação.
* Os tamanhos de chaves - devo utilizar pequenos/valores de chave ou chaves/valores grandes? Em geral, depende do cenário. Se o seu cenário precisa de chaves de maior, pode ajustar o ConnectionTimeout e repita valores e ajustar a lógica de repetição. De uma perspetiva de servidor de Redis, os valores mais pequenos são respeitados ter um melhor desempenho.
* Estas considerações não significam que não é possível armazenar valores maiores na Redis; tem de ser compatível com as seguintes considerações. As latências será superiores. Se tiver um conjunto de dados que são maiores e outro que é mais pequeno, pode utilizar várias instâncias de ConnectionMultiplexer, cada um deles configurado com um conjunto diferente de valores de tempo limite e tente novamente, conforme descrito na anterior [o que fazer o stackexchange. redis Opções de configuração fazer](#cache-configuration) secção.

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Como pode avaliar e testar o desempenho da minha cache?
* [Ativar o diagnóstico da cache](cache-how-to-monitor.md#enable-cache-diagnostics) para poder [monitorizar](cache-how-to-monitor.md) o estado de funcionamento da cache. Pode ver as métricas no portal do Azure e [transferi-las e revê-las](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) com as ferramentas da sua escolha.
* Pode utilizar redis benchmark.exe para teste de carga do servidor de Redis.
* Certifique-se de que a carga de testar o cliente e a cache de Redis estão na mesma região.
* Utilize o redis cli.exe e monitorizar a cache utilizando o comando de informações.
* Se a carga está a causar a fragmentação de memória elevada, deve aumentar verticalmente para um maior tamanho da cache.
* Para obter instruções sobre como transferir as ferramentas de Redis, veja o [como executar comandos de Redis?](#cache-commands) secção.

Os comandos seguintes fornecem um exemplo de utilização redis benchmark.exe. Para obter resultados precisos, execute estes comandos de uma VM na mesma região que a cache.

* Pedidos de encaminhados definido de teste utilizando um payload de k 1

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Teste encaminhados obter pedidos utilizando um payload de k de 1.
  Nota: Executar o conjunto de teste mostrado acima primeiro para povoar a cache

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Detalhes importantes sobre ThreadPool crescimento
O ThreadPool CLR tem dois tipos de threads - "Worker" e "Porta de conclusão e/s" (também conhecido como IOCP) threads.

* Threads de trabalho são utilizadas quando coisas como processamento `Task.Run(…)` ou `ThreadPool.QueueUserWorkItem(…)` métodos. Estes threads também são utilizadas por vários componentes no CLR trabalho tem de acontecer num thread em segundo plano.
* Threads IOCP são utilizadas quando acontece de e/s assíncrona (por exemplo, ao ler a partir da rede).

O conjunto de threads fornece novos threads de trabalho ou de threads de conclusão de e/s a pedido (sem qualquer limitação) até atingir a definição de "Mínimo" para cada tipo de thread. Por predefinição, o número mínimo de threads está definido para o número de processadores num sistema.

Depois do número de threads (ocupados) existentes pedidos com êxito o número de threads "mínimo", o ThreadPool irá limitar a velocidade a que este injects threads novos a um thread por 500 milissegundos. Normalmente, se o sistema obtém uma rajada de trabalho que necessita de um thread IOCP, processará que funcionam muito rapidamente. No entanto, se rajada de trabalho é maior do que a definição de "Mínimo" configurada, haverá algum atraso na parte do trabalho de processamento como o ThreadPool aguarda que um dos dois aspetos a acontecer.

1. Um thread existente fique livre para processar o trabalho.
2. Nenhum thread existente fique livre para 500ms, pelo que é criado um novo thread.

Basicamente, significa que quando o número de threads ocupados é superior ao mínimo threads, provavelmente pagar com um atraso de 500ms antes do tráfego de rede é processado pela aplicação. Além disso, é importante salientar que, quando um thread existente permanece inativo durante um período superior a 15 segundos (baseados no posso não se esqueça), serão limpos e pode repetir este ciclo de crescimento e shrinkage.

Se vamos ver uma mensagem de erro de exemplo do stackexchange. redis (criar 1.0.450 ou posterior), verá que agora imprime estatísticas ThreadPool (consulte IOCP e de trabalho os detalhes abaixo).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

No exemplo anterior, pode ver que para o thread IOCP existem 6 threads ocupados e o sistema está configurado para permitir 4 threads mínimos. Neste caso, o cliente iria ter provavelmente visto duas 500 ms atrasos porque 6 > 4.

Tenha em atenção que o stackexchange. redis pode atingir tempos limite se crescimento de threads IOCP ou WORKER obtém limitado.

### <a name="recommendation"></a>Recomendação
Tendo em conta estas informações, recomendamos vivamente que os clientes defina o valor de configuração mínima de threads IOCP e de trabalho para algo maior do que o valor predefinido. Iremos não é possível dar uniformes orientações sobre o que este valor deve ser porque o valor da direita para uma aplicação irá ser demasiado alta/baixa para outra aplicação. Esta definição também pode afetar o desempenho de outras partes do aplicações complicadas, para que cada cliente necessita de otimizar esta definição para as suas necessidades específicas. Um bom local inicial 200 ou 300, em seguida, testar e otimizar conforme necessário.

Como configurar esta definição:

* No ASP.NET, utilize o [definição de configuração "minIoThreads"] [ "minIoThreads" configuration setting] sob o `<processModel>` elemento de configuração em Web. config. Se estiver a executar no interior de Web sites do Azure, esta definição não está exposta através de opções de configuração. No entanto, deve ainda poderá configurar esta definição através de programação (ver abaixo) do seu método de Application_Start asax.

  > [!NOTE] 
  > O valor especificado neste elemento de configuração é um *por núcleo* definição. Por exemplo, se tiver uma máquina 4 core e pretende que a definição de minIOThreads ser 200 no tempo de execução, teria de utilizar `<processModel minIoThreads="50"/>`.
  >

* Fora do ASP.NET, utilize o [ThreadPool.SetMinThreads(...) ](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx) API.

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Ativar GC do servidor obter mais débito no cliente ao utilizar o stackexchange. redis
Ativar servidor GC pode otimizar o cliente e fornecer um melhor desempenho e débito ao utilizar o stackexchange. redis. Para obter mais informações sobre o servidor GC e como ativá-la, consulte os artigos seguintes:

* [Para ativar a GC do servidor](https://msdn.microsoft.com/library/ms229357.aspx)
* [Noções básicas sobre a recolha de lixo](https://msdn.microsoft.com/library/ee787088.aspx)
* [Libertação da memória e desempenho](https://msdn.microsoft.com/library/ee851764.aspx)


### <a name="performance-considerations-around-connections"></a>Considerações de desempenho em torno de ligações

Cada escalão de preço tem limites diferentes para ligações de cliente, memória e largura de banda. Enquanto cada tamanho da cache do permite *até* um determinado número de ligações, cada ligação Redis sobrecarga associado ao mesmo. Um exemplo deste tipo sobrecarga seria a utilização da memória e CPU devido a encriptação de TLS/SSL. O limite máximo de ligação para um tamanho de cache fornecido assume uma cache ligeiramente carregada. Se carregar de sobrecarga de ligação *plus* carga de operações do cliente excede a capacidade para o sistema, a cache pode ter problemas de capacidade, mesmo que não excedeu o limite de ligação para o tamanho atual da cache.

Para obter mais informações sobre os limites de ligações diferentes para cada camada, consulte [preços da Cache de Redis do Azure](https://azure.microsoft.com/pricing/details/cache/). Para obter mais informações sobre as ligações e outras configurações predefinidas, consulte [configuração do servidor de predefinição Redis](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Como monitorizar o estado de funcionamento e desempenho da minha cache?
Instâncias de Cache de Redis do Microsoft Azure podem ser monitorizadas no [portal do Azure](https://portal.azure.com). Pode ver as métricas, afixar gráficos de métricas ao Startboard, personalizar o intervalo de data e hora de monitorização de gráficos, adicionar e remover as métricas de gráficos e definir alertas quando forem cumpridas determinadas condições. Para obter mais informações, consulte [a Cache de Redis do Azure do Monitor](cache-how-to-monitor.md).

A Cache de Redis **menu recursos** contém também várias ferramentas de monitorização e resolução de problemas as caches.

* **Diagnosticar e resolver problemas** fornece informações sobre problemas comuns e de estratégias para resolvê-los.
* **Estado de funcionamento do recurso** monitoriza os seus recursos e indica o se estiver em execução conforme esperado. Para obter mais informações sobre o serviço de estado de funcionamento de recursos do Azure, consulte [descrição geral do Estado de funcionamento de recursos do Azure](../resource-health/resource-health-overview.md).
* **Novo pedido de suporte** fornece opções para abrir um pedido de suporte para a sua cache.

Estas ferramentas permitem-lhe monitorizar o estado de funcionamento das suas instâncias de Cache de Redis do Azure e ajudar a gerir as suas aplicações de colocação em cache. Para obter mais informações, consulte a secção "Suporte definições de & resolução de problemas" [como configurar a Cache de Redis do Azure](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Por que razão estou a ver os tempos limite?
Tempos limite acontecer no cliente que utiliza para comunicar com Redis. Quando um comando é enviado para o servidor de Redis, o comando é colocada na fila cópias de segurança e o servidor de Redis, eventualmente, escolherá o comando e executa-lo. No entanto o cliente pode tempo limite durante este processo e se existir uma exceção é gerado no lado de chamada. Para obter mais informações sobre como resolver problemas de limite de tempo, consulte [resolução de problemas do lado do cliente](cache-how-to-troubleshoot.md#client-side-troubleshooting) e [exceções de tempo limite de stackexchange. redis](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Por que motivo o meu cliente foi desligado da cache?
Seguem-se algumas motivo comum para desligar uma cache.

* Causas do lado do cliente
  * A aplicação de cliente foi implementada novamente.
  * A aplicação cliente efetuar uma operação de dimensionamento.
    * No caso de serviços em nuvem ou as aplicações Web, isto pode dever-se dimensionamento automático.
  * Alterar a camada de rede do lado do cliente.
  * Ocorreram erros transitórios no cliente ou em nós de rede entre o cliente e o servidor.
  * Foram atingidos os limites de limiar de largura de banda.
  * CPU vinculado operações demorou demasiado tempo a concluir.
* Causas do lado do servidor
  * Na cache padrão oferta, o serviço de Cache de Redis do Azure iniciada uma ativação pós-falha de nó principal para o nó secundário.
  * Azure foi aplicação de patches a instância em que a cache foi implementada
    * Isto pode ser para atualizações do servidor de Redis ou a manutenção geral de VM.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Qual das ofertas da Cache do Azure é melhor para mim?
> [!IMPORTANT]
> De acordo com o último ano [anúncio](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/), serviço de serviço de Cache gerida do Azure e a Cache de função do Azure **tenha sido extinto** em 30 de Novembro de 2016. A nossa recomendação é utilizar [a Cache de Redis do Azure](https://azure.microsoft.com/services/cache/). Para obter informações sobre a migração, consulte [migrar do serviço de Cache gerida para a Cache de Redis do Azure](cache-migrate-to-redis.md).
>
>

### <a name="azure-redis-cache"></a>Cache de Redis do Azure
Cache de Redis do Azure está normalmente disponível em tamanhos até 53 GB e de que tem um SLA de 99,9% de disponibilidade. A nova [escalão premium](cache-premium-tier-intro.md) oferece tamanhos até 530 GB e suporte para clustering, VNET e persistência, com um SLA de 99,9%.

Cache de Redis do Azure permite aos clientes utilizar uma cache de Redis segura, dedicada e gerida pela Microsoft. Com esta oferta, obter tirar partido do conjunto de funcionalidades avançada e ecossistema fornecida pelo Redis e fiável de alojamento e monitorização da Microsoft.

Ao contrário das caches tradicionais que tratam apenas pares chave-valor, é popular para o respetivos altamente performant tipos de dados de Redis. Redis também suporta ser executado operações atómicas nestes tipos, como a acrescentar para uma cadeia; incrementando o valor num hash; enviar por push para uma lista; computação conjunto intersecção, union e diferença; ou ao obter o membro com a classificação máxima de um conjunto ordenado. Outras funcionalidades incluem suporte para transações, pub/sub, Lua de script, chaves com uma limitada time-to-live e definições de configuração para se comportam-se mais como uma tradicional cache de Redis.

Outro aspeto chave para êxito de Redis é a ecossistema de bom estado de funcionamento, vibrant open source para construído em torno-lo. Isto é refletido o conjunto diverso de clientes de Redis disponíveis em vários idiomas. Este ecossistema e um vasto leque de clientes permitem a Cache de Redis do Azure ser utilizado por praticamente qualquer carga de trabalho que iria criar dentro do Azure.

Para obter mais informações sobre como começar a Cache de Redis do Azure, consulte [como Cache de Redis do Azure de utilização](cache-dotnet-how-to-use-azure-redis-cache.md) e [documentação de Cache de Redis do Azure](index.md).

### <a name="managed-cache-service"></a>Serviço de Cache gerida
[Gerido Cache do serviço foi extinguido 30 de Novembro de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Para ver a documentação arquivada, consulte [arquivado documentação do serviço de Cache gerida](https://msdn.microsoft.com/library/azure/dn386094.aspx).

### <a name="in-role-cache"></a>Cache na Função
[Cache de função foi extinguida 30 de Novembro de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Para ver a documentação arquivada, consulte [arquivado documentação de Cache de função](https://msdn.microsoft.com/library/azure/dn386103.aspx).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
