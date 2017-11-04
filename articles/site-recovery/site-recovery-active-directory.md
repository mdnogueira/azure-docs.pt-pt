---
title: Proteger o Active Directory e DNS com o Azure Site Recovery | Microsoft Docs
description: "Este artigo descreve como implementar uma solução de recuperação após desastre para o Active Directory utilizando o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: af1d9b26-1956-46ef-bd05-c545980b72dc
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 7/20/2017
ms.author: pratshar
ms.openlocfilehash: 197441fc24c178695d4eada6db59f503b21672ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>Proteger o Active Directory e DNS com o Azure Site Recovery
As aplicações empresariais, tais como o SharePoint, Dynamics AX e SAP dependem do Active Directory e uma infraestrutura DNS para funcionar corretamente. Quando cria uma solução de recuperação após desastre para aplicações, é importante lembrar-se de que precisa de proteger e recuperar o Active Directory e DNS antes dos outros componentes de aplicação, para garantir que coisas função corretamente quando ocorrer um desastre.

Recuperação de sites é um serviço Azure que permite a recuperação de desastre através da orquestração da replicação, ativação pós-falha e recuperação de máquinas virtuais. Recuperação de sites suporta um número de cenários de replicação para proteger de forma consistente, perfeitamente máquinas virtuais de ativação pós-falha e aplicações para nuvens, public, private ou fornecedor de alojamento.

Utilizar a recuperação de sites, pode criar um plano de recuperação após desastre automatizado completa para o Active Directory. Quando ocorrem interrupções, pode iniciar uma ativação pós-falha em segundos a partir de qualquer lugar e obter do Active Directory em execução dentro de alguns minutos. Se tiver implementado o Active Directory para várias aplicações, tais como o SharePoint e SAP no seu site primário e que pretende efetuar a ativação pós-falha completa do local, pode efetuar a ativação pós-falha do Active Directory utilizando o primeiro Site Recovery e, em seguida, efetuar a ativação pós-falha de outras aplicações utilizando planos de recuperação específico da aplicação.

Este artigo explica como criar uma solução de recuperação após desastre para o Active Directory, como efetuar planeada, não planeada e as ativações pós-falha de teste utilizando um plano de recuperação de um clique, configurações suportadas e pré-requisitos.  Deve estar familiarizado com o Active Directory e o Azure Site Recovery antes de começar.

## <a name="replicating-domain-controller"></a>Replicar o controlador de domínio

