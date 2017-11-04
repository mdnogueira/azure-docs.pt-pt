---
title: "Série 8000 do StorSimple como destino de cópia de segurança com a cópia de segurança Exec | Microsoft Docs"
description: "Descreve a configuração de destino de cópia de segurança do StorSimple com Veritas Exec de cópia de segurança."
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
ms.date: 12/05/2016
ms.author: hkanna
ms.openlocfilehash: a28b46e10bbdd5331cc665fad3f80523b3aa8a58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-as-a-backup-target-with-backup-exec"></a>StorSimple como destino de cópia de segurança com Exec cópia de segurança

## <a name="overview"></a>Descrição geral

Azure StorSimple é uma solução de armazenamento de nuvem híbrida da Microsoft. StorSimple endereços complexidades de crescimento de dados exponencial ao utilizar uma conta de armazenamento do Azure como uma extensão da solução no local e automaticamente criação de camadas de dados no armazenamento no local e no armazenamento na nuvem.

Neste artigo, discutimos a integração do StorSimple Veritas Exec de cópia de segurança e as melhores práticas para integrar ambas as soluções. Podemos também o tornam as recomendações sobre como configurar cópia de segurança Exec melhor integrar StorSimple. Iremos diferir Veritas melhores práticas, arquitetos de cópia de segurança e administradores para a melhor forma de configurar a cópia de segurança de Exec para satisfazer os requisitos de cópia de segurança individuais e contratos de nível de serviço (SLAs).

Embora é ilustrar os passos de configuração e conceitos chave, este artigo não é de um guia passo a passo de instalação ou a configuração. Iremos partem do princípio de que a infraestrutura e componentes básicos estão por ordem de trabalho e está preparado para suportar os conceitos dita.

### <a name="who-should-read-this"></a>Quem deve ler este artigo?

As informações neste artigo será mais úteis para administradores de cópia de segurança, os administradores de armazenamento e arquitetos de armazenamento que tem conhecimento do armazenamento, Windows Server 2012 R2, Ethernet, cloud services e Exec cópia de segurança.

## <a name="supported-versions"></a>Versões suportadas

