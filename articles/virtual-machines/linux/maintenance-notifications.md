---
title: "Processamento de notificações de manutenção para VMs com Linux no Azure | Microsoft Docs"
description: "Ver as notificações de manutenção para máquinas virtuais do Linux em execução no Azure e iniciar manutenção self-service."
services: virtual-machines-linux
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: zivr
ms.openlocfilehash: b31955e19883f9fe2e7ed6cf7f5076eaf52577c0
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="handling-planned-maintenance-notifications-for-linux-virtual-machines"></a>Notificações de manutenção de processamento planeada para máquinas virtuais do Linux

Azure executa periodicamente atualizações para melhorar a fiabilidade, desempenho e segurança da infraestrutura de anfitrião para máquinas virtuais. As atualizações são as alterações, como o ambiente de alojamento de aplicação de patches ou atualizar e desativar o hardware. A maioria destas atualizações são efetuadas sem qualquer impacto para as máquinas virtuais alojadas. No entanto, há casos em que as atualizações de ter um impacto:

- Se a manutenção não requer um reinício, o Azure utiliza migração no local para colocar em pausa a VM enquanto o anfitrião está atualizado.

- Se a manutenção requer um reinício, receberá um aviso quando a manutenção planeada. Nestes casos, é-lhe dada uma janela de tempo em que pode iniciar a manutenção de si próprio, quando funciona para si.


Manutenção planeada, que requer uma reinicialização, é agendada no waves. Cada wave tem um âmbito diferentes (regiões).

- Uma onda começa com uma notificação para os clientes. Por predefinição, a notificação é enviada para o proprietário da subscrição e os coproprietários. Pode adicionar mais opções de mensagens, como e-mail, SMS e Webhooks e os destinatários de notificações. 
- Logo após a notificação, é definida uma janela de self-service. Durante este período, encontra-se que as máquinas virtuais está incluído nesta manutenção wave e começar a utilizar proativa volte a implementar. 
- Os seguintes janela self-service, começa uma janela de manutenção agendada. Neste momento, o Azure agendas e aplica-se a manutenção necessária à máquina virtual.  

O objetivo no ter dois windows é para lhe dar tempo suficiente para iniciar a manutenção e reiniciar a máquina virtual ao saber quando Azure irá iniciar automaticamente manutenção.

Pode utilizar a CLI do Azure, PowerShell, REST API e portal do Azure para consultar as janelas de manutenção para as suas VMs e iniciar manutenção self-service.

 > [!NOTE]
 > Se tentar iniciar a manutenção e falhar, o Azure marca a VM como **ignorada** e é não reiniciar durante a janela de manutenção agendada. Em vez disso, são contactados num horário posterior, com uma nova agenda. 

## <a name="find-vms-scheduled-for-maintenance-using-cli"></a>Localizar VMs agendadas para utilizar a CLI de manutenção

