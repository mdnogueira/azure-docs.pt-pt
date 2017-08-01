---
title: "Guia de Introdução: Criar Bases de Dados do Azure para o servidor MySQL - portal do Azure | Microsoft Docs"
description: Este artigo mostra-lhe como utilizar o portal do Azure para criar rapidamente uma Base de Dados do Azure de exemplo para o servidor MySQL, em cerca de cinco minutos.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: hero-article
ms.date: 06/14/2017
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: dba50b369fb87d5f6d5118038c75392bd719cc10
ms.contentlocale: pt-pt
ms.lasthandoff: 06/28/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure
Este artigo mostra-lhe como utilizar o portal do Azure para criar uma Base de Dados do Azure para o servidor MySQL em cerca de cinco minutos. 

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Abra o browser e navegue para o [portal do Microsoft Azure](https://portal.azure.com/). Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.

## <a name="create-azure-database-for-mysql-server"></a>Criar Base de Dados do Azure para o servidor MySQL
1. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.

2. Selecione **Bases de Dados** na página **Nova** e selecione **Base de Dados do Azure para o MySQL** na página **Bases de Dados**. Também pode escrever **MySQL** na caixa de pesquisa da página Nova para encontrar o serviço.
![Portal do Azure – nova - base de dados – MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. Preencha os novos detalhes do servidor com as informações seguintes, conforme mostrado na imagem anterior:

| **Definição** | **Valor sugerido** | **Descrição do Campo** |
|---|---|---|
| *Nome do servidor* | myserver4demo  | O nome do servidor deve ser globalmente exclusivo. |
| *Subscrição* | mysubscription | Selecione a subscrição na lista pendente. |
| *Grupo de recursos* | myResourceGroup | Crie um grupo de recursos ou utilize um existente. |
| *Início de sessão de administrador do servidor* | myadmin | Especifique um nome de conta para ser administrador no motor do MySQL. |
| *Palavra-passe* |  | Defina uma palavra-passe forte para a conta de administrador. |
| *Confirmar palavra-passe* |  | Confirme a palavra-passe da conta de administrador. |
| *Localização* |  | Selecione uma região disponível. |
| *Versão* | 5.7 | Escolha a versão mais recente. |
| *Escalão de Preço* | Básico, 50 Unidades de Computação, Armazenamento 50 (GB)  | Escolha **Escalão de preço**, **Unidades de Computação**, **Armazenamento (GB)** e clique em **OK**. |
| *Afixar ao Dashboard* | Marcar | Recomendamos que marque esta caixa para encontrar facilmente o servidor posteriormente |

   Clique em **Escalão de Preço** para especificar o escalão de preço e o nível de desempenho da nova base de dados. Para este guia de introdução, selecione Camada Básica, 50 Unidades de Computação e 50 GB de armazenamento incluído. Em seguida, clique em **OK** para guardar este escalão de preço.
   
   ![Portal do Azure - fornecer a entrada de formulário necessária para criar o MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

   Em seguida, clique em **Criar**. Num ou dois minutos, uma nova Base de Dados do Azure para o servidor MySQL estará em execução na cloud. Clique no botão **Notificações** (ícone de sino) na barra de ferramentas para monitorizar o processo de implementação.

## <a name="configure-the-firewall"></a>Configurar a firewall
Antes de ligar à Base de Dados do Azure para o MySQL pela primeira vez, configure a firewall e adicione o endereço IP de rede público do cliente (ou um intervalo) à lista de permissões.

1. Depois de concluída a implementação, clique em **Todos os Recursos** no menu esquerdo e escreva o nome **myserver4demo** para procurar o servidor recentemente criado. Clique no nome do servidor listado no resultado da pesquisa. É apresentada a página Descrição Geral do servidor, que fornece opções para configuração adicional.

2. No painel do servidor, selecione **Segurança da Ligação**.

3. Clique em **Adicionar o meu IP** para adicionar o endereço IP do seu computador local ou configurar um intervalo de endereços IP. Não se esqueça de clicar em **Guardar** depois de criar a regras.
  ![Portal do Azure - adicionar regra de firewall e guardar](./media/quickstart-create-mysql-server-database-using-azure-portal/5_firewall-settings.png)

## <a name="get-connection-information"></a>Obter informações da ligação
Obtenha o nome de domínio completamente qualificado do servidor MySQL do Azure no portal do Azure. O nome de domínio completamente qualificado é utilizado para ligar ao seu servidor com a ferramenta de linha de comandos **mysql.exe**.

1.  No [portal do Azure](https://portal.azure.com/), clique em **Todos os recursos**, no menu do lado esquerdo, e clique na sua Base de Dados do Azure para o servidor MySQL.

2.  Clique em **Propriedades**. Aponte **NOME DO SERVIDOR** e **INÍCIO DE SESSÃO DE ADMINISTRADOR DO SERVIDOR**.
Neste exemplo, o nome do servidor é *myserver4demo.mysql.database.azure.com* e o início de sessão de administrador do servidor é *myadmin@myserver4demo*.

## <a name="connect-to-the-server-using-mysqlexe-command-line-tool"></a>Ligar ao servidor com a ferramenta de linha de comandos mysqlexe
Utilize a [ferramenta de linha de comandos do mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) para estabelecer uma ligação à base de dados do Azure para o servidor MySQL. Pode executar a ferramenta de linha de comandos do mysql no navegador com o Azure Cloud Shell ou iniciá-la a partir do seu computador com as ferramentas do mysql instaladas localmente. Para iniciar o Azure Cloud Shell, clique no botão `Try It` num bloco de código neste artigo ou visite o [portal do Azure](https://portal.azure.com) e clique no ícone `>_` da barra de ferramentas superior à direita. 

1. Escreva o comando para estabelecer a ligação:
```azurecli-interactive
mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
```

2. Veja o estado do servidor para certificar-se de que a ligação está funcional. Escreva `status` em mysql> linha de comandos, assim que estiver ligado.
```sql
status
```

   ![Linha de comandos - exemplo de linha de comandos do mysql](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

   > [!TIP]
   > Para obter comandos adicionais, veja [MySQL 5.7 Reference Manual - Chapter 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) (Manual de Referência do MySQL 5.7 - Capítulo 4.5.1).

3. Crie uma base de dados vazia, escrevendo o comando `CREATE DATABASE` em mysql > linha de comandos.

   ```sql
   CREATE DATABASE quickstartdb;
   ```

   Dentro de uma Base de Dados do Azure para o servidor MySQL, pode ter uma ou várias bases de dados. Pode optar por criar uma base de dados por servidor para utilizar todos os recursos ou criar várias bases de dados para partilhar os recursos. Não há limite para o número de bases de dados que podem ser criadas, mas múltiplas bases de dados partilham os mesmos recursos do servidor.  

4. Liste as bases de dados, escrevendo o comando `SHOW DATABASES` em mysql> linha de comandos.

   ```sql
   SHOW DATABASES;
   ```

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Ligar ao servidor com a ferramenta Workbench GUI do MySQL
1.  Inicie a aplicação MySQL Workbench no computador cliente. Pode transferir e instalar o MySQL Workbench [aqui](https://dev.mysql.com/downloads/workbench/).

2.  Na caixa de diálogo **Configurar Ligação Nova**, introduza as informações seguintes no separador **Parâmetros**:

   ![configurar ligação nova](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

| **Definição** | **Valor sugerido** | **Descrição do Campo** |
|---|---|---|
|   *Nome da Ligação* | Ligação de Demonstração| Especifique uma etiqueta para esta ligação. |
| *Método de Ligação* | Standard (TCP/IP) | Standard (TCP/IP) é suficiente. |
| *Nome de Anfitrião* | myserver4demo.mysql.database.azure.com | Utilize o nome de servidor completamente qualificado do seu servidor. |
| *Porta* | 3306 | Utilize a porta predefinida 3306. |
| *Nome de Utilizador* | myadmin@myserver4demo  | Utilize o início de sessão de administrador de servidor que anotou anteriormente com um caráter @ e o nome do servidor. |
| *Palavra-passe* | a sua palavra-passe | Clique no botão Armazenar no Cofre... mas guarde a palavra-passe. |

Clique em **Testar Ligação** para testar se todos os parâmetros estão configurados corretamente. Clique em OK para guardar a ligação. 

> [!NOTE]
> SSL é aplicado por predefinição no seu servidor e requer uma configuração extra, de modo a poder ligar com êxito. Veja [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Configurar a conectividade SSL na sua aplicação para ligar em segurança à Base de Dados do Azure para o MySQL).  Se pretender desativar o SSL para este guia de introdução, visite o portal do Azure e clique na página de segurança da Ligação para desativar o botão para ativar/desativar Impor ligação SLL.

## <a name="clean-up-resources"></a>Limpar recursos
Limpe todos os recursos que criou no guia de introdução ao eliminar o [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> Serão criados outros guias de introdução nesta coleção através deste guia. Se pretender continuar a trabalhar com os guias de introdução subsequentes, não limpe os recursos criados neste guia de introdução. Se não pretender continuar, utilize os passos seguintes para eliminar todos os recursos criados por este guia de introdução no portal do Azure.

1.  No menu esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique em **myresourcegroup**.
2.  Na página do grupo de recursos, clique em **Eliminar**, escreva **myresourcegroup** na caixa de texto e, em seguida, clique em Eliminar.

Se pretender eliminar o servidor recentemente criado:
1.  No menu esquerdo o portal do Azure, clique em Servidores PostgreSQL e procure o servidor que acabou de criar
2.  Na página Descrição Geral, clique no botão Eliminar no painel superior ![Base de Dados do Azure para o MySQL – Eliminar servidor](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)
3.  Confirme o nome do servidor que pretende eliminar e veja as bases de dados incluídas que são afetadas. Escreva **myserver4demo** na caixa de texto e, em seguida, clique em Eliminar.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Design your first Azure Database for MySQL database](./tutorial-design-database-using-portal.md) (Conceber a sua primeira Base de Dados do Azure para a base de dados MySQL)


