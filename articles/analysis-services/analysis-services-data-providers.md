---
title: "Bibliotecas de cliente necessárias para ligar ao Analysis Services do Azure | Microsoft Docs"
description: "Descreve as bibliotecas de cliente necessárias para aplicações de cliente e ferramentas ligar do Azure Analysis Services"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 9c9ceea9cc7fa9b6e4a63660bfa988bf50c0f5ae
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Bibliotecas de cliente para ligar ao Azure Analysis Services

Bibliotecas de cliente são necessárias para aplicações de cliente e ferramentas ligar aos servidores de Analysis Services. 

Do Analysis Services utilizar três bibliotecas de cliente. ADOMD.NET Analysis Services Management Objects (AMO), sendo bibliotecas de cliente gerido. O Analysis Services fornecedor OLE DB (MSOLAP DLL) é uma biblioteca de cliente nativo. Normalmente, são instaladas todas as três ao mesmo tempo. Serviços de análise do Azure requer que as versões mais recentes. 

Aplicações de cliente da Microsoft como o Excel e o Power BI Desktop instalar todas as bibliotecas de cliente três. No entanto, consoante a versão ou a frequência de atualizações, bibliotecas de cliente podem não estar as versões mais recentes necessárias pelo Azure Analysis Services. O mesmo se aplica a aplicações personalizadas ou a outras interfaces, como sCmd, TOM ou ADOMD.NET. Estas aplicações necessitam de instalar manualmente as bibliotecas. As bibliotecas de cliente para a instalação manual estão incluídas nos pacotes de funcionalidades do SQL Server como distributable pacotes. No entanto, estas bibliotecas de cliente estão associadas a versão do SQL Server e não podem ser a versão mais recente.  

Bibliotecas de cliente para ligações de cliente são diferentes de fornecedores de dados necessárias para ligar a partir de um servidor de Analysis Services do Azure a uma origem de dados. Para saber mais sobre ligações de origem de dados, consulte [Datasource ligações](analysis-services-datasource.md).

## <a name="download-the-latest-client-libraries"></a>Transferir as bibliotecas de cliente mais recentes  
Utilize as seguintes bibliotecas de cliente se estiver num ambiente de produção e exigir versões totalmente libertadas e suportadas.

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[NO AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Passos seguintes
[Estabelecer ligação com o Excel](analysis-services-connect-excel.md)    
[Ligar com o Power BI](analysis-services-connect-pbi.md)
