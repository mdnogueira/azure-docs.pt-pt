<properties
   pageTitle="Criar uma Base de Dados SQL Data Warehouse no Portal do Azure | Microsoft Azure"
   description="Saiba como criar um Azure SQL Data Warehouse no Portal do Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/03/2016"
   ms.author="lodipalm;"/>

# Criar um Novo Servidor Lógico

Na Base de Dados SQL e no SQL Data Warehouse, cada base de dados é atribuída a um servidor e cada servidor é atribuído a uma localização geográfica. O servidor é denominado um servidor lógico SQL.

> [AZURE.NOTE] <a name="note"></a>Um servidor lógico SQL:
  >
  > + Fornece uma forma consistente para configurar várias bases de dados na mesma localização geográfica.
  > + Não é hardware físico, como é para um servidor no local. Faz parte do software do serviço. É por este motivo que lhe chamamos um *servidor lógico*.
  > + Pode alojar várias bases de dados sem afetar o respetivo desempenho.
  > + Utiliza uma minúscula *s* no nome. O **s**ervidor SQL é um servidor lógico do Azure, enquanto que o SQL **S**erver é um produto de bases de dados no local da Microsoft.

1. Clique em **Servidor** > **Criar um novo servidor**. Não existem taxas para o servidor. Se já tiver uma servidor SQL lógico V12 que pretende utilizar, escolha o servidor existente e siga para o passo seguinte.

    ![Criar um novo servidor](./media/sql-data-warehouse-get-started-provision/create-server.png)

2. Preencha as informações em **Novo servidor**.

    - **Nome do Servidor**: introduza um nome para o servidor lógico. Este é exclusivo para cada localização geográfica.
    - **Nome do Administrador do Servidor**: introduza um nome de utilizador para a conta de administrador do servidor.
    - **Palavra-passe**: introduza a palavra-passe de administrador do servidor.
    - **Localização**: escolha uma localização geográfica para o servidor. Para reduzir o tempo de transferência de dados, é melhor localizar o seu servidor geograficamente próximo de outros recursos de dados a que esta base de dados terá acesso.
    - **Criar Servidor V12**: SIM é a única opção para o SQL Data Warehouse.
    - **Permitir que os serviços do Azure acedam ao servidor**: está sempre selecionado para o SQL Data Warehouse

    >[AZURE.NOTE] Certifique-se de que guarda o nome do servidor, o nome de administrador do servidor e a palavra-passe algures.  Irá precisar destas informações para iniciar sessão no servidor.

3. Clique em **OK** para guardar as definições de configuração do servidor SQL lógico e volte para o painel do SQL Data Warehouse.

    ![Configurar novo servidor](./media/sql-data-warehouse-get-started-provision/configure-server.png)



<!--HONumber=Jun16_HO2-->


