---
title: Gerir os dados pessoais no Microsoft Azure | Microsoft Docs
description: "documentação de orientação sobre como corrigir, atualizar, eliminar e exportar dados pessoais no Azure Active Directory e a SQL Database do Azure"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: barclayn
ms.openlocfilehash: 97e8d23d90821489575b3ac6e8f1e810b7ce25a9
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="manage-personal-data-in-microsoft-azure"></a>Gerir os dados pessoais no Microsoft Azure

Este artigo fornece orientação sobre como corrigir, atualizar, eliminar e exportar dados pessoais no Azure Active Directory e a SQL Database do Azure.

## <a name="scenario"></a>Cenário

Uma empresa com base em Dublin fornece paragem compras para fim elevada destino weddings na Irlanda e em todo o mundo para ambos os local e internacional cliente base. Têm de escritórios, os clientes, os funcionários e fornecedores localizados em todo o mundo totalmente venues oferecem de serviço.

Entre muitos outros itens, a empresa mantém um registo dos RSVPs que incluem allergies prato e preferências dietary. Convidados festa de casamento podem registar-se para diversas atividades tais como horseback riding, surfing, boat rides, etc. e mesmo interagir entre si numa página web central durante meses esquerda cópias de segurança para o evento. A empresa recolhe informações de pessoais de funcionários, os fornecedores, os clientes e convidados festa de casamento. Devido a natureza internacional do negócio a empresa tem de cumprir vários níveis da regulamento.

## <a name="problem-statement"></a>Declaração do problema

- Administradores de dados tem de ser capaz de corretas incorretas pessoais informações e atualização incompletas ou alterar informações pessoais.

- Administradores de dados tem de poder eliminar informações pessoais, após o pedido de um assunto de dados.

- Administradores de dados tem de exportar os dados e fornecê-lo para um assunto de dados num formato comuns, estruturado após o pedido.

## <a name="company-goals"></a>Objetivos da empresa

- Cliente incorreta ou incompleto, convidado festa de casamento, funcionários e informações pessoais do fornecedor tem de ser corrigidos ou atualizadas no Azure Active Directory e a SQL Database do Azure.

- As informações pessoais tem de ser eliminadas no Azure Active Directory e a SQL Database do Azure após o pedido de um assunto de dados.

- Dados pessoais tem de ser exportados num formato comuns, estruturado após o pedido de um assunto de dados.

## <a name="solutions"></a>Soluções

