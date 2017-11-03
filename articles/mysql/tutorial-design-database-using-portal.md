---
title: Conceber a sua primeira base de dados do Azure para a base de dados MySQL - Portal do Azure | Microsoft Docs
description: Este tutorial explica como criar e gerir a base de dados do Azure para o servidor de MySQL e base de dados utilizando o Portal do Azure.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.topic: tutorial
ms.date: 06/06/2017
ms.custom: mvc
ms.openlocfilehash: 92ae809646cb956b0b7cb71cf2a21610bb2d610f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="design-your-first-azure-database-for-mysql-database"></a>Conceber a sua primeira base de dados do Azure para a base de dados MySQL
A Base de Dados do Azure para MySQL é um serviço gerido que lhe permite executar, gerir e dimensionar as bases de dados MySQL de alta disponibilidade na cloud. No portal do Azure, pode facilmente gerir o seu servidor e crie uma base de dados.

Neste tutorial, utilize o portal do Azure para saber como:

> [!div class="checklist"]
> * Criar uma base de dados do Azure para MySQL
> * Configurar a firewall do servidor
> * Utilizar a ferramenta de linha de comandos de mysql ao criar uma base de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Abra o browser Favoritos e visite o [portal do Microsoft Azure](https://portal.azure.com/). Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.

## <a name="create-an-azure-database-for-mysql-server"></a>Criar uma Base de Dados do Azure para o servidor MySQL
É criada uma Base de Dados do Azure para o servidor MySQL com um conjunto definido de [recursos de armazenamento e computação](./concepts-compute-unit-and-storage.md). O servidor é criado dentro de um [grupo de recursos do Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).

1. Navegue para **bases de dados** > **base de dados do Azure para MySQL**. Se não é possível localizar o servidor de MySQL em **bases de dados** categoria, clique em **ver todos os** para mostrar todos os serviços de base de dados disponíveis. Também pode escrever **base de dados do Azure para MySQL** na caixa de pesquisa para localizar rapidamente o serviço.
![Navegue até 1 de 2 para MySQL](./media/tutorial-design-database-using-portal/2_1-Navigate-to-MySQL.png)

2. Clique em **base de dados do Azure para MySQL** mosaico e, em seguida, clique em **criar**.

No nosso exemplo, preencha o formulário de MySQL com as seguintes informações da base de dados do Azure:

| **Definição** | **Valor sugerido** | **Descrição do Campo** |
|---|---|---|
| *Nome do servidor* | myserver4demo  | O nome do servidor deve ser globalmente exclusivo. |
| *Subscrição* | mysubscription | Selecione a subscrição na lista pendente. |
| *Grupo de recursos* | myResourceGroup | Crie um grupo de recursos ou utilize um existente. |
| *Início de sessão de administrador do servidor* | myadmin | Nome da conta de administrador a configuração. |
| *Palavra-passe* |  | Defina uma palavra-passe forte para a conta de administrador. |
| *Confirmar palavra-passe* |  | Confirme a palavra-passe da conta de administrador. |
| *Localização* |  | Selecione uma região disponível. |
| *Versão* | 5.7 | Escolha a versão mais recente. |
| *Configurar o desempenho* | Computação de 50 básico, unidades, 50 GB  | Escolha **Escalão de preço**, **Unidades de Computação**, **Armazenamento (GB)** e clique em **OK**. |
| *Afixar ao Dashboard* | Marcar | Recomendamos que marque esta caixa para encontrar facilmente o servidor posteriormente |
Em seguida, clique em **Criar**. Num ou dois minutos, uma nova Base de Dados do Azure para o servidor MySQL estará em execução na cloud. Pode clicar em **notificações** botão na barra de ferramentas para monitorizar o processo de implementação.

## <a name="configure-firewall"></a>Configurar a firewall
Bases de dados do Azure para MySQL estão protegidos por uma firewall. Por predefinição, todas as ligações ao servidor e as bases de dados no servidor são rejeitadas. Antes de ligar à base de dados do Azure para MySQL pela primeira vez, configure a firewall para adicionar o endereço IP de rede pública do computador cliente (ou intervalo de endereços IP).

1. Clique no servidor recém-criado e, em seguida, clique em **segurança de ligação**.
   ![Segurança de ligação de 1 de 3](./media/tutorial-design-database-using-portal/3_1-Connection-security.png)
2. Pode **Adicionar IP do meu**, ou configurar regras de firewall aqui. Não se esqueça de clicar em **Guardar** depois de criar a regras.
Agora pode ligar ao servidor utilizando a ferramenta de linha de comandos mysql ou a ferramenta de MySQL Workbench GUI.

> [!TIP]
> Base de dados do Azure para o servidor de MySQL comunica através da porta 3306. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 3306 poderá não ser permitido pela firewall da rede. Se assim for, não é possível ligar ao servidor do Azure MySQL, a menos que o departamento de TI abre porta 3306.

## <a name="get-connection-information"></a>Obter informações da ligação
Obter completamente qualificado **nome do servidor** e **nome de início de sessão de administração do servidor** da base de dados do Azure para o servidor de MySQL do portal do Azure. Utilize o nome de servidor completamente qualificado para ligar ao servidor utilizando a ferramenta de linha de comandos mysql. 

1. No [portal do Azure](https://portal.azure.com/), clique em **todos os recursos** no menu da esquerda, escreva o nome e procure a base de dados do Azure para o servidor de MySQL. Selecione o nome do servidor para ver os detalhes.

2. Com as definições, clique em **propriedades**. Tome nota **nome do servidor** e **nome de início de sessão de administração do servidor**. Podem clicar no botão Copiar junto a cada campo para copiar para a área de transferência.
   ![Propriedades do servidor de 4-2](./media/tutorial-design-database-using-portal/4_2-server-properties.png)

Neste exemplo, o nome do servidor é *myserver4demo.mysql.database.azure.com* e o início de sessão de administrador do servidor é *myadmin@myserver4demo*.

## <a name="connect-to-the-server-using-mysql"></a>Ligar ao servidor utilizando o mysql
Utilize a [ferramenta de linha de comandos do mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) para estabelecer uma ligação à base de dados do Azure para o servidor MySQL. Pode executar a ferramenta de linha de comandos de mysql a partir da Shell de nuvem do Azure no browser ou a partir do seu próprio computador utilizando as ferramentas de mysql instaladas localmente. Para iniciar a Shell de nuvem do Azure, clique em de `Try It` botão num bloco de código neste artigo, ou visitar o portal do Azure e clique em de `>_` ícone na barra de ferramentas direita superior. 

Escreva o comando para estabelecer a ligação:
```azurecli-interactive
mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
```

## <a name="create-a-blank-database"></a>Criar uma base de dados vazia
Assim que o se estiver ligado ao servidor, crie uma base de dados em branco para trabalhar com.
```sql
CREATE DATABASE mysampledb;
```

Na linha de comandos, execute o seguinte comando para mudar a ligação a este criado recentemente a base de dados:
```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Criar tabelas na base de dados
Agora que sabe como ligar à base de dados do Azure para a base de dados MySQL, iremos pode passam como concluir algumas tarefas básicas.

Em primeiro lugar, iremos criar uma tabela e carregue-a com alguns dados. Vamos criar uma tabela que armazena informações de inventário.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Carregar dados para as tabelas
Agora que temos uma tabela, iremos pode inserir alguns dados para a mesma. A janela de linha de comandos aberta, execute a seguinte consulta para inserir alguns linhas de dados.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Tem agora duas linhas de dados de exemplo para a tabela que criou anteriormente.

## <a name="query-and-update-the-data-in-the-tables"></a>Consultar e atualizar os dados nas tabelas
Execute a seguinte consulta para obter informações da tabela da base de dados.
```sql
SELECT * FROM inventory;
```

Também pode atualizar os dados nas tabelas.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A linha obtém atualizada em conformidade ao obter dados.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restaurar uma base de dados para um ponto anterior no tempo
Imagine que tem sejam eliminados acidentalmente uma tabela de base de dados importantes e não é possível recuperar os dados facilmente. Base de dados do Azure para MySQL permite restaurar o servidor para um ponto no tempo, criar uma cópia das bases de dados no novo servidor. Pode utilizar este servidor novo para recuperar os dados eliminados. Os seguintes passos restaurar o servidor de exemplo para um ponto antes da tabela foi adicionada.

1. No portal do Azure, localize a base de dados do Azure para MySQL. No **descrição geral** página, clique em **restaurar** na barra de ferramentas. Abre a página de restauro.

   ![10-1 restaurar uma base de dados](./media/tutorial-design-database-using-portal/10_1-restore-a-db.png)

2. Preencha o **restaurar** formulário com as informações necessárias.
   
   ![formulário de restauro de 10-2](./media/tutorial-design-database-using-portal/10_2-restore-form.png)
   
   - **Ponto de restauro**: selecione um ponto no tempo que pretende restaurar, durante o período de tempo listado. Certifique-se converter o fuso UTC.
   - **Restaurar para o novo servidor**: forneça um novo nome de servidor que pretende restaurar.
   - **Localização**: A região é igual ao servidor de origem e não pode ser alterada.
   - **Escalão de preço**: O escalão de preço é o mesmo que o servidor de origem e não pode ser alterado.
   
3. Clique em **OK** para restaurar o servidor para [restaurar para um ponto no tempo](./howto-restore-server-portal.md) antes da tabela foi eliminada. Restaurar um servidor cria uma nova cópia do servidor, a partir do ponto no tempo que especificar. 

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, utilize a como de portal para adquiridas do Azure:

> [!div class="checklist"]
> * Criar uma base de dados do Azure para MySQL
> * Configurar a firewall do servidor
> * Utilizar a ferramenta de linha de comandos de mysql ao criar uma base de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

> [!div class="nextstepaction"]
> [Como ligar aplicações Azure base de dados MySQL](./howto-connection-string.md)
