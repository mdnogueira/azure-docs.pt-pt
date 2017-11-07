---
title: "Configurar a recuperação após desastre de VMs de VMware ou servidores físicos para um site secundário | Microsoft Docs"
description: "Este artigo explica como replicar servidores físicos no local as VMs VMware ou Windows/Linux para um site secundário, com o serviço do Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: b2cba944-d3b4-473c-8d97-9945c7eabf63
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2017
ms.author: raynew
ms.openlocfilehash: 8cfaa56735c1f4e2e01b58fdde2ad0e77b388762
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="set-up-disaster-recovery-of-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Configurar a recuperação após desastre de máquinas virtuais VMware ou servidores físicos para um site secundário


InMage Scout no Azure Site Recovery fornece em tempo real replicação entre sites do VMware no local. InMage Scout está incluído no subscrições de serviços do Azure Site Recovery.

Se não tiver uma subscrição do Azure, [criar uma conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="create-a-vault"></a>Criar um cofre
1. Inicie sessão no [portal do Azure](https://portal.azure.com/) > **Serviços de Recuperação**.
2. Clique em Novo > gestão > cópia de segurança e recuperação de sites (OMS). Em alternativa, pode clicar em Procurar > cofre dos serviços de recuperação > Adicionar.
3. Em **Nome**, introduza um nome amigável para identificar o cofre. Se tiver mais do que uma subscrição, selecione uma delas.
4. No **grupo de recursos** criar um novo grupo de recursos ou selecione um existente. Especifique uma região do Azure para concluir os campos obrigatórios.
5. No **localização**, selecione a região geográfica do cofre. Para verificar as regiões suportadas, consulte [preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Se quiser para aceder rapidamente ao Cofre no Dashboard do clique em Afixar ao dashboard e, em seguida, clique em criar.
7. O novo cofre é apresentado no Dashboard > todos os recursos e nos serviços de recuperação principal cofres dos página.

## <a name="configure-the-vault-and-download-inmage-scout-components"></a>Configurar o Cofre e transferir os componentes de InMage Scout
1. Nos serviços de recuperação cofres de página, selecione o cofre em clique em **definições**.
2. No **definições** > **introdução** clique **recuperação de Site** > Passo 1: **preparar infraestrutura**  >  **Objetivo de proteção**.
3. No **objetivo de proteção** selecione ao site de recuperação e selecione Sim, com o VMware vSphere hipervisor. Em seguida, clique em OK.
4. No **configuração Scout**, clique em chave de software e o registo do InMage Scout 8.0.1 GA de transferência para transferir. Os ficheiros de configuração para todos os componentes necessários estão no ficheiro. zip transferido.

## <a name="step-3-install-component-updates"></a>Passo 3: Instalar atualizações de componentes
Leia sobre a versão mais recente [atualizações](#updates). Irá instalar os ficheiros de atualização nos servidores pela seguinte ordem:

1. Servidor RX se forem relevantes
2. Servidores de configuração
3. Servidores de processos
4. Servidores de destino mestre
5. servidores de vContinuum
6. Servidor de origem (o Windows e o servidor Linux)

Instale as atualizações da seguinte forma:

1. Transferir o [atualizar](https://aka.ms/asr-scout-update5) ficheiro. zip. Este ficheiro. zip contém os seguintes ficheiros:

   * RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.GZ
   * CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe
   * UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
   * UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
   * vCon_Windows_8.0.5.0_GA_Update_5_11525767_20Apr17.exe
   * Bits de update4 UA para RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
2. Extraia os ficheiros. zip.<br>
3. **Para o servidor de RX**: cópia **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** para o servidor de RX e extraia. A pasta extraída, execute **/instalar**.<br>
4. **Para o servidor de processo do servidor de configuração**: cópia **CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe** para o servidor de configuração e o servidor de processos. Faça duplo clique para executá-la.<br>
5. **Para o servidor de destino principal do Windows**: para atualizar o agente unificado, copie **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** para o servidor de destino principal. Faça duplo clique para executá-la. Tenha em atenção que o agente unificado também é aplicável ao servidor de origem se a origem não for atualizada até Update4. Deve instalá-lo no servidor de origem bem, tal como mencionado mais tarde nesta lista.<br>
6. **Para o servidor vContinuum**: cópia **vCon_Windows_8.0.5.0_GA_Update_5_11525767_20Apr17.exe** para o servidor vContinuum.  Certifique-se de que tiver fechado o Assistente de vContinuum. Faça duplo clique no ficheiro executá-la.<br>
7. **Para o servidor de destino principal do Linux**: para atualizar o agente unificado, copie **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** ao mestre de servidor de destino e extraia. A pasta extraída, execute **/instalar**.<br>
8. **Para o servidor de origem do Windows**: não é necessário instalar o agente de atualização 5 na origem se já estiver a executar a atualização 4. Se tiver o menor do que a atualização 4, aplicam-se o agente de atualização 5.
Para atualizar o agente unificado, copie **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** ao servidor de origem. Faça duplo clique para executá-la. <br>
9. **Para o servidor de origem do Linux**: para atualizar o agente unificado, copie versão correspondente do ficheiro UA para o servidor Linux e extraia. A pasta extraída, execute **/instalar**.  Exemplo: Para o servidor do RHEL 6.7 64 bits, copie **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** para o servidor e extraia. A pasta extraída, execute **/instalar**.

## <a name="step-4-set-up-replication"></a>Passo 4: Configurar a replicação
1. Configurar a replicação entre a origem e destino sites de VMware.
2. Para obter orientações, utilize a documentação do InMage Scout que é transferida com o produto. Em alternativa, pode aceder a documentação da seguinte forma:

   * [Notas de versão](https://aka.ms/asr-scout-release-notes)
   * [Matriz de compatibilidade](https://aka.ms/asr-scout-cm)
   * [Guia do utilizador](https://aka.ms/asr-scout-user-guide)
   * [Guia do utilizador RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Guia de instalação rápida](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>Atualizações
### <a name="azure-site-recovery-scout-801-update-5"></a>Do Azure Site Recovery Scout 8.0.1 atualização 5
Atualização Scout 5 é uma atualização cumulativa. Tem todas as correções de atualização1 até update4 e seguintes correções de erros novas e melhorias.
Correções que são adicionadas a partir de update4 Scout de recuperação de Site para update5 são específicas para componentes de destino mestre e vContinuum. Se todos os seus servidores de origem, destino principal, o servidor de configuração, o servidor de processos e o RX já estejam nesse update4 Scout de recuperação de Site terá de aplicar a atualização 5 apenas no servidor de destino mestre. 

**Novo suporte de plataforma**
* SUSE Linux Enterprise Server 11 4(SP4) do Service Pack

> [!NOTE]
> SLES 11 SP4 64 bits **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** é compactada com o pacote do base Scout GA **InMage_Scout_Standard_8.0.1 GA.zip**. Transferir o pacote de Scout GA do portal como mencionado em [passo 1](#step-1-create-a-vault).
>

**Correções de erros e melhoramentos**

* Aumentar a fiabilidade do suporte de Cluster do Windows
    * Sometime corrigidos alguns o MSCS P2V tornar-se de discos de cluster RAW após a recuperação
    * Recuperação de cluster do MSCS de P2V Fixed-falha devido a erro de correspondência de ordem de disco
    * Cluster do Fixed-MSCS adicionar discos operação falha com erro de correspondência de tamanho de disco
    * Cluster do MSCS de origem do Fixed-com verificação de disponibilidade de mapeamento de RDM LUNs falha na verificação de tamanho
    * A proteção de cluster de nó único Fixed-falha devido ao problema de incompatibilidade de SCSI 
    * Voltar a fixed-proteger do servidor de cluster do Windows de P2V falha se existirem discos de cluster de destino. 
    
* Durante a proteção de reativação pós-falha, se MT selecionado não está no mesmo servidor ESXi que a máquina de origem protegida (durante a proteção direta), em seguida, vContinuum escolherá MT incorreto durante a recuperação de reativação pós-falha e, subsequentemente, operação de recuperação falhará.

> [!NOTE]
> 
> * Acima P2V cluster correções são aplicáveis a apenas esses MSCS clusters físicos que estão protegidos raiz com update5 Scout de recuperação de Site. Para beneficiar do cluster corrige no cluster do P2V MSCS já protegido com atualizações anteriores, tem de seguir os passos de atualização que são mencionados na secção 12, atualização protegidos clusters de P2V MSCS para Scout atualização 5 do [notas de versão](https://aka.ms/asr-scout-release-notes) .
> 
> * Voltar a proteger de cluster do MSCS físico pode reutilizar discos de destino existente, apenas se o momento da reativação da proteção, o mesmo conjunto de discos estão ativas em cada um de nós do cluster sejam idênticos quando inicialmente protegido. Se não, em seguida, existem passos manuais tal como mencionado na secção 12 o [notas de versão](https://aka.ms/asr-scout-release-notes) para mover os discos do lado do destino para o caminho de arquivo de dados correto para utilizar novamente durante a reativação da proteção. Se Proteja novamente o cluster do MSCS no modo de P2V sem os seguintes passos de atualização, irá criar o novo disco no servidor de ESXi do destino. Terá de eliminar manualmente os discos antigos do arquivo de dados.
> 
> * Sempre que a origem SLES11 ou SLES11 com qualquer servidor de pacote de serviço é reiniciado corretamente, em seguida, deve marcar manualmente o **raiz** pares de replicação para sincronizar novamente de disco não será notificado na placa CX IU. Se não marcou o disco de raiz para ressincronização, poderá ver os problemas de integridade (DI) de dados.
> 

### <a name="azure-site-recovery-scout-801-update-4"></a>Do Azure Site Recovery Scout 8.0.1 atualização 4
Scout atualização 4 é uma atualização cumulativa. Tem todas as correções de atualização1 até update3 e seguintes correções de erros novas e melhorias.

**Novo suporte de plataforma**

* Foi adicionado suporte para o vCenter/vSphere 6.0, 6.1 e 6.2
* Foi adicionado suporte para os seguintes sistemas operativos Linux
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 e 7.2
  * CentOS 7.0, 7.1 e 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64 bits **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** é compactada com o pacote do base Scout GA **InMage_Scout_Standard_8.0.1 GA.zip**. Transferir o pacote de Scout GA do portal como mencionado em [passo 1](#step-1-create-a-vault).
>
>

**Correções de erros e melhoramentos**

* Encerramento melhorado processamento pelos seguintes sos de Linux e clones para impedir problemas indesejáveis sincronizar novamente.
  * Red Hat Enterprise Linux (RHEL) 6. x
  * Oracle Linux (OL) 6. x
* Para Linux, conclua o acesso da pasta de permissões no diretório de instalação do agente unificada agora estão limitadas apenas ao utilizador local.
* No Windows exceder o tempo limite problema ao emitir marca de livro distribuída consistência comum nos descontos elevados carregar aplicações distribuídas, como clusters SQL e SharePoint.
* Registo adicionado relacionados com a correção no instalador base CX.
* Ligação de transferência do VMware vCLI 6.0 é adicionada ao programa de instalação base do destino principal do Windows.
* Adicionar mais verificações e os registos para alterações de configurações de rede durante a ativação pós-falha e aprofunda de DR.
* Informações de retenção sometime não foi reportadas para a placa CX.  
* Para um cluster físico, o volume tamanho novamente a operação através do Assistente de vContinuum falha quando ocorre a operação de encolhimento de volume de origem.
* A proteção do cluster falhou com o erro "Falha ao localizar a assinatura do disco" quando o disco de cluster é disco PRDM.
* Falha do servidor de transporte cxps devido à exceção de fora do intervalo.
* Nome do servidor e colunas IP estão agora redimensionáveis na página de instalação push do Assistente de vContinuum.
* Melhoramentos de API RX
  * Fornece cinco mais recente disponível comuns consistência pontos (apenas garantida etiquetas).
  * Fornece capacidade e detalhes de espaço livre para todos os dispositivos protegidos.
  * Fornece o estado do controlador de Scout no servidor de origem.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** pacote base agora atualizou instalador base CX **InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe** e o programa de instalação base do destino principal de Windows **InMage_ Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**. Para todos os nova instalação utilize o bits de GA CX e de destino principal do Windows novo.
> * Atualização 4 pode ser aplicada diretamente em 8.0.1 depois da disponibilidade geral
> * O servidor de configuração e atualizações de RX não é possível revertidas após a que está a ser aplicadas no sistema.
>
>

### <a name="azure-site-recovery-scout-801-update-3"></a>Do Azure Site Recovery Scout 8.0.1 Update 3
A atualização 3 inclui as seguintes correções de erros e melhoramentos:

* O servidor de configuração e RX falhar registar no Cofre de recuperação de Site quando forem atrás do proxy.
* O número de horas que ainda não foi cumprido o objetivo de ponto de recuperação (RPO) não está atualizado no relatório de estado de funcionamento.
* O servidor de configuração não está a sincronizar com RX quando os detalhes de hardware do ESX ou detalhes da rede contém caracteres UTF-8.
* Os controladores de domínio do Windows Server 2008 R2 não conseguem efetuar o arranque após a recuperação.
* Sincronização offline não está a funcionar conforme esperado.
* Após a ativação pós-falha de máquina virtual (VM), a eliminação do par de replicação obtém presa na IU do CX durante muito tempo e os utilizadores não é possível concluir a reativação pós-falha ou retomar a operação.
* Em geral instantâneo operações que são efetuadas pela tarefa de consistência foram otimizadas para ajudar a reduzir a aplicação desliga como os clientes de SQL.
* O desempenho da ferramenta de consistência (VACP.exe) foi melhorado, reduzindo a utilização de memória necessária para a criação de instantâneos no Windows.
* Push instalar serviço falhas quando a palavra-passe é superior a 16 carateres.
* vContinuum não está a verificar e pedir para novas credenciais do vCenter quando as credenciais forem alteradas.
* No Linux, o Gestor de cache de destino mestre (cachemgr) não está a transferir ficheiros do servidor de processos, o que resulta em limitação do par de replicação.
* Quando a ordem de disco de cluster (MSCS) de ativação pós-falha física não for igual em todos os nós, a replicação não está definida para alguns dos volumes de cluster.
  <br/>Tenha em atenção que o cluster precisa de proteger para tirar partido desta correção.  
* Funcionalidade de SMTP não está a funcionar conforme esperado após RX é atualizado de Scout 7.1 para Scout 8.0.1.
* Foram adicionadas estatísticas mais no registo para a operação de reversão rastrear o tempo que demorou a concluí-la.
* Foi adicionado suporte para sistemas de operativos Linux no servidor de origem:
  * Atualização do Red Hat Enterprise Linux (RHEL) 6 7
  * 7 de atualização do centOS 6
* A placa CX e RX IU agora podem apresentar a notificação para o par, que entra no modo de mapa de bits.
* Foram adicionadas as seguintes correções de segurança no RX:

| **Descrição do problema** | **Procedimentos de implementação** |
| --- | --- |
| Ignorar a autorização através do parâmetro de adulteração |Acesso restrito a utilizadores não aplicável. |
| Falsificação de pedidos entre sites |Implementado o conceito de token de página, o que gera aleatoriamente para todas as páginas. <br/>Com esta opção, verá: <li> Não há apenas uma única sessão instância para o mesmo utilizador.</li><li>Atualização de página não funciona –-irá redirecionar para o dashboard.</li> |
| Carregamento de ficheiros maliciosos |Ficheiros restritos para determinadas extensões. Permitidos extensões são: 7z, aiff, asf, avi, bmp, csv, documento, docx, fla, flv, gif, gz, gzip, jpeg, jpg, registo, mid mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar , tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml e zip. |
| Persistente de scripts entre sites |Adicionar validações de entrada. |

> [!NOTE]
> * Todas as atualizações de recuperação de Site são cumulativas. A atualização 3 tem todas as correções de atualização 1 e 2 de atualização. A atualização 3 pode ser aplicada diretamente em 8.0.1 depois da disponibilidade geral
> * O servidor de configuração e atualizações de RX não é possível revertidas após a que está a ser aplicadas no sistema.
>
>

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Do Azure Site Recovery Scout 8.0.1 atualização 2 (atualização 03 15 de Dec)
Correções Update 2 incluem:

* **Servidor de configuração**: corrigir um problema que impediu a funcionalidade de medição livre 31 dias funcionar conforme esperado quando o servidor de configuração foi registado no Site Recovery.
* **Agente unificada**: corrigir um problema no que resultaram na atualização não seja instalada no servidor de destino principal quando este tiver sido atualizado de versão 8.0 para 8.0.1 Update 1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Do Azure Site Recovery Scout 8.0.1 atualização 1
A atualização 1 inclui as seguintes correções e novas funcionalidades:

* 31 dias de proteção livre por instância de servidor. Isto permite-lhe testar a funcionalidade ou configurar um prova de conceito.
  * Todas as operações no servidor, incluindo a ativação pós-falha e a reativação pós-falha, são gratuitas primeiro 31 dias, começando pelo momento em que um servidor primeiro está protegido com Scout de recuperação de Site.
  * Do 32nd dia em diante, será cobrado cada servidor protegido, a taxa de instância padrão para proteção do Azure Site Recovery para um site de propriedade de cliente.
  * Em qualquer altura, o número de servidores protegidos que estão atualmente a ser cobrados está disponível na página do Dashboard do cofre do Azure Site Recovery.
* Adicionado suporte para vSphere Interface de linha de comandos (vCLI) 5.5 atualização 2.
* Adicionado suporte para sistemas de operativos Linux no servidor de origem:
  * RHEL 6 Update 6
  * RHEL 5 atualizar 11
  * Atualização 6 do centOS 6
  * Atualização do centOS 5 11
* Correções de erros para resolver os seguintes problemas:
  * Falha do registo do cofre para o servidor de configuração ou o servidor de RX.
  * Volumes de cluster não aparecem conforme esperado quando máquinas virtuais em cluster são proteger quando estes retomar.
  * Reativação pós-falha falha quando o servidor de destino principal é alojado num servidor ESXi diferentes das máquinas de virtuais de produção no local.
  * Permissões de ficheiro de configuração são alteradas ao atualizar para 8.0.1, que afeta a proteção e as operações.
  * O limiar de ressincronização não é imposto conforme esperado, que resulta comportamento de replicação inconsistente.
  * As definições de RPO não são apresentados corretamente na interface de servidor de configuração. O valor dos dados descomprimidos incorretamente mostra o valor comprimido.
  * A operação de remoção não elimina conforme esperado no Assistente de vContinuum e replicação não é eliminada da interface de servidor de configuração.
  * No Assistente de vContinuum, o disco é automaticamente não seleccionado quando clicar em **detalhes** na vista de disco durante a proteção de máquinas de virtuais MSCS.
  * Durante o cenário de conversão físico-virtual (P2V), os serviços de HP necessários, como CIMnotify e CqMgHost, não são movidos para manual na recuperação da máquina virtual. Isto resulta num tempo de arranque adicionais.
  * Proteção de máquinas virtuais do Linux falha quando não existem mais de 26 discos no servidor de destino mestre.

## <a name="next-steps"></a>Passos seguintes
Após alguma questão que tem no [fórum do Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).
