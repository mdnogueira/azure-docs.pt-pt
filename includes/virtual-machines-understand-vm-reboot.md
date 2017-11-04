Máquinas virtuais do Azure (VMs), por vezes, poderá reiniciar por nenhum motivo aparente, sem provas do ter iniciou a operação de reinício. Este artigo apresenta os eventos que podem fazer com que as VMs reiniciar o computador e fornece informações sobre como evitar problemas de reinício inesperado ou reduzir o impacto de tais problemas e ações.

## <a name="configure-the-vms-for-high-availability"></a>Configurar as VMs de elevada disponibilidade
A melhor forma de proteger uma aplicação que está em execução no Azure contra VM reinicia e período de indisponibilidade é configurar as VMs de elevada disponibilidade.

Para proporcionar este nível de redundância à sua aplicação, recomendamos que agrupe duas ou mais VMs num conjunto de disponibilidade. Esta configuração assegura que durante a um evento de manutenção planeada ou, pelo menos uma VM está disponível e que cumpra a percentagem de 99,95 [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/).

Para obter mais informações sobre conjuntos de disponibilidade, consulte os artigos seguintes:

- [Gerir a disponibilidade das VMs](../articles/virtual-machines/windows/manage-availability.md)
- [Configurar a disponibilidade de VMs](../articles/virtual-machines/windows/classic/configure-availability.md)

## <a name="resource-health-information"></a>Informações de estado de funcionamento de recursos 
Estado de funcionamento de recursos do Azure é um serviço que expõe o estado de funcionamento dos recursos do Azure individuais e fornece orientações acionável para resolução de problemas. Num ambiente de nuvem em que não se encontra possível diretamente aos servidores ou elementos de infraestrutura, o objetivo do Estado de funcionamento de recursos é reduzir o tempo que demora na resolução de problemas. Em particular, o objetivo é para reduzir o tempo que demora a determinar se a raiz do problema situam-se na aplicação ou um evento dentro de plataforma do Azure. Para obter mais informações, consulte [compreender e utilizar o estado de funcionamento do recurso](../articles/resource-health/resource-health-overview.md).

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Ações e os eventos que podem fazer com que a VM reiniciar o computador

### <a name="planned-maintenance"></a>Manutenção planeada
Microsoft Azure executa periodicamente as atualizações em todo o mundo, para melhorar a fiabilidade, o desempenho e a segurança da infraestrutura de anfitrião subjacente a VMs. Muitas destas atualizações, incluindo atualizações preservação de memória, são efetuadas sem qualquer impacto nas suas VMs ou serviços em nuvem.

No entanto, algumas atualizações requerem um reinício. Nestes casos, as VMs estão encerradas, mas iremos aplicar o patch da infraestrutura e, em seguida, as VMs são reiniciadas.

Para compreender quais manutenção planeada do Azure e como esta pode afetar a disponibilidade das suas VMs do Linux, consulte os artigos listados aqui. Os artigos fornecem fundo sobre o Azure planeada o processo de manutenção e como agendar manutenção planeada para reduzir o impacto.

