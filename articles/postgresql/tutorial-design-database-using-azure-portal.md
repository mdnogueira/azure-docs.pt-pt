---
title: "Conceber a sua primeira base de dados do Azure para PostgreSQL através do portal do Azure | Microsoft Docs"
description: Este tutorial mostra como conceber a sua primeira base de dados do Azure para PostgreSQL no portal do Azure.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: tutorial, mvc
ms.topic: tutorial
ms.date: 11/03/2017
ms.openlocfilehash: 1a210f813319a4f21c7c246002c968b8093f8a4e
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="design-your-first-azure-database-for-postgresql-using-the-azure-portal"></a>Conceber a sua primeira base de dados do Azure para PostgreSQL no portal do Azure

A Base de Dados do Azure para o PostgreSQL é um serviço gerido que lhe permite executar, gerir e dimensionar as bases de dados de alta disponibilidade do PostgreSQL na cloud. No portal do Azure, pode facilmente gerir o seu servidor e crie uma base de dados.

Neste tutorial, utilize o portal do Azure para saber como:
> [!div class="checklist"]
> * Criar uma Base de Dados do Azure para o servidor PostgreSQL
> * Configurar a firewall do servidor
> * Utilize [ **psql** ](https://www.postgresql.org/docs/9.6/static/app-psql.html) utilitário para criar uma base de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

## <a name="prerequisites"></a>Pré-requisitos
Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Criar uma Base de Dados do Azure para o PostgreSQL

É criada uma Base de Dados do Azure para o servidor PostgreSQL com um conjunto definido de [recursos de armazenamento e computação](./concepts-compute-unit-and-storage.md). O servidor é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

Siga estes passos para criar uma Base de Dados do Azure para o servidor PostgreSQL:
1.  Clique em de **+ novo** botão encontrado no canto superior esquerdo do portal do Azure.
2.  Selecione **Bases de Dados**, na página **Nova**, e selecione **Base de Dados do Azure para o PostgreSQL**, na página **Bases de Dados**.
 ![Base de Dados do Azure para o PostgreSQL – Criar a base de dados](./media/tutorial-design-database-using-azure-portal/1-create-database.png)

3.  Preencha os novos detalhes do servidor com as informações seguintes, conforme mostrado na imagem anterior:
    - Nome do servidor: **mypgserver 20170401** (o nome de um servidor é mapeado para o nome DNS e, por conseguinte, é necessário para ser globalmente exclusivo) 
    - Subscrição: se tiver várias subscrições, escolha a subscrição adequada na qual o recurso existe ou é cobrado.
    - Grupo de recursos: **myresourcegroup**
    - Início de sessão e palavra-passe de administrador do servidor à sua escolha
    - Localização
    - Versão do PostgreSQL

  > [!IMPORTANT]
  > O início de sessão de administrador de servidor e a palavra-passe que especificar aqui são necessários para iniciar sessão no servidor e as respetivas bases de dados mais à frente neste guia de introdução. Lembre-se ou grave estas informações para utilização posterior.

4.  Clique em **Escalão de preço** para especificar o escalão de serviço e o nível de desempenho para a nova base de dados. Para este guia de introdução, selecionar **básico** camada **50 unidades computação** e **50 GB** de armazenamento incluído.
 ![Base de Dados do Azure para o PostgreSQL – escolher a camada de serviços](./media/tutorial-design-database-using-azure-portal/2-service-tier.png)
5.  Clique em **OK**.
6.  Clique em **Criar** para aprovisionar o servidor. O aprovisionamento demora alguns minutos.

  > [!TIP]
  > Marque a opção **Afixar ao dashboard** para poder controlar de forma fácil as implementações.

7.  Na barra de ferramentas, clique em **Notificações** para monitorizar o processo de implementação.
 ![Base de Dados do Azure para o PostgreSQL – Ver as notificações](./media/tutorial-design-database-using-azure-portal/3-notifications.png)
   
  Por predefinição, é criada a base de dados **postgres** no servidor. A base de dados [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) é uma base de dados predefinida que se destina a ser utilizada por utilizadores, utilitários e aplicações de terceiros. 

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor

A base de dados do Azure para o serviço de PostgreSQL utiliza uma firewall ao nível do servidor. Por predefinição, este firewall impede que todas as aplicações externas e ferramentas de ligar ao servidor e quaisquer bases de dados no servidor, a menos que é criada uma regra de firewall para abrir a firewall para um intervalo de endereço IP específico. 

1.  Depois de concluída a implementação, clique em **Todos os Recursos**, no menu esquerdo, e escreva o nome **mypgserver 20170401** para procurar o servidor recentemente criado. Clique no nome do servidor listado no resultado da pesquisa. É apresentada a página **Descrição Geral** do servidor, que fornece opções para configuração adicional.
 
 ![Base de Dados do Azure para o PostgreSQL – Procurar o servidor ](./media/tutorial-design-database-using-azure-portal/4-locate.png)

2.  Na página do servidor, selecione **Segurança da ligação**. 
3.  Clique na caixa de texto em **Nome da Regra** e adicione uma nova regra de firewall para colocar na lista de permissões o intervalo IP para conectividade. Para este tutorial, vamos permitir que todos os IPs escrevendo na **nome da regra = AllowAllIps**, **IP inicial = 0.0.0.0** e **final IP = 255.255.255.255** e, em seguida, clique em **guardar**. Pode definir uma regra de firewall específicas que abrange um intervalo IP mais pequeno para efetuar a ligação da sua rede.
 
 ![Base de Dados do Azure para o PostgreSQL – Criar a Regra de Firewall](./media/tutorial-design-database-using-azure-portal/5-firewall-2.png)

4.  Clique em **guardar** e, em seguida, clique em de **X** para fechar o **segurança ligações** página.

  > [!NOTE]
  > O servidor PostgreSQL do Azure comunica através da porta 5432. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 5432 poderá não ser permitido pela firewall da rede. Se assim for, não é possível ligar ao servidor da SQL Database do Azure, a menos que o departamento de TI abre porta 5432.
  >


## <a name="get-the-connection-information"></a>Obter as informações da ligação

Quando criou a base de dados do Azure para o servidor de PostgreSQL, a predefinição **postgres** também foi criada a base de dados. Para ligar ao servidor da base de dados, terá de fornecer as credenciais de acesso e as informações de anfitrião.

1. No menu da esquerda no portal do Azure, clique em **todos os recursos** e procure o servidor que acabou de criar **mypgserver 20170401**.

  ![Base de Dados do Azure para o PostgreSQL – Procurar o servidor ](./media/tutorial-design-database-using-azure-portal/4-locate.png)

3. Clique no nome do servidor **mypgserver 20170401**.

4. Selecione a página **Descrição Geral** do servidor. Anote o **Nome do servidor** e **Nome de início de sessão de administrador do servidor**.

 ![Base de Dados do Azure para o PostgreSQL – Início de Sessão de Administrador do Servidor](./media/tutorial-design-database-using-azure-portal/6-server-name.png)


## <a name="connect-to-postgresql-database-using-psql-in-cloud-shell"></a>Ligar-se à base de dados do PostgreSQL com o psql no Cloud Shell

Vamos agora utilizar o [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) utilitário de linha de comandos para ligar à base de dados do Azure para o servidor de PostgreSQL. 
1. Inicie o Azure Cloud Shell através do ícone de terminal no painel de navegação superior.

   ![Base de Dados de Azure para o PostgreSQL – ícone do terminal do Azure Cloud Shell](./media/tutorial-design-database-using-azure-portal/7-cloud-shell.png)

2. O Azure Cloud Shell é aberto no browser, onde poderá escrever os comandos bash.

   ![Base de Dados do Azure para o PostgreSQL – Linha de Comandos Bash do Azure Shell](./media/tutorial-design-database-using-azure-portal/8-bash.png)

3. Na linha de comandos do Cloud Shell, estabeleça ligação à Base de Dados do Azure para o servidor PostgreSQL com os comandos psql. O formato seguinte é utilizado para estabelecer ligação a uma Base de Dados do Azure para o servidor PostgreSQL com o utilitário [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
   ```bash
   psql --host=<myserver> --port=<port> --username=<server admin login> --dbname=<database name>
   ```

   Por exemplo, o comando seguinte estabelece ligação à base de dados predefinida, denominada **postgres**, no servidor PostgreSQL **mypgserver 20170401.postgres.database.azure.com** com as credenciais de acesso. Quando lhe for pedido, introduza a palavra-passe de administrador do servidor.

   ```bash
   psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
   ```

## <a name="create-a-new-database"></a>Criar uma nova base de dados
Assim que estiver ligado ao servidor, crie uma base de dados vazia na linha de comandos.
```bash
CREATE DATABASE mypgsqldb;
```

Na linha de comandos, execute o comando seguinte para mudar a ligação para a base de dados **mypgsqldb** recentemente criada.
```bash
\c mypgsqldb
```
## <a name="create-tables-in-the-database"></a>Criar tabelas na base de dados
Agora que sabe como ligar à base de dados do Azure para PostgreSQL, pode concluir algumas tarefas básicas:

Em primeiro lugar, crie uma tabela e carregá-lo com alguns dados. Vamos criar uma tabela que controla as informações de inventário com este código SQL:
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

Agora, pode ver a tabela criada recentemente na lista de tabelas, escrevendo:
```sql
\dt
```

## <a name="load-data-into-the-tables"></a>Carregar dados para as tabelas
Agora que tem uma tabela, inserir alguns dados na mesma. A janela de linha de comandos aberta, execute a seguinte consulta para inserir alguns linhas de dados.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Tem agora duas linhas de dados de exemplo para a tabela de inventário que criou anteriormente.

## <a name="query-and-update-the-data-in-the-tables"></a>Consultar e atualizar os dados nas tabelas
Execute a seguinte consulta para obter informações da tabela de base de dados de inventário. 
```sql
SELECT * FROM inventory;
```

Também pode atualizar os dados na tabela.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Pode ver valores atualizados ao obter os dados.
```sql
SELECT * FROM inventory;
```

## <a name="restore-data-to-a-previous-point-in-time"></a>Restaurar dados para um ponto anterior no tempo
Imagine que tenha eliminado acidentalmente nesta tabela. Esta situação é algo que facilmente não consegue recuperar. Base de dados do Azure para PostgreSQL permite-lhe voltar atrás para qualquer ponto no tempo (em cima últimos sete dias (básico) e 35 dias (Standard)) e restaurar este ponto no tempo para um novo servidor. Pode utilizar este servidor novo para recuperar os dados eliminados. Os passos seguintes restauro o **mypgserver 20170401** servidor para um ponto antes da tabela de inventário foi adicionada.

1.  Na base de dados do Azure para PostgreSQL **descrição geral** página do seu servidor, clique em **restaurar** na barra de ferramentas. O **restaurar** é aberta a página.
  ![Portal do Azure – opções de formulário de restauro](./media/tutorial-design-database-using-azure-portal/9-azure-portal-restore.png)
2.  Preencha o **restaurar** formulário com as informações necessárias:

  ![Portal do Azure – opções de formulário de restauro](./media/tutorial-design-database-using-azure-portal/10-azure-portal-restore.png)
  - **Ponto de restauro**: selecione um ponto no tempo que ocorre antes do servidor foi alterado
  - **Servidor de destino**: forneça um novo nome de servidor que pretende restaurar para
  - **Localização**: não é possível selecionar a região, por predefinição é igual ao servidor de origem
  - **Escalão de preço**: não é possível alterar este valor ao restaurar um servidor. É igual ao servidor de origem. 
3.  Clique em **OK** [restaurar o servidor para um ponto no tempo](./howto-restore-server-portal.md) antes da tabela foi eliminada. Restaurar um servidor para um outro ponto no tempo cria um novo servidor duplicado como o servidor original a partir do ponto no tempo que especificar, desde que esteja dentro do período de retenção para sua [camada de serviço](./concepts-service-tiers.md).

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a utilizar o portal do Azure e outros utilitários para:
> [!div class="checklist"]
> * Criar uma Base de Dados do Azure para o servidor PostgreSQL
> * Configurar a firewall do servidor
> * Utilize [ **psql** ](https://www.postgresql.org/docs/9.6/static/app-psql.html) utilitário para criar uma base de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

Em seguida, para saber como utilizar a CLI do Azure para realizar tarefas semelhantes, reveja este tutorial: [conceber a sua primeira base de dados do Azure para PostgreSQL utilizando a CLI do Azure](tutorial-design-database-using-azure-cli.md)
