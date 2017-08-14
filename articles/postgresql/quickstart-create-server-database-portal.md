---
title: 'Portal do Azure: criar uma Base de Dados do Azure para o PostgreSQL | Microsoft Docs'
description: "Guia de introdução para criar e gerir a Base de Dados do Azure para o servidor PostgreSQL com a interface de utilizador do portal do Azure."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 08/04/2017
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: e89058a500aeb542ae4c7dc6bb4a9b9ae54db7f2
ms.contentlocale: pt-pt
ms.lasthandoff: 08/09/2017

---

# <a name="create-an-azure-database-for-postgresql-in-the-azure-portal"></a>Criar uma Base de Dados do Azure para o PostgreSQL no portal do Azure

A Base de Dados do Azure para o PostgreSQL é um serviço gerido que lhe permite executar, gerir e dimensionar as bases de dados de alta disponibilidade do PostgreSQL na cloud. Este guia de introdução mostra-lhe como criar uma Base de Dados do Azure para o servidor PostgreSQL através do portal do Azure.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Criar uma Base de Dados do Azure para o PostgreSQL

É criada uma Base de Dados do Azure para o servidor PostgreSQL com um conjunto definido de [recursos de armazenamento e computação](./concepts-compute-unit-and-storage.md). O servidor é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

Siga estes passos para criar uma Base de Dados do Azure para o servidor PostgreSQL:
1.  Clique no botão **Novo** (+) localizado no canto superior esquerdo do portal do Azure.
2.  Selecione **Bases de Dados**, na página **Nova**, e selecione **Base de Dados do Azure para o PostgreSQL**, na página **Bases de Dados**.
 ![Base de Dados do Azure para o PostgreSQL – Criar a base de dados](./media/quickstart-create-database-portal/1-create-database.png)