- [Manutenção planeada para VMs no Azure](../articles/virtual-machines/windows/planned-maintenance.md)
- [Como agendar manutenção planeada em VMs do Azure](../articles/virtual-machines/windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>Atualizações para preservação de memória   
Para esta classe de atualizações no Microsoft Azure, os utilizadores passam sem afetar as respetivas VMs em execução. Muitas destas atualizações são componentes ou serviços que podem ser atualizados sem interferir com a instância em execução. Alguns são atualizações de infraestrutura da plataforma no sistema operativo anfitrião que podem ser aplicados sem um reinício de VMs.

Estas atualizações preservação de memória são realizadas com a tecnologia que permite a migração em direto no local. Quando está a ser atualizado, a VM é colocada num *colocada em pausa* estado. Este estado preserva a memória RAM enquanto o sistema de operativo do anfitrião subjacente recebe as atualizações necessárias e correções de erros. A VM é retomada dentro de 30 segundos, de que está a ser colocada em pausa. Depois da VM é retomada, o relógio é automaticamente sincronizado.

Devido ao período curto pausa, implementar atualizações através destes mecanismo significativamente reduz o impacto nas VMs. No entanto, nem todas as atualizações podem ser implementadas desta forma. 

Atualizações de várias instâncias (para VMs num conjunto de disponibilidade) são aplicados um domínio de atualização de cada vez.

> [!NOTE]
> Máquinas de Linux que tenham versões antigas do kernel são afetadas por um panic kernel durante este método de atualização. Para evitar este problema, atualize para o kernel versão 3.10.0-327.10.1 ou posterior. Para obter mais informações, consulte [uma VM do Linux do Azure num kernel baseado em 3.10 panics após uma atualização do nó de anfitrião](https://support.microsoft.com/help/3212236).     
    
### <a name="user-initiated-reboot-or-shutdown-actions"></a>Ações de reinício ou encerramento iniciadas pelo utilizador
 
Se efetuar um reinício do portal do Azure, Azure PowerShell, a interface de linha de comandos ou repor API, pode encontrar o evento no [registo de atividade do Azure](../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Se efetuar a ação do sistema de operativo da VM, pode encontrar o evento nos registos do sistema.

Outros cenários que, normalmente, fazer com que a VM reiniciar o computador incluem várias ações de alteração da configuração. Normalmente, irá ver uma mensagem de aviso que indica que a execução de uma ação específica resultará numa reinicialização da VM. Os exemplos incluem quaisquer operações de redimensionamento de VM, alterar a palavra-passe da conta administrativa e definir um endereço IP estático.

### <a name="azure-security-center-and-windows-update"></a>Centro de segurança do Azure e no Windows Update
Centro de segurança do Azure monitoriza diárias Windows e VMs com Linux para atualizações do sistema operativo em falta. Centro de segurança obtém uma lista de atualizações críticas e de segurança disponíveis do Windows Update ou Windows Server Update Services (WSUS), dependendo de que o serviço está configurado numa VM do Windows. Centro de segurança também verifica as atualizações mais recentes para sistemas Linux. Se a VM está em falta uma atualização do sistema, o Centro de segurança recomenda se aplicam a atualizações do sistema. A aplicação destas atualizações do sistema é controlada através do Centro de segurança no portal do Azure. Depois de aplicar algumas atualizações, os reinícios VM poderão ser necessários. Para obter mais informações, consulte [aplicar atualizações do sistema no Centro de segurança do Azure](../articles/security-center/security-center-apply-system-updates.md).

Servidores no local, como o Azure não push atualizações do Windows Update para VMs do Windows Azure, porque estas máquinas são concebidas para serem geridos pelo que os utilizadores. É, no entanto, encouraged para deixar a definição do Windows Update automática ativada. Instalação automática de atualizações do Windows Update pode também fazer com que reinícios ocorrer após as atualizações são aplicadas. Para obter mais informações, consulte [FAQ de atualização do Windows](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Noutras situações que afetam a disponibilidade da sua VM
Existem outros casos em que Azure ativamente poderá suspender a utilização de uma VM. Receberá notificações de e-mail antes desta ação é executada, pelo que terá uma oportunidade para resolver os problemas subjacentes. Os exemplos dos problemas que afetam a disponibilidade VM incluem violações de segurança e a expiração de métodos de pagamento.

### <a name="host-server-faults"></a>Falhas de servidor de anfitrião 
A VM está alojada num servidor físico que está a ser executado no interior de um datacenter do Azure. O servidor físico executa um agente chamado o agente do anfitrião, além de alguns outros componentes do Azure. Quando estes componentes de software do Azure no servidor físico deixar de responder, o sistema de monitorização aciona um reinício do servidor de anfitrião, a tentativa de recuperação. A VM está normalmente disponível novamente dentro de cinco minutos e continua a em direto no mesmo anfitrião como anteriormente.

Falhas de servidor são normalmente causadas por falhas de hardware, tais como a falha de um disco rígido ou unidade de estado sólido. Azure monitoriza estes ocorrências continuamente, identifica os erros subjacentes e lança atualizações, depois de serem implementada e testada a mitigação.

Como algumas falhas de hardware do servidor de anfitrião podem ser específicas nesse servidor, uma situação de reinício VM repetida pode ser melhorada através da reimplementação manualmente a VM para outro servidor de anfitrião. Esta operação pode ser acionada utilizando o **Reimplementar** opção na página de detalhes da VM ou a parar e reiniciar a VM no portal do Azure.

### <a name="auto-recovery"></a>Recuperação automática
Se o servidor de anfitrião não é possível reiniciar por qualquer motivo, a plataforma do Azure inicia uma ação de recuperação automática para colocar o servidor de anfitrião defeituoso fora de rotação para uma investigação mais aprofundada. 

Todas as VMs nesse anfitrião são automaticamente relocalizadas para um servidor de anfitrião diferente, bom estado de funcionamento. Este processo é normalmente completo em 15 minutos. Para saber mais sobre o processo de recuperação automática, consulte o artigo [recuperação automática de VMs](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Manutenção não planeada
Em raras ocasiões, a equipa de operações do Azure poderá ter de efetuar atividades de manutenção para garantir o estado de funcionamento geral da plataforma do Azure. Este comportamento pode afetar a disponibilidade VM e que normalmente resulta na mesma ação de recuperação automática, tal como descrito anteriormente.  

Não planeadas maintenances incluem o seguinte:

- Desfragmentação de nó urgente
- Atualizações de comutador de rede urgente

### <a name="vm-crashes"></a>Falhas VM
VMs pode ser reiniciado devido a problemas de VM a próprio. A carga de trabalho ou a função que está em execução na VM pode acionar uma verificação de erros no sistema operativo convidado. Para ajudar a determinar o motivo da falha, veja o sistema e os registos de aplicações para as VMs do Windows e os registos de série para VMs com Linux.

### <a name="storage-related-forced-shutdowns"></a>Relacionadas com o armazenamento encerramentos forçados
As VMs no Azure baseiam-se nos discos virtuais para armazenamento de dados que está alojado na infraestrutura de armazenamento do Azure e de sistema operativo. Sempre que a disponibilidade ou a conectividade entre a VM e os discos virtuais associados é afetada por mais de 120 segundos, a plataforma do Azure efetua um encerramento forçado das VMs para evitar danificar os dados. As VMs estão automaticamente ligadas novamente após o restauro de conectividade de armazenamento. 

A duração do encerramento pode ser o mais curta cinco minutos mas pode ser bastante maior. Segue-se um dos casos específicos que estão associados com relacionadas com o armazenamento forçados encerramentos: 

**Limita a exceder a e/s**

VMs poderão estar temporariamente encerrar quando pedidos de e/s consistentemente limitados porque o volume de operações de e/s por segundo (IOPS) excede os limites de e/s para o disco. (Armazenamento de disco standard está limitado a 500 IOPS.) Para atenuar este problema, utilize striping de disco ou configurar o espaço de armazenamento no computador convidado VM, consoante a carga de trabalho. Para obter mais informações, consulte [configurar as VMs do Azure para otimizar o desempenho de armazenamento](http://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx).

Estão disponíveis através do armazenamento do Azure Premium com IOPS até 80.000 limites de IOPS superiores. Para obter mais informações, consulte [elevado desempenho Premium Storage](../articles/virtual-machines/windows/premium-storage.md).

### <a name="other-incidents"></a>Outros incidentes
Em raras circunstâncias, um problema ampla pode afetar vários servidores num datacenter do Azure. Se este problema ocorrer, a equipa do Azure envia notificações de correio eletrónico para as subscrições afetadas. Pode verificar o [dashboard de estado de funcionamento de serviço de Azure](https://azure.microsoft.com/status/) e o portal do Azure para o estado de falhas em curso e passado dos incidentes.
