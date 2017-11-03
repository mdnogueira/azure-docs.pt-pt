
## <a name="azure-backup"></a>Azure Backup

Cópia de segurança as VMs do Azure a executar cargas de trabalho de produção, utilize a cópia de segurança do Azure. Cópia de segurança do Azure suporta cópias de segurança consistentes com aplicações para Windows e VMs com Linux. Cópia de segurança do Azure cria pontos de recuperação que estão armazenados no cofres de recuperação com redundância geográfica. Quando restaurar a partir de um ponto de recuperação, pode restaurar VM todo ou ficheiros apenas específicos. 

Para uma introdução simple, prática para cópia de segurança do Azure para as VMs do Azure, consulte o "cópia de segurança tutorial de máquinas virtuais do Windows" para [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) ou [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md)

Para obter mais informações sobre como cópia de segurança do Azure funciona, consulte [planear a infraestrutura de cópia de segurança de VM no Azure](../articles/backup/backup-azure-vms-introduction.md)


## <a name="azure-site-recovery"></a>Azure Site Recovery

O Azure Site Recovery protege as VMs de um cenário de desastre grave, quando uma região toda sofre uma falha devido a desastre natural principais ou interrupção do serviço ampla. Pode configurar o Azure Site Recovery para as suas VMs, de modo a que pode recuperar a sua aplicação com um único clique no fim de minutos. Pode replicar para uma região do Azure à sua escolha, não está limitado a regiões emparelhadas. 

Pode executar simulações de recuperação após desastre com as ativações pós-falha de teste a pedido, sem afetar as cargas de trabalho de produção ou a replicação em curso. Crie planos de recuperação para orquestrar a ativação pós-falha e a reativação pós-falha da aplicação completa em execução em várias VMs. A funcionalidade do plano de recuperação está integrada com runbooks de automatização do Azure.

Pode começar a utilizar pelo [replicar as máquinas virtuais](https://aka.ms/a2a-getting-started). 

## <a name="managed-snapshots"></a>Gerido instantâneos 

Em ambientes de desenvolvimento e teste, os instantâneos proporcionar uma opção rápida e simple para fazer uma cópia de segurança de VMs que utilizam discos geridos. Um instantâneo gerido é uma cópia completa de só de leitura de um disco gerido. Instantâneos existem independentes de disco de origem e podem ser utilizados para criar novos discos geridos para recriar uma VM. Estes são cobrados com base na parte do disco utilizado. Por exemplo, se criar um instantâneo de um disco gerido com capacidade de aprovisionamento de 64 GB e o tamanho de dados utilizados real de 10 GB, instantâneo será faturado apenas para o tamanho de dados utilizados de 10 GB.  

Para obter mais informações sobre a criação de instantâneos, consulte:

* [Criar uma cópia de um VHD armazenado como Disco Gerido com os Instantâneos no Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Criar uma cópia de um VHD armazenado como Disco Gerido com os Instantâneos no Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Passos seguintes
Pode experimentar o Backup do Azure ao seguir o "cópia de segurança tutorial de máquinas virtuais do Windows" para [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) ou [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).
