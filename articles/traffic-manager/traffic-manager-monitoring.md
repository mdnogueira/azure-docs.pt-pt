---
title: "Monitorização de pontos finais do Traffic Manager do Azure | Microsoft Docs"
description: "Este artigo pode ajudá-lo a compreender como o Gestor de tráfego utiliza a monitorização de pontos finais e a ativação pós-falha do ponto final automática, para ajudar os clientes do Azure, implementar aplicações de elevada disponibilidade"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: fff25ac3-d13a-4af9-8916-7c72e3d64bc7
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2017
ms.author: kumud
ms.openlocfilehash: 3b30aa04854b779c25582abafc0f9ebba65b71ba
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="traffic-manager-endpoint-monitoring"></a>Monitorização de pontos finais do Gestor de tráfego

Traffic Manager do Azure inclui a monitorização de ponto final incorporado e ativação pós-falha do ponto final automática. Esta funcionalidade ajuda-o a fornecer aplicações de elevada disponibilidade que são resilientes a falhas de ponto final, incluindo falhas de região do Azure.

## <a name="configure-endpoint-monitoring"></a>Configurar a monitorização do ponto final

Para configurar o ponto final de monitorização, tem de especificar as seguintes definições no perfil do Traffic Manager:

* **Protocolo**. Escolha HTTP, HTTPS ou TCP como protocolo de que o Gestor de tráfego utiliza ao pesquisar o ponto final para verificar o estado de funcionamento. Monitorização de HTTPS não verifica se o certificado SSL não é válido – só verifica se o certificado está presente.
* **Porta**. Escolha a porta utilizada para o pedido.
* **Caminho**. Esta definição de configuração é válida apenas para os protocolos HTTP e HTTPS, para que o caminho de especificar a definição é necessária. Fornecer esta definição para a monitorização resultados de protocolo num erro TCP. Para o protocolo TCP, dê o caminho relativo e o nome da página Web ou o ficheiro que acede a monitorização. Uma barra (/) é uma entrada válida para o caminho relativo. Este valor implica que o ficheiro está no diretório de raiz (predefinição).
* **Intervalo de pesquisa**. Este valor Especifica a frequência de um ponto final está marcado para o seu estado de funcionamento de um agente de pesquisa do Gestor de tráfego. Pode especificar dois valores aqui: 30 segundos (pesquisa normal) e de 10 segundos (pesquisa rápida). Se não existem valores são fornecidos, o perfil define um valor predefinido de 30 segundos. Visite o [preços do Gestor de tráfego](https://azure.microsoft.com/pricing/details/traffic-manager) página para saber mais sobre os preços da pesquisa rápida.
* **Número de falhas de tolerated**. Este valor Especifica quantos falhas de um agente de pesquisa do Gestor de tráfego tolerates antes de marcar esse ponto final como estando danificado. O valor pode variar entre 0 e 9. Um valor de 0 significa que uma falha de monitorização único pode fazer com que a esse ponto final ser marcado como danificado. Se for especificado nenhum valor, utiliza o valor predefinido de 3.
* **Tempo limite de monitorização**. Esta propriedade especifica a quantidade de tempo que o agente de pesquisa do Gestor de tráfego deve aguardar antes de considerar que uma falha de verificação quando uma sonda de verificação do Estado de funcionamento é enviada para o ponto final. Se o intervalo de pesquisa é definido como 30 segundos, em seguida, pode definir o valor de limite de tempo entre 5 e 10 segundos. Se for especificado nenhum valor, utiliza um valor predefinido de 10 segundos. Se o intervalo de pesquisa está definido para 10 segundos, em seguida, pode definir o valor de limite de tempo entre 5 e 9 segundos. Se for especificado nenhum valor de tempo limite, utiliza um valor predefinido 9 segundos.

![Monitorização de pontos finais do Gestor de tráfego](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

**Figura 1: Monitorização de pontos finais de Gestor de tráfego**

## <a name="how-endpoint-monitoring-works"></a>Como funciona a monitorização de pontos finais

Se o protocolo de monitorização é definido como HTTP ou HTTPS, o agente de pesquisa do Gestor de tráfego faz um pedido GET para o ponto final utilizando o protocolo, porta e o caminho relativo especificado. Se voltar obtém uma resposta de 200 OK, esse ponto final é considerado em bom estado. Se a resposta é um valor diferente, ou, se não for recebida nenhuma resposta dentro do período de tempo limite especificado, em seguida, o Gestor de tráfego pesquisar agente tenta restabelecer, de acordo com a definição de Tolerated número de falhas (sem tenta restabelecer terminado se esta definição é 0). Se o número de falhas consecutivas é maior que a definição de Tolerated número de falhas, em seguida, esse ponto final está marcado como danificado. 

Se o protocolo de monitorização é TCP, o agente de pesquisa do Gestor de tráfego inicia um pedido de ligação de TCP utilizando a porta especificada. Se o ponto final responde ao pedido com uma resposta para estabelecer a ligação, que essa verificação do Estado de funcionamento está marcada como um êxito e o agente de pesquisa do Gestor de tráfego repõe a ligação de TCP. Se a resposta é um valor diferente ou se não for recebida nenhuma resposta dentro do período de tempo limite especificado, o Gestor de tráfego pesquisar agente tenta restabelecer, de acordo com a definição de Tolerated número de falhas (sem tenta restabelecer efetuada se esta definição é 0). Se o número de falhas consecutivas é maior que a definição de Tolerated número de falhas, em seguida, esse ponto final está marcado como danificado.

Em todos os casos, as sondas Gestor de tráfego de várias localizações e a determinação de falhas consecutivas acontece em cada região. Isto também significa que os pontos finais estão a receber sondas de estado de funcionamento do Gestor de tráfego com uma frequência superior à definição utilizada para o intervalo de pesquisa.

>[!NOTE]
>Para HTTP ou HTTPS monitorização protocolo, uma prática comum do lado do ponto final é implementar uma página na aplicação - por exemplo, /health.aspx personalizada. Utilizar este caminho para a monitorização, pode executar verificações específicas da aplicação, tais como a verificação de contadores de desempenho ou verificar a disponibilidade de base de dados. Com base nestas verificações personalizadas, a página devolve um código de estado HTTP adequado.

Todos os pontos finais num perfil do Traffic Manager partilham as definições de monitorização. Se precisar de utilizar as definições de monitorização diferentes para diferentes pontos finais, pode criar [aninhada perfis do Traffic Manager](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Estado de ponto final e perfil

Pode ativar e desativar perfis do Traffic Manager e os pontos finais. No entanto, uma alteração de estado de ponto final também poderão ocorrer como um resultado de Gestor de tráfego automatizada definições e processos.

### <a name="endpoint-status"></a>Estado de ponto final

Pode ativar ou desativar um ponto final específico. O serviço subjacente, o que poderá estar ainda em bom estado, não é afetado. Alterar o estado de ponto final controla a disponibilidade do ponto final do perfil do Traffic Manager. Quando um Estado de ponto final está desativado, o Gestor de tráfego não verifica se o seu estado de funcionamento e o ponto final não está incluído numa resposta DNS.

### <a name="profile-status"></a>Estado do perfil

Utilizar a definição do Estado do perfil, pode ativar ou desativar um perfil específico. Enquanto o estado de ponto final afeta um único ponto final, o estado do perfil afeta o perfil completo, incluindo todos os pontos finais. Ao desativar um perfil, os pontos finais não são verificados para Estado de funcionamento e não existem pontos finais são incluídos numa resposta DNS. Um [NXDOMAIN](https://tools.ietf.org/html/rfc2308) for devolvido o código de resposta para a consulta DNS.

### <a name="endpoint-monitor-status"></a>Estado do monitor de ponto final

Estado do monitor de ponto final é um valor gerado pelo Gestor de tráfego que mostra o estado do ponto final. Não é possível alterar esta definição manualmente. O estado do monitor de ponto final é uma combinação dos resultados da monitorização de pontos finais e o estado de ponto final configurado. Os valores possíveis de estado do monitor de ponto final são apresentados na tabela seguinte:

| Estado do perfil | Estado de ponto final | Estado do monitor de ponto final | Notas |
| --- | --- | --- | --- |
| Desativado |Ativado |Inativos |O perfil foi desativado. Apesar do Estado do ponto final está ativado, o estado do perfil (desativada) tem precedência. Pontos finais em perfis desativados não são monitorizados. É devolvido um código de resposta NXDOMAIN para a consulta DNS. |
| &lt;qualquer&gt; |Desativado |Desativado |O ponto final foi desativado. Pontos finais desativados não são monitorizados. O ponto final não está incluído em respostas DNS, por conseguinte, não receber tráfego. |
| Ativado |Ativado |Online |O ponto final está a ser monitorizado e está em bom estado. Está incluído nas respostas DNS e pode receber tráfego. |
| Ativado |Ativado |Atualmente degradado. |Verificações de estado de funcionamento de monitorização ponto final estão a falhar. O ponto final não está incluído em respostas DNS e não receber o tráfego. <br>Uma exceção é se todos os pontos finais estão degradados, caso em que todos eles são considerados a ser devolvido na resposta consulta).</br>|
| Ativado |Ativado |CheckingEndpoint |O ponto final está a ser monitorizado, mas os resultados da primeira sonda ainda não foram recebidos. CheckingEndpoint é um estado temporário que normalmente ocorre imediatamente após a adição ou ativar um ponto final do perfil. Um ponto final neste estado está incluído nas respostas DNS e pode receber tráfego. |
| Ativado |Ativado |Parada |A aplicação de web ou serviço em nuvem que o ponto final aponta para não está em execução. Verifique as definições de aplicação de web ou serviço em nuvem. Isto também pode acontecer se o ponto final do ponto final de tipo aninhado e o perfil subordinado está desativado ou está inativo. <br>Um ponto final com um estado parado, não é monitorizado. Este não está incluída em respostas DNS e não receber o tráfego. Uma exceção é se todos os pontos finais estão degradados, caso em que todos eles serão considerados para ser devolvido em resposta a consulta.</br>|

Para obter mais informações sobre como monitorizar estado do ponto final é calculado para pontos finais aninhados, consulte [aninhada perfis do Traffic Manager](traffic-manager-nested-profiles.md).

### <a name="profile-monitor-status"></a>Estado do monitor de perfil

O estado do monitor de perfil é uma combinação do Estado do perfil configurado e os valores de estado do monitor de ponto final para todos os pontos finais. Os valores possíveis são descritos na tabela seguinte:

| Estado do perfil (conforme configurado) | Estado do monitor de ponto final | Estado do monitor de perfil | Notas |
| --- | --- | --- | --- |
| Desativado |&lt;qualquer&gt; ou um perfil com não existem pontos finais definidos. |Desativado |O perfil foi desativado. |
| Ativado |O estado de, pelo menos, um ponto final está atualmente degradado. |Atualmente degradado. |Reveja os valores de estado de ponto final individuais para determinar quais os pontos finais mais necessitam de atenção. |
| Ativado |O estado de, pelo menos, um ponto final está Online. Não existem pontos finais de ter o estado Degraded. |Online |O serviço está a aceitar o tráfego. É necessária nenhuma ação adicional. |
| Ativado |O estado de, pelo menos, um ponto final é CheckingEndpoint. Não existem pontos finais estão num Estado Online ou Degraded. |CheckingEndpoints |Este estado de transição ocorre quando um perfil se criadas ou ativadas. O estado de funcionamento do ponto final está a ser verificado pela primeira vez. |
| Ativado |Os Estados de todos os pontos finais no perfil são desativado ou parado ou o perfil não tem definidas pontos finais. |Inativos |Não existem pontos finais estão ativos, mas o perfil ainda está ativado. |

## <a name="endpoint-failover-and-recovery"></a>Ponto final de ativação pós-falha e recuperação

Gestor de tráfego verifica periodicamente o estado de funcionamento de cada ponto final, incluindo pontos finais mau estado de funcionamento. Gestor de tráfego Deteta quando um ponto final fica bom estado de funcionamento e coloca-os novamente em rotação.

Um ponto final está danificado quando qualquer um dos seguintes eventos ocorrem:
- Se o protocolo de monitorização é HTTP ou HTTPS:
    - Foi recebida uma resposta de não 200 (incluindo um código de 2xx diferente ou um 301/302 Redireccionamento).
- Se o protocolo de monitorização é TCP: 
    - Foi recebida uma resposta que não sejam ACK ou SIN ACK em resposta ao pedido de SINCRONIZAÇÃO enviado pelo Gestor de tráfego para a tentativa de estabelecimento de ligação.
- Tempo limite. 
- Qualquer outro problema de ligação à resultante no ponto final a não ser acessível.

Para obter mais informações sobre as verificações de falha na resolução de problemas, consulte [estado de resolução de problemas atualmente degradado. % no Gestor de tráfego do Azure](traffic-manager-troubleshooting-degraded.md). 

A seguinte linha de tempo na figura 2 é uma descrição detalhada do processo de monitorização do ponto final do Gestor de tráfego que tem as seguintes definições: a monitorização de protocolo está HTTP, o intervalo de pesquisa é 30 segundos, número de falhas tolerated é 3, valor de tempo limite é de 10 segundos e o TTL de DNS é 30 segundos.

![Sequência de ativação pós-falha e a reativação pós-falha de ponto final do Gestor de tráfego](./media/traffic-manager-monitoring/timeline.png)

**Figura 2: Tráfego manager ponto final de ativação pós-falha e recuperação sequência**

1. **OBTER**. Para cada ponto final, o Gestor de tráfego de sistema de monitorização efetua um pedido GET o caminho especificado nas definições de monitorização.
2. **200 OK**. O sistema de monitorização espera que uma mensagem de HTTP 200 OK devem ser devolvidos dentro de 10 segundos. Quando recebe esta resposta, reconhece que o serviço está disponível.
3. **30 segundos, entre as verificações**. A verificação de estado de funcionamento do ponto final é repetida a cada 30 segundos.
4. **Serviço indisponível**. O serviço de fica indisponível. Gestor de tráfego não saberá que até a verificação de estado de funcionamento seguinte.
5. **Tenta aceder ao caminho de monitorização**. O sistema de monitorização efetua um pedido GET, mas não recebeu uma resposta dentro do período de tempo limite de 10 segundos (em alternativa, uma resposta de 200 não pode ser recebida). Em seguida, tenta três vezes mais, intervalos de 30 segundo. Se um da tenta for bem sucedido, o número de tentativas é reposto.
6. **Estado definido como Degraded**. Após uma falha de consecutivo quarta, o sistema de monitorização marca o estado de ponto final indisponível como Degraded.
7. **O tráfego é diverted para outros pontos finais**. Os servidores de nomes DNS do Gestor de tráfego são atualizados e Gestor de tráfego já não devolve o ponto final em resposta a consultas DNS. Novas ligações são direcionadas para outros, pontos finais disponíveis. No entanto, as respostas DNS anteriores que incluem este ponto final podem ainda ser colocadas em cache por clientes DNS e servidores DNS recursiva. Os clientes continuam a utilizar o ponto final até expira a cache DNS. Como a cache DNS expira, os clientes se novas consultas DNS e são direcionados para diferentes pontos finais. A duração da cache é controlada pela definição TTL no perfil do Gestor de tráfego, por exemplo, 30 segundos.
8. **Estado de funcionamento verifica continuar**. Gestor de tráfego continua a verificar o estado de funcionamento do ponto final, enquanto tiver um Estado de Degraded. Gestor de tráfego Deteta quando o ponto final regressar ao estado de funcionamento.
9. **Serviço de volta a ficar online**. O serviço torna-se disponível. O ponto final mantém o estado de Degraded no Traffic Manager até que o sistema de monitorização efetua a verificação de estado de funcionamento seguinte.
10. **Tráfego de retoma do serviço**. Gestor de tráfego envia um obter pedido e recebe uma resposta de 200 estado OK. O serviço devolveu a um bom estado. Os servidores de nomes do Gestor de tráfego são atualizados e começam a entregar o nome DNS do serviço em respostas DNS. Tráfego devolve para o ponto final como as respostas em cache de DNS que retorno outros pontos finais expirem e, como ligações existentes a outros pontos finais estão terminados.

    > [!NOTE]
    > Porque o Gestor de tráfego funciona ao nível do DNS, não pode influenciar a ligações existentes a qualquer ponto final. Quando que direciona o tráfego entre pontos finais (a pelas definições de perfil alteradas ou durante a ativação pós-falha ou de reativação pós-falha), o Gestor de tráfego direciona novas ligações a pontos finais disponíveis. No entanto, outros pontos finais poderão continuar a receber tráfego através de ligações existentes enquanto essas sessões estão terminadas. Para ativar o tráfego drenar de ligações existentes, as aplicações devem limitar a duração de sessão utilizada com cada ponto final.

## <a name="traffic-routing-methods"></a>Métodos de encaminhamento de tráfego

Quando um ponto final tem um Estado de Degraded, já não é devolvido em resposta a consultas DNS. Em vez disso, um ponto final alternativo é escolhido e devolvido. O método de encaminhamento de tráfego configurado no perfil determina a forma como o ponto final alternativo é escolhido.

* **Prioridade**. Pontos finais formam uma lista prioritária. O primeiro ponto de final disponível na lista sempre é devolvido. Se um Estado de ponto final está atualmente degradado, em seguida, é devolvido o próximo ponto final disponível.
* **Ponderado**. Qualquer ponto final disponível é escolhido aleatoriamente com base nas respetivas importâncias atribuídas e as ponderações de outros disponíveis pontos finais.
* **Desempenho**. O ponto final mais próximo para o utilizador final é devolvido. Se esse ponto final não estiver disponível, o Gestor de tráfego move o tráfego para os pontos finais na região do Azure mais próxima seguinte. Pode configurar planos de ativação pós-falha alternativo para o encaminhamento de tráfego de desempenho utilizando [aninhada perfis do Traffic Manager](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Geográfica**. O ponto final mapeado para servir a localização geográfica, com base no pedido de consulta é devolvido do IP. Se esse ponto final não estiver disponível, outro ponto final não será selecionado como a ativação pós-falha, uma vez que uma localização geográfica pode ser mapeada apenas para um ponto final num perfil (estão mais detalhes no [FAQ](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)). Como melhor prática, ao utilizar o encaminhamento geográfica, recomendamos que clientes utilizem aninhada perfis do Traffic Manager com mais do que um ponto final, como os pontos finais do perfil.

Para obter mais informações, consulte [métodos de encaminhamento de tráfego do Traffic Manager](traffic-manager-routing-methods.md).

> [!NOTE]
> Ocorre uma exceção ao comportamento de encaminhamento de tráfego normal quando todos os pontos finais elegíveis têm um Estado degradado. Torna do Gestor de tráfego "melhor esforço" de tentativa e *responde como se todos os os Degraded Estado pontos finais, na verdade, estão num estado online*. Este comportamento é preferível a alternativa que seria para não devolver qualquer ponto final na resposta DNS. Desativado ou pontos finais de parado não são monitorizados, por conseguinte, que não são consideradas elegível para o tráfego.
>
> Esta condição normalmente é causada por uma configuração incorreta do serviço, tais como:
>
> * Verifica uma lista de controlo de acesso [ACL] bloquear o estado de funcionamento do Gestor de tráfego.
> * Uma configuração incorreta de porta ou protocolo no perfil do Gestor de tráfego de monitorização.
>
> Consequence deste comportamento é que se as verificações de estado de funcionamento do Gestor de tráfego não estão configuradas corretamente, poderá ser apresentada de tráfego de encaminhamento como apesar do Traffic Manager *é* a funcionar corretamente. No entanto, neste caso, ativação pós-falha do ponto final não pode acontecer que afeta a disponibilidade da aplicação global. É importante verificar que o perfil mostra um Estado Online, não é um Estado de Degraded. Um Estado Online indica que as verificações de estado de funcionamento do Traffic Manager estão a funcionar conforme esperado.

Para obter mais informações sobre resolução de problemas de falhas de verificação de estado de funcionamento, consulte [estado de resolução de problemas atualmente degradado. % no Gestor de tráfego do Azure](traffic-manager-troubleshooting-degraded.md).



## <a name="next-steps"></a>Passos seguintes

Saiba [como funciona o Gestor de tráfego](traffic-manager-how-traffic-manager-works.md)

Saiba mais sobre o [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md) suportada pelo Gestor de tráfego

Saiba como [criar um perfil de Gestor de tráfego](traffic-manager-manage-profiles.md)

[Resolver problemas de estado de Degraded](traffic-manager-troubleshooting-degraded.md) um ponto de final do Gestor de tráfego
