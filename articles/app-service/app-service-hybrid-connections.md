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
ms.openlocfilehash: 677642e4e97523ed71ff5857ae27263743dca535
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Ligações híbridas do App Service do Azure #

As ligações híbridas é um serviço no Azure e uma funcionalidade no App Service do Azure. Como um serviço, tem capacidades para além das que são utilizados no App Service e utiliza. Para saber mais sobre as ligações híbridas e respetiva utilização fora do serviço de aplicações, consulte o artigo [Azure reencaminhamento híbrido ligações][HCService].

Dentro do serviço de aplicações, as ligações híbridas pode ser utilizadas para aceder a recursos de aplicação nas outras redes. Fornece acesso a partir da sua aplicação para um ponto final da aplicação. Não ativa uma capacidade alternativa aceder à sua aplicação. Como é utilizado no App Service, cada ligação híbrida está correlacionada com uma combinação de anfitrião e a porta TCP único. Isto significa que o ponto final da ligação híbrida pode estar em qualquer sistema operativo e qualquer aplicação, o que lhe forneceu estão a aceder a uma porta de escuta de TCP. A funcionalidade de ligações híbridas não souber ou cuidado o que é o protocolo de aplicação ou está a aceder. Basta que está a fornecer acesso à rede.  


## <a name="how-it-works"></a>Como funciona ##
A funcionalidade de ligações híbridas é constituída por dois chamadas de saída para reencaminhamento do Azure Service Bus. Há uma ligação a partir de uma biblioteca no anfitrião onde a aplicação está em execução no App Service. Também é uma ligação do Gestor de ligação híbrida (HCM) para o reencaminhamento do Service Bus. O HCM é um serviço de reencaminhamento que implementar dentro da rede que aloja o recurso que está a tentar aceder. 

Através de duas ligações associadas, a aplicação tiver um túnel TCP para uma combinação de anfitrião: porta fixo no outro lado do HCM. A ligação utiliza TLS 1.2 para segurança e as chaves de assinatura (SAS) de acesso partilhado para autenticação e autorização.    

![Diagrama de fluxo de alto nível da ligação híbrida][1]

Quando a aplicação envia um pedido DNS que corresponde a um ponto final da ligação híbrida configurado, o tráfego de saída do TCP será redirecionado através da ligação híbrida.  

> [!NOTE]
> Isto significa que deve tentar utilizar sempre um nome DNS para a sua ligação híbrida. Algum software de cliente não efetua uma pesquisa de DNS se o ponto final utiliza um endereço IP em vez disso.
>
>

A funcionalidade de ligações híbridas tem dois tipos: as ligações híbridas que são oferecidos como um serviço de reencaminhamento do Service Bus e a mais antiga do Azure BizTalk Services as ligações híbridas. A última opção tem denominada clássica ligações híbridas no portal. Não há mais informações sobre os mesmos neste artigo.

### <a name="app-service-hybrid-connection-benefits"></a>Vantagens da ligação híbrida de serviço de aplicações ###

Existem diversos benefícios para a capacidade de ligações híbridas, incluindo:

- As aplicações podem aceder a sistemas no local e serviços em segurança.
- A funcionalidade não necessita de um ponto final acessível através da internet.
- É um processo rápido e fácil de configurar. 
- Cada ligação híbrida corresponde a uma combinação de anfitrião: porta única, útil para segurança.
- Normalmente, não requer holes de firewall. As ligações são toda a saída através das portas de web padrão.
- Porque a funcionalidade de nível de rede, é agnóstico relativamente ao idioma utilizado pela sua aplicação e a tecnologia utilizada pelo ponto final.
- Pode ser utilizado para fornecer acesso em várias redes a partir de uma única aplicação. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Ações que não pode fazer com as ligações híbridas ###

Existem algumas coisas que não pode fazer com as ligações híbridas, incluindo:

- Ao instalar uma unidade.
- Utilizar UDP.
- Aceder a serviços baseados em TCP que utilizam portas dinâmicas, tais como modo passivo de FTP ou modo passivo expandido.
- Suporte de LDAP, porque é necessária, por vezes, UDP.
- Suporte do Active Directory.

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Adicionar e criar ligações híbridas na sua aplicação ##

Pode criar ligações híbridas através da sua aplicação de serviço de aplicações no portal do Azure ou de reencaminhamento do Azure no portal do Azure. Recomendamos que crie ligações híbridas através da aplicação de serviço de aplicações que pretende utilizar com a ligação híbrida. Para criar uma ligação híbrida, vá para o [portal do Azure] [ portal] e selecione a sua aplicação. Selecione **redes** > **configurar pontos finais da sua ligação híbrida**. Aqui, pode ver as ligações híbridas que estão configurados para a sua aplicação.  

