---
title: "O que são Logic Apps?"
description: Saiba mais sobre Logic Apps do App Service
author: kevinlam1
manager: dwrede
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 07765c05-72a6-4169-a8ab-f6420bfbaf07
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/12/2016
ms.author: klam
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 44941876c976fdad0303b3c638ed6a3811136634


---
# <a name="what-are-logic-apps"></a>O que são Logic Apps?
As Aplicações Lógicas proporcionam uma forma para simplificar e implementar integrações escaláveis e fluxos de trabalho na nuvem. Fornece um designer visual para modelar e automatizar o processo como uma série de passos conhecidos como fluxo de trabalho.  Existem [muitos conetores](../connectors/apis-list.md) na nuvem e no local para integração rápida em serviços e protocolos.  Uma aplicação lógica começa com um acionador (por exemplo, «Quando uma conta é adicionada ao Dynamics CRM») e depois de acionadas podem iniciar muitas ações de combinações, conversões e lógica de condição.

As vantagens de usar Aplicações Lógicas incluem as seguintes:  

* Poupar tempo ao conceber processos complexos utilizando ferramentas de criação mais simples de compreender
* Implementar padrões e fluxos de trabalho de forma totalmente integrada, que de outra forma seriam difíceis de implementar em código
* Começar rapidamente a partir de modelos
* Personalizar uma aplicação lógica com APIs personalizadas próprias, códigos e ações
* Ligar e sincronizar sistemas diferentes no local e na nuvem
* Criar com base no BizTalk server, Gestão de API, Funções do Azure e Azure Service Bus com o suporte de integração de primeira classe

Aplicações Lógicas é uma iPaaS completamente gerida (Plataforma de Integração como serviço) permitindo aos programadores não terem de se preocupar com a criação de alojamento, escalabilidade, disponibilidade e gestão.  As Aplicações Lógicas irão aumentar verticalmente de maneira automática para satisfazer o pedido.

![Designer de aplicação de fluxo](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

Conforme mencionado, é possível automatizar os processos de negócios com as Aplicações Lógicas. Eis alguns exemplos:  

* Mover os ficheiros carregados num um servidor FTP para o Armazenamento do Azure
* Processar e encaminhar encomendas no local e em sistemas de nuvem
* Monitorizar todos os tweets sobre um determinado tópico, analisar o sentimento e criar alertas e tarefas para itens que necessitam de seguimento.

Estes cenários podem ser totalmente configurados a partir do designer visual e sem ter de escrever uma única linha de código. Introdução a [complicar agora a sua aplicação lógica][criar].  Depois de escrito - Uma aplicação lógica pode ser [implementada e reconfigurada rapidamente](app-service-logic-create-deploy-template.md) em vários ambientes e regiões.

## <a name="why-logic-apps"></a>Porquê Logic Apps?
As Aplicações Lógicas proporcionam velocidade e escalabilidade ao espaço de integração empresarial.  A facilidade de utilização do estruturador, a variedade de acionadores e ações disponíveis e as ferramentas de gestão poderosas tornam a centralização das suas APIs mais simples do que nunca.  À medida que as empresas mudam para a digitalização, as Aplicações Lógicas permitem-lhe interligar sistemas de legado e inovadores.

Além disso, com o nosso [Enterprise Integration Accoun][biztalk] pode dimensionar para cenários de integração madura com a potência de [mensagem XML][xml], [gestão de parceiros comerciais][tpm] e muito mais.

* **Ferramentas de design fáceis de utilizar** – As Aplicações Lógicas podem ser concebidas ponto-a-ponto no browser ou com ferramentas do Visual Studio. Comece com um acionador - A partir de uma agenda simples para quando é criado um problema no GitHub. Em seguida, estabeleça várias ações utilizando a extensa galeria de funcionalidades de conectores.
* **Ligar APIs facilmente** – Até as tarefas de composição que são fáceis de descrever são difíceis de implementar em código. As Aplicações Lógicas permitem ligar sistemas diferentes de forma simples. Pretende ligar a sua solução de marketing na nuvem ao seu sistema de faturação no local? Pretende centralizar o processamento de mensagens através de APIs e sistemas com um Enterprise Service Bus? As Logic Apps são a forma mais fácil e fiável de fornecer soluções para esses problemas.
* **Começar rapidamente a partir de modelos** – Para ajudá-lo a começar, fornecemos uma [galeria de modelos][modelos] que permitem criar rapidamente algumas soluções comuns. Desde soluções avançadas de B2B a uma simples conectividade do SaaS, e mesmo algumas que são apenas “por diversão” – A galeria é a forma mais rápida de compreender a capacidade das Aplicações Lógicas.
* **Extensibilidade integrada** – Não encontra o conetor de que precisa? As Aplicações Lógicas foram concebidas para funcionar com as suas próprias APIs e código; pode criar facilmente a sua aplicação API para utilização como um conetor personalizado ou aceder a uma [Função do Azure](https://functions.azure.com) para executar fragmentos de código a pedido. 
* **Potência de integração real** - Comece fácil e aumente conforme necessário. As Logic Apps podem facilmente tirar partido das capacidades do BizTalk, a solução de integração líder de mercado da Microsoft, para permitir que profissionais da integração compilem as soluções que precisam. Saiba mais sobre o [Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md).

## <a name="logic-app-concepts"></a>Conceitos da Aplicação Lógica
As seguintes são algumas das peças-chave que compõem a experiência das Logic Apps. 

* **Fluxo de trabalho** - As Logic Apps fornecem uma forma gráfica para modelar os seus processos de negócios como uma série de passos ou um fluxo de trabalho.
* **Conetores Geridos** – As suas aplicações lógicas necessitam de aceder a dados e serviços. Os conetores geridos são criados especificamente para ajudar quando estiver a estabelecer ligação e a trabalhar com os seus dados. Veja a lista dos conetores disponíveis nos [conetores geridos][managedapis].
* **Acionadores** - Alguns Conetores Geridos podem também agir como acionador. Um acionador inicia uma nova instância de um fluxo de trabalho baseado num evento específico, tal como a chegada de uma mensagem de correio eletrónico ou uma alteração na sua conta do Storage do Azure.
* **Ações** - Cada passo após o acionar num fluxo de trabalho é designado uma ação. Cada ação normalmente mapeia para uma operação no seu conetor gerido ou aplicações API personalizadas.
* **Enterprise Integration Pack** - Para cenários de integração mais avançados, as Aplicações Lógicas incluem capacidades do Biztalk. O BizTalk é a plataforma de integração líder de mercado da Microsoft. Os conetores do Enterprise Integration Pack permitem a fácil inclusão de validação, transformação e muito mais nos seus fluxos de trabalho de Aplicações Lógicas.

## <a name="getting-started"></a>Introdução
* Para começar com as Logic Apps, siga o tutorial [criar uma Aplicação Lógica][criar].  
* [Ver exemplos e cenários comuns](app-service-logic-examples-and-scenarios.md)
* [Pode automatizar os processos de negócios com Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Saiba como integrar os seus sistemas com Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)

[biztalk]: app-service-logic-enterprise-integration-accounts.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[criar]: app-service-logic-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: app-service-logic-enterprise-integration-accounts.md
[xml]: app-service-logic-enterprise-integration-b2b.md
[modelos]: app-service-logic-use-logic-app-templates.md



<!--HONumber=Nov16_HO2-->


