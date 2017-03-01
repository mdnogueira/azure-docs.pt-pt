---
title: "Introdução ao Microsoft Power BI Embedded"
description: "Power BI Embedded, adicione relatórios interativos do Power BI à aplicação de business intelligence"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 02/06/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: bd7925f3fa9a717cbe0649bf899cdd00511d5ca6
ms.openlocfilehash: b9dff45d1bb60d50c882c6daf363fca86a7f8f4c
ms.lasthandoff: 02/22/2017


---
# <a name="get-started-with-microsoft-power-bi-embedded"></a>Introdução ao Microsoft Power BI Embedded
O **Power BI Embedded** é um serviço do Azure que permite aos programadores de aplicações adicionar relatórios de Power BI interativos às suas próprias aplicações. O **Power BI Embedded** funciona com as aplicações existentes sem precisar de recriar ou alterar a forma como os utilizadores iniciam sessão.

Os recursos para o **Microsoft Power BI Embedded** são aprovisionados através das [APIs ARM do Azure](https://msdn.microsoft.com/library/mt712306.aspx). Neste caso, o recurso que aprovisiona é uma **Coleção de Áreas de Trabalho do Power BI**.

![](media/power-bi-embedded-get-started/introduction.png)

## <a name="create-a-workspace-collection"></a>Criar uma coleção de área de trabalho
Uma **Coleção de Área de Trabalho** é o recurso mais superior do Azure e um contentor para o conteúdo que será incorporado na aplicação. Uma **Coleção de Área de Trabalho** pode ser criada de duas formas:

* Manualmente pela utilização do Portal do Azure
* Através de programação utilizando as APIs do Azure Resource Manager (ARM)

Vamos guiá-lo pelos passos para criar uma **Coleção de Área de Trabalho** através do Portal do Azure.

1. Abra e inicie sessão no **Portal do Azure **: [http://portal.azure.com](http://portal.azure.com).
2. Clique em **+ Novo** no painel superior.
   
   ![](media/power-bi-embedded-get-started/create-workspace-1.png)
3. Em **Dados + Análise** clique **Power BI Embedded**.
4. No **Painel de Coleção de Área de Trabalho**, introduza as informações necessárias. Para **Preços**, consulte o artigo [Preços do Power BI Embedded](http://go.microsoft.com/fwlink/?LinkID=760527).
   
   ![](media/power-bi-embedded-get-started/create-workspace-2.png)
5. Clique em **Criar**.

A **Coleção de Área de Trabalho** levará alguns minutos para aprovisionar. Quando concluída, será direcionado para o **Painel de Coleção de Área de Trabalho**.

   ![](media/power-bi-embedded-get-started/create-workspace-3.png)

O **Painel de Criação** contém as informações de que precisa para chamar as APIs que criam áreas de trabalho e implementam conteúdo nas mesmas.

<a name="view-access-keys"/>

## <a name="view-power-bi-api-access-keys"></a>Visualizar Chaves de Acesso da API do Power BI
Uma das informações mais importantes necessárias para chamar as APIs REST do Power BI é são as **Teclas de Acesso**. Estas são utilizadas para gerar os **tokens de aplicação** utilizados para autenticar os pedidos de API. Para ver as suas **Teclas de Acesso**, clique em **Teclas de Acesso** no **Painel de Definições**. Para obter mais informações sobre **tokens de aplicação**, veja [Authenticating and authorizing with Power BI Embedded (Autenticação e autorização com Power BI Embedded)](power-bi-embedded-app-token-flow.md).

   ![](media/power-bi-embedded-get-started/access-keys.png)

Vai notar que tem duas chaves.

   ![](media/power-bi-embedded-get-started/access-keys-2.png)

Copie estas chaves e armazene-as de forma segura na aplicação. É muito importante que trate estas chaves como se fossem uma palavra-passe, porque dão acesso a todo o conteúdo na **Coleção de Área de Trabalho**.

Embora sejam apresentadas duas chaves, apenas é necessária uma de cada vez. A segunda chave é fornecida para periodicamente regenerar chaves sem interromper o acesso ao serviço.

Agora que tem uma instância do Power BI para a aplicação e as **Chaves de Acesso**, pode importar um relatório para a própria aplicação. Antes de saber como importar um relatório, a secção a seguir descreve como criar conjuntos de dados do Power BI e relatórios para incorporar numa aplicação.

## <a name="working-with-workspaces"></a>Trabalhar com áreas de trabalho

Depois de ter criado a sua coleção da área de trabalho, terá de criar uma área de trabalho que irá guardar os relatórios e conjuntos de dados. Para criar uma área de trabalho, terá de utilizar a [API REST de Espaço de Trabalho de Publicação](https://msdn.microsoft.com/library/azure/mt711503.aspx).

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app"></a>Criar conjuntos de dados e relatórios do Power BI para incorporar numa aplicação
Agora que já criou uma instância do Power BI para a aplicação e tem **chaves de acesso**, terá de criar os conjuntos de dados do Power BI e os relatórios que pretende incorporar. Conjuntos de dados e relatórios podem ser criados utilizando o **Power BI Desktop**. Pode transferir o [Power BI Desktop gratuitamente](https://go.microsoft.com/fwlink/?LinkId=521662). Em alternativa, para começar rapidamente, pode transferir o [PBIX de exemplo de análise de revenda](http://go.microsoft.com/fwlink/?LinkID=780547).

> [!NOTE]
> Para saber mais sobre como utilizar **Power BI Desktop**, consulte o artigo [Introdução ao Power BI Desktop](https://powerbi.microsoft.com/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

Com o **Power BI Desktop** liga-se à fonte dos dados através da importação de uma cópia dos dados no **Power BI Desktop** ou ligando diretamente para a fonte de dados utilizando **DirectQuery**.

Estas são as diferenças entre a utilização de **Importar** e **DirectQuery**.

| Importar | DirectQuery |
| --- | --- |
| Tabelas, colunas *e dados* são importados ou copiados para o **Power BI Desktop**. À medida que trabalha com visualizações, o **Power BI Desktop** consulta uma cópia dos dados. Para ver as alterações ocorridas nos dados subjacentes, tem de atualizar ou importar novamente um conjunto de dados completo e atual. |Apenas *tabelas e colunas* são importadas ou copiadas para o **Power BI Desktop**. À medida que trabalha com visualizações, o **Power BI Desktop** consulta a fonte de dados subjacente, o que significa que está sempre a ver dados atuais. |

Para obter mais informações sobre como ligar a uma fonte de dados, consulte o artigo [Ligar a uma fonte de dados](power-bi-embedded-connect-datasource.md).

Depois de guardar o trabalho no **Power BI Desktop**, é criado um ficheiro PBIX. Este ficheiro contém o relatório. Além disso, se importar dados, o PBIX conterá o conjunto de dados completo, mas se utilizar o **DirectQuery**, o PBIX conterá apenas um esquema de conjunto de dados. Implemente o PBIX através de programação na área de trabalho utilizando a [API de Importação do Power BI](https://msdn.microsoft.com/library/mt711504.aspx).

> [!NOTE]
> O **Power BI Embedded ** inclui APIs adicionais para alterar o servidor e a base de dados para a qual o conjunto de dados está a apontar, e para definir uma credencial de conta de serviço que o conjunto de dados utilizará para ligar à base de dados. Consulte o artigo [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) e [Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx) (Correção de fonte de dados do Gateway).

## <a name="next-steps"></a>Passos Seguintes
Nos passos anteriores, criou uma coleção de áreas de trabalho e o primeiro relatório e conjunto de dados. Agora está na altura de aprender a escrever código para **Power BI Embedded**. Para o ajudar a começar, criámos uma aplicação Web de exemplo: [Introdução com exemplo](power-bi-embedded-get-started-sample.md). O exemplo mostra-lhe como:

* Aprovisionar conteúdo
  * Criar uma área de trabalho
  * Importar um ficheiro PBIX
  * Atualizar as cadeias de ligação e definir credenciais para os conjuntos de dados.
* Incorporar um relatório de forma segura

## <a name="see-also"></a>Veja também
* [Introdução com exemplo](power-bi-embedded-get-started-sample.md)
* [Autenticação e autorização com o Power BI Embedded](power-bi-embedded-app-token-flow.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

Mais perguntas? [Tente a Comunidade do Power BI](http://community.powerbi.com/)