Terá da configuração [replicação do Site Recovery](#enable-protection-using-site-recovery) , pelo menos, uma máquina virtual que aloja o controlador de domínio e DNS. Se tiver [vários controladores de domínio](#environment-with-multiple-domain-controllers) no seu ambiente, para além de replicar as máquinas de controlador de domínio com a recuperação de Site também teria de configurar um [docontroladordedomínioadicional](#protect-active-directory-with-active-directory-replication) no site de destino (Azure ou num datacenter secundário no local). 

### <a name="single-domain-controller-environment"></a>Ambiente de controlador de domínio único
Se tiver algumas aplicações e apenas um único controlador de domínio e que pretende efetuar a ativação pós-falha de todo o local em conjunto, em seguida, recomendamos que utilize a recuperação de sites para replicar o controlador de domínio para o site secundário (se está a efetuar a ativação pós-falha para o Azure ou para uma secundária site). O mesmo domínio de máquina virtual de controlador/DNS pode ser utilizada para foram replicadas [ativação pós-falha de teste](#test-failover-considerations) bem.

### <a name="environment-with-multiple-domain-controllers"></a>Ambiente com vários controladores de domínio
Se tiver muitas aplicações e não existe mais do que um controlador de domínio no ambiente ou se pretender efetuar a ativação pós-falha algumas aplicações de cada vez, recomendamos que, além de replicar as máquinas de controlador de domínio com a recuperação de Site também configurar um [controlador de domínio adicional](#protect-active-directory-with-active-directory-replication) no site de destino (Azure ou num datacenter secundário no local). Para [ativação pós-falha de teste](#test-failover-considerations), utilize o controlador de domínio replicado pela recuperação de sites e para ativação pós-falha, o controlador de domínio adicionais no site de destino. 


As secções seguintes explicam como ativar a proteção para um controlador de domínio no Site Recovery e como configurar um controlador de domínio no Azure.

## <a name="prerequisites"></a>Pré-requisitos
* Uma implementação no local do servidor do Active Directory e DNS.
* Um cofre dos serviços de recuperação de Site do Azure de uma subscrição do Microsoft Azure.
* Se estiver a replicar para o Azure, execute a ferramenta de avaliação de preparação de Máquina Virtual do Azure em VMs para se certificar de que está a compatível com as VMs do Azure e do Azure Site Recovery Services.

## <a name="enable-protection-using-site-recovery"></a>Ativar a proteção com a recuperação de Site
### <a name="protect-the-virtual-machine"></a>Proteger a máquina virtual
Ative a proteção da máquina virtual/DNS de controlador domínio no Site Recovery. Configure definições de recuperação de Site com base no tipo de máquina virtual (Hyper-V ou VMware). O controlador de domínio replicado com a recuperação de sites é utilizado para [ativação pós-falha de teste](#test-failover-considerations). Certifique-se de que cumpre os seguintes requisitos:

1. O controlador de domínio é um servidor de catálogo global
2. O controlador de domínio deve ser o proprietário da função FSMO para as funções que será necessário durante uma ativação pós-falha de teste (caso contrário estas funções tem de ser [seized](http://aka.ms/ad_seize_fsmo) após a ativação pós-falha)

### <a name="configure-virtual-machine-network-settings"></a>Configurar as definições de rede de máquina virtual
Para a máquina virtual do controlador/DNS de domínio, configure as definições de rede na recuperação de Site para que a máquina virtual será ligada à rede à direita após a ativação pós-falha. 

![Definições de rede VM](./media/site-recovery-active-directory/DNS-Target-IP.png)

## <a name="protect-active-directory-with-active-directory-replication"></a>Proteger o Active Directory com a replicação do Active Directory
### <a name="site-to-site-protection"></a>Proteção de site a site
Crie um controlador de domínio no site secundário. Quando promover o servidor para uma função de controlador de domínio, especifique o nome do mesmo domínio que está a ser utilizado no site primário. Pode utilizar o **serviços e locais do Active Directory** snap-in para configurar as definições no objeto de ligação de site a que são adicionados os sites. Ao configurar as definições de uma ligação de site, pode controlar quando a replicação ocorre entre dois ou mais sites,. e frequên. Para obter mais informações, consulte [agendar a replicação entre Sites](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Proteção de site para o Azure
Siga as instruções para [criar um controlador de domínio numa Azure virtual network](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Quando promover o servidor para uma função de controlador de domínio, especifique o mesmo nome de domínio que é utilizado no site primário.

Em seguida, [reconfigurar o servidor DNS para a rede virtual](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network), para utilizar o servidor DNS no Azure.

![Rede do Azure](./media/site-recovery-active-directory/azure-network.png)

**DNS na rede de produção do Azure**

## <a name="test-failover-considerations"></a>Considerações de ativação pós-falha de teste
Ativação pós-falha de teste ocorre numa rede que está isolada da rede de produção para que não há nenhum impacto nas cargas de trabalho de produção.

A maioria das aplicações também requerem a presença de um controlador de domínio e um servidor DNS para a função. Por conseguinte, antes da aplicação é a ativação pós-falha, um controlador de domínio tem de ser criados na rede isolada a ser utilizado para ativação pós-falha de teste. É a forma mais fácil de fazê-lo para replicar uma máquina virtual de controlador/DNS de domínio com a recuperação de Site. Em seguida, execute uma ativação pós-falha de teste da máquina de virtual do controlador de domínio antes de executar uma ativação pós-falha do plano de recuperação para a aplicação. Eis como pode fazê-lo:

1. [Replicar](site-recovery-replicate-vmware-to-azure.md) a máquina virtual do controlador/DNS de domínio com a recuperação de Site.
1. Crie uma rede isolada. Qualquer rede virtual criada no Azure, por predefinição está isolada de outras redes. Recomendamos que o intervalo de endereços IP para esta rede é igual da sua rede de produção. Não conseguir ative a conetividade site a site nesta rede.
1. Forneça um endereço de IP de DNS na rede de criado, como o endereço IP que espera que a máquina virtual DNS para obter. Se estiver a replicar para o Azure, em seguida, forneça o endereço IP para a VM que é utilizada na ativação pós-falha no **IP de destino** definição **computação e rede** definições. 

    ![IP de destino](./media/site-recovery-active-directory/DNS-Target-IP.png) **IP de destino**

    ![Rede de teste do Azure](./media/site-recovery-active-directory/azure-test-network.png)

    **DNS na rede de teste do Azure**

> [!TIP]
> Recuperação de site tenta criar máquinas virtuais de teste numa sub-rede do mesmo nome e utiliza o mesmo IP que foi fornecido no **computação e rede** definições da máquina virtual. Se a sub-rede do mesmo nome não está disponível na rede virtual do Azure fornecida para ativação pós-falha de teste, em seguida, máquina virtual de teste é criada na primeira sub-rede por ordem alfabética. Se o IP de destino é parte da sub-rede escolhida, em seguida, recuperação de Site tenta criar as máquinas de virtuais de ativação pós-falha de teste utilizando o IP de destino. Se o IP de destino não faz parte da sub-rede escolhida, máquina de virtual de ativação pós-falha de teste é criada utilizando qualquer IP disponível na sub-rede que escolheu. 
>
>


1. Se está a replicar para outro site no local e estiver a utilizar DHCP, siga as instruções para [configurar o DNS e DHCP para ativação pós-falha de teste](site-recovery-test-failover-vmm-to-vmm.md#prepare-dhcp)
1. Efetue uma ativação pós-falha de teste da máquina virtual do controlador de domínio executada na rede isolada. Utilize mais recente disponível **aplicação consistente** ponto de recuperação da máquina de virtual de controlador de domínio para efetuar a ativação pós-falha de teste. 
1. Execute uma ativação pós-falha de teste para o plano de recuperação contém máquinas virtuais da aplicação. 
1. Depois do teste concluído, **ativação pós-falha de teste de limpeza** na máquina de virtual do controlador de domínio. Este passo elimina o controlador de domínio que foi criado para ativação pós-falha de teste.


### <a name="removing-reference-to-other-domain-controllers"></a>Remover a referência para outros controladores de domínio
Quando estão a fazer uma ativação pós-falha de teste, não coloque todos os controladores de domínio na rede de teste. Para remover a referência de outros controladores de domínio que existe no seu ambiente de produção, poderá ser necessário [obter funções de FSMO Active Directory](http://aka.ms/ad_seize_fsmo) e [limpeza de metadados](https://technet.microsoft.com/library/cc816907.aspx) para o domínio em falta controladores. 



> [!IMPORTANT]
> Algumas das configurações descritas na secção seguinte não são as configurações de controlador de domínio/predefinido padrão. Se não pretender efetuar estas alterações a um controlador de domínio de produção, em seguida, pode criar um controlador de domínio dedicado para ser utilizado para ativação pós-falha de teste de recuperação de Site e efetuar estas alterações à.  
>
>

### <a name="issues-because-of-virtualization-safeguards"></a>Problemas devido a proteções de Virtualização 

Começando com o Windows Server 2012, [incorporadas proteções adicionais para os serviços de domínio do Active Directory](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Estas salvaguardas de ajudam a proteger os controladores de domínio virtualizados contra o USN reverte, desde que a plataforma subjacente do hipervisor suporta VM-GenerationID. Azure suporta VM-GenerationID, o que significa que os controladores de domínio que executam o Windows Server 2012 ou posterior no Azure máquinas virtuais têm as proteções adicionais. 


Quando o VM-Generation ID é reposto, o invocationID da base de dados do AD DS também é reposto, o conjunto de RID é rejeitado e SYSVOL está marcado como não autoritativo. Para obter mais informações, consulte [introdução à virtualização de serviços de domínio do Active Directory](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) e [virtualizar o DFSR com segurança](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/)

Falha ao longo para o Azure pode fazer com que a reposição do VM-GenerationID e que inicia nas proteções adicionais quando as máquinas do controlador de domínio é iniciado no Azure. Isto pode fazer um **atraso significativo** conseguir iniciar sessão na máquina de virtual do controlador de domínio de utilizador. Uma vez que este controlador de domínio apenas seria possível utilizar uma ativação pós-falha de teste, as proteções de Virtualização não são necessárias. Para garantir que não altera VM-GenerationID da máquina de virtual do controlador de domínio, em seguida, pode alterar o valor do DWORD seguinte para 4 no controlador de domínio no local.

        
        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start
 

#### <a name="symptoms-of-virtualization-safeguards"></a>Sintomas das proteções de Virtualização
 
Se as proteções de Virtualização tem kicked após uma ativação pós-falha de teste, poderá ver um ou mais dos seguintes sintomas:  

Alteração do ID de geração

![Alteração do ID de geração](./media/site-recovery-active-directory/Event2170.png)

Alteração do ID de invocação

![Alteração do ID de invocação](./media/site-recovery-active-directory/Event1109.png)

Partilhas de SYSVOL e Netlogon não estão disponíveis

![Partilha SYSVOL](./media/site-recovery-active-directory/sysvolshare.png)

![NTFRS Sysvol](./media/site-recovery-active-directory/Event13565.png)

São eliminados quaisquer bases de dados DFSR

![Eliminar da base de dados de DFSR](./media/site-recovery-active-directory/Event2208.png)


> [!IMPORTANT]
> Algumas das configurações descritas na secção seguinte não são as configurações de controlador de domínio/predefinido padrão. Se não pretender efetuar estas alterações a um controlador de domínio de produção, em seguida, pode criar um controlador de domínio dedicado para ser utilizado para ativação pós-falha de teste de recuperação de Site e efetuar estas alterações à.  
>
>


### <a name="troubleshooting-domain-controller-issues-during-test-failover"></a>Resolução de problemas problemas de controlador de domínio durante a ativação pós-falha de teste


Na linha de comandos, execute o seguinte comando para verificar se as pastas SYSVOL e NETLOGON são partilhadas:

    NET SHARE

Na linha de comandos, execute o seguinte comando para se certificar de que o controlador de domínio está a funcionar corretamente.

    dcdiag /v > dcdiag.txt

No registo de saída, procure o seguinte texto confirmar que o controlador de domínio está a funcionar corretamente. 

* "test transmitido conectividade"
* "transmitido teste publicidade"
* "test transmitido MachineAccount"

Se forem satisfeitas as condições anteriores, é provável que o controlador de domínio está a funcionar corretamente. Caso contrário, tente os passos seguintes.


* Efetue um restauro autoritativa do controlador de domínio.
    * Embora seja [não é recomendado que utilize replicação FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), mas se ainda estiver a utilizar, em seguida, siga os passos fornecidos [aqui](https://support.microsoft.com/kb/290762) para efetuar um restauro autoritativa. Pode ler mais sobre Burflags abordou a hiperligação anterior [aqui](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Se estiver a utilizar replicação de DFSR, em seguida, siga os passos disponíveis [aqui](https://support.microsoft.com/kb/2218556) para efetuar um restauro autoritativa. Também pode utilizar o Powershell funções disponíveis neste [ligação](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/) para esta finalidade. 
    
* Ignorar os requisitos de sincronização inicial, definindo a seguinte chave de registo para 0 no controlador de domínio no local. Se esta DWORD não existir, em seguida, pode criá-la sob o nó 'Parameters'. Pode ler mais sobre o assunto [aqui](https://support.microsoft.com/kb/2001093)

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations

* Desative o requisito de que o servidor de catálogo global está disponível para validar o início de sessão do utilizador ao definir a seguinte chave de registo como 1 no controlador de domínio no local. Se esta DWORD não existir, pode criá-la sob o nó 'Lsa'. Pode ler mais sobre o assunto [aqui](http://support.microsoft.com/kb/241789)

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures



### <a name="dns-and-domain-controller-on-different-machines"></a>DNS e controlador de domínio em computadores diferentes
Se o DNS não estiver na mesma máquina virtual como controlador de domínio, terá de criar uma VM de DNS para a ativação pós-falha de teste. Se não estiverem na mesma VM, pode ignorar esta secção.

Pode utilizar um servidor DNS de raiz e criar todas as zonas necessárias. Por exemplo, se o domínio do Active Directory for contoso.com, pode criar uma zona DNS com o nome contoso.com. As entradas correspondentes no Active Directory têm de ser atualizadas no DNS, da seguinte forma:

1. Certifique-se de que estas definições estão em vigor antes de qualquer outra máquina virtual no plano de recuperação é apresentada:
   
   * A zona deve ter o nome depois do nome de raiz de floresta.
   * A zona tem de ser de segurança de ficheiros.
   * A zona deve estar ativada para atualizações seguras e não segura.
   * A resolução da máquina de virtual do controlador de domínio deve apontar para o endereço IP da máquina virtual DNS.
2. Execute o seguinte comando na máquina de virtual do controlador de domínio:
   
    `nltest /dsregdns`
3. Adicionar uma zona no servidor DNS, permita que as atualizações não segura e adicione uma entrada para o mesmo no DNS:
   
        dnscmd /zoneadd contoso.com  /Primary
        dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
        dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
        dnscmd /config contoso.com /allowupdate 1

## <a name="next-steps"></a>Passos seguintes
Leitura [que cargas de trabalho posso proteger?](site-recovery-workload.md) para saber mais sobre como proteger cargas de trabalho empresariais com o Azure Site Recovery.

