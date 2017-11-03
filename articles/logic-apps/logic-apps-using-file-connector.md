---
title: Ligar a sistemas de ficheiros no local - Azure Logic Apps | Microsoft Docs
description: "Ligar a sistemas de ficheiros no local a partir de fluxos de trabalho de aplicação de lógica através do gateway de dados no local e conector sistema de ficheiros"
keywords: sistemas de ficheiros, no local
services: logic-apps
author: derek1ee
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: LADocs; deli
ms.openlocfilehash: 7738b3346af49cb8aa811eb17003d1b72b1bbe46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-on-premises-file-systems-from-logic-apps-with-the-file-system-connector"></a>Ligar a sistemas de ficheiros no local a partir das logic apps com o conector sistema de ficheiros

Para gerir os dados e aceder de forma segura a recursos no local, as logic apps podem utilizar o gateway de dados no local. Este artigo mostra como pode ligar a um sistema de ficheiros no local através de neste cenário de exemplo básico: copiar um ficheiro que é carregado para a Dropbox para uma partilha de ficheiros, em seguida, envie um e-mail.

## <a name="prerequisites"></a>Pré-requisitos

* Transferir a versão mais recente [gateway de dados no local](https://www.microsoft.com/download/details.aspx?id=53127).

* Instalar e configurar o gateway de dados no local mais recente, versão 1.15.6150.1 ou acima. Para obter os passos, consulte [ligar a origens de dados no local](http://aka.ms/logicapps-gateway). Tem de instalar o gateway numa máquina no local antes de poder continuar com estes passos.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/logic-apps-create-a-logic-app.md)

## <a name="add-trigger-and-actions-for-connecting-to-your-file-system"></a>Adicionar acionadores e ações para ligar ao seu sistema de ficheiros

1. Criar uma aplicação lógica em branco. Adicionar este acionador como o primeiro passo: **Dropbox - quando é criado um ficheiro** 

2. Sob o acionador, escolha **+ passo seguinte** > **adicionar uma ação**. 

3. Na caixa de pesquisa, introduza "sistema de ficheiros" como o filtro. Quando vir todas as ações para o conector sistema de ficheiros, escolha o **sistema de ficheiros - criar o ficheiro** ação. 

   ![Pesquisa para o conector do ficheiro](media/logic-apps-using-file-connector/search-file-connector.png)

4. Se ainda não tiver uma ligação para o sistema de ficheiros, é-lhe pedido que criar uma ligação. 

5. Selecione **ligar através do gateway de dados no local**. Quando as propriedades de ligação for apresentada, configure a ligação conforme especificado na tabela.

   ![Configurar a ligação](media/logic-apps-using-file-connector/create-file.png)

   | Definição | Descrição |
   | ------- | ----------- |
   | **Pasta raiz** | Especifique a pasta de raiz para o seu sistema de ficheiros. Pode especificar uma pasta local no computador onde está instalado o gateway de dados no local ou a pasta pode ser uma partilha de rede que a máquina pode aceder. <p>**Sugestão:** a pasta de raiz é a pasta principal principal, que é utilizada para caminhos relativos para todas as ações relacionadas com o ficheiro. | 
   | **Tipo de autenticação** | O tipo de autenticação que é utilizado pelo seu sistema de ficheiros | 
   | **Nome de Utilizador** | Forneça o seu nome de utilizador {*domínio*\\*username*} para o gateway instalado anteriormente. | 
   | **Palavra-passe** | Forneça a palavra-passe para o gateway instalado anteriormente. | 
   | **Gateway** | Selecione o gateway instalado anteriormente. | 
   ||| 

6. Depois de fornecer os detalhes da ligação, escolha **criar**. 

   As Logic Apps configura e testa a ligação, certificando-se de que a ligação funciona corretamente. 
   Se a ligação está configurada corretamente, são apresentadas as opções para a ação que selecionou anteriormente. 
   O conector sistema de ficheiros está agora pronto para ser utilizado.

7. Configurar o **criar ficheiro** ação para copiar ficheiros da Dropbox para a pasta raiz de partilha de ficheiros no local.

   ![Criar a ação de ficheiros](media/logic-apps-using-file-connector/create-file-filled.png)

8. Após esta ação de copiar o ficheiro, adicione uma ação do Outlook que envia uma mensagem de e-mail para que saibam utilizadores relevantes sobre o novo ficheiro. Introduza o destinatários, a título e o corpo da mensagem de e-mail. 

   No **conteúdo dinâmico** lista, pode escolher saídas de dados entre o conector de ficheiro para que pode adicionar mais detalhes para o e-mail.

   ![Enviar correio eletrónico ação](media/logic-apps-using-file-connector/send-email.png)

9. Guarde a sua aplicação lógica. Teste a aplicação através do carregamento de um ficheiro para a Dropbox. O ficheiro deve obter copiado para a partilha de ficheiros no local e deverá receber uma mensagem de e-mail sobre a operação.

Parabéns, tem agora uma aplicação de lógica de trabalho que se pode ligar para o sistema de ficheiros no local. 

Tente explorar outras funcionalidades que o conector oferece, por exemplo:

- Criar o ficheiro
- Lista de ficheiros na pasta
- Anexar ficheiro
- Eliminar ficheiro
- Obter o conteúdo do ficheiro
- Obter o conteúdo do ficheiro com o caminho
- Obter metadados do ficheiro
- Obter os metadados de ficheiro com o caminho
- Lista de ficheiros na pasta raiz
- Ficheiro de atualização

## <a name="view-the-swagger"></a>Ver o swagger

Consulte o [swagger detalhes](/connectors/fileconnector/). 

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para ajudar a melhorar o Azure Logic Apps e conectores, votar em ou submeter ideias no [site voz do utilizador do Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* [Ligar aos dados no local](../logic-apps/logic-apps-gateway-connection.md) 
* [Monitorizar as aplicações lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Integração do Enterprise para cenários B2B](../logic-apps/logic-apps-enterprise-integration-overview.md)
