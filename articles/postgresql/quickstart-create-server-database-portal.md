---
title: 'Portal do Azure: criar uma base de dados do Azure para o servidor de PostgreSQL | Microsoft Docs'
description: "Guia de início rápido para criar e gerir uma base de dados do Azure para o servidor de PostgreSQL utilizando a interface de utilizador do portal do Azure."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 08/10/2017
ms.openlocfilehash: 3a76e816f9b1fa484789f548899d7e8e7043febb
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="create-an-azure-database-for-postgresql-server-in-the-azure-portal"></a>Criar uma base de dados do Azure para o servidor de PostgreSQL no portal do Azure

Base de dados do Azure para PostgreSQL é um serviço gerido que utilizar para executar, gerir e escala elevadas PostgreSQL bases de dados na nuvem. Este guia de introdução mostra como criar uma base de dados do Azure para o servidor de PostgreSQL no cerca de cinco minutos, utilizando o portal do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Abra o browser e aceda ao [portal](https://portal.azure.com/). Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar uma Base de Dados do Azure para o servidor PostgreSQL

É criada uma Base de Dados do Azure para o servidor PostgreSQL com um conjunto definido de [recursos de armazenamento e computação](./concepts-compute-unit-and-storage.md). O servidor é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

Para criar uma base de dados do Azure para o servidor de PostgreSQL, siga os passos seguintes:
1. Selecione o botão (+) **Novo**, no canto superior esquerdo do portal.

2. Selecione **bases de dados** > **base de dados do Azure para PostgreSQL**.

    ![A opção "Azure base de dados para PostgreSQL"](./media/quickstart-create-database-portal/1-create-database.png)

3. Preencha os novos detalhes do servidor com as informações seguintes, conforme mostrado na imagem anterior:

    Definição|Valor sugerido|Descrição
    ---|---|---
    Nome do servidor |*mypgserver-20170401*|Um nome exclusivo que identifica a sua base de dados do Azure para o servidor de PostgreSQL. O nome de domínio *postgres.database.azure.com* é acrescentado ao nome do servidor que fornecer. O servidor pode conter apenas letras minúsculas, números e o caráter de hífen (-). Tem de conter, pelo menos, 3 e 63 carateres.
    Subscrição|A sua subscrição|A subscrição do Azure que pretende utilizar para o servidor. Se tiver várias subscrições, selecione a subscrição na qual será cobrado para o recurso.
    Grupo de recursos|*myresourcegroup*| Um nome de grupo de recursos novo ou uma existente da sua subscrição.
    Início de sessão de administrador do servidor |*mylogin*| Sua própria conta de início de sessão para utilizar quando ligar ao servidor. O nome de início de sessão de administrador não pode ser **azure_superuser,** **azure_pg_admin,** **admin,** **administrador,** **raiz,** **convidado,** ou **público.** Não é possível iniciar com **pg_**.
    Palavra-passe |A sua escolha | Uma palavra-passe nova para a conta de administrador do servidor. Tem de conter entre 8 e 128 carateres. A palavra-passe tem de conter carateres de três das seguintes categorias: em maiúsculas do inglês letras, letras minúsculas do inglês, números (0 a 9) e carateres não alfanuméricos (!, $, #, %, etc.).
    Localização|A região mais próxima aos seus utilizadores| A localização que esteja mais próxima para os seus utilizadores.
    Versão PostgreSQL|A versão mais recente| A versão mais recente, a menos que tenha requisitos específicos.
    Escalão de preço | **Básico**, **50 Unidades de Computação**, **50 GB** | A camada de serviços e o nível de desempenho da sua nova base de dados. Selecione **escalão de preço**. Em seguida, selecione o **básico** separador. Em seguida, selecione a extremidade esquerda do **computação unidades** controlo de deslize para ajustar o valor para a quantidade, pelo menos, disponível para este início rápido. Para guardar a seleção do escalão de preço, selecione **OK**. Para obter mais informações, veja a seguinte captura de ecrã. 
    Afixar ao dashboard | Marcar | Permite o controlo fácil do seu servidor na página do dashboard de front-do seu portal.

    > [!IMPORTANT]
    > O início de sessão de administrador de servidor e a palavra-passe que especificar aqui são necessários para iniciar sessão no servidor e as respetivas bases de dados mais à frente neste guia de introdução. Lembre-se ou grave estas informações para utilização posterior.

    ![O painel "Escalão de preço"](./media/quickstart-create-database-portal/2-service-tier.png)

4. Selecione **Criar** para aprovisionar o servidor. O aprovisionamento pode demorar até 20 minutos.

5. Na barra de ferramentas, selecione o **notificações** símbolo para monitorizar o processo de implementação.

    ![O painel de "Notificações"](./media/quickstart-create-database-portal/3-notifications.png)
   
  Por predefinição, um **postgres** base de dados é criado no seu servidor. O [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) base de dados é uma base de dados predefinido destina-se para utilização por utilizadores, utilitários e aplicações de terceiros. 

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor

Base de dados do Azure para PostgreSQL cria uma firewall ao nível do servidor. Impede que ferramentas e aplicações externas ao ligar ao servidor e quaisquer bases de dados no servidor, a menos que criar uma regra para abrir a firewall para endereços IP específicos. 

1. Depois de concluída a implementação, localize o seu servidor. Se necessário, pode procurá-lo. Por exemplo, no menu à esquerda, selecione **todos os recursos**. Escreva o nome do servidor, tais como exemplo, **mypgserver 20170401**, para procurar o seu servidor recentemente criado. Selecione o nome do servidor da lista de resultados de pesquisa. É apresentada a página **Descrição Geral** do servidor, que fornece opções para configuração adicional.
 
    ![Pesquisa de nome de servidor](./media/quickstart-create-database-portal/4-locate.png)

2. Na página do servidor, selecione **Segurança da ligação**.

    ![A definição "Segurança de ligação"](./media/quickstart-create-database-portal/5-firewall-2.png)

3. Sob o **regras de Firewall** no cabeçalho o **nome da regra** coluna, selecione o texto em branco caixa para começar a criar a regra de firewall. 

    Para este guia de introdução, vamos permita que todos os endereços IP para o servidor. Preencha a caixa de texto em cada coluna com os seguintes valores:

    Nome da regra | IP de início | IP de fim 
    ---|---|---
    AllowAllIps | 0.0.0.0 | 255.255.255.255

4. Na barra de ferramentas superior da página **Segurança da ligação**, selecione **Guardar**. Aguarde até que a notificação é apresentada a indicar que a atualização de segurança de ligação foi concluída com êxito antes de continuar.

    > [!NOTE]
    > As ligações à Base de Dados do Azure para o servidor PostgreSQL comunicam através da porta 5432. Se tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 5432 poderá não ser permitido pela firewall da sua rede. Se assim for, não é possível ligar ao seu servidor, a menos que o departamento de TI abre porta 5432.
    >

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Ao criar a base de dados do Azure para o servidor de PostgreSQL, uma base de dados predefinida denominada **postgres** é criado. Para ligar ao seu servidor de base de dados, terá das credenciais de início de sessão de nome e o administrador de servidor completo. Poderá ter apontado esses valores anteriormente no artigo Guia de Introdução. Se não, pode localizar facilmente o servidor de informações de nome e início de sessão no servidor **descrição geral** página no portal.

Abra a página **Descrição Geral** do servidor. Anote o **nome do servidor** e **nome de início de sessão de administração do servidor**. Coloque o cursor sobre cada campo e o símbolo de cópia aparece à direita do texto. Selecione o símbolo de cópia conforme necessário para copiar os valores.

 ![A página de "descrição geral" do servidor](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-the-postgresql-database-by-using-psql-in-cloud-shell"></a>Ligar à base de dados PostgreSQL utilizando psql na Shell de nuvem

Estão disponíveis diversas aplicações para ligar à sua Base de Dados do Azure para o servidor PostSQL. Vamos utilizar primeiro o utilitário de linha de comandos psql para ilustrar como ligar ao servidor. Pode utilizar um browser e a Shell de nuvem do Azure conforme descrito aqui sem a necessidade de instalar obrigatoriamente qualquer software adicional. Se tiver o utilitário de psql instalado localmente no seu computador, pode também ligar a partir do mesmo.

1. No painel de navegação superior, selecione o símbolo de terminal para abrir a Shell de nuvem.

   ![Símbolo de terminal Shell de nuvem do Azure](./media/quickstart-create-database-portal/7-cloud-console.png)

2. Shell de nuvem abre-se no seu browser, onde pode escrever comandos da shell Bash.

   ![Linha de Shell Bash em nuvem](./media/quickstart-create-database-portal/8-bash.png)

3. Na linha de comandos Shell de nuvem, liga a uma base de dados na base de dados do Azure para o servidor de PostgreSQL ao escrever a linha de comandos psql.

    Para ligar a uma base de dados do Azure para o servidor de PostgreSQL com o [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) utilitário, utilize o seguinte formato:
    ```bash
    psql --host=<yourserver> --port=<port> --username=<server admin login> --dbname=<database name>
    ```

    Por exemplo, o comando seguinte liga a um servidor de exemplo:

    ```bash
    psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
    ```

    parâmetro psql |Valor sugerido|Descrição
    ---|---|---
    --host | Nome do servidor | O valor de nome de servidor que utilizou quando criou a base de dados do Azure para o servidor de PostgreSQL anteriormente. Servidor nosso exemplo mostrado é **mypgserver 20170401.postgres.database.azure.com.** Utilize o nome de domínio completamente qualificado (**\*. postgres.database.azure.com**) conforme mostrado no exemplo. Se não se lembrar do nome do servidor, siga os passos na secção anterior para obter as informações da ligação. 
    --port | 5432 | A porta a utilizar ao ligar à base de dados do Azure para o servidor de PostgreSQL. 
    --username | Nome de início de sessão de administração do servidor |O servidor admin início de sessão nome de utilizador que especificou quando criou a base de dados do Azure para o servidor de PostgreSQL anteriormente. Não se lembra seu nome de utilizador, siga os passos na secção anterior para obter as informações de ligação. O formato é *username@servername*.
    --dbname | *postgres* | A predefinição, o nome de base de dados gerado pelo sistema que foi criada para a primeira ligação. Mais tarde, pode criar a sua própria base de dados.

    Depois de executar o comando psql com os seus próprios valores de parâmetro, é-lhe pedido que introduza a palavra-passe de administrador do servidor. Esta palavra-passe é o mesmo que forneceu quando criou o servidor. 

    parâmetro psql |Valor sugerido|Descrição
    ---|---|---
    palavra-passe | A palavra-passe de administrador | Os carateres de palavra-passe introduzidos não são apresentados na linha de bash. Depois de escrever todos os carateres, selecione o **Enter** chave para autenticar e ligar.

    Depois de ligar, o utilitário de psql apresenta um aviso de postgres onde escreve os comandos de sql. Na saída ligação inicial, poderá aparecer um aviso, porque o psql na Shell de nuvem poderá ser uma versão diferente da base de dados do Azure para a versão do servidor PostgreSQL. 
    
    Exemplo de saída psql:
    ```bash
    psql (9.5.7, server 9.6.2)
    WARNING: psql major version 9.5, server major version 9.6.
        Some psql features might not work.
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
    Type "help" for help.
   
    postgres=> 
    ```

    > [!TIP]
    > Se a firewall não está configurada para permitir que o endereço IP da Shell de nuvem, ocorre o seguinte erro:
    > 
    > "psql: FATAL: nenhuma entrada de pg_hba.conf para o anfitrião"138.91.195.82"utilizador"mylogin", base de dados"postgres", SSL no FATAL: não é necessária a ligação SSL. Especifique as opções de SSL e repita.
    > 
    > Para resolver o erro, certifique-se que a configuração do servidor corresponde aos passos na secção "Configurar uma regra de firewall ao nível do servidor" deste artigo.

4. Crie uma base de dados vazia na linha de comandos, escrevendo o seguinte comando:
    ```bash
    CREATE DATABASE mypgsqldb;
    ```
    O comando pode demorar alguns minutos a concluir. 

5. Na linha de comandos, execute o seguinte comando para mudar as ligações para a base de dados recentemente criado **mypgsqldb**:
    ```bash
    \c mypgsqldb
    ```

6. Tipo `\q`e, em seguida, selecione o **Enter** chave para sair psql. Pode fechar o Shell de nuvem depois tiver terminado.

Agora está ligado à base de dados do Azure para o servidor de PostgreSQL e criou uma base de dados do utilizador em branco. Continue para a secção seguinte para ligar utilizando outra ferramenta comuns, pgAdmin.

## <a name="connect-to-the-postgresql-database-by-using-pgadmin"></a>Ligar à base de dados PostgreSQL utilizando pgAdmin

Para ligar ao servidor do Azure PostgreSQL utilizando pgAdmin de ferramenta GUI:
1. Abra a aplicação de pgAdmin no computador cliente. Pode instalar pgAdmin do [pgAdmin site](http://www.pgadmin.org/).

2. Na página do dashboard, sob o **ligações rápidas** secção, selecione o **adicionar o novo servidor** símbolo.

3. No **criar - servidor** caixa de diálogo a **geral** separador, introduza um nome amigável exclusivo para o servidor, tais como **Azure PostgreSQL servidor**.

    ![O separador "Geral"](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)

4. No **criar - servidor** caixa de diálogo a **ligação** separador, utilize as definições conforme especificado e, em seguida, selecione **guardar**.

   ![No separador "Ligação"](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)

    parâmetro pgAdmin |Valor sugerido|Descrição
    ---|---|---
    Nome/Endereço do Anfitrião | Nome do servidor | O valor de nome de servidor que utilizou quando criou a base de dados do Azure para o servidor de PostgreSQL anteriormente. Servidor nosso exemplo é **mypgserver 20170401.postgres.database.azure.com.** Utilize o nome de domínio completamente qualificado (**\*. postgres.database.azure.com**) conforme mostrado no exemplo. Se não se lembrar do nome do servidor, siga os passos na secção anterior para obter as informações da ligação. 
    Porta | 5432 | A porta a utilizar ao ligar à base de dados do Azure para o servidor de PostgreSQL. 
    Base de dados de manutenção | *postgres* | O nome de base de dados gerado pelo sistema predefinido.
    Nome de utilizador | Nome de início de sessão de administração do servidor | O servidor admin início de sessão nome de utilizador que especificou quando criou a base de dados do Azure para o servidor de PostgreSQL anteriormente. Se não se lembrar do nome de utilizador, siga os passos na secção anterior para obter as informações da ligação. O formato é *username@servername*.
    Palavra-passe | A palavra-passe de administrador | A palavra-passe que selecionou quando criou o servidor anteriormente neste guia de introdução.
    Função | Deixar em branco | Não é necessário para fornecer um nome de função neste momento. Deixe o campo em branco.
    Modo SSL | Necessário | Por predefinição, todos os servidores de PostgreSQL do Azure são criados com SSL impor ativada. Para desativar a imposição de SSL, consulte [impor SSL](./concepts-ssl-connection-security.md).
    
5. Selecione **Guardar**.

6. No **Browser** painel à esquerda, expanda o **servidores** nós. Selecione o seu servidor, por exemplo, **Azure PostgreSQL servidor**. Clique para ligar ao mesmo.

7. Expanda o nó de servidor e, em seguida, expanda **Bases de Dados** sob o mesmo. A lista deve incluir existentes *postgres* base de dados e qualquer utilizador recentemente criado base de dados, tais como **mypgsqldb**, que criámos na secção anterior. Tenha em atenção que pode criar várias bases de dados por servidor com base de dados do Azure para PostgreSQL.

8. Clique com botão direito **bases de dados**, escolha o **criar** e, em seguida, selecione **base de dados**.

9. Escreva um nome de base de dados da sua preferência no **base de dados** campo, tal como **mypgsqldb**, conforme mostrado no exemplo.

10. Selecione o **proprietário** para a base de dados de caixa de listagem. Escolha o seu servidor de nome de início de sessão de administrador, como o nosso exemplo, **mylogin**.

11. Selecione **guardar** para criar uma nova base de dados em branco.

12. No **Browser** painel, consulte a base de dados que criou na lista de bases de dados em seu nome de servidor.

    ![O painel "Browser"](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>Limpar recursos
É possível limpar os recursos que criou no início rápido de uma das duas formas. Pode eliminar o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md), que inclui todos os recursos no grupo de recursos. Se pretender manter os outros recursos intactas, elimine apenas o recurso de servidor único.

> [!TIP]
> Outros Guias de Introdução desta coleção compilados com base neste Guia de Introdução. Se pretender continuar a trabalhar com Guias de Introdução, não limpe os recursos que criou neste Guia de Introdução. Se não pretender continuar, siga estes passos para eliminar os recursos que foram criados por este guia de introdução no portal.

Para eliminar o grupo de recursos completo, incluindo o servidor recentemente criado:
1. Localize o grupo de recursos no portal. No menu à esquerda, selecione **grupos de recursos**. Em seguida, selecione o nome do grupo de recursos, tais como o nosso exemplo, **myresourcegroup**.

2. Na página do grupo de recursos, selecione **Eliminar**. Escreva o nome do grupo de recursos, tais como o nosso exemplo, **myresourcegroup**, na caixa de texto para confirmar a eliminação. Selecione **Eliminar**.

Para eliminar apenas o servidor recentemente criado:
1. Localize o seu servidor no portal, não se este estiver aberto. No menu à esquerda, selecione **todos os recursos**. Em seguida, procure o servidor que criou.

2. Na página **Descrição geral**, selecione **Eliminar**.

    ![O botão "Eliminar"](./media/quickstart-create-database-portal/12-delete.png)

3. Confirmar o nome do servidor que pretende eliminar e ver as bases de dados em são afetados. Escreva o nome do servidor na caixa de texto, como o nosso exemplo, **mypgserver 20170401**. Selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a base de dados com Exportar e Importar](./howto-migrate-using-export-and-import.md)
