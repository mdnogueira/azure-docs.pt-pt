---
title: Criar e gerir regras de firewall de MySQL na base de dados do Azure para MySQL | Microsoft Docs
description: Criar e gerir a base de dados do Azure MySQL das regras de firewall com o portal do Azure
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: 63ea6337b35193420924096690ed15cc1d5ede25
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Criar e gerir o Azure base de dados MySQL das regras de firewall com o portal do Azure
Regras de firewall ao nível do servidor permitem que os administradores aceder a uma base de dados do Azure para o servidor de MySQL de um endereço IP especificado ou um intervalo de endereços IP. 

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall ao nível do servidor no portal do Azure

1. Na página de servidor MySQL, em definições, clique em **ligação segurança** para abrir a página de segurança de ligação da base de dados do Azure para MySQL.

   ![Portal do Azure – clique em segurança de ligação](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Clique em **Adicionar IP do meu** na barra de ferramentas para criar uma regra com o endereço IP do seu computador, como percetível pelo sistema do Azure.

   ![Portal do Azure – clique em Adicionar meu IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Verifique o seu endereço IP antes de guardar a configuração. Em algumas situações, o endereço IP observado pelo portal do Azure é diferente do endereço IP utilizado ao aceder à internet e os servidores do Azure. Por conseguinte, terá de alterar o IP inicial e o IP final para se a função de regra, conforme esperado.

   Utilize um motor de busca ou outra ferramenta online para verificar o seu próprio endereço IP (por exemplo, pesquisar "qual é o meu endereço IP").

   ![Bing para saber o que é o meu IP](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Adicione intervalos de endereços adicionais. Em regras para a base de dados do Azure para firewall de MySQL, pode especificar um único endereço IP ou um intervalo de endereços. Se pretender limitar a regra para um único endereço IP, escreva o mesmo endereço nos campos de IP inicial e final IP. Abrir a firewall permite que os administradores e utilizadores aceder a qualquer base de dados no servidor de MySQL a que tenham credenciais válidas.

   ![Portal do Azure – regras de firewall ](./media/howto-manage-firewall-using-portal/5-specify-addresses.png)


5. Clique em **guardar** na barra de ferramentas para guardar esta regra de firewall ao nível do servidor. Aguarde a confirmação de que a atualização para as regras de firewall é efetuada com êxito.

   ![Portal do Azure – clique em Guardar](./media/howto-manage-firewall-using-portal/4-save-firewall-rule.png)

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Gerir regras de firewall ao nível do servidor existente utilizando o portal do Azure
Repita os passos para gerir as regras de firewall.
* Para adicionar o computador atual, clique em **+ adicionar My IP**.
* Para adicionar endereços IP adicionais, escreva o **nome da regra**, **IP inicial**, e **IP final**.
* Para modificar uma regra existente, clique em qualquer um dos campos existentes na regra e, em seguida, modifique.
* Para eliminar uma regra existente, clique no botão de reticências [...] e, em seguida, clique em **eliminar**.
* Clique em **Guardar** para guardar as alterações.

## <a name="next-steps"></a>Passos seguintes
Para obter ajuda na ligação para uma base de dados do Azure para o servidor de MySQL, consulte [bibliotecas de ligação para base de dados do Azure para MySQL](./concepts-connection-libraries.md)
