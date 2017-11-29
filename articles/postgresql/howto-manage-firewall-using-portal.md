---
title: Criar e gerir regras de firewall na base de dados do Azure para PostgreSQL | Microsoft Docs
description: Criar e gerir a base de dados do Azure para as regras de firewall de PostgreSQL no portal do Azure
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: 248bd491bf688ff9b3ef4252c295989dc340b79c
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-the-azure-portal"></a>Criar e gerir a base de dados do Azure para as regras de firewall de PostgreSQL no portal do Azure
Regras de firewall ao nível do servidor permitem que os administradores aceder a uma base de dados do Azure para o servidor de PostgreSQL de um endereço IP especificado ou intervalo de endereços IP. 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, tem de:
- Um servidor [criar uma base de dados do Azure para PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall ao nível do servidor no portal do Azure
1. Na página de servidor PostgreSQL, em definições, clique em **segurança de ligação** para abrir a página de segurança de ligação da base de dados do Azure para PostgreSQL.

  ![Portal do Azure – clique em segurança de ligação](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Selecione **Adicionar IP do meu** na barra de ferramentas. Esta ação cria automaticamente uma regra de firewall com o endereço IP público do seu computador, como percetível pelo sistema do Azure.

  ![Portal do Azure – clique em Adicionar meu IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Verifique o seu endereço IP antes de guardar a configuração. Em algumas situações, o endereço IP observado pelo portal do Azure é diferente do endereço IP utilizado ao aceder à internet e os servidores do Azure. Por conseguinte, terá de alterar o IP inicial e o IP final para se a função de regra, conforme esperado.
Utilize um motor de busca ou outra ferramenta online para verificar o seu próprio endereço IP (por exemplo, pesquisa de Bing "qual é o meu IP").

  ![Pesquisa do Bing para saber o que é o meu IP](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Adicione intervalos de endereços adicionais. Nas regras da firewall para a base de dados do Azure para PostgreSQL, pode especificar um único endereço IP ou um intervalo de endereços. Se pretender limitar a regra para um único endereço IP, escreva o mesmo endereço no campo para o IP inicial e final IP. Abrir a firewall permite que os administradores, utilizadores e aplicações para iniciar sessão para uma base de dados no servidor PostgreSQL aos quais têm credenciais válidas.

  ![Portal do Azure – regras de firewall ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Clique em **guardar** na barra de ferramentas para guardar esta regra de firewall ao nível do servidor. Aguarde a confirmação de que a atualização para as regras de firewall foi concluída com êxito.

  ![Portal do Azure – clique em Guardar](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)


## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gerir regras de firewall ao nível do servidor existentes através do Portal do Azure
Repita os passos para gerir as regras de firewall.
* Para adicionar o computador atual, clique no botão para + **Adicionar IP do meu**. Clique em **Guardar** para guardar as alterações.
* Para adicionar endereços IP adicionais, escreva o Nome da Regra, o Endereço IP Inicial e o Endereço IP Final. Clique em **Guardar** para guardar as alterações.
* Para modificar uma regra existente, clique em qualquer um dos campos na regra e modifique. Clique em **Guardar** para guardar as alterações.
* Para eliminar uma regra existente, clique no botão de reticências [...] e clique em **eliminar** para remover a regra. Clique em **Guardar** para guardar as alterações.

## <a name="next-steps"></a>Passos seguintes
- Da mesma forma, pode aplicar o script para [criar e gerir a base de dados do Azure PostgreSQL das regras de firewall ao utilizar a CLI do Azure](howto-manage-firewall-using-cli.md).
- Para obter ajuda na ligação para uma base de dados do Azure para o servidor de PostgreSQL, consulte [bibliotecas de ligação para base de dados do Azure para PostgreSQL](concepts-connection-libraries.md).
