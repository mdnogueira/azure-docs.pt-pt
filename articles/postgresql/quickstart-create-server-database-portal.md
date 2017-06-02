---
title: 'Portal do Azure: criar uma Base de Dados do Azure para o PostgreSQL | Microsoft Docs'
description: "Guia de introdução para criar e gerir a Base de Dados do Azure para o servidor PostgreSQL com a interface de utilizador do portal do Azure."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: quick start create, mvc
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c67ada15c11b81021ff5e6f6e5edc5cb530ece98
ms.contentlocale: pt-pt
ms.lasthandoff: 05/10/2017

---

# <a name="create-an-azure-database-for-postgresql-in-the-azure-portal"></a>Criar uma Base de Dados do Azure para o PostgreSQL no portal do Azure

A Base de Dados do Azure para o PostgreSQL é um serviço gerido que lhe permite executar, gerir e dimensionar as bases de dados de alta disponibilidade do PostgreSQL na cloud. Este guia de introdução mostra-lhe como criar uma Base de Dados do Azure para o servidor PostgreSQL através do portal do Azure.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Criar uma Base de Dados do Azure para o PostgreSQL

É criada uma Base de Dados do Azure para o servidor PostgreSQL com um conjunto definido de [recursos de armazenamento e computação](./concepts-compute-unit-and-storage.md). O servidor é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

Siga estes passos para criar uma Base de Dados do Azure para o servidor PostgreSQL:
1.    Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.
2.    Selecione **Bases de Dados**, na página **Nova**, e selecione **Base de Dados do Azure para o PostgreSQL**, na página **Bases de Dados**.
 ![Base de Dados do Azure para o PostgreSQL – Criar a base de dados](./media/quickstart-create-database-portal/1-create-database.png)

3.    Preencha os novos detalhes do servidor com as informações seguintes, conforme mostrado na imagem anterior:
    - Nome do servidor: **mypgserver 20170401** (o nome de um servidor é mapeado para o nome DNS e, por conseguinte, é necessário para ser globalmente exclusivo) 
    - Subscrição: se tiver várias subscrições, escolha a subscrição adequada na qual o recurso existe ou é cobrado.
    - Grupo de recursos: **myresourcegroup**
    - Início de sessão e palavra-passe de administrador do servidor à sua escolha
    - Localização
    - Versão do PostgreSQL

  > [!IMPORTANT]
  > O início de sessão e a palavra-passe de administrador de servidor que especificar aqui serão necessários para iniciar sessão no servidor e nas respetivas bases de dados mais tarde neste guia de introdução. Lembre-se ou grave estas informações para utilização posterior.

4.    Clique em **Escalão de preço** para especificar o escalão de serviço e o nível de desempenho para a nova base de dados. Para este guia de introdução, selecione Camada **Básica**, **50 Unidades de Computação** e **50 GB** de armazenamento incluído.
 ![Base de Dados do Azure para o PostgreSQL – escolher a camada de serviços](./media/quickstart-create-database-portal/2-service-tier.png)
5.    Clique em **OK**.
6.    Clique em **Criar** para aprovisionar o servidor. O aprovisionamento demora alguns minutos.

  > [!TIP]
  > Marque a opção **Afixar ao dashboard** para poder controlar de forma fácil as implementações.

7.    Na barra de ferramentas, clique em **Notificações** para monitorizar o processo de implementação.
 ![Base de Dados do Azure para o PostgreSQL – Ver as notificações](./media/quickstart-create-database-portal/3-notifications.png)
   
  Por predefinição, é criada a base de dados **postgres** no servidor. A base de dados [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) é uma base de dados predefinida que se destina a ser utilizada por utilizadores, utilitários e aplicações de terceiros. 

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor

A Base de Dados do Azure para o serviço PostgreSQL cria uma firewall ao nível do servidor. Esta firewall impede que as aplicações e ferramentas externas estabeleçam ligação ao servidor e a quaisquer bases de dados no servidor, a menos que seja criada uma regra de firewall para abrir a firewall aos endereços IP específicos. 

