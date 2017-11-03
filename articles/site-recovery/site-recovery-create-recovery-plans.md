---
title: "Criar planos de recuperação para ativação pós-falha e recuperação no Azure Site Recovery | Microsoft Docs"
description: "Descreve como criar e personalizar planos de recuperação no Azure Site Recovery, para efetuar a ativação pós-falha e recuperação de VMs e servidores físicos"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/25/2017
ms.author: raynew
ms.openlocfilehash: 202e0ac8be36e9156ec16fadc1b722f4eb3d1432
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="create-recovery-plans"></a>Criar planos de recuperação


Este artigo fornece informações sobre como criar e personalizar planos de recuperação no [do Azure Site Recovery](site-recovery-overview.md).

Publique comentários ou perguntas na parte inferior deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

 Crie planos de recuperação para fazer o seguinte:

* Defina grupos de computadores que efetuam a ativação pós-falha em conjunto e, em seguida, iniciar a cópia de segurança em conjunto.
* Modelo dependências entre máquinas, ao agrupá-los em conjunto para uma recuperação planear grupo. Por exemplo, para efetuar a ativação pós-falha e trazer uma aplicação específica, agrupa todas as VMs para essa aplicação para o mesmo grupo de plano de recuperação.
* Execute uma ativação pós-falha. Pode executar um teste de ativação pós-falha planeada, ou a ativação pós-falha não planeada num plano de recuperação.


## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

1. Clique em **planos de recuperação** > **criar plano de recuperação**.
   Especifique um nome para o plano de recuperação e uma origem e de destino. A localização de origem tem de ter as máquinas virtuais que estão ativadas para ativação pós-falha e recuperação.

    - Para o VMM para a replicação do VMM, selecione **tipo de origem** > **VMM**e os servidores do VMM de origem e de destino. Clique em **Hyper-V** para ver as nuvens que estão protegidas.
    - Para VMM no Azure, selecione **tipo de origem** > **VMM**.  Selecione o servidor do VMM de origem, e **Azure** como destino.
    - Para replicação de Hyper-V para o Azure (sem VMM), selecione **tipo de origem** > **site Hyper-V**. Selecione o site como a origem e **Azure** como destino.
    - Para uma VM de VMware ou um servidor físico no local para o Azure, selecione um servidor de configuração como origem, e **Azure** como destino.
    - Para um plano de recuperação do Azure para o Azure, selecione uma região do Azure como a origem e de uma região secundária do Azure como destino. Regiões do Azure secundárias são apenas os que são protegidas máquinas virtuais.
2. No **selecionar máquinas virtuais**, selecione as máquinas virtuais (ou grupo de replicação) que pretende adicionar ao grupo predefinido (grupo 1) no plano de recuperação.

## <a name="customize-and-extend-recovery-plans"></a>Personalizar e expandir os planos de recuperação

Pode personalizar e expandir os planos de recuperação:

- **Adicionar novos grupos**— adicionar grupos de plano de recuperação adicionais (até sete) para o grupo predefinido e, em seguida, adicione mais computadores ou grupos de replicação a esses grupos do plano de recuperação. Grupos são numerados pela ordem na qual pode adiciona. Uma máquina virtual ou grupo de replicação, só pode ser incluído no grupo do plano de recuperação de um.
- **Adicionar uma ação manual**— pode adicionar ações manuais que são executados antes ou depois de um grupo do plano de recuperação. Quando executa o plano de recuperação, interrompe o momento em que a ação manual que inserido. Uma caixa de diálogo pede-lhe para especificar que a ação manual foi concluída.
- **Adicionar um script**— pode adicionar scripts que são executados antes ou depois de um grupo do plano de recuperação. Quando adiciona um script, adiciona um novo conjunto de ações para o grupo. Por exemplo, será criado um conjunto de pré-passos de para grupo 1 com o nome: grupo 1: pré-passos. Todos os pré-passos de serão listados no interior este conjunto. Só é possível adicionar um script no site primário se tiver um servidor VMM implementado.
- **Adicionar os runbooks do Azure**— pode expandir a planos de recuperação com os runbooks do Azure. Por exemplo, para automatizar tarefas ou criar recuperação único passo. [Saiba mais](site-recovery-runbook-automation.md).

## <a name="add-scripts"></a>Adicione scripts

Pode utilizar scripts do PowerShell no seu plano de recuperação.

 - Certifique-se de que os scripts utilizam blocos try-catch para que as exceções são processadas normalmente.
    - Se existir uma exceção no script, deixa de ser executada e a tarefa mostra como falhado.
    - Se ocorrer um erro, qualquer parte restante do script não é executado.
    - Se ocorrer um erro ao executar uma ativação pós-falha não planeada, o plano de recuperação continua.
    - Se ocorrer um erro ao executar uma ativação pós-falha planeada, deixa o plano de recuperação. Tem de corrigir o script, verifique se funciona como esperado e execute a recuperação planear novamente.
