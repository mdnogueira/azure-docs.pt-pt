---
title: Instalar Visual Studio e SSDT para SQL Data Warehouse | Microsoft Docs
description: Instalar o Visual Studio e SQL Server Development Tools (SSDT) para o Azure SQL Data Warehouse
services: sql-data-warehouse
documentationcenter: NA
author: antvgski
manager: jhubbard
editor: 
ms.assetid: 0ed9b406-9b42-4fe6-b963-fe0a5b48aac1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: connect
ms.date: 03/30/2017
ms.author: anvang;barbkess
ms.openlocfilehash: f7023b78c241a7bc8014276cd0bfa455165b42cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Instalar Visual Studio e SSDT para SQL Data Warehouse
Para desenvolver aplicações SQL do armazém de dados, recomendamos que utilize a versão mais recente do Visual Studio com a versão mais recente do SQL Server Data Tools (SSDT).  A Atualização 5 do Visual Studio 2013 com SSDT também é suportada para compatibilidade com versões anteriores.  

Utilizar o Visual Studio com SSDT irá permitir a utilização do SQL Server Object Explorer para explorar visualmente tabelas, vistas, procedimentos armazenados e muitos mais objetos no SQL Data Warehouse, bem como executar consultas.

> [!NOTE]
> O SQL Data Warehouse ainda não suporta Projetos de Base de Dados do Visual Studio.  Esta funcionalidade será adicionada numa versão futura.
> 
> 

## <a name="step-1-install-visual-studio"></a>Passo 1: Instalar o Visual Studio
Siga estas ligações para transferir e instalar o Visual Studio. Se já tiver o Visual Studio 2013 ou posterior instalado, pode avançar para o passo 2, instale o SSDT.

1. [Transferir o Visual Studio][].
2. Siga o [instalar o Visual Studio] [ Installing Visual Studio] orientar na MSDN e escolha as configurações predefinidas.

## <a name="step-2-install-ssdt"></a>Passo 2: Instalar o SSDT
Para instalar o SSDT para Visual Studio, basta procurar uma atualização do SSDT a partir do Visual Studio, seguindo estes passos.

1. No Visual Studio, clique em **ferramentas** / **extensões e atualizações...** / **Atualizações**
2. Selecione **Atualizações de Produtos** e, em seguida, procure **Atualização do Microsoft SQL Server para Ferramentas de Base de Dados**

Se não for encontrada uma atualização, deve ter a versão mais recente instalada.  Para confirmar que o SSDT está instalado, clique em **Ajuda** / **Sobre o Microsoft Visual Studio** e procure SQL Server Data Tools na lista.  A versão mais recente do SSDT é 14.0.60525.0.  Se a opção de instalação não está disponível a partir do Visual Studio, em alternativa, pode visitar o [transferência do SSDT] [ SSDT Download] página para transferir e instalar manualmente o SSDT.

## <a name="next-steps"></a>Passos seguintes
Agora que tem a versão mais recente do SSDT, está pronto para [ligar] [ connect] para o SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Transferir o Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
