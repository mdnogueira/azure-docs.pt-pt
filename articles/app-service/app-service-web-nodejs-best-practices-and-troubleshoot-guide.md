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
ms.date: 11/09/2017
ms.author: ranjithr
ms.openlocfilehash: f7f3186ba93670e566a10f97dde86a977101e8fc
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-web-apps"></a>Melhores práticas e guia de resolução de problemas para aplicações de nó na Web Apps do Azure
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Neste artigo, aprende as melhores práticas e passos de resolução de problemas para [aplicações de nó](app-service-web-get-started-nodejs.md) em execução no Web Apps do Azure (com [iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Tenha cuidado quando utilizar os passos de resolução de problemas no seu site de produção. Recomenda-se resolver problemas relacionados com a aplicação numa configuração de não produção por exemplo o bloco de transição e quando é corrigido o problema, trocar o bloco de transição com a ranhura de produção.
> 
> 

## <a name="iisnode-configuration"></a>Configuração do IISNODE
Isto [ficheiro de esquema](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) mostra todas as definições que pode configurar para o iisnode. Algumas das definições que são úteis para a sua aplicação:

* nodeProcessCountPerApplication
  
    Esta definição controla o número de processos de nó que são iniciadas por aplicações de IIS. O valor predefinido é 1. Pode iniciar node.exes tantos como a contagem de vCPU VM por definir este como 0. O valor recomendado é 0 para a maioria das aplicações, pelo que pode utilizar todas as vCPUs no seu computador. NODE.exe é único thread para que um node.exe consome um máximo de 1 vCPU. Para obter o máximo desempenho fora da aplicação de nó, que pretende utilizar todos os vCPUs.
* nodeProcessCommandLine
  
    Esta definição controla o caminho para o node.exe. Pode definir este valor para apontar para a versão de node.exe.
* maxConcurrentRequestsPerProcess
  
    Esta definição controla o número máximo de pedidos simultâneos enviados por iisnode para cada node.exe. No Web Apps do Azure, o valor predefinido é infinito. Não tem de se preocupar com esta definição. Fora de Web Apps do Azure, o valor predefinido é de 1024. Pode configurar esta opção, dependendo de quantos pedidos que recebe a sua aplicação e a rapidez a aplicação processa cada pedido.
* maxNamedPipeConnectionRetry
  
    Esta definição controla o número máximo de vezes que efetuar a ligação no pipe nomeado para enviar o pedido de ativação pós-falha para node.exe de tentativas de iisnode. Esta definição em combinação com namedPipeConnectionRetryDelay determina o total de tempo limite de cada pedido de iisnode. O valor predefinido é de 200 na Web Apps do Azure. Total de tempo limite em segundos = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000
* namedPipeConnectionRetryDelay
  
    Esta definição controla a quantidade de tempo (em ms) iisnode aguarda entre cada tentativa para enviar o pedido para node.exe através do pipe nomeado. O valor predefinido é 250 ms.
    Total de tempo limite em segundos = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000
  
    Por predefinição, o total de tempo limite no iisnode no Web Apps do Azure é de 200 \* 250 ms = 50 segundos.
* logDirectory
  
    Esta definição controla o diretório onde o iisnode os registos de stdout/stderr. O valor predefinido é iisnode, que é relativos ao diretório principal do script (diretório onde se encontra presente server.js principal)
* debuggerExtensionDll
  
    Esta definição controla que versão do nó-inspector iisnode utiliza quando depurar a aplicação de nó. Atualmente, o iisnode-inspector-0.7.3.dll e iisnode inspector.dll são apenas dois valores válidos para esta definição. O valor predefinido é iisnode-inspector-0.7.3.dll. A versão do iisnode-inspector-0.7.3.dll utiliza o nó-inspector-0.7.3 e utiliza websockets. Tem de ativar websockets a webapp do Azure para utilizar esta versão. Consulte <http://www.ranjithr.com/?p=98> para obter mais detalhes sobre como configurar o iisnode para utilizar o novo nó-inspector.
* flushResponse
  
    O comportamento predefinido do IIS é que coloca-os dados de resposta cópias de segurança para 4 MB antes de libertar ou até ao fim da resposta, o que ocorrer primeiro. o iisnode oferece uma definição de configuração para substituir este comportamento: descarregar um fragmento de corpo de entidade de resposta, assim que o iisnode recebe-lo a partir de node.exe, tem de definir o iisnode/@flushResponse atributo em Web. config como 'true':
  
    ```
    <configuration>    
        <system.webServer>    
            <!-- ... -->    
            <iisnode flushResponse="true" />    
        </system.webServer>    
    </configuration>
    ```
  
    Ativar a libertar de cada fragmento de entidade do corpo da resposta adiciona a sobrecarga de desempenho que reduz o débito do sistema % ~ 5 (a partir da v0.1.13), pelo que é melhor definir o âmbito esta definição apenas para pontos finais que necessitam de resposta de transmissão em fluxo (por exemplo, utilizando o <location> elemento na Web. config).
  
    Além disto, para transmissão em fluxo de aplicações, também tem de definir responseBufferLimit do processador do iisnode para 0.
  
    ```
    <handlers>    
        <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>    
    </handlers>
    ```
* watchedFiles
  
    Esta é uma lista de ponto e vírgula separada dos ficheiros que são observada para as alterações. Uma alteração a um ficheiro faz com que a aplicação reciclar. Cada entrada é constituído por um nome de diretório opcional e um nome de ficheiro necessárias que são relativos ao diretório onde está localizado o ponto de entrada de aplicação principal. São permitidos carateres universais no apenas a parte de nome de ficheiro. O valor predefinido é "\*. js;web.config"
* recycleSignalEnabled
  
    O valor predefinido é falso. Se estiver ativada, a aplicação de nó pode ligar a um pipe nomeado (variável de ambiente IISNODE\_controlo\_PIPE) e enviar uma mensagem de "Reciclagem". Isto faz com que o w3wp reciclar corretamente.
* idlePageOutTimePeriod
  
    O valor predefinido é 0, o que significa que esta funcionalidade está desativada. Quando definido como um valor maior que 0, iisnode será página enviados todos os respetivos processos subordinados cada 'idlePageOutTimePeriod' em milissegundos. Consulte [documentação](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx) para compreender quais página saída significa. Esta definição é útil para aplicações que consomem muita memória e pretendem página fora de memória para o disco ocasionalmente para libertar algumas RAM.

> [!WARNING]
> Tenha cuidado ao ativar as seguintes definições de configuração em aplicações de produção. A recomendação é não ativá-los em aplicações de produção em direto.
> 
> 

* debugHeaderEnabled
  
    O valor predefinido é falso. Se definido como VERDADEIRO, iisnode adiciona o cabeçalho de resposta um HTTP iisnode-debug cada resposta de HTTP envia que o valor do cabeçalho de depuração do iisnode é um URL. Podem ser obtidas partes individuais de informações de diagnóstico ao observar o fragmento de URL, mas uma muito melhor visualização é conseguida ao abrir o URL no browser.
* loggingEnabled
  
    Esta definição controla o registo de stdout e stderr por iisnode. O Iisnode captura stdout/stderr de processos de nó que inicia e grava no diretório especificado na definição de 'logDirectory'. Quando esta estiver ativada, a aplicação escreve registos para o sistema de ficheiros e dependendo da quantidade de registo efetuada pela aplicação, pode haver as implicações de desempenho.
* devErrorsEnabled
  
   O valor predefinido é falso. Quando definido como VERDADEIRO, iisnode apresenta o código de estado HTTP e o código de erro do Win32 no seu browser. O código win32 é útil em determinados tipos de problemas de depuração.
* debuggingEnabled (não ativar no site de produção em direto)
  
    Esta definição controla a funcionalidade de depuração. O Iisnode está integrado com o node-inspector. Ao ativar esta definição, ativar a depuração da sua aplicação de nó. Quando esta definição estiver ativada, o iisnode irá apresentar os ficheiros de nó-inspector necessário no diretório 'debuggerVirtualDir' no primeiro pedido de depuração para a aplicação de nó. Pode carregar o nó-inspector ao enviar um pedido para http://yoursite/server.js/debug. Pode controlar o segmento de URL de depuração com a definição de 'debuggerPathSegment'. Por predefinição, debuggerPathSegment = 'debug'. Pode definir esta opção para um GUID, por exemplo, para que seja mais difícil de ser detetados por outros.
  
    Selecionar esta opção [ligação](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) para obter mais detalhes sobre a depuração.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Cenários e recomendações/de resolução de problemas
### <a name="my-node-application-is-making-too-many-outbound-calls"></a>A minha aplicação do nó é efetuar demasiadas chamadas de saída.
Muitas aplicações iriam querer efetuar ligações de saída como parte da respetiva operação regular. Por exemplo, quando um pedido é apresentada no, a aplicação de nó seria deve contactar uma API de REST noutro local e obter algumas informações para processar o pedido. Iria querer utilizar um agente do keep alive ao efetuar chamadas http ou https. Por exemplo, pode utilizar o módulo de agentkeepalive como o agente de acesso keep alive ao efetuar estas chamadas de saída. Isto certifica-se de que o sockets é reutilizado no seu webapp VM do Azure e reduzir o overhead de criação de novo sockets para cada pedido de saída. Além disso, isto certifica-se de que está a utilizar o menor número de sockets para tornar o número de pedidos de saída e, por conseguinte, não excedem o maxSockets são alocadas por VM. A recomendação na Web Apps do Azure é definir o valor de maxSockets agentKeepAlive para um total de 160 sockets por VM. Isto significa que se tiver node.exe quatro em execução na VM, que pretende definir a maxSockets agentKeepAlive para 40 por node.exe, que é 160 total por VM.

Exemplo [agentKeepALive](https://www.npmjs.com/package/agentkeepalive) configuração:

```
var keepaliveAgent = new Agent({    
    maxSockets: 40,    
    maxFreeSockets: 10,    
    timeout: 60000,    
    keepAliveTimeout: 300000    
});
```

Este exemplo assume que tem node.exe 4 em execução numa VM. Se tiver um número diferente de node.exe em execução na VM, tem de modificar o maxSockets definição em conformidade.

### <a name="my-node-application-is-consuming-too-much-cpu"></a>A minha aplicação do nó está a consumir demasiados CPU.
Provavelmente, irá obter uma recomendação de Web Apps do Azure no seu portal sobre elevado consumo de cpu. Também pode definir monitores para ver alguns [métricas](web-sites-monitor.md). Quando a verificar a utilização da CPU no [Dashboard do Portal do Azure](../application-insights/app-insights-web-monitor-performance.md), verifique os valores máximo para a CPU, pelo que não perder os valores das horas de ponta.
Nos casos em que considera que a aplicação está a consumir demasiados CPU e não é possível a explicar o motivo, pode perfil da aplicação do nó para saber.

### 
#### <a name="profiling-your-node-application-on-azure-web-apps-with-v8-profiler"></a>A aplicação de nó num Web Apps do Azure com o gerador de perfis V8 de criação de perfis
Por exemplo, vamos supor que tiver uma aplicação do mundo Olá que pretende para criar um perfil da seguinte forma:

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

Verá uma linha de comandos, conforme mostrado abaixo. Vá para o diretório site/wwwroot.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Execute o comando "npm instalar o gerador de perfis v8".

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

Os perfis de código anterior a WriteConsoleLog funcionar e, em seguida, escreve o resultado de perfil para o ficheiro 'profile.cpuprofile' em seu wwwroot de site. Envie um pedido para a aplicação. Pode ver um ficheiro de 'profile.cpuprofile' criado no seu wwwroot de site.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Transferir este ficheiro e abri-lo com as ferramentas de F12 do Chrome. Premir a tecla F12 no Chrome, em seguida, escolha o **perfis** separador. Escolha o **carga** botão. Selecione o ficheiro de profile.cpuprofile que transferiu. Clique no perfil de que acabou de carregar.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Pode ver que 95% do tempo consumida pela função WriteConsoleLog. Isto também mostra-lhe os números de linha exata e os ficheiros de origem que causou o problema.

### <a name="my-node-application-is-consuming-too-much-memory"></a>A minha aplicação do nó está a consumir demasiada memória
Se a aplicação está a consumir demasiada memória, pode ver um aviso de Web Apps do Azure no seu portal sobre elevado consumo de memória. Pode configurar monitores para ver alguns [métricas](web-sites-monitor.md). Quando a verificar a utilização de memória no [Dashboard do Portal do Azure](../application-insights/app-insights-web-monitor-performance.md), certifique-se verificar os valores máximo da memória, pelo que não perder os valores das horas de ponta.

#### <a name="leak-detection-and-heap-diffing-for-nodejs"></a>Deteção de fuga e área dinâmica para dados Diffing para node.js
Pode utilizar [memwatch de nó](https://github.com/lloyd/node-memwatch) para o ajudar a identificar memória fugas.
Pode instalar memwatch tal como o gerador de perfis v8 e editar o código para capturar e diff fugas de pilhas para identificar a memória na sua aplicação.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>A minha node.exe são obter cancelado aleatoriamente
Existem algumas razões motivo pelo qual este pode estar a ocorrer:

1. A aplicação que está a gerar exceções não identificadas – d: de verificação\\raiz\\LogFiles\\aplicação\\ficheiro de registo errors.txt para obter os detalhes na exceção emitida. Este ficheiro tem o rastreio da pilha, pelo que pode corrigir a sua aplicação com base neste.
2. A aplicação está a consumir demasiada memória que está a afetar outros processos de introdução. Se a memória total da VM está próximo 100%, o node.exe foi cancelado pelo Gestor de processo para permitir que outros processos obter uma oportunidade de fazer alguns cálculos. Para corrigir este problema, certifique-se de que a aplicação não é a fuga de memória ou se a aplicação tem de utilizar uma grande quantidade de memória, aumentar verticalmente a uma VM com muito mais RAM superior.

### <a name="my-node-application-does-not-start"></a>A minha aplicação do nó não é iniciado
Se a aplicação está a devolver 500 erros quando é iniciado, pode dever-se a algumas razões:

1. NODE.exe não está presente na localização correta. Verifique a definição de nodeProcessCommandLine.
2. Ficheiro de script principal não está presente na localização correta. Verifique o Web. config e certifique-se de que o nome do ficheiro de script principal na secção de processadores corresponde ao ficheiro de script principal.
3. Configuração do Web. config não está correta – verificar nomes/valores das definições.
4. Arranque a frio – a aplicação está a demorar demasiado tempo a iniciar. Se a sua aplicação demora mais de (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1 000 segundos, o iisnode devolve um erro 500. Aumente os valores destas definições para corresponder a hora de início de aplicações para impedir que o iisnode de exceder o tempo limite e devolver o erro 500.

### <a name="my-node-application-crashed"></a>A minha aplicação nó falhada
A aplicação que está a gerar exceções não identificadas – d: verificação do volte\\raiz\\LogFiles\\aplicação\\ficheiro de registo errors.txt para obter os detalhes na exceção emitida. Este ficheiro tem o rastreio da pilha, pelo que pode corrigir a sua aplicação com base neste.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>A minha aplicação do nó demora demasiado tempo a iniciar (início amovíveis)
A razão mais comum para uma aplicação demorar demasiado tempo a iniciar é um número elevado de ficheiros no nó\_módulos. A aplicação tenta ao carregar a maioria destes ficheiros ao iniciar. Por predefinição, uma vez que residem os ficheiros na partilha de rede na Web Apps do Azure, carregar muitos ficheiros pode demorar tempo.
Algumas soluções para tornar este processo mais rápido são:

1. Lembre-se de que tem uma estrutura de dependência simples e não existem dependências duplicadas utilizando npm3 para instalar os módulos.
2. Tente lento para carregar o nó\_módulos e não carregar todos os módulos no início da aplicação. Isto significa que a chamada para require('module') deve ser efetuada quando precisar realmente numa função que tente ao utilizar o módulo.
3. As aplicações Web do Azure oferece uma funcionalidade denominada local cache. Esta funcionalidade copia o conteúdo da partilha de rede para o disco local na VM. Uma vez que os ficheiros são locais, o tempo de carregamento do nó\_módulos é muito mais rápido.

## <a name="iisnode-http-status-and-substatus"></a>Subestado e o estado de http do IISNODE
Isto [ficheiro de origem](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) apresenta uma lista de todos os iisnode de combinações possíveis de estado/subestado podem devolver, em caso de erro.

Ativar FREB para a sua aplicação ver o código de erro do win32 (Lembre-se de ativar FREB apenas em sites de não produção por motivos de desempenho).

| Estado de HTTP | Subestado http | Razão possível? |
| --- | --- | --- |
| 500 |1000 |Ocorreu algum problema ao emitir o pedido para o IISNODE – Verifique se node.exe foi iniciada. NODE.exe foi ter falhado ao iniciar. Verifique a configuração do Web. config para erros. |
| 500 |1001 |-Win32Error 0x2 - App não está a responder ao URL. Verifique as regras de reescrever de URL ou marcar se a aplicação express tiver as rotas corretas definidas. -Win32Error 0x6d – pipe nomeado está ocupado – Node.exe não está a aceitar pedidos porque o pipe está ocupado. Verifique a utilização elevada da cpu. -Outros erros – Verifique se node.exe falhado. |
| 500 |1002 |NODE.exe falhou – verificar d:\\raiz\\LogFiles\\errors.txt de registo de rastreio da pilha. |
| 500 |1003 |Problema de configuração do pipe – deve nunca vir isto, mas se o fizer, a configuração de pipe nomeado está incorreta. |
| 500 |1004-1018 |Ocorreu um erro ao enviar o pedido ou processamento da resposta do node.exe. Verifique se node.exe falhado. verificação de d:\\raiz\\LogFiles\\errors.txt de registo de rastreio da pilha. |
| 503 |1000 |Não existe memória suficiente para atribuir mais ligações de pipe nomeado. Por que motivo está a consumir memória, tanto que a aplicação de verificação. Verifique o valor da definição maxConcurrentRequestsPerProcess. Se não for infinita e tiver muitos pedidos, aumente este valor para evitar este erro. |
| 503 |1001 |Não foi possível emitir pedido node.exe porque a aplicação é Reciclagem. Depois da aplicação tem reciclados, devem ser normalmente servidos pedidos. |
| 503 |1002 |Não foi possível emitir o código de erro do win32 verificação motivo real – pedido para um node.exe. |
| 503 |1003 |Pipe nomeado é demasiado ocupado – verificar se o nó está a consumir excessiva da CPU |

Há uma definição no NODE.exe denominado nó\_pendente\_PIPE\_instâncias. Por predefinição, fora das Web Apps do Azure, este valor é 4. Isto significa que node.exe só podem aceitar pedidos de quatro num momento no pipe nomeado. No Web Apps do Azure, este valor é definido como 5000. Este valor deve ser suficientemente ideal para a maioria de nós aplicações em execução nas Web Apps do Azure. Não verá 503.1003 na Web Apps do Azure devido ao valor superior para o nó\_pendente\_PIPE\_instâncias.  |

## <a name="more-resources"></a>Mais recursos
Siga estas ligações para saber mais sobre aplicações node.js no App Service do Azure.

* [Introdução ao Web Apps Node.js no App Service do Azure](app-service-web-get-started-nodejs.md)
* [Como depurar uma aplicação Web Node.js no Serviço de Aplicações do Azure](app-service-web-tutorial-nodejs-mongodb-app.md)
* [Utilizar Módulos do Node.js com aplicações do Azure](../nodejs-use-node-modules-azure-apps.md)
* [Aplicações Web do Serviço de Aplicações do Azure: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Centro para Programadores do Node.js](../nodejs-use-node-modules-azure-apps.md)
* [Explorar a Consola de Depuração do Kudu Super Secreta](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)