![Lista de captura de ecrã da ligação híbrida][2]

Para adicionar uma nova ligação híbrida, selecione **adicionar ligação híbrida**.  Verá uma lista das ligações híbridas que já tenha criado. Para adicionar um ou mais dos-los para a sua aplicação, seleccione aqueles que pretende e, em seguida, selecione **adicionar selecionada da ligação híbrida**.  

![Portal de captura de ecrã da ligação híbrida][3]

Se pretender criar uma nova ligação híbrida, selecione **criar nova ligação híbrida**. Especifique o: 

- Nome do ponto final.
- Nome de anfitrião de ponto final.
- Porta do ponto final.
- Espaço de nomes de barramento de serviço que pretende utilizar.

![Captura de ecrã de criar nova caixa de diálogo de ligação de híbrida][4]

Cada ligação híbrida está associada a um espaço de nomes do Service Bus e cada espaço de nomes de barramento de serviço está numa região do Azure. É importante tentar utilizar um espaço de nomes do Service Bus na mesma região que a sua aplicação, para evitar a latência de rede induzida pelo.

Se pretender remover a ligação híbrida da sua aplicação, faça duplo clique nele e selecione **desligar**.  

Quando uma ligação híbrida é adicionada à sua aplicação, pode ver os detalhes no mesmo, simplesmente ao selecioná-la. 

