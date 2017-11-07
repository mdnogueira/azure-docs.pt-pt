---
title: "Servidor de cópia de segurança do Azure protege o estado do sistema e restaura bare-metal | Microsoft Docs"
description: "Utilize o servidor de cópia de segurança do Azure para criar cópias de segurança do Estado do sistema e fornecer proteção de recuperação bare-metal (BMR)."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
keywords: 
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.targetplatform: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: markgal,masaran
ms.openlocfilehash: 30f70a702d7d9a3e1196c04096708c035e406607
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Cópia de segurança do Estado do sistema e restaurar para bare-metal com o servidor de cópia de segurança do Azure

Servidor de cópia de segurança do Azure efetua cópias de segurança do Estado do sistema e fornece proteção de recuperação bare-metal (BMR).

*   **Cópia de segurança de estado de sistema**: efetua cópias de segurança de ficheiros do sistema operativo, pelo que pode recuperar quando um computador é iniciado, mas os ficheiros de sistema e registo sejam perdidas. Inclui uma cópia de segurança do Estado do sistema:
    * Membro de domínio: ficheiros, a base de dados de registo de classe COM+, registo de arranque
    * Controlador de domínio: Windows Server Active Directory (NTDS), efetuar o arranque de ficheiros, a base de dados de registo de classe COM+, registo, o volume de sistema (SYSVOL)
    * Computador que executa serviços de cluster: os metadados do servidor de Cluster
    * Computador que executa os serviços de certificados: dados de certificado
* **Cópia de segurança bare-metal**: efetua cópias de segurança de ficheiros do sistema operativo e todos os dados em volumes críticos (exceto os dados de utilizador). Por definição, uma cópia de segurança de BMR inclui uma cópia de segurança do Estado do sistema. Fornece proteção quando um computador não iniciará e for necessário recuperar tudo.

A tabela seguinte resume o que pode fazer cópias de segurança e recuperar. Para obter informações detalhadas sobre as versões da aplicação que podem ser protegidos com o estado do sistema e a BMR, consulte [servidor de cópia de segurança do Azure que faz uma cópia de segurança?](backup-mabs-protection-matrix.md).

|Cópia de segurança|Problema|Recuperar a partir de cópia de segurança do servidor de cópia de segurança do Azure|Recuperar a partir de cópia de segurança de estado de sistema|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Dados de ficheiro**<br /><br />Cópia de segurança de dados normal<br /><br />Cópia de segurança de estado de sistema/BMR|Dados de ficheiro perdidas|S|N|N|
|**Dados de ficheiro**<br /><br />Cópia de segurança do servidor do Backup do Azure de dados de ficheiro<br /><br />Cópia de segurança de estado de sistema/BMR|Sistema de operativo perdido ou danificado|N|S|S|
|**Dados de ficheiro**<br /><br />Cópia de segurança do servidor do Backup do Azure de dados de ficheiro<br /><br />Cópia de segurança de estado de sistema/BMR|Servidor perdido (volumes de dados intactos)|N|N|S|
|**Dados de ficheiro**<br /><br />Cópia de segurança do servidor do Backup do Azure de dados de ficheiro<br /><br />Cópia de segurança de estado de sistema/BMR|Servidor perdido (volumes de dados perdidos)|S|Não|Sim (BMR, seguida de recuperação normal de dados de ficheiro de cópia de segurança)|
|**Dados do SharePoint**:<br /><br />Cópia de segurança de servidor do Backup do Azure de dados do farm<br /><br />Cópia de segurança de estado de sistema/BMR|Site perdido, listas, itens de lista, documentos|S|N|N|
|**Dados do SharePoint**:<br /><br />Cópia de segurança de servidor do Backup do Azure de dados do farm<br /><br />Cópia de segurança de estado de sistema/BMR|Sistema de operativo perdido ou danificado|N|S|S|
|**Dados do SharePoint**:<br /><br />Cópia de segurança de servidor do Backup do Azure de dados do farm<br /><br />Cópia de segurança de estado de sistema/BMR|Recuperação após desastre|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Cópia de segurança de servidor do Backup do Azure de convidado ou anfitrião Hyper-V<br /><br />Cópia de segurança do sistema/BMR estado do anfitrião|VM perdida|S|N|N|
|Hyper-V<br /><br />Cópia de segurança de servidor do Backup do Azure de convidado ou anfitrião Hyper-V<br /><br />Cópia de segurança do sistema/BMR estado do anfitrião|Sistema de operativo perdido ou danificado|N|S|S|
|Hyper-V<br /><br />Cópia de segurança de servidor do Backup do Azure de convidado ou anfitrião Hyper-V<br /><br />Cópia de segurança do sistema/BMR estado do anfitrião|Anfitrião de Hyper-V perdido (VMs intactas)|N|N|S|
|Hyper-V<br /><br />Cópia de segurança de servidor do Backup do Azure de convidado ou anfitrião Hyper-V<br /><br />Cópia de segurança do sistema/BMR estado do anfitrião|Anfitrião de Hyper-V perdido (VMs perdidas)|N|N|S<br /><br />BMR, seguida de recuperação normal de servidor de cópia de segurança do Azure|
|SQL Server/Exchange<br /><br />Cópia de segurança de aplicação do servidor de cópia de segurança do Azure<br /><br />Cópia de segurança de estado de sistema/BMR|Dados da aplicação perdidas|S|N|N|
|SQL Server/Exchange<br /><br />Cópia de segurança de aplicação do servidor de cópia de segurança do Azure<br /><br />Cópia de segurança de estado de sistema/BMR|Sistema de operativo perdido ou danificado|N|Y|S|
|SQL Server/Exchange<br /><br />Cópia de segurança de aplicação do servidor de cópia de segurança do Azure<br /><br />Cópia de segurança de estado de sistema/BMR|Servidor perdido (base de dados/os registos de transações intactos)|N|N|S|
|SQL Server/Exchange<br /><br />Cópia de segurança de aplicação do servidor de cópia de segurança do Azure<br /><br />Cópia de segurança de estado de sistema/BMR|Servidor perdido (base de dados/os registos de transações perdidos)|N|N|S<br /><br />Recuperação de BMR, seguida de recuperação normal de servidor de cópia de segurança do Azure|

