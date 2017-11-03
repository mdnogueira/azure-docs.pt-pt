---
title: "Série 8000 do StorSimple como destino de cópia de segurança com Veeam | Microsoft Docs"
description: "Descreve a configuração de destino de cópia de segurança do StorSimple com Veeam."
services: storsimple
documentationcenter: 
author: harshakirank
manager: matd
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2016
ms.author: hkanna
ms.openlocfilehash: cc1c7a3f77af76c451bb6e97a081a01c119333b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>StorSimple como destino de cópia de segurança com Veeam

## <a name="overview"></a>Descrição geral

Azure StorSimple é uma solução de armazenamento de nuvem híbrida da Microsoft. StorSimple endereços complexidades de crescimento de dados exponencial utilizando uma conta de armazenamento do Azure como uma extensão da solução no local e os dados automaticamente em camadas através de armazenamento no local e na nuvem.

Neste artigo, discutimos a integração do StorSimple com Veeam e melhores práticas para integrar ambas as soluções. Podemos também o tornam as recomendações sobre como configurar Veeam melhor integrar StorSimple. Iremos diferir Veeam melhores práticas, arquitetos de cópia de segurança e administradores para a melhor forma de configurar Veeam para satisfazer os requisitos de cópia de segurança individuais e contratos de nível de serviço (SLAs).

Embora é ilustrar os passos de configuração e conceitos chave, este artigo não é de um guia passo a passo de instalação ou a configuração. Iremos partem do princípio de que a infraestrutura e componentes básicos estão por ordem de trabalho e está preparado para suportar os conceitos dita.

### <a name="who-should-read-this"></a>Quem deve ler este artigo?

As informações neste artigo será mais úteis para administradores de cópia de segurança, os administradores de armazenamento e arquitetos de armazenamento que tem conhecimento do armazenamento, Windows Server 2012 R2, Ethernet, cloud services e Veeam.

### <a name="supported-versions"></a>Versões suportadas

