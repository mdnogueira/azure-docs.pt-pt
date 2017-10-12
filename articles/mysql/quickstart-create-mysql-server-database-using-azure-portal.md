---
title: "Guia de Introdução: Criar Bases de Dados do Azure para o servidor MySQL - portal do Azure | Microsoft Docs"
description: Este artigo mostra-lhe como utilizar o portal do Azure para criar rapidamente uma Base de Dados do Azure de exemplo para o servidor MySQL, em cerca de cinco minutos.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 08/15/2017
ms.openlocfilehash: 46aee37a00a923addc08dd185ce0326a64ccb48c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-database-for-mysql-server-by-using-the-azure-portal"></a>Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure
A Base de Dados do Azure para MySQL é um serviço gerido que utiliza para executar, gerir e dimensionar as Bases de Dados MySQL de elevada disponibilidade na cloud. Este Guia de Introdução mostra-lhe como criar uma Base de Dados do Azure para o servidor MySQL em cerca de cinco minutos através do portal do Azure.  

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Abra o browser e, em seguida, avance para o [portal do Azure](https://portal.azure.com/). Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.

## <a name="create-an-azure-database-for-mysql-server"></a>Criar uma Base de Dados do Azure para o servidor MySQL
Irá criar uma Base de Dados do Azure para o servidor MySQL com um conjunto definido de [recursos de armazenamento e computação](./concepts-compute-unit-and-storage.md). Irá criar o servidor dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

Siga estes passos para criar uma Base de Dados do Azure para o servidor MySQL:

1. Selecione o botão (+) **Novo** no canto superior esquerdo do portal.

2. Selecione **Bases de Dados** > **Base de Dados do Azure para MySQL**. Também pode escrever **MySQL** na caixa de pesquisa para encontrar o serviço.

    ![Base de Dados do Azure para opção MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

4. Preencha os novos detalhes do servidor com as informações seguintes, conforme mostrado na imagem anterior:

    **Definição** | **Valor sugerido** | **Descrição do campo** 
    ---|---|---
    Nome do servidor | myserver4demo | Um nome exclusivo que identifica a sua Base de Dados do Azure para o servidor MySQL. O nome de domínio *mysql.database.azure.com* é acrescentado ao nome de servidor que indicar. O nome do servidor pode conter apenas letras minúsculas, números e o caráter de hífen (-). Tem de conter entre 3 e 63 carateres.
    Subscrição | A sua subscrição | A subscrição do Azure que quer utilizar para o servidor. Se tiver várias subscrições, escolha a subscrição na qual o recurso é cobrado.
    Grupo de recursos | myResourceGroup | Um novo nome do grupo de recursos ou um existente.
    Início de sessão de administrador do servidor | myadmin | Uma conta de início de sessão para utilizar quando ligar ao servidor. O nome de início de sessão de administrador não pode ser **azure_superuser**, **admin**, **administrator**, **root**, **guest** ou **public**.
    Palavra-passe | *A sua escolha* | Uma palavra-passe nova para a conta de administrador do servidor. Tem de conter entre 8 e 128 carateres. A palavra-passe tem de conter carateres das três categorias seguintes: letras em maiúscula inglesas, letras em minúscula inglesas, números (0 - 9) e carateres não alfanuméricos (!, $, #, %, etc.).
    Confirmar palavra-passe | *A sua escolha*| A palavra-passe da conta de administrador confirmada.
    Localização | *A região mais próxima dos seus utilizadores*| A localização que estiver mais próxima dos seus utilizadores ou de outras aplicações do Azure.
    Versão | *A versão mais recente*| A versão mais recente (a não ser que tenha requisitos específicos que exijam outra versão).
    Escalão de preço | **Básico**, **50 Unidades de Computação**, **50 GB** | A camada de serviços e o nível de desempenho da sua nova base de dados. Selecione o **escalão Básico** no separador na parte superior. Selecione a extremidade esquerda do controlo de deslize **Unidades de Computação** para ajustar o valor para a quantidade mais baixa no âmbito deste Guia de Introdução. Selecione **Ok** para guardar a seleção do escalão de preço. Para obter mais informações, veja a seguinte captura de ecrã.
    Afixar ao dashboard | Marcar | Permite uma fácil monitorização do servidor na página de rosto do dashboard do portal do Azure.

    > [!IMPORTANT]
    > O início de sessão e a palavra-passe de administrador de servidor que especificar aqui serão necessários para iniciar sessão no servidor e nas respetivas bases de dados mais tarde neste Guia de Introdução. Lembre-se ou grave estas informações para utilização posterior.
    > 

    ![Portal do Azure - fornecer a entrada de formulário necessária para criar o MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

4.  Selecione **Criar** para aprovisionar o servidor. O aprovisionamento pode demorar até 20 minutos.
   
5.  Selecione **Notificações** na barra de ferramentas (o ícone da campainha) para monitorizar o processo de implementação.

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor

A Base de Dados do Azure para o serviço MySQL cria uma firewall ao nível do servidor. A mesma impede que as aplicações e ferramentas externas se liguem ao servidor e a quaisquer bases de dados no servidor, a menos que seja criada uma regra de firewall para abrir a firewall aos endereços IP específicos. 

1.   Depois de concluída a implementação, localize o seu servidor. Se for preciso, pode procurá-lo. Por exemplo, selecione **Todos os Recursos** no menu à esquerda. Em seguida, escreva o nome do servidor, como o exemplo **myserver4demo**, para procurar o servidor recentemente criado. Selecione o nome do servidor da lista de resultados de pesquisa. É apresentada a página **Descrição Geral** do servidor, que fornece opções para configuração adicional.

2. Na página do servidor, selecione **Segurança da ligação**.

3.  No cabeçalho **Regras de firewall**, selecione a caixa de texto em branco na coluna **Nome da Regra** para começar a criar a regra de firewall. 

    Neste Guia de Introdução, vamos permitir todos os endereços IP no servidor, ao preencher as caixas em cada coluna com os seguintes valores:

    Nome da regra | IP de início | IP de fim 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. Na barra de ferramentas superior da página **Segurança da ligação**, selecione **Guardar**. Aguarde até que a notificação apareça e indique que a atualização foi concluída com êxito antes de continuar. 

    > [!NOTE]
    > As ligações à base de dados do Azure para MySQL comunicam através da porta 3306. Se tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 3306 poderá não ser permitido. Se for este o caso, não pode ligar ao servidor, a menos que o departamento de TI abra a porta 3306.
    > 

## <a name="get-the-connection-information"></a>Obter as informações da ligação
Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Poderá ter apontado esses valores anteriormente no artigo Guia de Introdução. Se não o tiver feito, pode encontrar facilmente o nome do servidor e as informações de início de sessão na página **Descrição Geral** do servidor ou na página **Propriedades** do portal do Azure.

Para os localizar, siga os passos seguintes: 

1. Abra a página **Descrição Geral** do servidor. Anote o **Nome do servidor** e **Nome de início de sessão de administrador do servidor**. 

2. Coloque o cursor sobre cada campo e o ícone de cópia é apresentado à direita do texto. Selecione o ícone de cópia conforme necessário para copiar os valores.

Neste exemplo, o nome do servidor é **myserver4demo.mysql.database.azure.com** e o início de sessão de administrador do servidor é **myadmin@myserver4demo**.

## <a name="connect-to-mysql-by-using-the-mysql-command-line-tool"></a>Ligar ao MySQL com a ferramenta de linha de comandos mysql
Estão disponíveis diversas aplicações que pode utilizar para ligar à sua Base de Dados do Azure para o servidor MySQL. 

Vamos utilizar primeiro a ferramenta de linha de comandos [mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) para ilustrar como ligar ao servidor. Também pode utilizar um browser e o Azure Cloud Shell conforme descrito aqui sem instalar software adicional. Se tiver o utilitário mysql instalado localmente, também pode ligar a partir do mesmo.

1. Inicie o Azure Cloud Shell através do ícone de terminal (**>_**) no canto superior direito do portal do Azure.

2.  O Azure Cloud Shell é aberto no browser, onde pode escrever os comandos bash da shell.

    ![Linha de comandos - exemplo de linha de comandos do mysql](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

3. Na linha de comandos Cloud Shell, ligue à Base de Dados do Azure para o servidor MySQL escrevendo a linha de comandos mysql.

    Para ligar a uma Base de Dados do Azure para o servidor MySQL com o utilitário mysql, utilize o seguinte formato:

    ```bash
    mysql --host <yourserver> --user <server admin login> --password
    ```

    Por exemplo, o comando seguinte liga ao nosso servidor de exemplo:

    ```azurecli-interactive
    mysql --host myserver4demo.mysql.database.azure.com --user myadmin@myserver4demo --password
    ```

    mysql parameter |Valor sugerido|Descrição
    ---|---|---
    --host | *Nome do servidor* | O valor de nome de servidor que foi utilizado anteriormente quando criou a Base de Dados do Azure para o servidor MySQL. O nosso servidor de exemplo é **myserver4demo.mysql.database.azure.com**. Utilize o nome de domínio completamente qualificado (**\*.mysql.database.azure.com**), conforme mostrado no exemplo. Se não se lembrar do nome do servidor, siga os passos na secção anterior para obter as informações da ligação. 
    --user | *Nome de início de sessão de administrador do servidor* |O nome de utilizador de início de sessão de administrador do servidor que foi fornecido quando criou a Base de Dados do Azure para o servidor MySQL anteriormente. Se não se lembrar do nome de utilizador, siga os passos na secção anterior para obter as informações da ligação. O formato é *username@servername*.
    --password | *Aguarde até que seja pedido* |A mesma palavra-passe que indicou quando criou o servidor. Tenha em conta que os carateres da palavra-passe introduzida não são apresentados na linha de comandos bash ao escrevê-los. Após introduzir a palavra-passe, selecione **Enter**.

   Assim que estiver ligado, o utilitário mysql apresenta uma linha de comandos `mysql>` onde pode escrever os comandos. 

   Segue-se um exemplo da saída mysql:

    ```bash
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65505
    Server version: 5.6.26.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql>
    ```
    > [!TIP]
    > Se a firewall não estiver configurada para permitir o endereço IP do Azure Cloud Shell, ocorre o seguinte erro:
    >
    > ERROR 2003 (28000): Client with IP address 123.456.789.0 is not allowed to access the server. (ERRO 2003 (28000): o cliente com o endereço IP 123.456.789.0 não tem permissão para aceder ao servidor.)
    >
    > Para resolver o erro, certifique-se de que a configuração do servidor corresponde aos passos na secção "Configure a server-level firewall rule" (Configurar uma regra de firewall ao nível do servidor) do artigo.

4. Para garantir que a ligação está funcional, veja o estado do servidor escrevendo `status` na linha de comandos mysql>.

    ```sql
    status
    ```

   > [!TIP]
   > Para obter comandos adicionais, veja [MySQL 5.7 Reference Manual - Chapter 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) (Manual de Referência do MySQL 5.7 - Capítulo 4.5.1).

5.  Crie uma base de dados vazia na linha de comandos **mysql>** escrevendo o seguinte comando:
    ```sql
    CREATE DATABASE quickstartdb;
    ```
    O comando pode demorar alguns minutos a concluir. 

    Dentro de uma Base de Dados do Azure para o servidor MySQL, pode criar uma ou várias bases de dados. Pode optar por criar uma base de dados por servidor para utilizar todos os recursos ou criar várias bases de dados para partilhar os recursos. Não há limite para o número de bases de dados que pode criar, mas múltiplas bases de dados partilham os mesmos recursos do servidor. 

6. Liste as bases de dados na linha de comandos **mysql>** escrevendo o seguinte comando:

    ```sql
    SHOW DATABASES;
    ```

7.  Escreva `\q` e, em seguida, selecione a tecla **Enter** para sair da ferramenta mysql. Pode fechar o Azure Cloud Shell depois de terminar.

Está agora ligado à Base de Dados do Azure para o servidor MySQL e criou uma base de dados de utilizador vazia. Continue para a secção seguinte para um exercício semelhante. O exercício seguinte liga-se ao mesmo servidor ao utilizar outra ferramenta comum, Workbench do MySQL.

## <a name="connect-to-the-server-by-using-the-mysql-workbench-gui-tool"></a>Ligar ao servidor com a ferramenta Workbench GUI do MySQL
Para ligar ao servidor com a ferramenta Workbench GUI do MySQL, siga os passos seguintes:

1.  Abra a aplicação MySQL Workbench no computador cliente. Pode transferir e instalar o MySQL Workbench em [Transferir MySQL Workbench](https://dev.mysql.com/downloads/workbench/).

2.  Na caixa de diálogo **Configurar Ligação Nova**, introduza as informações seguintes no separador **Parâmetros**:

    ![Configurar nova ligação](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

    |Definição |Valor sugerido|Descrição do campo|
    |---|---|---|
     Nome da ligação | Ligação de demonstração | Uma etiqueta para esta ligação. |
    Método de ligação | Standard (TCP/IP) | Standard (TCP/IP) é suficiente. |
    Nome de anfitrião | *Nome do servidor* | O valor de nome de servidor que foi utilizado quando criou anteriormente a Base de Dados do Azure para o servidor MySQL. O nosso servidor de exemplo é **myserver4demo.mysql.database.azure.com**. Utilize o nome de domínio completamente qualificado (**\*.mysql.database.azure.com**), conforme mostrado no exemplo. Se não se lembrar do nome do servidor, siga os passos na secção anterior para obter as informações da ligação.|
     Porta | 3306 | A porta a utilizar quando ligar à Base de Dados do Azure para o servidor MySQL. |
    Nome de utilizador |  *Nome de início de sessão de administrador do servidor* | As informações de início de sessão de administrador do servidor que forneceu quando criou a Base de Dados do Azure para o servidor MySQL anteriormente. O nosso nome de utilizador de exemplo é **myadmin@myserver4demo**. Se não se lembrar do nome de utilizador, siga os passos na secção anterior para obter as informações da ligação. O formato é *username@servername*.
    Palavra-passe | *A sua palavra-passe* | Selecione o botão **Armazenar no Cofre...** para guardar a palavra-passe. |

3. Selecione **Testar Ligação** para testar se todos os parâmetros estão configurados corretamente. Em seguida, clique em **OK** para guardar a ligação. 

    > [!NOTE]
    > O SSL é aplicado por predefinição no seu servidor e requer uma configuração adicional para uma ligação com êxito. Para obter mais informações, veja [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Configurar a conectividade SSL na sua aplicação para ligar em segurança à Base de Dados do Azure para MySQL). Para desativar o SSL neste Guia de Introdução, aceda ao portal do Azure. Em seguida, selecione a página de segurança de Ligação para desativar o botão de alternar de ligação **Impor SSL**.

## <a name="clean-up-resources"></a>Limpar recursos
Pode limpar os recursos que criou no Guia de Introdução de duas formas. Pode eliminar o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md), que inclui todos os recursos no grupo de recursos. Se pretender manter os outros recursos intactos, elimine apenas o recurso de um servidor.

> [!TIP]
> Outros Guias de Introdução desta coleção compilados com base neste Guia de Introdução. Se pretender continuar a trabalhar com Guias de Introdução, não limpe os recursos que criou neste Guia de Introdução. Se não quiser continuar, utilize os passos seguintes para eliminar todos os recursos que criou com este Guia de Introdução.
>

Para eliminar o grupo de recursos inteiro, incluindo o servidor recentemente criado, siga os seguintes passos:

1.  Localize o grupo de recursos no portal do Azure. No menu do lado esquerdo, selecione **Grupos de recursos** e, em seguida, selecione o nome do grupo de recursos, (como o nosso exemplo **myresourcegroup**).

2.  Na página do grupo de recursos, selecione **Eliminar**. Em seguida, escreva o nome do grupo de recursos, (como o nosso exemplo **myresourcegroup**), na caixa para confirmar a eliminação e, em seguida, selecione **Eliminar**.

Para eliminar apenas o servidor recentemente criado, siga os passos seguintes:

1.  Localize o servidor no portal do Azure, se ainda não estiver aberto. No menu no lado esquerdo do Portal do Azure, selecione **Todos os recursos**. Em seguida, procure o servidor que criou.

2.  Na página **Descrição geral**, selecione **Eliminar**. 

    ![Base de Dados do Azure para MySQL - Eliminar servidor](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)

3.  Confirme o nome do servidor que pretende eliminar e veja as bases de dados incluídas que são afetadas. Escreva o nome do servidor na caixa, (como o nosso exemplo **myserver4demo**). Selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Design your first Azure Database for MySQL database](./tutorial-design-database-using-portal.md) (Conceber a sua primeira Base de Dados do Azure para a base de dados MySQL)