1.    Depois de concluída a implementação, clique em **Todos os Recursos**, no menu esquerdo, e escreva o nome **mypgserver 20170401** para procurar o servidor recentemente criado. Clique no nome do servidor listado no resultado da pesquisa. É apresentada a página **Descrição Geral** do servidor, que fornece opções para configuração adicional.
 
 ![Base de Dados do Azure para o PostgreSQL – Procurar o servidor ](./media/quickstart-create-database-portal/4-locate.png)

2.    No painel do servidor, selecione **Segurança da Ligação**. 
3.    Clique na caixa de texto em **Nome da Regra** e adicione uma nova regra de firewall para colocar na lista de permissões o intervalo IP para conectividade. Neste guia de introdução, vamos permitir todos os IPs. Para tal, escreva **Nome da Regra = AllowAllIps**, **IP Inicial = 0.0.0.0** e **IP Final = 255.255.255.255** e, em seguida, clique em **Guardar**. Pode definir uma regra de firewall que abrange um intervalo IP para conseguir estabelecer ligação a partir da sua rede.

 ![Base de Dados do Azure para o PostgreSQL – Criar a Regra de Firewall](./media/quickstart-create-database-portal/5-firewall-2.png)

4.    Clique em **Guardar** e, em seguida, no **X** para fechar a página **Segurança das Ligações**.

  > [!NOTE]
  > O servidor PostgreSQL do Azure comunica através da porta 5432. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 5432 poderá não ser permitido pela firewall da rede. Se assim for, não poderá ligar ao servidor da Base de Dados SQL do Azure, a menos que o departamento de TI abra a porta 5432.
  >

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Quando criámos a nossa Base de Dados do Azure para o servidor PostgreSQL, também é criada a base de dados **postgres** predefinida. Para ligar ao servidor da base de dados, terá de fornecer as credenciais de acesso e as informações de anfitrião.

1. No menu esquerdo do portal do Azure, clique em **Todos os recursos** e procure o servidor que acabou de criar **mypgserver-20170401**.

  ![Base de Dados do Azure para o PostgreSQL – Procurar o servidor ](./media/quickstart-create-database-portal/4-locate.png)

