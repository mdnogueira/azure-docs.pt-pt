---
title: "Criar uma VM do Azure clássico com MySQL | Microsoft Docs"
description: "Crie uma máquina virtual do Azure com o Windows Server 2012 R2 e a base de dados MySQL utilizando o modelo de implementação clássica."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 98fa06d2-9b92-4d05-ac16-3f8e9fd4feaa
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: cynthn
ms.openlocfilehash: e4135f96027cc25e1e0a149857ace1672570fc4b
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2016"></a>Instalar o MySQL numa máquina virtual criada com o modelo de implementação clássica com o Windows Server 2016
[MySQL](https://www.mysql.com) é um código aberto popular, a base de dados SQL. Este tutorial mostra como instalar e executar o **versão de Comunidade do MySQL 5.7.18** como um servidor de MySQL numa máquina virtual em execução **Windows Server 2016**. A experiência poderão ser ligeiramente diferente para outras versões de MySQL ou Windows Server.

Para obter instruções sobre como instalar o MySQL no Linux, consulte: [como instalar o MySQL no Azure](../../linux/mysql-install.md).

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="create-a-virtual-machine-running-windows-server-2016"></a>Criar uma máquina virtual com o Windows Server 2016
Se ainda não tiver uma VM com o Windows Server 2016, pode utilizá-lo [tutorial](./tutorial.md) para criar a máquina virtual.

## <a name="attach-a-data-disk"></a>Anexar um disco de dados
Depois da máquina virtual é criada, opcionalmente, pode anexar um disco de dados. Adicionar que um disco de dados é recomendado para cargas de trabalho de produção e para evitar a ficar sem espaço na unidade do SO (c), que inclui o sistema operativo.

Consulte [como anexar um disco de dados para uma máquina virtual do Windows](../attach-managed-disk-portal.md) e siga as instruções para anexar um disco vazio. Configure a definição de cache do anfitrião como **nenhum** ou **só de leitura**.

## <a name="log-on-to-the-virtual-machine"></a>Iniciar sessão na máquina virtual
Em seguida, irá [a máquina virtual inicia sessão](./connect-logon.md) como tal, pode instalar o MySQL.

## <a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>Instalar e executar o servidor de Comunidade MySQL na máquina virtual
Siga estes passos para instalar, configurar e executar a versão de Comunidade do servidor de MySQL:

> [!NOTE]
> Quando a transferência de itens utilizando o Internet Explorer, pode definir o IE **configuração de segurança avançada** para desativado e simplificar o processo de transferência. No menu Iniciar, administrativo ferramentas/Local/Gestor de servidor, clique em IE **configuração de segurança avançada** e definir a configuração para desligado).
>
>

1. Depois de se ligar à máquina virtual utilizando o ambiente de trabalho remoto, clique em **Internet Explorer** no ecrã Iniciar.
2. Selecione o **ferramentas** botão no canto superior direito (o ícone roda cogged) e, em seguida, clique em **opções da Internet**. Clique em de **segurança** separador, clique em de **Sites fidedignos** ícone e, em seguida, clique o **Sites** botão. Adicione http://*.mysql.com à lista de sites fidedignos. Clique em **fechar**e, em seguida, clique em **OK**.
3. No endereço barra do Internet Explorer, escreva https://dev.mysql.com/downloads/mysql/.
4. Utilize o site de MySQL para localizar e transferir a versão mais recente do programa de instalação MySQL para Windows. Quando escolher o instalador do MySQL, transfira a versão que tem todo o conjunto (por exemplo, o mysql-instalador-Comunidade-5.7.18.0.msi com um tamanho de ficheiro de 352.8 MB) de ficheiro e guarde o instalador.
5. Quando o instalador terminou de transferir, clique em **executar** para iniciar o programa de configuração.
6. No **contrato de licença** página, aceite o contrato de licença e clique em **seguinte**.
7. No **escolher um tipo de configuração** página, clique no tipo de configuração que pretende e, em seguida, clique em **seguinte**. Os seguintes passos assumem a seleção do **apenas servidor** tipo de configuração.
8. Se o **verificar requisitos** página apresenta, clique em **executar** para permitir que o instalador instalar quaisquer componentes em falta. Siga as instruções que apresentem, tais como o tempo de execução C++ Redistributable.
9. No **instalação** página, clique em **executar**. Quando a instalação estiver concluída, clique em **seguinte**.

10. No **configuração do produto** página, clique em **seguinte**.

11. No **tipo e a rede** página, especifique opções de tipo e a conectividade da configuração pretendida, incluindo a porta TCP, se necessário. Selecione **Mostrar opções avançadas**e, em seguida, clique em **seguinte**.
    ![](./media/mysql-2008r2/MySQL_TypeNetworking.png)

