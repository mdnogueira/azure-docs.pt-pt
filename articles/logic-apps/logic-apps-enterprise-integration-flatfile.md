---
title: Codificar ou descodificar ficheiros simples no Azure logic apps | Microsoft Docs
description: Como utilizar o codificador de ficheiro do ficheiro ou o descodificador no Enterprise Integration Pack nas suas logic apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 82152dab-c7ad-43df-b721-596559703be8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; mandia
ms.openlocfilehash: bc3430624844cdeb92958433fba295f67a8ae0ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-enterprise-integration-with-flat-files"></a>Descrição geral da integração empresarial com ficheiros simples

Pretende codificar o conteúdo XML antes de enviá-lo para um parceiro de negócios num cenário empresa-empresa (B2B). Numa aplicação lógica, pode utilizar o conector de codificação com ficheiros simples para efetuar este procedimento. A aplicação lógica que criar pode obter a respetiva XML conteúdos de uma variedade de origens, incluindo a partir de um acionador de pedido HTTP, de outra aplicação ou mesmo a partir de um dos muitos [conectores](../connectors/apis-list.md). Para mais informações sobre as logic apps, consulte o [documentação de aplicações lógicas](logic-apps-what-are-logic-apps.md "saber mais sobre as Logic apps").  

## <a name="create-the-flat-file-encoding-connector"></a>Criar o ficheiro simples codificação conector
Siga estes passos para adicionar um ficheiro simples codificação conector à sua aplicação lógica.

1. Criar uma aplicação lógica e [ligá-lo à sua conta de integração](logic-apps-enterprise-integration-accounts.md "saiba associar uma conta de integração para uma aplicação lógica"). Esta conta contém o esquema que irá utilizar para codificar os dados XML.  
2. Adicionar um **pedido - pedido de HTTP um quando é recebido** acionador à sua aplicação lógica.  
   ![Captura de ecrã do acionador para selecionar](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
3. Adicione o ficheiro simples codificação ação, da seguinte forma:
   
    a. Selecione o **plus** início de sessão.
   
    b. Selecione o **adicionar uma ação** ligação (aparece depois de selecionar o sinal de adição).
   
    c. Na caixa de pesquisa, introduza *simples* para filtrar todas as ações de que pretende utilizar.
   
    d. Selecione o **codificação de ficheiro simples** opção na lista.   
   ![Opção de captura de ecrã do simples codificação de ficheiro](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
4. No **codificação de ficheiro simples** caixa de diálogo, selecione o **conteúdo** caixa de texto.  
   ![Caixa de texto de captura de ecrã do conteúdo](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  
5. Selecione a etiqueta body como o conteúdo que pretende codificar. A etiqueta body irá preencher o campo de conteúdo.     
   ![Captura de ecrã da etiqueta body](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  
6. Selecione o **nome do esquema** caixa de lista e escolha o esquema que pretende utilizar para codificar o conteúdo de entrada.    
   ![Caixa de listagem de captura de ecrã do nome de esquema](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  
7. Guarde o seu trabalho.   
   ![Ícone de captura de ecrã de guardar](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

Neste momento, tiver terminado de configurar o conector de codificação de ficheiro simples. Uma aplicação do mundo real, poderá armazenar os dados codificados numa aplicação de linha de negócio, tais como o Salesforce. Ou pode enviar que dados codificados para um comerciais parceiro. Pode facilmente adicionar uma ação para enviar o resultado da ação codificação Salesforce ou para o seu parceiro comercial, utilizando qualquer um dos outros conectores fornecido.

Agora pode testar o seu conector ao efetuar um pedido para o ponto final de HTTP e incluindo o conteúdo XML no corpo do pedido.  

## <a name="create-the-flat-file-decoding-connector"></a>Criar o ficheiro simples descodificar o conector

> [!NOTE]
> Para concluir estes passos, tem de ter um ficheiro de esquema já carregado na conta integração.

1. Adicionar um **pedido - pedido de HTTP um quando é recebido** acionador à sua aplicação lógica.  
   ![Captura de ecrã do acionador para selecionar](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
2. Adicione o ficheiro simples descodificar ação, da seguinte forma:
   
    a. Selecione o **plus** início de sessão.
   
    b. Selecione o **adicionar uma ação** ligação (aparece depois de selecionar o sinal de adição).
   
    c. Na caixa de pesquisa, introduza *simples* para filtrar todas as ações de que pretende utilizar.
   
    d. Selecione o **descodificar de ficheiro simples** opção na lista.   
   ![Captura de ecrã do simples ficheiro descodificar opção](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
3. Selecione o **conteúdo** controlo. Isto produz uma lista do conteúdo dos passos anteriores, que pode utilizar como o conteúdo ao descodificar. Tenha em atenção que o *corpo* de HTTP a receber está disponível para ser utilizado como o conteúdo ao descodificar o pedido. Também pode introduzir o conteúdo descodificar diretamente para o **conteúdo** controlo.     
4. Selecione o *corpo* etiquetas. Tenha em atenção a etiqueta body está agora no **conteúdo** controlo.
5. Selecione o nome do esquema que pretende utilizar para descodificar o conteúdo. A seguinte captura de ecrã mostra que *OrderFile* é o nome de esquema selecionado. Este nome de esquema tinha sido carregado para a conta de integração anteriormente.
   
   ![Captura de ecrã do simples ficheiro descodificar a caixa de diálogo](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png)    
6. Guarde o seu trabalho.  
   ![Ícone de captura de ecrã de guardar](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

Neste momento, tiver terminado de configurar o seu ficheiro simples descodificar o conector. Uma aplicação do mundo real, poderá armazenar os dados descodificados numa aplicação de linha de negócio, tais como o Salesforce. Pode facilmente adicionar uma ação para enviar o resultado da ação decoding Salesforce.

Agora pode testar o seu conector ao efetuar um pedido para o ponto final de HTTP e incluindo o conteúdo XML que pretende descodificar no corpo do pedido.  

## <a name="next-steps"></a>Passos seguintes
* [Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack").  