3.  Preencha os novos detalhes do servidor com as informações seguintes, conforme mostrado na imagem anterior:

    Definição|Valor sugerido|Descrição
    ---|---|---
    Nome do servidor |*mypgserver-20170401*|Escolha um nome exclusivo que identifique a sua Base de Dados do Azure para o servidor PostgreSQL. O nome de domínio *postgres.database.azure.com* é acrescentado ao nome de servidor que indicar e aos quais as aplicações se devem ligar. O nome do servidor pode conter apenas letras minúsculas, números, o caráter de hífen (-) e tem de ter entre 3 e 63 carateres.
    Subscrição|*A sua subscrição*|A subscrição do Azure que quer utilizar para o servidor. Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso é cobrado.
    Grupo de Recursos|*myresourcegroup*| Pode criar um nome de grupo de recursos novo ou utilizar um já existente na sua subscrição.
    Início de sessão de administrador do servidor |*mylogin*| Utilize a sua própria conta de início de sessão quando ligar ao servidor. O nome de início de sessão de administrador não pode ser “superutilizador_azure”, “administrador_azure_pg”, “administrador”, “admin”, “raiz, “convidado” ou “público” e não pode começar por “pg_”.
    Palavra-passe |*A sua escolha* | Crie uma palavra-passe nova para a conta de administrador do servidor. Tem de conter entre 8 e 128 carateres e das três categorias seguintes: letras em maiúscula inglesas, letras em minúscula inglesas, números (0 - 9) e carateres não alfanuméricos (!, $, #, %, etc.).
    Localização|*A região mais próxima dos seus utilizadores*| Escolha a localização que esteja mais próxima dos seus utilizadores.
    Versão do PostgreSQL|*Escolha a versão mais recente*| Escolha a versão mais recente, a não ser que tenha requisitos específicos.
    Escalão de Preço | **Básico**, **50 Unidades de Computação** **50 GB** | Clique em **Escalão de preço** para especificar o escalão de serviço e o nível de desempenho para a nova base de dados. Escolha o escalão Básico no separador na parte superior. Clique na extremidade esquerda do controlo de deslize Unidades de Computação para ajustar o valor para a quantidade mínima no âmbito deste início rápido. Clique em **OK** para guardar a seleção do escalão de preço. Veja a captura de ecrã abaixo.
    | Afixar ao dashboard | Marcar | Assinale a opção **Afixar ao dashboard** para permitir uma fácil monitorização do servidor na página de rosto do dashboard do portal do Azure.

  > [!IMPORTANT]
  > O início de sessão e a palavra-passe de administrador de servidor que especificar aqui serão necessários para iniciar sessão no servidor e nas respetivas bases de dados mais tarde neste guia de introdução. Lembre-se ou grave estas informações para utilização posterior.

    ![Base de Dados do Azure para PostgreSQL – escolher o escalão de preços](./media/quickstart-create-database-portal/2-service-tier.png)

4.  Clique em **Criar** para aprovisionar o servidor. O aprovisionamento demora alguns minutos, num máximo de 20 minutos.

5.  Na barra de ferramentas, clique em **Notificações** para monitorizar o processo de implementação.
 ![Base de Dados do Azure para o PostgreSQL – Ver as notificações](./media/quickstart-create-database-portal/3-notifications.png)
   
  Por predefinição, é criada a base de dados **postgres** no servidor. A base de dados [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) é uma base de dados predefinida que se destina a ser utilizada por utilizadores, utilitários e aplicações de terceiros. 

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor

A Base de Dados do Azure para o serviço PostgreSQL cria uma firewall ao nível do servidor. Esta firewall impede que as aplicações e ferramentas externas estabeleçam ligação ao servidor e a quaisquer bases de dados no servidor, a menos que seja criada uma regra de firewall para abrir a firewall aos endereços IP específicos. 

1.  Localize o seu servidor depois de concluída a implementação. Se necessário, pode procurá-lo. Por exemplo, clique em **Todos os Recursos**, no menu do lado esquerdo, e escreva o nome do servidor (como o exemplo mypgserver-20170401) para procurar o servidor recentemente criado. Clique no nome do servidor apresentado no resultado da pesquisa. É apresentada a página Descrição Geral do servidor, que fornece opções para configuração adicional.
 
    ![Base de Dados do Azure para o PostgreSQL – Procurar o servidor ](./media/quickstart-create-database-portal/4-locate.png)

2.  Na página do servidor, selecione **Segurança da Ligação**. 
    ![Base de Dados do Azure para PostgreSQL – Criar Regra de Firewall](./media/quickstart-create-database-portal/5-firewall-2.png)

3.  No cabeçalho **Regras de firewall**, clique na caixa de texto em branco na coluna **Nome da Regra** para começar a criar a regra de firewall. 

    Neste início rápido, vamos permitir todos os endereços IP no servidor, ao preencher a caixa de texto em cada coluna com os valores seguintes:

    Nome da Regra | IP de início | IP de fim 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. Na barra de ferramentas superior da página Segurança da Ligação, clique em **Guardar**. Em seguida, clique no **X** para fechar a página.

    > [!NOTE]
    > O servidor PostgreSQL do Azure comunica através da porta 5432. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 5432 poderá não ser permitido pela firewall da rede. Se assim for, não poderá ligar ao servidor da Base de Dados SQL do Azure, a menos que o departamento de TI abra a porta 5432.
  >

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Quando criámos a nossa Base de Dados do Azure para o servidor PostgreSQL, também foi criada uma base de dados predefinida, com o nome **postgres**. Para ligar ao seu servidor de bases de dados, tem de recuperar o nome completo do servidor e as credenciais de início de sessão de administrador. Poderá ter apontado esses valores anteriormente no artigo de início rápido. Se não o tiver feito, pode encontrar facilmente o nome do servidor e as informações de início de sessão a partir da página Descrição Geral do servidor, no portal do Azure.

1. Abra a página **Descrição Geral** do servidor. Anote o **Nome do servidor** e **Nome de início de sessão de administrador do servidor**.
    Coloque o cursor sobre cada campo e o ícone de cópia é apresentado à direita do texto. Clique no ícone de cópia conforme necessário para copiar os valores.

 ![Base de Dados do Azure para o PostgreSQL – Início de Sessão de Administrador do Servidor](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-postgresql-database-using-psql-in-cloud-shell"></a>Ligar-se à base de dados do PostgreSQL com o psql no Cloud Shell

Estão disponíveis diversas aplicações para ligar à sua Base de Dados do Azure para o servidor PostSQL. Vamos utilizar primeiro o utilitário de linha de comandos psql para ilustrar como ligar ao servidor.  Pode utilizar um browser e o Azure Cloud Shell conforme descrito aqui sem ter de instalar software adicional. Se tiver o utilitário de psql instalado localmente no seu computador, pode também ligar a partir do mesmo.

1. Inicie o Azure Cloud Shell através do ícone de terminal no painel de navegação superior.

   ![Base de Dados de Azure para o PostgreSQL – ícone do terminal do Azure Cloud Shell](./media/quickstart-create-database-portal/7-cloud-console.png)

2. O Azure Cloud Shell é aberto no browser, permitindo-lhe escrever os comandos bash da shell.

   ![Base de Dados do Azure para o PostgreSQL – Linha de Comandos Bash do Azure Shell](./media/quickstart-create-database-portal/8-bash.png)

3. Na linha de comandos do Cloud Shell, ligue a uma base de dados na sua Base de Dados do Azure para o servidor PostgreSQL, ao escrever a linha de comandos psql na linha verde.

    O formato seguinte é utilizado para estabelecer ligação a uma Base de Dados do Azure para o servidor PostgreSQL com o utilitário [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
    ```bash
    psql --host=<yourserver> --port=<port> --username=<server admin login> --dbname=<database name>
    ```

    Por exemplo, o comando seguinte liga a um servidor de exemplo:

    ```bash
    psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
    ```

    parâmetro psql |Valor sugerido|Descrição
    ---|---|---
    --host | *server name* | Especifique o valor de nome de servidor que foi utilizado quando criou a Base de Dados do Azure para PostgreSQL anteriormente. O nosso servidor de exemplo mostrado é mypgserver 20170401.postgres.database.azure.com. Utilize o nome de domínio completamente qualificado (\*.postgres.database.azure.com), conforme mostrado no exemplo. Siga a secção anterior para obter as informações da ligação, se não se lembrar do nome do servidor. 
    --port | **5432** | Utilize sempre a porta 5432 para se ligar à Base de Dados do Azure para PostgreSQL. 
    --username | *nome de início de sessão de administrador do servidor* |Introduza o nome de utilizador de início de sessão de administrador do servidor que foi fornecido quando criou a Base de Dados do Azure para PostgreSQL anteriormente. Siga a secção anterior para obter as informações da ligação, se não se lembrar do nome de utilizador.  O formato é *username@servername*.
    --dbname | **postgres** | Utilize o nome da base de dados predefinido gerado pelo sistema, *postgres*, para a primeira ligação. Mais tarde, vai criar a sua própria base de dados.

    Depois de executar o comando psql, com os seus próprios valores de parâmetros, é-lhe pedido que introduza a palavra-passe de administrador do servidor. Esta é a palavra-passe que indicou quando criou o servidor. 

    parâmetro psql |Valor sugerido|Descrição
    ---|---|---
    palavra-passe | *a sua palavra-passe de administrador* | Tenha em conta que os carateres da palavra-passe introduzida não são apresentados na linha do bash. Depois de escrever todos os carateres, prima “enter” para se autenticar e ligar.

4.  Assim que estiver ligado ao servidor, crie uma base de dados vazia na linha de comandos com o comando seguinte:
    ```bash
    CREATE DATABASE mypgsqldb;
    ```

5.  Na linha de comandos, execute o comando seguinte para mudar a ligação para a base de dados **mypgsqldb** recentemente criada.
    ```bash
    \c mypgsqldb
    ```

6.  Escreva \q e prima ENTER para sair do psql. Pode fechar o Azure Cloud Shell.

Agora, está ligado à Base de Dados do Azure para PostgreSQL e tem criada uma base de dados de utilizador vazia. Avance para a secção seguinte para se ligar com outra ferramenta pgAdmin comum.

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Ligar-se à base de dados do PostgreSQL com a pgAdmin

Para ligar ao servidor PostgreSQL do Azure com a ferramenta _pgAdmin_ da GUI
1.  Inicie a aplicação _pgAdmin_ no computador cliente. Pode instalar a _pgAdmin_ a partir do site http://www.pgadmin.org/.
2.  Escolha **Adicionar Novo Servidor**, no menu **Ligações Rápidas**.
3.  Na caixa de diálogo **Criar – Servidor**, separador **Geral**, introduza um Nome amigável exclusivo para o servidor, tal como **Servidor PostgreSQL do Azure**.
![ferramenta pgAdmin - criar - servidor](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)
4.  Na caixa de diálogo **Criar – Servidor**, separador **Ligação**, utilize as definições conforme especificado e clique em **Guardar**.
   ![pgAdmin – Criar – Servidor](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)
    - **Endereço/Nome do Anfitrião**: mypgserver 20170401.postgres.database.azure.com 
        - Nome de servidor completamente qualificado.
    - **Porta**: 5432
        - O número de porta utilizado por este servidor da base de dados é 5432.
    - **Base de Dados de Manutenção**: postgres 
        - O nome predefinido da base de dados gerado pelo sistema.
    - **Nome de utilizador:**mylogin@mypgserver-20170401 
        - O Início de sessão de administrador do servidor (user@mypgserver), obtido anteriormente neste guia de introdução.
    - **Palavra-passe**: a palavra-passe que escolher quando criou o servidor anteriormente neste guia de introdução.
    - **Modo SSL**: necessário
        - Por predefinição, todos os servidores PostgreSQL do Azure são criados com a imposição SSL ativada. Para desativar a imposição SSL, veja os detalhes em [Enforcing SSL (Impor SSL)](./concepts-ssl-connection-security.md).
5.  Clique em **Guardar**.
6.  No painel esquerdo do Browser, expanda os **Grupos de Servidores**. Selecione o **Servidor PostgreSQL do Azure**.
7.  Escolha o **Servidor** ao qual se ligou e, em seguida, escolha **Bases de Dados** abaixo do mesmo. 
8.  Clique com o botão direito do rato em **Bases de Dados** para Criar uma Base de Dados.
9.  Escolha um nome de base de dados, **mypgsqldb**, e o seu proprietário como o início de sessão de administrador do servidor **mylogin**.
10. Clique em **Guardar** para criar uma base de dados vazia.
11. No **Browser**, expanda o **Servidor**. Expanda o servidor que criou e veja a base de dados **mypgsqldb** abaixo do mesmo.
 ![pgAdmin – Criar – Base de Dados](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>Limpar recursos
Limpe todos os recursos que criou no guia de introdução ao eliminar o [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> Serão criados outros guias de introdução nesta coleção através deste guia. Se pretender continuar a trabalhar com os guias de introdução subsequentes, não limpe os recursos criados neste guia de introdução. Se não pretender continuar, utilize os passos seguintes para eliminar todos os recursos criados por este guia de introdução no portal do Azure.

1.  No menu esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique em **myresourcegroup**.
2.  Na página do grupo de recursos, clique em **Eliminar**, escreva **myresourcegroup** na caixa de texto e, em seguida, clique em Eliminar.

Se apenas pretender eliminar o servidor recentemente criado:
1.  No menu esquerdo o portal do Azure, clique em Servidores PostgreSQL e procure o servidor que acabou de criar
2.  Na página Descrição geral, clique no botão Eliminar no painel superior ![Base de Dados do Azure para o PostgreSQL – Eliminar o servidor](./media/quickstart-create-database-portal/12-delete.png)
3.  Confirme o nome do servidor que pretende eliminar e veja as bases de dados incluídas que são afetadas. Escreva **mypgserver-20170401** na caixa de texto e, em seguida, clique em Eliminar.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a base de dados com Exportar e Importar](./howto-migrate-using-export-and-import.md)

