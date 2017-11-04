---
title: Criar e gerir a base de dados do Azure para o servidor de MySQL utilizando o portal do Azure | Microsoft Docs
description: "Este artigo descreve como pode rapidamente criar uma nova base de dados do Azure para o servidor de MySQL e gerir o servidor através do Portal do Azure."
services: mysql
author: v-chenyh
ms.author: nolanwu
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 6e9c541aac1241b6af0e4a58f5591d46f9a98c40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Criar e gerir a base de dados do Azure para o servidor de MySQL utilizando o portal do Azure
Este tópico descreve como pode criar rapidamente uma nova base de dados do Azure para o servidor de MySQL. Também inclui informações sobre como gerir o servidor utilizando o portal do Azure. Gestão de servidor inclui ver detalhes do servidor e bases de dados, a repor a palavra-passe e a eliminação do servidor.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Criar uma Base de Dados do Azure para o servidor MySQL
Siga estes passos para criar uma base de dados do Azure para o servidor de MySQL com o nome "mysqlserver4demo."

1. Clique em de **novo** botão localizada no canto superior esquerdo do portal do Azure.

2. Na nova página, selecione **bases de dados**e, em seguida, na página de bases de dados, selecione **base de dados do Azure para MySQL**.

    > É criada uma base de dados do Azure para o servidor de MySQL com um conjunto definido de [computação e armazenamento](./concepts-compute-unit-and-storage.md) recursos. A base de dados é criado dentro de um grupo de recursos do Azure e numa base de dados do Azure para o servidor de MySQL.

   ![Criar novo-servidor](./media/howto-create-manage-server-portal/create-new-server.png)

3. Preencha o formulário de MySQL da base de dados do Azure utilizando as seguintes informações:

    | **Campo do Formulário** | **Descrição do Campo** |
    |----------------|-----------------------|
    | *Nome do servidor* | Azure mysql (nome do servidor é exclusivo global) |
    | *Subscrição* | MySQLaaS (selecione no menu pendente) |
    | *Grupo de recursos* | MyResource (criar um novo grupo de recursos ou utilize uma já existente) |
    | *Início de sessão de administrador do servidor* | myadmin (configure o nome da conta de administração) |
    | *Palavra-passe* | palavra-passe de conta de administrador a configuração |
    | *Confirmar palavra-passe* | confirme a palavra-passe da conta de administrador |
    | *Localização* | Europa do Norte (selecione entre Europa do Norte e EUA oeste) |
    | *Versão* | 5.6 (escolher a base de dados do Azure para a versão do servidor MySQL) |

4. Clique em **escalão de preço** para especificar o nível de desempenho e o escalão de serviço para o novo servidor. Unidade de computação pode ser configurada entre 50 e 100 na camada básica, entre 100 e 200 no escalão Standard, e o armazenamento pode ser adicionado, com base na quantidade incluída. Para este guia HowTo, vamos escolha a unidade de computação de 50 e 50 GB. Clique em **OK** para guardar a sua seleção.

   ![Criar-server--escalão de preço](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Clique em **Criar** para aprovisionar o servidor. O aprovisionamento demora alguns minutos.

    > Selecione o **afixar ao dashboard** opção para permitir controlo fácil das implementações.
    > [!NOTE]
    > Embora na camada básica até 1000 GB e 10 000 GB no escalão Standard é suportada para o armazenamento, para pré-visualização pública máxima de armazenamento está temporariamente limitada a 1000 GB.</Include>

## <a name="update-an-azure-database-for-mysql-server"></a>Atualizar uma base de dados do Azure para o servidor de MySQL
Depois do novo servidor é aprovisionado, o utilizador tem duas opções para editar um servidor existente: repor a palavra-passe de administrador ou aumentar o servidor, ou para baixo, alterando as unidades de computação.

### <a name="change-the-administrator-user-password"></a>Alterar a palavra-passe de utilizador do administrador
1. No servidor **descrição geral** painel, clique em **Repor palavra-passe** para preencher uma janela de entrada e a confirmação da palavra-passe.

2. Introduza a nova palavra-passe e confirme a palavra-passe na janela, conforme mostrado:

   ![Reposição de palavra-passe](./media/howto-create-manage-server-portal/reset-password.png)

3. Clique em **OK** para guardar a nova palavra-passe.

### <a name="scale-updown-by-changing-compute-units"></a>Escala para cima/para baixo alterando as unidades de computação

1. No painel de servidor, em **definições**, clique em **escalão de preço** para abrir o painel de escalão de preço da base de dados do Azure para o servidor de MySQL.

2. Siga o passo 4 em **criar uma base de dados do Azure para o servidor de MySQL** para alterar as unidades de computação da mesma camada de preço.

## <a name="delete-an-azure-database-for-mysql-server"></a>Eliminar uma base de dados do Azure para o servidor de MySQL

1. No servidor **descrição geral** painel, clique em de **eliminar** botão de comando para abrir o painel de confirmação de eliminação.

2. Escreva o nome de servidor correto na caixa de entrada do painel para confirmação duplo.

3. Clique em de **eliminar** botão novamente para confirmar a ação de eliminação e, em seguida, aguardar para "êxito da eliminação" aparecer apareça na barra de notificação.

## <a name="list-the-azure-database-for-mysql-databases"></a>Lista a base de dados do Azure para bases de dados MySQL
No servidor **descrição geral** painel, desloque para baixo até verá o mosaico na parte inferior da base de dados. Todas as bases de dados estão listados na tabela. Clique em de **eliminar** botão de comando para abrir o painel de confirmação de eliminação.

   ![Mostrar-bases de dados](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Mostrar detalhes de uma base de dados do Azure para o servidor de MySQL
No painel de servidor, em **definições**, clique em **propriedades** para abrir o **propriedades** painel e, em seguida, ver todas as informações detalhadas sobre o servidor.

## <a name="next-steps"></a>Passos seguintes

[Início rápido: Criar a base de dados do Azure para o servidor de MySQL utilizando o portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)