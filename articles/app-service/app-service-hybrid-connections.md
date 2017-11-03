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
ms.date: 04/22/2017
ms.author: ccompy
ms.openlocfilehash: fef9e7b280387934cb093f51b4c8aa134a3b87e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Ligações híbridas do App Service do Azure #

## <a name="overview"></a>Descrição geral ##

As ligações híbridas é de um serviço no Azure, bem como uma funcionalidade do serviço de aplicações do Azure.  Como um serviço tem utilização e funcionalidades para além das que são aproveitadas no App Service do Azure.  Para saber mais sobre as ligações híbridas e respetiva utilização fora do App Service do Azure pode começar aqui, [ligações de híbridas de reencaminhamento do Azure][HCService]

No App Service do Azure, as ligações híbridas podem ser utilizadas para aceder a recursos de aplicação nas outras redes. Fornece acesso a partir da sua aplicação para um ponto final da aplicação.  Não ativa uma capacidade alternativa aceder à sua aplicação.  Como utilizar o serviço de aplicações, cada ligação híbrida está correlacionada com uma combinação de anfitrião e a porta TCP único.  Isto significa que o ponto final da ligação híbrida pode estar em qualquer sistema operativo e qualquer aplicação fornecida que está a atingir uma porta de escuta de TCP. As ligações híbridas não souber ou cuidado o que é o protocolo de aplicação ou está a aceder.  Basta que está a fornecer acesso à rede.  


## <a name="how-it-works"></a>Como funciona ##
A funcionalidade de ligações híbridas é composta por dois chamadas de saída para reencaminhamento do Service Bus.  Há uma ligação a partir de uma biblioteca no anfitrião onde a aplicação está em execução no app service e, em seguida, existe uma ligação entre o Manager(HCM) de ligação híbrida e reencaminhamento do Service Bus.  O HCM é um serviço de reencaminhamento que implementou no alojamento de rede 

Através de duas ligações associadas a aplicação tiver um túnel TCP para uma combinação de anfitrião: porta fixo no outro lado do HCM.  A ligação utiliza TLS 1.2 para segurança e as chaves de SAS de autenticação/autorização.    

![][1]

Quando a aplicação envia um pedido DNS que corresponde a um ponto final da ligação híbrida configurar, o tráfego de saída do TCP será redirecionado para baixo da ligação híbrida.  

> [!NOTE]
> Isto significa que deve tentar utilizar sempre um nome DNS para a sua ligação híbrida.  Algum software de cliente não efetua uma pesquisa de DNS se o ponto final utiliza um endereço IP em vez disso.
>
>

Existem dois tipos de ligações híbridas, as ligações híbridas novas que são oferecidas como um serviço de reencaminhamento do Azure e o ligações híbridas mais antiga do BizTalk.  O ligações híbridas mais antiga do BizTalk são referidas como clássico ligações híbridas no portal.  Não há mais informações posteriormente neste documento sobre os mesmos.

### <a name="app-service-hybrid-connection-benefits"></a>Vantagens de ligação do serviço de aplicações híbridas ###

Existem diversos benefícios para o híbrida ligações capacidade, incluindo

- As aplicações podem aceder de forma segura nos sistemas no local e serviços em segurança
- a funcionalidade não necessita de um ponto final acessível de internet
- é um processo rápido e fácil de configurar  
- cada ligação híbrida corresponde a uma combinação de anfitrião: porta única que é um aspeto de segurança excelente
- Normalmente, não requer holes de firewall, as ligações são toda a saída através das portas de web padrão
- porque a funcionalidade de nível de rede que também significa que é agnóstico relativamente ao idioma utilizado pela sua aplicação e a tecnologia utilizada pelo ponto final
- pode ser utilizado para fornecer acesso em várias redes a partir de uma única aplicação 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Ações que não pode fazer com as ligações híbridas ###

Existem algumas coisas que não pode fazer com as ligações híbridas e incluem:

- montar a unidade
- utilizar UDP
- TCP de acesso baseado em serviços que utilizam portas dinâmicas como modo passivo de FTP ou modo passivo expandido
- Suporte LDAP, como, por vezes, necessita de UDP
- Suporte de diretório Active Directory

## <a name="adding-and-creating-a-hybrid-connection-in-your-app"></a>Adicionar e criar uma ligação híbrida na sua aplicação ##