-   Veeam 9 e versões posteriores
-   [StorSimple Update 3 e versões posteriores](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Por que motivo StorSimple como destino de cópia de segurança?

StorSimple é uma boa opção para um destino de cópia de segurança porque:

-   Fornece armazenamento standard, local para aplicações de cópia de segurança utilizar como um destino de cópia de segurança rápida, sem quaisquer alterações. Também pode utilizar StorSimple para que um restauro rápido das cópias de segurança recentes.
-   A nuvem em camadas é totalmente integrada com uma conta de armazenamento em nuvem do Azure para utilizar armazenamento económico e do Azure.
-   Automaticamente fornece armazenamento externo para recuperação após desastre.


## <a name="key-concepts"></a>Conceitos-chave

À semelhança de qualquer solução de armazenamento, uma avaliação tenha o cuidado de desempenho do armazenamento da solução, SLA, a taxa de alteração e as suas necessidades de crescimento de capacidade é fundamental para êxito. A ideia principal é que, introduzindo um escalão de nuvem, os tempos de acesso e throughputs para a play nuvem de uma função fundamental na capacidade do StorSimple para efetuar a tarefa.

StorSimple foi concebido para fornecer armazenamento para aplicações que funcionam num conjunto de trabalho bem definidos de dados (dados). Neste modelo, o conjunto de trabalho de dados é armazenado nas camadas locais e o conjunto nonworking/frio/arquivados restantes de dados está em camadas para a nuvem. Este modelo é representado na figura seguinte. A linha quase flat verde representa os dados armazenados nas camadas locais do dispositivo StorSimple. A linha vermelha representa a quantidade total de dados armazenados na solução StorSimple em todas as camadas. O espaço entre a linha de flat verde e a curva vermelha exponencial representa a quantidade total de dados armazenados na nuvem.

**Criação de camadas do StorSimple**
![diagrama camado do StorSimple](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

Com esta arquitetura em mente, encontrará que StorSimple é Idealmente adequado para funcionar como um destino de cópia de segurança. Pode utilizar StorSimple para:

-   Execute os restauros mais frequentes do conjunto de trabalho local dos dados.
-   Utilize a nuvem de recuperação de desastre num local externo e os dados mais antigos, onde são menos frequentes restauros.

## <a name="storsimple-benefits"></a>Vantagens do StorSimple

StorSimple fornece uma solução no local que está totalmente integrada com o Microsoft Azure, ao tirar partido do acesso totalmente integrado no local e de armazenamento na nuvem.

StorSimple utiliza camadas automática entre o dispositivo no local, o que tem o dispositivo de estado sólido (SSD) e ligados à série armazenamento de SCSI (SAS) e o armazenamento do Azure. Criação automática de camadas mantém os dados acedidos com frequência local, em camadas SSD e SAS. -Move os dados acedidos com pouca frequência para armazenamento do Azure.

StorSimple oferece destas vantagens:

-   Algoritmos de eliminação de duplicados e compressão exclusivos que utilizam a nuvem para obter níveis de eliminação de duplicados inédita
-   Elevada disponibilidade
-   Georreplicação ao utilizar a georreplicação do Azure
-   Integração do Azure
-   Encriptação de dados na nuvem
-   Recuperação após desastre melhorada e conformidade

Embora StorSimple apresenta dois cenários de implementação principal (destino de cópia de segurança primário e secundário destino de cópia de segurança), fundamentalmente, é um dispositivo de armazenamento de blocos simples. StorSimple todos os a compressão e eliminação de duplicados. Perfeitamente envia e obtém dados entre a nuvem e a aplicação e o sistema de ficheiros.

Para mais informações sobre StorSimple, consulte [série 8000 do StorSimple: solução de armazenamento de nuvem híbrida](storsimple-overview.md). Além disso, pode rever o [as especificações da série 8000 do StorSimple técnicas](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Utilizar um StorSimple dispositivo como destino de cópia de segurança só é suportado para 3 de atualização de 8000 do StorSimple e versões posteriores.

## <a name="architecture-overview"></a>Descrição geral da arquitetura

As tabelas seguintes mostram as orientações inicial do modelo a arquitetura de dispositivo.

**As capacidades do StorSimple para locais e de armazenamento na nuvem**

| Capacidade de armazenamento | 8100 | 8600 |
|---|---|---|
| Capacidade de armazenamento local | &lt;10 TiB\*  | &lt;20 TiB\*  |
| Capacidade de armazenamento na nuvem | &gt;TiB de 200\* | &gt;500 TiB\* |

\*Tamanho de armazenamento parte do princípio de não eliminação de duplicados ou compressão.

**Capacidades do StorSimple para cópias de segurança primários e secundários**

| Cenário de cópia de segurança  | Capacidade de armazenamento local  | Capacidade de armazenamento na nuvem  |
|---|---|---|
| Cópia de segurança primária  | Cópias de segurança recentes armazenadas no armazenamento local para a recuperação rápida para satisfazer o objetivo de ponto de recuperação (RPO) | Histórico de cópia de segurança (RPO) se adequa a capacidade de nuvem |
| Cópia de segurança secundária | Secundária cópia dos dados de cópia de segurança pode ser armazenada na capacidade de nuvem  | N/D  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple como um destino de cópia de segurança primário

Neste cenário, os volumes do StorSimple são apresentados para a aplicação de cópia de segurança como o único repositório para cópias de segurança. A figura seguinte mostra uma arquitetura de solução em que todas as cópias de segurança utilize StorSimple volumes em camadas para cópias de segurança e restauros.

![StorSimple como um diagrama lógico de destino de cópia de segurança primário](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Passos de lógica de cópia de segurança de destino principal

1.  O servidor de cópia de segurança entra em contacto com o agente de cópia de segurança de destino e o agente de cópia de segurança transmite dados para o servidor de cópia de segurança.
2.  O servidor de cópia de segurança escreve dados para o StorSimple volumes em camadas.
3.  O servidor de cópia de segurança da base de dados do catálogo de atualizações e, em seguida, concluir a tarefa de cópia de segurança.
4.  Um script de instantâneo aciona nuvem snapshot manager do StorSimple (iniciar ou eliminar).
5.  O servidor de cópia de segurança elimina expiradas cópias de segurança com base na política de retenção.

### <a name="primary-target-restore-logical-steps"></a>Passos de lógica de restauro de destino principal

1.  O servidor de cópia de segurança começa restaurar os dados adequados do repositório de armazenamento.
2.  O agente de cópia de segurança recebe os dados do servidor de cópia de segurança.
3.  O servidor de cópia de segurança é concluída a tarefa de restauro.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple como um destino de cópia de segurança secundário

Neste cenário, volumes do StorSimple principalmente são utilizados para a retenção de longa duração ou arquivar.

A figura seguinte mostra uma arquitetura em que cópias de segurança iniciais e restaura o volume de destino um elevado desempenho. Estas cópias de segurança são copiadas e arquivadas para StorSimple camadas volume numa agenda definida.

É importante para o tamanho do volume de elevado desempenho, de modo a que pode processar os requisitos de capacidade e o desempenho da política de retenção.

![StorSimple como um diagrama lógico de destino de cópia de segurança secundário](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Passos de lógica de cópia de segurança de destino secundária

1.  O servidor de cópia de segurança entra em contacto com o agente de cópia de segurança de destino e o agente de cópia de segurança transmite dados para o servidor de cópia de segurança.
2.  O servidor de cópia de segurança escreve dados para armazenamento de elevado desempenho.
3.  O servidor de cópia de segurança da base de dados do catálogo de atualizações e, em seguida, concluir a tarefa de cópia de segurança.
4.  O servidor de cópia de segurança copia as cópias de segurança para StorSimple com base na política de retenção.
5.  Um script de instantâneo aciona nuvem snapshot manager do StorSimple (iniciar ou eliminar).
6.  O servidor de cópia de segurança elimina expiradas cópias de segurança com base na política de retenção.

### <a name="secondary-target-restore-logical-steps"></a>Passos de lógica de restauro de destino secundária

1.  O servidor de cópia de segurança começa restaurar os dados adequados do repositório de armazenamento.
2.  O agente de cópia de segurança recebe os dados do servidor de cópia de segurança.
3.  O servidor de cópia de segurança é concluída a tarefa de restauro.

## <a name="deploy-the-solution"></a>Implementar a solução

Implementar a solução requer três passos:

1. Prepare a infraestrutura de rede.
2. Implemente o dispositivo StorSimple como destino de cópia de segurança.
3. Implemente Veeam.

Cada passo é abordado em detalhe nas secções seguintes.

### <a name="set-up-the-network"></a>Configure a rede

Uma vez StorSimple é uma solução que está integrada com a nuvem do Azure, StorSimple requer uma ligação de trabalho para a nuvem do Azure e Active Directory. Esta ligação é utilizada para operações como instantâneos de nuvem, a gestão de dados e a transferência de metadados e à camada mais antiga, menos os dados acedidos para armazenamento na nuvem do Azure.

Para a solução apresente um desempenho ideal, recomendamos que siga estas práticas recomendadas de rede:

-   A hiperligação que se liga a StorSimple em camadas para o Azure tem de cumprir os requisitos de largura de banda. Conseguir isto ao aplicar o nível de Quality of Service (QoS) necessário para a sua infraestrutura de objetivo (RTO) SLAs de tempo de comutadores, para que corresponda ao seu RPO e a recuperação.
-   Latências de acesso de armazenamento de Blobs do Azure máximas devem ser cerca de 80 ms.

### <a name="deploy-storsimple"></a>Implementar StorSimple

Para orientações passo a passo de implementação StorSimple, consulte [implementar o dispositivo StorSimple no local](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Implementar Veeam

Para Veeam melhores práticas de instalação, consulte [Veeam cópia de segurança & replicação melhores práticas](https://bp.veeam.expert/), e ler o guia de utilizador em [Veeam Centro de ajuda (documentação técnica)](https://www.veeam.com/documentation-guides-datasheets.html).

## <a name="set-up-the-solution"></a>Configurar a solução

Nesta secção, iremos demonstrar alguns exemplos de configuração. Os exemplos e as recomendações seguintes ilustram a implementação mais básica e fundamental. Esta implementação não podem ser aplicadas diretamente aos seus requisitos de cópia de segurança específicos.

### <a name="set-up-storsimple"></a>Configurar StorSimple

| Tarefas de implementação do StorSimple  | Comentários adicionais |
|---|---|
| Implemente o dispositivo StorSimple no local. | Versões suportadas: atualizar versões de 3 e posteriores. |
| Ative o destino de cópia de segurança. | Utilize estes comandos para ativar ou desativar o modo de destino de cópia de segurança e ao obter o estado. Para obter mais informações, consulte [ligar remotamente ao dispositivo StorSimple](storsimple-remote-connect.md).</br> Para ativar o modo de cópia de segurança: `Set-HCSBackupApplianceMode -enable`. </br> Para desativar o modo de cópia de segurança: `Set-HCSBackupApplianceMode -disable`. </br> Para obter o estado atual das definições do modo de cópia de segurança: `Get-HCSBackupApplianceMode`. |
| Crie um contentor de volume comum para o volume que armazena os dados de cópia de segurança. Todos os dados num contentor de volume tem eliminação de duplicados. | Contentores de volume do StorSimple definem domínios de eliminação de duplicados.  |
| Crie volumes do StorSimple. | Crie volumes com tamanhos como próximo a utilização prevista dos quanto possível, porque o tamanho do volume afeta o tempo de duração de instantâneos de nuvem. Para obter informações sobre como para o tamanho de um volume, leia sobre [políticas de retenção](#retention-policies).</br> </br> Utilize StorSimple volumes em camadas e selecione o **utilizar este volume para menos dados de arquivo acedidos com frequência** caixa de verificação. </br> Não é suportada apenas localmente afixado volumes a utilizar. |
| Crie uma política de cópia de segurança StorSimple exclusiva para todos os volumes de destino de cópia de segurança. | Uma política de cópia de segurança do StorSimple define o grupo de consistência de volume. |
| Desactivar o agendamento de como os instantâneos de expirarem. | Instantâneos são acionados como uma operação de pós-processamento. |

### <a name="set-up-the-host-backup-server-storage"></a>Configurar o armazenamento de cópia de segurança do servidor de anfitrião

Configure o armazenamento de cópia de segurança do servidor de anfitrião, de acordo com estas diretrizes:  

- Não utilize volumes expandidos (criados pela gestão de discos do Windows). Volumes expandidos não são suportados.
- Formate os volumes utilizando o NTFS com o tamanho da unidade de alocação de 64 KB.
- Mapear os volumes do StorSimple diretamente para o servidor de Veeam.
    - Utilize iSCSI para servidores físicos.


## <a name="best-practices-for-storsimple-and-veeam"></a>Melhores práticas para StorSimple e Veeam

Configurar a sua solução de acordo com as diretrizes a seguir alguns secções.

### <a name="operating-system-best-practices"></a>Melhores práticas de sistema operativo

-   Desative a encriptação do Windows Server e a eliminação de duplicados para o sistema de ficheiros NTFS.
-   Desative uma desfragmentação do Windows Server nos volumes do StorSimple.
-   Desative indexação do Windows Server nos volumes do StorSimple.
-   Execute uma análise antivírus o anfitrião de origem (não contra os volumes do StorSimple).
-   Desativar a predefinição [manutenção do Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) no Gestor de tarefas. Fazê-lo de uma das seguintes formas:
    - Desative o configurator de manutenção no programador de tarefas do Windows.
    - Transferir [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) do Windows Sysinternals. Depois de transferir PsExec, execute o Windows PowerShell como um administrador e escreva:
      ```powershell
      psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
      ```

### <a name="storsimple-best-practices"></a>Melhores práticas do StorSimple

-   Certifique-se de que o dispositivo StorSimple é atualizado para [atualização 3 ou posterior](storsimple-install-update-3.md).
-   Isolar tráfego iSCSI e na nuvem. Utilize ligações de iSCSI dedicado para o tráfego entre StorSimple e o servidor de cópia de segurança.
-   Lembre-se de que o dispositivo StorSimple é um destino de cópia de segurança dedicado. Cargas de trabalho mistas não são suportadas porque que afetam o RTO e RPO.

### <a name="veeam-best-practices"></a>Veeam melhores práticas

-   A base de dados Veeam deve ser local para o servidor e reside num StorSimple volume.
-   Recuperação de desastres, fazer cópias de segurança da base de dados Veeam num StorSimple volume.
-   Suportamos Veeam cópias de segurança de completas e incrementais para esta solução. Recomendamos que utilize cópias de segurança diferenciais e sintéticas.
-   Ficheiros de dados de cópia de segurança devem conter apenas os dados para uma tarefa específica. Por exemplo, sem suporte de dados acrescenta entre as diferentes tarefas são permitidas.
-   Desative a verificação de tarefa. Se necessário, a verificação deve ser agendada após a tarefa de cópia de segurança mais recente. É importante compreender que esta tarefa afeta a janela de cópia de segurança.
-   Ative a alocação de pré-lançamento do suporte de dados.
-   Não se esqueça de processamento paralelo está ativado.
-   Desative a compressão.
-   Desative a eliminação de duplicados na tarefa de cópia de segurança.
-   Como a otimização **LAN destino**.
-   Ativar **criar cópia de segurança ativa completa** (a cada 2 semanas).
-   No repositório de cópia de segurança, configurar **utilizar ficheiros de cópia de segurança por VM**.
-   Definir **utilizar vários fluxos de carregamento por tarefa** para **8** (é permitido um máximo de 16). Ajuste este número de cópias de segurança ou para baixo com base na utilização da CPU no dispositivo StorSimple.

## <a name="retention-policies"></a>Políticas de retenção

Um dos tipos de política de retenção de cópias de segurança mais comuns é uma política de avô, Pai e filho (GFS). Uma política GFS, cópia de segurança incremental é executada diariamente em cópias de segurança completas terminadas semanais e mensais. Este resultados da política no StorSimple seis volumes em camadas: um volume contém as semanais, mensais e anuais cópias de segurança completas; os volumes de cinco armazenam cópias de segurança incrementais diárias.

No exemplo seguinte, utilizamos uma rotação GFS. O exemplo assume o seguinte:

-   Dados comprimidos ou eliminação de não são utilizados.
-   Cópias de segurança completas são 1 TiB.
-   Cópias de segurança incrementais diárias são 500 GiB.
-   Cópias de segurança semanais quatro são mantidas durante um mês.
-   Cópias de segurança mensais doze são mantidas durante um ano.
-   Uma cópia de segurança anual é mantida para 10 anos.

Com base em suposições de anteriores, cria um 26-TiB StorSimple camadas volume para as cópias de segurança completas mensais e anuais. Criar um TiB 5 StorSimple volume em camadas para cada um das cópias de segurança incrementais diárias.

| Retenção do tipo de cópia de segurança | Tamanho (TiB) | Multiplicador GFS\* | Capacidade total (TiB)  |
|---|---|---|---|
| Completa semanal | 1 | 4  | 4 |
| Diária incremental | 0.5 | 20 (ciclos igual número de semanas por mês) | 12 (2 para quota adicional) |
| Completa mensal | 1 | 12 | 12 |
| Completa anual | 1  | 10 | 10 |
| Requisito de GFS |   | 38 |   |
| Quota adicional  | 4  |   | 42 requisito GFS total  |
\*O multiplicador GFS é o número de cópias que tem para proteger e manter para satisfazer os seus requisitos de política de cópia de segurança.

## <a name="set-up-veeam-storage"></a>Configurar o armazenamento de Veeam

### <a name="to-set-up-veeam-storage"></a>Para configurar o armazenamento de Veeam

1.  Na consola de cópia de segurança Veeam e replicação, no **repositório ferramentas**, aceda a **infraestrutura de cópia de segurança**. Clique com botão direito **repositórios de cópia de segurança**e, em seguida, selecione **adicionar repositório de cópia de segurança**.

    ![Consola de gestão de Veeam, page repositório de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  No **novo repositório de cópia de segurança** caixa de diálogo, introduza um nome e descrição para o repositório. Selecione **seguinte**.

    ![Página de consola, o nome e descrição da gestão Veeam](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  Para o tipo, selecione **do Microsoft Windows server**. Selecione o servidor de Veeam. Selecione **seguinte**.

    ![Consola de gestão de Veeam, selecione de tipo do repositório de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  Para especificar **localização**, procure e selecione o volume. Selecione o **limitar máximas tarefas simultâneas:** caixa de verificação e defina o valor **4**. Isto garante que apenas quatro discos virtuais estão a ser processados em simultâneo enquanto cada máquina virtual (VM) está a ser processada. Selecione o **avançadas** botão.

    ![Consola de gestão de Veeam, selecione de volume](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  No **as definições de compatibilidade do armazenamento** caixa de diálogo, selecione o **utilizar ficheiros de cópia de segurança por VM** caixa de verificação.

    ![Consola de gestão de Veeam, as definições de compatibilidade do armazenamento](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  No **novo repositório de cópia de segurança** caixa de diálogo, selecione o **ativar vPower NFS serviço no servidor de montagem (recomendado)** caixa de verificação. Selecione **seguinte**.

    ![Consola de gestão de Veeam, page repositório de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Reveja as definições e, em seguida, selecione **seguinte**.

    ![Consola de gestão de Veeam, page repositório de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    Um repositório é adicionado ao servidor Veeam.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Configurar StorSimple como um destino de cópia de segurança primário

> [!IMPORTANT]
> Restauro de dados a partir de uma cópia de segurança que tenha sido camada para a nuvem ocorre velocidades de nuvem.

A figura seguinte mostra o mapeamento de um volume normal para uma tarefa de cópia de segurança. Neste caso, todas cópias de segurança semanais mapeiam para o disco completo Sábado e as cópias de segurança incrementais mapeiam para discos incrementais segunda-sexta-feira. Todas as cópias de segurança e restauros são do StorSimple camadas volume.

![Diagrama lógico de configuração de destino de cópia de segurança primário](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Exemplo de agendar StorSimple como um destino de cópia de segurança primário GFS

Eis um exemplo de uma agenda de rotação GFS para quatro semanas, mensais e anuais:

| Tipo de frequência/cópia de segurança | Completo | Incremental (dias 1-5)  |   
|---|---|---|
| Semanal (1-4 de semanas) | Sábado | Segunda-sexta |
| Custo  | Sábado  |   |
| Anual | Sábado  |   |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>Atribuir volumes do StorSimple para uma tarefa de cópia de segurança Veeam

Para o cenário de destino de cópia de segurança primário, crie uma tarefa diária com o volume do Veeam StorSimple primário. Para um cenário de destino de cópia de segurança secundária, crie uma tarefa diária através de ligação direta armazenamento (DAS), armazenamento de anexado de rede (NAS) ou apenas um armazenamento Bunch de discos (JBOD).

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>Para atribuir volumes do StorSimple para uma tarefa de cópia de segurança Veeam

1.  Na consola de cópia de segurança Veeam e replicação, selecione **cópia de segurança e replicação**. Clique com botão direito **cópia de segurança**e, em seguida, selecione **VMware** ou **Hyper-V**, consoante o seu ambiente.

    ![Consola de gestão de Veeam, nova tarefa de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  No **nova tarefa de cópia de segurança** caixa de diálogo, introduza um nome e descrição para a tarefa de cópia de segurança diária.

    ![Consola de gestão de Veeam, nova página de tarefa de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Selecione uma máquina virtual para efetuar uma cópia até.

    ![Consola de gestão de Veeam, nova página de tarefa de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  Selecione os valores que pretende para **proxy de cópia de segurança** e **repositório de cópia de segurança**. Selecione um valor para **restaurar pontos a manter no disco** , de acordo com as definições de RPO e RTO para o seu ambiente no armazenamento ligado localmente. Selecione **avançadas**.

    ![Consola de gestão de Veeam, nova página de tarefa de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. No **definições avançadas** caixa de diálogo a **cópia de segurança** separador, selecione **Incremental**. Certifique-se ser que o **criar cópias de segurança completas sintéticas periodicamente** caixa de verificação está desmarcada. Selecione o **criar cópias de segurança completas Active Directory periodicamente** caixa de verificação. Em **Active Directory cópia de segurança completa**, selecione o **semanalmente nos dias selecionados** caixa de verificação para Sábado.

    ![Consola de gestão Veeam, a nova tarefa de cópia de segurança avançadas página de definições](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. No **armazenamento** separador, certifique-se de que o **ativar a eliminação de dados duplicados inline** caixa de verificação está desmarcada. Selecione o **blocos de ficheiros de comutação de exclusão** caixa de verificação e selecione o **excluir eliminar blocos de ficheiros** caixa de verificação. Definir **nível de compressão** para **nenhum**. Com balanceamento de desempenho e a eliminação de duplicados, defina **otimização de armazenamento** para **destino LAN**. Selecione **OK**.

    ![Consola de gestão Veeam, a nova tarefa de cópia de segurança avançadas página de definições](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Para informações sobre definições de eliminação de duplicados e compressão Veeam, consulte [compressão de dados e a eliminação de duplicados](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html).

7.  No **Editar tarefa de cópia de segurança** caixa de diálogo, pode selecionar o **ativar aplicação com suporte para processamento** caixa de verificação (opcional).

    ![Consola de gestão de Veeam, nova página de processamento de convidado de tarefa de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Defina a agenda para ser executada após diárias, numa altura, que pode especificar.

    ![Consola de gestão de Veeam, nova página agenda de tarefa de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Configurar StorSimple como um destino de cópia de segurança secundário

> [!NOTE]
> Restauros de dados de uma cópia de segurança que tenha sido camadas para a nuvem ocorrerem velocidades de nuvem.

Neste modelo, tem de ter um suporte de dados de armazenamento (que não seja StorSimple) para servir como uma cache temporária. Por exemplo, pode utilizar uma matriz redundante de volume de discos independentes (RAID) para acomodar espaço, a entrada/saída (e/s) e a largura de banda. Recomendamos que utilize o RAID 5, 50 e 10.

A figura seguinte mostra típico volumes de local (para o servidor) de retenção curto prazo e os volumes de arquivo de retenção longo prazo. Neste cenário, executam todas as cópias de segurança no volume RAID local (para o servidor). Estas cópias de segurança são periodicamente duplicadas e são arquivadas para um volume de arquivo. É importante para o tamanho do volume RAID local (para o servidor) para que pode processar os requisitos de capacidade e o desempenho de retenção curta duração.

![StorSimple como diagrama lógico de destino de cópia de segurança secundário](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple como um exemplo GFS secundário destino de cópia de segurança

A tabela seguinte mostra como configurar as cópias de segurança para executar no local e discos do StorSimple. Inclui os requisitos de capacidade individuais e total.

| Tipo de cópia de segurança e retenção | Armazenamento configurados | Tamanho (TiB) | Multiplicador GFS | Total de capacidade\* (TiB) |
|---|---|---|---|---|
| Semana 1 (completas e incrementais) |Disco local (curto prazo)| 1 | 1 | 1 |
| StorSimple semanas 2 a 4 |Disco do StorSimple (longo prazo) | 1 | 4 | 4 |
| Completa mensal |Disco do StorSimple (longo prazo) | 1 | 12 | 12 |
| Completa anual |Disco do StorSimple (longo prazo) | 1 | 1 | 1 |
|Requisito de tamanho de volumes GFS |  |  |  | 18*|
\*Capacidade total inclui 17 TiB do StorSimple discos e 1 TiB de local RAID volume.


### <a name="gfs-example-schedule"></a>Agenda de exemplo GFS

Rotação de GFS agenda semanal, mensal e anual

| Semana | Completo | Dia incremental 1 | Dia incremental 2 | Dia incremental 3 | Dia incremental 4 | Dia incremental 5 |
|---|---|---|---|---|---|---|
| Semana 1 | Local RAID volume  | Local RAID volume | Local RAID volume | Local RAID volume | Local RAID volume | Local RAID volume |
| Semana 2 | StorSimple semanas 2 a 4 |   |   |   |   |   |
| Semana 3 | StorSimple semanas 2 a 4 |   |   |   |   |   |
| Semana 4 | StorSimple semanas 2 a 4 |   |   |   |   |   |
| Custo | Mensalmente StorSimple |   |   |   |   |   |
| Anual | Anualmente StorSimple  |   |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>Atribuir volumes do StorSimple para uma tarefa de cópia Veeam

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>Para atribuir volumes do StorSimple para uma tarefa de cópia Veeam

1.  Na consola de cópia de segurança Veeam e replicação, selecione **cópia de segurança e replicação**. Clique com botão direito **cópia de segurança**e, em seguida, selecione **VMware** ou **Hyper-V**, consoante o seu ambiente.

    ![Consola de gestão de Veeam, nova página de tarefa de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  No **nova tarefa de cópia da cópia de segurança** caixa de diálogo, introduza um nome e descrição para a tarefa.

    ![Consola de gestão de Veeam, nova página de tarefa de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  Selecione as VMs que pretende processar. Selecione as cópias de segurança e, em seguida, selecione a cópia de segurança diária que criou anteriormente.

    ![Consola de gestão de Veeam, nova página de tarefa de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  Exclua objetos da tarefa de cópia de segurança, se necessário.

5.  Selecione o seu repositório de cópia de segurança e defina um valor para **restaurar pontos para manter**. Certifique-se de selecionar o **tenha os seguintes pontos de restauro para fins de registo** caixa de verificação. Definir a frequência de cópia de segurança e, em seguida, selecione **avançadas**.

    ![Consola de gestão de Veeam, nova página de tarefa de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  Especifique as seguintes definições avançadas:

    * No **manutenção** separador, desative a proteção de nível de danos de armazenamento.

    ![Consola de gestão Veeam, a nova tarefa de cópia de segurança avançadas página de definições](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * No **armazenamento** separador, certifique-se de que a eliminação de duplicados e compressão estão desativadas.

    ![Consola de gestão Veeam, a nova tarefa de cópia de segurança avançadas página de definições](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Especifique que a transferência de dados é direta.

8.  Definir a agenda da janela de cópia de segurança consoante as suas necessidades e, em seguida, conclua o assistente.

Para obter mais informações, consulte [criar tarefas de cópia de segurança](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html).

## <a name="storsimple-cloud-snapshots"></a>Instantâneos de nuvem do StorSimple

Instantâneos de nuvem do StorSimple protegem os dados que residem no seu dispositivo StorSimple. Criar um instantâneo na nuvem é equivalente ao envio bandas de cópia de segurança locais para um local externo. Se utilizar o armazenamento com redundância geográfica do Azure, criar um instantâneo na nuvem é equivalente ao envio bandas de cópia de segurança para múltiplos sites. Se tiver de restaurar um dispositivo depois de um desastre, poderá colocar o dispositivo StorSimple outro online e efetue uma ativação pós-falha. Após a ativação pós-falha, deverá conseguir aceder os dados (velocidades de nuvem) a partir do instantâneo na nuvem mais recente.

A seguinte secção descreve como criar um script curto para iniciar e eliminar os instantâneos de nuvem do StorSimple durante o pós-processamento de cópia de segurança.

> [!NOTE]
> Instantâneos manualmente ou programaticamente criados não siga a política de expiração de instantâneo do StorSimple. Estes instantâneos tem de ser manualmente ou programaticamente eliminados.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Iniciar e eliminar os instantâneos de nuvem utilizando um script

> [!NOTE]
> Avalie cuidadosamente o repercussions de retenção de dados e de conformidade antes de eliminar um instantâneo do StorSimple. Para obter mais informações sobre como executar um script pós-cópia de segurança, consulte a documentação de Veeam.


### <a name="backup-lifecycle"></a>Ciclo de vida de cópia de segurança

![Diagrama de ciclo de vida de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

### <a name="requirements"></a>Requisitos

-   O servidor que executa o script tem de ter acesso a recursos na nuvem do Azure.
-   A conta de utilizador tem de ter as permissões necessárias.
-   Uma política de cópia de segurança do StorSimple com os volumes do StorSimple associados tem configurar, mas não ativada.
-   Terá do nome de recurso do StorSimple, a chave de registo, ID de política de nome de cópia de segurança do dispositivo.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Para iniciar ou eliminar um instantâneo na nuvem

1. [Instalar o Azure PowerShell](/powershell/azure/overview).
2. Transferir e configurar [gerir CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) script do PowerShell.
3. No servidor que executa o script, execute o PowerShell como administrador. Certifique-se de que tem de executar o script com `-WhatIf $true` ver o que altera o script irá efetuar. Depois de concluída a validação, transmitir `-WhatIf $false`. Execute o comando abaixo:
```powershell
.\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
```
4. Para adicionar o script para a tarefa de cópia de segurança, edite a tarefa de Veeam opções avançadas.

    ![Separador de scripts de cópia de segurança definições avançadas de Veeam](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

Recomendamos que execute a sua política de cópia de segurança de instantâneos de nuvem StorSimple como um script de pós-processamento no fim da tarefa de cópia de segurança diária. Para obter mais informações sobre como fazer cópias de segurança e restaurar o ambiente de aplicação de cópia de segurança para ajudar a satisfazer as suas RPO e RTO, consulte com o arquiteto de sistemas de cópia de segurança.

## <a name="storsimple-as-a-restore-source"></a>StorSimple como uma origem de restauro

Restaura a partir de um trabalho do dispositivo StorSimple como restaura a partir de qualquer dispositivo de armazenamento de blocos. Ocorre restauros dos dados que estão em camadas para a nuvem velocidades de nuvem. Para os dados locais, restauros ocorrerem na velocidade do disco local do dispositivo.

Com Veeam, obter recuperação rápida granular, ao nível dos ficheiros através do StorSimple através de vistas explorer incorporada na consola Veeam. Utilize o exploradores Veeam para recuperar os itens individuais, como mensagens de e-mail, os objetos do Active Directory e itens do SharePoint a partir de cópias de segurança. A recuperação pode ser realizada sem interrupção de VM no local. Também pode efetuar a recuperação do ponto no tempo para bases de dados SQL Database do Azure e Oracle. Veeam e StorSimple tornar o processo de recuperação ao nível do item a partir do Azure rápida e fácil. Para obter informações sobre como efetuar um restauro, consulte a documentação de Veeam:

- Para [do Exchange Server](https://www.veeam.com/microsoft-exchange-recovery.html)
- Para [do Active Directory](https://www.veeam.com/microsoft-active-directory-explorer.html)
- Para [do SQL Server](https://www.veeam.com/microsoft-sql-server-explorer.html)
- Para [SharePoint](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)
- Para [Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple ativação pós-falha e recuperação após desastre

> [!NOTE]
> Para cenários de destino de cópia de segurança, o dispositivo de nuvem do StorSimple não é suportado como um destino de restauro.

Um desastre pode ser causado por vários fatores. A tabela seguinte apresenta uma lista de cenários de recuperação após desastre comuns.

| Cenário | Impacto | Como recuperar | Notas |
|---|---|---|---|
| Falha de dispositivo do StorSimple | Operações de cópia de segurança e restauro sejam interrompidas. | Substitua o dispositivo com falhas e efetuar [StorSimple ativação pós-falha e recuperação após desastre](storsimple-device-failover-disaster-recovery.md). | Se precisar de efetuar um restauro após a recuperação de dispositivo, conjuntos de trabalho completo de dados são obtidos a partir da nuvem para o novo dispositivo. Todas as operações são velocidades de nuvem. O índice e o catálogo de reanálise da processo poderão fazer com que todos os conjuntos de cópias de segurança ser analisada e retirado da camada de nuvem para a camada de dispositivo local, o que pode ser um processo moroso. |
| Falha do servidor Veeam | Operações de cópia de segurança e restauro sejam interrompidas. | Reconstruir o servidor de cópia de segurança e efetuar o restauro de base de dados, conforme detalhado em [Veeam Centro de ajuda (documentação técnica)](https://www.veeam.com/documentation-guides-datasheets.html).  | Tem de reconstruir ou restaurar o servidor de Veeam no site de recuperação de desastre. Restaure a base de dados para o ponto mais recente. Se a base de dados restaurada do Veeam não está sincronizada com as suas tarefas de cópia de segurança mais recentes, a indexação e cataloging são necessário. Este índice e o catálogo de reanálise da processo poderão fazer com que todos os conjuntos de cópias de segurança ser analisada e retirado da camada de nuvem para a camada do dispositivo local. Isto torna mais tempo intensivas. |
| Falha do site que resulte em perda do servidor de cópia de segurança e do StorSimple | Operações de cópia de segurança e restauro sejam interrompidas. | Restaurar StorSimple primeiro e, em seguida, restaure Veeam. | Restaurar StorSimple primeiro e, em seguida, restaure Veeam. Se precisar de efetuar um restauro após a recuperação de dispositivo, os conjuntos de trabalho completo de dados são obtidos a partir da nuvem para o novo dispositivo. Todas as operações são velocidades de nuvem. |


## <a name="references"></a>Referências

Os seguintes documentos foram referenciados para este artigo:

- [Configuração de e/s multipath do StorSimple](storsimple-configure-mpio-windows-server.md)
- [Cenários de armazenamento: aprovisionamento dinâmico](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Utilizar o GPT unidades](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Configurar cópias sombra para pastas partilhadas](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como [restauro a partir de um conjunto de cópia de segurança](storsimple-restore-from-backup-set-u2.md).
- Saiba mais sobre como efetuar [dispositivo ativação pós-falha e recuperação após desastre](storsimple-device-failover-disaster-recovery.md).
