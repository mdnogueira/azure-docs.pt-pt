---
title: "As ligações híbridas do App Service do Azure | Microsoft Docs"
description: "Como criar e utilizar ligações híbridas para aceder a recursos em redes diferentes"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: ccompy
ms.openlocfilehash: f0b148cb9c304c54b47be9601740e7634462d59b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Ligações híbridas do App Service do Azure #

## <a name="overview"></a>Descrição geral ##

As ligações híbridas é de um serviço no Azure, bem como uma funcionalidade do serviço de aplicações do Azure.  Como um serviço tem utiliza e funcionalidades para além das que são aproveitadas no App Service do Azure.  Para saber mais sobre as ligações híbridas e respetiva utilização fora do App Service do Azure pode começar aqui, [ligações de híbridas de reencaminhamento do Azure][HCService]

No App Service do Azure, as ligações híbridas pode ser utilizadas para aceder a recursos de aplicação nas outras redes. Fornece acesso a partir da sua aplicação para um ponto final da aplicação.  Não ativa uma capacidade alternativa aceder à sua aplicação.  Como é utilizado no App Service, cada ligação híbrida está correlacionada com uma combinação de anfitrião e a porta TCP único.  Isto significa que o ponto final da ligação híbrida pode estar em qualquer sistema operativo e qualquer aplicação, o que lhe forneceu são atingir uma porta de escuta de TCP. As ligações híbridas não sabe ou cuidado o que é o protocolo de aplicação ou está a aceder.  Estes são simplesmente fornecer acesso à rede.  


## <a name="how-it-works"></a>Como funciona ##
A funcionalidade de ligações híbridas é constituída por dois chamadas de saída para reencaminhamento do Service Bus.  Há uma ligação a partir de uma biblioteca no anfitrião onde a aplicação está em execução no App Service e existe uma ligação do Gestor de ligação híbrida (HCM) para o reencaminhamento do Service Bus.  O HCM é um serviço de reencaminhamento que implementar dentro da rede que aloja o recurso que está a tentar aceder. 

Através de duas ligações associadas, a aplicação tiver um túnel TCP para uma combinação de anfitrião: porta fixo no outro lado do HCM.  A ligação utiliza TLS 1.2 para segurança e as chaves de SAS de autenticação/autorização.    

![Fluxo de alto nível da ligação híbrida][1]

Quando a aplicação envia um pedido DNS que corresponde a um ponto final da ligação híbrida configurado, o tráfego de saída do TCP será redirecionado através da ligação híbrida.  

> [!NOTE]
> Isto significa que deve tentar utilizar sempre um nome DNS para a sua ligação híbrida.  Algum software de cliente não efetua uma pesquisa de DNS se o ponto final utiliza um endereço IP em vez disso.
>
>

Existem dois tipos de ligações híbridas: novas ligações híbridas que são oferecidos como um serviço de reencaminhamento do Azure e o ligações híbridas mais antiga do BizTalk.  O ligações híbridas mais antiga do BizTalk são referidas como clássico ligações híbridas no portal.  Não há mais informações posteriormente neste documento sobre os mesmos.

### <a name="app-service-hybrid-connection-benefits"></a>Vantagens da ligação híbrida de serviço de aplicações ###

Existem diversos benefícios para a capacidade de ligações híbridas, incluindo:

- As aplicações podem aceder de forma segura sistemas no local e serviços em segurança.
- A funcionalidade não necessita de um ponto final acessível através da Internet.
- É um processo rápido e fácil de configurar. 
- Cada ligação híbrida corresponde a uma combinação de anfitrião: porta única, o que é um aspeto de segurança excelente.
- Normalmente, não requer holes de firewall, as ligações são toda a saída através das portas de web padrão.
- Porque a funcionalidade de nível de rede, é agnóstico relativamente ao idioma utilizado pela sua aplicação e a tecnologia utilizada pelo ponto final.
- Pode ser utilizado para fornecer acesso em várias redes a partir de uma única aplicação. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Ações que não pode fazer com as ligações híbridas ###

Existem algumas coisas que não pode fazer com as ligações híbridas, incluindo:

- montar a unidade
- utilizar UDP
- TCP de acesso baseado em serviços que utilizam portas dinâmicas, tais como modo passivo de FTP ou modo passivo expandido
- Suporte LDAP, como, por vezes, necessita de UDP
- Suporte de diretório Active Directory

## <a name="adding-and-creating-a-hybrid-connection-in-your-app"></a>Adicionar e criar uma ligação híbrida na sua aplicação ##

As ligações híbridas podem ser criadas através da sua aplicação de serviço de aplicações no portal do Azure ou de reencaminhamento do Azure no portal do Azure.  Recomenda vivamente que crie ligações híbridas através da aplicação de serviço de aplicações que pretende utilizar com a ligação híbrida.  Para criar uma ligação híbrida, vá para o [portal do Azure] [ portal] e selecione a sua aplicação.  Selecione **redes > configurar pontos finais da sua ligação híbrida**.  Aqui pode ver as ligações híbridas que estão configurados para a sua aplicação.  

