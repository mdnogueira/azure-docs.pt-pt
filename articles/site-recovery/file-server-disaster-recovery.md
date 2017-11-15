---
title: Proteger um servidor de ficheiros ao utilizar o Azure Site Recovery
description: Este artigo descreve como ajudar a proteger um servidor de ficheiros utilizando o Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: cc585d6add9b9c5ce7f3379aeaf5ec79f5c61d51
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Proteger um servidor de ficheiros ao utilizar o Azure Site Recovery 

O [do Azure Site Recovery](site-recovery-overview.md) serviço contribui para a sua estratégia de recuperação (BCDR) de continuidade e desastre negócio ao manter as suas aplicações empresariais durante falhas planeadas e não. Recuperação de site gere e orquestra a recuperação de desastre de máquinas no local e máquinas virtuais do Azure (VMs), incluindo a replicação, ativação pós-falha e recuperação de várias cargas de trabalho.

Este artigo descreve como ajudar a proteger um servidor de ficheiros utilizando o Azure Site Recovery e outras recomendações de acordo com vários ambientes.

## <a name="file-server-architecture"></a>Arquitetura de servidor de ficheiros
Um sistema de partilha de ficheiros aberto e distribuído, fornece um ambiente onde distribuídos geograficamente os utilizadores podem colaborar nos ficheiros sem comprometer a requisitos de integridade. 

Um ecossistema de servidor de ficheiros típica no local suporta um número elevado de utilizadores em simultâneo e itens de conteúdo utiliza o ficheiro de sistema de replicação distribuídos (DFSR) para replicação agendamento e limitação de largura de banda. DFSR utiliza o algoritmo de compressão de compressão diferencial remota (RDC), que pode ser utilizado de forma eficiente atualizar ficheiros através de uma rede de largura de banda limitada. A RDC Deteta inserções, remoção e rearrangements dos dados em ficheiros. Permite que DFSR replicar apenas os blocos de ficheiros alterados quando são atualizados ficheiros. 

Em alguns ambientes de servidor de ficheiros, são efetuadas cópias de segurança diárias em períodos de pico para recuperação após desastre. Estes ambientes, não utilizem DFSR.

A seguinte topologia ilustra um ambiente de servidor de ficheiros com DFSR implementado:
                
![Arquitetura DFSR](media/site-recovery-file-server/dfsr-architecture.JPG)

Na figura, vários servidores de ficheiros (referidos como membros) ativamente participam na replicação de ficheiros entre um grupo de replicação. O conteúdo na pasta replicada está disponível para todos os clientes que estão a enviar pedidos para um dos membros, mesmo se um dos membros fica offline.

## <a name="disaster-recovery-strategies-for-file-servers"></a>Estratégias de recuperação de desastres para servidores de ficheiros

- **Replicar num servidor de ficheiros para o Azure utilizando o Azure Site Recovery**: quando um ou mais dos servidores de ficheiros no local não estão acessíveis, a recuperação de VMs pode ser colocada no Azure. A recuperação de VMs, em seguida, pode servir pedidos de clientes, no local, se existir conectividade VPN de site a site e do Active Directory está configurado no Azure. Pode fazê-lo num ambiente de DFSR-configurada ou num ambiente de servidor de ficheiros simples com nenhuma DFSR. 

- **Expandir o DFSR para uma VM do IaaS do Azure**: num ambiente de servidor de ficheiros em cluster com o DFSR implementado, sugerida uma abordagem é para expandir o DFSR no local para o Azure. Uma máquina virtual do Azure, em seguida, pode realizar a função de servidor de ficheiros. 

    Depois das dependências de conectividade VPN de site a site e o Active Directory são processadas e DFSR no local, quando um ou mais dos servidores de ficheiros no local não estão acessíveis, os clientes ainda conseguir ligar à VM do Azure. A VM do Azure serve os pedidos.

    Sugerimos que esta abordagem se as VMs têm configurações que não suportam o Azure Site Recovery. Um exemplo de uma configuração deste tipo é um disco de cluster partilhado que é normalmente utilizado em ambientes de servidor de ficheiros. DFSR também funciona bem em ambientes de pouca largura de banda com a taxa de rotatividade média. Com esta abordagem, terá de acomodar o custo adicional de ter uma VM do Azure, cópias de segurança e sempre executadas.  

