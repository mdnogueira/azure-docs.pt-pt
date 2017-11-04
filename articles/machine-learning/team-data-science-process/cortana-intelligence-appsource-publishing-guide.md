---
title: "Guia de publicação do Cortana Intelligence AppSource | Microsoft Docs"
description: "Como Partner Microsoft, seguem-se todos os passos que tem de seguir para publicar a sua solução do Cortana Intelligence AppSource."
services: machine-learning
documentationcenter: 
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: anupams;v-bruham;garye
ms.openlocfilehash: 9f867641b77c8148c1d6cbf2913da9c1f5ce5b71
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="cortana-intelligence-appsource-publishing-guide"></a>Guia de publicação do Cortana Intelligence AppSource

## <a name="overview"></a>Descrição geral
AppSource é o único destino para decisores de negócios (BDMs) para detetar e tente perfeitamente a aplicações empresariais de soluções/criada por parceiros e avaliado pela Microsoft. Veja [este vídeo](https://youtu.be/hpq_Y9LuIB8) para saber como funciona o AppSource. 

Como Partner Microsoft, realmente podem beneficiar da publicação no AppSource se tiver:
- Criada com uma solução inteligente/aplicação [Cortana Intelligence Suite](https://azure.microsoft.com/en-us/suites/cortana-intelligence-suite/?cdn=disable).
- A aplicação ou solução corrige um problema empresarial específico.
- Criou módulos ou de propriedade intelectual que os seus clientes podem reutilizar relativamente rapidamente de forma previsível.

Observe [soluções do Cortana Intelligence](https://appsource.microsoft.com/en-us/marketplace/apps?product=cortana-intelligence&page=1) que já são publicados no AppSource. 

Este artigo irá guiá-os passos para obter a solução do Cortana Intelligence de um parceiro publicada AppSource

## <a name="getting-started"></a>Introdução
1. No [parceiro Comunidade vantagens guia](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Partner%20Community%20Benefits%20Guide%20-%20Cloud%20and%20Enterprise.pdf) (PDF), consulte a página 9 para obter listado como um parceiro de análise avançadas.
1. Concluir o [submeter a sua aplicação](https://appsource.microsoft.com/en-us/partners/list-an-app) formulário.

    Para a pergunta *"Escolher os produtos que a aplicação é criada para*", verifique o **outros** controlo de "Cortana Intelligence" na edição de caixa de verificação e lista.
1. Antes de uma aplicação do Cortana Intelligence pode obter integrado para AppSource, tem de obter certificada pela equipa de tecnologia de solução de parceiros do Cortana Intelligence. Para obter que processo iniciado, partilha kindly detalhes sobre a sua aplicação através do preenchimento no formulário de inquérito na [avaliação de solução da Cortana Intelligence para publicação AppSource](https://aka.ms/cisappsrceval). Este site está protegido de palavra-passe e o site tem instruções sobre como obter a palavra-passe.

## <a name="solution-evaluation-criteria"></a>Critérios de avaliação de solução
Eis a lista dos critérios que tem de cumprir a aplicação
1. Tem de aplicações empresariais específicos do endereço utiliza problema maiúsculas dentro de uma determinada área funcional de forma repetíveis com configurações mínimas para propostas de valor predefinido implementable num período curto.
1. Solução deve utilizar pelo menos um dos seguintes componentes:

    - HDInsight
    - Machine Learning
    - Data Lake Analytics
    - Stream Analytics
    - Serviços Cognitivos
    - Bot Framework
    - Analysis Services
    - Microsoft R Server funcionar autonomamente
    - Serviços de R no SQL Server 2016 ou o HDInsight Premium
1. Solução deve ser geração de, pelo menos, $1000 mês por cliente utilizar DPOR/CSP.
1. Solução deve utilizar o signon único do Azure Active Directory Federado (SSO federado o AAD) com consentimento ativado para autenticação de utilizador e controlos de acesso de recursos. Terá de mostrar à equipa de avaliação se a sua solução é que ativada antes da aplicação pode ser integrado para AppSource SSO federado o AAD.

     Para ver o que significa ser AAD federado SSO ativado, procurar o ressarcimento de posição 02:35 no [percorrer a experiência de avaliação gratuita AppSource](https://aka.ms/trialexperienceforwebapps) vídeo. Se a aplicação não estiver ativada com o AAD federado SSO ainda, eis algumas documentação sobre o assunto
   1. [Início de sessão de um clique](https://identity.microsoft.com/Landing?ru=https://identity.microsoft.com/).
   1. [Integrar aplicações com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application).
     
1. Utilizar o Power BI na sua solução: opcional mas vivamente recomendado como é provada para gerar o número mais elevado de clientes potenciais clientes potenciais.

## <a name="devcenter-account-setup"></a>Configuração da conta DevCenter
Este é o processo de registar a sua empresa para tornar-se de um fabricante com a Microsoft. Se já for um publicador registado com uma conta existente do DevCenter, partilhe o ID de correio eletrónico associado à conta DevCenter. Assim que a aplicação for aprovada para publicação, irá obter acesso a documentação completa [perfil do publicador de gerir do Portal de nuvem](https://cloudpartner.azure.com/#documentation/manage-publisher-profile)

Se não tiver um, são os principais passos para configurar uma conta de DevCenter abaixo.
1. Criar uma conta Microsoft [aqui](https://signup.live.com/signup.aspx).
1. Vá para a Microsoft DevCenter [página registo](http://go.microsoft.com/fwlink/?LinkId=615100) e clique em "inscrever-se".
1. Quando lhe for pedido para pagamento, utilize o código que fornecemos-lhe a si. Se não tiver um código, contacte [ appsourcecissupport@microsoft.com ](mailto:appsourcecissupport@microsoft.com?subject=Request%20for%20promotion-code%20for%20DevCenter%20account%20setup).
1. Selecione o [país/região](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees) em que tem em direto ou onde está localizada a sua empresa. **Não poderá alterá-lo posteriormente.**
1. Selecione o [tipo de conta de programador](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees) para AppSource, selecione **empresa**. Para uma conta de empresa, não se esqueça de consultar estes [diretrizes](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account).
1. Introduza as informações de contacto que pretende utilizar para a sua conta de programador.
Para instruções passo a passo detalhadas sobre a configuração DevCenter conta, consulte as instruções [aqui](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration).

## <a name="provide-info-for-microsoft-sellers"></a>Forneça as informações para sellers da Microsoft
Um das propostas de valor de chave de AppSource para parceiros é podem colaborar com a Microsoft Sellers no posicionamento aplicações de parceiros à frente de potenciais clientes.

Preencher [as informações de solução de parceiros para a Microsoft Sellers](https://aka.ms/aapartnerappinfo) e enviá-lo para [ appsourcecissupport@microsoft.com ](mailto:appsourcecissupport@microsoft.com?subject=Request%20publisher%20account%20creation%20for%20%3cPartner%20Name%3e%20and%20whitelist%20owner/contributer%20AAD/MSA%20email%20IDs). Este é um passo necessário para uma aplicação do Cortana Intelligence obter aprovada para publicação.

## <a name="build-a-compelling-customer-walkthrough-on-appsource"></a>Criar uma convincentes instruções de cliente no AppSource
Em primeiro lugar, observe [Neal análise de inventário de otimização](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome) no AppSource. Cada entrada de aplicação AppSource tem título, o resumo (máximas de 100 carateres), descrição (1300 carateres no máximo), imagens, vídeos (opcionais), pdf documentos além entrada apontar para uma experiência de avaliação. Parceiros devem tirar partido dos todas estas para criar uma experiência apelativa do cliente.

Parceiros devem considerar o conteúdo colocam no AppSource como uma orquestração de venda ponto a ponto. Os clientes leem o título e descrição para compreender a proposta de valor e, em seguida, seguir imagens & vídeos para compreender o que é a solução sobre. Casos práticos ajudar potenciais clientes, consulte o artigo existentes como os clientes estão a obter o valor. 

Tudo que isto deve certificar-se o cliente pode interessado e intenção para saber mais. Considere estas como apresentação do decks com base em tipos que uma pessoa de venda técnica boa seria guiá-os clientes novos. O formato da descrição sugerido é dividir o texto em secções subpastas com base no valor propositions, cada um com realçado com um cabeçalho secundárias. 

Normalmente, relance visitantes sobre o campo "oferecem Resumo" e os cabeçalhos secundárias para obter gist de que os endereços de aplicação e por que motivo devem considere de que a aplicação em apenas uma leitura rápida. Por isso, é importante obter o utilizador atenção conceder-lhes um motivo para ler para obter as especificações.

Ver o que efetuou destes parceiros.
- [Análise de Neal inventário Otimização](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview)
- [Personalização de revenda Plexure](https://appsource.microsoft.com/en-us/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)
- [AvePoint Citizen serviços](https://appsource.microsoft.com/en-us/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)

É o ATO final de vendas para mostrar uma demonstração da aplicação/solução que mostra como é entregue a proposta de valor. Que é exatamente o que uma experiência de avaliação do cliente no AppSource destinam-se. Uma demonstração boa será efetuado o seguinte:
- Resumir voltar a proposta de valor da aplicação num curto período e a lista terminar as pessoas fictícias dentro de firm de cliente que seria interagem com a solução
- Indique uma história e define o contexto sobre um cliente de exemplo lidar com problemas específicos
- Explicam como a situação pode geralmente devolve e afetar o cliente (antes) VS que aconteceria se a solução está a ser utilizado. Isto pode ser apresentado utilizar os dashboards de PowerBI etc.
- Resumem a forma como a solução torna acontecer utilizando os algoritmos de Machine Learning específicos etc.

O conteúdo que está a ser adicionado no AppSource deve ser de alta qualidade e stitched cópias de segurança suficientes para ativar o seguinte:
- Utilizadores de vendas técnica do parceiro devem ser utilizar para as respetivas orquestração de venda. As equipas de vendas utilizá-lo é um boa início de sessão que pode esperar MS utilizadores de vendas também a capacidade para o fazer. Isto irá ativar o ponto de cliente de contacto para conseguir o mesmo bloco aos respetivos mates de equipa e agrega superior para orçamento e aprovações antes de pode ser feito um grau de compra de reencaminhamento consistentemente.
- Um cliente visitando o site organically pode percorrer o conteúdo de todos os por si e a funcionalidade entusiasmados responder para comunicação de parceiro mover avançar com próximos passos.

É por esse motivo parceiros devem considerar o conteúdo colocam no AppSource como uma orquestração de venda ponto a ponto. Com base na linha de bloco e funcionalidades para ser mostrada na experiência de avaliação, pode decidir o tipo de oferta.

## <a name="publish-your-app-on-the-publishing-portal"></a>Publicar a aplicação no portal de publicação
Assim que tiver avaliada os passos acima para a sua aplicação, irá obter acesso ao portal de publicação e pode ver [como publicar uma oferta do Cortana Intelligence através do Portal de parceiros de nuvem](https://cloudpartner.azure.com/#documentation/cloud-partner-portal-publish-cortana-intelligence-app) para instruções detalhadas sobre os passos seguintes.

Se precisar de informações sobre qualquer um dos campos, de e-mail < appsourcecissupport@microsoft.com >.
## <a name="my-app-is-published-on-appsource---now-what"></a>A minha aplicação está publicada no AppSource - agora que?
Firstly, parabéns sobre como começar a aplicação publicaram.
O nível de devolve que obtém a partir da sua aplicação AppSource de publicação descontos elevados depende de como pode influencia o público-alvo. Consulte [pirataria crescimento da Cortana Intelligence aplicação AppSource](http://aka.ms/aagrowthhackguide) para obter mais detalhes sobre a forma como pode maximizar a devolve.

Se tiver quaisquer dúvidas ou sugestões, entrar-nos < appsourcecissupport@microsoft.com >.

