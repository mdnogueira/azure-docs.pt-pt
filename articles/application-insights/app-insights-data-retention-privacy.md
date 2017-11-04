---
title: "Retenção de dados e armazenamento no Azure Application Insights | Microsoft Docs"
description: "Declaração de política de retenção e privacidade"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: a6268811-c8df-42b5-8b1b-1d5a7e94cbca
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: mbullwin
ms.openlocfilehash: 3e13cc70dc09dd795bb0df57a4bbb29c8fcddb9e
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Recolha de dados, retenção e armazenamento no Application Insights


Quando instala [Azure Application Insights] [ start] SDK na sua aplicação, envia telemetria sobre a sua aplicação para a nuvem. Naturalmente, os programadores responsável querem saber exatamente os dados que são enviados, o que acontece aos dados e como se podem manter o controlo do mesmo. Em especial, foi possível enviar os dados confidenciais, onde é que está armazenada e como protegem é? 

Primeiro, a resposta curto:

* Os módulos de telemetria padrão que "caixa" são pouco provável enviar dados confidenciais para o serviço. A telemetria está preocupada com carga, métricas de desempenho e a utilização, relatórios de exceções e outros dados de diagnóstico. Os dados de utilizador principal visíveis nos relatórios de diagnóstico são URLs; mas a sua aplicação em qualquer dos casos não deve colocar os dados confidenciais em texto simples num URL.
* Pode escrever código que envia a telemetria personalizada adicional para ajudá-lo com diagnóstico e utilização de monitorização. (Este extensibilidade é uma funcionalidade excelente do Application Insights). Seria possível que, por engano, escrever este código de modo a que inclui pessoais e outros dados confidenciais. Se a aplicação funciona com esses dados, deve ser aplicada uma revisão de detalhado processos para todos os o código de escrita.
* Ao desenvolver e testar a sua aplicação, é fácil inspecionar os que estão a ser enviado pelo SDK. Os dados são apresentados do windows de saída de depuração do browser e IDE. 
* Os dados que são mantidos em [Microsoft Azure](http://azure.com) servidores nos E.U.A. ou Europa. (Mas a aplicação pode ser executado em qualquer local). O Azure tem [segurança forte processa e cumpre uma vasta gama de normas de conformidade](https://azure.microsoft.com/support/trust-center/). Apenas o e a equipa de designado têm acesso aos seus dados. Os funcionários da Microsoft podem ter acesso restrito ao mesmo apenas em circunstâncias limitadas específicas com o conhecimento do utilizador. São encriptado em trânsito, apesar de não nos servidores.

O resto deste artigo elaborates mais detalhadamente nestes respostas. Foi concebido para ser autónomo, para que pode mostrá-lo aos colegas que não fazem parte da sua equipa de imediato.

## <a name="what-is-application-insights"></a>O que é o Application Insights?
[Azure Application Insights] [ start] é um serviço fornecido pela Microsoft que o ajuda a melhorar o desempenho e a facilidade de utilização da sua aplicação em direto. Monitoriza a aplicação sempre está em execução, durante os testes e depois de ter publicado ou implementado. Application Insights cria gráficos e tabelas que mostram, por exemplo, as horas do dia, obter maior parte dos utilizadores, como responder a aplicação está e quão bem é servido por quaisquer serviços externos que depende. Se existirem falhas, falhas ou problemas de desempenho, pode procurar os dados telemétricos em detalhe para diagnosticar a causa. E o serviço irá enviar-lhe e-mails se existirem quaisquer alterações na disponibilidade e desempenho da sua aplicação.

Para obter esta funcionalidade, instale um Application Insights SDK na sua aplicação, que passa a fazer parte do seu código. Quando a aplicação está em execução, o SDK monitoriza a conclusão da operação e envia a telemetria para o serviço do Application Insights. Este é um serviço em nuvem alojado pela [Microsoft Azure](http://azure.com). (Mas Application Insights funciona para quaisquer aplicações, não apenas os que estão alojados no Azure).

![O SDK na sua aplicação envia a telemetria para o serviço do Application Insights.](./media/app-insights-data-retention-privacy/01-scheme.png)

O serviço do Application Insights armazena e analisa a telemetria. Para ver o Analysis Services ou a pesquisa através de telemetria armazenada, inicie sessão na sua conta do Azure e abrir o recurso do Application Insights para a sua aplicação. Também pode partilhar o acesso aos dados com outros membros da sua equipa ou com os subscritores do Azure especificados.

Pode ter dados exportados a partir do serviço Application Insights, por exemplo, para uma base de dados ou ferramentas externas. Fornecer cada ferramenta com uma chave especial que obtém a partir do serviço. Pode ser revogada a chave, se necessário. 

SDKs do Application Insights estão disponíveis para uma variedade de tipos de aplicação: web serviços alojados no seus próprios servidores J2EE ou ASP.NET, ou no Azure; Web clientes - ou seja, o código em execução numa página web; aplicações de ambiente de trabalho e serviços; aplicações de dispositivos, como o Windows Phone, iOS e Android. Todos eles enviam telemetria para o mesmo serviço.

## <a name="what-data-does-it-collect"></a>Os dados que-recolhe?
### <a name="how-is-the-data-is-collected"></a>Como são os dados é recolhidos?
Existem três origens de dados:

* O SDK, que integrar com a sua aplicação ou [no desenvolvimento](app-insights-asp-net.md) ou [em tempo de execução](app-insights-monitor-performance-live-website-now.md). Existem SDKs diferentes para tipos de aplicação diferente. Há também um [SDK para páginas web](app-insights-javascript.md), que é carregado no browser do utilizador final, juntamente com a página.
  
  * Cada SDK tem um número de [módulos](app-insights-configuration-with-applicationinsights-config.md), que utilize técnicas de diferentes para diferentes tipos de telemetria de recolher.
  * Se instalar o SDK de desenvolvimento, pode utilizar a API para enviar a sua própria telemetria, além dos módulos padrão. Esta telemetria personalizada pode incluir quaisquer dados que pretende enviar.
* Em alguns servidores web, também existem agentes que são executados em conjunto com a aplicação e enviar telemetria sobre a CPU, memória e ocupação da rede. Por exemplo, as VMs do Azure, anfitriões de Docker, e [J2EE servidores](app-insights-java-agent.md) pode ter desses agentes.
* [Testes de disponibilidade](app-insights-monitor-web-app-availability.md) processos são executados pela Microsoft que enviar pedidos para a aplicação web em intervalos regulares. Os resultados são enviados para o serviço do Application Insights.

### <a name="what-kinds-of-data-are-collected"></a>Os tipos de dados são recolhidos?
As categorias principais são:

* [Telemetria do servidor de Web](app-insights-asp-net.md) -pedidos de HTTP.  URI, tempo decorrido para processar o pedido, o código de resposta, o endereço IP do cliente. Id de sessão.
* [Páginas Web](app-insights-javascript.md) -contagens de sessões, utilizador e página. Tempos de carregamento de página. Exceções. Chamadas AJAX.
* Desempenho contadores - memória, CPU, e/s, ocupação da rede.
* Cliente e servidor contexto - SO, região, tipo de dispositivo, browser, resolução do ecrã.
* [Exceções](app-insights-asp-net-exceptions.md) e falhas - **capturas da pilha**, criar id, o tipo de CPU. 
* [Dependências](app-insights-asp-net-dependencies.md) -chamadas para serviços externos como REST, SQL, AJAX. URI ou cadeia de ligação, duração, com êxito, o comando.
* [Testes de disponibilidade](app-insights-monitor-web-app-availability.md) -duração de teste e os passos, as respostas.
* [Registos de rastreio](app-insights-asp-net-trace-logs.md) e [telemetria personalizada](app-insights-api-custom-events-metrics.md) - **nada código no seu registos ou telemetria**.

[Mais detalhadamente](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Como posso verificar o que é recolhido?
Se estiver a desenvolver aplicações com o Visual Studio, execute a aplicação no modo de depuração (F5). A telemetria é apresentado na janela de saída. A partir daí, pode copiá-lo e formate-o como JSON para inspecção fácil. 

![](./media/app-insights-data-retention-privacy/06-vs.png)

Há também uma vista mais legível na janela de diagnóstico.

Para páginas web, abra a janela de depuração do seu browser.

![Premir a tecla F12 e abra o separador de rede.](./media/app-insights-data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Pode escrever código para filtrar a telemetria antes de ser enviada?
Isto seria possível escrevendo um [Plug-in de processador de telemetria](app-insights-api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Quanto são os dados mantidos?
Pontos de dados não processados (ou seja, os itens que pode consultar na análise e inspecionar na pesquisa) são mantidos durante 90 dias. Se precisar de manter mais de que os dados, pode utilizar [a exportação contínua](app-insights-export-telemetry.md) para a copiar para uma conta de armazenamento.

Dados agregados (ou seja, contagens, médias e outros dados estatísticos que visualiza no Explorador de métrica) são retidos num intervalo de 1 minuto 90 dias.

## <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?
Os dados são visíveis para o utilizador e, se tiver uma conta de organização, os membros do agrupamento. 

Este pode ser exportado pelo utilizador e os membros da equipa e foi ser copiado para outras localizações e transmitido para outras pessoas.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>O que a Microsoft a fazer com as informações que envia a minha aplicação para o Application Insights?
A Microsoft utiliza os dados apenas para fornecer o serviço para si.

## <a name="where-is-the-data-held"></a>Onde estão contidos os dados?
* Nos E.U.A. ou Europa. Pode selecionar a localização quando cria um novo recurso do Application Insights. 


#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-or-europe"></a>Significa que a minha aplicação tem de ser alojados na Europa ou EUA?
* Não. A aplicação pode executar em qualquer local, na suas própria anfitriões no local ou na nuvem.

## <a name="how-secure-is-my-data"></a>Qual é o como protegem os meus dados?
Application Insights é um serviço do Azure. As políticas de segurança descritas o [segurança do Azure, privacidade e conformidade documento técnico](http://go.microsoft.com/fwlink/?linkid=392408).

Os dados são armazenados nos servidores do Microsoft Azure. Para contas no Portal do Azure, as restrições de conta são descritas no [documento de segurança do Azure, privacidade e conformidade](http://go.microsoft.com/fwlink/?linkid=392408).

O acesso aos seus dados pela equipa da Microsoft é restrito. Iremos aceder aos seus dados apenas com a sua permissão e se é necessário suportar a utilização do Application Insights. 

Dados agregados em todas as aplicações de todos os nossos clientes (por exemplo, taxas de dados e o tamanho médio de rastreios) são utilizados para melhorar o Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Telemetria de outra pessoa pode interferir com os meus dados do Application Insights?
Foi enviam telemetria adicional à sua conta utilizando a chave de instrumentação que pode ser encontrada no código das suas páginas web. Com a dados suficientes adicionais, métricas não corretamente representaria desempenho e a utilização da sua aplicação.

Se partilhar código com outros projetos, não se esqueça de remover a sua chave de instrumentação.

## <a name="is-the-data-encrypted"></a>Os dados são encriptados?
Não dentro os servidores no presente.

Todos os dados são encriptados são transmitidos entre centros de dados.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Os dados são encriptados em trânsito da minha aplicação para servidores do Application Insights?
Sim, iremos utilizar https para enviar dados para o portal de quase todos os SDKs, incluindo servidores web, dispositivos e páginas web HTTPS. A única exceção é os dados enviados a partir de páginas de web HTTP simples. 

## <a name="personally-identifiable-information"></a>Informações de identificação pessoal
#### <a name="could-personally-identifiable-information-pii-be-sent-to-application-insights"></a>Foi possível enviar informação identificativa (PII) para o Application Insights?
Sim, é possível. 

Como orientação geral:

* Telemetria mais standard (ou seja, telemetria enviada sem que escrever qualquer código) não inclui PII explícita. No entanto, poderá ser possível identificar os indivíduos ao inferência de uma coleção de eventos.
* Mensagens de exceção e rastreio podem conter PII
* Telemetria personalizada - ou seja, chamadas como TrackEvent escreve no código utilizando os rastreios de registo ou a API - pode conter quaisquer dados que escolher.

A tabela no final deste documento contém descrições mais detalhadas dos dados recolhidos.

#### <a name="am-i-responsible-for-complying-with-laws-and-regulations-in-regard-to-pii"></a>Pude responsável por complying com as leis e regulamentos in regard to PII?
Sim. É da responsabilidade do cliente, certifique-se de que a recolha e utilização de dados está em conformidade com as leis e regulamentos e com os termos do Microsoft Online Services.

Deve informar os seus clientes adequadamente sobre os dados que da aplicação recolhe e a forma como os dados são utilizados.

#### <a name="can-my-users-turn-off-application-insights"></a>Podem os meus utilizadores desativar Application Insights?
Não diretamente. Não, fornecemos um comutador que os utilizadores podem operar para desativar o Application Insights.

No entanto, pode implementar este tipo uma funcionalidade na sua aplicação. Todos os SDKs incluem uma definição de API que desativa a coleção de telemetria. 

#### <a name="my-application-is-unintentionally-collecting-sensitive-information-can-application-insights-scrub-this-data-so-it-isnt-retained"></a>A minha aplicação está a recolher inadvertidamente informações confidenciais. Application Insights limpar estes dados, de modo não é mantido?
Application Insights não filtrar ou eliminar os seus dados. Deve gerir corretamente os dados e evitar o envio desses dados para o Application Insights.

## <a name="data-sent-by-application-insights"></a>Dados enviados pelo Application Insights
Os SDKs variam entre plataformas e existem tem vários componentes que podem instalar. (Consulte [Application Insights - descrição geral][start].) Cada componente envia dados diferentes.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Classes de dados enviados em cenários diferentes
| A acção | Classes de dados recolhidos (consulte a tabela seguinte) |
| --- | --- |
| [Adicionar o Application Insights SDK para um projeto web do .NET][greenbrown] |ServerContext<br/>Inferido<br/>Contadores de desempenho<br/>Pedidos<br/>**Exceções**<br/>Sessão<br/>utilizadores |
| [Instalar o Monitor de estado no IIS][redfield] |Dependências<br/>ServerContext<br/>Inferido<br/>Contadores de desempenho |
| [Adicionar o Application Insights SDK para uma aplicação web Java][java] |ServerContext<br/>Inferido<br/>Pedir<br/>Sessão<br/>utilizadores |
| [Adicionar o SDK de JavaScript para a página web][client] |ClientContext <br/>Inferido<br/>Página<br/>ClientPerf<br/>AJAX |
| [Definir propriedades predefinidas][apiproperties] |**Propriedades** em todos os eventos padrão e personalizados |
| [Chamada TrackMetric][api] |Valores numéricos<br/>**Propriedades** |
| [Chamada controlar *][api] |Nome do evento<br/>**Propriedades** |
| [Chamada TrackException][api] |**Exceções**<br/>Captura de pilha<br/>**Propriedades** |
| SDK não é possível recolher dados. Por exemplo: <br/> -Não é possível aceder aos contadores de desempenho<br/> -exceção no inicializador de telemetria |Diagnóstico SDK |

Para [SDKs para outras plataformas][platforms], consulte os respetivos documentos.

#### <a name="the-classes-of-collected-data"></a>As classes de dados recolhidos
| Classe de dados recolhidos | Inclui (não uma lista exaustiva) |
| --- | --- |
| **Propriedades** |**Quaisquer dados - determinados pelo seu código** |
| DeviceContext |ID IP, região, o modelo de dispositivo, rede, o tipo de rede, o nome OEM, resolução do ecrã, instância de função, o nome de função, o tipo de dispositivo |
| ClientContext |SO, região, idioma, rede, resolução de janela |
| Sessão |id de sessão |
| ServerContext |Nome da máquina, região, OS dispositivos, sessão de utilizador, contexto de utilizador, a operação |
| Inferido |geolocalização do endereço IP, timestamp, SO, browser |
| Métricas |O nome da métrica e valor |
| Eventos |Nome de evento e o valor |
| PageViews |Nome de URL e página ou o nome de ecrã |
| Desempenho do cliente |Nome do URL/página, tempo de carregamento do browser |
| AJAX |Chamadas HTTP de página web para servidor |
| Pedidos |URL, a duração, código de resposta |
| Dependências |Tipo (SQL Server, HTTP,...), a cadeia de ligação ou URI, sincronização/async, duração, sucesso, instrução de SQL (com o Monitor de estado) |
| **Exceções** |Tipo de **mensagem**, chamar pilhas, ficheiros e a linha número, o id da thread de origem |
| Falhas |Id do processo, o id de processo principal, id de thread de falhas; patch de aplicação, id, compilação;  tipo de exceção, endereço, reason; ocultos símbolos e regista, endereços de início e de fim binários, nome binário e caminho, tipo de cpu |
| Rastreio |**Mensagem** e nível de gravidade |
| Contadores de desempenho |Tempo do processador, memória disponível, taxa de pedidos, taxa de exceção, bytes privados do processo, a taxa de e/s, duração de pedido, comprimento da fila de pedido |
| Disponibilidade |Código de resposta do teste Web, a duração de cada passo de teste, nome de teste, timestamp, sucesso, tempo de resposta, localização de teste |
| Diagnóstico SDK |Exceção ou mensagem de rastreio |

Pode [desactivar alguns dos dados, editando Applicationinsights][config]

## <a name="credits"></a>Créditos
Este produto inclui dados de GeoLite2 criados por MaxMind, disponível a partir do [http://www.maxmind.com](http://www.maxmind.com).



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

