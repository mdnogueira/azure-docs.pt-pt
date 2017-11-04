---
title: Traffic Manager do Azure - perguntas mais frequentes | Microsoft Docs
description: "Este artigo fornece respostas às perguntas mais frequentes sobre o Gestor de tráfego"
services: traffic-manager
documentationcenter: 
author: KumudD
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2017
ms.author: kumud
ms.openlocfilehash: eac9c3c2b7fde4ac225e17cc3b98ca5ee926c3b3
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Gestor de tráfego perguntas mais frequentes (FAQ)

## <a name="traffic-manager-basics"></a>Noções básicas do Gestor de tráfego

### <a name="what-ip-address-does-traffic-manager-use"></a>O endereço IP utilizar o Gestor de tráfego?

Conforme explicado no [como Gestor de tráfego funciona](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Gestor de tráfego funciona ao nível do DNS. Envia as respostas DNS para direcionar clientes para o ponto final de serviço apropriado. Os clientes, em seguida, ligam ao ponto final do serviço diretamente, não através do Gestor de tráfego.

Por conseguinte, o Gestor de tráfego não fornece um ponto final ou o endereço IP para os clientes se liguem a. Se pretender que o endereço IP estático para o seu serviço, o que deve ser configurado no serviço, não no Traffic Manager.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Gestor de tráfego suporta sessões 'temporária'?

Conforme explicado no [como Gestor de tráfego funciona](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Gestor de tráfego funciona ao nível do DNS. Utiliza as respostas DNS para direcionar clientes para o ponto final de serviço apropriado. Os clientes ligam ao ponto final do serviço diretamente, não através do Gestor de tráfego. Por conseguinte, Gestor de tráfego não consegue ver o tráfego HTTP entre o cliente e o servidor.

Além disso, pertence o endereço IP de origem da consulta DNS recebida pelo Gestor de tráfego para o serviço DNS recursiva, não o cliente. Por conseguinte, o Gestor de tráfego não tem nenhuma forma de controlar clientes individuais e não é possível implementar 'temporária' sessões. Esta limitação é comum para todos os sistemas de gestão de tráfego com base no DNS e não é específica para o Gestor de tráfego.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Porque estou a ver um erro HTTP ao utilizar o Gestor de tráfego?

Conforme explicado no [como Gestor de tráfego funciona](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Gestor de tráfego funciona ao nível do DNS. Utiliza as respostas DNS para direcionar clientes para o ponto final de serviço apropriado. Os clientes, em seguida, ligam ao ponto final do serviço diretamente, não através do Gestor de tráfego. Gestor de tráfego não Consulte tráfego HTTP entre cliente e servidor. Por conseguinte, qualquer erro HTTP que vir tem ser provenientes da aplicação. Para o cliente ligar à aplicação, todos os passos de resolução DNS estão concluídos. Que inclui qualquer interação do Gestor de tráfego com no fluxo de tráfego de aplicações.

Investigação adicional, por conseguinte, deve focar-se na aplicação.

O cabeçalho de anfitrião HTTP enviado do browser do cliente é a origem dos problemas mais comuns. Certifique-se de que a aplicação está configurada para aceitar o cabeçalho de anfitrião correto para o nome de domínio que está a utilizar. Para pontos finais utilizando o App Service do Azure, consulte [configurar um nome de domínio personalizado para uma aplicação web no serviço de aplicações do Azure utilizando o Gestor de tráfego](../app-service/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>O que é o impacto do desempenho da utilização do Gestor de tráfego?

Conforme explicado no [como Gestor de tráfego funciona](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Gestor de tráfego funciona ao nível do DNS. Uma vez que os clientes ligam diretamente ao seu pontos finais de serviço, não há nenhum impacto no desempenho tarifas ao utilizar o Gestor de tráfego assim que a ligação for estabelecida.

Uma vez que o Gestor de tráfego integra-se com aplicações ao nível do DNS, requer uma pesquisa DNS adicional inseridos na cadeia de resolução de DNS. O impacto do Gestor de tráfego no tempo de resolução DNS é mínimo. Gestor de tráfego utiliza uma rede global de servidores de nomes e utiliza [anycast](https://en.wikipedia.org/wiki/Anycast) de rede para garantir que o DNS consultas são sempre encaminhadas para o servidor de nome disponível mais próximo. Além disso, a colocação em cache de respostas DNS significa que a latência DNS adicional gasta utilizando o Gestor de tráfego só se aplica a fração de sessões.

O método de desempenho encaminha o tráfego para o ponto de final disponível mais próximo. O resultado net é que o impacto de desempenho global associado este método deverá ser mínimo. Algum aumento na latência DNS deve ser deslocamento pelo menor latência de rede para o ponto final.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Os protocolos de aplicação posso utilizar com o Gestor de tráfego?

Conforme explicado no [como Gestor de tráfego funciona](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Gestor de tráfego funciona ao nível do DNS. Depois de concluída a pesquisa de DNS, os clientes ligam ao ponto final da aplicação diretamente, não através do Gestor de tráfego. Por conseguinte, a ligação pode utilizar qualquer protocolo de aplicação. Se selecionar TCP como o monitorização, Gestor de tráfego do protocolo monitorização de estado de funcionamento do ponto final pode ser realizada sem utilizar quaisquer protocolos de aplicação. Se optar por ter o estado de funcionamento verificado através de um protocolo de aplicação, o ponto final tem de ser capazes de responder a pedidos de HTTP ou HTTPS obter.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Pode utilizar o Gestor de tráfego com um nome de domínio 'naked'?

Não. As normas DNS não permitem a CNAMEs coexista com outros registos DNS com o mesmo nome. O vértice (ou raiz) de uma zona DNS sempre contém dois registos DNS pré-existente; o SOA e os registos NS autoritativos. Isto significa que não pode ser criado um registo CNAME no vértice da zona sem violar as normas DNS.

Gestor de tráfego necessita de um registo CNAME no DNS para mapear o nome DNS intuitivos. Por exemplo, mapear www.contoso.com para o contoso.trafficmanager.net de nome DNS de perfil do Traffic Manager. Além disso, o perfil do Traffic Manager devolve um CNAME DNS segundo para indicar que o cliente deve ligar ao ponto final do.

Para contornar este problema, recomendamos que utilize um redirecionamento HTTP para o tráfego direto do nome do domínio naked para um URL diferente, o que, em seguida, pode utilizar o Gestor de tráfego. Por exemplo, o domínio naked 'contoso.com' pode redirecionar utilizadores para o CNAME "www.contoso.com" que aponte para o nome DNS do Gestor de tráfego.

Suporte integral para domínios naked no Gestor de tráfego é controlado no nosso registo de segurança de funcionalidade. Pode registar o suporte para este pedido de funcionalidade por [votar para o mesmo no site de comentários nossa comunidade](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Gestor de tráfego considerar o endereço de sub-rede de cliente ao processamento de consultas DNS? 
Sim, para além do endereço IP de origem da consulta DNS recebe (que é normalmente o endereço IP de resolução de DNS), quando efetuar pesquisas de métodos de encaminhamento de desempenho e Geographic, o Gestor de tráfego considera também o endereço de sub-rede do cliente se for incluído na consulta, a resolução de efetuar o pedido em nome do utilizador final.  
Especificamente, [RFC 7871 – sub-rede de cliente em consultas de DNS](https://tools.ietf.org/html/rfc7871) que fornece um [mecanismo de extensão para o DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) que podem transmitir o endereço de sub-rede de cliente da resoluções de que o suportam.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>O que é o TTL do DNS e a forma como afeta os meus utilizadores?

Quando uma consulta DNS no Gestor de tráfego que chegam, até define um valor na resposta chamada time-to-live (TTL). Este valor, cuja unidade está em segundos, indica ao resoluções DNS downstream no quanto para esta resposta em cache. Enquanto não são garantidas que as resoluções DNS este resultado da cache, a colocação em cache permite-lhes responder a todas as consultas subsequentes desativar a cache em vez de aceder aos servidores DNS de Gestor de tráfego. Isto afeta as respostas da seguinte forma:
- um valor de TTL superior reduz o número de consultas que apresentado nos servidores DNS de Gestor de tráfego, que podem reduzir o custo de um cliente, uma vez que o número de consultas servidos é a utilização de um sujeito a faturação.
- um valor de TTL superior pode, possivelmente, reduzir o tempo que demora para fazer uma pesquisa de DNS.
- um valor de TTL superior também significa que os dados não refletem as últimas informações de estado de funcionamento do Traffic Manager tem obtido através do respetivos agentes de pesquisa.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Alto ou insuficiente posso pode definir o valor de TTL de respostas de Gestor de tráfego?

Pode definir, cada um por nível de perfil, o valor de TTL do DNS para ser tão elevado como 2,147,483,647 segundos e como tão baixo como 0 segundos (o intervalo máximo em conformidade com [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt )). Um valor de TTL de 0 significa que a jusante resoluções DNS não colocar em cache as respostas de consulta e todas as consultas são esperadas para alcançar o DNS do Gestor de tráfego de servidores para a resolução.

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Método de encaminhamento de tráfego do Gestor de tráfego Geographic

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Quais são alguns casos de utilização em que o encaminhamento geográfica é útil? 
Tipo de encaminhamento geográfico pode ser utilizado em qualquer cenário em que um cliente do Azure tem de distinguir os seus utilizadores com base nas regiões geográficas. Por exemplo, utilizando o método de encaminhamento de tráfego geográfica, que pode dar aos utilizadores de regiões específicas uma experiência de utilizador diferente dos existentes a partir de outras regiões. Outro exemplo é complying com indica a soberania dos dados locais que requerem que os utilizadores de uma região específica ser servido apenas por pontos finais nessa região.

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Quais são as regiões suportadas pelo Gestor de tráfego para o encaminhamento geográfica? 
É possível encontrar a hierarquia de país/região que é utilizada pelo Gestor de tráfego [aqui](traffic-manager-geographic-regions.md). Enquanto esta página onde permanece atualizada com todas as alterações, através de programação também pode obter as mesmas informações utilizando a [REST API da Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Como determinar o Gestor de tráfego a onde um utilizador está a consultar de? 
Gestor de tráfego analisa o IP de origem da consulta (provavelmente é uma resolução DNS local, fazer a consulta em nome do utilizador) e utiliza um IP interno para o mapa de região para determinar a localização. Este mapa é atualizado numa base contínua para a conta para que as alterações na internet. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>É garantida o que o Gestor de tráfego corretamente pode determinar a localização geográfica exata do utilizador em cada caso?
Não, o Gestor de tráfego não pode garantir que a região geográfica que inferir do endereço IP de origem de uma consulta DNS será sempre corresponder para a localização do utilizador devido a pelas seguintes razões: 

- Em primeiro lugar, conforme descrito em FAQ do anterior, o endereço IP de origem, vemos é o facto de uma resolução DNS a fazer a pesquisa em nome do utilizador. Enquanto a localização geográfica de resolução de DNS é um boa proxy para a localização geográfica do utilizador, também pode ser diferente, consoante os requisitos de espaço do serviço de resolução DNS e o serviço de resolução DNS específico optado por utilizar um cliente. Por exemplo, um cliente localizado em Malaysia poderia especificar na utilização de definições do respetivo dispositivo, um serviço de resolução DNS cujo servidor DNS no Singapura poderá obter selecionado para processar as resoluções de consulta para que o dispositivo/utilizador. Nesse caso, o Gestor de tráfego só pode ver endereço IP a resolução que corresponde à localização Singapura. Além disso, consulte as FAQ sobre o suporte de endereço de sub-rede do cliente anterior nesta página.

- Segundo, o Gestor de tráfego utiliza um mapa interno para o endereço IP a tradução de região geográfica. Apesar deste mapa está validado e atualizado numa base contínua para aumentar a precisão e em conta a natureza evolução da internet, há a possibilidade de que o nosso informações não são uma representação exata da localização geográfica de todos os endereços IP.


###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Um ponto final tem de estar fisicamente localizados na mesma região que está configurado com para o encaminhamento geográfica? 
Não, a localização do ponto final impõe sem restrições em que regiões podem ser mapeadas para o mesmo. Por exemplo, um ponto final na região do Azure e.u. a Central pode ter todos os utilizadores de Índia direcionado ao mesmo.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Pode atribuir regiões geográficas para pontos finais num perfil que não está configurada para encaminhamento geográfica? 

Sim, se o método de encaminhamento de um perfil não é geográfico, pode utilizar o [REST API da Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/) atribuir regiões geográficas pontos finais nesse perfil. No caso de perfis de tipo de encaminhamento não geográfica, esta configuração é ignorada. Se alterar destes perfis para o tipo de encaminhamento geográfico num momento posterior, o Gestor de tráfego pode utilizar os mapeamentos.


### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Por que razão estou posso obter um erro ao tentar alterar o método de encaminhamento de um perfil existente para Geographic?

Todos os pontos finais sob um perfil com o encaminhamento geográfica tem de ter, pelo menos, uma região mapeada para este. Para converter um perfil existente para o tipo de encaminhamento geográfico, terá primeiro de associar regiões geográficas para todos os respetivos pontos finais utilizando o [REST API da Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/) antes de alterar o tipo de encaminhamento para geográfica. Se utilizar o portal, elimine primeiro os pontos finais, alterar o método de encaminhamento do perfil para geográfica e, em seguida, adicione os pontos finais, juntamente com os respetivos mapeamento de região geográfica. 


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Por que motivo é é vivamente recomendado que os clientes crie perfis aninhados em vez de pontos finais de um perfil com o serviço de encaminhamento geográfica ativado? 

Uma região pode ser atribuída a apenas um ponto final dentro de um perfil, se o utilizando o tipo de encaminhamento geográfico. Se esse ponto final não é um tipo aninhado com um perfil de subordinados anexado, se esse ponto final em mau estado de funcionamento, o tráfego Manager continua a enviar o tráfego para a mesma desde a alternativa não enviar qualquer tráfego não está nenhum melhor. Gestor de tráfego efetua uma ativação pós-falha não para outro ponto final, mesmo quando a região atribuída é um "elemento principal" da região atribuído para o ponto final que ficou em mau estado de funcionamento (por exemplo, se um ponto final que tem região Espanha ficar danificado, que fazermos não ativação pós-falha para outro ponto final Tem a região que Europa atribuída ao mesmo). Isto é feito para se certificar de que o Gestor de tráfego respeita limites geográficos que um cliente tem a configuração no respetivo perfil. Para obter o benefício de falha de ativação pós-falha para outro ponto final de quando um ponto final fica em mau estado de funcionamento, recomenda-se que regiões geográficas ser atribuídos a perfis aninhados com vários pontos finais dentro do mesmo em vez de pontos finais individuais. Desta forma, se um ponto final no perfil subordinado aninhado falhar, o tráfego pode ativação pós-falha para outro ponto final dentro do mesmo perfil subordinado aninhado.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Existem restrições na versão da API que suporte este tipo de encaminhamento?

Sim, apenas a versão da API de 2017-03-01 e a mais recente suporta o encaminhamento geográfica tipo. Todas as antigas versões API não podem ser utilizado para criar perfis do tipo de encaminhamento geográfico ou atribuir regiões geográficas para pontos finais. Se uma versão de API antiga é utilizada para obter perfis a partir de uma subscrição do Azure, qualquer perfil do tipo de encaminhamento geográfico não é devolvido. Além disso, quando utilizar versões anteriores de API, qualquer perfil devolveu que tem pontos finais com uma atribuição de região geográfica, não tem a sua atribuição de região geográfica mostrada.

## <a name="real-user-measurements"></a>Medidas de Utilizadores Reais

>[!NOTE]
>A funcionalidade de medidas de utilizador Real no Traffic Manager está em pré-visualização pública e não pode ter o mesmo nível de disponibilidade e fiabilidade como versão de funcionalidades que estão em geral disponibilidade. A funcionalidade não é suportada, pode ter restrita capacidades e poderão não estar disponível em todas as localizações do Azure. Para as notificações mais atualizadas à sua disponibilidade e o estado desta funcionalidade, consulte o [Traffic Manager do Azure atualiza](https://azure.microsoft.com/updates/?product=traffic-manager) página.

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Quais são as vantagens da utilização Real medidas de utilizador?
Quando utiliza o método de encaminhamento de desempenho, Traffic Manager seleciona a melhor região do Azure para o utilizador final ligar a inspecionar o IP de origem e a sub-rede de cliente EDNS (se transmitido) e a verificá-lo contra o intelligence de latência de rede do serviço mantém. Medidas de utilizador reais melhora isto para os seus utilizadores finais base, fazendo com que a sua experiência contribuir para esta tabela de latência, além de garantir que esta tabela tem abrange as redes de utilizador final a partir de onde os utilizadores finais ligar ao Azure. Isto leva a uma precisão maior no encaminhamento dos utilizadores finais.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Pode utilizar medidas de utilizador reais com regiões não do Azure?
Medidas de medidas de utilizador reais e relatórios sobre apenas a latência para alcançar regiões do Azure. Se estiver a utilizar o encaminhamento baseado em desempenho com pontos finais alojados em regiões do Azure não, ainda pode beneficiar desta funcionalidade por ter maior latência obter informações sobre a região do Azure representativa tenha selecionado a ser associado este ponto final.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>O método de encaminhamento beneficia de medidas de utilizador reais?
As informações adicionais adquiridas através de medidas de utilizador reais são aplicáveis apenas para perfis que utilizam o método de encaminhamento de desempenho. Tenha em atenção que a ligação de medidas de utilizador reais disponível em todos os perfis de quando vê-lo através do portal do Azure.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>É necessário ativar as medidas de utilizador reais cada perfil separadamente
Não, só tem de ativá-la de uma vez por subscrição e todas as informações de latência medido e comunicadas estão disponíveis para todos os perfis.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Como desligo Real medidas de utilizador para a minha subscrição?
Pode parar a acumular encargos relacionados com a medidas de utilizador reais quando parar a recolha e envio de latência back medidas da sua aplicação de cliente. Por exemplo, quando medida JavaScript incorporada em páginas web, pode deixar de utilizar esta funcionalidade ao remover o JavaScript ou ao desativar a sua invocação quando a página ser composta.
Outra forma para desativar a medidas de utilizador Real é ao eliminar a sua chave. Depois, fazê-lo, são eliminadas quaisquer medidas enviadas para o Gestor de tráfego com essa chave.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Pode utilizar medidas de utilizador reais com aplicações de cliente que não sejam páginas web?
Sim, medidas de utilizador reais foi concebida para a ingestão de dados recolhidos através de diferentes tipos de clientes do utilizador final. Estas FAQ será atualizada como obterem suportados novos tipos de aplicações de cliente.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Quantos valores são feitas a cada vez que o meu medidas de utilizador reais ativado página web é composta?
Quando Real medidas de utilizador é utilizada com a medição JavaScript fornecido, cada composição da página resulta em seis medidas que está a ser executadas. Em seguida, são reportados novamente para o serviço do Gestor de tráfego. Tenha em atenção que lhe é cobrados para esta funcionalidade com base no número de valores que relatados ao serviço do Gestor de tráfego. Por exemplo, se o utilizador navega para fora da sua página Web enquanto os valores estão a ser executados, mas antes de foi comunicado, esses valores não são considerados para fins de faturação.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Existe um atraso antes da execução do script de medidas de utilizador reais na minha página Web?
Não, não há nenhum atraso programados e antes do script é invocado.

### <a name="can-i-use-configure-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Pode utilizar configurar Real de utilizador de medidas com apenas as regiões do Azure pretender medir?
Não, cada hora que é invocado, o script de medidas de utilizador reais mede um conjunto de seis regiões do Azure, conforme determinado pelo serviço. Este conjunto de alterações entre diferentes invocações e quando um grande número de invocações de tais acontecer, a cobertura de medida abrange em diferentes regiões do Azure.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Pode limitar o número de valores efetuadas num número específico?
A medição JavaScript é incorporado dentro da sua página Web e de está no controlo total ao longo do quando a iniciar e parar a utilizá-la. Desde que o serviço do Gestor de tráfego recebe um pedido para obter uma lista de regiões do Azure para ser medido, são devolvidos um conjunto de regiões. Tenha também em atenção que durante o período de pré-visualização, que lhe será não ser cobrados quaisquer medidas reportadas para o Gestor de tráfego

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Pode ver as medições efetuadas pela minha aplicação de cliente como parte das medidas de utilizador reais desde?
Uma vez que a lógica da medida é executada a partir da aplicação de cliente, está em total controlo que acontece, incluindo ver os valores de latência. Gestor de tráfego não comunica uma vista de agregação de medidas recebido sob a chave ligada à sua subscrição

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Pode modificar o script de medida fornecido pelo Gestor de tráfego?
Enquanto estiver no controlo sobre o que está incorporado na sua página web, é vivamente desencorajar-tem de efetuar quaisquer alterações para o script de medida para garantir que mede e reporta as latências corretamente.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Será possível que outras pessoas ver a chave a que utilizar com valores reais do utilizador?
Ao incorporar o script de medida para uma página web será possível que outras pessoas ver o script e a chave de medidas de utilizador reais (RUM). Mas é importante saber que esta chave é diferente do seu id de subscrição e é gerada pelo Gestor de tráfego a ser utilizado apenas para esta finalidade. Saber a sua chave RUM não será comprometer a segurança da conta do Azure

### <a name="can-others-abuse-my-rum-key"></a>Outras pessoas injuriar a minha chave RUM?
Embora seja possível para que outros a utilizar a sua chave para enviar informações erradas ao Azure tenha em atenção que alguns valores errados não irão alterar o encaminhamento, uma vez que este é contemplada juntamente com todos os outros valores recebido. Se precisar de alterar as suas chaves, pode gerar novamente a chave, altura em que a chave antiga passa a ser rejeitada.

###  <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>É necessário colocar a medição JavaScript em todas as minhas páginas web
Medidas de utilizador reais fornece mais valor como o número de aumento de medidas. Ter consiga aceder tal que, é a decisão sobre se precisa de colocá-la em todas as páginas web ou um selecione poucos. É a nossa recomendação para iniciar colocando-o na sua página mais visitada em que um utilizador deve permanecer nessa página cinco segundos ou mais.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Podem obter informações sobre os meus utilizadores finais ser identificadas pelo Gestor de tráfego se utilizar medidas de utilizador reais?
Quando é utilizada a medição fornecida JavaScript, o Gestor de tráfego terão visibilidade para o endereço IP do cliente do utilizador final e o endereço IP de origem da resolução DNS local que estiverem a utilizar. Gestor de tráfego utiliza o endereço IP do cliente apenas depois de ter que truncado para conseguir identificar o utilizador final específico que enviou as medidas. 

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>É a página Web medição medidas Real do utilizador tem de estar a utilizar o Gestor de tráfego para o encaminhamento?
Não, não precisa de utilizar o Gestor de tráfego. O lado de encaminhamento do Gestor de tráfego funciona em separado da peça a medida de utilizador reais e embora seja uma excelente ideia possuam ambas na mesma propriedade web, não precisa de ser.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>É necessário alojar qualquer serviço em regiões do Azure para utilizar com os valores reais do utilizador?
Não, não precisa de alojar qualquer componente do lado do servidor no Azure para medidas de utilizador reais trabalhar. A imagem de pixel único transferiu a medição JavaScript e o serviço executá-lo em diferentes regiões do Azure é alojado e gerido pelo Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>A minha utilização de largura de banda do Azure irá aumentar quando utilizar medidas de utilizador reais?
Tal como mencionado na resposta anterior, os componentes do lado do servidor de medidas de utilizador reais são propriedade e geridos pelo Azure. Isto significa que a utilização da largura de banda do Azure não irá aumentar porque utilizar medidas de utilizador reais. Tenha em atenção que, não inclui qualquer utilização de largura de banda fora os custos do Azure. Iremos minimiza a largura de banda utilizada ao transferir uma imagem de pixel único para a medida a latência para uma região do Azure. 

## <a name="traffic-view"></a>Vista de Tráfego

>[!NOTE]
>A funcionalidade de vista de tráfego no Traffic Manager está em pré-visualização pública e não pode ter o mesmo nível de disponibilidade e fiabilidade como versão de funcionalidades que estão em geral disponibilidade. A funcionalidade não é suportada, pode ter restrita capacidades e poderão não estar disponível em todas as localizações do Azure. Para as notificações mais atualizadas à sua disponibilidade e o estado desta funcionalidade, consulte o [Traffic Manager do Azure atualiza](https://azure.microsoft.com/updates/?product=traffic-manager) página.

### <a name="what-does-traffic-view-do"></a>O que faz a vista de tráfego?
A vista de tráfego é uma funcionalidade do Gestor de tráfego que o ajuda a compreender mais sobre os seus utilizadores e como é a sua experiência. Utiliza as consultas recebidas pelo Gestor de tráfego e as tabelas de intelligence de latência de rede que o serviço mantém para lhe fornecer o seguinte:
- As regiões a partir de onde os utilizadores se ligam a pontos finais da sua no Azure.
- O volume de utilizadores a ligar a partir destes regiões.
- Regiões do Azure para que estes são obter encaminhados para.
- Sua experiência de latência para estas regiões do Azure.

Esta informação está disponível para que possa consumir através de uma vista em tabela no portal para além de ser disponível como dados não processados para que possa transferir.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Como pode beneficiar da utilização de vista de tráfego?

Vista de tráfego dá-lhe a vista geral do tráfego de que recebem os perfis do Traffic Manager. Em particular, pode ser utilizado para compreender onde a sua base de utilizadores liga-se do e igualmente acima de tudo, o que é sua experiência de latência média. Em seguida, pode utilizar estas informações para determinar áreas em que terá de focar-se, por exemplo, expandindo os requisitos de espaço do Azure para uma região que possa servir os utilizadores com uma latência inferior. Outra informações que pode derivar ou utilizar a vista de tráfego é para ver os padrões de tráfego a regiões diferentes, que por sua vez podem ajudar a tomar decisões sobre aumente ou diminua invent essas regiões.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Como ver o tráfego é diferente das métricas de Gestor de tráfego disponíveis através do monitor do Azure?

Monitor do Azure pode ser utilizado para compreender num nível agregar o tráfego recebido pelo seu perfil e os respetivos pontos finais. Também permite-lhe controlar o estado de funcionamento dos pontos finais por expor os resultados da verificação de estado de funcionamento. Quando precisar de ponha estes e que compreende a experiência dos seus utilizadores finais a ligar ao Azure a um nível regional, a vista de tráfego pode ser utilizada para alcançar que.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>A vista de tráfego utiliza as informações de sub-rede de cliente EDNS?

Vista de tráfego não considere as informações de sub-rede de cliente EDNS ao criar o resultado. Utiliza o endereço IP de resolução de DNS local dos seus utilizadores para agrupá-los.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Quantos dias de dados utilizar a vista de tráfego?

Vista de tráfego cria o respetivo resultado ao processar os dados de sete dias anteriores o dia anterior, quando for visualizado por si. Esta é uma janela de mover e os dados mais recentes serão utilizados sempre que visitar.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Como é que o tráfego de vista processar pontos finais externos?

Quando utiliza pontos finais externos alojados fora regiões do Azure num perfil do Traffic Manager pode escolher que mapeado para uma região do Azure que é um proxy para as características de latência (isto é na realidade necessário se utilizar o método de encaminhamento de desempenho). Se tiver desta mapeamento de região do Azure, as métricas de latência nessa região do Azure serão utilizadas ao criar a vista de tráfego de saída. Não se for especificada nenhuma região do Azure, as informações de latência estará vazios dos dados para esses pontos finais externos.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>É necessário ativar vista de tráfego para cada perfil na minha subscrição?
Durante o período de pré-visualização, ver o tráfego está ativado um nível de subscrição e está disponível para todos os perfis do Gestor de tráfego dessa subscrição.

### <a name="how-can-i-turn-off-traffic-view"></a>Como posso desativar a vista de tráfego?
Durante o período de pré-visualização, estamos a pedir que crie um pedido de suporte para desativar a vista de tráfego para a sua subscrição.

### <a name="how-does-traffic-view-billing-work"></a>Como funciona a faturação da vista de tráfego?

Tráfego Ver preços baseia-se no número de pontos de dados utilizado para criar a saída. Atualmente, o tipo de dados apenas suportado é consultas que recebe o seu perfil. Além disso, apenas são cobradas para o processamento foi feito quando tiver de vista de tráfego ativado. Isto significa que, se ativar vista de tráfego para um determinado período de tempo num mês e desativá-la durante noutros casos, apenas os pontos de dados processados enquanto tinha a funcionalidade ativada contagem para a fatura.
Durante o período de pré-visualização, não lhe serem cobrados para utilizar a vista de tráfego.

## <a name="traffic-manager-endpoints"></a>Pontos finais do Gestor de tráfego

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Pode utilizar o Gestor de tráfego com pontos finais de várias subscrições?

A utilização de pontos finais de várias subscrições não é possível com as Web Apps do Azure. As aplicações Web do Azure requer que qualquer nome de domínio personalizado utilizada com as Web Apps só é utilizada dentro de uma única subscrição. Não é possível utilizar as aplicações Web a partir de várias subscrições com o mesmo nome de domínio.

Para outros tipos de ponto final, é possível utilizar o Gestor de tráfego com pontos finais de mais do que uma subscrição. No Gestor de recursos, pontos finais de qualquer subscrição podem de ser adicionados ao Gestor de tráfego, desde que a pessoa que está a configurar o perfil de Gestor de tráfego tenha acesso de leitura para o ponto final. Estas permissões podem ser concedidas utilizando [do Azure Resource Manager controlo de acesso baseado em funções (RBAC)](../active-directory/role-based-access-control-configure.md).


### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Pode utilizar o Gestor de tráfego com ranhuras de 'Teste' do serviço em nuvem?

Sim. O serviço em nuvem "testes" ranhuras pode ser configurado no Traffic Manager como pontos finais externos. Verificações de estado de funcionamento ainda são cobrados à taxa de pontos finais do Azure. Porque o tipo de ponto final externo está a ser utilizado, alterações ao serviço subjacente não são captadas automaticamente. Com pontos finais externos, o Gestor de tráfego não consegue detetar quando o serviço em nuvem foi parado ou eliminado. Por conseguinte, o Gestor de tráfego continua a faturação de verificações de estado de funcionamento até que o ponto final está desativado ou eliminado.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Gestor de tráfego suporta pontos finais de IPv6?

Atualmente, o Gestor de tráfego não fornecem IPv6 addressible servidores de nomes. No entanto, o Gestor de tráfego ainda podem ser utilizado por clientes de IPv6 que se ligam a pontos finais de IPv6. Um cliente não faz pedidos DNS diretamente para Gestor de tráfego. Em vez disso, o cliente utiliza um serviço DNS recursiva. Um cliente apenas de IPv6 envia pedidos ao serviço DNS recursiva através de IPv6. Em seguida, o serviço de recursiva deve ser capaz de contactar os servidores de nomes do Gestor de tráfego através de IPv4.

Gestor de tráfego responde com o nome DNS do ponto final. Para suportar um ponto final de IPv6, tem de existir um registo de DNS AAAA apontar o nome DNS do ponto final para o endereço IPv6. Verificações de estado de funcionamento do Traffic Manager suportam apenas endereços IPv4. O serviço tem de expor um ponto final de IPv4 no mesmo nome DNS.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Pode utilizar o Gestor de tráfego com mais do que uma aplicação Web na mesma região?

Normalmente, o Gestor de tráfego é utilizado para direcionar o tráfego para as aplicações implementadas em regiões diferentes. No entanto, também pode ser utilizado em que uma aplicação tem mais de uma implementação na mesma região. Os pontos finais do Azure do Gestor de tráfego permite mais do que um ponto final de aplicação Web da mesma região do Azure para ser adicionado para o mesmo perfil de Gestor de tráfego.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group"></a>Como posso mover pontos finais do meu perfil do Traffic Manager do Azure para um grupo de recursos diferente?

Pontos finais do Azure que estão associados um perfil de Gestor de tráfego são registados utilizando os respetivos IDs de recurso. Quando um recurso do Azure que está a ser utilizado como um ponto final (por exemplo, IP público, o serviço em nuvem clássico, WebApp ou outro perfil de Gestor de tráfego utilizado de forma aninhada) é movido para um grupo de recursos diferente, as alterações do mesmo ID de recurso. Neste cenário, atualmente, tem de atualizar o perfil do Gestor de tráfego de primeiro eliminar e adicionando novamente os pontos finais para o perfil. 

##  <a name="traffic-manager-endpoint-monitoring"></a>Monitorização de pontos finais do Gestor de tráfego

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>É Gestor de tráfego sejam resilientes a falhas de região do Azure?

Gestor de tráfego é um componente fundamental de entrega de aplicações altamente disponíveis no Azure.
Para fornecer elevada disponibilidade, o Gestor de tráfego tem de ter um exceptionally elevado nível de disponibilidade e sejam resilientes a falhas regionais.

Por predefinição, os componentes de Gestor de tráfego são resilientes a uma falha completa do qualquer região do Azure. Este resiliência aplica-se a todos os componentes do Gestor de tráfego: os nomes do DNS servidores, a API, a camada de armazenamento e o ponto final do serviço de monitorização.

Na improvável eventualidade de uma falha de uma região do Azure completa, o Gestor de tráfego é esperado para continuar a funcionar normalmente. As aplicações implementadas em várias regiões do Azure podem basear-se no Gestor de tráfego para direcionar o tráfego para uma instância disponível da respetiva aplicação.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Como a opção de localização do grupo de recursos afeta o Gestor de tráfego?

Gestor de tráfego é um serviço único e global. Não é regional. A opção de localização do grupo de recursos faz diferença para perfis do Traffic Manager implementadas nesse grupo de recursos.

O Azure Resource Manager requer que todos os grupos de recurso especificar uma localização, que determina a localização predefinida para recursos implementados nesse grupo de recursos. Quando cria um perfil do Traffic Manager, é criado num grupo de recursos. Todos os perfis do Traffic Manager utilizar **global** como a respetiva localização, substituir a predefinição do grupo de recursos.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Como posso determinar o estado de funcionamento atual de cada ponto final?

O estado atual de monitorização de cada ponto final, para além do perfil de geral, é apresentado no portal do Azure. Estas informações também estão disponíveis através do Monitor de tráfego [REST API](https://msdn.microsoft.com/library/azure/mt163667.aspx), [cmdlets do PowerShell](https://msdn.microsoft.com/library/mt125941.aspx), e [CLI do Azure de várias plataformas](../cli-install-nodejs.md).

Azure não fornece informações de histórico sobre o último Estado de funcionamento do ponto final ou da capacidade de emitir alertas sobre alterações ao estado de funcionamento do ponto final.

### <a name="can-i-monitor-https-endpoints"></a>Pode monitorizar os pontos finais HTTPS?

Sim. O Traffic Manager suporta a pesquisa através de HTTPS. Configurar **HTTPS** como o protocolo na configuração de monitorização.

Gestor de tráfego não é possível fornecer a validação de certificado, incluindo:

* Não são validados certificados do lado do servidor
* Não são suportados certificados do lado do servidor SNI
* Não são suportados certificados de cliente

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Posso utilizar o Gestor de tráfego, mesmo se a minha aplicação não tiver suporte para HTTP ou HTTPS?

Sim. Pode especificar o TCP como protocolo de monitorização e o Gestor de tráfego pode iniciar uma ligação de TCP e aguardar pela resposta do ponto final. Se o ponto final responde ao pedido de ligação com uma resposta para estabelecer a ligação, dentro do período de tempo limite, em seguida, esse ponto final está marcado como bom estado de funcionamento.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>As respostas específicas são necessárias do ponto final ao utilizar a monitorização de TCP?

Quando a monitorização de TCP é utilizado, o Gestor de tráfego é iniciado um handshake TCP de três vias ao enviar um pedido de SIN ao ponto final na porta especificada. Em seguida, aguarda um período de tempo (conforme especificado nas definições de tempo limite) para uma resposta do ponto final. Se o ponto final responde ao pedido SIN com uma resposta de confirmação SIN dentro do período de tempo limite especificado nas definições de monitorização, em seguida, esse ponto final é considerado em bom estado. Se não for recebida a resposta de confirmação SIN, o Gestor de tráfego repõe a ligação ao responder novamente com um RST.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>A rapidez é que o Gestor de tráfego de mover os meus utilizadores sair de um ponto de final mau estado de funcionamento?

Gestor de tráfego fornece várias definições que podem ajudar a controlar o comportamento de ativação pós-falha do perfil do Gestor de tráfego da seguinte forma:
- Pode especificar que o Gestor de tráfego as sondas os pontos finais com mais frequência ao definir o intervalo de pesquisa em 10 segundos. Isto garante que qualquer ponto final em mau estado de funcionamento pode ser detetado logo que possível. 
- Pode especificar o período de tempo de espera antes de um Estado de funcionamento Consulte tempos de pedido (o valor de tempo limite mínimo é seg 5).
- Pode especificar o número de falhas podem ocorrer antes do ponto final está marcado como danificado. Este valor pode ser baixa como 0, na qual caso o ponto final está marcado como danificado assim não for a primeira verificação de estado de funcionamento. No entanto, utilizar o valor mínimo de 0 para o número de falhas tolerated pode levar a que está a ser retirados do rotação devido a problemas transitórios que possam ocorrer no momento da pesquisa de pontos finais.
- Pode especificar o time-to-live (TTL) para a resposta DNS ser tão baixo como 0. Se o fizer, significa que as resoluções DNS não é possível colocar em cache da resposta e cada nova consulta obtém uma resposta que incorpora as informações de estado de funcionamento mais atualizadas com o Gestor de tráfego.

Ao utilizar estas definições, Gestor de tráfego pode fornecer as ativações pós-falha em 10 segundos depois de um ponto final fica em mau estado de funcionamento e é feita uma consulta DNS contra o perfil de correspondente.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Como pode especificar diferentes definições de monitorização para diferentes pontos finais num perfil?

Gestor de tráfego, as definições de monitorização estão num nível de perfil por. Se precisar de utilizar uma definição de monitorização diferente para apenas um ponto final, pode ser feito, fazendo com que esse ponto final como um [aninhada perfil](traffic-manager-nested-profiles.md) cujas definições de monitorização são diferentes do perfil principal.

### <a name="what-host-header-do-endpoint-health-checks-use"></a>O estado de funcionamento de ponto final de escolha de cabeçalho do anfitrião verifica utilização?

Gestor de tráfego utiliza cabeçalhos de anfitrião nas verificações de estado de funcionamento HTTP e HTTPS. O cabeçalho de anfitrião utilizado pelo Gestor de tráfego é o nome do destino de ponto final configurado no perfil. O valor utilizado no cabeçalho de anfitrião não pode ser especificado em separado da propriedade de destino.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Quais são os endereços IP a partir do qual o estado de funcionamento verifica têm origem?

A lista seguinte contém os endereços IP a partir do qual o estado de funcionamento do Gestor de tráfego verifica podem provir. Pode utilizar esta lista para garantir que as ligações recebidas destes endereços IP são permitidas em pontos finais para verificar o estado de funcionamento.

* 40.68.30.66
* 40.68.31.178
* 137.135.80.149
* 137.135.82.249
* 23.96.236.252
* 65.52.217.19
* 40.87.147.10
* 40.87.151.34
* 13.75.124.254
* 13.75.127.63
* 52.172.155.168
* 52.172.158.37
* 104.215.91.84
* 13.75.153.124
* 13.84.222.37
* 23.101.191.199
* 23.96.213.12
* 137.135.46.163
* 137.135.47.215
* 191.232.208.52
* 191.232.214.62
* 13.75.152.253
* 104.41.187.209
* 104.41.190.203
* 52.173.90.107
* 52.173.250.232
* 104.45.149.110
* 40.114.5.197
* 52.240.151.125
* 52.240.144.45
* 13.65.95.152
* 13.65.92.252
* 40.78.67.110
* 104.42.192.195

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Quantos verificações de estado de funcionamento ao ponto final do meu posso esperar do Gestor de tráfego?

O número de estado de funcionamento do Gestor de tráfego verifica atingir o ponto final depende o seguinte:
- o valor que definiu para o intervalo de monitorização (intervalo mais pequeno significa mais pedidos de destino no seu ponto final em qualquer período de tempo indicado).
- o número de localizações de onde verifica o estado de funcionamento provir (os endereços IP a partir de onde pode contar com estas verificações está listado nas FAQ do anterior).

## <a name="traffic-manager-nested-profiles"></a>Gestor de tráfego aninhada perfis

### <a name="how-do-i-configure-nested-profiles"></a>Como configurar a perfis aninhados?

Aninhada perfis do Traffic Manager podem ser configuradas com o Azure Resource Manager e clássico do Azure REST APIs, cmdlets do Azure PowerShell e comandos da CLI do Azure de várias plataformas. Também são suportados através do portal do Azure. Não são suportados no portal clássico.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Quantas camadas do aninhamento faz o tráfego do Configuration Manager suportam?

Podem aninhar perfis até 10 níveis de profundidade. 'Repete' não é permitida.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Pode misturar outros tipos de ponto final com perfis de subordinado aninhado, o mesmo perfil de Gestor de tráfego?

Sim. Não existem sem restrições sobre como combinar pontos finais de diferentes tipos de dentro de um perfil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Como o modelo de faturação é aplicável para perfis aninhados?

Não há preços impacto de utilização de perfis aninhados não negativo.

Gestor de tráfego faturação tem dois componentes: verificações de estado de funcionamento do ponto final e milhões de consultas DNS

* As verificações de estado de funcionamento do ponto final: há sem encargos durante um perfil subordinado quando configurado como um ponto final num perfil principal. Monitorização dos pontos finais no perfil subordinado é faturada como habitualmente.
* Consultas DNS: cada consulta apenas é contabilizada uma vez. Uma consulta em relação a um perfil de principal que devolve um ponto final de um perfil subordinado é contabilizada contra o perfil de principal apenas.

Para mais informações, consulte o [Gestor de tráfego a página de preços](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Existe um impacto no desempenho de perfis aninhados?

Não. Não há nenhum impacto no desempenho tarifas quando através de perfis aninhados.

Os servidores de nomes do Gestor de tráfego atravessam internamente a hierarquia de perfil durante o processamento cada consulta DNS. Uma consulta DNS para um perfil de principal pode receber uma resposta DNS com um ponto final de um perfil subordinado. Um único registo CNAME é utilizado se estiver a utilizar um perfil único ou aninhadas perfis. Não é necessário para criar um registo CNAME para cada perfil na hierarquia.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Como é que o Gestor de tráfego computação o estado de funcionamento de um ponto final aninhado num perfil principal?

O perfil de principal não executar verificações do Estado de funcionamento no subordinado diretamente. Em vez disso, o estado de funcionamento dos pontos finais do perfil de subordinados são utilizados para calcular o estado de funcionamento geral do perfil subordinado. Esta informação é propagada ascendentemente na hierarquia de perfis aninhados para determinar o estado de funcionamento do ponto final aninhado. O perfil de principal utiliza este estado de funcionamento agregado para determinar se o tráfego pode ser direcionado para o elemento subordinado.

A tabela seguinte descreve o comportamento do Gestor de tráfego de estado de funcionamento verifica a existência de um ponto de final aninhado.

| Estado do Monitor de perfil de subordinados | Estado do Monitor de ponto final principal | Notas |
| --- | --- | --- |
| Desativado. O perfil subordinado foi desativado. |Parada |O estado de ponto final principal estiver parado, não desativado. O estado desativado está reservado para indicar que o ponto final no perfil de principal tiver desativado. |
| Degradado. Ponto final do perfil, pelo menos, um subordinado está num Estado Degraded. |Online: o número de pontos finais Online no perfil de subordinados, pelo menos, é o valor de MinChildEndpoints.<BR>CheckingEndpoint: o número de pontos finais Online plus CheckingEndpoint no perfil de subordinados, pelo menos, é o valor de MinChildEndpoints.<BR>Degradado: caso contrário. |O tráfego é encaminhado para um ponto final do Estado CheckingEndpoint. Se MinChildEndpoints estiver definido demasiado elevado, o ponto final é sempre degradado. |
| Online. Ponto final do perfil, pelo menos, um subordinado é o Estado Online. Nenhum ponto final está no Estado Degraded. |Consulte acima. | |
| CheckingEndpoints. Ponto final do perfil, pelo menos, um subordinado é 'CheckingEndpoint'. Não existem pontos finais são 'Online' ou 'Degradado' |Igual acima. | |
| Inativos. Todos os pontos finais perfil de subordinados são desativado ou parado ou este perfil não tem pontos finais. |Parada | |

## <a name="next-steps"></a>Passos seguintes:
- Saiba mais sobre o Gestor de tráfego [ativação pós-falha automática e monitorização do ponto final](../traffic-manager/traffic-manager-monitoring.md).
- Saiba mais sobre o Gestor de tráfego [métodos de encaminhamento de tráfego](../traffic-manager/traffic-manager-routing-methods.md).
