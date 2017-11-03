---
title: "Novidades nas coleções de área de trabalho do Power BI"
description: "Obter as informações mais recentes sobre o que é novo no coleções de área de trabalho do Power BI"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 2794ae98-b9a7-45df-b6e1-962a395b91fa
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: a2faf610ca50acdb54353ade7c7a4ecabd314347
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="whats-new-in-power-bi-workspace-collections"></a>Novidades nas coleções de área de trabalho do Power BI

Atualizações **coleções de área de trabalho do Power BI** são lançadas regularmente. No entanto, não cada versão inclui novas funcionalidades de destinada ao utilizador; Algumas versões são concentra-se nas capacidades do serviço de back-end. Iremos realce novas capacidades de destinada ao utilizador aqui.

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="march-2017"></a>Março de 2017

**Capacidades de self-service**

* [Criar novo relatório](create-report-from-dataset.md)
* [Relatório SaveAs](save-reports.md)
* Incorporar relatório no novo modo de leitura/editar/criar 
* [Relatório de alternar entre modos editar/leitura](toggle-mode.md)

**Conectividade de dados com REST APIs**

* [Criar conjunto de dados](https://msdn.microsoft.com/library/azure/mt778875.aspx)
* Enviar dados 

**APIs de gestão**

* Relatório de clonagem e o conjunto de dados
* Vincular o relatório para um conjunto de dados diferente

**Amostras**

* Atualizado [exemplo incorporar o relatório de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo)

## <a name="december-2016"></a>Dezembro de 2016

* [Exemplo de incorporação de JavaScript novo](https://microsoft.github.io/PowerBI-JavaScript/demo/)

## <a name="october-2016"></a>Outubro de 2016

* [Análise avançada com o Power BI área de trabalho coleções e R](https://powerbi.microsoft.com/blog/r-in-pbie/)

## <a name="august-31-2016"></a>31 de Agosto de 2016
Esta versão inclui:

* Todos os novos JavaScript SDK que suporte [avançadas filtragem e página navegação](interact-with-reports.md).
* Coleções de área de trabalho do Power BI são agora suportadas no Centro de dados Canadá Central. Verifique [datacenter estado](https://azure.microsoft.com/status/).

## <a name="july-11-2016"></a>11 de julho de 2016
Esta versão inclui:

* **Excelente notícias!** O serviço de coleções de área de trabalho do Power BI já não está em pré-visualização - GA respetivo agora (geralmente disponível).  
* Todas as APIs REST ter movidas **/beta** para **/v1.0**.
* .NET e JavaScript SDKs tiver sido atualizado por **v 1.0**.
* Power BI API chamadas agora podem ser autenticadas diretamente através da utilização de chaves de API. Tokens de aplicação só são necessárias para incorporar. Como parte deste processo, tokens de aprovisionar e dev foram preteridos na v 1.0 APIs, mas irá continuem a funcionar na versão beta até 30 de Dezembro de 2016. Para obter mais informações, consulte [autenticação e autorização com Power BI área de trabalho coleções](app-token-flow.md).
* Suporte de segurança ao nível (RLS) de linha para tokens de aplicação e os relatórios incorporados. Para obter mais informações, consulte [linha segurança ao nível com coleções de área de trabalho do Power BI](row-level-security.md).
* Atualizar a aplicação de exemplo para todos os **v 1.0** chamadas da API.
* Coleções de área de trabalho do Power BI suporta do SDK do Azure, PowerShell e CLI.
* Os utilizadores podem exportar dados de visualização para uma **. csv**.
* Coleções de área de trabalho do Power BI agora são suportadas em todas as mesmas idiomas/regiões como o Microsoft Azure. Para obter mais informações, consulte [Azure - idiomas](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx).