- O comando de anfitrião de escrita não funciona no script de plano de recuperação e o script irá falhar. Para criar a saída, crie um script de proxy que por sua vez, execute o script principal. Certifique-se de que todas as saídas é direcionada utilizando o >> comando.
  * O script exceder o tempo limite se esta aplicação não devolver num 600 segundos.
  * Se nada é escrito em STDERR, o script é classificado como falhado. Estas informações são apresentadas nos detalhes da execução do script.

Se estiver a utilizar o VMM na sua implementação:

* Executar scripts num plano de recuperação no contexto da conta de serviço do VMM. Certifique-se que esta conta tem permissões de leitura para a partilha remota, no qual o script está localizado. Teste o script para serem executados quando o nível de privilégios de conta de serviço do VMM.
* Cmdlets do VMM são entregues por um módulo do Windows PowerShell. O módulo é instalado quando instalar a consola do VMM. Podem ser carregado para o script, utilizando o seguinte comando do script:
   - Import-Module-Name virtualmachinemanager. [Saiba mais](https://technet.microsoft.com/library/hh875013.aspx).
* Certifique-se de que tem pelo menos um servidor de biblioteca na sua implementação do VMM. Por predefinição, o caminho de partilha de biblioteca para um servidor VMM se encontra localmente num servidor do VMM, com o nome de pasta MSCVMMLibrary.
    * Se o caminho de partilha de biblioteca remoto (ou local, mas não partilhado com MSCVMMLibrary), configurar a partilha da seguinte forma (utilizando \\libserver2.contoso.com\share\ como um exemplo):
      * Abra o Editor de registo e navegue para **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.
      * Edite o valor **ScriptLibraryPath** e coloque-o como \\libserver2.contoso.com\share\. Especifique o FQDN completo. Fornece permissões para a localização da partilha. Tenha em atenção que este é o nó de raiz da partilha. **Para verificar isto, pode procurar a biblioteca no nó raiz no VMM. O caminho que abre será a raiz do caminho - aquele que terá de utilizar a variável**.
      * Certifique-se de que testa o script com uma conta de utilizador que tem as mesmas permissões à conta de serviço do VMM. Verifica se autónomo testar scripts executadas da mesma forma como estes serão nos planos de recuperação. No servidor do VMM, defina a política de execução para ignorar da seguinte forma:
        * Abra o **64-bit Windows PowerShell** consola utilizando privilégios elevados.
        * Tipo: **Set-executionpolicy ignorar**. [Saiba mais](https://technet.microsoft.com/library/ee176961.aspx).

> [!IMPORTANT]
> Deve definir a política de execução para ignorar o PowerShell apenas de 64 bits. Se tiver definido-lo para o powershell de 32 bits, os scripts serão exeute não.

## <a name="add-a-script-or-manual-action-to-a-plan"></a>Adicionar uma ação de script ou manual para um plano

Pode adicionar um script para o grupo predefinido do plano de recuperação depois de ter adicionado VMs ou grupos de replicação para a mesma e criado o plano.

1. Abra o plano de recuperação.
2. Clique num item de **passo** lista e, em seguida, clique em **Script** ou **ação Manual**.
3. Especifique se pretende adicionar o script ou ação antes ou depois do item selecionado. Utilize o **mover para cima** e **mover para baixo** botões, mover a posição do script ou reduzir verticalmente.
4. Se adicionar um script do VMM, selecione **ativação pós-falha para o script VMM**. No **caminho do Script**, escreva o caminho relativo para a partilha. No exemplo VMM abaixo, especifique o caminho: **\RPScripts\RPScript.PS1**.
5. Se adicionar uma run book a automatização do Azure, especifique a conta de automatização do Azure no qual está localizado o runbook e selecione o script do runbook do Azure adequada.
6. Efetue uma ativação pós-falha do plano de recuperação, para se certificar de que o script funciona conforme esperado.


### <a name="add-a-vmm-script"></a>Adicionar um script do VMM

Se tiver um site de origem do VMM, pode criar um script no servidor do VMM e incluí-la no seu plano de recuperação.

1. Crie uma nova pasta na partilha de biblioteca. Por exemplo, \<Nome_servidor_vmm > \MSSCVMMLibrary\RPScripts. Coloque-o na origem e servidores do VMM de destino.
2. Criar o script (por exemplo RPScript) e verificar funciona conforme esperado.
3. Coloque o script na localização \<Nome_servidor_vmm > \MSSCVMMLibrary, nos servidores do VMM de origem e de destino.


## <a name="next-steps"></a>Passos seguintes

[Saiba mais](site-recovery-failover.md) sobre como executar as ativações pós-falha.