![Lista de ligações híbridas][2]

Para adicionar uma nova ligação híbrida, clique em Adicionar da ligação híbrida.  A IU que se abre lista as ligações híbridas que já tenha criado.  Para adicionar um ou mais dos mesmos para a sua aplicação, clique naqueles que pretende e acessos **adicionar selecionada da ligação híbrida**.  

![Portal de ligação híbrida][3]

Se pretender criar uma nova ligação híbrida, clique em **criar nova ligação híbrida**.  Aqui, especifique o: 

- nome do ponto final
- nome de anfitrião de ponto final
- porta do ponto final
- Espaço de nomes de barramento de serviço que pretende utilizar

![Criar uma ligação híbrida][4]

Cada ligação híbrida está associada a um espaço de nomes do Service Bus e cada espaço de nomes de barramento de serviço está numa região do Azure.  É importante tentar utilizar um espaço de nomes do Service Bus na mesma região que a sua aplicação, de modo a evitar a latência de rede induzida pelo.

Se pretender remover a ligação híbrida da sua aplicação, com o botão direito clique no mesmo e selecione **desligar**.  

Depois de uma ligação híbrida é adicionada à sua aplicação, pode ver os detalhes no mesmo ao clicar no mesmo. 

![Detalhes da ligação híbrida][5]

### <a name="creating-a-hybrid-connection-in-the-azure-relay-portal"></a>Criar uma ligação híbrida no portal do reencaminhamento do Azure ###

Para além da experiência do portal na sua aplicação, há também uma capacidade de criar ligações híbridas no portal do Azure reencaminhamento. Para que uma ligação híbrida a ser utilizado pelo serviço de aplicações do Azure-deve satisfazer os critérios de dois. É necessário:

* Exigir a autorização de cliente
* Tem um item de metadados com o nome o ponto final que contenha uma combinação de anfitrião: porta como o valor

## <a name="hybrid-connections-and-app-service-plans"></a>As ligações híbridas e planos de serviços aplicacionais ##

As ligações híbridas só estão disponíveis no básicas, Standard, Premium e Isolated SKUs de preço.  Existem limites associados para o plano de preços.  

> [!NOTE] 
> Só pode criar novas ligações híbridas com base no reencaminhamento do Azure. Não é possível criar novas ligações do BizTalk híbrida.
>

| Plano de preços | Número de ligações híbridas utilizável no plano de |
|----|----|
| Básica | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolado | 200 |

Uma vez que existem restrições plano do App Service, há também IU no plano do App Service que mostra estão a ser utilizadas como muitas ligações híbridas e que aplicações.  

![Propriedades de nível de plano Servie de aplicação][6]

Pode ver os detalhes na sua ligação híbrida clicando nela.  Nas propriedades mostradas aqui, pode ver todas as informações que vimos na vista de aplicação e também pode ver como muitas outras aplicações no mesmo plano do App Service estiver a utilizar essa ligação híbrida.

Enquanto existir um limite no número de pontos finais da ligação híbrida que podem ser utilizados num plano do App Service, cada ligação híbrida utilizado pode ser utilizada em qualquer número de aplicações que plano do App Service.  Por outras palavras, uma ligação híbrida única que é utilizado um plano do App Service 5 aplicações separado conta como 1 da ligação híbrida.

Não há um custo adicional ao utilizar ligações híbridas.  Para obter detalhes sobre os preços da ligação híbrida, leia aqui: [preços do Service Bus][sbpricing].

## <a name="hybrid-connection-manager"></a>Gestor de ligações híbridas ##

Para as ligações híbridas funcione, é necessário um agente de reencaminhamento na rede que aloja o ponto final da ligação híbrida.  Esse agente de reencaminhamento denomina-o Gestor de ligação híbrida (HCM).  Esta ferramenta pode ser transferida a partir de **redes > configurar pontos finais da sua ligação híbrida** IU disponível a partir da sua aplicação no [portal do Azure][portal].  

Esta ferramenta é executada no Windows server 2012 e versões posteriores do Windows.  Uma vez instalado o executa HCM como um serviço.  Este serviço liga-se para reencaminhamento de barramento de serviço do Azure com base nos pontos finais configurados.  As ligações a partir de HCM são saídas no Azure através da porta 443.    

O HCM tem uma IU para configurá-lo.  Depois do HCM estiver instalado, pode trazer a IU executando HybridConnectionManagerUi.exe que se encontra no diretório de instalação do Gestor de ligações híbridas.  É também facilmente atingido no Windows 10, escrevendo *IU do Gestor de ligações híbridas* na sua caixa de pesquisa.  

![Portal de ligação híbrida][7]

Quando é iniciada a IU de HCM, a primeira coisa que vê é uma tabela que apresenta uma lista de todas as ligações híbridas que estão configurados com esta instância do HCM.  Se pretender efetuar quaisquer alterações, terá de autenticar com o Azure. 

