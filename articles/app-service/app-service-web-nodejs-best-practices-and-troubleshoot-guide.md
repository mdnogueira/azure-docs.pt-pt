---
title: "Melhores práticas e guia de resolução de problemas para aplicações de nó na Web Apps do Azure"
description: "Conheça as melhores práticas e passos de resolução de problemas para aplicações de nó na Web Apps do Azure."
services: app-service\web
documentationcenter: nodejs
author: ranjithr
manager: wadeh
editor: 
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 06/06/2016
ms.author: ranjithr
ms.openlocfilehash: 8f39b5e6faf5f9121ec2abe347f50653c5c3e4f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-web-apps"></a>Melhores práticas e guia de resolução de problemas para aplicações de nó na Web Apps do Azure
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Neste artigo, ficará a saber as melhores práticas e passos de resolução de problemas para [aplicações de nó](app-service-web-get-started-nodejs.md) em execução no Azure Webapps (com [iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Tenha cuidado quando utilizar os passos de resolução de problemas no seu site de produção. Recomenda-se resolver problemas relacionados com a aplicação numa configuração de não produção por exemplo o bloco de transição e quando é corrigido o problema, trocar o bloco de transição com a ranhura de produção.
> 
> 

## <a name="iisnode-configuration"></a>Configuração do IISNODE
Isto [ficheiro de esquema](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) mostra todas as definições que podem ser configuradas para o iisnode. Algumas das definições que serão úteis para a sua aplicação são:

* nodeProcessCountPerApplication
  
    Esta definição controla o número de processos de nó que são iniciadas por aplicações de IIS. Valor predefinido é 1. Pode iniciar tantos node.exe como a contagem de núcleos VM por definir este como 0. Valor recomendado é 0 para a maioria das aplicações, pelo que pode utilizar todos os núcleos no seu computador. NODE.exe é único thread para que um node.exe irá consumir um máximo de 1 núcleo e ao obter o máximo desempenho fora da aplicação de nó que pretenda utilizar todos os núcleos.
* nodeProcessCommandLine
  
    Esta definição controla o caminho para o node.exe. Pode definir este valor para apontar para a versão de node.exe.
* maxConcurrentRequestsPerProcess
  
    Esta definição controla o número máximo de pedidos simultâneos enviados por iisnode para cada node.exe. No azure webapps, o valor predefinido é infinito. Não terá de preocupar com esta definição. Fora do azure webapps, o valor predefinido é de 1024. Pode querer configurar esta opção, dependendo de quantos os pedidos de que aplicação obtém e a rapidez a aplicação processa cada pedido.
* maxNamedPipeConnectionRetry
  
    Esta definição controla o número máximo de vezes que o iisnode tentará efetuar ligação no pipe nomeado para enviar o pedido de ativação pós-falha para node.exe. Esta definição em combinação com namedPipeConnectionRetryDelay determina o total de tempo limite de cada pedido de iisnode. Valor predefinido é de 200 no Azure Webapps. Total de tempo limite em segundos = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000
* namedPipeConnectionRetryDelay
  
    Controlos esta definição a quantidade de tempo (em ms) iisnode espera que entre cada tentativa para enviar o pedido para node.exe através do pipe nomeado. Valor predefinido é 250ms.
    Total de tempo limite em segundos = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000
  
    Por predefinição, o total de tempo limite no iisnode no webapps do azure é de 200 \* 250ms = 50 segundos.
* logDirectory
  
    Esta definição controla o diretório onde o iisnode irá iniciar stdout/stderr. Valor predefinido é o iisnode que é relativos ao diretório principal do script (diretório onde se encontra presente server.js principal)
* debuggerExtensionDll
  
    Esta definição controla que versão do nó-inspector iisnode irá utilizar quando depurar a aplicação de nó. Atualmente iisnode-inspector-0.7.3.dll iisnode inspector.dll, sendo os apenas 2 valores válidos para esta definição. Valor predefinido é iisnode-inspector-0.7.3.dll. versão do iisnode-inspector-0.7.3.dll utiliza o nó-inspector-0.7.3 e utiliza websockets, por isso terá de ativar websockets no seu webapp do azure para utilizar esta versão. Consulte <http://www.ranjithr.com/?p=98> para obter mais detalhes sobre como configurar o iisnode para utilizar o novo nó-inspector.
* flushResponse
  
    O comportamento predefinido do IIS é que coloca-os dados de resposta cópias de segurança para 4MB antes de libertar ou até ao fim da resposta, o que ocorrer primeiro. o iisnode oferece uma definição de configuração para substituir este comportamento: descarregar um fragmento de corpo de entidade de resposta, assim que o iisnode recebe-lo a partir de node.exe, tem de definir o iisnode/@flushResponse atributo em Web. config como 'true':
  
    ```
    <configuration>    
        <system.webServer>    
            <!-- ... -->    
            <iisnode flushResponse="true" />    
        </system.webServer>    
    </configuration>
    ```
  
    Ativar a libertar de cada fragmento de entidade do corpo da resposta adiciona a sobrecarga de desempenho que reduz o débito do sistema % ~ 5 (a partir da v0.1.13), pelo que é melhor definir o âmbito esta definição apenas para pontos finais que necessitam de resposta de transmissão em fluxo (por exemplo, utilizando o <location>elemento na Web. config)
  
    Além disto, para transmissão em fluxo de aplicações, terá também definir responseBufferLimit do processador do iisnode para 0.
  
    ```
    <handlers>    
        <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>    
    </handlers>
    ```
* watchedFiles
  
    Esta é uma lista de ponto e vírgula separada dos ficheiros que irá ser observada para as alterações. Uma alteração a um ficheiro faz com que a aplicação reciclar. Cada entrada é constituído por um nome de diretório opcional e nome de ficheiro necessárias que são relativos ao diretório onde está localizado o ponto de entrada de aplicação principal. São permitidos carateres universais no apenas a parte de nome de ficheiro. Valor predefinido é "\*. js;web.config"
* recycleSignalEnabled
  
    Valor predefinido é falso. Se estiver ativada, a aplicação de nó pode ligar a um pipe nomeado (variável de ambiente IISNODE\_controlo\_PIPE) e enviar uma mensagem de "Reciclagem". Isto fará com que o w3wp reciclar corretamente.
* idlePageOutTimePeriod
  
    Valor predefinido é 0 que significa que esta funcionalidade está desativada. Quando definido como um valor maior que 0, iisnode será página enviados todos os respetivos processos subordinados cada milissegundos 'idlePageOutTimePeriod'. Para compreender quais página saída significa, consulte este [documentação](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx). Esta definição será útil para aplicações que consomem muita memória e pretendem ocasionalmente, a memória pageout para disco para libertar algumas RAM.

> [!WARNING]
> Tenha cuidado ao ativar as seguintes definições de configuração em aplicações de produção. Recomenda-se para não ativá-los em aplicações de produção em direto.
> 
> 

* debugHeaderEnabled
  
    O valor predefinido é falso. Se definido como VERDADEIRO, iisnode irá adicionar cabeçalho de resposta um HTTP iisnode-debug cada resposta de HTTP envia que o valor do cabeçalho de depuração do iisnode é um URL. Podem ser obtidas partes individuais de informações de diagnóstico ao observar o fragmento de URL, mas uma muito melhor visualização é conseguida ao abrir o URL no browser.
* loggingEnabled
  
    Esta definição controla o registo de stdout e stderr por iisnode. Iisnode irá capturar stdout/stderr de processos de nó que inicia e escrita para o diretório especificado na definição de 'logDirectory'. Assim que este está activada, a aplicação irá escrever os registos para o sistema de ficheiros e dependendo da quantidade de registo efetuada pela aplicação, pode haver as implicações de desempenho.
* devErrorsEnabled
  
    Valor predefinido é falso. Quando definido como VERDADEIRO, iisnode irá apresentar o código de estado HTTP e o código de erro do Win32 no seu browser. O código de win32 será útil em determinados tipos de problemas de depuração.
* debuggingEnabled (não ativar no site de produção em direto)
  
    Esta definição controla a funcionalidade de depuração. O Iisnode está integrado com o node-inspector. Ao ativar esta definição, ativar a depuração da sua aplicação de nó. Quando esta definição estiver ativada, o iisnode será esquema os ficheiros de nó-inspector necessário no diretório 'debuggerVirtualDir' no primeiro pedido de depuração para a aplicação de nó. Pode carregar o nó-inspector ao enviar um pedido para http://yoursite/server.js/debug. Pode controlar o segmento de URL de depuração com a definição de 'debuggerPathSegment'. Por predefinição debuggerPathSegment = 'debug'. Pode definir esta opção para um GUID, por exemplo, para que seja mais difícil de ser detetados por outros.
  
    Selecionar esta opção [ligação](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) para obter mais detalhes sobre a depuração.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Cenários e recomendações/de resolução de problemas
### <a name="my-node-application-is-making-too-many-outbound-calls"></a>A minha aplicação do nó é efetuar demasiadas chamadas de saída.
Muitas aplicações iriam querer efetuar ligações de saída como parte da respetiva operação regular. Por exemplo, quando um pedido é apresentada no, a aplicação de nó seria deve contactar uma API de REST noutro local e obter algumas informações para processar o pedido. Iria querer utilizar um agente do keep alive ao efetuar chamadas http ou https. Por exemplo, pode utilizar o módulo de agentkeepalive como o agente de acesso keep alive ao efetuar estas chamadas de saída. Isto certifica-se de que o sockets é reutilizado no seu webapp VM do azure e reduzir o overhead de criação de novo sockets para cada pedido de saída. Além disso, isto certifica-se de que está a utilizar o menor número de sockets para tornar o número de pedidos de saída e, por conseguinte, não excedem o maxSockets são alocadas por VM. Recomendação no Azure Webapps seria definir o valor de maxSockets agentKeepAlive para um total de 160 sockets por VM. Isto significa que se tiver node.exe 4 em execução na VM, seria pretende definir a maxSockets agentKeepAlive para 40 por node.exe que é 160 total por VM.

Exemplo [agentKeepALive](https://www.npmjs.com/package/agentkeepalive) configuração:

```
var keepaliveAgent = new Agent({    
    maxSockets: 40,    
    maxFreeSockets: 10,    
    timeout: 60000,    
    keepAliveTimeout: 300000    
});
```

Este exemplo assume que tem node.exe 4 em execução numa VM. Se tiver um número diferente de node.exe em execução na VM, terá de modificar o maxSockets definição em conformidade.

### <a name="my-node-application-is-consuming-too-much-cpu"></a>A minha aplicação do nó está a consumir demasiados CPU.
Provavelmente, irá obter uma recomendação do Azure Webapps no seu portal sobre elevado consumo de cpu. Também pode configurar monitores para ver alguns [métricas](web-sites-monitor.md). Quando a verificar a utilização da CPU no [Dashboard do Portal do Azure](../application-insights/app-insights-web-monitor-performance.md), verifique os valores máximo para a CPU, pelo que não perder os valores das horas de ponta.
Nos casos em que considera que a aplicação está a consumir demasiados CPU e não é possível a explicar o motivo, precisa para criar um perfil da aplicação de nó.

### 
#### <a name="profiling-your-node-application-on-azure-webapps-with-v8-profiler"></a>A aplicação de nó num webapps do azure com o gerador de perfis V8 de criação de perfis
Por exemplo, permite diga tiverem uma aplicação do mundo Olá que pretende que a perfil conforme mostrado abaixo:

```
var http = require('http');    
function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    WriteConsoleLog();    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

Aceda ao seu scm site https://yoursite.scm.azurewebsites.net/DebugConsole

Irá ver uma linha de comandos, como mostrado abaixo. Vá para o diretório site/wwwroot

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Execute o comando "npm instalar o gerador de perfis v8"

Isto deverá instalar v8-o gerador de perfis no nó\_diretório de módulos e todas as dependências dele.
Agora, edite o server.js para criar um perfil da aplicação.

```
var http = require('http');    
var profiler = require('v8-profiler');    
var fs = require('fs');

function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    profiler.startProfiling('HandleRequest');    
    WriteConsoleLog();    
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

As alterações acima serão a função de WriteConsoleLog de perfil e, em seguida, escrever a saída de perfil 'profile.cpuprofile' no ficheiro em seu wwwroot de site. Envie um pedido para a aplicação. Irá ver um ficheiro de 'profile.cpuprofile' criado no seu wwwroot de site.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Transferir este ficheiro e terá de abrir este ficheiro com as ferramentas de F12 do Chrome. Acessos F12 no chrome, em seguida, clique em "Perfis separador". Clique no botão "Carga". Selecione o ficheiro de profile.cpuprofile que acabou de transferir. Clique no perfil de que acabou de carregar.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Verá que 95% do tempo consumida pela função WriteConsoleLog conforme mostrado abaixo. Isto também mostra-lhe os números de linha exata e os ficheiros de origem que fazer com que o problema.

### <a name="my-node-application-is-consuming-too-much-memory"></a>A minha aplicação do nó está a consumir demasiada memória.
Provavelmente, irá obter uma recomendação do Azure Webapps no seu portal sobre elevado consumo de memória. Também pode configurar monitores para ver alguns [métricas](web-sites-monitor.md). Quando a verificar a utilização de memória no [Dashboard do Portal do Azure](../application-insights/app-insights-web-monitor-performance.md), verifique os valores máximo da memória, pelo que não perder os valores das horas de ponta.

#### <a name="leak-detection-and-heap-diffing-for-nodejs"></a>Deteção de fuga e área dinâmica para dados Diffing para node.js
Pode utilizar [memwatch de nó](https://github.com/lloyd/node-memwatch) para o ajudar a identificar memória fugas.
Pode instalar memwatch tal como o gerador de perfis v8 e editar o código para capturar e diff fugas de pilhas para identificar a memória na sua aplicação.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>A minha node.exe são obter cancelado aleatoriamente
Existem algumas razões motivo pelo qual este pode estar a ocorrer:

1. A aplicação que está a gerar exceções não identificadas – d: verificação do volte\\raiz\\LogFiles\\aplicação\\ficheiro de registo errors.txt para obter os detalhes na exceção emitida. Este ficheiro tem o rastreio da pilha, pelo que pode corrigir a sua aplicação com base neste.
2. A aplicação está a consumir demasiada memória que está a afetar outros processos de introdução. Se a memória total da VM está próximo 100%, o node.exe foi cancelado pelo Gestor de processo para permitir que outros processos obter uma oportunidade de fazer alguns cálculos. Para corrigir este problema, certifique-se de que a aplicação não é a fuga de memória ou se a aplicação realmente necessita de utilizar uma grande quantidade de memória,. aumentar verticalmente a uma VM com muito mais RAM superior.

### <a name="my-node-application-does-not-start"></a>A minha aplicação do nó não é iniciado
Se a aplicação está a devolver 500 erros durante o arranque, pode dever-se a algumas razões:

1. NODE.exe não está presente na localização correta. Verifique a definição de nodeProcessCommandLine.
2. Ficheiro de script principal não está presente na localização correta. Verifique o Web. config e certifique-se de que o nome do ficheiro de script principal na secção de processadores corresponde ao ficheiro de script principal.
3. Configuração do Web. config não está correta – verificar nomes/valores das definições.
4. Arranque a frio – a aplicação está a demorar demasiado tempo para o arranque. Se a sua aplicação demora mais de (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1 000 segundos, iisnode irá devolver o erro 500. Aumente os valores destas definições para corresponder a hora de início de aplicações para impedir que o iisnode de exceder o tempo limite e devolver o erro 500.

### <a name="my-node-application-crashed"></a>A minha aplicação nó falhada
A aplicação que está a gerar exceções não identificadas – d: verificação do volte\\raiz\\LogFiles\\aplicação\\ficheiro de registo errors.txt para obter os detalhes na exceção emitida. Este ficheiro tem o rastreio da pilha, pelo que pode corrigir a sua aplicação com base neste.

### <a name="my-node-application-takes-too-much-time-to-startup-cold-start"></a>A minha aplicação do nó demora demasiado tempo a arranque (início amovíveis)
A razão mais comum para esta situação é que a aplicação tem muitos ficheiros no nó de\_módulos e a aplicação tenta ao carregar a maioria destes ficheiros durante o arranque. Por predefinição, uma vez que residem os ficheiros na partilha de rede no Azure Webapps, carregar os ficheiros de muitos pode demorar algum tempo.
Algumas soluções para tornar esta mais rápida são:

1. Certifique-se de que tem uma estrutura de dependência simples e não existem dependências duplicadas utilizando npm3 para instalar os módulos.
2. Tente lento para carregar o nó\_módulos e não carregar todos os módulos no arranque. Isto significa que a chamada para require('module') deve ser efetuada quando precisar realmente numa função que tenta utilizar o módulo.
3. Webapps do Azure oferece uma funcionalidade denominada local cache. Esta funcionalidade copia o conteúdo da partilha de rede para o disco local na VM. Uma vez que os ficheiros são locais, o tempo de carregamento do nó\_módulos é muito mais rápido.

## <a name="iisnode-http-status-and-substatus"></a>Subestado e o estado de http do IISNODE
Isto [ficheiro de origem](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) apresenta uma lista de todos os iisnode de combinação possível de estado/subestado pode devolver, no caso de erro.

Ativar FREB para a sua aplicação ver o código de erro do win32 (. Certifique-se de que ativa FREB apenas em sites de não produção por motivos de desempenho).

| Estado de HTTP | Subestado http | Razão possível? |
| --- | --- | --- |
| 500 |1000 |Ocorreu algum problema ao emitir o pedido para o IISNODE – verificar se node.exe foi iniciada. NODE.exe foi falharam durante o arranque. Verifique a configuração do Web. config para erros. |
| 500 |1001 |-Win32Error 0x2 - App não está a responder ao URL. Verifique as regras de reescrever de URL ou se a aplicação express tiver as rotas corretas definidas. -Win32Error 0x6d – pipe nomeado está ocupado – Node.exe não está a aceitar pedidos porque o pipe está ocupado. Verifique a utilização elevada da cpu. -Outros erros – Verifique se node.exe falhado. |
| 500 |1002 |NODE.exe falhou – verificar d:\\raiz\\LogFiles\\errors.txt de registo de rastreio da pilha. |
| 500 |1003 |Problema de configuração do pipe – deve nunca vir isto, mas se o fizer, a configuração de pipe nomeado está incorreta. |
| 500 |1004-1018 |Ocorreu um erro ao enviar o pedido ou processamento da resposta do node.exe. Verifique se node.exe falhado. verificação de d:\\raiz\\LogFiles\\errors.txt de registo de rastreio da pilha. |
| 503 |1000 |Não existe memória suficiente para atribuir mais ligações de pipe nomeado. Por que motivo está a consumir memória, tanto que a aplicação de verificação. Verifique o valor da definição maxConcurrentRequestsPerProcess. Se não infinito e ter um grande número de pedidos, aumentar este valor para evitar este erro. |
| 503 |1001 |Não foi possível emitir pedido node.exe porque a aplicação é Reciclagem. Depois da aplicação tem reciclados, devem ser normalmente servidos pedidos. |
| 503 |1002 |Não foi possível emitir o código de erro do win32 verificação motivo real – pedido para um node.exe. |
| 503 |1003 |Pipe nomeado é demasiado ocupado – verificar se o nó está a consumir uma grande quantidade de CPU |

Há uma definição no NODE.exe denominado nó\_pendente\_PIPE\_instâncias. Por predefinição fora do azure webapps, este valor é 4. Isto significa que node.exe só pode aceitar 4 pedidos de cada vez no pipe nomeado. No Azure Webapps, este valor é definido como 5000 e este valor deve ser suficientemente ideal para a maioria das aplicações de nó em execução no azure webapps. Deverá ver não 503.1003 no azure webapps porque temos um valor elevado para o nó\_pendente\_PIPE\_instâncias.  |

## <a name="more-resources"></a>Mais recursos
Siga estas ligações para saber mais sobre aplicações node.js no App Service do Azure.

* [Introdução ao web apps Node.js no App Service do Azure](app-service-web-get-started-nodejs.md)
* [Como depurar uma aplicação Web Node.js no Serviço de Aplicações do Azure](app-service-web-tutorial-nodejs-mongodb-app.md)
* [Utilizar Módulos do Node.js com aplicações do Azure](../nodejs-use-node-modules-azure-apps.md)
* [Aplicações Web do Serviço de Aplicações do Azure: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Centro para Programadores do Node.js](../nodejs-use-node-modules-azure-apps.md)
* [Explorar a Consola de Depuração do Kudu Super Secreta](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)