## <a name="how-system-state-backup-works"></a>Como funciona a cópia de segurança de estado de sistema

Quando é executada uma cópia de segurança do Estado do sistema, o servidor de cópia de segurança comunica com cópia de segurança do Windows Server para pedir uma cópia de segurança do servidor estado do sistema. Por predefinição, o servidor de cópia de segurança e de cópia de segurança do Windows Server utilizam a unidade que tem o mais espaço livre. Informações sobre esta unidade são guardadas no ficheiro PSDataSourceConfig.xml. Esta é a unidade que utiliza a cópia de segurança do Windows Server para cópias de segurança.

Pode personalizar a unidade que utiliza o servidor de cópia de segurança para a cópia de segurança do Estado do sistema. No servidor protegido, aceda a C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources. Abra o ficheiro PSDataSourceConfig.xml para edição. Alterar o \<FilesToProtect\> valor para a letra de unidade. Guarde e feche o ficheiro. Se existir um conjunto de grupos de proteção para proteger o estado do sistema do computador, execute uma verificação de consistência. Se for gerado um alerta, selecione **Modificar grupo de proteção** no alerta e, em seguida, conclua o assistente. Em seguida, execute outra verificação de consistência.

Tenha em atenção que, se o servidor de proteção estiver num cluster, é possível que uma unidade de cluster será selecionada como a unidade com mais espaço livre. Se essa propriedade da unidade foi mudado para outro nó e uma execução de cópia de segurança de estado de sistema, a unidade não está disponível e a cópia de segurança falhará. Neste cenário, modifique o ficheiro PSDataSourceConfig.xml para apontar para uma unidade local.

Em seguida, a cópia de segurança do Windows Server cria uma pasta designada WindowsImageBackup na raiz da pasta de restauro. Como a cópia de segurança do Windows Server cria a cópia de segurança, todos os dados é colocados nesta pasta. Quando a cópia de segurança estiver concluída, o ficheiro é transferido para o computador do servidor de cópia de segurança. Tenha em atenção as seguintes informações:

* Esta pasta e respetivo conteúdo não sejam limpos quando a cópia de segurança ou transferência for concluída. A melhor forma de pensar desta situação é que o espaço está a ser reservado para a próxima vez que uma cópia de segurança estiver concluída.
* A pasta é criada sempre que é efetuada uma cópia de segurança. O carimbo de data e a indicar a hora da sua última cópia de segurança de estado de sistema.