2. Clique no nome do servidor **mypgserver 20170401**.
3. Selecione a página **Descrição Geral** do servidor. Anote o **Nome do servidor** e **Nome de início de sessão de administrador do servidor**.

 ![Base de Dados do Azure para o PostgreSQL – Início de Sessão de Administrador do Servidor](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-postgresql-database-using-psql-in-cloud-shell"></a>Ligar-se à base de dados do PostgreSQL com o psql no Cloud Shell

Agora, vamos utilizar o utilitário da linha de comandos psql para ligar à Base de Dados do Azure para o servidor PostgreSQL. 
1. Inicie o Azure Cloud Shell através do ícone de terminal no painel de navegação superior.

   ![Base de Dados de Azure para o PostgreSQL – ícone do terminal do Azure Cloud Shell](./media/quickstart-create-database-portal/7-cloud-console.png)

2. O Azure Cloud Shell é aberto no browser, onde poderá escrever os comandos bash.

   ![Base de Dados do Azure para o PostgreSQL – Linha de Comandos Bash do Azure Shell](./media/quickstart-create-database-portal/8-bash.png)

3. Na linha de comandos do Cloud Shell, estabeleça ligação à Base de Dados do Azure para o servidor PostgreSQL com os comandos psql. O formato seguinte é utilizado para estabelecer ligação a uma Base de Dados do Azure para o servidor PostgreSQL com o utilitário [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
   ```bash
   psql --host=<myserver> --port=<port> --username=<server admin login> --dbname=<database name>
   ```

   Por exemplo, o comando seguinte estabelece ligação à base de dados predefinida, denominada **postgres**, no servidor PostgreSQL **mypgserver 20170401.postgres.database.azure.com** com as credenciais de acesso. Quando lhe for pedido, introduza a palavra-passe de administrador do servidor.

   ```bash
   psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
   ```
4.  Assim que estiver ligado ao servidor, crie uma base de dados vazia na linha de comandos.
```bash
CREATE DATABASE mypgsqldb;
```

5.  Na linha de comandos, execute o comando seguinte para mudar a ligação para a base de dados **mypgsqldb** recentemente criada.
```bash
\c mypgsqldb
```

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Ligar-se à base de dados do PostgreSQL com a pgAdmin

Para ligar ao servidor PostgreSQL do Azure com a ferramenta _pgAdmin_ da GUI
1.    Inicie a aplicação _pgAdmin_ no computador cliente. Pode instalar a _pgAdmin_ a partir do site http://www.pgadmin.org/.
2.    Escolha **Adicionar Novo Servidor**, no menu **Ligações Rápidas**.
3.    Na caixa de diálogo **Criar – Servidor**, separador **Geral**, introduza um Nome amigável exclusivo para o servidor, tal como **Servidor PostgreSQL do Azure**.
![ferramenta pgAdmin - criar - servidor](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)
4.    Na caixa de diálogo **Criar – Servidor**, separador **Ligação**, utilize as definições conforme especificado e clique em **Guardar**.
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
5.    Clique em **Guardar**.
6.    No painel esquerdo do Browser, expanda os **Grupos de Servidores**. Selecione o **Servidor PostgreSQL do Azure**.
7.  Escolha o **Servidor** ao qual se ligou e, em seguida, escolha **Bases de Dados** abaixo do mesmo. 
8.    Clique com o botão direito do rato em **Bases de Dados** para Criar uma Base de Dados.
9.    Escolha um nome de base de dados, **mypgsqldb**, e o seu proprietário como o início de sessão de administrador do servidor **mylogin**.
10. Clique em **Guardar** para criar uma base de dados vazia.
11. No **Browser**, expanda o **Servidor**. Expanda o servidor que criou e veja a base de dados **mypgsqldb** abaixo do mesmo.
 ![pgAdmin – Criar – Base de Dados](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>Limpar recursos
Limpe todos os recursos que criou no guia de introdução ao eliminar o [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> Serão criados outros guias de introdução nesta coleção através deste guia. Se pretender continuar a trabalhar com os guias de introdução subsequentes, não limpe os recursos criados neste guia de introdução. Se não pretender continuar, utilize os passos seguintes para eliminar todos os recursos criados por este guia de introdução no portal do Azure.

1.    No menu esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique em **myresourcegroup**.
2.    Na página do grupo de recursos, clique em **Eliminar**, escreva **myresourcegroup** na caixa de texto e, em seguida, clique em Eliminar.

Se apenas pretender eliminar o servidor recentemente criado:
1.    No menu esquerdo o portal do Azure, clique em Servidores PostgreSQL e procure o servidor que acabou de criar
2.    Na página Descrição geral, clique no botão Eliminar no painel superior ![Base de Dados do Azure para o PostgreSQL – Eliminar o servidor](./media/quickstart-create-database-portal/12-delete.png)
3.    Confirme o nome do servidor que pretende eliminar e veja as bases de dados incluídas que são afetadas. Escreva **mypgserver-20170401** na caixa de texto e, em seguida, clique em Eliminar.

## <a name="next-steps"></a>Passos seguintes
- Migrar a base de dados com [Exportar e Importar](./howto-migrate-using-export-and-import.md) ou [Cópia de Segurança e Restauro](./howto-migrate-using-dump-and-restore.md).
- Para criar a Base de Dados do Azure para o servidor PostgreSQL com a CLI do Azure, veja [Create PostgreSQL server - CLI (Criar servidor PostgreSQL – CLI)](./quickstart-create-server-database-azure-cli.md).
- Para obter uma descrição técnica, veja [Acerca da Base de Dados Azure para o serviço PostgreSQL](./overview.md).
