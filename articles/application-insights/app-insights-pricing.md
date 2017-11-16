---
title: "Gerir o volume de dados e preços para o Azure Application Insights | Microsoft Docs"
description: Volumes de telemetria de gerir e monitorizar os custos no Application Insights.
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: mbullwin
ms.openlocfilehash: ecb6dd0343c36a0f1571b416817aad5e7a52fccb
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Gerir o volume de preços e os dados no Application Insights


Preços para [Azure Application Insights] [ start] baseia-se no volume de dados por aplicação. Baixa utilização durante a programação ou para uma aplicação pequena é provável que seja livre, porque não há um allowance mensal de 1 GB de dados de telemetria.

Cada recurso do Application Insights é cobrado como um serviço separado e contribui para a fatura para a sua subscrição para o Azure.

Existem dois planos de preços. O plano predefinido é denominado básico. Pode optar pelas para o plano de empresa, que tem cobrada uma taxa diária, mas permite que determinadas funcionalidades adicionais, tais como [a exportação contínua](app-insights-export-telemetry.md).

Se tiver dúvidas sobre como preços funciona para o Application Insights, não hesite em publicar uma pergunta nos nossos [fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights). 

## <a name="the-price-plans"></a>Os planos de preços

Consulte o [página de preços do Application Insights] [ pricing] para os preços atuais na moeda.

### <a name="basic-plan"></a>Plano básico

O plano básico é o predefinido quando é criado um novo recurso do Application Insights e será suffice para a maioria dos clientes.

* No plano básico, são-lhe cobrados pelo volume de dados: número de bytes de telemetria recebida pelo Application Insights. Volume de dados é medido como o tamanho do pacote de dados JSON descomprimido recebido pelo Application Insights da sua aplicação.
Para [importados para análise de dados de tabela](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), o volume de dados é medido como o tamanho descomprimido de ficheiros enviados para o Application Insights.  
* O primeiro 1 GB para cada aplicação é gratuito, pelo que se estiver a apenas conseguirmos uma ou desenvolver, pode não ter de pagar.
* [Transmissão de velocidade de métricas](app-insights-live-stream.md) dados não estão contabilizados para fins de preço.
* [A exportação contínua](app-insights-export-telemetry.md) está disponível para um custo por GB adicional no plano básico.

### <a name="enterprise-plan"></a>Plano empresarial

* O plano de empresa, a aplicação pode utilizar todas as funcionalidades do Application Insights. [A exportação contínua](app-insights-export-telemetry.md) e 

[Conector de análise de registo](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) estão disponíveis sem qualquer custo adicional no plano de empresa.
* Paga por nó que está a enviar telemetria para todas as aplicações no plano de empresa. 
 * A *nó* é um computador do servidor físico ou virtual ou uma instância de função de plataforma-como-um-serviço, o que aloja a aplicação.
 * Máquinas de desenvolvimento, os browsers cliente e dispositivos móveis não são contabilizados como nós.
 * Se a aplicação tiver vários componentes que enviam telemetria, como um serviço web e de um trabalho de back-end, estes são contadas separadamente.
 * [Transmissão de velocidade de métricas](app-insights-live-stream.md) dados não estão contados para os preços purposes.* através de uma subscrição, os encargos são por nó, não por aplicação. Se tiver cinco nós que envia a telemetria para 12 aplicações, em seguida, os encargos destina-se a cinco nós.
* Embora os encargos são quoted por mês, está a cobrados apenas para qualquer hora em que um nó envia telemetria a partir de uma aplicação. A taxa de hora a hora é a taxa mensal delimitado por aspas / 744 (o número de horas num mês de 31 dias).
* Para cada nó detetado (com granularidade horária) recebe uma atribuição de volume de dados de 200 MB por dia. Alocação de dados não utilizados não é executada de um dia para o seguinte.
 * Se escolher a opção de preços do Enterprise, cada subscrição recebe um allowance diária de dados com base no número de nós que envia a telemetria para os recursos do Application Insights nessa subscrição. Por isso, se tiver 5 nós enviar dados de todos os dias, terá um allowance agrupado de 1 GB aplicada a todos os recursos do Application Insights nessa subscrição. É irrelevante se determinados nós estão a enviar mais dados dos outros nós porque os dados incluídos são partilhados entre todos os nós. Se, num determinado dia, os recursos do Application Insights recebem mais dados do que está incluído na atribuição de dados diárias para esta subscrição, aplicam-se os encargos de dados excedido por GB. 
 * O diário allowance de dados é calculada como o número de horas do dia (utilizando a UTC) que cada nó está a enviar telemetria dividida pelo 200 MB de 24 horas. Para que o se tiver de 4 nós que envia a telemetria durante 15 de 24 horas do dia, os dados incluídos para esse dia seria ((4 x 15) / 24) x 200 MB = 500 MB. O preço 2.30 EUR por GB para excesso de dados, a taxa seria EUR 1.15 se os nós de envio de 1 GB de dados nesse dia.
 * Tenha em atenção que allowance diária o plano de empresa não está partilhado com aplicações para o qual tiver escolhido a opção básica e allowance não utilizada não transportado através de rotina. 
