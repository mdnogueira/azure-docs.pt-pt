<properties
    pageTitle="Criar uma Aplicação Lógica | Microsoft Azure"
    description="Saiba como criar uma Aplicação Lógica de ligação aos Serviços SaaS"
    authors="stepsic-microsoft-com"
    manager="dwrede"
    editor=""
    services="app-service\logic"
    documentationCenter=""/>

<tags
    ms.service="app-service-logic"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/16/2016"
    ms.author="stepsic"/>

# Criar uma nova aplicação lógica de ligação aos serviços SaaS

| Referência rápida |
| --------------- |
| [Linguagem de Definição das Logic Apps](https://msdn.microsoft.com/library/azure/mt643789.aspx) |
| [Documentação do Conector das Logic Apps](../connectors/apis-list.md) |
| [Fórum de Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) |

Este tópico demonstra como, em apenas alguns minutos, pode começar a utilizar as [Logic Apps do App Service](app-service-logic-what-are-logic-apps.md). Explicaremos através de um fluxo de trabalho que lhe permite fornecer um conjunto de Tweets em que está interessado para uma caixa de correio.

Para utilizar este cenário, precisa de:

- Uma subscrição do Azure.
- Uma conta do Twitter
- Uma conta Office 365

## Criar uma nova aplicação lógica para enviar os tweets por correio eletrónico

1. No dashboard do Portal do Azure, selecione **Marketplace**. 
2. Em Tudo, procure “logic apps” e, em seguida, selecione **Aplicação Lógica (pré-visualização)**. Também pode selecionar **Novo**, **Web + Móvel**, e selecione **Aplicação Lógica (pré-visualização)**. 
3. Introduza um nome para a sua aplicação lógica, selecione o plano do App Service e selecione **Criar**.  
    Neste passo, partimos do princípio de que tem um plano do App Service e de que está familiarizado com as propriedades necessárias. Se não, não se preocupe, pode começar em [Descrição geral dos planos do App Service do Azure](azure-web-sites-web-hosting-plans-in-depth-overview.md). 

4. Quando a aplicação lógica abre pela primeira vez, necessitará de um acionador. Procure por **twitter** na caixa de pesquisa do acionador e selecione-o.

7. Agora irá escreva a palavra-chave pela a qual pretende pesquisar o twitter. 
    ![Pesquisa do twitter](./media/app-service-logic-create-a-logic-app/twittersearch.png)

5. Selecione o sinal de adição e, em seguida, escolha **Adicionar uma ação** ou **Adicionar uma condição**:  
    ![Sinal de adição](./media/app-service-logic-create-a-logic-app/plus.png)
6. Quando seleciona **Adicionar uma ação**, todos os conectores com as respetivas ações disponíveis são listados. Em seguida, pode escolher qual o conector e a ação a adicionar à sua aplicação lógica. Por exemplo, pode selecionar **Office 365 – Enviar correio eletrónico**, e mais ações do Office 365:  
    ![Ações](./media/app-service-logic-create-a-logic-app/actions.png)

7. Agora tem de preencher os parâmetros para o correio eletrónico que pretende:  ![Parâmetros](./media/app-service-logic-create-a-logic-app/parameters.png)

8. Por fim, pode selecionar **Guardar** para colocar a sua aplicação lógica em direto.

## Gerir a sua aplicação lógica após a criação

A aplicação lógica está agora instalada e em execução. Sempre que o fluxo de trabalho agendado é executado, a mesma verifica a existência de tweets com o hashtag específico. Quando encontrar um tweet correspondente, coloca-o no seu Dropbox. Por fim, irá saber como desativar a aplicação ou ver o respetivo estado.

1. Clique em **Procurar** no lado esquerdo do ecrã e selecione **Logic Apps**.

2. Clique na nova aplicação lógica que acabou de criar para ver o estado atual e informações gerais.

3. Para editar a sua nova aplicação lógica, clique em **Acionadores e Ações**.

5. Para desativar a aplicação, clique em **Desativar** na barra de comandos.

Em menos de 5 minutos conseguiu configurar uma aplicação lógica simples em execução na nuvem. Para saber mais sobre como utilizar funcionalidades das Logic Apps, consulte o artigo [Utilizar funcionalidades da aplicação lógica]. Para saber mais sobre as próprias definições da Aplicação Lógica, consulte o artigo [Criar definições da Aplicação Lógica](app-service-logic-author-definitions.md).

<!-- Shared links -->
[Portal do Azure]: https://portal.azure.com
[Utilizar funcionalidades da aplicação lógica]: app-service-logic-create-a-logic-app.md



<!--HONumber=Jun16_HO2-->