## <a name="bmr-backup"></a>Cópia de segurança de BMR

Para a BMR (incluindo uma cópia de segurança do Estado do sistema), a tarefa de cópia de segurança é guardada diretamente para uma partilha no computador do servidor de cópia de segurança. Não é guardado para uma pasta no servidor protegido.

Servidor de cópia de segurança chama a cópia de segurança do Windows Server e partilha o volume de réplica para essa cópia de segurança de BMR. Neste caso, não indica cópia de segurança do Windows Server para utilizar a unidade com mais espaço livre. Em vez disso, utiliza a partilha criada para a tarefa.

Quando a cópia de segurança estiver concluída, o ficheiro é transferido para o computador do servidor de cópia de segurança. Os registos são armazenados em c:\windows\logs\windowsserverbackup.

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

-   BMR não é suportada para computadores que executam o Windows Server 2003 ou computadores que executam um sistema operativo cliente.

-   Não é possível proteger a BMR e o sistema de estado para o mesmo computador em grupos de proteção diferentes.

-   Um computador do servidor de cópia de segurança não é possível proteger a próprio para a BMR.

-   Proteção de curta duração em banda (disco para banda ou D2T) não é suportada para a BMR. Armazenamento de longa duração em banda (disco para disco para banda ou D2D2T) é suportado.

-   Para a proteção de BMR, a cópia de segurança do Windows Server tem de estar instalada no computador protegido.

-   Para a proteção de BMR, ao contrário para proteção do Estado do sistema, servidor de cópia de segurança não têm quaisquer requisitos de espaço no computador protegido. Cópia de segurança do Windows Server transfere diretamente as cópias de segurança para o computador do servidor de cópia de segurança. A tarefa de cópia de segurança de transferência não aparece no servidor de cópia de segurança **tarefas** vista.

-   Servidor de cópia de segurança reserva 30 GB de espaço no volume de réplica para a BMR. Pode alterar isto no **alocação do disco** página no assistente Modificar grupo de proteção ou utilizando os cmdlets Get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation PowerShell. No volume de ponto de recuperação, a proteção BMR requer cerca de 6 GB para uma retenção de cinco dias.
    * Tenha em atenção que não é possível reduzir o tamanho do volume de réplica para menos de 15 GB.
    * Servidor de cópia de segurança não calcula o tamanho da origem de dados de BMR. Parte do pressuposto de 30 GB para todos os servidores. Altere o valor com base no tamanho de cópias de segurança de BMR que pretende no seu ambiente. O tamanho de uma cópia de segurança de BMR pode ser aproximadamente calculado como a soma de espaço utilizado em todos os volumes críticos. Volumes críticos = volume de arranque + volume de sistema + volume que aloja os dados de estado do sistema, como o Active Directory.

-   Se alterar o estado da proteção do sistema à proteção de BMR, a proteção BMR requer menos espaço no *volume de pontos de recuperação*. No entanto, o espaço extra no volume não será recuperado. Pode diminuir manualmente o tamanho do volume no **modificar alocação do disco** página do assistente Modificar grupo de proteção ou utilizando os cmdlets Get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation PowerShell.

    Se alterar o estado da proteção do sistema à proteção de BMR, a proteção BMR requer mais espaço no *volume de réplica*. O volume é automaticamente expandido. Se pretender alterar as alocações de espaço predefinidas, utilize o cmdlet do PowerShell Modify-DiskAllocation.

-   Se alterar da proteção de BMR para a proteção de estado do sistema, é necessário mais espaço no volume do ponto de recuperação. Servidor de cópia de segurança poderá tentar aumentar automaticamente o volume. Se não existir espaço suficiente no agrupamento de armazenamento, ocorre um erro.

    Se alterar da proteção de BMR para a proteção de estado do sistema, necessita de espaço disponível no computador protegido. Isto acontece porque a proteção do Estado do sistema guarda primeiro a réplica no computador local e, em seguida, transfere-o para o computador do servidor de cópia de segurança.

## <a name="before-you-begin"></a>Antes de começar

