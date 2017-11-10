---
title: "Configurar a recuperação após desastre de VMs de VMware ou servidores físicos para um site secundário com o Azure Site Recovery | Microsoft Docs"
description: "Saiba como configurar a recuperação após desastre de VMs de VMware, ou o Windows e Linux servidores físicos, para um site secundário com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauarvd
editor: 
ms.assetid: 68616d15-398c-4f40-8323-17b6ae1e65c0
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: raynew
ms.openlocfilehash: 503d7060437d08ed35681fca7f1b9306746b7f44
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Configure a recuperação de desastre no local em máquinas virtuais VMware ou servidores físicos para um site secundário

InMage Scout no [do Azure Site Recovery](site-recovery-overview.md) fornece replicação em tempo real no local a locais de VMware. InMage Scout está incluído no subscrições de serviços do Azure Site Recovery. 


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- [Reveja](site-recovery-support-matrix-to-sec-site.md) os requisitos de suporte para todos os componentes.
- Certifique-se de que estão em conformidade com as máquinas que pretende replicar [replicado suporte máquina](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions).


## <a name="create-a-vault"></a>Criar um cofre

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="choose-a-protection-goal"></a>Escolha um objetivo de proteção

Selecione o que a replicação e onde pretende replicar para.

1. Clique em **recuperação de Site** > **preparar infraestrutura** > **objetivo de proteção**.
2. Selecione **ao site de recuperação** > **Sim, com o VMware vSphere hipervisor**. Em seguida, clique em **OK**.
3. No **Scout configuração**, transferir o software de GA InMage Scout 8.0.1 e a chave de registo. Os ficheiros de configuração de todos os componentes são incluídos no ficheiro. zip transferido.