-   [Exec 16 e versões posteriores de cópia de segurança](http://backupexec.com/compatibility)
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
![diagrama camado do StorSimple](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

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

| Capacidade de armazenamento       | 8100          | 8600            |
|------------------------|---------------|-----------------|
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

![StorSimple como um diagrama lógico de destino de cópia de segurança primário](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

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

![StorSimple como um diagrama lógico de destino de cópia de segurança secundário](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

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
3. Implemente a cópia de segurança Exec.

Cada passo é abordado em detalhe nas secções seguintes.

### <a name="set-up-the-network"></a>Configure a rede

Uma vez StorSimple é uma solução que está integrada com a nuvem do Azure, StorSimple requer uma ligação de trabalho para a nuvem do Azure e Active Directory. Esta ligação é utilizada para operações como instantâneos de nuvem, gestão e a transferência de metadados e à camada mais antiga, menos os dados acedidos para armazenamento na nuvem do Azure.

Para a solução apresente um desempenho ideal, recomendamos que siga estas práticas recomendadas de rede:

-   A hiperligação que se liga a StorSimple em camadas para o Azure tem de cumprir os requisitos de largura de banda. Para atingir esse objetivo, aplicar o nível de Quality of Service (QoS) necessário para a sua infraestrutura de objetivo (RTO) SLAs de tempo de comutadores, para que corresponda ao seu RPO e a recuperação.
-   Latências de acesso de armazenamento de Blobs do Azure máximas devem ser cerca de 80 ms.

### <a name="deploy-storsimple"></a>Implementar StorSimple

Para uma orientação de implementação do StorSimple passo a passo, consulte [implementar o dispositivo StorSimple no local](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-backup-exec"></a>Implementar Exec cópia de segurança

Para cópia de segurança Exec melhores práticas de instalação, consulte [melhores práticas para a instalação de cópia de segurança Exec](https://www.veritas.com/support/en_US/article.000068207).

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

- Não utilize volumes expandidos (criados pela gestão de discos do Windows). Discos expandidos não são suportados.
- Formate os volumes utilizando o NTFS com tamanho de alocação de 64 KB.
- Mapear os volumes do StorSimple diretamente para o servidor de cópia de segurança Exec.
    - Utilize iSCSI para servidores físicos.
    - Utilize discos pass-through para servidores virtuais.

## <a name="best-practices-for-storsimple-and-backup-exec"></a>Melhores práticas para StorSimple e Exec de cópia de segurança

Configure a sua solução de acordo com as diretrizes nas secções seguintes.

### <a name="operating-system-best-practices"></a>Melhores práticas de sistema operativo

-   Desative a encriptação do Windows Server e a eliminação de duplicados para o sistema de ficheiros NTFS.
-   Desative uma desfragmentação do Windows Server nos volumes do StorSimple.
-   Desative indexação do Windows Server nos volumes do StorSimple.
-   Execute uma análise antivírus o anfitrião de origem (não contra os volumes do StorSimple).
-   Desativar a predefinição [manutenção do Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) no Gestor de tarefas. Fazê-lo de uma das seguintes formas:
   - Desative o configurator de manutenção no programador de tarefas do Windows.
   - Transferir [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) do Windows Sysinternals. Depois de transferir PsExec, execute o Azure PowerShell como um administrador e escreva:
      ```powershell
      psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
      ```

### <a name="storsimple-best-practices"></a>Melhores práticas do StorSimple

  -   Certifique-se de que o dispositivo StorSimple é atualizado para [atualização 3 ou posterior](storsimple-install-update-3.md).
  -   Isolar tráfego iSCSI e na nuvem. Utilize ligações de iSCSI dedicado para o tráfego entre StorSimple e o servidor de cópia de segurança.
  -   Lembre-se de que o dispositivo StorSimple é um destino de cópia de segurança dedicado. Cargas de trabalho mistas não são suportadas porque que afetam o RTO e RPO.

### <a name="backup-exec-best-practices"></a>Cópia de segurança Exec as melhores práticas

-   Exec cópia de segurança tem de ser instalado numa unidade local do servidor e não num StorSimple volume.
-   Defina o armazenamento de cópia de segurança Exec **operações de escrita em simultâneo** ao máximo permitido.
    -   Defina o armazenamento de cópia de segurança Exec **tamanho de bloco e da memória intermédia** para 512 KB.
    -   Ativar o armazenamento de cópia de segurança Exec **colocado na memória intermédia de leitura e escrita**.
-   StorSimple suporta cópias de segurança de cópia de segurança Exec, completas e incrementais. Recomendamos que não utilize cópias de segurança diferenciais e sintéticas.
-   Ficheiros de dados de cópia de segurança devem conter dados apenas para uma tarefa específica. Por exemplo, sem suporte de dados acrescenta entre as diferentes tarefas são permitidas.
-   Desative a verificação de tarefa. Se necessário, a verificação deve ser agendada após a tarefa de cópia de segurança mais recente. É importante compreender que esta tarefa afeta a janela de cópia de segurança.
-   Selecione **armazenamento** > **seu disco** > **detalhes** > **propriedades**. Desativar **previamente alocar espaço em disco**.

Para as definições de cópia de segurança Exec mais recentes e melhores práticas para implementar estes requisitos, consulte [o Web site Veritas](https://www.veritas.com).

## <a name="retention-policies"></a>Políticas de retenção

Um dos tipos de política de retenção de cópias de segurança mais comuns é uma política de avô, Pai e filho (GFS). Uma política GFS, cópia de segurança incremental é executada diariamente em cópias de segurança completas terminadas semanais e mensais. Este resultados da política no StorSimple seis volumes em camadas. Um volume contém as cópias de segurança completas semanais, mensais e anuais. Os volumes de cinco armazenam cópias de segurança incrementais diárias.

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

## <a name="set-up-backup-exec-storage"></a>Configurar o armazenamento de cópia de segurança Exec

### <a name="to-set-up-backup-exec-storage"></a>Para configurar o armazenamento de cópia de segurança Exec

1.  Na consola de gestão Exec de cópia de segurança, selecione **armazenamento** > **configurar o armazenamento** > **armazenamento baseado em disco** > **seguinte**.

    ![Consola de gestão de Exec de cópia de segurança, configurar a página de armazenamento](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

2.  Selecione **armazenamento em disco**e, em seguida, selecione **seguinte**.

    ![Consola de gestão de Exec, página selecionar armazenamento de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

3.  Introduza um nome representativo, por exemplo, **Sábado completa**e uma descrição. Selecione **seguinte**.

    ![Cópia de segurança Exec consola, nome e descrição da página de gestão](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

4.  Selecione o disco para criar o dispositivo de armazenamento de disco e, em seguida, selecione onde pretende **seguinte**.

    ![Consola de gestão de Exec, página de seleção de disco de armazenamento de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

5.  Incrementar o número de operações de escrita para **16**e, em seguida, selecione **seguinte**.

    ![Página de definições de operações de escrita de cópia de segurança Exec consola de gestão, em simultâneo](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

6.  Reveja as definições e, em seguida, selecione **concluir**.

    ![Consola de gestão de Exec, página de resumo de configuração do armazenamento de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

7.  No final de cada atribuição de volume, alterar as definições do dispositivo de armazenamento para corresponder a esses recomendada em [melhores práticas para StorSimple e cópia de segurança Exec](#best-practices-for-storsimple-and-backup-exec).

    ![Consola de gestão de Exec, página de definições do dispositivo de armazenamento de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

8.  Repita os passos 1 a 7 até tiver terminado de atribuir os volumes do StorSimple a cópia de segurança Exec.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Configurar StorSimple como um destino de cópia de segurança primário

> [!NOTE]
> Restauro de dados a partir de uma cópia de segurança que tenha sido camada para a nuvem ocorre velocidades de nuvem.

A figura seguinte mostra o mapeamento de um volume normal para uma tarefa de cópia de segurança. Neste caso, todas cópias de segurança semanais mapeiam para o disco completo Sábado e as cópias de segurança incrementais mapeiam para discos incrementais segunda-sexta-feira. Todas as cópias de segurança e restauros são do StorSimple camadas volume.

![Diagrama lógico de configuração de destino de cópia de segurança primário](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Exemplo de agendar StorSimple como um destino de cópia de segurança primário GFS

Eis um exemplo de uma agenda de rotação GFS para quatro semanas, mensais e anuais:

| Tipo de frequência/cópia de segurança | Completo | Incremental (dias 1-5)  |   
|---|---|---|
| Semanal (1-4 de semanas) | Sábado | Segunda-sexta |
| Custo  | Sábado  |   |
| Anual | Sábado  |   |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Atribuir volumes do StorSimple para uma tarefa de cópia de segurança da cópia de segurança Exec

A seguinte sequência parte do princípio de que Exec de cópia de segurança e o anfitrião de destino estão configurados de acordo com as diretrizes de agente de cópia de segurança Exec.

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Para atribuir volumes do StorSimple para uma tarefa de cópia de segurança da cópia de segurança Exec

1.  Na consola de gestão Exec de cópia de segurança, selecione **anfitrião** > **cópia de segurança** > **cópia de segurança em disco**.

    ![Consola de gestão de Exec cópia de segurança, selecione o anfitrião, cópia de segurança e cópia de segurança em disco](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

2.  No **propriedades de definição de cópia de segurança** caixa de diálogo em **cópia de segurança**, selecione **editar**.

    ![Consola de gestão de Exec de cópia de segurança, caixa de diálogo de propriedades de definição de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

3.  Configure as cópias de segurança completas e incrementais, para que possam satisfazer os seus requisitos de RPO e RTO e estar em conformidade com as melhores práticas de Veritas.

4.  No **opções de cópia de segurança** caixa de diálogo, selecione **armazenamento**.

    ![Consola de gestão de Exec de cópia de segurança, caixa de diálogo de armazenamento de opções de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

5.  Atribua volumes do StorSimple correspondentes a sua agenda de cópia de segurança.

    > [!NOTE]
    > **Compressão** e **tipo de encriptação** estão definidas como **nenhum**.

6.  Em **verifique**, selecione o **não verificar os dados para esta tarefa** caixa de verificação. Utilizar esta opção pode afetar a criação de camadas do StorSimple.

    > [!NOTE]
    > Desfragmentação, indexação e verificação de segundo plano afetam negativamente a criação de camadas de StorSimple.

    ![Consola de gestão de Exec de cópia de segurança, as opções de cópia de segurança Verifique as definições](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

7.  Quando configurou o resto das suas opções de cópia de segurança para satisfazer os seus requisitos, selecione **OK** para concluir.

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Configurar StorSimple como um destino de cópia de segurança secundário

> [!NOTE]
>Restauros de dados de uma cópia de segurança que tenha sido camadas para a nuvem ocorrerem velocidades de nuvem.

Neste modelo, tem de ter um suporte de dados de armazenamento (que não seja StorSimple) para servir como uma cache temporária. Por exemplo, pode utilizar uma matriz redundante de volume de discos independentes (RAID) para acomodar espaço, a entrada/saída (e/s) e a largura de banda. Recomendamos que utilize o RAID 5, 50 e 10.

A figura seguinte mostra a retenção de curto prazo típica volumes locais (para o servidor) e retenção de longo prazo arquiva volumes. Neste cenário, executam todas as cópias de segurança no volume RAID local (para o servidor). Estas cópias de segurança são periodicamente duplicadas e são arquivadas para um volume de arquivos. É importante para o tamanho do volume RAID local (para o servidor) para que pode processar os requisitos de capacidade e o desempenho de retenção curta duração.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple como um exemplo GFS secundário destino de cópia de segurança

![StorSimple como diagrama lógico de destino de cópia de segurança secundário](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

A tabela seguinte mostra como configurar as cópias de segurança para executar no local e discos do StorSimple. Inclui os requisitos de capacidade individuais e total.

### <a name="backup-configuration-and-capacity-requirements"></a>Configuração de cópia de segurança e os requisitos de capacidade

| Tipo de cópia de segurança e retenção | Armazenamento configurados | Tamanho (TiB) | Multiplicador GFS | Total de capacidade\* (TiB) |
|---|---|---|---|---|
| Semana 1 (completas e incrementais) |Disco local (curto prazo)| 1 | 1 | 1 |
| StorSimple semanas 2 a 4 |Disco do StorSimple (longo prazo) | 1 | 4 | 4 |
| Completa mensal |Disco do StorSimple (longo prazo) | 1 | 12 | 12 |
| Completa anual |Disco do StorSimple (longo prazo) | 1 | 1 | 1 |
|Requisito de tamanho de volumes GFS |  |  |  | 18*|
\*Capacidade total inclui 17 TiB do StorSimple discos e 1 TiB de local RAID volume.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>Agenda de exemplo GFS: rotação GFS agenda semanal, mensal e anual

| Semana | Completo | Dia incremental 1 | Dia incremental 2 | Dia incremental 3 | Dia incremental 4 | Dia incremental 5 |
|---|---|---|---|---|---|---|
| Semana 1 | Local RAID volume  | Local RAID volume | Local RAID volume | Local RAID volume | Local RAID volume | Local RAID volume |
| Semana 2 | StorSimple semanas 2 a 4 |   |   |   |   |   |
| Semana 3 | StorSimple semanas 2 a 4 |   |   |   |   |   |
| Semana 4 | StorSimple semanas 2 a 4 |   |   |   |   |   |
| Custo | Mensalmente StorSimple |   |   |   |   |   |
| Anual | Anualmente StorSimple  |   |   |   |   |   |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-archive-and-deduplication-job"></a>Atribuir volumes do StorSimple para um arquivo de cópia de segurança Exec e a tarefa de eliminação de duplicados

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-archive-and-duplication-job"></a>Para atribuir volumes do StorSimple para uma tarefa de arquivo e duplicação de Exec de cópia de segurança

1.  Na consola de gestão Exec de cópia de segurança, faça duplo clique na tarefa de que pretende arquivar a um volume StorSimple e, em seguida, selecione **propriedades de definição de cópia de segurança** > **editar**.

    ![Consola de gestão de Exec de cópia de segurança, separador de propriedades de definição de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

2.  Selecione **adicionar fase** > **duplicada para o disco** > **editar**.

    ![Consola de gestão de Exec de cópia de segurança, adicione fase](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

3.  No **opções de duplicar** diálogo caixa, selecione os valores que pretende utilizar para **origem** e **agenda**.

    ![Exec e consola de gestão, propriedades de definições de cópia de segurança duplicadas opções de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

4.  No **armazenamento** pendente lista, selecione o volume do StorSimple onde pretende que a tarefa de arquivo para armazenar os dados.

    ![Exec e consola de gestão, propriedades de definições de cópia de segurança duplicadas opções de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

5.  Selecione **verifique**e, em seguida, selecione o **não verificar os dados para esta tarefa** caixa de verificação.

    ![Exec e consola de gestão, propriedades de definições de cópia de segurança duplicadas opções de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

6.  Selecione **OK**.

    ![Exec e consola de gestão, propriedades de definições de cópia de segurança duplicadas opções de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

7.  No **cópia de segurança** coluna, adicione uma fase de novo. Para a origem de utilizar **incremental**. Para o destino, escolha o volume do StorSimple onde a tarefa de cópia de segurança incremental é arquivada. Repita os passos 1-6.

## <a name="storsimple-cloud-snapshots"></a>Instantâneos de nuvem do StorSimple

Instantâneos de nuvem do StorSimple protegem os dados que residem no seu dispositivo StorSimple. Criar um instantâneo na nuvem é equivalente ao envio bandas de cópia de segurança locais para um local externo. Se utilizar o armazenamento com redundância geográfica do Azure, criar um instantâneo na nuvem é equivalente ao envio bandas de cópia de segurança para múltiplos sites. Se tiver de restaurar um dispositivo depois de um desastre, poderá colocar o dispositivo StorSimple outro online e efetue uma ativação pós-falha. Após a ativação pós-falha, deverá conseguir aceder os dados (velocidades de nuvem) a partir do instantâneo na nuvem mais recente.

A seguinte secção descreve como criar um script curto para iniciar e eliminar os instantâneos de nuvem do StorSimple durante o pós-processamento de cópia de segurança.

> [!NOTE]
> Instantâneos manualmente ou programaticamente criados não siga a política de expiração de instantâneo do StorSimple. Estes instantâneos tem de ser manualmente ou programaticamente eliminados.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Iniciar e eliminar os instantâneos de nuvem utilizando um script

> [!NOTE]
> Avalie cuidadosamente o repercussions de retenção de dados e de conformidade antes de eliminar um instantâneo do StorSimple. Para obter mais informações sobre como executar um script pós-cópia de segurança, consulte o [documentação de cópia de segurança Exec](https://www.veritas.com/support/en_US/15047.html).

### <a name="backup-lifecycle"></a>Ciclo de vida de cópia de segurança

![Diagrama de ciclo de vida de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

### <a name="requirements"></a>Requisitos

-   O servidor que executa o script tem de ter acesso a recursos na nuvem do Azure.
-   A conta de utilizador tem de ter as permissões necessárias.
-   Uma política de cópia de segurança do StorSimple com os volumes do StorSimple associados tem configurar, mas não ativada.
-   Terá do nome de recurso do StorSimple, a chave de registo, ID de política de nome de cópia de segurança do dispositivo.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Para iniciar ou eliminar um instantâneo na nuvem

1.  [Instalar o Azure PowerShell](/powershell/azure/overview).
2. Transferir e configurar [gerir CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) script do PowerShell.
3. No servidor que executa o script, execute o PowerShell como administrador. Certifique-se de que tem de executar o script com `-WhatIf $true` ver o que altera o script irá efetuar. Depois de concluída a validação, transmitir `-WhatIf $false`. Execute o comando abaixo:
```powershell
.\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
```
4.  Adicione o script para a tarefa de cópia de segurança na cópia de segurança Exec ao editar o pré-processamento de as opções tarefa Exec cópia de segurança e processamento pós-cópia de comandos.

    ![Consola de Exec, opções de cópia de segurança, separador de pré e pós-atualização processamento de comandos de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> Recomendamos que execute a sua política de cópia de segurança de instantâneos de nuvem StorSimple como um script de pós-processamento no fim da tarefa de cópia de segurança diária. Para obter mais informações sobre como fazer cópias de segurança e restaurar o ambiente de aplicação de cópia de segurança para ajudar a satisfazer as suas RPO e RTO, consulte com o arquiteto de sistemas de cópia de segurança.

## <a name="storsimple-as-a-restore-source"></a>StorSimple como uma origem de restauro

Restaura a partir de um trabalho do dispositivo StorSimple como restaura a partir de qualquer dispositivo de armazenamento de blocos. Ocorre restauros dos dados que estão em camadas para a nuvem velocidades de nuvem. Para os dados locais, restauros ocorrerem na velocidade do disco local do dispositivo. Para obter informações sobre como efetuar um restauro, consulte a documentação de cópia de segurança Exec. Recomendamos que lhe está em conformidade com a cópia de segurança Exec restauro melhores práticas.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple ativação pós-falha e recuperação após desastre

> [!NOTE]
> Para cenários de destino de cópia de segurança, o dispositivo de nuvem do StorSimple não é suportado como um destino de restauro.

Um desastre pode ser causado por vários fatores. A tabela seguinte apresenta uma lista de cenários de recuperação após desastre comuns.

| Cenário | Impacto | Como recuperar | Notas |
|---|---|---|---|
| Falha de dispositivo do StorSimple | Operações de cópia de segurança e restauro sejam interrompidas. | Substitua o dispositivo com falhas e efetuar [StorSimple ativação pós-falha e recuperação após desastre](storsimple-device-failover-disaster-recovery.md). | Se precisar de efetuar um restauro após a recuperação de dispositivo, conjuntos de trabalho completo de dados são obtidos a partir da nuvem para o novo dispositivo. Todas as operações são velocidades de nuvem. O processo de indexação e cataloging rescanning poderá fazer com que todos os conjuntos de cópias de segurança ser analisada e retirado da camada de nuvem para a camada de dispositivo local, o que pode ser um processo moroso. |
| Falha do servidor Exec de cópia de segurança | Operações de cópia de segurança e restauro sejam interrompidas. | Reconstruir o servidor de cópia de segurança e efetuar o restauro de base de dados, conforme detalhado em [como fazer uma base de dados manual de cópia de segurança e restauro da cópia de segurança Exec (BEDB)](http://www.veritas.com/docs/000041083). | Tem de reconstruir ou restaurar o servidor de cópia de segurança Exec no site de recuperação de desastre. Restaure a base de dados para o ponto mais recente. Se a base de dados restaurada do Exec cópia de segurança não está sincronizada com as suas tarefas de cópia de segurança mais recentes, a indexação e cataloging são necessário. Este índice e o catálogo de reanálise da processo poderão fazer com que todos os conjuntos de cópias de segurança ser analisada e retirado da camada de nuvem para a camada do dispositivo local. Isto torna mais tempo intensivas. |
| Falha do site que resulte em perda do servidor de cópia de segurança e do StorSimple | Operações de cópia de segurança e restauro sejam interrompidas. | Restaurar StorSimple primeiro e, em seguida, restaure a cópia de segurança Exec. | Restaurar StorSimple primeiro e, em seguida, restaure a cópia de segurança Exec. Se precisar de efetuar um restauro após a recuperação de dispositivo, os conjuntos de trabalho completo de dados são obtidos a partir da nuvem para o novo dispositivo. Todas as operações são velocidades de nuvem. |

## <a name="references"></a>Referências

Os seguintes documentos foram referenciados para este artigo:

- [Configuração de e/s multipath do StorSimple](storsimple-configure-mpio-windows-server.md)
- [Cenários de armazenamento: aprovisionamento dinâmico](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Utilizar o GPT unidades](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Configurar cópias sombra para pastas partilhadas](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como [restauro a partir de um conjunto de cópia de segurança](storsimple-restore-from-backup-set-u2.md).
- Saiba mais sobre como efetuar [dispositivo ativação pós-falha e recuperação após desastre](storsimple-device-failover-disaster-recovery.md).
