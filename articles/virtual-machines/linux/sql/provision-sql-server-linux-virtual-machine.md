---
title: Criar uma VM do SQL Server 2017 do Linux no Azure | Microsoft Docs
description: "Este tutorial mostra como criar uma máquina virtual do SQL Server 2017 do Linux no portal do Azure."
services: virtual-machines-linux
author: rothja
ms.author: jroth
manager: jhubbard
ms.date: 10/25/2017
ms.topic: hero-article
tags: azure-service-management
ms.devlang: na
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.technology: database-engine
ms.openlocfilehash: 8d714718e4fc27f0abbfede8fe6d0e2693698ec1
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>Aprovisionar uma máquina virtual do SQL Server do Linux no portal do Azure

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

Neste tutorial de início rápido, vai utilizar o portal do Azure para criar uma máquina virtual do Linux com o SQL Server 2017 instalado.

Neste tutorial, irá:

* [Criar uma VM do SQL do Linux a partir da galeria](#create)
* [Ligar à VM nova com ssh](#connect)
* [Alterar a palavra-passe SA](#password)
* [Configurar ligações remotas](#remote)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a id="create"></a>Criar uma VM do Linux com o SQL Server instalado

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. No painel do lado esquerdo, clique em **Nova**.

1. No painel **Nova**, clique em **Computação**.

1. Clique em **Ver tudo**, junto ao cabeçalho **Em destaque**.

   ![Ver todas as imagens de VMs](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. Na caixa de pesquisa, escreva **SQL Server 2017** e prima **Enter** para iniciar a pesquisa.

1. Clique no ícone **Filtro**, limite a pesquisa a imagens **Com base no Linux** e **Microsoft** e, em seguida, clique em **Concluído**.

    ![Filtro de pesquisa para imagens de VM do SQL Server 2017](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. Selecione uma imagem de Linux do SQL Server 2017 nos resultados da pesquisa. Este tutorial utiliza a **Licença do SQL Server Gratuita: SQL Server 2017 Developer em Red Hat Enterprise Linux 7.4**.

   > [!TIP]
   > A edição Developer permite-lhe testar ou desenvolver com as funcionalidades da edição Enterprise, mas sem os custos de licenciamento do SQL Server. Só paga pelo custo de execução da VM do Linux.

1. Clique em **Criar**.

1. Na janela **Informações básicas**, preencha os detalhes da VM do Linux. 

    ![Janela Informações básicas](./media/provision-sql-server-linux-virtual-machine/basics.png)

    > [!Note]
    > Tem a opção de utilizar uma chave pública SSH ou uma Palavra-passe para autenticação. O SSH é mais seguro. Para obter instruções sobre como gerar uma chave SSH, veja [Criar chaves SSH em Linux e Mac para VMs do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

1. Clique em **OK**.

1. Na janela **Tamanho**, selecione um tamanho para a máquina. Para ver outros tamanhos, selecione **Ver tudo**. Para obter mais informações sobre os tamanhos das VMs, veja [Linux VM size](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes) (Tamanhos de VMs do Linux).

    ![Selecionar um tamanho de VM](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > Para desenvolvimento e testes funcionais, recomendamos o tamanho de VM de **DS2** ou superior. Para testes de desempenho, utilize **DS13** ou superior.

1. Clique em **Selecionar**.

1. Na janela **Definições** , pode fazer alterações às definições ou manter as predefinições.

1. Clique em **OK**.

1. Na página **Resumo**, clique em **Comprar** para criar a VM.

## <a id="connect"></a>Ligar à VM do Linux

Se já utiliza uma shell de BASH, utilize o comando **ssh** para ligar à VM do Azure. No comando seguinte, substitua o nome de utilizador e o endereço IP da VM para ligar à sua VM do Linux.

```bash
ssh azureadmin@40.55.55.555
```

Pode encontrar o endereço IP da VM no portal do Azure.

![Endereço IP no portal do Azure](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

Se estiver a executar no Windows e não tiver uma shell de BASH, pode instalar um cliente de SSH, como o PuTTY.

1. [Transferir e instalar PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

1. Execute o PuTTY.

1. No ecrã de configuração do PuTTY, introduza o endereço IP público da VM.

1. Clique em Abrir e introduza o nome de utilizador e a palavra-passe nos pedidos.

Para obter mais informações sobre como ligar a VMs do Linux, veja [Criar uma VM do Linux no Azure com o Portal](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal#ssh-to-the-vm).

## <a id="password"></a>Alterar a palavra-passe SA

A máquina virtual nova instala o SQL Server com uma palavra-passe SA aleatória. Tem de repor esta palavra-passe antes de poder ligar ao SQL Server com o início de sessão de SA.

1. Depois de ligar à sua VM do Linux, abra um terminal de comando novo.

1. Altere a palavra-passe SA com os comandos seguintes:

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   Introduza uma palavra-passe SA nova e a confirmação da palavra-passe quando lhe for pedido.

1. Reinicie o serviço do SQL Server.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>Adicionar as ferramentas ao seu caminho (opcional)

São instalados por predefinição vários [pacotes](sql-server-linux-virtual-machines-overview.md#packages) do SQL Server, incluindo o pacote SQL Server command-line Tools. O pacote das ferramentas contém as ferramentas **sqlcmd** e **bcp**. Para sua comodidade, opcionalmente, pode adicionar o caminho das ferramentas, `/opt/mssql-tools/bin/`, à variável do seu ambiente **PATH**.

1. Execute os comandos seguintes para modificar **PATH** para sessões de início de sessão e sessões interativas/sem início de sessão:

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a id="remote"></a> Configurar ligações remotas

Se tiver de ligar remotamente ao SQL Server na VM do Azure, tem de configurar uma regra de entrada no grupo de segurança de rede. A regra permite o tráfego na porta em que o SQL Server escuta (predefinição de 1433). Os passos seguintes mostram como utilizar o portal do Azure para este passo. 

1. No portal do Azure, selecione **Máquinas virtuais** e selecione a sua VM do SQL Server.

1. Na lista de propriedades, selecione **Redes**.

1. Na janela **Redes**, clique no botão **Adicionar**, em **Regras da Porta de Entrada**.

   ![Regras da porta de entrada](./media/provision-sql-server-linux-virtual-machine/networking.png)

1. Na lista **Serviço**, selecione **MS SQL**.

    ![Regra de grupo de segurança do MS SQL](./media/provision-sql-server-linux-virtual-machine/sqlnsgrule.png)

1. Clique em **OK** para guardar a regra da VM.

### <a name="open-the-firewall-on-rhel"></a>Abrir a firewall no RHEL

Este tutorial orientou-o na criação de uma VM do Red Hat Enterprise Linux (RHEL). Se quiser ligar remotamente a VMs do RHEL, também tem de abrir a porta 1433 na firewall do Linux.

1. [Ligue](#connect) à sua VM do RHEL.

1. Na shell de BASH, execute os comandos seguintes:

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma máquina virtual do SQL Server 2017 no Azure, pode ligar localmente com **sqlcmd** para executar consultas de Transact-SQL.

Se tiver configurado a VM do Azure para ligações remotas do SQL Server, também deverá conseguir ligar remotamente. Para obter um exemplo de como ligar remotamente ao SQL Server no Linux a partir do Windows, veja [Use SSMS on Windows to connect to SQL Server on Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms) (Utilizar o SSMS no Windows para ligar ao SQL Server no Linux). Para ligar ao Visual Studio Code, veja [Use Visual Studio Code to create and run Transact-SQL scripts for SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode) (Utilizar o Visual Studio Code para criar e executar scripts de Transact-SQL para o SQL Server).

Para obter mais informações gerais sobre o SQL Server no Linux, veja [Overview of SQL Server 2017 on Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview) (Descrição geral do SQL Server 2017 no Linux). Para obter mais informações sobre como utilizar máquinas virtuais do Linux do SQL Server 2017, veja [Overview of SQL Server 2017 virtual machines on Azure](sql-server-linux-virtual-machines-overview.md) (Descrição geral das máquinas virtuais do SQL Server 2017 no Azure).
