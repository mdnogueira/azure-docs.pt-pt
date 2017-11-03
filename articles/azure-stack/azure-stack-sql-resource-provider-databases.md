---
title: Utilizar bases de dados fornecidas pelo RP de adaptador de SQL na pilha do Azure | Microsoft Docs
description: Como criar e gerir bases de dados SQL aprovisionados utilizando o fornecedor de recursos do adaptador de SQL
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: JeffGo
ms.openlocfilehash: 0cc08c37e879b00f8cd9a4046a4c81c55dab167c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-sql-databases"></a>Criar bases de dados SQL

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Bases de dados self-service são fornecidos através da experiência do portal de utilizador. O utilizador precisa de uma subscrição que tenha uma oferta, que contém o serviço de base de dados.

1. Iniciar sessão para o [Azure pilha](azure-stack-poc.md) portal de utilizador (administradores de serviço também podem utilizar o portal de administração).

2. Clique em **+ novo** &gt; **dados + armazenamento "** &gt; **base de dados do SQL Server (pré-visualização)** &gt; **adicionar**.

3. Preencha o formulário com detalhes de base de dados, incluindo um **nome de base de dados**, **tamanho máximo**e alterar os outros parâmetros conforme necessário. É-lhe pedido para escolher um SKU para a base de dados. Servidores de alojamento são adicionadas, foram atribuídos um SKU. Bases de dados são criadas nesse agrupamento de servidores que compõem o SKU de alojamento.

  ![Nova base de dados](./media/azure-stack-sql-rp-deploy/newsqldb.png)

  >[!NOTE]
  > O tamanho de base de dados tem de ser, pelo menos, a 64 MB. Podem ser aumentado com as definições.

4. Preencha as definições de início de sessão: **início de sessão da base de dados**, e **palavra-passe**. Estas definições estão a credencial de autenticação do SQL Server que é criada para o acesso ao apenas esta base de dados. O nome de utilizador de início de sessão tem de ser globalmente exclusivo. Crie uma nova definição de início de sessão ou selecione um existente. Pode reutilizar definições de início de sessão para outras bases de dados utilizando o SKU do mesmo.

    ![Criar um novo início de sessão de base de dados](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. Submeter o formulário e aguarde a conclusão da implementação.

    No painel resultante, tenha em atenção o campo de "Cadeia de ligação". Pode utilizar essa cadeia em qualquer aplicação que necessita de acesso do SQL Server (por exemplo, uma aplicação web) na sua pilha do Azure.

    ![Obter a cadeia de ligação](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="delete-sql-databases"></a>Eliminar as bases de dados SQL
No portal,

>[!NOTE]
>
>Quando uma base de dados do AlwaysOn de SQL é eliminado no RP, este é eliminada com êxito da primário e o grupo de disponibilidade do AlwaysOn, mas por conceção SQL AG coloca a base de dados em estado de cada réplica de restauro e não remover a base de dados, a menos que acionado. Se uma base de dados não for removido, as réplicas secundárias vai para sincronizar não Estado. Adicionar uma nova base de dados novamente para o AG com o mesmo através de RP ainda funciona. Consulte ![remoção de uma base de dados secundária](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/remove-a-secondary-database-from-an-availability-group-sql-server)

## <a name="manage-database-credentials"></a>Gerir as credenciais da base de dados
Pode atualizar as credenciais de base de dados (definições de início de sessão).

## <a name="verify-sql-alwayson-databases"></a>Certifique-se de bases de dados do AlwaysOn de SQL
Bases de dados do AlwaysOn devem aparecer como sincronizadas e estão disponíveis em todas as instâncias e no grupo de disponibilidade. Após a ativação pós-falha, a base de dados deve ligar de forma totalmente integrada. Pode utilizar o SQL Server Management Studio para verificar que uma base de dados está a sincronizar:

![Verifique se AlwaysOn](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)