1.  **Implementar o servidor de cópia de segurança do Azure**. Certifique-se de que o servidor de cópia de segurança é corretamente implementado. Para obter mais informações, consulte:
    * [Requisitos de sistema do servidor de cópia de segurança do Azure](http://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Matriz de proteção do servidor de cópia de segurança](backup-mabs-protection-matrix.md)

2.  **Configurar o armazenamento**. Pode armazenar dados de cópia de segurança em disco, banda e na nuvem com o Azure. Para obter mais informações, consulte [preparar o armazenamento de dados](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

3.  **Configurar o agente de proteção**. Instale o agente de proteção no computador que pretende criar cópias de segurança. Para obter mais informações, consulte [implementar o agente de proteção do DPM](http://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Criar cópias de segurança do Estado do sistema e bare-metal
Configurar um grupo de proteção, conforme descrito em [implementar grupos de proteção](http://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups). Tenha em atenção que não é possível proteger a BMR e o sistema de estado para o mesmo computador em diferentes grupos. Além disso, ao selecionar a BMR, estado do sistema é ativado automaticamente.


1.  Para abrir o Assistente Criar novo grupo de proteção na consola do administrador do servidor de cópia de segurança, selecione **proteção** > **ações** > **criar grupo de proteção** .

2.  No **selecionar tipo de grupo de proteção** página, selecione **servidores**e, em seguida, selecione **seguinte**.

3.  No **selecionar membros do grupo** página, expanda o computador e, em seguida, selecione **BMR** ou **estado do sistema**.

    Lembre-se de que não é possível proteger a BMR e o sistema de estado para o mesmo computador em diferentes grupos. Além disso, ao selecionar a BMR, estado do sistema é ativado automaticamente. Para obter mais informações, consulte [implementar grupos de proteção](http://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

4.  No **selecionar método de proteção de dados** página, selecione como pretende lidar com a cópia de segurança de curta e longa duração. Cópia de segurança de curta duração é sempre para disco pela primeira vez, com a opção de criar cópias de segurança do disco do Azure na nuvem utilizando o Backup do Azure (curta ou longa duração). É uma alternativa à cópia de segurança de longo prazo para a nuvem para configurar a cópia de segurança de longa duração numa biblioteca de bandas ou dispositivo de banda de autónomo que está ligado ao servidor de cópia de segurança.

5.  No **selecionar objetivos a curto prazo** página, selecione como pretende criar cópias de segurança para armazenamento de curta duração no disco:
    1. Para **período de retenção**, selecione o período de tempo que pretende manter os dados no disco. 
    2. Para **frequência de sincronização**, selecione a frequência com que pretende executar uma cópia de segurança incremental para disco. Se não pretender definir um intervalo de cópia de segurança, pode verificar o **apenas antes de um ponto de recuperação** opção. Servidor de cópia de segurança será executada uma cópia de segurança rápida completa imediatamente antes de cada ponto de recuperação está agendado.

6.  Se pretender armazenar dados em banda para armazenamento de longa duração, a **especificar objetivos de longo prazo** página, selecione o período de tempo que pretende manter os dados da banda (1 a 99 anos). 
    1. Para **frequência da cópia de segurança**, selecione a frequência cópia de segurança em banda deve ser executada. A frequência é baseada no período de retenção que selecionou:
        * Quando o período de retenção for de 1 a 99 anos, pode selecionar as cópias de segurança ocorrem diárias, semanais, mensais, trimestrais bissemanais, semestral ou anual.
        * Quando o período de retenção for de 1 a 11 meses, pode selecionar as cópias de segurança ocorrem diárias, semanais, bissemanais ou mensais.
        * Quando o período de retenção for de 1 a 4 semanas, pode selecionar as cópias de segurança diárias ou semanais.

    2. No **selecionar da banda e os detalhes da biblioteca** página, selecione a banda e biblioteca a utilizar e, se os dados devem ser comprimidos e encriptados.

7.  No **rever alocação do disco** , reveja o espaço de disco de agrupamento de armazenamento que está alocado para o grupo de proteção.

    1. **Tamanho de dados total** é o tamanho dos dados que pretende criar cópias de segurança.
    2. **Disco espaço a ser aprovisionado no servidor de cópia de segurança do Azure** é o espaço que recomenda a cópia de segurança do servidor para o grupo de proteção. Servidor de cópia de segurança escolhe o volume de cópia de segurança ideal com base nas definições. No entanto, pode editar opções de cópia de segurança de volume no **detalhes de alocação de disco**. 
    3. Para cargas de trabalho, no menu pendente, selecione o armazenamento preferencial. As edições que efectuou alterar os valores para **armazenamento Total** e **armazenamento livre** no **armazenamento em disco disponível** painel. Espaço underprovisioned é a quantidade de armazenamento de cópia de segurança do servidor sugere a que adicionar ao volume, para garantir que as cópias de segurança uniforme.

8.  No **escolher método de criação de réplica** página, selecione como pretende lidar com a replicação inicial de dados completa. Se optar por replicar através da rede, recomendamos que escolha uma hora de ponta. Para grandes quantidades de dados ou para condições de rede que são ideais, considere replicar os dados offline utilizando suportes de dados amovível.

9. No **escolher opções de verificação de consistência** página, selecione como pretende automatizar as verificações de consistência. Pode optar por executar uma verificação apenas quando os dados de réplica se tornar inconsistente, ou com base numa agenda. Se não pretender configurar a verificação de consistência automática, pode executar uma verificação manual em qualquer altura. Execute uma verificação manual, além de **proteção** área da consola do administrador do servidor de cópia de segurança, clique no grupo de proteção e, em seguida, selecione **efetuar verificação de consistência**.

10. Se tiver selecionado para cópia de segurança para a nuvem, utilizando a cópia de segurança do Azure, o **especificar dados da proteção Online** página, certifique-se de que seleciona as cargas de trabalho que pretende fazer cópias de segurança do Azure.

11. No **Especificar agenda de cópia de segurança Online** página, selecionadas cópias de segurança incrementais frequência para o Azure irão ocorrer. Pode agendar cópias de segurança para executar cada dia, semana, mês e ano e selecione a data e a que deve executar. As cópias de segurança podem ocorrer até duas vezes por dia. Sempre que é executada uma cópia de segurança, um ponto de recuperação de dados é criado no Azure a partir de cópia dos dados de cópia de segurança armazenados no disco do servidor de cópia de segurança.

12. No **especificar política de retenção Online** página, selecione a forma como os pontos de recuperação criados a partir de cópias de segurança diárias, semanais, mensais e anuais são mantidos no Azure.

13. No **escolher Online replicação** página, selecione a forma como ocorre a replicação inicial completa dos dados. Pode replicar através da rede ou fazer uma offline (offline seeding) de cópia de segurança. Cópia de segurança offline utiliza a funcionalidade de importação do Azure. Para obter mais informações, consulte [Offline fluxo de trabalho de cópia de segurança na cópia de segurança do Azure](backup-azure-backup-import-export.md).

14. No **resumo** , reveja as suas definições. Depois de selecionar **criar grupo**, ocorre a replicação inicial dos dados. Quando a replicação de dados estiver concluído, no **estado** página, o estado do grupo de proteção é **OK**. Cópia de segurança, em seguida, ocorre pela proteção definições do grupo.

## <a name="recover-system-state-or-bmr"></a>Recuperar o estado do sistema ou da BMR
Pode recuperar a BMR ou estado do sistema para uma localização de rede. Se tiver criado cópias de segurança de BMR, utilize o ambiente de recuperação do Windows (WinRE) para iniciar o sistema e ligá-lo à rede. Em seguida, utilize a cópia de segurança do Windows Server para recuperar a partir da localização de rede. Se tiver uma cópia de segurança do Estado do sistema, utilize apenas a cópia de segurança do Windows Server para recuperar a partir da localização de rede.

### <a name="restore-bmr"></a>Restaurar BMR
Execute recuperação no computador do servidor de cópia de segurança:

1.  No **recuperação** painel, localizar o computador que pretende recuperar e, em seguida, selecione **recuperação bare-Metal**.

2.  Pontos de recuperação disponíveis são indicados a negrito no calendário. Selecione a data e hora para o ponto de recuperação que pretende utilizar.

3.  No **selecionar tipo de recuperação** página, selecione **copiar para uma pasta de rede.**

4.  No **especificar destino** página, selecione onde pretende copiar os dados. Lembre-se de que o destino selecionado tem de ter espaço suficiente. Recomendamos que crie uma nova pasta.

5.  No **especificar opções de recuperação** página, selecione as definições de segurança a aplicar. Em seguida, selecione se pretende utilizar a rede de armazenamento (SAN)-com base em instantâneos de hardware, para acelerar a recuperação. (Esta é uma opção apenas se tiver uma SAN com esta funcionalidade disponível e a capacidade para criar e dividi para o tornar gravável. Além disso, o computador protegido e o computador do servidor de cópia de segurança tem de estar ligados à mesma rede.)

6.  Configure opções de notificação. No **confirmação** página, selecione **recuperar**.

Configure a localização da partilha:

1.  Na localização de restauro, vá para a pasta que tenha a cópia de segurança.

2.  Partilhe a pasta que é um nível acima de WindowsImageBackup para que a raiz da pasta partilhada seja a pasta WindowsImageBackup. Se não fizer isto, o restauro não irá localizar a cópia de segurança. Para ligar utilizando o ambiente de recuperação do Windows (WinRE), terá de uma partilha que pode aceder no WinRE com o endereço IP correto e as credenciais.

Restaure o sistema:

1.  Inicie o computador no qual pretende restaurar a imagem através do DVD do Windows para o sistema que está a restaurar.

2.  Na primeira página, verifique as definições de idioma e região. No **instalar** página, selecione **reparar o seu computador**.

3.  No **opções de recuperação de sistema** página, selecione **restaurar o computador utilizando uma imagem do sistema que criou anteriormente**.

4.  No **selecionar uma cópia de segurança de imagem de sistema** página, selecione **selecionar uma imagem de sistema** > **avançadas** > **procurar uma imagem de sistema na rede**. Se aparecer um aviso, selecione **Sim**. Vá para o caminho de partilha, introduza as credenciais e, em seguida, selecione o ponto de recuperação. Esta ação procura cópias de segurança específicas que estão disponíveis nesse ponto de recuperação. Selecione o ponto de recuperação que pretende utilizar.

5.  No **escolha como restaurar a cópia de segurança** página, selecione **formatar e dividir discos**. Na página seguinte, verifique as definições. 

6.  Para iniciar o restauro, selecione **concluir**. É necessário um reinício.

### <a name="restore-system-state"></a>Restaurar estado do sistema

Execute recuperação no servidor de cópia de segurança:

1.  No **recuperação** painel, localizar o computador que pretende recuperar e, em seguida, selecione **recuperação bare-Metal**.

2.  Pontos de recuperação disponíveis são indicados a negrito no calendário. Selecione a data e hora para o ponto de recuperação que pretende utilizar.

3.  No **selecionar tipo de recuperação** página, selecione **copiar para uma pasta de rede**.

4.  No **especificar destino** página, selecione onde pretende copiar os dados. Lembre-se de que o destino selecionado tem espaço suficiente. Recomendamos que crie uma nova pasta.

5.  No **especificar opções de recuperação** página, selecione as definições de segurança a aplicar. Em seguida, selecione se pretende utilizar instantâneos de hardware baseada em SAN para acelerar a recuperação. (Esta é uma opção apenas se tiver uma SAN com esta funcionalidade e a capacidade para criar e dividi para o tornar gravável. Além disso, o computador protegido e o servidor de cópia de segurança tem de estar ligados à mesma rede.)

6.  Configure opções de notificação. No **confirmação** página, selecione **recuperar**.

Execute cópia de segurança do Windows Server:

1.  Selecione **ações** > **recuperar** > **neste servidor** > **seguinte**.

2.  Selecione **outro servidor**, selecione o **especificar tipo de localização** página e, em seguida, selecione **remoto pasta partilhada**. Introduza o caminho para a pasta que contém o ponto de recuperação.

3.  No **selecionar tipo de recuperação** página, selecione **estado do sistema**. 

4. No **selecionar localização para recuperação do Estado do sistema** página, selecione **localização Original**.

5.  No **confirmação** página, selecione **recuperar**. Após o restauro, reinicie o servidor.

6.  Também pode executar o restauro do Estado do sistema numa linha de comandos. Para fazê-lo, inicie a cópia de segurança do Windows Server no computador que pretende recuperar. Para obter o identificador de versão, numa linha de comandos, introduza:```wbadmin get versions -backuptarget \<servername\sharename\>```

    Utilize o identificador da versão para iniciar o restauro do Estado do sistema. Na linha de comandos, introduza:```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    Certifique-se de que pretende iniciar a recuperação. Pode ver o processo na janela da linha de comandos. É criado um registo do restauro. Após o restauro, reinicie o servidor.

