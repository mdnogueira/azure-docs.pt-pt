---
title: Expandir no local Always On nos grupos de disponibilidade para o Azure | Microsoft Docs
description: "Este tutorial utiliza recursos criados com o modelo de implementação clássica e descreve como utilizar o Assistente de adicionar a réplica no SQL Server Management Studio (SSMS) para adicionar uma réplica sempre no grupo de disponibilidade no Azure."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 50326a093adaf3558c56dfd0b38544f0e60be460
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Expandir no local Always On nos grupos de disponibilidade para o Azure
Grupos de disponibilidade Always fornecer elevada disponibilidade para grupos de base de dados, adicionando as réplicas secundárias. Permitem a estas réplicas efetuar a ativação pós-falha de bases de dados em caso de falha. Além disso, podem ser utilizados para a descarga de cargas de trabalho de leitura ou tarefas de cópia de segurança.

Pode expandir grupos de disponibilidade no local para o Microsoft Azure ao aprovisionamento de um ou mais VMs do Azure com o SQL Server e, em seguida, adicioná-los como réplicas para os grupos de disponibilidade no local.

Este tutorial parte do princípio de que tem o seguinte:

* Uma subscrição ativa do Azure. Pode [inscrever-se numa avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Existente sempre no grupo de disponibilidade no local. Para obter mais informações sobre grupos de disponibilidade, consulte [em grupos de disponibilidade Always](https://msdn.microsoft.com/library/hh510230.aspx).
* Conectividade entre a rede no local e a sua rede virtual do Azure. Para obter mais informações sobre como criar esta rede virtual, consulte [criar uma ligação Site a Site utilizando o portal do Azure (clássica)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

## <a name="add-azure-replica-wizard"></a>Adicionar Assistente de réplica do Azure
Esta secção mostra como utilizar o **Adicionar Assistente do Azure réplica** para expandir a sua solução sempre no grupo de disponibilidade para incluir as réplicas do Azure.

> [!IMPORTANT]
> O **Adicionar Assistente do Azure réplica** suporta apenas máquinas virtuais criadas com o modelo de implementação clássica. Novas implementações de VM devem utilizar o modelo do Resource Manager mais recente. Se estiver a utilizar as VMs com o Resource Manager, em seguida, tem de adicionar manualmente a réplica secundária do Azure, utilizando commmands Transact-SQL (não apresentada aqui). Este assistente não irá funcionar no cenário de Gestor de recursos.

1. De dentro do SQL Server Management Studio, expanda **sempre na disponibilidade elevada** > **grupos de disponibilidade** > **[nome do seu grupo de disponibilidade]**.
2. Clique com botão direito **réplicas de disponibilidade**, em seguida, clique em **Adicionar réplica**.
3. Por predefinição, o **réplica adicionar ao Assistente do grupo de disponibilidade** é apresentado. Clique em **Seguinte**.  Se tiver selecionado o **não mostrar esta página novamente** opção na parte inferior da página durante um lançamento anterior deste assistente, este ecrã não será apresentada.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Será necessário para ligar a todas as réplicas secundárias existentes. Pode clicar em **ligar...** junto a cada réplica ou pode clicar em **ligar todos os...** na parte inferior do ecrã. Após a autenticação, clique em **seguinte** para avançar para o ecrã seguinte.
5. No **especificar réplicas** página, vários separadores são apresentados na parte superior: **réplicas**, **pontos finais**, **preferências de cópia de segurança**, e **escuta**. Do **réplicas** separador, clique em **Adicionar réplica do Azure...** para iniciar o Assistente para Adicionar réplica do Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Selecione um certificado de gestão do Azure existente do arquivo de certificados local do Windows se tiver instalado um antes. Selecione ou introduza o id de uma subscrição do Azure, se tiver utilizado um antes. Pode clicar em transferência para transferir e instalar um certificado de gestão do Azure e transferir a lista de subscrições através de uma conta do Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Preencherá a cada campo na página com os valores que serão utilizados para criar o Azure Máquina Virtual (VM) que irá alojar a réplica.
   
   | Definição | Descrição |
   | --- | --- |
   | **Imagem** |Selecione a combinação pretendida do SO e SQL Server |
   | **Tamanho da VM** |Selecione o tamanho da VM que melhor se adapta às suas necessidades de negócio |
   | **Nome da VM** |Especifique um nome exclusivo para a nova VM. O nome tem de conter entre 3 e 15 carateres, pode conter apenas letras, números e hífenes e tem de começar com uma letra e terminar com uma letra ou número. |
   | **Nome de utilizador VM** |Especifique um nome de utilizador que irá tornar-se a conta de administrador na VM |
   | **Palavra-passe de administrador VM** |Especifique uma palavra-passe para a nova conta |
   | **Confirmar palavra-passe** |Confirme a palavra-passe da conta nova |
   | **Rede Virtual** |Especifique a rede virtual do Azure que deve utilizar a nova VM. Para obter mais informações sobre redes virtuais, consulte [descrição geral de rede Virtual](../../../virtual-network/virtual-networks-overview.md). |
   | **Sub-rede de rede virtual** |Especifique a sub-rede de rede virtual que deve utilizar a nova VM |
   | **Domínio** |Confirmar que o valor pré-preenchidos para o domínio está correto |
   | **Nome de utilizador de domínio** |Especifique uma conta que está no grupo de administradores locais em nós de local cluster |
   | **Palavra-passe** |Especifique a palavra-passe para o nome de utilizador de domínio |
8. Clique em **OK** para validar as definições de implementação.
9. Em seguida são apresentados os termos legais. Leia e clique em **OK** se aceitar estes termos.
10. O **especificar réplicas** página é apresentada. Verifique as definições para o novo réplica do Azure no **réplicas**, **pontos finais**, e **preferências de cópia de segurança** separadores. Modificar as definições para satisfazer os seus requisitos empresariais.  Para obter mais informações sobre os parâmetros contidos nestes separadores, consulte [especificar réplicas página (disponibilidade Assistente novo grupo de Assistente/Adicionar réplica)](https://msdn.microsoft.com/library/hh213088.aspx). Tenha em atenção que os serviços de escuta não não possível criar utilizando o separador de serviço de escuta para grupos de disponibilidade que contêm as réplicas do Azure. Além disso, se já tiver sido criado um serviço de escuta antes de iniciar o assistente, irá receber uma mensagem a indicar que não é suportado no Azure. Iremos abordar a como criar os serviços de escuta no **criar um serviço de escuta do grupo de disponibilidade** secção.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Clique em **Seguinte**.
12. Selecione o método de sincronização de dados que pretende utilizar no **selecionar sincronização de dados inicial** página e clique em **seguinte**. Para a maioria dos cenários, selecione **completo de dados de sincronização**. Para obter mais informações sobre os métodos de sincronização de dados, consulte [selecione inicial dados página de sincronização (sempre na disponibilidade grupo Assistentes)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Reveja os resultados no **validação** página. Corrigir problemas pendentes e volte a executar a validação, se necessário. Clique em **Seguinte**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Reveja as definições no **resumo** página, em seguida, clique em **concluir**.
15. Iniciar o processo de aprovisionamento. Quando o assistente ser concluído com êxito, clique em **fechar** para sair do assistente.

> [!NOTE]
> O Assistente para Adicionar réplica do Azure cria um ficheiro de registo no Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Este ficheiro de registo pode ser utilizado para resolver implementações de réplica do Azure falhou. Se o assistente não consegue executar qualquer ação, todas as operações anteriores são revertidas, incluindo a eliminar a VM aprovisionada.
> 
> 

## <a name="create-an-availability-group-listener"></a>Criar um serviço de escuta do grupo de disponibilidade
Depois de criar o grupo de disponibilidade, deve criar um serviço de escuta para clientes que ligam às réplicas. Encaminham as ligações recebidas para o site primário ou de uma réplica secundária só de leitura. Para obter mais informações sobre os serviços de escuta, consulte [configurar um serviço de escuta do ILB para grupos de disponibilidade Always no Azure](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>Passos seguintes
Além de utilizar o **Adicionar Assistente do Azure réplica** para expandir o seu sempre no grupo de disponibilidade para o Azure, pode também mover algumas cargas de trabalho do servidor SQL completamente no Azure. Para começar a utilizar, consulte o artigo [aprovisionamento de uma Máquina Virtual do SQL Server no Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Para outros tópicos relacionados com a executar o SQL Server em VMs do Azure, consulte [do SQL Server em Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