As ligações híbridas podem ser criadas através do portal de aplicação ou a partir do portal de serviço da ligação híbrida.  É altamente recomendado que utilize o portal de aplicação para criar as ligações híbridas que pretende utilizar com a sua aplicação.  Para criar uma ligação híbrida, vá para o [portal do Azure] [ portal] e vá para a IU para a sua aplicação.  Selecione **redes > configurar pontos finais da sua ligação híbrida**.  Aqui pode ver as ligações híbridas que estão configurados com a sua aplicação  

![][2]

Para adicionar uma nova ligação híbrida, clique em Adicionar da ligação híbrida.  A IU que se abre lista as ligações híbridas que já tenha criado.  Para adicionar um ou mais dos mesmos para a sua aplicação, clique naqueles que pretende e acessos **adicionar selecionada da ligação híbrida**.  

![][3]

Se pretender criar uma nova ligação híbrida, clique em **criar nova ligação híbrida**.  A partir daqui, especificar a: 

- nome do ponto final
- nome de anfitrião de ponto final
- porta do ponto final
- espaço de nomes de barramento de serviço que pretende utilizar

![][4]

Cada ligação híbrida está associada a um espaço de nomes de barramento de serviço e cada espaço de nomes de barramento de serviço está numa região do Azure.  É importante tentar utilizar um espaço de nomes de barramento de serviço na mesma região que a sua aplicação, de modo a evitar a latência de rede induzida pelo.

Se pretender remover a ligação híbrida da sua aplicação, com o botão direito clique no mesmo e selecione **desligar**.  

Depois de uma ligação híbrida é adicionada à sua aplicação web, pode ver os detalhes no mesmo ao clicar no mesmo.  

![][5]

## <a name="hybrid-connections-and-app-service-plans"></a>As ligações híbridas e planos de serviços aplicacionais ##

As ligações híbridas só que pode agora efetuar são novas ligações híbridas.  Só estão disponíveis no básicas, Standard, Premium e Isolated SKUs de preço.  Existem limites associados para o plano de preços.  

| Plano de preços | Número de ligações híbridas utilizáveis no plano de |
|----|----|
| Básica | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolado | 200 |

Uma vez que existem restrições plano do App Service também é IU no plano do App Service que mostra estão a ser utilizadas como muitas ligações híbridas e que aplicações.  

![][6]

Tal como com a vista de aplicação, pode ver os detalhes na sua ligação híbrida clicando nela.  As propriedades mostradas aqui, pode ver todas as informações que tinha na vista de aplicação, mas também pode ver como muitas outras aplicações no mesmo plano do App Service estiver a utilizar essa ligação híbrida.

![][7]

Enquanto existir um limite no número de pontos finais da ligação híbrida que podem ser utilizados num plano do App Service, cada ligação híbrida utilizada pode ser utilizada em qualquer número de aplicações que plano do App Service.  Isto é dizer o que posso tinha 1 da ligação híbrida que utilizei 5 aplicações separadas no meu plano do App Service, que é ainda apenas 1 da ligação híbrida.

Não há um custo adicional para as ligações híbridas para além de que está a ser apenas utilizável na básico, Standard, Premium ou SKU isolado.  Para obter detalhes sobre preços da ligação híbrida aceda aqui: [preços do Service Bus][sbpricing].

## <a name="hybrid-connection-manager"></a>Gestor de ligações híbridas ##

Para as ligações híbridas trabalhar é necessário um agente de reencaminhamento na rede que aloja o ponto de final de ligação híbrida.  Esse agente de reencaminhamento denomina-o Gestor de ligação híbrida (HCM).  Esta ferramenta pode ser transferida a partir de **redes > configurar pontos finais da sua ligação híbrida** IU disponível a partir da sua aplicação no [portal do Azure][portal].  

Esta ferramenta é executada no Windows server 2008 R2 e versões posteriores do Windows.  Uma vez instalado o executa HCM como um serviço.  Este serviço liga-se para reencaminhamento de servicebus do Azure com base nos pontos finais configurados.  As ligações a partir de HCM são saídas para as portas 80 e 443.    

O HCM tem uma IU para configurá-lo.  Após a instalação de HCM pode trazer a IU executando HybridConnectionManagerUi.exe que se encontra no diretório de instalação do Gestor de ligações híbridas.  É também facilmente atingido no Windows 10, escrevendo *IU do Gestor de ligações híbridas* na sua caixa de pesquisa.  

Quando é iniciada a IU de HCM, a primeira coisa que vê é uma tabela que apresenta uma lista de todas as ligações híbridas que estão configuradas com esta instância do HCM.  Se pretender efetuar alterações, terá de autenticar com o Azure. 

