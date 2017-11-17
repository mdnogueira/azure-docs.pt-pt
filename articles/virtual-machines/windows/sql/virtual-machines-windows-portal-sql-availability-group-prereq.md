---
title: "Grupos de disponibilidade do SQL Server - máquinas virtuais do Azure - pré-requisitos | Microsoft Docs"
description: "Este tutorial mostra como configurar os pré-requisitos para a criação de um grupo de disponibilidade SQL Server Always On em VMs do Azure."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/09/2017
ms.author: mikeray
ms.openlocfilehash: 0748e0ffa405fc02f6da7e2c412beec12510fde5
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="complete-the-prerequisites-for-creating-always-on-availability-groups-on-azure-virtual-machines"></a>Concluir os pré-requisitos para a criação de grupos de disponibilidade Always On em máquinas virtuais do Azure

Este tutorial mostra como concluir os pré-requisitos para a criação de um [SQL Server Always On o grupo de disponibilidade em máquinas de virtuais (VMs) do Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md). Quando tiver terminado as pré-requisitos, tiver um controlador de domínio, duas VMs de SQL Server e um servidor de testemunho num grupo de recursos única.

**Estimativa de tempo**: poderá demorar algumas horas para concluir os pré-requisitos. Grande parte este tempo é gasto a criação de máquinas virtuais.

O diagrama seguinte ilustra a compilação no tutorial.

