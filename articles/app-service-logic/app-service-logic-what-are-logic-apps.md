<properties 
    pageTitle="O que são Logic Apps?" 
    description="Saiba mais sobre Logic Apps do App Service" 
    authors="kevinlam1" 
    manager="dwrede" 
    editor="" 
    services="app-service\logic" 
    documentationCenter=""/>

<tags
    ms.service="app-service-logic"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article" 
    ms.date="04/07/2016"
    ms.author="klam"/>

#O que são Logic Apps?

| Referência rápida |
| --------------- |
| [Linguagem de Definição das Logic Apps](https://msdn.microsoft.com/library/azure/mt643789.aspx) |
| [Documentação do Conector das Logic Apps](../connectors/apis-list.md) |
| [Fórum de Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) |

O App Service do Azure é uma PaaS (Plataforma como Serviço) completamente gerida para programadores que torna mais fácil criar Web Apps, móveis e integração. As Logic Apps fazem parte deste conjunto e permitem que qualquer utilizador técnico ou programador automatize a execução do processo de negócios e fluxo de trabalho através de um designer visual de fácil utilização.

Melhor de tudo, as Logic Apps podem ser combinadas com [APIs geridas ][managedapis] incorporadas para ajudar a resolver facilmente até cenários de integração mais complicados: 

![Designer de aplicação de fluxo](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

Conforme mencionado, é possível automatizar os processos de negócios com as Logic Apps. Eis alguns exemplos:  
 
* Pode replicar automaticamente novos registos na SQL Database e, em seguida, enviar correio para o front desk.   
* Encontre automaticamente tweets negativos e envie-os para um canal slack.

Estes cenários podem ser totalmente configurados a partir do designer visual e sem ter de escrever uma única linha de código. Introdução a [complicar agora a sua aplicação lógica][criar].

## Porquê Logic Apps?

As Logic Apps permitem aos programadores conceber fluxos de trabalho que são iniciados a partir de um acionador e, em seguida, executam uma série de passos. Cada passo invoca uma API enquanto tratam, de forma segura, da autenticação e das melhores práticas, tal como checkpointing e uma execução durável.

Se pretende automatizar qualquer processo de negócios (por exemplo, encontrar tweets negativos e publicar no seu canal slack interno ou replicar registos de novos clientes a partir do SQL, enquanto vão chegando, no seu sistema CRM), as Logic Apps facilitam a integração de fontes de dados diferentes, da nuvem para o local. Consulte [APIs geridas][managedapis] para obter mais inspiração e [comece][criar] agora para ver o que pode fazer. 

Além disso, com as nossas [APIs geridas BizTalk ][biztalk] pode escalar cenários de uma integração madura com a potência de um [motor de regras][regras], [gestão do parceiro comercial][tpm], e muito mais.

- **Ferramentas de design fáceis de utilizar** – As Logic Apps podem ser concebidas ponto-a-ponto no browser. Comece com um acionador - a partir de um simples agendamento sempre que surja um tweet sobre a sua empresa. Em seguida, estabeleça várias ações utilizando a extensa galeria de funcionalidades de conectores.

- **Componha facilmente o SaaS** – A composição de tarefas fáceis de descrever são, por vezes, difíceis de implementar em código. As Logic Apps permitem ligar sistemas diferentes de forma simples. Pretende criar uma tarefa no seu software CRM baseado na atividade das suas contas do Facebook e Twitter? Pretende ligar a sua solução de marketing na nuvem ao seu sistema de faturação no local? As Logic Apps são a forma mais fácil e fiável de fornecer soluções para esses problemas.

- **Começar rapidamente a partir de modelos** – Para ajudá-lo a começar, fornecemos uma [galeria de modelos][modelos] que permitem criar rapidamente algumas soluções comuns. Desde soluções BizTalk avançadas a uma simples conectividade do SaaS, e até algumas apenas “por diversão” – a galeria é a forma mais rápida para compreender a capacidade das Logic Apps.

- **Extensibilidade integrada** – Não encontra a API que precisa? As Logic Apps estão concebidas para funcionar com API Apps. Pode criar facilmente a sua própria API de aplicação para utilizar como uma aplicação API personalizada. Compilar uma nova aplicação só para si ou partilhe e rentabilize no mercado.

- **Potência de integração real** - Comece fácil e aumente conforme necessário. As Logic Apps podem facilmente tirar partido das capacidades do BizTalk, a solução de integração líder de mercado da Microsoft, para permitir que profissionais da integração compilem as soluções que precisam. Saiba mais sobre as [capacidades do BizTalk fornecidas com a Logic Apps][biztalk].

## Conceitos da Aplicação Lógica

As seguintes são algumas das peças-chave que compõem a experiência das Logic Apps. 

- **Fluxo de trabalho** - As Logic Apps fornecem uma forma gráfica para modelar os seus processos de negócios como uma série de passos ou um fluxo de trabalho.
- **APIs Geridas** – As suas Logic Apps precisam de aceder aos dados e serviços. As APIs geridas são criados especificamente para ajudar quando estiver a estabelecer ligação com e a trabalhar com os seus dados. Consulte a lista das APIs atualmente disponíveis em [APIs geridas][managedapis].
- **Acionadores** - Algumas APIs geridas podem também agir como um acionador. Um acionador inicia uma nova instância de um fluxo de trabalho baseado num evento específico, tal como a chegada de uma mensagem de correio eletrónico ou uma alteração na sua conta do Storage do Azure.
-  **Ações** - Cada passo após o acionar num fluxo de trabalho é designado uma ação. Cada ação normalmente mapeia para uma operação nas suas API Apps geridas ou personalizadas.
- **BizTalk** – Para cenários de integração mais avançados, as Logic Apps incluem capacidades do Biztalk. O BizTalk é a plataforma de integração líder de mercado da Microsoft. As API Apps BizTalk permitem a fácil inclusão de validação, transformação, regras, entre outras, nos seus fluxos de trabalho da Aplicação Lógica. Saiba mais em [quais são as API Apps do BizTalk][biztalk].

## Introdução  

 - Para começar com as Logic Apps, siga o tutorial [criar uma Aplicação Lógica][criar].  
 - [Ver exemplos e cenários comuns](app-service-logic-examples-and-scenarios.md)
 - [Pode automatizar os processos de negócios com Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694) 
 - [Saiba como integrar os seus sistemas com Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)
- Para obter mais informações sobre a plataforma de App Service do Azure, consulte [App Service do Azure][appservice].

[biztalk]: app-service-logic-what-are-biztalk-api-apps.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[criar]: app-service-logic-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: app-service-logic-create-a-trading-partner-agreement.md
[regras]: app-service-logic-use-biztalk-rules.md
[modelos]: app-service-logic-use-logic-app-templates.md



<!--HONumber=Jun16_HO2-->