Para adicionar um ou mais ligações híbridas ao seu HCM:

1. Iniciar a IU da HCM
1. Clique em configurar outra ligação híbrida ![adicionar um HC o HCM][8]

1. Inicie sessão com a sua conta do Azure
1. Escolha uma subscrição
1. Clique nas ligações híbridas pretende HCM para reencaminhamento ![selecionar um HC][9]

1. Clicar em Guardar

Neste momento, verá as ligações híbridas que adicionou.  Também pode clique na ligação híbrida configurado e ver detalhes acerca das.

![Detalhes HC][10]

Para sua HCM suportar as ligações híbridas está configurado com, tem de:

- Acesso TCP para o Azure através das portas 80 e 443
- Acesso TCP para o ponto final da ligação híbrida
- Capacidade para fazer look-ups DNS no anfitrião de ponto final e o espaço de nomes do Service Bus do Azure

O HCM suporta as novas ligações híbridas, bem como o ligações híbridas mais antiga do BizTalk.

> [!NOTE]
> Reencaminhamento do Azure baseia-se em Web Sockets para a conectividade. Esta funcionalidade só está disponível no Windows Server 2012 ou mais recente. Devido a que o Gestor de ligações híbridas não é suportado qualquer anterior ao Windows Server 2012.
>

### <a name="redundancy"></a>Redundância ###

Cada HCM pode suportar várias ligações híbridas.  Além disso, qualquer ligação híbrida fornecido pode ser suportada por várias HCMs.  O comportamento predefinido é o round robin tráfego entre os HCMs configurados para qualquer ponto final especificado.  Se quiser elevada disponibilidade no seu ligações híbridas da sua rede, basta instanciar HCMs vários em computadores separados. 

### <a name="manually-adding-a-hybrid-connection"></a>Adicionar manualmente uma ligação híbrida ###

Se quiser alguém fora da sua subscrição para alojar uma instância HCM para um determinado da ligação híbrida, pode partilhar a cadeia de ligação de gateway para a ligação híbrida com os mesmos.  Pode ver este nas propriedades para uma ligação híbrida no [portal do Azure][portal]. Para utilizar essa cadeia, clique em de **introduzir manualmente** clique no botão no HCM e cole a cadeia de ligação de gateway.


## <a name="troubleshooting"></a>Resolução de problemas ##

O estado da ligação para uma ligação híbrida significa que, pelo menos, um HCM está configurado com essa ligação híbrida e é capaz de alcançar o Azure.  Se o estado da ligação híbrida não indicar **ligado**, em seguida, a ligação híbrida não está configurada em qualquer HCM que tenha acesso ao Azure.

O principal motivo que os clientes não é possível ligar ao respetivo ponto final é porque o ponto final foi especificado um endereço IP a utilizar em vez de um nome DNS.  Se a aplicação não é possível alcançar o ponto final pretendido e utilizou um endereço IP, mude para utilizar um nome DNS que é válido no anfitrião onde o HCM está em execução.  São outros aspetos a verificar se o nome DNS é resolvido corretamente no anfitrião onde o HCM está em execução e se existe conectividade do anfitrião onde o HCM está em execução para o ponto final da ligação híbrida.  

Há uma ferramenta no App Service pode ser invocada a partir da consola de ferramentas avançadas (Kudu) chamada tcpping.  Esta ferramenta pode indicar-lhe se tem acesso a um ponto final TCP, mas não indicam se tem acesso a um ponto final da ligação híbrida.  Quando utilizar a consola de um ponto final da ligação híbrida, um ping enviado com êxito irá apenas indicam que tem uma ligação híbrida configurado para a sua aplicação que utiliza essa combinação de anfitrião: porta.  

## <a name="biztalk-hybrid-connections"></a>Ligações Híbridas do BizTalk ##

A capacidade de ligações do BizTalk híbridas mais antiga foi fechada desativado para novas ligações do BizTalk híbrida.  Pode continuar a utilizar o seu as ligações híbridas do BizTalk existente com as suas aplicações, mas deve migrar para o novo ligações híbridas que utilizam o reencaminhamento do Azure.  Entre as vantagens no novo serviço sobre a versão de BizTalk são:

- Não é necessária nenhuma conta de BizTalk adicional.
- TLS é 1.2 em vez de 1.0 como ligações do BizTalk híbridas.
- A comunicação é através das portas das portas 80 e 443 utilizando um nome DNS para alcançar Azure em vez de endereços IP e um intervalo de adicionais outros.  

Para adicionar uma ligação de híbrida BizTalk existente à sua aplicação, aceda à sua aplicação no [portal do Azure] [ portal] e clique em **redes > configurar pontos finais da sua ligação híbrida**.  Na tabela clássico ligações híbridas, clique em **da ligação híbrida clássico adicionar**.  Aqui, verá uma lista das suas ligações do BizTalk híbrida.  


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png

<!--Links-->
[HCService]: http://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: http://portal.azure.com/
[oldhc]: http://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: http://azure.microsoft.com/pricing/details/service-bus/