![Detalhes de ligações de captura de ecrã de híbrida][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Criar uma ligação híbrida no portal do reencaminhamento do Azure ###

Para além da experiência do portal na sua aplicação, pode criar ligações híbridas no portal do Azure reencaminhamento. Para uma ligação híbrida a ser utilizado pelo serviço de aplicações, deve:

* Necessitam de autorização de cliente.
* Tem um item de metadados, ponto final com o nome que contenha uma combinação de anfitrião: porta como o valor.

## <a name="hybrid-connections-and-app-service-plans"></a>Planos de ligações híbridas e o serviço de aplicações ##

A funcionalidade de ligações híbridas só está disponível no básicas, Standard, Premium e Isolated SKUs de preço. Existem limites associados para o plano de preços.  

> [!NOTE] 
> Só pode criar novas ligações híbridas com base no reencaminhamento do Azure. Não é possível criar novas ligações do BizTalk híbrida.
>

| plano de preços | Número de ligações híbridas utilizável no plano de |
|----|----|
| Básica | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolado | 200 |

Tenha em atenção que o plano de serviço de aplicações mostra quantos ligações híbridas estão a ser utilizadas e que aplicações.  

![Propriedades do plano de captura de ecrã do serviço de aplicações][6]

Selecione a ligação híbrida para ver detalhes. Pode ver todas as informações que vimos na vista de aplicação. Também pode ver como muitas outras aplicações no mesmo plano estiver a utilizar essa ligação híbrida.

Não há um limite no número de pontos finais da ligação híbrida que podem ser utilizados num plano do serviço de aplicações. Cada ligação híbrida utilizado, no entanto, pode ser utilizada em qualquer número de aplicações no que plano. Por exemplo, uma ligação híbrida única que é utilizado em aplicações separadas cinco num plano do serviço de aplicações conta como uma ligação híbrida.

Não há um custo adicional ao utilizar ligações híbridas. Para obter mais informações, consulte [preços do Service Bus][sbpricing].

## <a name="hybrid-connection-manager"></a>Gestor de ligações híbridas ##

A funcionalidade de ligações híbridas requer um agente de reencaminhamento na rede que aloja o ponto final da ligação híbrida. Esse agente de reencaminhamento denomina-o Gestor de ligação híbrida (HCM). Para transferir HCM, da sua aplicação no [portal do Azure][portal], selecione **redes** > **configurar a ligação híbrida pontos finais da sua**.  

Esta ferramenta é executada no Windows Server 2012 e versões posteriores. Quando é instalada, HCM é executado como um serviço que estabelece ligação com o reencaminhamento do Service Bus, com base nos pontos finais configurados. As ligações do HCM são saídas no Azure através da porta 443.    

Depois de instalar HCM, pode executar HybridConnectionManagerUi.exe utilizar a IU para a ferramenta. Este ficheiro está no diretório de instalação do Gestor de ligações híbridas. No Windows 10, pode também simplesmente procurar *IU do Gestor de ligações híbridas* na sua caixa de pesquisa.  

![Captura de ecrã do Gestor de ligações híbridas][7]

Quando iniciar a IU de HCM, a primeira coisa que vê é uma tabela que apresenta uma lista de todas as ligações híbridas que estão configurados com esta instância do HCM. Se pretender efetuar quaisquer alterações, primeiro autenticar com o Azure. 

Para adicionar um ou mais ligações híbridas ao seu HCM:

1. Inicie a IU de HCM.
1. Selecione **configurar outra ligação híbrida**.
![Captura de ecrã do configurar novas ligações híbridas][8]

1. Inicie sessão com a sua conta do Azure.
1. Escolha uma subscrição.
1. Selecione as ligações híbridas que pretende que o HCM para reencaminhamento.
![Captura de ecrã das ligações híbridas][9]

1. Selecione **Guardar**.

Agora, pode ver as ligações híbridas que adicionou. Também pode selecionar a ligação híbrida configurado para ver detalhes.

![Captura de ecrã dos detalhes da ligação híbrida][10]

Para suportar as ligações híbridas está configurado com, HCM requer:

- Acesso TCP para o Azure através das portas 80 e 443.
- Acesso TCP para o ponto final da ligação híbrida.
- A capacidade de fazer look-ups DNS no anfitrião de ponto final e o espaço de nomes do Service Bus.

HCM suporta novas ligações híbridas e as ligações híbridas do BizTalk.

> [!NOTE]
> Reencaminhamento do Azure baseia-se em Web Sockets para a conectividade. Esta funcionalidade só está disponível no Windows Server 2012 ou posterior. Devido a que, HCM não é suportada qualquer anterior ao Windows Server 2012.
>

### <a name="redundancy"></a>Redundância ###

Cada HCM pode suportar várias ligações híbridas. Além disso, qualquer ligação híbrida fornecido pode ser suportada por várias HCMs. É o comportamento predefinido para encaminhar o tráfego entre os HCMs configurados para qualquer ponto final especificado. Se quiser elevada disponibilidade no seu ligações híbridas da sua rede, execute HCMs vários em computadores separados. 

### <a name="manually-add-a-hybrid-connection"></a>Adicionar manualmente uma ligação híbrida ###

Para ativar a alguém fora da sua subscrição para alojar uma instância HCM para uma ligação híbrida fornecido, partilhe a cadeia de ligação de gateway para a ligação híbrida com os mesmos. Pode ver este nas propriedades para uma ligação híbrida no [portal do Azure][portal]. Para utilizar essa cadeia, selecione **introduzir manualmente** na HCM e cole a cadeia de ligação de gateway.


## <a name="troubleshooting"></a>Resolução de problemas ##

O estado de "Ligado" significa que pelo menos um HCM está configurado com essa ligação híbrida e é capaz de alcançar o Azure. Se o estado da ligação híbrida não indicar **ligado**, a ligação híbrida não está configurada em qualquer HCM que tenha acesso ao Azure.

O principal motivo que os clientes não é possível ligar ao respetivo ponto final é porque foi especificado o ponto final utilizando um endereço IP em vez de um nome DNS. Se a aplicação não é possível alcançar o ponto final pretendido e utilizou um endereço IP, mude para utilizar um nome DNS que é válido no anfitrião onde o HCM está em execução. Além disso, verifique se o nome DNS é resolvido corretamente no anfitrião onde o HCM está em execução. Confirme se existe conectividade do anfitrião onde o HCM está em execução para o ponto final da ligação híbrida.  

No App Service, a ferramenta de tcpping pode ser invocada a partir da consola de ferramentas avançadas (Kudu). Esta ferramenta pode indicar-lhe se tem acesso a um ponto final TCP, mas não indicam se tem acesso a um ponto final da ligação híbrida. Quando utilizar a ferramenta na consola do contra um ponto final da ligação híbrida, apenas confirma que utiliza uma combinação de anfitrião: porta.  

## <a name="biztalk-hybrid-connections"></a>Ligações Híbridas do BizTalk ##

A capacidade de ligações do BizTalk híbridas mais antiga foi fechada para novas ligações do BizTalk híbrida. Pode continuar a utilizar o seu as ligações híbridas do BizTalk existente com as suas aplicações, mas deve migrar para o novo ligações híbridas que utilizam o reencaminhamento do Azure. Entre as vantagens no novo serviço sobre a versão de BizTalk são:

- Não é necessária nenhuma conta de BizTalk adicional.
- TLS é a versão 1.2 em vez da versão 1.0.
- Comunicação através das portas 80 e 443 e utiliza um nome DNS para alcançar o Azure, em vez de endereços IP e um intervalo de portas adicionais.  

Para adicionar uma ligação de híbrida BizTalk existente à sua aplicação, aceda à sua aplicação no [portal do Azure][portal]e selecione **redes** > **configurar pontos finais da sua ligação híbrida**. Na tabela clássico ligações híbridas, selecione **da ligação híbrida clássico adicionar**. Em seguida, pode ver uma lista das suas ligações do BizTalk híbrida.  


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
