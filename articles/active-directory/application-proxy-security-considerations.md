---
title: "Considerações de segurança para o Proxy de aplicações do Azure AD | Microsoft Docs"
description: "Abrange as considerações de segurança para utilizar o Proxy de aplicações do Azure AD"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 239f3830a5e50f80ace15ff3f080653a5f402136
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Considerações de segurança para aceder a aplicações remotamente com o Proxy de aplicações do Azure AD

Este artigo explica os componentes que funcionam para manter os seus utilizadores e aplicações seguros ao utilizar o Proxy de aplicações do Azure Active Directory.

O diagrama seguinte mostra como do Azure AD permite o acesso remoto seguro às suas aplicações no local.

 ![Diagrama de acesso remoto seguro através do Proxy de aplicações do Azure AD](./media/application-proxy-security-considerations/secure-remote-access.png)

## <a name="security-benefits"></a>Vantagens de segurança

Proxy de aplicações do Azure AD oferece as seguintes vantagens de segurança:

### <a name="authenticated-access"></a>Acesso autenticado 

Se optar por utilizar o Azure Active Directory pré-autenticação, em seguida, apenas as ligações autenticadas podem aceder à sua rede.

Proxy de aplicações do Azure AD depende do serviço de token de segurança (STS) do Azure AD para toda a autenticação.  Pré-autenticação, pelo respetiva natureza muito, bloqueia um número significativo de ataques anónimos, porque apenas identidades autenticadas podem aceder à aplicação de back-end.

Se escolher Passthrough como método de pré-autenticação, não receberá desta vantagem. 

### <a name="conditional-access"></a>Acesso condicional

Aplica controlos de política mais rico antes de ligações à sua rede são estabelecidas.

Com [acesso condicional](active-directory-conditional-access-azure-portal-get-started.md), pode definir restrições no qual o tráfego tem permissão para aceder às suas aplicações de back-end. Pode criar políticas que restringem inícios de sessão com base na localização, força de autenticação e o perfil de risco do utilizador.

Também pode utilizar o acesso condicional para configurar políticas de autenticação Multifator, adicionar outra camada de segurança para autenticações de utilizador. 

### <a name="traffic-termination"></a>Terminação de tráfego

Todo o tráfego é terminado na nuvem.

Porque o Proxy de aplicações do Azure AD é um proxy inverso, todo o tráfego para aplicações de back-end é terminado no serviço. Pode obter restabelecer a sessão apenas com o servidor de back-end, o que significa que os servidores de back-end não são expostos a direto tráfego HTTP. Esta configuração significa que melhor estão protegidos contra ataques direcionados.

### <a name="all-access-is-outbound"></a>Todo o acesso é de saída 

Não precisa de abrir ligações de entrada à rede empresarial.

Conetores da Proxy da aplicação utilizam apenas ligações de saída para o serviço de Proxy de aplicações do Azure AD, o que significa que não é necessário abrir portas de firewall para ligações de entrada. Proxies tradicionais necessária numa rede de perímetro (também conhecido como *DMZ*, *zona desmilitarizada*, ou *sub-rede filtrada*) e a permissão para aceder a ligações não autenticadas no limite de rede. Este cenário necessários investimentos em produtos de firewall de aplicação web para analisar o tráfego e proteger o ambiente. Com o Proxy da aplicação, não precisa de uma rede de perímetro porque todas as ligações são saídas e ocorrer através de um canal seguro.

