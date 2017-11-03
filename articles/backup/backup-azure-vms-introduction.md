---
title: "Planeamento da sua infraestrutura de cópia de segurança de VM no Azure | Microsoft Docs"
description: "Considerações importantes sobre quando planear a cópia de segurança de máquinas virtuais no Azure"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "vms de cópia de segurança, a cópia de segurança de máquinas virtuais"
ms.assetid: 19d2cf82-1f60-43e1-b089-9238042887a9
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/18/2017
ms.author: markgal;trinadhk
ms.openlocfilehash: 9a4e0b5a400668cb9ec96000d274f43739139a03
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="plan-your-vm-backup-infrastructure-in-azure"></a>Planear a sua infraestrutura de cópias de segurança de VMs no Azure
Este artigo fornece desempenho e sugestões de recursos para ajudar a planear a infraestrutura de cópia de segurança de VM. Também define aspetos fundamentais do serviço de cópia de segurança; Estes aspetos podem ser fundamental para determinar a arquitetura, planeamento de capacidade e agendamento. Se já [preparar o ambiente](backup-azure-vms-prepare.md), planeamento é o passo seguinte antes de começar [para fazer uma cópia de segurança de VMs](backup-azure-vms.md). Se precisar de mais informações sobre máquinas virtuais do Azure, consulte o [documentação de Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="how-does-azure-back-up-virtual-machines"></a>Como funciona o Azure cópia de segurança máquinas virtuais?
Quando o serviço de cópia de segurança do Azure inicia uma tarefa de cópia de segurança na hora agendada, aciona a extensão de cópia de segurança para criar um instantâneo de ponto no tempo. As utilizações do serviço de cópia de segurança do Azure a _VMSnapshot_ extensão no Windows e o _VMSnapshotLinux_ extensão no Linux. A extensão é instalada durante a primeira cópia de segurança VM. Para instalar a extensão, tem de executar a VM. Se a VM não está em execução, o serviço de cópia de segurança tira um instantâneo de armazenamento subjacente (uma vez que não existem escritas de aplicação ocorrem enquanto a VM está parada).

Quando um instantâneo de VMs do Windows, o serviço de cópia de segurança coordena com o serviço do Volume de cópia de sombra de volumes (VSS) para obter um instantâneo consistente dos discos da máquina virtual. Se estiver a cópia de segurança de VMs com Linux, pode escrever scripts personalizados para garantir consistência quando um instantâneo da VM. São fornecidos detalhes sobre como invocar estes scripts neste artigo.

Assim que o serviço de cópia de segurança do Azure assume o instantâneo, os dados são transferidos para o cofre. Para maximizar a eficiência, o serviço identifica, transfere apenas os blocos de dados que foram alterados desde a cópia de segurança anterior.

![Arquitetura de cópia de segurança de máquina virtual do Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

Quando a transferência de dados estiver concluída, o instantâneo é removido e é criado um ponto de recuperação.

> [!NOTE]
> 1. Durante o processo de cópia de segurança, cópias de segurança do Azure não inclui o disco temporário anexado à máquina virtual. Para obter mais informações, consulte o blogue no [armazenamento temporário](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).
> 2. Uma vez que a cópia de segurança do Azure tira um instantâneo de nível de armazenamento e transfere esse instantâneo para o cofre, não altere as chaves de conta de armazenamento até que a conclusão da tarefa de cópia de segurança.
> 3. Para VMs do premium, iremos copiar o instantâneo para a conta de armazenamento. Isto é certificar-se de que o serviço de cópia de segurança do Azure obtém IOPS suficiente para transferir dados para o cofre. Esta cópia de armazenamento adicional é cobrada de acordo com a VM alocada tamanho. 
>

### <a name="data-consistency"></a>Consistência dos dados
Cópia de segurança e restaurar dados críticos é complicou pelo facto da que dados críticos da empresa tem uma cópia de segurança, enquanto as aplicações que produzem os dados de negócio estão em execução. Para resolver isto, o Backup do Azure suporta cópias de segurança consistentes com aplicações para Windows e VMs com Linux
#### <a name="windows-vm"></a>VM do Windows
Cópia de segurança do Azure utiliza cópias de segurança completas VSS em VMs do Windows (ler mais sobre [cópia de segurança completa do VSS](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)). Para ativar as cópias de segurança de cópia VSS, a seguinte chave de registo tem de ser definido na VM.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```

#### <a name="linux-vms"></a>VMs do Linux
Cópia de segurança do Azure fornece uma arquitetura de script. Para garantir a consistência de aplicações quando a cópia de segurança de VMs com Linux, crie pré-scripts de personalizados e scripts pós-implementação que controlam o fluxo de trabalho de cópia de segurança e o ambiente. Cópia de segurança do Azure invoca o script de pré-lançamento antes de tirar o instantâneo da VM e invoca o script de pós-implementação, uma vez concluída a tarefa de instantâneo VM. Para obter mais detalhes, consulte [aplicação consistentes VM cópias de segurança utilizando pré- scripts de e scripts pós-implementação](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).
> [!NOTE]
> Cópia de segurança do Azure invoca só de escrita de cliente pré e scripts pós-implementação. Se o pré- script de e os scripts pós-implementação executados com êxito, a cópia de segurança do Azure marca o ponto de recuperação como aplicação consistente. No entanto, o cliente é responsável pela consistência de aplicações ao utilizar scripts personalizados.
>


Esta tabela explica os tipos de consistência e as condições que possam ocorrerem em durante a VM do Azure de cópia de segurança e restaurar os procedimentos.

| Consistência | Com base em VSS | Explicação e detalhes |
| --- | --- | --- |
| Consistência das aplicações |Sim para Windows|Consistência de aplicação é ideal para cargas de trabalho como assegura que:<ol><li> A VM *arranca*. <li>Não há *não danos*. <li>Não há *sem perda de dados*.<li> Os dados são consistentes da aplicação que utiliza os dados, pelo que envolve a aplicação no momento da cópia de segurança – utilizando VSS ou prévio/script.</ol> <li>*VMs do Windows*-cargas de trabalho Microsoft mais têm escritores VSS se que efetue as ações específicas relacionadas com a consistência dos dados. Por exemplo, o Microsoft SQL Server tem um escritor VSS assegura que as operações de escrita para o ficheiro de registo de transações e a base de dados são efetuadas corretamente. Para cópias de segurança de VM do Windows Azure, para criar um ponto de recuperação consistentes com aplicações, a extensão de cópia de segurança tem invocar o fluxo de trabalho do VSS e concluí-la antes de tirar o instantâneo VM. Para ser exato o instantâneo da VM do Azure, os escritores VSS de todas as aplicações da VM do Azure tem de concluir bem. (Obter o [Noções básicas do VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) e profundo aprofundar os detalhes da [como funciona](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx)). </li> <li> *VMs com Linux*-os clientes podem executar [pré-script de personalizado e scripts pós-implementação para garantir a consistência de aplicações](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). </li> |
| Consistência de sistema de ficheiros |Sim - para computadores baseados em Windows |Existem dois cenários em que o ponto de recuperação pode ser *sistema de ficheiros consistente*:<ul><li>Cópias de segurança de VMs com Linux no Azure, sem pré-script/post-script ou se pré-script/post-script falhou. <li>Falha do VSS durante a cópia de segurança para VMs do Windows no Azure.</li></ul> Ambos nestes casos, o melhor pode ser feito é Certifique-se de que: <ol><li> A VM *arranca*. <li>Não há *não danos*.<li>Não há *sem perda de dados*.</ol> Aplicações necessita de implementar os seus próprios mecanismo "garantia de cópia de segurança" nos dados restaurados. |
| Consistência de falhas |Não |Esta situação é equivalente a uma máquina virtual com uma "Falha" (através de qualquer uma reposição recuperável ou disco rígida). Consistência de falhas ocorre normalmente quando a máquina virtual do Azure é encerrada no momento da cópia de segurança. Um ponto de recuperação consistentes com falhas fornece sem garantias relativamente a consistência dos dados de média de armazenamento – a partir da perspetiva do sistema operativo ou aplicação. Apenas os dados que já existe no disco no momento da cópia de segurança são capturados e cópia de segurança. <br/> <br/> Enquanto existirem não garante, normalmente, o sistema operativo arranques, seguido a verificação de disco procedimento, como o chkdsk, para corrigir os erros de danos. Quaisquer dados na memória ou operações de escrita que não tem sido transferidas para o disco são perdidas. A aplicação normalmente segue-se com o suas próprias mecanismo de verificação no caso de reversão de dados tem de ser feito. <br><br>Por exemplo, se o registo de transações tem entradas que não estão presentes na base de dados, em seguida, o software de base de dados efetua uma reversão até que os dados são consistentes. Quando dados é distribuídos por vários discos virtuais (como volumes expandidos), um ponto de recuperação consistentes com falhas fornece sem garantias de correcção dos dados. |

## <a name="performance-and-resource-utilization"></a>Utilização de recursos e de desempenho
Como o software de cópia de segurança que é implementado no local, deverá planear capacidade e a utilização de recursos necessidades ao fazer cópias de segurança de VMs no Azure. O [limites de armazenamento do Azure](../azure-subscription-service-limits.md#storage-limits) definir como implementações de VM para obter o máximo desempenho com um impacto mínimo para executar cargas de trabalho de estrutura.

Quando planear o desempenho de cópia de segurança, quais deve preste atenção os seguintes limites de armazenamento do Azure:

* Saída de máx. por conta de armazenamento
* Taxa de pedidos total por conta de armazenamento

### <a name="storage-account-limits"></a>Limites de conta de armazenamento
Dados de cópia de segurança copiados a partir de uma conta de armazenamento, adiciona as operações de entrada/saída por segundo (IOPS) e saída (ou débito) métricas da conta de armazenamento. Ao mesmo tempo, as máquinas virtuais são também consumir IOPS e débito. O objetivo é para garantir a cópia de segurança e o tráfego de máquina virtual não excedem os limites de conta de armazenamento.

### <a name="number-of-disks"></a>Número de discos
O processo de cópia de segurança tenta concluir uma tarefa de cópia de segurança mais rapidamente possível. Ao fazê-lo, o que consome tantos recursos possível. No entanto, todas as operações de e/s são limitadas pelo *débito de destino para o Blob único*, que tem um limite de 60 MB por segundo. Numa tentativa para maximizar a velocidade, o processo de cópia de segurança tenta efetuar cópias de segurança a cada um dos discos da VM *em paralelo*. Se uma VM tem quatro discos, o serviço tenta efetuar cópias de segurança de todos os discos de quatro em paralelo. O **número de discos** a cópia de segurança, é o fator mais importante para determinar o tráfego de cópia de segurança de conta de armazenamento.

### <a name="backup-schedule"></a>Agenda de cópia de segurança
É um fator adicional que tem impacto no desempenho o **agenda de cópia de segurança**. Se configurar as políticas para que todas as VMs cópias de segurança criadas ao mesmo tempo, tiver agendado um encravado de tráfego. O processo de cópia de segurança tenta efetuar cópias de segurança de todos os discos em paralelo. Para reduzir o tráfego de cópia de segurança de uma conta de armazenamento, fazer cópias de segurança diferentes VMs em outra altura do dia, sem sobreposição.

## <a name="capacity-planning"></a>Planeamento de capacidade
Colocar os fatores anteriores em conjunto, terá de planear as necessidades de utilização da conta de armazenamento. Transferir o [capacidade de cópia de segurança de VM folha de cálculo do Excel planeamento](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) para ver o impacto do disco e opções de agenda de cópia de segurança.

### <a name="backup-throughput"></a>Débito de cópia de segurança
Para cada disco a cópia de segurança, cópias de segurança do Azure lê os blocos no disco e armazena apenas os dados alterados (cópia de segurança incremental). A tabela seguinte mostra os valores de débito de serviço de cópia de segurança média. Utilizar os seguintes dados, pode estimar a quantidade de tempo necessário para efetuar cópias de segurança de um disco de um tamanho específico.

| Operação de cópia de segurança | Débito mais favorável |
| --- | --- |
| Cópia de segurança inicial |160 Mbps |
| Cópia de segurança incremental (DR) |640 Mbps <br><br> Débito significativamente ignora se os dados alterados (que tem de ser feita) estejam dispersos em disco.|

## <a name="total-vm-backup-time"></a>Tempo de cópia de segurança de VM total
Enquanto a maioria das cópias de segurança é despendido a ler e a cópia dos dados, outras operações contribuem para o total de tempo necessário para efetuar uma cópia de segurança de uma VM:

* Tempo necessário para [instale ou Atualize a extensão de cópia de segurança](backup-azure-vms.md).
* Tempo de instantâneo, é o tempo decorrido para acionar um instantâneo. Instantâneos são acionados próximo da hora de cópia de segurança agendada.
* Tempo de espera na fila. Uma vez que o serviço de cópia de segurança está a processar cópias de segurança de vários clientes, ao copiar dados de cópia de segurança do instantâneo para a cópia de segurança ou um cofre dos serviços de recuperação poderá não iniciar imediatamente. Em tempos de pico de carga, a espera pode stretch devido ao número de cópias de segurança que está a ser processados até oito horas. No entanto, o tempo de cópia de segurança de VM total é inferior a 24 horas para políticas de cópia de segurança diárias.
* Tempo de transferência de dados, o tempo necessário para o serviço de cópia de segurança para as alterações incrementais de cópia de segurança anterior de computação e transferir essas alterações para o Cofre de armazenamento.

### <a name="why-am-i-observing-longer12-hours-backup-time"></a>Por que razão estou posso observar longer(>12 hours) tempo de cópia de segurança?
Cópia de segurança consiste em duas fases: tirar instantâneos e transferir os instantâneos para o cofre. Otimiza o serviço de cópia de segurança para armazenamento. Ao transferir os dados de instantâneos para um cofre, o serviço transfere apenas as alterações incrementais do instantâneo anterior.  Para determinar as alterações incrementais, o serviço calcula a soma de verificação dos blocos de. Se um bloco for alterado, o bloco é identificado como um bloco sejam enviados para o cofre. Em seguida, simulações de serviço adicionais para cada um dos blocos de identificados à procura de oportunidades minimizar os dados para transferir. Depois de avaliar os blocos alterados tudo, o serviço une as alterações e envia-os para o cofre. Em algumas aplicações antigas, escritas pequenas, fragmentadas, não são ideais para armazenamento. Se o instantâneo contém muitas escritas pequenas, fragmentadas, o serviço despende mais tempo a processar os dados escritos pelas aplicações. O bloco de escrita de aplicação recomendada do Azure, para aplicações em execução dentro da VM, é um mínimo de 8 KB. Se a sua aplicação utiliza um bloco de inferior a 8 KB, o desempenho de cópia de segurança é afetado. Para obter ajuda com a sua aplicação para melhorar o desempenho de cópia de segurança de otimização, consulte [otimização aplicações para um desempenho ideal com armazenamento do Azure](../virtual-machines/windows/premium-storage-performance.md). Embora o artigo no desempenho da cópia de segurança utiliza exemplos de armazenamento Premium, a documentação de orientação é aplicável para discos de armazenamento Standard.

## <a name="total-restore-time"></a>Tempo total de restauro
Uma operação de restauro é composto por duas tarefas sub principais: copiar dados novamente a partir do cofre para a conta de armazenamento do cliente que escolheu e criar a máquina virtual. Copiar os dados novamente a partir do cofre depende de onde as cópias de segurança são armazenadas internamente no Azure e onde está armazenada a conta de armazenamento do cliente. Tempo decorrido para copiar dados depende de:
* Tempo de espera na fila - uma vez que o serviço processa restaurar tarefas a partir de vários clientes em simultâneo, restauro pedidos é colocados numa fila.
* Tempo de - de cópia de dados dados são copiados do cofre para a conta de armazenamento do cliente. Restaurar o tempo depende de IOPS e débito do serviço de cópia de segurança do Azure obtém na conta de armazenamento de cliente selecionado. Para reduzir o tempo de cópia durante o processo de restauro, selecione uma conta de armazenamento não carregada com outras aplicações escritas e leituras.

## <a name="best-practices"></a>Melhores práticas
Sugerimos seguindo estas práticas ao configurar cópias de segurança de máquinas virtuais:

* Não agende mais de 10 VMs clássicas do mesmo serviço de nuvem para criar cópias de segurança ao mesmo tempo. Se pretender efetuar cópias de segurança de várias VMs do mesmo serviço de nuvem, escalonar as horas de início de cópia de segurança por uma hora.
* Não agende a mais de 40 VMs para fazer cópias de segurança ao mesmo tempo.
* Agende cópias de segurança da VM durante o horário de pico. Desta forma, o serviço de cópia de segurança utiliza o IOPS para a transferência de dados da conta de armazenamento do cliente para o cofre.
* Certifique-se de que uma política é aplicada em VMs distribuídos por contas de armazenamento diferente. Sugerimos que mais do que 20 totais discos de uma única conta de armazenamento ser protegidos com a mesma agenda de cópia de segurança. Se tiver o maior do que 20 discos numa conta do storage, distribuídos dessas VMs por várias políticas para obter o IOPS necessário durante a fase de transferência do processo de cópia de segurança.
* Não restaure uma VM em execução no armazenamento Premium, a mesma conta de armazenamento. Se o processo de operação de restauro coincida com a operação de cópia de segurança, reduz o IOPS disponível para cópia de segurança.
* Para cópia de segurança de VM do Premium, certifique-se essa conta do storage que os discos premium anfitriões tem pelo menos 50% de espaço livre para instantâneos para uma cópia de segurança de teste. 
* Certifique-se de que a versão python em VMs do Linux ativada para a cópia de segurança é 2.7

## <a name="data-encryption"></a>Encriptação de dados
Cópia de segurança do Azure não encripta os dados como parte do processo de cópia de segurança. No entanto, pode encriptar dados dentro da VM e criar cópias de segurança dos dados protegidos de forma totalmente integrada (ler mais sobre [cópia de segurança dos dados encriptados](backup-azure-vms-encryption.md)).

## <a name="calculating-the-cost-of-protected-instances"></a>A calcular o custo de instâncias protegidas
Máquinas virtuais do Azure, cópias de segurança efetuadas através de cópia de segurança do Azure estão sujeitos a [preços da cópia de segurança do Azure](https://azure.microsoft.com/pricing/details/backup/). O cálculo de instâncias protegidos baseia-se no *real* tamanho da máquina virtual, que é a soma de todos os dados a máquina virtual - excluindo o "disco de recursos".

Preços para fazer uma cópia de segurança de VMs é *não* com base no tamanho máximo suportado para cada disco de dados ligado à máquina virtual. Preços baseia-se em reais dados armazenados no disco de dados. Da mesma forma, a fatura do armazenamento de cópia de segurança baseia-se na quantidade de dados que são armazenados em cópia de segurança do Azure, que é a soma dos dados reais em cada ponto de recuperação.

Por exemplo, colocar uma máquina virtual tamanho A2 padrão, que tem dois discos de dados adicionais com um tamanho máximo de 1 TB. A tabela seguinte fornece os dados reais armazenados em cada um destes discos:

| Tipo de disco | Tamanho máx. | Disponibilizar dados reais presentes |
| --- | --- | --- |
| Disco do sistema operativo |1023 GB |17 GB |
| Disco local / disco de recursos |135 GB |5 GB (não incluído para cópia de segurança) |
| Disco de dados 1 |1023 GB |30 GB |
| Disco de dados 2 |1023 GB |0 GB |

O *real* tamanho da máquina virtual neste caso é 17 GB + 30 GB + 0 GB = 47 GB. Este tamanho de instância protegido (47 GB) torna-se a base para a fatura mensal. Pelo contrário em conformidade à medida que aumenta a quantidade de dados na máquina virtual, o tamanho da instância protegidos utilizado para alterações de faturação.

Faturação não inicia até concluir a primeira cópia de segurança com êxito. Neste momento, a faturação para armazenamento e instâncias protegido começa. Continua a faturação, desde que não há *quaisquer dados armazenados no Cofre de cópia de segurança* para a máquina virtual. Se parar a proteção na máquina virtual, mas os dados de cópia de segurança da máquina virtual existem num cofre, continua a faturação.

Faturação do deixa de uma máquina virtual especificada apenas se a proteção está parada *e* todos os dados de cópia de segurança é eliminado. Quando interrompe a proteção e existem não existem tarefas de cópia de segurança Active Directory, o tamanho da última bem-sucedida VM cópia de segurança torna-se o tamanho da instância protegidos utilizado para a fatura mensal.

## <a name="questions"></a>Tem dúvidas?
Se tiver dúvidas ou se houver alguma funcionalidade que gostaria de ver incluída, [envie-nos comentários](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Passos seguintes
* [Fazer uma cópia de segurança de máquinas virtuais](backup-azure-vms.md)
* [Gerir a cópia de segurança da máquina virtual](backup-azure-manage-vms.md)
* [Monitorizar máquinas virtuais](backup-azure-restore-vms.md)
* [Resolver problemas de cópia de segurança de VM](backup-azure-vms-troubleshoot.md)
