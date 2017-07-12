---
title: "Lição 13 do tutorial do Azure Analysis Services: implementar | Microsoft Docs"
description: Descreve como implementar o projeto de tutorial do Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 8e3e1be572aa66ab46f894a2e5f395d1e6f2ea23
ms.contentlocale: pt-pt
ms.lasthandoff: 06/03/2017

---
<a id="lesson-13-deploy" class="xliff"></a>

# Lição 13: implementar

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, irá configurar as propriedades de implementação ao especificar um servidor do Analysis Services no Azure ou um vNext Analysis Services do SQL Server no local e um nome para o modelo. Em seguida, irá implementar o modelo nessa instância. Depois de o modelo ser implementado, os utilizadores podem ligar-se ao mesmo através de uma aplicação de relatório para clientes. Para obter mais informações, consulte [Deploy to Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-deploy) (Implementar no Azure Analysis Services).  
  
Tempo estimado para concluir esta lição: **5 minutos**  
  
<a id="prerequisites" class="xliff"></a>

## Pré-requisitos  
Este tópico faz parte de um tutorial de modelação em tabela que deve ser concluído por ordem. Antes de executar as tarefas nesta lição, deverá ter concluído a lição anterior: [Lição 12: análise no Excel](../tutorials/aas-lesson-12-analyze-in-excel.md).  

**Importante:** se tiver instalado a base de dados de exemplo AdventureWorksDW2014 num SQL Server local e se estiver a implementar o seu modelo num servidor Azure Analysis Services, é necessário um [gateway de dados no local](../analysis-services-gateway.md).
  
<a id="deploy-the-model" class="xliff"></a>

## Implementar o modelo  
  
<a id="to-configure-deployment-properties" class="xliff"></a>

#### Para configurar propriedades de implementação  

  
1.  No **Explorador de Soluções**, clique com o botão direito do rato no projeto **AW Internet Sales** e clique em **Propriedades**.  
  
2.  Na caixa de diálogo **Páginas de propriedades de vendas na Internet AW**, em **Servidor de implementação**, nas propriedades do **Servidor**, digite o nome de um servidor do Analysis Services no Azure ou no local.  

    ![aas-lesson13-deploy-property](../tutorials/media/aas-lesson13-deploy-property.png)
 
    > [!IMPORTANT]  
    > Deverá ter permissões de administrador na instância remota do Analysis Services para a implementação.  
  
3.  Nas propriedades da **base de dados**, digite **Vendas na Internet Adventure Works**.  
  
4.  Nas propriedades do **nome do modelo**, digite **Modelo de vendas na Internet Adventure Works**.  
  
5.  Verifique suas seleções e, em seguida, clique em **OK**.  
  
<a id="to-deploy-the-adventure-works-internet-sales" class="xliff"></a>

#### Para implementar as Vendas na Internet Adventure Works
  
1.  No **Explorador de Soluções**, clique com o botão direito do rato no projeto **Vendas na Internet AW** > **Criar**.  

2.  Clique com botão direito do rato no projeto **Vendas na Internet AW** > **Implementar**.

    Ao implementar no Azure Analysis Services, pode ser-lhe solicitado que entre na sua conta. Entre na conta da sua organização e introduza a palavra-passe, por exemplo nancy@adventureworks.com. Esta conta deverá ser uma conta de administrador na instância do servidor.
  
    É apresentada a caixa de diálogo Implementar que exibe o estado da implementação dos metadados e cada tabela incluída no modelo.  
    
    ![aas-lesson13-deploy-status](../tutorials/media/aas-lesson13-deploy-status.png)
  
3. Quando a implementação for concluída com êxito, avance e clique em **Fechar**.  
  
<a id="conclusion" class="xliff"></a>

## Conclusão  
Parabéns! Acabou de criar e implementar o seu primeiro modelo do Analysis Services Tabular. Este tutorial ajudou na conclusão das tarefas mais comuns na criação de um modelo de tabela. Agora que o modelo das Vendas na Internet Adventure Works é implementado, pode usar o SQL Server Management Studio para gerir o modelo; crie scripts de processo e um plano de cópia de segurança. Agora, os utilizadores também podem ligar-se ao modelo através de uma aplicação de relatório para clientes, como o Microsoft Excel ou Power BI.  

![aas-lesson13-ssms](../tutorials/media/aas-lesson13-ssms.png)
  
  
  
<a id="whats-next" class="xliff"></a>

## Passos seguintes?
*  [Lição suplementar - Segurança dinâmica](../tutorials/aas-supplemental-lesson-dynamic-security.md)

*  [Lição suplementar - Linhas detalhadas](../tutorials/aas-supplemental-lesson-detail-rows.md)

*  [Lição suplementar - Hierarquias desalinhadas](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)

