---
title: Implementar Azure Analysis Services com o SSDT | Microsoft Docs
description: Saiba como implementar um modelo de tabela num servidor Azure Analysis Services com o SSDT.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 5f1f0ae7-11de-4923-a3da-888b13a3638c
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/01/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 0b15399cade0a9dc21b2274a64172d65f2f4e877
ms.contentlocale: pt-pt
ms.lasthandoff: 06/03/2017


---
<a id="deploy-a-model-from-ssdt" class="xliff"></a>

# Implementar um modelo a partir de SSDT
Depois de criar um servidor na sua subscrição do Azure, está pronto para implementar uma base de dados de modelo de tabela no mesmo. Pode usar as SQL Server Data Tools (SSDT) para criar e implementar um projeto de modelo de tabela em que está a trabalhar. 

<a id="before-you-begin" class="xliff"></a>

## Antes de começar
Para começar, precisa do seguinte:

* **Servidor Analysis Services** no Azure. Para obter mais informações, consulte [Criar um servidor Azure Analysis](analysis-services-create-server.md).
* **Projeto de modelo de tabela** no SSDT ou modelo tabular existente no nível de compatibilidade 1200 ou posterior. Nunca criou um? Experimente o [tutorial da Adventure Works](https://msdn.microsoft.com/library/hh231691.aspx)
* **Gateway local** - se uma ou mais origens de dados se encontrarem no local da rede da sua organização, é necessário instalar um [gateway de dados no local](analysis-services-gateway.md). O gateway é necessário para que o servidor na nuvem se ligue às suas origens de dados no local para processar e atualizar os dados no modelo.

> [!TIP]
> Antes de implementar, certifique-se de que pode processar os dados nas tabelas. No SSDT, clique em **Modelo** > **Processar** > **Processar tudo**. Se o processamento falhar, a implementação não é efetuada com êxito.
> 
> 

<a id="to-deploy-a-tabular-model-from-ssdt" class="xliff"></a>

## Para implantar um modelo de tabela do SSDT

1. Antes de implementar, é necessário obter o nome do servidor. No **portal do Azure** > servidor > **Descrição geral** > **Nome do servidor**, copie o nome do servidor.
   
    ![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. No SSDT > **Explorador de Soluções**, clique com o botão direito do rato no projeto > **Propriedades**. Em seguida, em **Implementação** > **Servidor** cole o nome do servidor.   
   
    ![Colar o nome do servidor na propriedade de implementação do servidor](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
3. Em **Explorador de Soluções**, clique com o botão direito do rato em **Propriedades** e, em seguida, clique em **Implementar**. Ser-lhe-á pedido que inicie sessão no Azure.
   
    ![Implementar no servidor](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    O estado de implementação é exibido na janela de saída e na implementação.
   
    ![Estado da implementação](./media/analysis-services-deploy/aas-deploy-status.png)

E é tudo o que é preciso!


<a id="but-something-went-wrong" class="xliff"></a>

## Mas, algo correu mal
Se a implementação falhar durante a implementação de metadados, é provável que se deva ao facto de não ser possível estabelecer ligação entre o SSDT e o seu servidor. Verifique se é possível estabelecer ligação ao seu servidor com o SSMS. Em seguida, verifique se a propriedade de implementação do servidor é a correta para o projeto.

Se a implementação falhar numa tabela, provavelmente não foi possível estabelecer ligação entre o servidor e a origem de dados. Se a sua origem de dados se encontra na rede da sua organização, certifique-se de que instala um [gateway de dados no local](analysis-services-gateway.md).

<a id="next-steps" class="xliff"></a>

## Passos seguintes
Agora que tem o modelo de tabela implementado no seu servidor, está pronto para se estabelecer ligação com o mesmo. Pode [ligá-lo com o SSMS](analysis-services-manage.md) para o gerir. E pode [ligá-lo através de uma ferramenta de cliente](analysis-services-connect.md) como o Power BI, o Power BI Desktop ou o Excel e começar a criar relatórios.


