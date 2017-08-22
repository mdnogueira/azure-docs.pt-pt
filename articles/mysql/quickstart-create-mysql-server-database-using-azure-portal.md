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
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 829c7e73cbf22d866bbd6fd54edc7a954ad7174c
ms.contentlocale: pt-pt
ms.lasthandoff: 08/16/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure
A Base de Dados do Azure para MySQL é um serviço gerido que lhe permite executar, gerir e dimensionar as bases de dados MySQL de alta disponibilidade na cloud. Este Guia de Introdução mostra-lhe como criar uma Base de Dados do Azure para o servidor MySQL através do portal do Azure em cerca de cinco minutos. 

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Abra o browser e navegue para o [portal do Microsoft Azure](https://portal.azure.com/). Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.

## <a name="create-azure-database-for-mysql-server"></a>Criar Base de Dados do Azure para o servidor MySQL
É criada uma Base de Dados do Azure para o servidor MySQL com um conjunto definido de [recursos de armazenamento e computação](./concepts-compute-unit-and-storage.md). O servidor é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

Siga estes passos para criar uma Base de Dados do Azure para o servidor MySQL:

1. Clique no botão **Novo** (+) localizado no canto superior esquerdo do portal do Azure.

2. Selecione **Bases de Dados** na página **Nova** e selecione **Base de Dados do Azure para o MySQL** na página **Bases de Dados**. Também pode escrever **MySQL** na caixa de pesquisa da página Nova para encontrar o serviço.
![Portal do Azure – nova - base de dados – MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. Preencha os novos detalhes do servidor com as informações seguintes, conforme mostrado na imagem anterior:

    **Definição** | **Valor sugerido** | **Descrição do Campo** 
    ---|---|---
    Nome do servidor | myserver4demo | Escolha um nome exclusivo que identifique a sua Base de Dados do Azure para o servidor MySQL. O nome de domínio *mysql.database.azure.com* é acrescentado ao nome de servidor que indicar, ao qual as aplicações se devem ligar. O nome do servidor pode conter apenas letras minúsculas, números, o caráter de hífen (-) e tem de ter entre 3 e 63 carateres.
    Subscrição | A sua subscrição | A subscrição do Azure que quer utilizar para o servidor. Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso é cobrado.
    Grupo de recursos | myResourceGroup | Pode criar um nome de grupo de recursos novo ou utilizar um já existente na sua subscrição.
    Início de sessão de administrador do servidor | myadmin | Utilize a sua própria conta de início de sessão quando ligar ao servidor. O nome de início de sessão de administrador não pode ser “azure_superuser”, “admin”, “administrator”, “root”, “guest” ou “public”.
    Palavra-passe | *A sua escolha* | Crie uma palavra-passe nova para a conta de administrador do servidor. Tem de conter entre 8 e 128 carateres e das três categorias seguintes: letras em maiúscula inglesas, letras em minúscula inglesas, números (0 - 9) e carateres não alfanuméricos (!, $, #, %, etc.).
    Confirmar palavra-passe | *A sua escolha*| Confirme a palavra-passe da conta de administrador.
    Localização | *A região mais próxima dos seus utilizadores*| Escolha a localização que estiver mais próxima dos seus utilizadores ou de outras aplicações do Azure.
    Versão | *Escolha a versão mais recente*| Escolha a versão mais recente, a não ser que tenha requisitos específicos.
    Escalão de Preço | **Básico**, **50 Unidades de Computação** **50 GB** | Clique em **Escalão de preço** para especificar o escalão de serviço e o nível de desempenho para a nova base de dados. Escolha o **Escalão básico** no separador na parte superior. Clique na extremidade esquerda do controlo de deslize **Unidades de Computação** para ajustar o valor para a quantidade mínima no âmbito deste Guia de Introdução. Clique em **OK** para guardar a seleção do escalão de preço. Veja a captura de ecrã abaixo.
    Afixar ao dashboard | Marcar | Assinale a opção **Afixar ao dashboard** para permitir uma fácil monitorização do servidor na página de rosto do dashboard do portal do Azure.

    > [!IMPORTANT]
    > O início de sessão e a palavra-passe de administrador de servidor que especificar aqui serão necessários para iniciar sessão no servidor e nas respetivas bases de dados mais tarde neste Guia de Introdução. Lembre-se ou grave estas informações para utilização posterior.
    > 

    ![Portal do Azure - fornecer a entrada de formulário necessária para criar o MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

4.  Clique em **Criar** para aprovisionar o servidor. O aprovisionamento demora alguns minutos, num máximo de 20 minutos.
   
5.  Na barra de ferramentas, clique em **Notificações** (ícone da campainha) para monitorizar o processo de implementação.

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor

A Base de Dados do Azure para o serviço MySQL cria uma firewall ao nível do servidor. Esta firewall impede que as aplicações e ferramentas externas estabeleçam ligação ao servidor e a quaisquer bases de dados no servidor, a menos que seja criada uma regra de firewall para abrir a firewall aos endereços IP específicos. 

1.  Localize o seu servidor depois de concluída a implementação. Se necessário, pode procurá-lo. Por exemplo, clique em **Todos os Recursos**, no menu do lado esquerdo, e escreva o nome do servidor (como o exemplo *myserver4demo*) para procurar o servidor recentemente criado. Clique no nome do servidor apresentado no resultado da pesquisa. É apresentada a página **Descrição Geral** do servidor, que fornece opções para configuração adicional.

2. Na página do servidor, selecione **Segurança da ligação**.

3.  No cabeçalho **Regras de firewall**, clique na caixa de texto em branco na coluna **Nome da Regra** para começar a criar a regra de firewall. 

    Neste Guia de Introdução, vamos permitir todos os endereços IP no servidor, ao preencher a caixa de texto em cada coluna com os seguintes valores:

    Nome da Regra | IP de início | IP de fim 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. Na barra de ferramentas superior da página **Segurança da Ligação**, clique em **Guardar**. Aguarde uns instantes e repare que a notificação mostra que a atualização de segurança da ligação foi concluída com êxito, antes de continuar.

    > [!NOTE]
    > As ligações à base de dados do Azure para MySQL comunicam através da porta 3306. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 3306 poderá não ser permitido pela firewall da rede. Se assim for, não poderá ligar ao servidor, a menos que o departamento de TI abra a porta 3306.
    > 

## <a name="get-the-connection-information"></a>Obter as informações da ligação
Para ligar ao seu servidor de bases de dados, tem de lembrar-se do nome completo do servidor e das credenciais de início de sessão de administrador. Poderá ter apontado esses valores anteriormente no artigo Guia de Introdução. Se não o tiver feito, pode encontrar facilmente o nome do servidor e as informações de início de sessão na página **Descrição Geral** do servidor ou na página **Propriedades** do portal do Azure.

1. Abra a página **Descrição Geral** do servidor. Anote o **Nome do servidor** e **Nome de início de sessão de administrador do servidor**. 
    Coloque o cursor sobre cada campo e o ícone de cópia é apresentado à direita do texto. Clique no ícone de cópia conforme necessário para copiar os valores.

    Neste exemplo, o nome do servidor é *myserver4demo.mysql.database.azure.com* e o início de sessão de administrador do servidor é *myadmin@myserver4demo*.

## <a name="connect-to-mysql-using-mysql-command-line-tool"></a>Ligar ao MySQL com a ferramenta de linha de comandos mysql
Estão disponíveis diversas aplicações que pode utilizar para ligar à sua Base de Dados do Azure para o servidor MySQL. Vamos utilizar primeiro a ferramenta de linha de comandos [mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) para ilustrar como ligar ao servidor.  Pode utilizar um browser e o Azure Cloud Shell conforme descrito aqui sem ter de instalar software adicional. Se tiver o utilitário mysql instalado localmente no seu computador, pode também ligar a partir do mesmo.

1. Inicie o Azure Cloud Shell através do ícone de terminal (> _) no canto superior direito da página Web do portal do Azure.

2. O Azure Cloud Shell é aberto no browser, permitindo-lhe escrever os comandos bash da shell.

    ![Linha de comandos - exemplo de linha de comandos do mysql](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

3. Na linha de comandos Cloud Shell, estabeleça ligação à Base de Dados do Azure para o servidor MySQL, ao escrever a linha de comandos mysql na linha verde.

    O formato seguinte é utilizado para estabelecer ligação a uma Base de Dados do Azure para o servidor MySQL com o utilitário mysql:
    ```bash
    mysql --host <yourserver> --user <server admin login> --password
    ```

    Por exemplo, o comando seguinte liga ao nosso servidor de exemplo:
    ```azurecli-interactive
    mysql --host myserver4demo.mysql.database.azure.com --user myadmin@myserver4demo --password
    ```

    mysql parameter |Valor sugerido|Descrição
    ---|---|---
    --host | *server name* | Especifique o valor de nome de servidor que foi utilizado quando criou a Base de Dados do Azure para MySQL anteriormente. O nosso servidor de exemplo mostrado é myserver4demo.mysql.database.azure.com. Utilize o nome de domínio completamente qualificado (\*.mysql.database.azure.com), conforme mostrado no exemplo. Siga os passos na secção anterior para obter as informações da ligação, se não se lembrar do nome do servidor. 
    --user | *nome de início de sessão de administrador do servidor* |Introduza o nome de utilizador de início de sessão de administrador do servidor que foi fornecido quando criou a Base de Dados do Azure para MySQL anteriormente. Siga os passos na secção anterior para obter as informações da ligação, se não se lembrar do nome de utilizador.  O formato é *username@servername*.
    --password | *aguarde até ao pedido* | Ser-lhe-á solicitado para “Introduzir palavra-passe” depois de introduzir o comando. Quando lhe for pedido, escreva a palavra-passe que indicou quando criou o servidor.  Tenha em conta que os carateres da palavra-passe introduzida não são apresentados na linha de comandos bash ao escrevê-los. Depois de escrever todos os carateres, prima “enter” para se autenticar e ligar.

   Assim que estiver ligado, o utilitário mysql apresenta uma linha de comandos `mysql>` para que possa escrever os comandos. 

    Exemplo de saída mysql:
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
    > Para resolver o erro, certifique-se de que a configuração do servidor corresponde aos passos na secção *Configurar uma regra de firewall ao nível do servidor* do artigo.

4. Veja o estado do servidor para certificar-se de que a ligação está funcional. Escreva `status` em mysql> linha de comandos, assim que estiver ligado.
    ```sql
    status
    ```

   > [!TIP]
   > Para obter comandos adicionais, veja [MySQL 5.7 Reference Manual - Chapter 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) (Manual de Referência do MySQL 5.7 - Capítulo 4.5.1).

5.  Crie uma base de dados vazia na linha de comandos mysql>, escrevendo o seguinte comando:
    ```sql
    CREATE DATABASE quickstartdb;
    ```
    O comando pode demorar alguns minutos a concluir. 

    Dentro de uma Base de Dados do Azure para o servidor MySQL, pode criar uma ou várias bases de dados. Pode optar por criar uma base de dados por servidor para utilizar todos os recursos ou criar várias bases de dados para partilhar os recursos. Não há limite para o número de bases de dados que podem ser criadas, mas múltiplas bases de dados partilham os mesmos recursos do servidor. 

6. Liste as bases de dados na linha de comandos mysql>, escrevendo o seguinte comando:

    ```sql
    SHOW DATABASES;
    ```

7.  Escreva `\q` e prima ENTER para sair da ferramenta mysql. Pode fechar o Azure Cloud Shell depois de terminar.

Está agora ligado à Base de Dados do Azure para MySQL e criou uma base de dados de utilizador vazia. Continue para a secção seguinte para repetir um exercício semelhante ou ligar ao mesmo servidor com outra ferramenta comum, o MySQL Workbench.

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Ligar ao servidor com a ferramenta Workbench GUI do MySQL
Para ligar ao servidor MySQL do Azure com a ferramenta da GUI MySQL Workbench:

1.  Inicie a aplicação MySQL Workbench no computador cliente. Pode transferir e instalar o MySQL Workbench [aqui](https://dev.mysql.com/downloads/workbench/).

2.  Na caixa de diálogo **Configurar Ligação Nova**, introduza as informações seguintes no separador **Parâmetros**:

    ![configurar ligação nova](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

    | **Definição** | **Valor sugerido** | **Descrição do Campo** |
    |---|---|---|
    |   Nome da Ligação | Ligação de Demonstração | Especifique uma etiqueta para esta ligação. |
    | Método de Ligação | Standard (TCP/IP) | Standard (TCP/IP) é suficiente. |
    | Nome de anfitrião | *server name* | Especifique o valor de nome de servidor que foi utilizado quando criou a Base de Dados do Azure para MySQL anteriormente. O nosso servidor de exemplo mostrado é myserver4demo.mysql.database.azure.com. Utilize o nome de domínio completamente qualificado (\*.mysql.database.azure.com), conforme mostrado no exemplo. Siga os passos na secção anterior para obter as informações da ligação, se não se lembrar do nome do servidor.  |
    | Porta | 3306 | Utilize sempre a porta 3306 para se ligar à Base de Dados do Azure para MySQL. |
    | Nome de utilizador |  *nome de início de sessão de administrador do servidor* | Introduza o nome de utilizador de início de sessão de administrador do servidor que foi fornecido quando criou a Base de Dados do Azure para MySQL anteriormente. O nosso nome de utilizador de exemplo é myadmin@myserver4demo. Siga os passos na secção anterior para obter as informações da ligação, se não se lembrar do nome de utilizador. O formato é *username@servername*.
    | Palavra-passe | a sua palavra-passe | Clique no botão Armazenar no Cofre... mas guarde a palavra-passe. |

    Clique em **Testar Ligação** para testar se todos os parâmetros estão configurados corretamente. Clique em OK para guardar a ligação. 

    > [!NOTE]
    > SSL é aplicado por predefinição no seu servidor e requer uma configuração extra, de modo a poder ligar com êxito. Veja [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Configurar a conectividade SSL na sua aplicação para ligar em segurança à Base de Dados do Azure para o MySQL).  Se pretender desativar o SSL para este Guia de Introdução, visite o portal do Azure e clique na página de segurança da Ligação para desativar o botão para ativar/desativar Impor ligação SLL.

## <a name="clean-up-resources"></a>Limpar recursos
Limpe os recursos que criou no guia de introdução, ao eliminar o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md), que inclui todos os recursos no grupo de recursos, ou ao eliminar um recurso do servidor, se pretender manter os outros recursos intactos.

> [!TIP]
> Outros Guias de Introdução desta coleção têm por base este Guia de Introdução. Se pretender continuar a trabalhar com os guias de introdução subsequentes, não limpe os recursos criados neste guia de introdução. Se não pretender continuar, utilize os passos seguintes para eliminar todos os recursos criados por este guia de introdução no portal do Azure.
>

Para eliminar o grupo de recursos inteiro, incluindo o servidor recentemente criado:
1.  Localize o grupo de recursos no portal do Azure. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome do grupo de recursos, como o nosso exemplo **myresourcegroup**.
2.  Na página do grupo de recursos, clique em **Eliminar**. Em seguida, escreva o nome do grupo de recursos, como o nosso exemplo **myresourcegroup**, na caixa de texto para confirmar a eliminação e, em seguida, clique em **Eliminar**.

Ou, em alternativa, para eliminar o servidor recentemente criado:
1.  Localize o servidor no portal do Azure, se não estiver aberto. No menu esquerdo do portal do Azure, clique em **Todos os recursos** e, em seguida, procure o servidor que criou.
2.  Na página **Descrição Geral**, clique no botão **Eliminar** no painel superior.
![Base de Dados do Azure para MySQL - Eliminar servidor](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)
3.  Confirme o nome do servidor que pretende eliminar e veja as bases de dados incluídas que são afetadas. Escreva o nome do servidor na caixa de texto, como o nosso exemplo **myserver4demo**e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Design your first Azure Database for MySQL database](./tutorial-design-database-using-portal.md) (Conceber a sua primeira Base de Dados do Azure para a base de dados MySQL)