### <a name="azure-active-directory-rectifycorrect-inaccurate-or-incomplete-personal-data-and-erasedelete-personal-datauser-profiles"></a>Azure Active Directory: resolver/corrija incorretas ou incompletas dados pessoais e apagar/eliminar perfis de utilizador/dados pessoais

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é baseado na nuvem, o multi-inquilino diretório e identidade do serviço de gestão da Microsoft.
Pode corrigir, atualizar ou eliminar os perfis de utilizador do cliente e dos empregados e informações de trabalho de utilizador que contenham dados pessoais, como o nome do utilizador, o título de trabalho, endereço ou número de telefone, no seu [do Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD) ambiente utilizando o [portal do Azure](https://portal.azure.com/).

Tem de iniciar sessão com uma conta que seja um administrador global do diretório.

#### <a name="how-do-i-correct-or-update-user-profile-and-work-information-in-azure-active-directory"></a>Como corrigir ou atualizar o perfil de utilizador e de trabalho informações no Azure Active Directory?

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2. Selecione **mais serviços**, introduza **utilizadores e grupos** na caixa de texto e, em seguida, selecione **Enter**.

    ![suporte de dados/image1.png](media/manage-personal-data-azure/image001.png)

3. No **utilizadores e grupos** painel, selecione **utilizadores**.

    ![suporte de dados/image2.png](media/manage-personal-data-azure/image003.png)

4. No **utilizadores e grupos - os utilizadores** painel, selecione um utilizador na lista e, em seguida, no painel de para o utilizador selecionado, selecione **perfil** para ver as informações de perfil de utilizador que tem de ser corrigido ou atualizadas.

    ![suporte de dados/image3.png](media/manage-personal-data-azure/image005.png)

5. Corrigir ou atualizar as informações e, em seguida, na barra de comandos, selecione **guardar.**

6.  No painel de para o utilizador selecionado, selecione **trabalhar informações** para ver informações de trabalho de utilizador que tem de ser corrigido ou atualizadas.

    ![suporte de dados/image4.png](media/manage-personal-data-azure/image007.png)

7. Corrigir ou atualizar as informações de trabalho de utilizador e, em seguida, na barra de comando, selecione **guardar.**

#### <a name="how-do-i-delete-a-user-profile-in-azure-active-directory"></a>Como posso eliminar um perfil de utilizador no Azure Active Directory?

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2. Selecione **mais serviços**, introduza **utilizadores e grupos** na caixa de texto e, em seguida, selecione **Enter**.

    ![](media/manage-personal-data-azure/image001.png)

3. No **utilizadores e grupos** painel, selecione **utilizadores**.

    ![suporte de dados/image2.png](media/manage-personal-data-azure/image003.png)

4. No painel **Utilizadores e grupos - Utilizadores**, selecione um utilizador na lista.

    ![suporte de dados/image3.png](media/manage-personal-data-azure/image007.png)

5. No painel de para o utilizador selecionado, selecione **descrição geral**e, em seguida, na barra de comandos, selecione **eliminar**.

    ![](media/manage-personal-data-azure/image013.png)

### <a name="sql-database-rectifycorrect-inaccurate-or-incomplete-personal-data-erasedelete-personal-data-export-personal-data"></a>Base de dados do SQL Server: resolver/corrija os dados pessoais incorretas ou incompletas; apagar/eliminar os dados pessoais; exportar os dados pessoais 

[Base de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/?v=16.50) é uma base de dados de nuvem que ajuda os programadores criar e manter as aplicações.

Dados pessoais podem ser atualizados no [SQL Database do Azure](https://azure.microsoft.com/services/sql-database/?v=16.50) utilizando padrão consultas SQL, também pode ser eliminado. Além disso, os dados pessoais podem ser exportados a partir da base de dados do SQL Server utilizando uma variedade de métodos, incluindo o servidor de SQL do Azure importar e exportar assistente e numa variedade de formatos, incluindo um ficheiro BACPAC.

#### <a name="how-do-i-correct-update-or-erase-personal-data-in-sql-database"></a>Como posso corrigir, atualizar ou apagar os dados pessoais na base de dados SQL?

Para saber como corrigir ou atualizar os dados pessoais na base de dados SQL, visite o [atualização (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql), [atualizar texto](https://docs.microsoft.com/sql/t-sql/queries/updatetext-transact-sql), [atualizar com a expressão de tabela comum](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql), ou [Atualização escrever texto](https://docs.microsoft.com/sql/t-sql/queries/writetext-transact-sql) documentação.

Para saber como eliminar os dados pessoais na base de dados SQL, visite o [eliminar (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) documentação.

#### <a name="how-do-i-export-personal-data-to-a-bacpac-file-in-sql-database"></a>Como posso exportar dados pessoais num ficheiro de BACPAC na base de dados SQL?

Um ficheiro BACPAC inclui os dados de base de dados SQL e os metadados e é um ficheiro zip com uma extensão BACPAC. Isto pode ser feito utilizando o [portal do Azure](https://portal.azure.com/), o utilitário da linha de comandos SQLPackage, o SQL Server Management Studio (SSMS) ou o PowerShell.

Para saber como exportar dados para um ficheiro BACPAC, visite o [exportar uma base de dados SQL do Azure para um ficheiro BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-export) página, que incluem instruções detalhadas para cada método listados acima.

Como exportar dados pessoais a partir de base de dados SQL com o Assistente de exportação e importação do servidor SQL?

Este assistente ajuda a copiar dados de uma origem para um destino. Para uma introdução para o assistente, incluindo como obtê-lo, informações de permissões e como obter ajuda com a ferramenta, visite o [importar e exportar dados com o Assistente de exportação e importação do servidor SQL](https://docs.microsoft.com/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard) página web.

Para obter uma descrição geral dos passos para que o assistente, visite o [os passos no Assistente de exportação e importação do servidor SQL](https://docs.microsoft.com/sql/integration-services/import-export-data/steps-in-the-sql-server-import-and-export-wizard) página web.

## <a name="next-steps"></a>Passos Seguintes:

[Base de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/?v=16.50) 

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