* Seguem-se alguns exemplos de determinar o número de nós distintas:
| Cenário                               | Contagem total de nó diária |
|:---------------------------------------|:----------------:|
| 1 aplicação está a utilizar 3 instâncias do App Service do Azure e o servidor virtual 1 | 4 |
| 3 aplicações em execução no 2 VMs e os recursos do Application Insights para estas aplicações estão na mesma subscrição e no plano de Enterprise | 2 | 
| 4 aplicações cujos recursos de informações de aplicações estão na mesma subscrição. Cada aplicação é executada 2 instâncias 16 horas de ponta e 4 instâncias durante 8 horas de pico. | 13.33 | 
| Serviços em nuvem com a função de trabalho 1 e 1 função da Web, cada 2 instâncias em execução | 4 | 
| executar 50 microserviços, cada serviço de micro 3 instâncias em execução de Cluster do nó de 5 Service Fabric | 5|

* O nó preciso contando comportamento depende em que a aplicação do Application Insights SDK está a utilizar. 
  * Em versões do SDK 2.2 e em diante, tanto o Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) ou [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) será relatório cada anfitrião de aplicações como um nó, por exemplo, o nome do computador para o servidor físico e anfitriões de VM ou o nome da instância no caso de serviços em nuvem.  A única exceção é aplicações utilizando apenas [.NET Core](https://dotnet.github.io/) e o Application Insights Core SDK, na qual maiúsculas apenas um nó será reportado para todos os anfitriões porque o nome do anfitrião não está disponível. 
  * Para versões anteriores do SDK, o [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) irão comportar-se tal como as versões mais recentes do SDK, no entanto, a [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) comunicará apenas um nó, independentemente do número de anfitriões de aplicação real. 
  * Tenha em atenção que se a aplicação estiver a utilizar o SDK ao definir roleInstance para um valor personalizado, por predefinição esse mesmo valor será utilizado para determinar o número de nós. 
  * Se estiver a utilizar uma nova versão do SDK com uma aplicação que é executada a partir de dispositivos móveis ou computadores cliente, é possível que a contagem de nós pode devolver um número que é muito grande (a partir de elevado número de dispositivos móveis ou computadores cliente). 

### <a name="multi-step-web-tests"></a>Testes Web com vários passos

Há um custo adicional para [testes web com vários passos](app-insights-monitor-web-app-availability.md#multi-step-web-tests). Isto refere-se para testes web que execute uma sequência de ações. 

Não há sem qualquer encargo separado para 'os testes de ping' de uma única página. A telemetria de testes de ping e testes de vários passos é cobrada juntamente com outra telemetria da sua aplicação.
 
## <a name="operations-management-suite-subscription-entitlement"></a>Elegibilidade de subscrição do Operations Management Suite

Como [anunciou recentemente](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/), os clientes que comprar E1 do Microsoft Operations Management Suite e E2 conseguem obter o Application Insights empresarial como um componente sem custos adicionais. Especificamente, cada unidade de Operations Management Suite E1 e E2 inclui uma elegibilidade para 1 nó do plano empresarial do Application Insights. Conforme indicado acima, cada nó do Application Insights inclui até 200 MB de dados ingeridos por dia (separado da ingestão de dados de análise de registos), com retenção de dados de 90 dias sem custos adicionais. 

> [!NOTE]
> Para garantir que recebem esta elegibilidade, tem de ter os recursos do Application Insights na empresa plano de preços. Esta elegibilidade aplica-se apenas como nós de, pelo que os recursos do Application Insights no plano básico não serão tenha em consideração qualquer benefício em. Tenha em atenção que esta elegibilidade não ficará visível nos custos estimados apresentados na funcionalidades + preços do painel. 
>
 
## <a name="review-pricing-plans-and-estimate-costs"></a>Reveja os planos de preços e estimar os custos

Applicaition Insights torna mais fácil de compreender os planos de preços disponíveis e que os custos são provável que ser com base nos padrões de utilização recente. Comece por abrir o **funcionalidades + preços** painel no recurso do Application Insights no portal do Azure:

![Escolha o preço.](./media/app-insights-pricing/01-pricing.png)

**a.** Reveja o volume de dados para o mês. Isto inclui todos os dados recebidos e mantidos (após qualquer [amostragem](app-insights-sampling.md) do seu servidor e as aplicações cliente e de testes de disponibilidade.

**b.** É efetuada cobrada uma taxa separada [testes web com vários passos](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (Isto não incluir testes de disponibilidade simples, que estão incluídos na taxa de volume de dados.)

**c.** Ative o plano de empresa.

**d.** Clicar para dados opções de gestão para ver o volume de dados para o último mês, definir um limite diário ou defina a amostragem da ingestão.

Custos do Application Insights são adicionados à fatura do Azure. Pode ver os detalhes do seu Azure cobrar na secção de faturação do portal do Azure ou no [Azure Billing Portal](https://account.windowsazure.com/Subscriptions). 

![No menu do lado, escolha a faturação.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Taxa de dados
Existem três formas em que o volume de enviar dados está limitado:

* **A amostragem:** pode ser utilizado este mecanismo de reduzir a quantidade de telemetria enviada das suas aplicações de servidor e cliente, com distortion mínima de métricas. Esta é a principal ferramenta que terá de ajustar a quantidade de dados. Saiba mais sobre [amostragem funcionalidades](app-insights-sampling.md). 
* **Cap diária:** quando criar um recurso do Application Insights do portal do Azure está definido para 100 GB diário. A predefinição quando criar um recurso do Application Insights do Visual Studio, é pequeno (apenas 32.3 MB/dia) destina-se apenas a testar faciliate. Neste caso, destina-se que o utilizador irá elevar o limite diário antes de implementar a aplicação em produção. O limite máximo é diário 1000 GB, a menos que pediu um máximo superior para uma aplicação de tráfego elevado. Utilizar cuidado ao definir a cap diária, como deve ser a sua intenção **nunca para atingiu o limite diário**, porque, em seguida, irá perder dados para o resto do dia e não conseguir monitorizar a sua aplicação. Para alterá-lo, utilize o painel de extremidade de volume diária, ligações a partir do painel de gestão de volumes de dados (ver abaixo). Tenha em atenção que alguns tipos de subscrição tem crédito que não pode ser utilizado para o Application Insights. Se a subscrição tem um limite de gastos, o painel de extremidade diária tem instruções como removê-lo e ativar a cap diária gerado para além do diário 32.3 MB.  
* **Limitação:** Isto limita a taxa de dados para k 32 eventos por segundo, apresentou uma média superior a 1 minuto. 


*O que acontece se a taxa de limitação de exceder a minha aplicação?*

* O volume de dados que a aplicação envia é avaliado a cada minuto. Se exceder a taxa de por segundo média o minuto, o servidor recusa-se alguns pedidos. O SDK coloca os dados e, em seguida, tenta reenviar, propagando-se com um aumento através de alguns minutos. Se a aplicação consistentemente envia dados em acima a taxa de limitação, alguns dados serão ignorados. (O ASP.NET, Java e JavaScript SDKs tentarem Reenviar desta forma; outros SDKs poderão simplesmente largar limitada dados). Se ocorrer a limitação, verá uma notificação de aviso se isto tiver ocorrido.

*Como sei a minha aplicação está a enviar quantidade de dados?*

* Abra o **gestão de volumes de dados** painel para ver o gráfico de volume de dados diária. 
* Ou, no Explorador de métricas, adicione um novo gráfico e selecione **volume do ponto de dados** como a métrica. Comutador no agrupamento e agrupar por **tipo de dados**.

## <a name="to-reduce-your-data-rate"></a>Para reduzir a velocidade de dados
Eis algumas coisas que pode fazer para reduzir o volume de dados:

* Utilize [amostragem](app-insights-sampling.md). Esta tecnologia reduz a taxa de dados sem skewing métricas e sem perturbar a capacidade de navegar entre itens relacionados na pesquisa. Nas aplicações de servidor, funciona automaticamente.
* [Limitar o número de chamadas Ajax que podem ser comunicadas](app-insights-javascript.md#detailed-configuration) em cada vista de página ou comutador desativar relatórios de Ajax.
* Desactivar módulos de coleção não precisa de por [editar Applicationinsights](app-insights-configuration-with-applicationinsights-config.md). Por exemplo, poderá decidir se os contadores de desempenho ou dados de dependência são inessential.
* Divida a telemetria para separar as chaves de instrumentação. 
* Pré-agrega as métricas. Se tiver coloca chamadas para TrackMetric na sua aplicação, pode reduzir o tráfego utilizando a sobrecarga que aceita o cálculo da média e o desvio-padrão de um lote de medidas. Ou pode utilizar um [previamente agregar pacote](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="managing-the-maximum-daily-data-volume"></a>Gerir o volume de dados diária máximo

Pode utilizar a extremidade de volume diária para limitar os dados recolhidos, mas se a extremidade for cumprida, irá resultar em perda de todos os telemetery enviados a partir da sua aplicação para o resto do dia. É **não recomendado** ter a sua aplicação para atingiu o limite diário, uma vez que não é possível controlar o estado de funcionamento e desempenho da aplicação depois é atingido. 

Em alternativa, utilize [amostragem](app-insights-sampling.md) para otimizar o volume de dados para o nível de seria como e utilizar a cap diária apenas como um "último recurso", no caso da aplicação começa a enviar muito superiores dos volumes de telemetery inesperadamente. 

Para alterar a cap diária, na secção de configuração do seu recurso Insihgts de aplicação, clique em **gestão de volumes de dados** , em seguida, **extremidade diária**.

![Ajustar a extremidade de volume de telemetria diária](./media/app-insights-pricing/daily-cap.png) 

## <a name="sampling"></a>Amostragem
[Amostragem](app-insights-sampling.md) é um método de reduzir a velocidade a que a telemetria é enviada para a sua aplicação, mantendo a capacidade para encontrar eventos relacionados durante pesquisas de diagnóstico, e ainda manter evento correto contagens. 

A amostragem é uma forma eficaz para reduzir os custos e permanecer dentro da sua quota mensal. O algoritmo de amostragem mantém relacionadas com itens de telemetria, para que, por exemplo, quando utilizar a pesquisa, pode encontrar o pedido relacionadas com uma exceção específica. O algoritmo mantém também contagens corretas, para que pode ver os valores corretos no Explorador de métrica de taxas de pedidos, taxas de exceção e outras contagens.

Existem várias formas de amostragem.

* [Amostragem adaptável](app-insights-sampling.md) é a predefinição para o SDK do ASP.NET, que se ajusta automaticamente para o volume de telemetria que envia a sua aplicação. Funciona automaticamente no SDK na sua aplicação web, para que o tráfego de telemetria na rede é reduzido. 
* *A amostragem de ingestão* é uma alternativa que opera no ponto onde a telemetria da sua aplicação introduz o serviço do Application Insights. Não afeta o volume de telemetria enviado a partir da sua aplicação, mas reduz o volume mantido pelo serviço. Pode utilizá-lo a reduzir a quota gastar pela telemetria de outros SDKs e browsers.

Para configurar a amostragem de ingestão, defina o controlo no painel de preços:

![A Quota e o painel preço, clique no mosaico de exemplos e selecione uma fração de amostragem.](./media/app-insights-pricing/04.png)

> [!WARNING]
> O painel de amostragem de dados controla apenas o valor da amostragem da ingestão. Esta não reflete a frequência de amostragem que está a ser aplicada pelo Application Insights SDK na sua aplicação. Se a telemetria de entrada já amostragem no SDK, amostragem de ingestão não está aplicada.
> 

Para detetar a frequência de amostragem real, independentemente de onde foi aplicado, utilize um [consulta de análise](app-insights-analytics.md) como esta:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

Em cada retidos registo, `itemCount` indica o número de registos originais que representa, igual a 1 + o número de registos rejeitados anteriores. 


## <a name="automation"></a>Automatização

Pode escrever um script para definir o plano de preços, utilizando a gestão de recursos do Azure. [Saiba como](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Resumo de limites
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>Passos seguintes

* [Amostragem](app-insights-sampling.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