## <a name="download-and-install-component-updates"></a>Transferir e instalar atualizações de componentes

 Reveja e instale a versão mais recente [atualizações](#updates). Atualizações devem ser instaladas em servidores pela seguinte ordem:

1. Servidor RX (se aplicável)
2. Servidores de configuração
3. Servidores de processos
4. Servidores de destino mestre
5. servidores de vContinuum
6. Servidor de origem (Windows e servidores Linux)

Instale as atualizações da seguinte forma:

> [!NOTE]
>Versão de atualização de ficheiros dos todos os componentes Scout não pode ser o mesmo no ficheiro. zip atualização. A versão mais antiga indicar que não há nenhuma alteração no componente de desde a atualização anterior para esta atualização.

Transferir o [atualizar](https://aka.ms/asr-scout-update6) ficheiro. zip. O ficheiro contém os seguintes componentes: 
  - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.GZ
  - CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
  - UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
  - UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  - vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
  - Bits de update4 UA para RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
1. Extraia os ficheiros. zip.
2. **Servidor RX**: cópia **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** para o servidor de RX e extraia. A pasta extraída, execute **/instalar**.
3. **Servidor de configuração e o servidor de processos**: cópia **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** para o servidor de configuração e o servidor de processos. Faça duplo clique para executá-la.<br>
4. **Servidor de destino mestre do Windows**: para atualizar o agente unificado, copie **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** para o servidor. Faça duplo clique para executá-la. A mesma atualização de agente unificada também é aplicável para o servidor de origem. Se a origem não foi atualizada para atualização 4, deve atualizar o agente unificado.
  A atualização não necessita de aplicar o mestre de destino preparado com **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** como isto é novo GA instalador com todas as alterações mais recentes.
5. **servidor vContinuum**: cópia **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** para o servidor.  Certifique-se de que tiver fechado o Assistente de vContinuum. Faça duplo clique no ficheiro executá-la.
    A atualização não necessita de aplicar no destino mestre preparado com **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** como isto é novo GA instalador com todas as alterações mais recentes.
6. **Servidor de destino principal do Linux**: para atualizar o agente unificado, copie **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** ao mestre de servidor de destino e extraia. A pasta extraída, execute **/instalar**.
7. **Servidor de origem do Windows**: para atualizar o agente unificado, copie **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** ao servidor de origem. Faça duplo clique no ficheiro executá-la. 
    Não precisa de instalar o agente de atualização 5 no servidor de origem se este já tiver sido atualizado para a atualização 4 ou o agente de origem é instalado com o instalador base mais recente **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
8. **Servidor de origem do Linux**: para atualizar o agente unificado, copiar a versão do ficheiro unificada de agente correspondente para o servidor Linux e, extraia-lo. A pasta extraída, execute **/instalar**.  Exemplo: Para o servidor do RHEL 6.7 64 bits, copie **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** para o servidor e extraia. A pasta extraída, execute **/instalar**.

## <a name="enable-replication"></a>Ativar a replicação

1. Configurar a replicação entre a origem e destino sites de VMware.
2. Consulte os seguintes documentos para obter mais informações sobre a instalação, a proteção e recuperação:

   * [Notas de versão](https://aka.ms/asr-scout-release-notes)
   * [Matriz de compatibilidade](https://aka.ms/asr-scout-cm)
   * [Guia do utilizador](https://aka.ms/asr-scout-user-guide)
   * [Guia do utilizador RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Guia de instalação rápida](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>Atualizações

### <a name="site-recovery-scout-801-update-6"></a>Atualização do site Recovery Scout 8.0.1 6 
Atualizada: 12 de Outubro de 2017

Transferir [atualização Scout 6](https://aka.ms/asr-scout-update6).

Scout atualização 6 é uma atualização cumulativa. Contém todas as correções de atualização 1 Update 5 e o novo correções e melhoramentos descritos abaixo. 

#### <a name="new-platform-support"></a>Novo suporte de plataforma
* Foi adicionado suporte para a origem do Windows Server 2016
* Foi adicionado suporte para os seguintes sistemas operativos Linux:
    - Red Hat Enterprise Linux (RHEL) 6.9
    - CentOS 6.9
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* Foi adicionado suporte para VMware Center 6.5

> [!NOTE]
> * Base Unified Agent(UA) installer do Windows tiver sido atualizado para suportar o Windows Server 2016. O instalador novo **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** é compactada com o pacote de Scout GA base (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). O instalador mesmo será utilizado para todos os versão suportada do Windows. 
> * Base Windows vContinuum & destino mestre instalador foi atualizado para suportar o Windows Server 2016. O instalador novo **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** é compactada com o pacote de Scout GA base (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). O instalador mesmo será utilizado para implementar o destino principal do Windows 2016 e o destino principal do Windows 2012R2.
> * Transferir o pacote de GA no portal, conforme descrito em [criar um cofre](#create-a-vault).
>

#### <a name="bug-fixes-and-enhancements"></a>Correções de erros e melhoramentos
- Falha de proteção de reativação pós-falha para a VM com Linux com a lista de discos para ser replicado está vazio no final da configuração.

### <a name="site-recovery-scout-801-update-5"></a>Atualização do site Recovery Scout 8.0.1 5
Atualização Scout 5 é uma atualização cumulativa. Contém todas as correções de atualização 1 a 4 de atualização e as correções novo descritas abaixo.
- Correções de Site de recuperação Scout atualização 4 a 5 de atualização são especificamente para os componentes de vContinuum e de destino mestres.
- Se os servidores de origem, destino principal, configuração, processo e servidores de RX já estiver a executar a atualização 4, em seguida, aplique-apenas no servidor de destino mestre. 

#### <a name="new-platform-support"></a>Novo suporte de plataforma
* SUSE Linux Enterprise Server 11 4(SP4) do Service Pack
* SLES 11 SP4 64 bits **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** é compactada com o pacote de Scout GA base (**InMage_Scout_Standard_8.0.1 GA.zip**). Transferir o pacote de GA no portal, conforme descrito em [criar um cofre](#create-a-vault).


#### <a name="bug-fixes-and-enhancements"></a>Correções de erros e melhoramentos

* Correções para o cluster do Windows uma maior fiabilidade de suporte:
    * Corrigido-alguns o MSCS P2V tornar-se de discos de cluster RAW após a recuperação.
    * A recuperação de cluster do MSCS de P2V Fixed-falha devido a um erro de correspondência de ordem de disco.
    * Fixo - o MSCS operação para adicionar discos falha com um erro de incompatibilidade de tamanho de disco de cluster.
    * Verificar preparação fixo-o para a origem de cluster do MSCS com o mapeamento de RDM LUNs falha na verificação de tamanho.
    * A proteção de cluster de nó único Fixed-falha devido a um problema de incompatibilidade de SCSI. 
    * Fixed-reativação da proteção do servidor de cluster do Windows de P2V falha se existirem discos de cluster de destino. 
    
* Fixo: Durante a proteção de reativação pós-falha, se o servidor de destino principal selecionado não se encontra no mesmo servidor ESXi que a máquina de origem protegida (durante a proteção direta), em seguida, vContinuum escolherá o servidor de destino mestre incorreto durante a recuperação de reativação pós-falha e a recuperação Falha da operação.

> [!NOTE]
> * As correções de cluster de P2V são aplicáveis apenas a clusters MSCS físicos que estão protegidos recentemente com o Site de recuperação Scout Update 5. Para instalar as correções de cluster em clusters de P2V MSCS protegidos com as atualizações mais antigas, siga os passos de atualização mencionados na secção 12 o [notas de versão do Site de recuperação Scout](https://aka.ms/asr-scout-release-notes).
> * se no momento da reativação da proteção, o mesmo conjunto de discos está ativo em cada um de nós do cluster sejam idênticos quando inicialmente protegido, reativação da proteção de um cluster do MSCS físico só pode reutilizar existente discos de destino. Se não estiver, em seguida, utilize os passos manuais na secção 12 [notas de versão do Site de recuperação Scout](https://aka.ms/asr-scout-release-notes), mover os discos do lado do destino para o caminho de arquivo de dados correta, para serem reutilizadas durante a reativação da proteção. Se voltar a proteger o cluster do MSCS no modo de P2V sem seguir os passos de atualização, cria um novo disco no servidor de ESXi do destino. Terá de eliminar manualmente os discos antigos do arquivo de dados.
> * Quando um servidor de origem SLES11 ou SLES11 (sem qualquer service pack) é reiniciado corretamente, em seguida, marcar manualmente o **raiz** disco pares de replicação para a sincronização. Não há qualquer notificação na interface de CX. Se não marcou o disco de raiz para ressincronização, poderá reparar problemas de integridade de dados.


### <a name="azure-site-recovery-scout-801-update-4"></a>Do Azure Site Recovery Scout 8.0.1 atualização 4
Scout atualização 4 é uma atualização cumulativa. Inclui todas as correções de 1 de atualização para o Update 3 e as correções novo descritas abaixo.

#### <a name="new-platform-support"></a>Novo suporte de plataforma

* Foi adicionado suporte para o vCenter/vSphere 6.0, 6.1 e 6.2
* Foi adicionado suporte para estes sistemas de operativos Linux:
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 e 7.2
  * CentOS 7.0, 7.1 e 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64 bits **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** é compactada com o pacote de Scout GA base **InMage_Scout_Standard_8.0.1 GA.zip**. Transferir o pacote de Scout GA do portal conforme descrito em [criar um cofre](#create-a-vault).

#### <a name="bug-fixes-and-enhancements"></a>Correções de erros e melhoramentos

* Encerramento melhorado de processamento para os seguintes sistemas operativos Linux e seja clonado, para evitar problemas de sincronização a indesejável:
    * Red Hat Enterprise Linux (RHEL) 6. x
    * Oracle Linux (OL) 6. x
* Para Linux, todas as permissões de acesso de pasta no diretório de instalação de agente unificada agora estão limitadas apenas ao utilizador local.
* No Windows, uma correção para um tempo limite problema ocorreu durante a emissão de marcadores de consistência distribuída comuns, no bastante carregado aplicações distribuídas como clusters SQL Server e o ponto de partilha.
* Um registo relacionados com a correção no instalador de base do servidor de configuração.
* Foi adicionada uma ligação de transferência para VMware vCLI 6.0 para o instalador de base de destino principal de Windows.
* Foram adicionados verificações adicionais e os registos, para que as alterações de configuração de rede durante simulações de recuperação após desastre e ativação pós-falha.
* Uma correção para um problema que causou a geração de informações de retenção para não ser comunicada ao servidor de configuração.  
* Para clusters físicos, uma correção para um problema que causou o redimensionamento do volume falha no Assistente de vContinuum, ao encolher o volume de origem.
* Uma correção para um problema de proteção de cluster falhou com o erro: "Falha ao localizar a assinatura do disco", quando o disco de cluster é um disco PRDM.
* Uma correção para uma falha de servidor de transporte cxps, causada por uma exceção de fora do intervalo.
* Nome do servidor e colunas de endereço IP estão agora redimensionáveis no **a instalação Push do** página do Assistente de vContinuum.
* Melhoramentos de RX API:
  * A cinco mais recente disponível comuns consistência agora pontos disponíveis (apenas garantida etiquetas).
  * Detalhes de espaço livre e capacidade são apresentados para todos os dispositivos protegidos.
  * Estado do controlador Scout no servidor de origem está disponível.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** pacote base tem:
    * Um instalador base do servidor de configuração atualizada (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
    * Um instalador de base de destino principal de Windows (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
    * Para todas as novas instalações, utilize o novo servidor de configuração e bits de destino mestre GA do Windows.
> * Atualização 4 pode ser aplicada diretamente em 8.0.1 depois da disponibilidade geral
> * O servidor de configuração e atualizações de RX não é possível ser revertidas novamente depois de estas foram aplicadas.


### <a name="azure-site-recovery-scout-801-update-3"></a>Do Azure Site Recovery Scout 8.0.1 Update 3

Todas as atualizações de recuperação de Site são cumulativas. A atualização 3 contém todas as correções de atualização 1 e 2 de atualização. A atualização 3 pode ser aplicada diretamente em 8.0.1 depois da disponibilidade geral O servidor de configuração e atualizações de RX não é possível ser revertidas novamente depois de estas foram aplicadas.

#### <a name="bug-fixes-and-enhancements"></a>Correções de erros e melhoramentos
A atualização 3 corrige os problemas seguintes:

* O servidor de configuração e RX não estão registados no cofre quando forem atrás do proxy.
* O número de horas em que não foi atingido o objetivo de ponto de recuperação (RPO) não é atualizado no relatório de estado de funcionamento.
* O servidor de configuração não está a sincronizar com RX quando os detalhes de hardware do ESX ou detalhes da rede, contenham quaisquer carateres UTF-8.
* Os controladores de domínio do Windows Server 2008 R2 não iniciar após a recuperação.
* Sincronização offline não está a funcionar conforme esperado.
* Após a ativação pós-falha da VM, a eliminação do par de replicação não progresso na consola do servidor de configuração durante muito tempo. Os utilizadores não é possível concluir a reativação pós-falha ou retomar as operações.
* Em geral, as operações de instantâneo pela tarefa de consistência foram otimizadas ajudar a reduzir a aplicação desliga tais como clientes de SQL Server.
* Desempenho de ferramenta (VACP.exe) consistência foi melhorado. Utilização de memória necessária para a criação de instantâneos no Windows foi reduzida.
* Push instalar serviço falhas quando a palavra-passe é superior a 16 carateres.
* vContinuum não verifique e pedir novas credenciais do vCenter, quando as credenciais são modificadas.
* No Linux, o Gestor de cache de destino mestre (cachemgr) não está a transferir ficheiros do servidor de processos. Isto resulta na limitação do par de replicação.
* Quando a ordem de disco de cluster (MSCS) de ativação pós-falha física não é o mesmo em todos os nós, a replicação não está definida para alguns dos volumes de cluster. O cluster tem de ser proteger para tirar partido desta correção.  
* Funcionalidade de SMTP não está a funcionar conforme esperado, depois de RX é atualizado de Scout 7.1 para Scout 8.0.1.
* Foram adicionadas estatísticas mais no registo para a operação de reversão, para controlar o tempo necessário para concluí-la.
* Foi adicionado suporte para sistemas de operativos Linux no servidor de origem:
  * Atualização do Red Hat Enterprise Linux (RHEL) 6 7
  * 7 de atualização do centOS 6
* O servidor de configuração e RX as consolas agora mostrar notificações para o par, que entra no modo de mapa de bits.
* Foram adicionadas as seguintes correções de segurança no RX:
    * Ignorar a autorização através do parâmetro de adulteração: acesso restrito a utilizadores não aplicável.
    * Falsificação de pedidos entre sites: O conceito de token de página foi implementado e será gerado aleatoriamente para todas as páginas. Isto significa que existe apenas uma única sessão instância para o mesmo utilizador e a atualização de página não funciona. Em vez disso, será redirecionado para o dashboard.
    * Carregamento de ficheiros maliciosos: ficheiros estão limitados a extensões específicas: z, aiff, asf, avi, bmp, csv, documento, docx, fla, flv, gif, gz, gzip, jpeg, jpg, registo, mid mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf , sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml e zip.
    * Persistente de scripts entre sites: foram adicionadas validações de entrada.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Do Azure Site Recovery Scout 8.0.1 atualização 2 (atualização 03 15 de Dec)

Correções Update 2 incluem:

* **Servidor de configuração**: problemas que impediu o dia 31 livre medição da funcionalidade de funcionar conforme esperado, quando o servidor de configuração foi registado no Site Recovery.
* **Agente unificada**: corrigir um problema no que resultaram na atualização não a ser instalada no servidor de destino mestre, durante a atualização da versão 8.0 para 8.0.1 Update 1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Do Azure Site Recovery Scout 8.0.1 atualização 1
A atualização 1 inclui as seguintes correções e novas funcionalidades:

* 31 dias de proteção livre por instância de servidor. Isto permite-lhe testar a funcionalidade ou configurar um prova de conceito.
* Todas as operações no servidor, incluindo a ativação pós-falha e a reativação pós-falha, são gratuitas primeiro 31 dias. A hora é iniciado quando um servidor primeiro estiver protegido por Scout de recuperação de Site. A contar do dia 32nd, cada servidor protegido é cobrado à taxa instância padrão para proteção de recuperação de Site para um site de propriedade de cliente.
* Em qualquer altura, o número de servidores protegidos atualmente a ser-lhe cobrados está disponível na **Dashboard** no cofre.
* Foi adicionado suporte para vSphere Interface de linha de comandos (vCLI) 5.5 atualização 2.
* Foi adicionado suporte para estes sistemas de operativos Linux no servidor de origem:
    * RHEL 6 Update 6
    * RHEL 5 atualizar 11
    * Atualização 6 do centOS 6
    * Atualização do centOS 5 11
* Correções de erros para resolver os seguintes problemas:
  * Falha do registo do cofre para o servidor de configuração ou o servidor de RX.
  * Volumes de cluster não aparecem como esperados quando agrupados que VMS são proteger estes retomar.
  * Reativação pós-falha falha quando o servidor de destino principal é alojado num servidor diferente ESXi partir de VMs de produção no local.
  * Permissões de ficheiro de configuração são alteradas ao atualizar para 8.0.1. Esta alteração afeta a proteção e as operações.
  * O limiar de ressincronização não é imposto conforme esperado, fazendo com que o comportamento de replicação inconsistente.
  * As definições de RPO não aparecem corretamente na consola do servidor de configuração. O valor dos dados descomprimidos incorretamente mostra o valor comprimido.
  * A operação de remoção não elimina conforme esperado no Assistente de vContinuum e replicação não é eliminada a partir da consola do servidor de configuração.
  * No Assistente de vContinuum, o disco é automaticamente não seleccionado quando clicar em **detalhes** na vista de disco, durante a proteção de VMs do MSCS.
  * No cenário de conversão físico-virtual (P2V), serviços HP necessários (como CIMnotify e CqMgHost) não são movidos para manual na recuperação VM. Este problema resulta no momento do arranque adicionais.
  * A proteção de VM com Linux falha quando existirem mais de 26 discos no servidor de destino principal.