Para adicionar um ou mais ligações híbridas ao seu HCM:

1. Iniciar a IU da HCM
1. Clique em configurar outra ligação híbrida![][8]

1. Inicie sessão com a sua conta do Azure
1. Escolha uma subscrição
1. Clique nas ligações híbridas que pretende que este HCM para reencaminhamento![][9]

1. Clicar em Guardar

Neste momento, verá as ligações híbridas que adicionou.  Também pode clicar em da ligação híbrida configurado e ver detalhes sobre a ligação.

![][10]

Para sua HCM suportar as ligações híbridas que está configurado com, tem de:

- Acesso TCP para o Azure através das portas 80 e 443
- Acesso TCP para o ponto final da ligação híbrida
- capacidade para fazer DNS ups aspeto no anfitrião de ponto final e o espaço de nomes de barramento de serviço do azure

O HCM suporta as novas ligações híbridas, bem como as ligações híbridas mais antigas do BizTalk.

### <a name="redundancy"></a>Redundância ###

Cada HCM pode suportar várias ligações híbridas.  Além disso, qualquer ligação híbrida fornecido pode ser suportada por várias HCMs.  O comportamento predefinido é o round robin tráfego entre os HCMs configurados para qualquer ponto final especificado.  Se quiser elevada disponibilidade no seu ligações híbridas da sua rede, basta instanciar HCMs vários em computadores separados. 

### <a name="manually-adding-a-hybrid-connection"></a>Adicionar manualmente uma ligação híbrida ###

Se quiser alguém fora da sua subscrição para alojar uma instância HCM para uma ligação híbrida fornecido, pode partilhar com os mesmos a cadeia de ligação de gateway para a ligação híbrida.  Pode ver este nas propriedades de uma ligação híbrida no [portal do Azure][portal]. Para utilizar essa cadeia, clique em de **configurar manualmente** clique no botão no HCM e cole a cadeia de ligação de gateway.


## <a name="troubleshooting"></a>Resolução de problemas ##

Quando a ligação híbrida está configurada com uma aplicação em execução e houver HCM, pelo menos, um que tenha essa ligação híbrida configurada, em seguida, irá indicar o estado **ligado** no portal.  Se não supor que **ligado** , em seguida, significa que a aplicação está inativo ou o HCM não é possível ligar a saída para o Azure nas portas 80 ou 443.  

O principal motivo que os clientes não é possível ligar ao respetivo ponto final é porque o ponto final foi especificado um endereço IP a utilizar em vez de um nome DNS.  Se a aplicação não é possível alcançar o ponto final pretendido e utilizou um endereço IP, mude para utilizar um nome DNS que é válido no anfitrião onde o HCM está em execução.  São outros aspetos a verificar se o nome DNS é resolvido corretamente no anfitrião onde o HCM está em execução e se existe conectividade do anfitrião onde o HCM está em execução para o ponto final da ligação híbrida.  

Há uma ferramenta com o serviço de aplicações que pode ser invocada a partir da consola do chamado tcpping.  Esta ferramenta pode indicar-lhe se tiver acesso a um ponto final TCP, mas não indicam se tem acesso a um ponto final da ligação híbrida.  Quando utilizar a consola de um ponto final da ligação híbrida, um ping enviado com êxito irá apenas indicam que tem uma ligação híbrida configurada para a sua aplicação que utiliza essa combinação de anfitrião: porta.  

## <a name="biztalk-hybrid-connections"></a>Ligações Híbridas do BizTalk ##

A capacidade de ligações do BizTalk híbridas mais antiga foi fechada desativado para adicionais criações de BizTalk da ligação híbrida.  Pode continuar a utilizar o seu as ligações híbridas do BizTalk pré-existentes com as suas aplicações, mas deve migrar para o novo serviço.  Entre as vantagens no novo serviço sobre a versão de BizTalk são:

- Não é necessária nenhuma conta de BizTalk adicional
- É de TLS 1.2, em vez de 1.0 como ligações híbridas de BizTalk
- A comunicação é através das portas das portas 80 e 443 utilizando um nome DNS para alcançar Azure em vez de endereços IP e um intervalo de adicionais outros.  

Para adicionar uma ligação híbrida do BizTalk à sua aplicação, aceda à sua aplicação no [portal do Azure] [ portal] e clique em **redes > configurar pontos finais da sua ligação híbrida**.  Na tabela de ligações híbridas do clássico clique **adicionar ligação híbrida clássico**.  A partir daqui, verá uma lista das suas ligações do BizTalk híbrida.  


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