- **Utilizar a sincronização de ficheiros do Azure para replicar os ficheiros**: Se estiver a preparar da sua viagem para a nuvem, ou se já estiver a utilizar uma VM do Azure, sugerimos a utilização de sincronização de ficheiros do Azure. Este serviço oferece a sincronizar completamente gerido de partilhas de ficheiros na nuvem que estão acessíveis através da norma da indústria [Server Message Block ](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)protocolo (SMB). Pode, em seguida, partilhas de ficheiros do Azure de montagem em simultâneo por nuvem ou no local as implementações do Windows, Linux e macOS. 

O diagrama a seguir pode ajudá-lo a decidir que estratégia a utilizar para o seu ambiente de servidor de ficheiros:

![árvore de decisões](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-for-making-a-disaster-recovery-decision"></a>Fatores para tomar uma decisão de recuperação após desastre

|Ambiente  |Recomendação  |Pontos a considerar |
|---------|---------|---------|
|Ambiente de servidor de ficheiros com ou sem DFSR|   [Utilizar o Azure Site Recovery para replicação](#replicate-an-on-premises-file-server-by-using-azure-site-recovery)   |    Recuperação de sites não suporta clusters de disco partilhado ou NAS. Se o seu ambiente utiliza uma das seguintes configurações, utilize uma das outras abordagens conforme apropriado. <br> O Azure Site Recovery não suporta o SMB 3.0. A VM replicada será incorporar as alterações efetuadas aos ficheiros apenas quando as alterações são atualizadas na localização original do ficheiro. 
|Ambiente de servidor de ficheiros com o DFSR     |  [Expandir o DFSR para uma máquina virtual do IaaS do Azure](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |      DFSR funciona bem em ambientes crunched de largura de banda. No entanto, esta abordagem requer que tenha uma VM do Azure em execução sempre. O planeamento tem em conta o custo da VM.         |
|VM do IaaS do Azure     |     [Utilizar a sincronização de ficheiros do Azure](#use-azure-file-sync-to-replicate-your-on-premises-files)   |     Num cenário de recuperação após desastre, se estiver a utilizar a sincronização de ficheiros do Azure durante a ativação pós-falha, tem de colocar as ações manuais para se certificar de que as partilhas de ficheiros se encontram acessíveis de forma transparente para a máquina do cliente. Sincronização de ficheiros do Azure requer que a porta 445 para ser aberto a partir do computador cliente.     |


### <a name="site-recovery-support"></a>Suporte de recuperação de site
Como a replicação do Site Recovery desconhece de aplicação, as recomendações fornecidas aqui são esperadas para conter VERDADEIRO para os seguintes cenários:
| Origem    |Para um site secundário    |Para o Azure
|---------|---------|---------|
|Azure| -|Sim|
|Hyper-V|   Sim |Sim
|VMware |Sim|   Sim
|Servidor físico|   Sim |Sim
 

> [!IMPORTANT]
> Antes de continuar com qualquer uma das seguintes abordagens, não se esqueça de dependências de endereço.

**Conetividade site a Site**: tem de ser estabelecida uma ligação direta entre o site no local e a rede do Azure para permitir a comunicação entre servidores. Pode utilizar uma ligação de VPN de site a site segura a uma rede virtual do Azure que será utilizada como o site de recuperação após desastre. Para obter mais informações, consulte [criar uma ligação Site a Site no portal do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Do Active Directory**: DFSR depende do Active Directory. Isto significa que a floresta do Active Directory com os controladores de domínio local é expandida para o site de recuperação após desastre no Azure. Mesmo se não estiver a utilizar DFSR, se os utilizadores pretendidos têm de ser concedido/verificada para acesso, tem de efetuar estes passos.
Para obter mais informações, consulte [expanda no local do Active Directory para o Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendations-for-azure-iaas-virtual-machines"></a>Recomendações de recuperação de desastre para as máquinas virtuais do IaaS do Azure

Se estiver a configurar e gerir a recuperação após desastre de servidores de ficheiros alojados em VMs do IaaS do Azure, pode escolher entre duas opções: sincronização de ficheiros do Azure e o Azure Site Recovery. A decisão depende se pretende mover para [ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

### <a name="use-azure-file-sync-to-replicate-files-hosted-on-iaas-virtual-machines"></a>Utilizar a sincronização de ficheiros do Azure para replicar os ficheiros alojados em máquinas virtuais de IaaS

Pode utilizar ficheiros do Azure para completamente substituir ou complementar servidores de ficheiros tradicional no local ou de dispositivos NAS. Partilhas de ficheiros do Azure também podem ser replicadas com sincronização de ficheiros do Azure para os servidores do Windows, no local ou na nuvem, para performant e caching distribuído dos dados em que está a ser utilizado. 

A recomendação de recuperação após desastre para as VMs do Azure que executam a mesma função que os servidores de ficheiros tradicional de detalhe os seguintes passos:
1. Proteger máquinas ao utilizar o Azure Site Recovery, os passos aqui mencionados.

2. Utilize a sincronização de ficheiros do Azure para replicar os ficheiros da VM que funciona como servidor de ficheiros, para a nuvem.

3. Utilize o [plano de recuperação](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-create-recovery-plans) funcionalidade do Azure Site Recovery para adicionar scripts para [montar a partilha de ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) e aceder à partilha na sua máquina virtual.

Os passos seguintes descrevem brevemente como utilizar o serviço de sincronização de ficheiros do Azure:

1. [Criar uma conta de armazenamento no Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Se tiver escolhido o armazenamento georredundante com acesso de leitura para as contas do storage, irá obter acesso de leitura aos seus dados da região secundária em caso de desastre. Para obter mais informações, consulte [estratégias de recuperação de desastre de partilha de ficheiros do Azure](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

2. [Criar uma partilha de ficheiros](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

3. [Implementar a sincronização de ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) no seu servidor de ficheiros do Azure.

4. Crie um grupo de sincronização. Pontos finais dentro de um grupo de sincronização e irão permanecer sincronizados com entre si. Um grupo de sincronização tem de conter ponto final de pelo menos uma nuvem, que representa uma partilha de ficheiros do Azure, e um ponto final do servidor, que representa um caminho num servidor do Windows.  
    Os ficheiros agora permanecerão sincronizados entre a partilha de ficheiros do Azure e o servidor no local.

5. Se ocorrer um desastre no seu ambiente no local, execute uma ativação pós-falha utilizando um plano de recuperação. Adicione o script para [montar a partilha de ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) e aceder à partilha na sua máquina virtual.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-azure-site-recovery"></a>Replicar a máquina de virtual de servidor de ficheiros com o IaaS através da utilização do Azure Site Recovery

Se tiver clientes no local que aceder à máquina virtual de servidor de ficheiro de IaaS, execute os seguintes passos. Caso contrário, execute apenas o passo 3.

1. Estabelece uma ligação de VPN de site a site entre o site no local e a rede do Azure.  

2. Expanda o Active Directory no local.

3. [Configurar a recuperação após desastre](azure-to-azure-tutorial-enable-replication.md) para a máquina do servidor de ficheiros IaaS para uma região secundária.

Para obter mais informações sobre recuperação de desastres para uma região secundária, consulte [arquitetura da replicação do Azure para o Azure](concepts-azure-to-azure-architecture.md).

## <a name="disaster-recovery-recommendations-for-on-premises-virtual-machines"></a>Recomendações de recuperação de desastres para máquinas virtuais no local 

### <a name="replicate-an-on-premises-file-server-by-using-azure-site-recovery"></a>Replicar um servidor de ficheiros no local utilizando o Azure Site Recovery
Os seguintes passos de detalhe replicação para uma VM de VMware. Para obter os passos replicar uma VM de Hyper-V, consulte [configurar a recuperação de desastre de VMs de Hyper-V no local para Azure](https://docs.microsoft.com/en-us/azure/site-recovery/tutorial-hyper-v-to-azure).

1. [Preparar os recursos do Azure](tutorial-prepare-azure.md) para a replicação de máquinas no local.

2. Estabelece uma ligação de VPN de site a site entre o site no local e a rede do Azure.  

3. Expanda o Active Directory no local.

4. [Preparar servidores do VMware no local](tutorial-prepare-on-premises-vmware.md).

5. [Configurar a recuperação após desastre](tutorial-vmware-to-azure.md) para o Azure para VMs no local.

### <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Expandir o DFSR para uma máquina virtual do IaaS do Azure

1. Estabelece uma ligação de VPN de site a site entre o site no local e a rede do Azure. 

2. Expanda o Active Directory no local.

3. [Criar e aprovisionar um servidor de ficheiros VM](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) na rede virtual do Azure.  
    Certifique-se de que a máquina virtual é adicionada à mesma rede virtual do Azure, que tenha conectividade em vários locais com o ambiente no local. 

4. Instalar e [configurar a replicação DFS](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) no Windows Server.

5. [Implementar um espaço de nomes DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).

6. Com o espaço de nomes DFS implementado, pode falhar pastas partilhadas de produção para locais de recuperação após desastre, atualizando os destinos de pasta de espaço de nomes DFS. Depois destas DFS alterações de espaço de nomes são replicadas através do Active Directory, os utilizadores estão ligados transparente para os destinos de pasta adequados.

### <a name="use-azure-file-sync-to-replicate-your-on-premises-files"></a>Utilizar a sincronização de ficheiros do Azure para replicar os ficheiros no local
Ao utilizar o serviço de sincronização de ficheiros do Azure, pode replicar os ficheiros pretendidos para a nuvem. Na eventualidade de ocorrer um desastre e indisponibilidade do seu servidor de ficheiros no local, em seguida, pode montar as localizações de ficheiros pretendido da nuvem e continuar a pedidos de serviço, as máquinas de cliente.

É a abordagem sugerida de integrar a sincronização de ficheiros do Azure com o Azure Site Recovery:
1. Proteger as máquinas de servidor de ficheiros ao utilizar o Azure Site Recovery. Siga os passos no [configure a recuperação de desastre para o Azure para as VMs de VMware no local](tutorial-vmware-to-azure.md).

2. Utilize a sincronização de ficheiros do Azure para replicar os ficheiros da máquina que funciona como um servidor de ficheiros, para a nuvem.

3. Utilize a funcionalidade de plano de recuperação no Azure Site Recovery para adicionar scripts para montar a partilha de ficheiros do Azure no servidor de ficheiros de efetuada a ativação pós-falha VM no Azure.

O detalhe os passos seguintes, utilizando o serviço de sincronização de ficheiros do Azure:

1. [Criar uma conta de armazenamento no Azure](https://docs.microsoft.com/en-us/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Se tiver escolhido o armazenamento georredundante com acesso de leitura (recomendado) para as contas do storage, terá acesso de leitura aos seus dados da região secundária em caso de desastre. Para obter mais informações, consulte [estratégias de recuperação de desastre de partilha de ficheiros do Azure](https://docs.microsoft.com/en-us/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

2. [Criar uma partilha de ficheiros](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

3. [Implementar a sincronização de ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) no seu servidor de ficheiros no local.

4. Crie um grupo de sincronização. Pontos finais dentro de um grupo de sincronização e irão permanecer sincronizados com entre si. Um grupo de sincronização tem de conter ponto final de pelo menos uma nuvem, que representa uma partilha de ficheiros do Azure, e o ponto final de um servidor, que representa um caminho no Windows server no local.  
    Os ficheiros agora permanecerão sincronizados entre a partilha de ficheiros do Azure e o servidor no local.

5. Se ocorrer um desastre no seu ambiente no local, execute uma ativação pós-falha utilizando um plano de recuperação. Adicione o script para montar a partilha de ficheiros do Azure e aceder à partilha na sua máquina virtual.

> [!NOTE]
> Certifique-se de que a porta 445 está aberta. Ficheiros do Azure utiliza o protocolo SMB. SMB comunica através da porta TCP 445. Verifique se a firewall está a bloquear a porta 445 do computador cliente.


## <a name="perform-a-test-failover"></a>Execute uma ativação pós-falha de teste

1. Aceda ao portal do Azure e selecione o Cofre dos serviços de recuperação.

2. Selecione o plano de recuperação criado para o ambiente de servidor de ficheiros.

3. Selecione **ativação pós-falha de teste**.

4. Selecione o ponto de recuperação e a rede virtual do Azure para iniciar o processo de ativação pós-falha de teste.

5. Quando o ambiente secundário, execute as validações.

6. Quando as validações estiverem concluídas, pode selecionar **ativação pós-falha de teste de limpeza** no plano de recuperação e ativação pós-falha de teste ambiente limpos.

Para obter mais informações sobre como efetuar uma ativação pós-falha de teste, consulte [testar a ativação pós-falha para o Azure Site Recovery](site-recovery-test-failover-to-azure.md).

Para obter orientações sobre a efetuar ativação pós-falha de teste para o Active Directory e DNS, consulte [considerações de ativação pós-falha do Active Directory e DNS](site-recovery-active-directory.md).

## <a name="perform-a-failover"></a>Execute uma ativação pós-falha

1. Aceda ao portal do Azure e selecione o Cofre dos serviços de recuperação.

2. Selecione o plano de recuperação criado para o ambiente de servidor de ficheiros.

3. Selecione **ativação pós-falha**.

4. Selecione o ponto de recuperação para iniciar o processo de ativação pós-falha.

Para obter mais informações sobre como efetuar uma ativação pós-falha, consulte [ativação pós-falha na recuperação de Site](site-recovery-failover.md).