Manutenção planeada informações podem ser vistas utilizando [vm do azure get--vista de instância](/cli/azure/vm?view=azure-cli-latest#az_vm_get_instance_view).
 
Informações de manutenção são devolvidas apenas se não houver manutenção planeada. Se não existir que nenhuma manutenção agendada que impactos a VM, o comando não devolve quaisquer informações de manutenção. 

```azure-cli
az vm get-instance-view  - g rgName  -n vmName 
```

Os seguintes valores são devolvidos em MaintenanceRedeployStatus: 

| Valor | Descrição   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se pode iniciar manutenção na VM neste momento ||
| PreMaintenanceWindowStartTime         | O início da janela de manutenção self-service quando podem iniciar manutenção na VM ||
| PreMaintenanceWindowEndTime           | O fim da janela de manutenção self-service quando podem iniciar manutenção na VM ||
| MaintenanceWindowStartTime            | O início da janela de manutenção agendada quando podem iniciar manutenção na VM ||
| MaintenanceWindowEndTime              | O fim da janela de manutenção agendada quando podem iniciar manutenção na VM ||
| LastOperationResultCode               | O resultado da última tentativa para iniciar a manutenção da VM ||




## <a name="start-maintenance-on-your-vm-using-cli"></a>Iniciar manutenção na VM a utilizar a CLI

A seguinte chamada iniciará manutenção numa VM, se `IsCustomerInitiatedMaintenanceAllowed` está definido como true.

```azure-cli
az vm perform-maintenance rgName vmName 
```

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="classic-deployments"></a>Implementações clássicas

Se ainda tiver legadas VMs que foram implementados utilizando o modelo de implementação clássica, pode utilizar CLI 1.0 a consulta para as VMs e iniciar a manutenção.

Certifique-se de que está no modo correto para trabalhar com VMS clássicas, escrevendo:

```
azure config mode asm
```

Para obter o estado de manutenção de uma VM chamada *myVM*, tipo:

```
azure vm show myVM 
``` 

Para iniciar a manutenção VMS clássicas chamada *myVM* no *myService* serviço e *myDeployment* implementação, escreva:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```


## <a name="faq"></a>FAQ


**P: por que motivo precisa de reiniciar o meu máquinas de virtuais agora?**

**R:** enquanto a maioria das atualizações e melhoramentos para a plataforma do Azure não afetar a disponibilidade da máquina virtual, há casos em que podemos não é possível evitar reiniciando das máquinas virtuais alojadas no Azure. Podemos ter acumulados várias alterações que requerem-nos reiniciar os nossos servidores que resultará no reinício de máquinas virtuais.

**P: se posso seguir as recomendações para elevada disponibilidade através de um conjunto de disponibilidade, pude seguro?**

**R:**máquinas virtuais implementadas na disponibilidade de um conjunto ou conjuntos de dimensionamento de máquina virtual têm a noção de domínios de atualização (UD). Quando efetuar a manutenção, o Azure honra a restrição UD e não irá reiniciar as máquinas virtuais de diferentes UD (no mesmo conjunto de disponibilidade).  Azure também tem de aguardar pelo menos 30 minutos antes de mover para o seguinte grupo de máquinas virtuais. 

Para obter mais informações sobre como de elevada disponibilidade, consulte Gerir a disponibilidade das virtual machines Windows no Azure ou gerir a disponibilidade das máquinas virtuais do Linux no Azure.

**P: Posso ter recuperação após desastre definido noutra região. Pude seguro?**

**R:** região do Azure cada se encontra emparelhado com outro região dentro da mesma geografia (por exemplo, EUA, Europa ou Ásia). As atualizações do Azure planeadas são lançadas para regiões emparelhadas, uma de cada vez, para minimizar o período de indisponibilidade e o risco de indisponibilidade de aplicação. Durante a manutenção planeada, o Azure pode agendar uma janela semelhante para os utilizadores a iniciarem a manutenção, no entanto, a janela de manutenção agendada serão diferente entre as regiões emparelhadas.  

Para obter mais informações sobre regiões do Azure, consulte a regiões e disponibilidade para máquinas virtuais no Azure.  Pode ver a lista completa de pares regionais aqui.

**P: como posso ser notificado sobre a manutenção planeada?**

**R:** uma onda de manutenção planeada é iniciada através da definição de uma agenda a um ou mais regiões do Azure. Logo, é enviada uma notificação por e-mail para os proprietários de subscrição (um e-mail por subscrição). Canais adicionais e os destinatários para esta notificação foi possível configurar a utilizar alertas de registo de atividade. No caso de implementar uma máquina virtual numa região em que já foi agendada manutenção planeada, não irá receber a notificação mas em vez disso tem de verificar o estado de manutenção da VM.

**P: não vejo qualquer indicação de manutenção planeada no portal, Powershell ou CLI, o que é incorreto?**

**R:** informações relacionadas com a manutenção planeada estão disponíveis durante uma onda de manutenção planeada apenas para as VMs que vai ser afetado por-lo. Por outras palavras, se vir não os dados, poderia ser que o wave de manutenção tem já foi concluída (ou não iniciado) ou que a máquina virtual já estiver alojada num servidor atualizado.

**P: deve iniciar a manutenção na minha máquina virtual?**

**R:** em geral, as cargas de trabalho que são implementadas num serviço em nuvem, conjunto de disponibilidade ou conjunto de dimensionamento de máquinas virtuais, são resilientes a manutenção planeada.  Durante a manutenção planeada, apenas um domínio com uma única atualização é afetado em qualquer momento. Lembre-se de que a ordem dos domínios de atualização que está a ser afetado não necessariamente acontecer sequencialmente.

Pretende iniciar manutenção por si nos seguintes casos:
- A aplicação for executada numa única máquina virtual e precisar de aplicar todas as de manutenção durante off-hours
- Terá de coordenar a hora a manutenção como parte do seu SLA
- É necessário mais de 30 minutos entre cada reinício VM mesmo dentro de um disponibilidade definir.
- Pretende tirá a aplicação completa (várias camadas, vários domínios de atualização) para concluir a manutenção mais rápida.

**P: existe uma forma de saber exatamente quando a minha máquina virtual será afetada?**

**R:** ao definir a agenda, iremos definir um intervalo de tempo de vários dias. No entanto, a sequenciação exata de servidores (e as VMs) esse período é desconhecida. Os clientes que gostaria de saber o momento exato para as respetivas VMs podem utilizar eventos agendados e consulta a partir da máquina virtual e receber uma notificação de 10 minutos antes de um reinício VM.
  
**P: quanto-leva-o para reiniciar a minha máquina virtual?**

**R:** dependendo do tamanho da sua VM, reiniciar o computador pode demorar vários minutos. Tenha em atenção que no caso de utilizar serviços em nuvem, dimensionar define ou conjunto de disponibilidade, terá de 30 minutos entre cada grupo de VMs (UD). 

**P: qual será a experiência no caso de serviços em nuvem, conjuntos de dimensionamento e recursos de infraestrutura de serviço?**

**R:** enquanto estas plataformas são afetadas pela manutenção planeada, os clientes que utilizam estas plataformas é consideradas seguras dada que só VMs numa única atualização domínio (UD) vai ser o impacto em qualquer momento.  

**P: Posso recebeu uma mensagem de e-mail sobre a desativação de hardware, este é o mesmo que a manutenção planeada?**

**R:** enquanto a desativação de hardware é um evento de manutenção planeada, não ainda temos simplificar este caso de utilização para a nova experiência.  Esperamos que os clientes confundem-se no caso de recebem dois semelhantes mensagens de correio eletrónico sobre dois waves de manutenção planeada diferente.

**P: não vejo qualquer informação de manutenção na minha VMs. O que aconteceu de errado?**

**R:** existem vários motivos por que motivo não está a ver as informações de manutenção nas suas VMs:
1.  Estão a utilizar uma subscrição marcada como Microsoft interna.
2.  As suas VMs não estão agendadas para manutenção. É possível que terminou a wave de manutenção, cancelada ou modificada para que as suas VMs já não são afetadas por este.
3.  Não tem a coluna 'Manutenção' adicionada à sua vista de lista VM. Enquanto podemos adicionar esta coluna para a vista predefinida, os clientes que configurado para ver as colunas não predefinido tem de adicionar manualmente o **manutenção** coluna para a sua vista de lista VM.

**P: minha VM está agendada para manutenção para a segunda vez. Porquê?**

**R:** existem vários casos de utilização, onde poderá ver a VM agendada para manutenção depois de já ter concluído a implementar de manutenção:
1.  Podemos ter cancelado wave a manutenção e reiniciado-lo com um payload de diferentes. Isto pode dever-se ao que foi detetada payload com falhas e precisamos simplesmente implementar um payload adicional.
2.  A VM foi *serviço healed* para outro nó devido a uma falha de hardware
3.  Selecionou a parar (desalocar) e reinicie a VM
4.  Tiver **encerramento automático** ativada para a VM


## <a name="next-steps"></a>Passos Seguintes

Saiba como pode registar eventos de manutenção de dentro da VM utilizando [agendada eventos](scheduled-events.md).