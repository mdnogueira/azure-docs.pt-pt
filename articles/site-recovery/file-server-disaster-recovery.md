---
title: Proteger um servidor de ficheiros utilizando o Azure Site Recovery
description: Este artigo descreve como proteger um servidor de ficheiros utilizando o Azure Site Recovery
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
ms.openlocfilehash: 78ce74450ce933e2aced4b6e62504373de7954f8
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="protect-a-file-server-using-azure-site-recovery"></a>Proteger um servidor de ficheiros utilizando o Azure Site Recovery 

O [do Azure Site Recovery](site-recovery-overview.md) serviço contribui para a sua estratégia de recuperação (BCDR) de continuidade e desastre negócio ao manter as suas aplicações empresariais a cópia de segurança e em execução disponíveis durante a falhas planeadas e não. Recuperação de site gere e orquestra a recuperação de desastre de máquinas no local e máquinas virtuais do Azure (VMs), incluindo a replicação, ativação pós-falha e recuperação de várias cargas de trabalho.

Este artigo descreve como proteger um servidor de ficheiros utilizando o Azure Site Recovery e outras recomendações de acordo com vários ambientes.     

- [Proteger máquinas de servidor de ficheiros do IaaS do Azure](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Proteger servidores de ficheiros no local](#replicate-an-onpremises-file-server-using-azure-site-recovery)


## <a name="file-server-architecture"></a>Arquitetura de servidor de ficheiros
O objetivo de um abra distribuído partilha de ficheiros de sistema consiste em fornecer um ambiente em que um grupo de utilizadores distribuídos geograficamente pode colaborar para trabalhar em ficheiros de forma eficiente e ser garantida que os respetivos requisitos de integridade são impostos. Um ecossistema de servidor de ficheiros típica no local que suporta um número elevado de utilizadores em simultâneo e um grande número de itens de conteúdo utilizar ficheiro de sistema de replicação distribuídos (DFSR) para replicação agendamento e limitação de largura de banda. DFSR utiliza um algoritmo de compressão conhecido como diferencial compressão remota (RDC), que podem ser utilizados de forma eficiente atualizar ficheiros através de uma rede de largura de banda limitada. Deteta inserções, remoção e rearrangements dos dados nos ficheiros, permitindo DFSR replicar apenas os blocos de ficheiros alterados quando são atualizados ficheiros. Também existem ambientes de servidor de ficheiros, onde são efetuadas cópias de segurança diárias em temporizações de pico, que se adaptar a necessidades de desastre e não existe nenhuma implementação de DFSR.

A topologia seguinte ilustra o ambiente de servidor de ficheiros com DFSR implementado.
                
![DFSR architexture](media/site-recovery-file-server/dfsr-architecture.JPG)

A referência de acima, vários servidores de ficheiros referido como membros, ativamente participam na replicação de ficheiros entre um grupo de replicação. O conteúdo na pasta replicada estará disponível para todos os clientes enviar pedidos para um dos membros, mesmo em caso de um dos membros ficar offline.

## <a name="disaster-recovery-recommendation-for-file-servers"></a>Recomendação de recuperação de desastres para servidores de ficheiros:

1.  Replicar servidor de ficheiros com o Azure Site Recovery: os servidores de ficheiros podem ser replicados para o Azure utilizando o Azure Site Recovery. Quando um ou mais do ficheiro servidores no local estão inacessível, a recuperação de VMs pode ser colocada cópias de segurança no Azure, o que pode então servirem pedidos de clientes, no local, fornecido não existe da conectividade VPN de Site a Site e do Active Directory configurado no Azure. Pode fazê-lo em caso de um ambiente de DFSR configurado ou um ambiente de servidor de ficheiros simples com nenhuma DFSR. 

2.  Expandir o DFSR para uma VM do Iaas do Azure:-num ambiente de servidor de ficheiros em cluster com o DFSR implementado, sugerida uma abordagem é para expandir o DFSR no local para o Azure. Uma máquina virtual do Azure, em seguida, é ativada para realizar a função de servidor de ficheiros. 

    Depois das dependências de conectividade VPN de Site a Site e o Active directory são processadas e de DFSR está no local, quando um ou mais do ficheiro servidores no local não estão acessível, os clientes ainda conseguir ligar à VM do Azure, o que irá servir os pedidos.

    Esta abordagem é sugerida caso as suas VMs tem configurações que não são suportadas pelo Azure Site Recovery, como por exemplo: disco de cluster partilhado que é por vezes, normalmente utilizado em ambientes de servidor de ficheiros.  DFSR também funciona bem em ambientes de pouca largura de banda com a taxa de rotatividade média. O custo adicional de ter uma VM do Azure, cópias de segurança e sempre executadas também tem de ser acomodado com esta.  

3.  Utilizar o serviço de sincronização de ficheiros do Azure para replicar os ficheiros: Se estiver a preparar da sua viagem para a nuvem, ou já estiver a utilizar uma VM do Azure, em seguida, sugerimos que a utilização do serviço de sincronização de ficheiros do Azure, que oferece a sincronizar completamente gerido de partilhas de ficheiros na nuvem que estão acessível v IA a norma da indústria [Server Message Block ](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)protocolo (SMB). Ficheiros do Azure, partilhas, em seguida, podem ser montadas em simultâneo na nuvem ou no local implementações do Windows, Linux e macOS. 

Diagrama, abaixo fornece uma representação pictorial diversificada easing enviados a decisão de que estratégia para utilizar para o seu ambiente de servidor de ficheiros.

![decisiontree](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-while-making-disaster-recovery-decision"></a>Fatores a considerar ao efetuar a decisão de recuperação de desastre

|Ambiente  |Recomendação  |Pontos a considerar |
|---------|---------|---------|
|Ambiente de servidor com/sem DFSR de ficheiros|   [Utilizar o Azure Site Recovery para replicação](#replicate-an-onpremises-file-servers-using-azure-site-recovery)   |    Recuperação de sites não suporta o cluster de disco partilhado, NAS. Se o seu ambiente utiliza qualquer uma destas configurações, utilize uma das outras abordagens conforme apropriado. <br> O Azure Site Recovery não suporta o SMB 3.0, o que significa que apenas quando as alterações efetuadas aos ficheiros são atualizadas na localização original do ficheiro será a VM replicada incorporar as alterações.
|Ambiente de servidor de ficheiros com o DFSR     |  [Expanda o DFSR para uma máquina virtual do IaaS do Azure:](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |     DFSR funciona bem em ambientes de largura de banda crunched extremamente, esta abordagem requer no entanto, uma VM do Azure, cópias de segurança e de todos os o tempo de execução. O custo da VM tem de ser tidas em conta no seu planeamento.         |
|VM do Iaas do Azure     |     [Sincronização de ficheiros do Azure](#use-azure-file-sync-service-to-replicate-your-files)   |     Num cenário de DR se estiver a utilizar a sincronização de ficheiros do Azure, durante a ativação pós-falha ações manuais tem de ser executadas para se certificar de que o partilhas de ficheiros como acessíveis de forma transparente para a máquina do cliente. AFS requer que a porta 445 para ser aberto a partir do computador cliente.     |


### <a name="site-recovery-support"></a>Suporte de recuperação de site
Como a replicação do Site Recovery desconhece de aplicação, as recomendações fornecidas aqui são esperadas para conter VERDADEIRO para os seguintes cenários:
| Origem    |Para um site secundário    |Para o Azure
|---------|---------|---------|
|Azure| -|Sim|
|Hyper-V|   Sim |Sim
|VMware |Sim|   Sim
|Servidor físico|   Sim |Sim
 

> [!IMPORTANT]
> Antes de continuar com qualquer um do abaixo três abordagens, certifique-se de que as seguintes dependências são tratadas:

**Conetividade site a Site**: direcionar tem de ser estabelecida ligação entre o site no local e a rede do Azure para permitir a comunicação entre servidores.  Este processo pode ser certificar-se através de uma ligação VPN Site a Site segura a uma rede Virtual do Azure Windows que será utilizado como o site de DR.  
Consulte: [ligação VPN de Site para Site estabelecer entre o site no local e de rede do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

**Do Active Directory**: DFSR depende do Active Directory.  Isto significa que a floresta do Active Directory com os controladores de domínio local é expandida para o site de DR no Azure. Mesmo se não estiver a utilizar DFSR se os utilizadores pretendidos tem de ser concedido / verificada para acesso como na maioria dos organização, estes passos têm de ser efetuada.
Consulte: [expandir o Active Directory no local para o Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Recomendação de recuperação de desastre para as máquinas virtuais do IaaS do Azure

Se estiver a configurar anf gerir a recuperação após desastre de servidores de ficheiros alojados em Vms do IaaS do Azure, pode escolher entre duas opções, com base em se pretende mover para [ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

1. [Utilizar a sincronização de ficheiros do Azure](#use-azure-file-sync-service-to-replicate-files-hosted-on-iaas-virtual-machine)
2. [Utilizar o Azure Site Recovery (Use Azure Site Recovery)](#replicate-an-iaas-file-server-virtual-machine-using-azure-site-recovery)

## <a name="use-azure-file-sync-service-to-replicate-files-hosted-on-iaas-virtual-machine"></a>Utilizar o serviço de sincronização de ficheiros do Azure para replicar ficheiros alojados numa máquina virtual IaaS

**Ficheiros do Azure** pode ser utilizado para completamente substituir ou complementar servidores de ficheiros tradicional no local ou de dispositivos NAS. As partilhas de Ficheiros do Azure também podem ser replicadas com o Azure File Sync em Servidores Windows, no local ou na cloud, para uma colocação em cache eficaz e distribuída dos dados no local onde estão a ser utilizados. Passos abaixo de detalhe a recomendação de DR para VMs do Azure que executam a mesma funcionalidade como servidores de ficheiros tradicional:
1.  Proteger máquinas utilizando o Azure Site Recovery utilizando os passos mencionados aqui
2.  Utilize a sincronização de ficheiros do Azure para replicar os ficheiros da VM que funciona como servidor de ficheiros, para a nuvem.
3.  Utilize a funcionalidade de plano de recuperação do Azure Site Recovery para adicionar scripts para [montar a partilha de ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) e aceder à partilha na sua máquina virtual.

Os passos abaixo descrevem brevemente como utilizar o serviço de sincronização de ficheiros do Azure:

1. [Criar uma conta de armazenamento no Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Se tiver escolhido o armazenamento georredundante com acesso de leitura (RA-GRS) para as contas do storage, irá obter acesso de leitura aos seus dados da região secundária em caso de desastre. Consulte o [estratégias de recuperação de desastre de partilha de ficheiros do Azure](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obter mais informações.

2. [Criar uma partilha de ficheiros](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share)

3. [Implementar a sincronização de ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) no seu servidor de ficheiros do Azure.

4. Criar um grupo de sincronização: os pontos finais dentro de um grupo de sincronização serão mantidos sincronizados entre si. Um grupo de sincronização tem de conter pelo menos um ponto final da nuvem, que representa uma partilha de ficheiros do Azure, e um ponto final do servidor, que representa um caminho num servidor do Windows.
5.  Os ficheiros serão agora ser mantidos sincronizados entre a partilha de ficheiros do Azure e o servidor no local.
6.  Em caso de desastre no seu ambiente no local, realizar enquanto a ativação pós-falha utilizando um plano de recuperação e adicione o script para [montar a partilha de ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) e aceder à partilha na sua máquina virtual.

### <a name="replicate-an-iaas-file-server-virtual-machine-using-azure-site-recovery"></a>Replicar o ficheiro servidor máquina virtual IaaS utilizando o Azure Site Recovery

Se tiver clientes no local ao aceder ao IaaS máquina de virtual do servidor de ficheiros efetuar os primeiro 2 passos bem, pessoa avance para o passo 3.

1. Estabelecer uma ligação VPN de Site a Site entre o site no local e de rede do Azure.  
2. Expanda o Active Directory no local.
3. [Configurar a recuperação após desastre](azure-to-azure-tutorial-enable-replication.md) para a máquina do servidor de ficheiros IaaS para uma região secundária.


Para obter mais informações sobre recuperação de desastres para uma região secundária, consulte [aqui](concepts-azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-using-azure-site-recovery"></a>Replicar um servidor de ficheiros no local utilizando o Azure Site Recovery
A abaixo da replicação de detalhe de passos para uma VM de VMware, para obter os passos replicar uma VM de Hyper-V, consulte aqui.

1.  [Preparar os recursos do Azure](tutorial-prepare-azure.md) para a replicação de máquinas no local.
2.  Estabelecer uma ligação VPN de Site a Site entre o site no local e de rede do Azure.  
3. Expanda o Active Directory no local.
4.  [Preparar servidores do VMware no local](tutorial-prepare-on-premises-vmware.md).
5.  [Configurar a recuperação após desastre](tutorial-vmware-to-azure.md) para o Azure para VMs no local.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Expanda o DFSR para uma máquina virtual do IaaS do Azure:

1.  Estabelecer uma ligação VPN de Site a Site entre o site no local e de rede do Azure. 
2.  Expanda o Active Directory no local.
3.  [Criar e aprovisionar um servidor de ficheiros VM](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) no Windows Azure Virtual Network.

    Certifique-se de que a máquina virtual é adicionada à mesma Windows Azure Virtual Network, que tem a conectividade entre com o ambiente no local. 

4.  Instalar e [configurar a replicação DFS](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) no Windows Server.

5.  [Implementar um espaço de nomes DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6.  Com o espaço de nomes DFS implementado, a ativação pós-falha das pastas partilhadas de produção para sites de DR pode ser feita atualizando os destinos da pasta de espaço de nomes DFS.  Depois destas alterações de espaço de nomes DFS replicar através do Active Directory, os utilizadores estão ligados transparente os destinos de pasta adequados.

## <a name="use-azure-file-sync-service-to-replicate-your-on-premises-files"></a>Utilize o serviço de sincronização de ficheiros do Azure para replicar os ficheiros no local:
Utilizar o serviço de sincronização de ficheiros do Azure, pode replicar os ficheiros pretendidos na nuvem, para que na eventualidade de ocorrer um desastre e indisponibilidade do seu servidor de ficheiros no local, pode montar as localizações de ficheiros pretendido da nuvem para e continuar a pedidos de serviço do as máquinas de cliente.
É a abordagem sugerida de integrar a sincronização de ficheiros do Azure com o Azure Site Recovery
1.  Proteger as máquinas de servidor de ficheiros utilizando o Azure Site Recovery utilizando os passos mencionados [aqui](tutorial-vmware-to-azure.md)
2.  Utilize a sincronização de ficheiros do Azure para replicar os ficheiros da máquina que funciona como um servidor de ficheiros, para a nuvem.
3.  Utilize a funcionalidade de plano de recuperação do Azure Site Recovery para adicionar scripts para montar a partilha de ficheiros do Azure na qual foi através de VM do servidor de ficheiros no Azure.

O abaixo os detalhes de passos com o serviço de sincronização de ficheiros do Azure:

1. [Criar uma conta de armazenamento no Azure](https://docs.microsoft.com/en-us/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Se tiver escolhido o armazenamento de georredundante de acesso de leitura (RA-GRS) (recomendado) para as contas do storage, terá acesso de leitura aos seus dados da região secundária em caso de desastre. Consulte o [estratégias de recuperação de desastre de partilha de ficheiros do Azure](https://docs.microsoft.com/en-us/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obter mais informações.

2. [Criar uma partilha de ficheiros](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share)

3. [Implementar a sincronização de ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) no seu servidor de ficheiros no local.

4. Criar um grupo de sincronização: os pontos finais dentro de um grupo de sincronização serão mantidos sincronizados entre si. Um grupo de sincronização tem de conter pelo menos um ponto final da nuvem, que representa uma partilha de ficheiros do Azure, e um ponto final do servidor, que representa um caminho no Windows Server no local.

1. Os ficheiros serão agora ser mantidos sincronizados entre a partilha de ficheiros do Azure e o servidor no local.
6.  Em caso de desastre no seu ambiente no local, realizar enquanto a ativação pós-falha utilizando um plano de recuperação e adicione o script para montar a partilha de ficheiros do Azure e aceder à partilha na sua máquina virtual.

> [!NOTE]
> Certifique-se de que a porta 445 está aberta: ficheiros do Azure utiliza o protocolo SMB. O SMB comunica através da porta TCP 445 - verifique se a firewall não está a bloquear as portas TCP 445 no computador cliente.


## <a name="doing-a-test-failover"></a>Efetuar uma ativação pós-falha de teste

1.  Aceda ao portal do Azure e selecione o Cofre de serviço de recuperação.
2.  Clique no plano de recuperação criado para o ambiente de servidor de ficheiros.
3.  Clique em "Ativação pós-falha de teste".
4.  Selecione o ponto de recuperação e de rede virtual do Azure para iniciar o processo de ativação pós-falha de teste.
5.  Assim que o ambiente secundário está ativo, pode realizar as validações.
6.  Depois de validações estiverem concluídas, pode clicar em "Limpeza ativação pós-falha de teste" no plano de recuperação e o ambiente de ativação pós-falha de teste é limpa.

Para mais informações sobre como efetuar a ativação pós-falha de teste, consulte [aqui](site-recovery-test-failover-to-azure.md).

Para obter orientações sobre a fazer a ativação pós-falha de teste do AD e DNS, consulte [considerações de ativação pós-falha do AD e DNS](site-recovery-active-directory.md).

## <a name="doing-a-failover"></a>Efetuar uma ativação pós-falha

1.  Aceda ao portal do Azure e selecione o Cofre dos serviços de recuperação.
2.  Clique no plano de recuperação criado para o ambiente de servidor de ficheiros.
3.  Clique em 'Failover'.
4.  Selecione o ponto de recuperação para iniciar o processo de ativação pós-falha.

Para mais informações sobre como efetuar a ativação pós-falha de teste, consulte [aqui](site-recovery-failover.md).