Para obter mais informações sobre conectores, consulte [conetores da Proxy da aplicação Azure compreender AD](application-proxy-understand-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Análise de escala da nuvem e de aprendizagem 

Obter a proteção de segurança inovadores.

Porque faz parte do Azure Active Directory, o Proxy de aplicações podem tirar partido [do Azure AD Identity Protection](active-directory-identityprotection.md), com dados do Microsoft Security Response Center e na unidade de Crimes digitais. Em conjunto, proativamente identificar contas comprometidas e oferecem proteção contra inícios de sessão de alto risco. Iremos tenha em conta vários fatores para determinar quais attemps início de sessão são de alto risco. Estes fatores incluem dispositivos infetado a sinalizar, redes anonymizing e atípica ou pouco provável localizações.

Muitas destes eventos e os relatórios já serem encontram disponíveis através de uma API para integração com as suas informações de segurança e sistemas de gestão (SIEM) de eventos.

### <a name="remote-access-as-a-service"></a>Acesso remoto como um serviço

Não tem de se preocupar com a manutenção e a aplicação de patches de servidores no local.

Software tratam ainda contas para um grande número de ataques. Proxy de aplicações do Azure AD é um serviço de escala de Internet que é proprietário Microsoft, pelo que obter sempre os patches de segurança mais recentes e atualizações.

Para melhorar a segurança de aplicações publicadas pelo Proxy de aplicações do Azure AD, iremos bloquear robots do crawler web de indexação e arquivar as suas aplicações. Proxy de aplicações de cada vez um robot de crawler web tenta obter as definições o robot para uma aplicação publicada, responde com um ficheiro de robots.txt que inclui `User-agent: * Disallow: /`.

### <a name="ddos-prevention"></a>Prevenção de DDOS

As aplicações publicadas através do Proxy de aplicações estão protegidas contra ataques de distribuídas de recusa de serviço (DDoS) distribuídos.

O serviço de Proxy de aplicações monitoriza a quantidade de tráfego a tentar aceder a aplicações e a sua rede. Se os picos do número de dispositivos que pedem acesso remoto às suas aplicações Microsoft acelera acesso à sua rede. 

Microsoft monitoriza os padrões de tráfego para aplicações individuais e para a sua subscrição, como um todo. Se uma aplicação recebe superior pedidos normais, em seguida, pedidos para aceder a essa aplicação serão negados durante um curto período de tempo. Se receber superior pedidos normais entre a sua subscrição completa, em seguida, pedidos para aceder a qualquer uma das suas aplicações serão negados. Este medidas preventivas mantém os seus servidores de aplicações assoberbados por pedidos de acesso remoto, para que os utilizadores no local podem manter a aceder às suas aplicações. 

## <a name="under-the-hood"></a>Os bastidores

Proxy de aplicações do Azure AD é composto por duas partes:

* O serviço baseado na nuvem: este serviço é executado no Azure, não sendo onde são efetuadas as ligações de cliente/utilizador externo.
* [O conector no local](application-proxy-understand-connectors.md): um componente no local, o conector escuta pedidos de ligações de serviço e identificadores de Proxy de aplicações do Azure AD para as aplicações internas. 

Um fluxo entre o conector e o serviço de Proxy de aplicações é estabelecido quando:

* O conector é configurado pela primeira vez.
* O conector obtém as informações de configuração do serviço de Proxy de aplicações.
* Um utilizador acede a uma aplicação publicada.

>[!NOTE]
>Todas as comunicações ocorrem através de SSL, e têm origem sempre no conector com o serviço de Proxy de aplicações. O serviço é de saída apenas.

O conector utiliza um certificado de cliente para autenticar para o serviço de Proxy de aplicações para quase todas as chamadas. A única exceção a este processo é o passo de configuração inicial, onde o certificado de cliente é estabelecido.

### <a name="installing-the-connector"></a>Instalar o conector

Quando o conector é configurado pela primeira vez, os seguintes eventos de fluxo ocorrer:

1. O registo do conector para o serviço ocorre como parte da instalação do conector. Os utilizadores recebem um pedido para introduzir as respetivas credenciais de administrador do Azure AD. O token adquirido desta autenticação, em seguida, é apresentado para o serviço de Proxy de aplicações do Azure AD.
2. O serviço de Proxy de aplicações avalia o token. Verifica se o utilizador é um administrador de empresa no inquilino. Se o utilizador não for um administrador, o processo é terminado.
3. O conector gera um pedido de certificado de cliente e passa-, juntamente com o token, para o serviço de Proxy de aplicações. Por sua vez, o serviço verifica o token e inicia o pedido de certificado de cliente.
4. O conector utiliza o certificado de cliente para a comunicação futura com o serviço de Proxy de aplicações.
5. O conector efetua uma solicitação inicial dos dados de sistema de configuração do serviço utilizando o respetivo certificado de cliente e está agora pronto para tirar pedidos.

### <a name="updating-the-configuration-settings"></a>Ao atualizar as definições de configuração

Sempre que o serviço de Proxy de aplicações atualiza as definições de configuração, os seguintes eventos de fluxo ocorrer:

1. O conector estabelece ligação ao ponto final de configuração do serviço Proxy de aplicações utilizando o respetivo certificado de cliente.
2. Depois do certificado de cliente é validado, o serviço de Proxy da aplicação devolve dados de configuração para o conector (por exemplo, o grupo de conector que o conector, deve fazer parte do).
3. Se o certificado atual é mais de 180 dias, o conector gera um novo pedido de certificado, que atualiza eficazmente o certificado de cliente a cada 180 dias.

### <a name="accessing-published-applications"></a>Aceder a aplicações publicadas

Quando os utilizadores acedem uma aplicação publicada, os seguintes eventos ocorrer entre o serviço de Proxy de aplicações e o conector do Proxy de aplicações:

1. [O serviço autentica o utilizador da aplicação](#the-service-checks-the-configuration-settings-for-the-app)
2. [O serviço coloca um pedido na fila de conector](#The-service-places-a-request-in-the-connector-queue)
3. [Um conector processa o pedido da fila](#the-connector-receives-the-request-from-the-queue)
4. [O conector aguarda uma resposta](#the-connector-waits-for-a-response)
5. [O serviço de fluxos de dados para o utilizador](#the-service-streams-data-to-the-user)

Para saber mais sobre o que ocorre em cada um destes passos, mantenha a leitura.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. O serviço autentica o utilizador da aplicação

Se tiver configurado a aplicação utilize Passthrough como o método de pré-autenticação, os passos nesta secção são ignorados.

Se tiver configurado a aplicação preauthenticate com o Azure AD, os utilizadores são redirecionados para o STS do AD do Azure para autenticar e efetuar os seguintes passos:

1. Proxy da aplicação verifica a existência de quaisquer requisitos de política de acesso condicional para a aplicação específica. Este passo garante que o utilizador foi atribuído à aplicação. Se for necessária a verificação de dois passos, a sequência de autenticação pede ao utilizador para um segundo método de autenticação.

2. Depois de todas as verificações passaram, o Azure AD STS emite um token assinado da aplicação e redireciona o utilizador novamente para o serviço de Proxy de aplicações.

3. Proxy da aplicação verifica se o token foi emitido para corrigir a aplicação. Executa outras verificações também, tais como de se certificar de que o token foi assinado pelo Azure AD e que está ainda dentro da janela válida.

4. Conjuntos de Proxy da aplicação tenha ocorrido um cookie de autenticação encriptada para indicar que a autenticação para a aplicação. O cookie inclui um carimbo de expiração com base no token do Azure AD e outros dados, tais como o nome de utilizador a autenticação baseada em. O cookie é encriptado com uma chave privada conhecida apenas para o serviço de Proxy de aplicações.

5. Proxy da aplicação redireciona o utilizador novamente para o URL pedido inicialmente.

Se qualquer parte dos passos pré-autenticação falhar, é negado o pedido do utilizador e o utilizador é apresentado uma mensagem a indicar a origem do problema.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. O serviço coloca um pedido na fila de conector

Conectores manter uma ligação de saída aberta para o serviço de Proxy de aplicações. Quando um pedido é apresentada no, o serviço e escalona as cópias de segurança o pedido das ligações abertas para o conector recolher.

O pedido inclui itens da aplicação, tais como cabeçalhos de pedido, dados de cookie encriptado, o utilizador efetuar o pedido e o ID do pedido. Apesar de dados a partir do cookie encriptado são enviados com o pedido, o cookie de autenticação propriamente dito não é.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. O conector processar o pedido da fila. 

Com base no pedido, o Proxy de aplicações efetua uma das seguintes ações:

* Se o pedido é uma operação simples (por exemplo, não há nenhum dados dentro do corpo é com um RESTful *obter* pedido), o conector estabelece uma ligação para o recurso interno de destino e, em seguida, aguarda uma resposta.

* Se o pedido tem dados associados no corpo (por exemplo, um RESTful *POST* operação), o conector estabelece uma ligação de saída utilizando o certificado de cliente para a instância de Proxy de aplicações. Torna esta ligação para os dados de pedido e abrir uma ligação para o recurso interno. Depois de receber o pedido do conector do, o serviço de Proxy da aplicação começa a aceitar conteúdo de utilizador e reencaminha dados para o conector. O conector, por sua vez, reencaminha os dados para o recurso interno.

#### <a name="4-the-connector-waits-for-a-response"></a>4. O conector aguarda uma resposta.

Depois do pedido e a transmissão de todo o conteúdo para o back-end estiver concluída, o conector aguarda uma resposta.

Depois de receber uma resposta, o conector estabelece uma ligação de saída para o serviço de Proxy de aplicações, para devolver os detalhes do cabeçalho e começar a transmissão em fluxo a devolução de dados.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. O serviço de fluxos de dados para o utilizador. 

Alguns processamento da aplicação pode ocorrer aqui. Se tiver configurado o Proxy de aplicações para traduzir cabeçalhos ou URLs na sua aplicação, esse processamento acontece conforme necessário durante este passo.


## <a name="next-steps"></a>Passos seguintes

[Considerações de topologia de rede ao utilizar o Proxy de aplicações do Azure AD](application-proxy-network-topology-considerations.md)

[Compreender os conectores de Proxy de aplicações do Azure AD](application-proxy-understand-connectors.md)