![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Reveja a documentação do grupo de disponibilidade

Este tutorial parte do princípio de que tem uma compreensão básica do SQL Server Always On nos grupos de disponibilidade. Se não estiver familiarizado com esta tecnologia, consulte [descrição geral de grupos de disponibilidade Always (SQL Server)](http://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Crie uma conta Azure
Precisa de uma conta do Azure. Pode [abrir uma conta do Azure gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar os benefícios de subscritor do Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
1. Inicie sessão no [Portal do Azure](http://portal.azure.com).
2. Clique em  **+**  para criar um novo objeto no portal.

   ![Novo objeto](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Tipo **grupo de recursos** no **Marketplace** janela pesquisa.

   ![Grupo de recursos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Clique em **grupo de recursos**.
5. Clique em **Criar**.
6. No **grupo de recursos** painel, em **nome do grupo de recursos**, escreva um nome para o grupo de recursos. Por exemplo, digite **sql-ha-rg**.
7. Se tiver várias subscrições do Azure, verifique se a subscrição que pretende criar o grupo de disponibilidade na subscrição do Azure.
8. Selecione uma localização. A localização é a região do Azure onde pretende criar o grupo de disponibilidade. Para este tutorial, vamos criar todos os recursos numa única localização do Azure.
9. Certifique-se de que **afixar ao dashboard** está marcada. Esta definição opcional, coloca um atalho para o grupo de recursos no dashboard do portal do Azure.

   ![Grupo de recursos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Clique em **criar** para criar o grupo de recursos.

O Azure cria o grupo de recursos e pins um atalho para o grupo de recursos no portal.

## <a name="create-the-network-and-subnets"></a>Criar a rede e sub-redes
O passo seguinte consiste em criar as redes e sub-redes no grupo de recursos do Azure.

A solução utiliza uma rede virtual com duas sub-redes. O [descrição geral de rede Virtual](../../../virtual-network/virtual-networks-overview.md) fornece mais informações sobre redes no Azure.

Para criar a rede virtual:

1. No portal do Azure, no grupo de recursos, clique em **+ adicionar**. Abre-se do Azure a **tudo** painel.

   ![Novo item](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Procurar **rede virtual**.

     ![Rede virtual de pesquisa](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Clique em **rede Virtual**.
4. No **rede Virtual** painel, clique em de **Resource Manager** modelo de implementação e, em seguida, clique em **criar**.

    A tabela seguinte mostra as definições para a rede virtual:

   | **Campo** | Valor |
   | --- | --- |
   | **Nome** |autoHAVNET |
   | **Espaço de endereços** |10.33.0.0/24 |
   | **Nome da sub-rede** |Admin |
   | **Intervalo de endereços da sub-rede** |10.33.0.0/29 |
   | **Subscrição** |Especifique a subscrição que pretende utilizar. **Subscrição** está em branco se tiver apenas uma subscrição. |
   | **Grupo de recursos** |Escolha **utilizar existente** e escolha o nome do grupo de recursos. |
   | **Localização** |Especifique a localização do Azure. |

   O intervalo de endereços endereço espaço e a sub-rede pode ser diferente da tabela. Dependendo da sua subscrição, o portal sugere um espaço de endereços disponível e o intervalo de endereços de sub-rede correspondente. Se não existe suficiente espaço de endereço estiver disponível, utilize uma subscrição diferente.

   O exemplo utiliza o nome de sub-rede **Admin**. Esta sub-rede é para os controladores de domínio.

5. Clique em **Criar**.

   ![Configurar a rede virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

Azure devolve ao dashboard do portal e notifica-o quando é criada a nova rede.

### <a name="create-a-second-subnet"></a>Criar uma segunda sub-rede
A nova rede virtual possui uma sub-rede, com o nome **Admin**. Os controladores de domínio utilizam esta sub-rede. A utilização de VMs de SQL Server com o nome de uma segunda sub-rede **SQL**. Para configurar esta sub-rede:

1. No seu dashboard, clique no grupo de recursos que criou, **SQL-HA-RG**. Localizar a rede no grupo de recursos em **recursos**.

    Se **SQL-HA-RG** não estão visíveis, encontrá-lo ao clicar em **grupos de recursos** e a filtragem pelo nome do grupo de recursos.
2. Clique em **autoHAVNET** na lista de recursos. Azure abre o painel de configuração de rede.
3. No **autoHAVNET** painel virtual network, em **definições** , clique em **sub-redes**.

    Tenha em atenção a sub-rede que já criou.

   ![Configurar a rede virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Crie uma segunda sub-rede. Clique em **+ sub-rede**.
6. No **adicionar sub-rede** painel, configurar a sub-rede, escrevendo **sqlsubnet** em **nome**. Azure automaticamente Especifica um **intervalo de endereços**. Certifique-se de que este intervalo de endereços tem, pelo menos, 10 endereços na mesma. Num ambiente de produção, poderá necessitar de mais de endereços.
7. Clique em **OK**.

    ![Configurar a rede virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

A tabela seguinte resume as definições de configuração de rede:

| **Campo** | Valor |
| --- | --- |
| **Nome** |**autoHAVNET** |
| **Espaço de endereços** |Este valor depende de espaços de endereços disponíveis na sua subscrição. Um valor típico é 10.0.0.0/16. |
| **Nome da sub-rede** |**Admin** |
| **Intervalo de endereços da sub-rede** |Este valor depende dos intervalos de endereços disponíveis na sua subscrição. Um valor típico é 10.0.0.0/24. |
| **Nome da sub-rede** |**sqlsubnet** |
| **Intervalo de endereços da sub-rede** |Este valor depende dos intervalos de endereços disponíveis na sua subscrição. Um valor típico é 10.0.1.0/24. |
| **Subscrição** |Especifique a subscrição que pretende utilizar. |
| **Grupo de Recursos** |**SQL SERVER-HA-RG** |
| **Localização** |Especifique a mesma localização que escolheu para o grupo de recursos. |

## <a name="create-availability-sets"></a>Criar conjuntos de disponibilidade

Antes de criar máquinas virtuais, terá de criar conjuntos de disponibilidade. Conjuntos de disponibilidade reduzem o período de inatividade para eventos de manutenção planeado ou não planeada. Um conjunto de disponibilidade do Azure é um grupo lógico de recursos Azure coloca em domínios físicos de falhas e domínios de atualização. Um domínio de falhas assegura que os membros do conjunto de disponibilidade têm recursos de energia e da rede separados. Um domínio de atualização garante que os membros do conjunto de disponibilidade não são colocados para manutenção em simultâneo. Para obter mais informações, consulte [gerir a disponibilidade das máquinas virtuais](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Precisa de dois conjuntos de disponibilidade. Um é para os controladores de domínio. A segunda serve para as VMs de SQL Server.

Para criar um conjunto de disponibilidade, vá para o grupo de recursos e clique em **adicionar**. Filtrar os resultados, escrevendo **conjunto de disponibilidade**. Clique em **do conjunto de disponibilidade** na resultados e, em seguida, clique em **criar**.

Configure dois conjuntos de disponibilidade, de acordo com os parâmetros na tabela seguinte:

| **Campo** | Conjunto de disponibilidade do controlador de domínio | Conjunto de disponibilidade do SQL Server |
| --- | --- | --- |
| **Nome** |adavailabilityset |sqlavailabilityset |
| **Grupo de recursos** |SQL SERVER-HA-RG |SQL SERVER-HA-RG |
| **Domínios de falhas** |3 |3 |
| **Domínios de atualização** |5 |3 |

Depois de criar os conjuntos de disponibilidade, volte para o grupo de recursos no portal do Azure.

## <a name="create-domain-controllers"></a>Criar controladores de domínio
Depois de criar a rede, sub-redes, conjuntos de disponibilidade e um balanceador de carga para a Internet, está pronto para criar máquinas virtuais para os controladores de domínio.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Criar máquinas virtuais para os controladores de domínio
Para criar e configurar os controladores de domínio, regresse à **SQL-HA-RG** grupo de recursos.

1. Clique em **Adicionar**. O **tudo** abre o painel.
2. Tipo **Datacenter do Windows Server 2016**.
3. Clique em **Datacenter do Windows Server 2016**. No **Datacenter do Windows Server 2016** painel, certifique-se de que o modelo de implementação é **Resource Manager**e, em seguida, clique em **criar**. Abre-se do Azure a **criar máquina virtual** painel.

Repita os passos anteriores para criar duas máquinas virtuais. Nome as duas máquinas virtuais:

* AD-primary-dc
* AD-secundária-dc

  > [!NOTE]
  > O **ad-secundária-dc** máquina virtual é opcional, para fornecer elevada disponibilidade para serviços de domínio do Active Directory.
  >
  >

A tabela seguinte mostra as definições para estas duas máquinas:

| **Campo** | Valor |
| --- | --- |
| **Nome** |Primeiro controlador de domínio: *ad-primary-dc*.</br>Controlador de domínio de segundo *ad-secundária-dc*. |
| **Tipo de disco da VM** |SSD |
| **Nome de utilizador** |DomainAdmin |
| **Palavra-passe** |Contoso! 0000 |
| **Subscrição** |*A sua subscrição* |
| **Grupo de recursos** |SQL SERVER-HA-RG |
| **Localização** |*A localização* |
| **Tamanho** |DS1_V2 |
| **Armazenamento** | **Utilizar discos geridos** - **Sim** |
| **Rede virtual** |autoHAVNET |
| **Sub-rede** |Admin |
| **Endereço IP público** |*Mesmo nome que a VM* |
| **Grupo de segurança de rede** |*Mesmo nome que a VM* |
| **Conjunto de disponibilidade** |adavailabilityset </br>**Domínios de falha**: 2</br>**Domínios de atualização**: 2|
| **Diagnóstico** |Ativado |
| **Conta de armazenamento de diagnóstico** |*Criado automaticamente* |

   >[!IMPORTANT]
   >Só é possível colocar uma VM num aquando da respetiva criação de conjunto de disponibilidade. Não é possível alterar o depois de criar uma VM de conjunto de disponibilidade. Consulte [gerir a disponibilidade das máquinas virtuais](../manage-availability.md).

O Azure cria as máquinas virtuais.

Depois das máquinas virtuais são criadas, configure o controlador de domínio.

### <a name="configure-the-domain-controller"></a>Configurar o controlador de domínio
Nos passos seguintes, configure o **ad-primary-dc** computador como um controlador de domínio corp.contoso.com.

1. No portal, abra o **SQL-HA-RG** recurso, grupo e selecione o **ad-primary-dc** máquina. No **ad-primary-dc** painel, clique em **Connect** para abrir um ficheiro RDP para acesso de ambiente de trabalho remoto.

    ![Ligar a uma máquina virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Inicie sessão com a sua conta de administrador configurado (**\DomainAdmin**) e palavra-passe (**Contoso! 0000**).
3. Por predefinição, o **Gestor de servidor** dashboard deve ser apresentado.
4. Clique em de **para adicionar funções e funcionalidades** ligação no dashboard.

    ![Gestor de servidor - adicionar funções](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Selecione **seguinte** até chegar à **funções de servidor** secção.
6. Selecione o **serviços de domínio do Active Directory** e **servidor DNS** funções. Quando lhe for pedido, adicione quaisquer funcionalidades adicionais que são necessários para estas funções.

   > [!NOTE]
   > Windows avisa que não há nenhum endereço IP estático. Se estiver a testar a configuração, clique em **continuar**. Para cenários de produção, definir o endereço IP estático no portal do Azure, ou [utilizar o PowerShell para definir o endereço IP estático do computador de controlador domínio](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Funções caixa de diálogo Adicionar](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Clique em **seguinte** até chegar a **confirmação** secção. Selecione o **reinicie o servidor de destino automaticamente se necessário** caixa de verificação.
8. Clique em **Instalar**.
9. Depois de concluir as funcionalidades a instalar, regresse ao **Gestor de servidor** dashboard.
10. Selecione o novo **do AD DS** opção no painel da esquerda.
11. Clique em de **mais** hiperligação na barra de aviso amarelo.

    ![Caixa de diálogo do AD DS na VM de servidor DNS](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. No **ação** coluna o **todos os detalhes da tarefa de servidor** caixa de diálogo, clique em **promover este servidor a controlador de domínio**.
13. No **Assistente do configuração de serviços de domínio do Active Directory**, utilize os seguintes valores:

    | **Página** | Definição |
    | --- | --- |
    | **Configuração de implementação** |**Adicionar uma nova floresta**<br/> **Nome de domínio de raiz** = corp.contoso.com |
    | **Opções de controlador de domínio** |**Palavra-passe DSRM** = Contoso! 0000<br/>**Confirmar palavra-passe** = Contoso! 0000 |
14. Clique em **seguinte** passar por outras páginas no assistente. No **verificação de pré-requisitos** página, certifique-se de que vê a mensagem seguinte: **todas as verificações de pré-requisitos passaram com êxito**. Pode rever as mensagens de aviso aplicável, mas é possível continuar a instalação.
15. Clique em **Instalar**. O **ad-primary-dc** máquina virtual é reiniciado automaticamente.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Tenha em atenção o endereço IP do controlador de domínio primário

Utilize o controlador de domínio primário para o DNS. Tenha em atenção o endereço IP do controlador de domínio primário.

Uma forma para obter o endereço IP do controlador de domínio primário é através do portal do Azure.

1. No portal do Azure, abra o grupo de recursos.

2. Clique no controlador de domínio primário.

3. No painel de controlador de domínio principal, clique em **interfaces de rede**.

![Interfaces de rede](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Tenha em atenção o endereço IP privado para este servidor.

### <a name="configure-the-virtual-network-dns"></a>Configurar a DNS de rede virtual
Depois de criar o primeiro controlador de domínio e ativar o DNS no primeiro servidor, configure a rede virtual para utilizar este servidor para o DNS.

1. No portal do Azure, clique na rede virtual.

2. Em **definições**, clique em **servidor DNS**.

3. Clique em **personalizada**e escreva o endereço IP privado de controlador de domínio primário.

4. Clique em **Guardar**.

### <a name="configure-the-second-domain-controller"></a>Configurar o controlador de domínio de segundo
Depois do controlador de domínio primário é reiniciado, pode configurar o controlador de domínio de segundo. Este passo opcional é de elevada disponibilidade. Siga estes passos para configurar o controlador de domínio de segundo:

1. No portal, abra o **SQL-HA-RG** recurso, grupo e selecione o **ad-secundária-dc** máquina. No **ad-secundária-dc** painel, clique em **Connect** para abrir um ficheiro RDP para acesso de ambiente de trabalho remoto.
2. Iniciar sessão para a VM ao utilizar a sua conta de administrador configurado (**BUILTIN\DomainAdmin**) e palavra-passe (**Contoso! 0000**).
3. Altere o endereço do servidor DNS preferencial para o endereço do controlador de domínio.
4. No **Centro de partilha de rede e**, clique na interface de rede.
   ![Interface de rede](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Clique em **Propriedades**.
6. Selecione **protocolo IP versão 4 (TCP/IPv4)** e clique em **propriedades**.
7. Selecione **utilize os seguintes endereços de servidor DNS** e especifique o endereço do controlador de domínio primário no **servidor DNS preferencial**.
8. Clique em **OK**e, em seguida, **fechar** para consolidar as alterações. Está agora podem associar a VM **corp.contoso.com**.

   >[!IMPORTANT]
   >Se perder a ligação ao ambiente de trabalho remoto depois de alterar a definição de DNS, aceda ao portal do Azure e reinicie a máquina virtual.

9. A partir do ambiente de trabalho remoto para o controlador de domínio secundário, abra **Dashboard do Gestor de servidor**.
10. Clique em de **para adicionar funções e funcionalidades** ligação no dashboard.

    ![Gestor de servidor - adicionar funções](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Selecione **seguinte** até chegar à **funções de servidor** secção.
12. Selecione o **serviços de domínio do Active Directory** e **servidor DNS** funções. Quando lhe for pedido, adicione quaisquer funcionalidades adicionais que são necessários para estas funções.
13. Depois de concluir as funcionalidades a instalar, regresse ao **Gestor de servidor** dashboard.
14. Selecione o novo **do AD DS** opção no painel da esquerda.
15. Clique em de **mais** hiperligação na barra de aviso amarelo.
16. No **ação** coluna o **todos os detalhes da tarefa de servidor** caixa de diálogo, clique em **promover este servidor a controlador de domínio**.
17. Em **configuração de implementação**, selecione **adicionar um controlador de domínio a um domínio existente**.
   ![Configuração de implementação](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Clique em **Selecionar**.
19. Ligar utilizando a conta de administrador (**Corp. CONTOSO.COM\domainadmin**) e palavra-passe (**Contoso! 0000**).
20. No **selecionar um domínio da floresta**, clique no seu domínio e, em seguida, clique em **OK**.
21. No **opções de controlador de domínio**, utilize os valores predefinidos e defina uma palavra-passe do DSRM.

   >[!NOTE]
   >O **opções DNS** página poderá avisá-lo que não é possível criar uma delegação para este servidor DNS. Pode ignorar este aviso em ambientes de não produção.
22. Clique em **seguinte** até que a caixa de diálogo atinja o **pré-requisitos** verificar. Em seguida, clique em **Instalar**.

Depois do servidor concluir as alterações de configuração, reinicie o servidor.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Adicione o endereço de IP privado para o controlador de domínio de segundo para o servidor de DNS da VPN

No portal do Azure, na rede virtual, altere o servidor DNS para incluir o endereço IP do controlador de domínio secundário. Isto permite que a redundância de serviço do DNS.

### <a name=DomainAccounts></a>Configurar as contas de domínio

Passos seguintes, irá configurar as contas do Active Directory. A tabela seguinte mostra as contas:

| |Conta de instalação<br/> |SQLServer-0 <br/>Conta do SQL Server e o serviço de agente do SQL Server |SQLServer-1<br/>Conta do SQL Server e o serviço de agente do SQL Server
| --- | --- | --- | ---
|**Nome próprio** |Instalar |SQLSvc1 | SQLSvc2
|**SamAccountName de utilizador** |Instalar |SQLSvc1 | SQLSvc2

Utilize os seguintes passos para criar cada conta.

1. Iniciar sessão para o **ad-primary-dc** máquina.
2. No **Gestor de servidor**, selecione **ferramentas**e, em seguida, clique em **Active Directory Administrative Center**.   
3. Selecione **corp (local)** no painel esquerdo.
4. No lado direito **tarefas** painel, selecione **novo**e, em seguida, clique em **utilizador**.
   ![Centro de administração do Active Directory](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Defina uma palavra-passe complexa para cada conta.<br/> Para ambientes de não produção, defina a conta de utilizador para nunca expirar.

5. Clique em **OK** para criar o utilizador.
6. Repita os passos anteriores para cada uma das três contas.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Conceder as permissões necessárias para a conta de instalação
1. No **Active Directory Administrative Center**, selecione **corp (local)** no painel esquerdo. Em seguida, no direito **tarefas** painel, clique em **propriedades**.

    ![Propriedades do utilizador CORP](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Selecione **extensões**e, em seguida, clique em de **avançadas** botão no **segurança** separador.
3. No **definições de segurança avançadas para corp** caixa de diálogo, clique em **adicionar**.
4. Clique em **selecionar um principal**, procure **CORP\Install**e, em seguida, clique em **OK**.
5. Selecione o **ler todas as propriedades** caixa de verificação.

6. Selecione o **criar objetos de computador** caixa de verificação.

     ![Permissões de utilizador Corp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Clique em **OK**e, em seguida, clique em **OK** novamente. Fechar o **corp** janela Propriedades.

Agora que concluiu a configuração do Active Directory e os objetos de utilizador, crie duas VMs de SQL Server e um servidor de testemunho VM. Em seguida, associe três ao domínio.

## <a name="create-sql-server-vms"></a>Criar VMs de SQL Server

Crie três máquinas virtuais adicionais. A solução requer duas máquinas virtuais com instâncias do SQL Server. Terceira máquina virtual irá funcionar como um testemunho. Pode utilizar o Windows Server 2016 um [nuvem testemunho](http://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness); no entanto, para consistência com sistemas operativos anteriores neste documento utiliza uma máquina virtual para um testemunho.  

Antes de continuar, considere as seguintes decisões de deisign.

* **Armazenamento - discos geridos pelo Azure**

   Para o armazenamento de máquina virtual, utilize discos gerida do Azure. A Microsoft recomenda discos geridos por máquinas virtuais do SQL Server. O Managed Disks processa o armazenamento em segundo plano. Além disso, se houver máquinas virtuais com Managed Disks no mesmo conjunto de disponibilidade, o Azure distribui os recursos de armazenamento para fornecer a redundância adequada. Para obter informações adicionais, veja [Azure Managed Disks Overview](../managed-disks-overview.md) (Descrição Geral do Managed Disks). Para informações específicas sobre discos geridos num conjunto de disponibilidade, consulte [utilizar discos de gerido para as VMs num conjunto de disponibilidade](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Endereços de IP privado de rede - na produção**

   Para as máquinas virtuais, este tutorial utiliza endereços IP públicos. Isto permite que a ligação remota diretamente à máquina virtual através da internet - -torna mais fácil e os passos de configuração. Em ambientes de produção, a Microsoft recomenda apenas endereços IP privados para reduzir os requisitos de espaço de vulnerabilidade de recurso VM de instância do SQL Server.

### <a name="create-and-configure-the-sql-server-vms"></a>Criar e configurar as VMs de SQL Server
Em seguida, crie três VMs – duas VMs de SQL Server e uma VM para um nó de cluster adicionais. Para criar cada uma das VMs, volte atrás para o **SQL-HA-RG** grupo de recursos, clique em **adicionar**, procure o item da Galeria adequado, clique em **Máquina Virtual**e, em seguida, clique em  **Na galeria**. Utilize as informações na tabela seguinte para ajudar a criar as VMs:


| Página | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Selecione o item da Galeria adequado |**O Centro de dados do Windows Server 2016** |**SQL Server 2016 SP1 Enterprise, Windows Server 2016** |**SQL Server 2016 SP1 Enterprise, Windows Server 2016** |
| Configuração da máquina virtual **Noções básicas** |**Nome** = fsw de cluster<br/>**Nome de utilizador** = DomainAdmin<br/>**Palavra-passe** = Contoso! 0000<br/>**Subscrição** = a sua subscrição<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Localização** = a localização do azure |**Nome** = sqlserver 0<br/>**Nome de utilizador** = DomainAdmin<br/>**Palavra-passe** = Contoso! 0000<br/>**Subscrição** = a sua subscrição<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Localização** = a localização do azure |**Nome** = sqlserver-1<br/>**Nome de utilizador** = DomainAdmin<br/>**Palavra-passe** = Contoso! 0000<br/>**Subscrição** = a sua subscrição<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Localização** = a localização do azure |
| Configuração da máquina virtual **tamanho** |**TAMANHO** = DS1\_V2 (1 vCPU, 3.5 GB) |**TAMANHO** = DS2\_V2 (vCPUs 2, 7 GB)</br>O tamanho tem de suportar armazenamento SSD (suporte de disco Premium. )) |**TAMANHO** = DS2\_V2 (vCPUs 2, 7 GB) |
| Configuração da máquina virtual **definições** |**Armazenamento**: utilize discos geridos pelo.<br/>**Rede virtual** = autoHAVNET<br/>**Sub-rede** = sqlsubnet(10.1.1.0/24)<br/>**Endereço IP público** geradas automaticamente.<br/>**Grupo de segurança de rede** = None<br/>**Diagnóstico de monitorização** = ativada<br/>**Conta de armazenamento do Diagnostics** = utilizar uma conta de armazenamento gerado automaticamente<br/>**Conjunto de disponibilidade** = sqlAvailabilitySet<br/> |**Armazenamento**: utilize discos geridos pelo.<br/>**Rede virtual** = autoHAVNET<br/>**Sub-rede** = sqlsubnet(10.1.1.0/24)<br/>**Endereço IP público** geradas automaticamente.<br/>**Grupo de segurança de rede** = None<br/>**Diagnóstico de monitorização** = ativada<br/>**Conta de armazenamento do Diagnostics** = utilizar uma conta de armazenamento gerado automaticamente<br/>**Conjunto de disponibilidade** = sqlAvailabilitySet<br/> |**Armazenamento**: utilize discos geridos pelo.<br/>**Rede virtual** = autoHAVNET<br/>**Sub-rede** = sqlsubnet(10.1.1.0/24)<br/>**Endereço IP público** geradas automaticamente.<br/>**Grupo de segurança de rede** = None<br/>**Diagnóstico de monitorização** = ativada<br/>**Conta de armazenamento do Diagnostics** = utilizar uma conta de armazenamento gerado automaticamente<br/>**Conjunto de disponibilidade** = sqlAvailabilitySet<br/> |
| Configuração da máquina virtual **definições do SQL Server** |Não aplicável |**Conectividade do SQL** = privada (na Virtual Network)<br/>**Porta** = 1433<br/>**Autenticação do SQL Server** = desativar<br/>**Configuração de armazenamento** = geral<br/>**Aplicação de patches automatizada** = Domingo, 2:00<br/>**Cópia de segurança automatizada** = desativado</br>**Integração do Cofre de chaves do Azure** = desativado |**Conectividade do SQL** = privada (na Virtual Network)<br/>**Porta** = 1433<br/>**Autenticação do SQL Server** = desativar<br/>**Configuração de armazenamento** = geral<br/>**Aplicação de patches automatizada** = Domingo, 2:00<br/>**Cópia de segurança automatizada** = desativado</br>**Integração do Cofre de chaves do Azure** = desativado |

<br/>

> [!NOTE]
> Os tamanhos de máquina sugeridos aqui destinam-se para grupos de disponibilidade de teste em VMs do Azure. Para obter o melhor desempenho em cargas de trabalho de produção, consulte as recomendações para tamanhos de máquina do SQL Server e a configuração no [melhores práticas de desempenho para o SQL Server em virtual machines do Azure](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

Depois das três VMs são totalmente aprovisionadas, precisa de associá-los para o **corp.contoso.com** domínio e conceder direitos administrativos de CORP\Install para as máquinas.

### <a name="joinDomain"></a>Associar os servidores ao domínio

Agora, está podem associar as VMs para **corp.contoso.com**. Efetue o seguinte procedimento para as VMs de SQL Server e o servidor de testemunho de partilha de ficheiros:

1. Ligar remotamente à máquina virtual com **BUILTIN\DomainAdmin**.
2. No **Gestor de servidor**, clique em **servidor Local**.
3. Clique em de **WORKGROUP** ligação.
4. No **nome do computador** secção, clique em **alteração**.
5. Selecione o **domínio** caixa de verificação e tipo **corp.contoso.com** na caixa de texto. Clique em **OK**.
6. No **segurança do Windows** caixa de diálogo de pop-up, especifique as credenciais para a conta de administrador de domínio predefinida (**CORP\DomainAdmin**) e a palavra-passe (**Contoso! 0000**) .
7. Quando vir a mensagem "Boas-vindas ao domínio corp.contoso.com", clique em **OK**.
8. Clique em **fechar**e, em seguida, clique em **reiniciar agora** na caixa de diálogo de pop-up.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Adicionar o utilizador Corp\Install como um administrador em cada cluster de VM

Depois de reiniciar cada máquina virtual como um membro do domínio, adicione **CORP\Install** como membro do grupo de administradores locais.

1. Aguarde até que a VM seja reiniciada, em seguida, inicie o ficheiro RDP novamente a partir do controlador de domínio principal para iniciar sessão no **sqlserver-0** utilizando o **CORP\DomainAdmin** conta.
   >[!TIP]
   >Certifique-se de que inicia sessão com a conta de administrador de domínio. Nos passos anteriores, que estava a utilizar a conta de administrador INCORPORADA IN. Agora que o servidor estiver num domínio, utilize a conta de domínio. Na sua sessão do RDP, especifique *domínio*\\*username*.

2. No **Gestor de servidor**, selecione **ferramentas**e, em seguida, clique em **gestão de computadores**.
3. No **gestão de computadores** janela, expanda **utilizadores e grupos locais**e, em seguida, selecione **grupos**.
4. Faça duplo clique o **administradores** grupo.
5. No **administradores propriedades** caixa de diálogo, clique em de **adicionar** botão.
6. Introduza o utilizador **CORP\Install**e, em seguida, clique em **OK**.
7. Clique em **OK** para fechar o **administrador propriedades** caixa de diálogo.
8. Repita os passos anteriores no **sqlserver-1** e **cluster fsw**.

### <a name="setServiceAccount"></a>Definir as contas de serviço do SQL Server

Em cada VM do SQL Server, defina a conta de serviço do SQL Server. Utilizar as contas que criou quando a [configurado as contas de domínio](#DomainAccounts).

1. Abra **Gestor de configuração do SQL Server**.
2. O serviço SQL Server com o botão direito e, em seguida, clique em **propriedades**.
3. Defina a conta e palavra-passe.
4. Repita estes passos em de outras VM do SQL Server.  

Para grupos de disponibilidade do SQL Server, cada VM do SQL Server tem de ser executado como uma conta de domínio.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Criar um início de sessão em cada VM do SQL Server para a conta de instalação

Utilize a conta de instalação (CORP\install) para configurar o grupo de disponibilidade. Esta conta tem de ser um membro do **sysadmin** função de servidor fixa em cada VM do SQL Server. Os seguintes passos criar um início de sessão para a conta de instalação:

1. Ligar ao servidor através do protocolo de ambiente de trabalho remoto (RDP) utilizando o  *\<MachineName\>\DomainAdmin* conta.

1. Abra o SQL Server Management Studio e ligue à instância local do SQL Server.

1. No **Object Explorer**, clique em **segurança**.

1. Clique com botão direito **inícios de sessão**. Clique em **novo início de sessão**.

1. No **início de sessão - novo**, clique em **pesquisa**.

1. Clique em **localizações**.

1. Introduza as credenciais de rede de administrador de domínio.

1. Utilize a conta de instalação.

1. Definir o início de sessão para ser um membro do **sysadmin** função de servidor fixa.

1. Clique em **OK**.

Repita os passos anteriores na outros VM do SQL Server.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Adicionar funcionalidades do Clustering de ativação pós-falha para ambas as VMs de SQL Server

Para adicionar funcionalidades de Clustering de ativação pós-falha, efetue o seguinte procedimento em ambas as VMs de SQL Server:

1. Ligar à máquina virtual do SQL Server através do protocolo de ambiente de trabalho remoto (RDP), utilizando o *CORP\install* conta. Abra **Dashboard do Gestor de servidor**.
2. Clique em de **para adicionar funções e funcionalidades** ligação no dashboard.

    ![Gestor de servidor - adicionar funções](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Selecione **seguinte** até chegar à **funcionalidades de servidor** secção.
4. No **funcionalidades**, selecione **Clustering de ativação pós-falha**.
5. Adicione quaisquer funcionalidades adicionais necessárias.
6. Clique em **instalar** para adicionar as funcionalidades.

Repita os passos na outros VM do SQL Server.

## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall">Configurar a firewall em cada VM do SQL Server

A solução requer as seguintes portas TCP para ser aberta na firewall:

- **SQL Server VM**:<br/>
   Porta 1433 para uma instância predefinida do SQL Server.
- **Sonda do Balanceador de carga do Azure:**<br/>
   Qualquer porta disponível. Exemplos utilizam frequentemente 59999.
- **Ponto final de espelhamento da base de dados:** <br/>
   Qualquer porta disponível. Exemplos utilizam frequentemente 5022.

As portas de firewall têm de ser abertas em ambas as VMs de SQL Server.

O método de abrir as portas depende a solução de firewall que utilizar. A secção seguinte explica como abrir portas na Firewall do Windows. Abra as portas necessárias em cada uma das suas VMs do SQL Server.

### <a name="open-a-tcp-port-in-the-firewall"></a>Abrir uma porta TCP na firewall

1. No primeiro servidor de SQL **iniciar** ecrã, iniciar **Firewall do Windows com segurança avançada**.
2. No painel esquerdo, selecione **regras de entrada**. No painel direito, clique em **nova regra**.
3. Para **tipo de regra**, escolha **porta**.
4. Para a porta, especifique **TCP** e escreva os números de porta adequado. Consulte o exemplo seguinte:

   ![Firewall do SQL Server](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Clique em **Seguinte**.
6. No **ação** página, mantenha **permitir a ligação** selecionada e, em seguida, clique em **seguinte**.
7. No **perfil** página, aceite as predefinições e, em seguida, clique em **seguinte**.
8. No **nome** página, especifique um nome de regra (tais como **pesquisa do Azure LB**) no **nome** caixa de texto e, em seguida, clique em **concluir**.

Repita estes passos na VM do segundo servidor de SQL.

## <a name="next-steps"></a>Passos seguintes

* [Criar um grupo de disponibilidade SQL Server Always On em máquinas virtuais do Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md)
