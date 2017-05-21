---
title: "Início Rápido: Criar Bases de Dados do Azure para o servidor MySQL - portal do Azure | Microsoft Docs"
description: Este artigo mostra-lhe como utilizar o portal do Azure para criar rapidamente uma Base de Dados do Azure de exemplo para o servidor MySQL, em cerca de cinco minutos.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 25bfd2c6c25ddb8747dec58fdc68f904f81127fa
ms.contentlocale: pt-pt
ms.lasthandoff: 05/10/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure

Este artigo mostra-lhe como utilizar o portal do Azure para criar uma Base de Dados do Azure de exemplo para o servidor MySQL em cerca de cinco minutos. 

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Abra o browser e navegue para o [portal do Microsoft Azure](https://portal.azure.com/). Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.

![Portal do Azure - início de sessão e dashboard](./media/quickstart-create-mysql-server-database-using-azure-portal/1_portal-login.png)

## <a name="create-azure-database-for-mysql-server"></a>Criar Base de Dados do Azure para o servidor MySQL

1. Navegue para **Bases de Dados** > **MySQL**. Se não encontrar a Base de Dados do Azure para o servidor MySQL na categoria **Bases de Dados**, clique em **Ver tudo**, para mostrar todos os serviços de bases de dados disponíveis. Também pode escrever **MySQL** na caixa de pesquisa para encontrar rapidamente o serviço.
![Portal do Azure – nova - base de dados – MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

2. Clique no ícone **MySQL** e em **Criar**.
No nosso exemplo, preencha a página Base de Dados do Azure para MySQL com as informações seguintes:

| **Campo De** | **Descrição do Campo** |
|----------------|-----------------------|
| *Nome do servidor* | mysqlserver4demo (o nome do servidor é exclusivo globalmente) |
| *Subscrição* | MySQLaaS (selecione no menu pendente) |
| *Grupo de recursos* | myresource (crie um grupo de recursos ou utilize um já existente) |
| *Início de sessão de administrador do servidor* | myadmin (configure o nome da conta de administração) |
| *Palavra-passe* | configure a palavra-passe da conta de administrador |
| *Confirmar palavra-passe* | confirme a palavra-passe da conta de administrador |
| *Localização* | Europa do Norte (selecione entre **Europa do Norte** e **E.U.A. Oeste**). |
| *Versão* | 5.6 (escolha a versão de servidor MySQL) |
| *Configurar o desempenho* | Básico (escolha **Escalão de desempenho**, **Unidades de Computação**, **Armazenamento** e clique em **OK**). |

![Portal do Azure - fornecer a entrada de formulário necessária para criar o MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

Após alguns minutos, a Base de Dados do Azure para o servidor MySQL estará aprovisionada e a funcionar. Pode clicar no botão **Notificações** (ícone de sino) na barra de ferramentas para monitorizar o processo de implementação.

> [!TIP]
> Recomendamos que coloque os serviços do Azure na mesma região e que selecione a localização mais perto de si. Além disso, pode assinalar a opção **Afixar ao dashboard** para permitir um fácil acompanhamento das suas implementações.

## <a name="configure-the-firewall"></a>Configurar a firewall
Antes de ligar à Base de Dados do Azure para o MySQL a partir do seu cliente pela primeira vez, tem de configurar a firewall e adicionar o endereço IP de rede público do cliente (ou o intervalo de endereços IP) à lista de permissões.

1. Clique no servidor acabado de criar e clique em **Definições**.
  ![Portal do Azure - MySQL - botão Definições](./media/quickstart-create-mysql-server-database-using-azure-portal/4_server-settings.png)

2. Na secção **GERAL**, clique em **Definições da firewall**. Pode clicar em **Adicionar o meu IP** para adicionar o endereço IP do seu computador local ou configurar um intervalo de endereços IP. Não se esqueça de clicar em **Guardar** depois de criar a regras.
  ![Portal do Azure - adicionar regra de firewall e guardar](./media/quickstart-create-mysql-server-database-using-azure-portal/5_firewall-settings.png)

## <a name="get-connection-information"></a>Obter informações da ligação
Obtenha o nome de domínio completamente qualificado do servidor MySQL do Azure no portal do Azure. O nome de domínio completamente qualificado é utilizado para ligar ao seu servidor com a ferramenta de linha de comandos **mysql.exe**.

1.    No [portal do Azure](https://portal.azure.com/), clique em **Todos os recursos**, no menu do lado esquerdo, e clique na sua Base de Dados do Azure para o servidor MySQL.

2.    Clique em **Propriedades**. Aponte **NOME DO SERVIDOR** e **INÍCIO DE SESSÃO DE ADMINISTRADOR DO SERVIDOR**.
Neste exemplo, o nome do servidor é *mysql4doc.database.windows.net*, e o início de sessão de administrador do servidor é *mysqladmin@mysql4doc*.

## <a name="connect-to-the-server-using-mysqlexe-command-line-tool"></a>Ligar ao servidor com a ferramenta de linha de comandos mysqlexe
Pode criar várias bases de dados dentro de um servidor MySQL. Não há limite para o número de bases de dados que podem ser criadas, mas múltiplas bases de dados partilham os mesmos recursos do servidor.  Para ligar ao servidor com a ferramenta de linha de comandos **mysql.exe**, abra o **Azure Cloud Shell** no portal e introduza o seguinte:

1. Ligar ao servidor com a ferramenta de linha de comandos **mysql**:
```dos
 mysql -h mysqlserver4demo.database.windows.net -u myadmin@mysqlserver4demo -p
```

2. Ver o estado do servidor:
```dos
 mysql> status
```
  ![Linha de comandos - exemplo de linha de comandos do mysql](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

> [!TIP]
> Para obter comandos adicionais, veja [MySQL 5.6 Reference Manual - Chapter 4.5.1](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) (Manual de Referência do MySQL 5.6 - Capítulo 4.5.1).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Ligar ao servidor com a ferramenta Workbench GUI do MySQL
1.    Inicie a aplicação MySQL Workbench no computador cliente. Pode transferir e instalar o MySQL Workbench [aqui](https://dev.mysql.com/downloads/workbench/).

2.    Na caixa de diálogo **Configurar Ligação Nova**, introduza as informações seguintes no separador **Parâmetros**:

| **Parâmetros** | **Descrição** |
|----------------|-----------------|
|    *Nome da Ligação* | especifique um nome para esta ligação (pode ser qualquer nome) |
| *Método de Ligação* | escolha Standard (TCP/IP) |
| *Nome de Anfitrião* | mycliserver.database.windows.net (o NOME DO SERVIDOR que apontou anteriormente) |
| *Porta* | 3306 |
| *Nome de Utilizador* | myadmin@mycliserver (o INÍCIO DE SESSÃO DE ADMINISTRADOR DO SERVIDOR que apontou anteriormente) |
| *Palavra-passe* | pode armazenar a palavra-passe da conta de administrador no cofre |

![configurar ligação nova](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

3.    Clique em **Testar Ligação** para testar se todos os parâmetros estão configurados corretamente.

4.    Agora, pode clicar na ligação que acabou de criar para ligar com êxito ao servidor.

> SSL é aplicado por predefinição no seu servidor, o que requer uma configuração extra, de modo a poder ligar com êxito. Veja [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Configurar a conectividade SSL na sua aplicação para ligar em segurança à Base de Dados do Azure para o MySQL).  Se pretender desativar o SSL neste início rápido, pode aceder à "Segurança da ligação" no portal para desativar a aplicação do SSL.

## <a name="clean-up-resources"></a>Limpar recursos

Se não precisa destes recursos para outro início rápido/tutorial, pode eliminá-los ao realizar o seguinte procedimento:

1. No menu do lado esquerdo no portal do Azure, clique em **Grupos de recursos** e, em seguida, clique em **myresource**. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva **myresource** na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Design your first Azure Database for MySQL database](./tutorial-design-database-using-portal.md) (Conceber a sua primeira Base de Dados do Azure para a base de dados MySQL)