12. No **contas e funções** página, especifique uma palavra-passe da raiz de MySQL segura. Adicionar mais contas de utilizador do MySQL, conforme necessário e, em seguida, clique em **seguinte**.

    ![](./media/mysql-2008r2/MySQL_AccountsRoles_Filled.png)
13. No **Windows serviço** página, especifique as alterações para as predefinições para executar o servidor de MySQL como um serviço do Windows conforme necessário e, em seguida, clique em **seguinte**.

    ![](./media/mysql-2008r2/MySQL_WindowsService.png)
14. As escolhas no **plug-ins e extensões** página são opcionais. Clique em **seguinte** para continuar.
15. No **opções avançadas** página, especifique as alterações às opções de registo, conforme necessário e, em seguida, clique em **seguinte**.

    ![](./media/mysql-2008r2/MySQL_AdvOptions.png)
16. No **aplicar a configuração do servidor** página, clique em **executar**. Quando os passos de configuração estiverem concluídos, clique em **concluir**.
17. No **configuração do produto** página, clique em **seguinte**.
18. No **instalação concluída** página, clique em **registo copiar para área de transferência** se pretender examiná-lo mais tarde e, em seguida, clique em **concluir**.
19. A partir do ecrã Iniciar, escreva **mysql**e, em seguida, clique em **MySQL 5.7 da linha de comandos cliente**.
20. Introduza a palavra-passe de raiz que especificou no passo 12 e são apresentadas, com uma linha de comandos, onde pode emitir comandos para configurar o MySQL. Para obter os detalhes de comandos e sintaxe, consulte [MySQL referência como](https://dev.mysql.com/doc/refman/5.7/en/server-configuration.html).

    ![](./media/mysql-2008r2/MySQL_CommandPrompt.png)
21. Também pode configurar definições de predefinição de configuração do servidor, como os diretórios de base e os dados e unidades. Para obter mais informações, consulte [6.1.2 predefinições do servidor de configuração](https://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html).

## <a name="configure-endpoints"></a>Configurar pontos finais

Para o serviço de MySQL esteja disponível para computadores cliente na Internet, tem de configurar um ponto final para a porta TCP e criar uma regra de Firewall do Windows. O valor de porta predefinido em que o serviço de MySQL servidor escuta MySQL clientes é 3306. Pode especificar outra porta, desde que a porta é consistente com o valor fornecido no **tipo e a rede** página (passo 11 do procedimento anterior).

> [!NOTE]
> Para utilização em produção, considere as implicações de segurança de disponibilizar o serviço de MySQL servidor em todos os computadores na Internet. Pode definir o conjunto de endereços IP de origem que estão autorizados a utilizar o ponto final com uma lista de controlo de acesso (ACL). Para obter mais informações, consulte [como conjunto de cópias de segurança de pontos finais para uma Máquina Virtual](setup-endpoints.md).
>
>

Para configurar um ponto final para o serviço de MySQL servidor:

1. No portal do Azure, clique em **máquinas virtuais (clássicas)**, clique no nome da máquina virtual MySQL e, em seguida, clique em **pontos finais**.
2. Na barra de comandos, clique em **adicionar**.
3. No **adicionar ponto final** , escreva um nome exclusivo para **nome**.
4. Selecione **TCP** como o protocolo.
5. Escreva o número da porta, tal como **3306**, no **Porta pública** e **porta privada**e, em seguida, clique em **OK**.

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>Adicionar uma regra de Firewall do Windows para permitir tráfego de MySQL
Para adicionar uma regra de Firewall do Windows que permita o tráfego de MySQL a partir da Internet, execute o seguinte comando num _linha de comandos elevada do Windows PowerShell_ na máquina virtual de servidor de MySQL.

    New-NetFirewallRule -DisplayName "MySQL57" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public

## <a name="test-your-remote-connection"></a>Testar a ligação remota
Para testar a ligação remota à VM do Azure com o serviço servidor MySQL, tem de fornecer o nome DNS do serviço em nuvem que contém o VN.

1. No portal do Azure, clique em **máquinas virtuais (clássicas)**, clique no nome da máquina virtual server MySQL e, em seguida, clique em **descrição geral**.
2. A partir do dashboard de máquina virtual, tenha em atenção o **nome DNS** valor. Segue-se um exemplo:

   ![](media/mysql-2008r2/MySQL_DNSName.png)
3. A partir de um computador local com o MySQL ou o cliente do MySQL, execute o seguinte comando para iniciar sessão como um utilizador MySQL.

     MySQL -u <yourMysqlUsername> - p -h<yourDNSname>

   Por exemplo, utilizando o nome de utilizador MySQL _dbadmin3_ e _testmysql.cloudapp.net_ nome de DNS para a máquina virtual, poderá começar MySQL utilizando o seguinte comando:

     MySQL -u dbadmin3 -p -h testmysql.cloudapp.net

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como executar MySQL, consulte o [MySQL documentação](http://dev.mysql.com/doc/).
