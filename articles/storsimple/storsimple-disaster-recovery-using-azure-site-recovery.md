---
title: Automatizar a partilha de ficheiros do StorSimple DR com o Azure Site Recovery | Microsoft Docs
description: "Descreve os passos e melhores práticas para criar uma solução de recuperação após desastre para as partilhas de ficheiros alojados no armazenamento do Microsoft Azure StorSimple."
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: vidarmsft
ms.openlocfilehash: e60cc83f49f9e0d0f878d7f49333f1be34ce54a6
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Solução de recuperação após desastre automatizada utilizando o Azure Site Recovery para partilhas de ficheiros alojadas no StorSimple
## <a name="overview"></a>Descrição geral
Microsoft Azure StorSimple é uma solução de armazenamento de nuvem híbrida endereços complexidades de dados não estruturados normalmente associadas a partilhas de ficheiros. StorSimple utiliza armazenamento na nuvem como uma extensão da solução no local e automaticamente os dados de camadas em armazenamento no local e na nuvem. Integrado proteção de dados no local e na nuvem instantâneos, elimina a necessidade de uma infraestrutura de armazenamento sprawling.

[O Azure Site Recovery](../site-recovery/site-recovery-overview.md) é um serviço baseado no Azure que fornece capacidades de (DR) de recuperação de desastre através da orquestração da replicação, ativação pós-falha e recuperação de máquinas virtuais. O Azure Site Recovery suporta um número de tecnologias de replicação para replicar de forma consistente, proteger e totalmente integrada efetuar a ativação pós-falha de máquinas virtuais e aplicações para nuvens pública/privada ou alojadas.

Utilizar o Azure Site Recovery, a replicação da máquina virtual e as capacidades de instantâneos de nuvem do StorSimple, pode proteger o ambiente de servidor de ficheiro completo. Em caso de uma interrupção, pode utilizar um único clique para atualizar as partilhas de ficheiros online no Azure em apenas alguns minutos.

Este documento explica em detalhe como pode criar uma solução de recuperação após desastre para as partilhas de ficheiros alojadas no armazenamento do StorSimple e efetuar planeada, não planeada e testar as ativações pós-falha utilizando um plano de recuperação de um clique. Essencialmente, mostra como é possível modificar o plano de recuperação no seu Cofre de recuperação de sites do Azure para ativar as ativações pós-falha de StorSimple durante cenários após desastre. Além disso, descreve pré-requisitos e configurações suportadas. Este documento parte do princípio de que está familiarizado com as noções básicas do Azure Site Recovery e StorSimple arquiteturas.

## <a name="supported-azure-site-recovery-deployment-options"></a>Opções de implementação suportadas do Azure Site Recovery
Os clientes podem implementar servidores de ficheiros como servidores físicos ou máquinas virtuais (VMs) em execução no Hyper-V ou VMware e, em seguida, criar partilhas de ficheiros dos volumes carved fora do armazenamento StorSimple. O Azure Site Recovery pode proteger as implementações físicas e virtuais para a um site secundário ou para o Azure. Este documento inclui detalhes de uma solução de DR com o Azure como o site de recuperação para um servidor de ficheiros que VM alojada no Hyper-V e com partilhas de ficheiros no armazenamento StorSimple. Outros cenários em que a VM do servidor de ficheiros é uma VM de VMware ou um computador físico podem ser implementados da mesma forma.

## <a name="prerequisites"></a>Pré-requisitos
Implementar uma solução de recuperação após desastre de um clique que utiliza o Azure Site Recovery para partilhas de ficheiros alojadas no armazenamento StorSimple tem os seguintes pré-requisitos:

* Servidor de ficheiros do Windows Server 2012 R2 que VM alojada no Hyper-V ou VMware ou um computador físico no local
* Registado StorSimple armazenamento dispositivos no local com o Azure StorSimple manager
* Aplicação de nuvem do StorSimple criada no Azure StorSimple manager. A aplicação pode ser mantida em estado de encerrar pendente.
* Partilhas de ficheiros alojadas nos volumes configurados no dispositivo de armazenamento StorSimple
* [Cofre de serviços do Azure Site Recovery](../site-recovery/site-recovery-vmm-to-vmm.md) criado numa subscrição do Microsoft Azure

Além disso, se utilizar o Azure como o site de recuperação, execute o [ferramenta de avaliação de preparação de Máquina Virtual do Azure](http://azure.microsoft.com/downloads/vm-readiness-assessment/) em VMs para se certificar de que sejam compatíveis com as VMs do Azure e o Azure Site Recovery services.

Para evitar problemas (o que poderão resultar em custos superiores), certifique-se de que cria a aplicação de nuvem do StorSimple, a conta de automatização e o armazenamento de latência conta (s) na mesma região.

## <a name="enable-dr-for-storsimple-file-shares"></a>Ativar DR para partilhas de ficheiros do StorSimple
Cada componente do ambiente no local tem de ser protegidas para ativar a replicação e recuperação. Esta secção descreve como:

* Configurar a replicação do Active Directory e DNS (opcional)
* Utilizar o Azure Site Recovery para ativar a proteção no servidor de ficheiros VM
* Ativar a proteção de volumes do StorSimple
* Configurar a rede

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Configurar a replicação do Active Directory e DNS (opcional)
Se pretender proteger as máquinas em execução do Active Directory e DNS, para que fiquem disponíveis no site de DR, terá de protegê-los explicitamente (de modo a que os servidores de ficheiros são acessíveis após a ativação pós-falha com a autenticação). Existem duas opções recomendadas com base na complexidade do ambiente de no local do cliente.

#### <a name="option-1"></a>opção 1
Se o cliente tem um pequeno número de aplicações, um único controlador de domínio para todo o site no local e será possível efetuar a ativação pós-falha de todo o local, em seguida, recomendamos que utilize replicação de Azure Site Recovery para replicar a máquina de controlador de domínio para uma secundária site (isto é aplicável para o site para site e o site para o Azure).

#### <a name="option-2"></a>Opção 2
Se o cliente tem um grande número de aplicações, está em execução numa floresta do Active Directory e será possível efetuar a ativação pós-falha de algumas aplicações numa altura, em seguida, é recomendável configurar um controlador de domínio adicional no site de DR (ou um site secundário ou no Azure).

Consulte [soluções de DR automatizada para o Active Directory e DNS, utilizando o Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) para obter instruções quando disponibilizar um controlador de domínio no site de DR. Para o resto deste documento, partimos do que controlador de domínio está disponível no site de DR.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Utilizar o Azure Site Recovery para ativar a proteção no servidor de ficheiros VM
Este passo necessita que a preparar o ambiente de servidor de ficheiros no local, cria e preparar um cofre do Azure Site Recovery e ativar a proteção de ficheiros da VM.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Para preparar o ambiente de servidor de ficheiros no local
1. Definir o **controlo de conta de utilizador** para **nunca notificar**. Isto é necessário para que possa utilizar scripts de automatização do Azure para ligar os destinos iSCSI após a ativação pós-falha pelo Azure Site Recovery.

   1. Prima a tecla Windows + Q e procure **UAC**.
   2. Selecione **definições de controlo de conta de utilizador de alteração**.
   3. Arraste a barra na parte inferior para **nunca notificar**.
   4. Clique em **OK** e, em seguida, selecione **Sim** quando lhe for pedido.

      ![Definições de controlo de conta de utilizador](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png)
2. Instale o agente VM em cada servidor de ficheiros VMs. Isto é necessário para que possam executar scripts de automatização do Azure na qual foi a ativação pós-falha das VMs.

   1. [Transferir o agente](http://aka.ms/vmagentwin) para `C:\\Users\\<username>\\Downloads`.
   2. Abra o Windows PowerShell no modo de administrador (executar como administrador) e, em seguida, introduza o seguinte comando para navegar para a localização de transferência:

      `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`

      > [!NOTE]
      > Pode alterar o nome de ficheiro, dependendo da versão.
      >
      >
3. Clique em **Seguinte**.
4. Aceitar o **termos do contrato** e, em seguida, clique em **seguinte**.
5. Clique em **Concluir**.
6. Crie partilhas de ficheiros utilizando volumes carved fora do armazenamento StorSimple. Para obter mais informações, consulte [utilizar o serviço StorSimple Manager para gerir volumes](storsimple-manage-volumes.md).

   1. No seu VMs no local, prima a tecla Windows + Q e procure **iSCSI**.
   2. Selecione **iniciador iSCSI**.
   3. Selecione o **configuração** separador e copie o nome do iniciador.
   4. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
   5. Selecione o **StorSimple** separador e, em seguida, selecione o serviço StorSimple Manager que contém o dispositivo físico.
   6. Criar contentores de volume e, em seguida, crie volumes. (Estes volumes destinam-se as partilhas de ficheiros no servidor de ficheiros VMs). Copie o nome do iniciador e dê um nome adequado para os registos de controlo de acesso quando criar os volumes.
   7. Selecione o **configurar** separador e tenha em atenção pendente o endereço IP do dispositivo.
   8. No seu VMs no local, vá para o **iniciador iSCSI** novamente e introduza o IP na secção de ligação rápido. Clique em **ligar rápida** (o dispositivo agora deve ser ligado).
   9. Abra o Azure portal e selecione o **Volumes e dispositivos** separador. Clique em **automática configurar**. O volume que criou deve aparecer.
   10. No portal, selecione o **dispositivos** separador e, em seguida, selecione **criar um novo dispositivo Virtual.** (Este dispositivo virtual será utilizado se ocorrer uma ativação pós-falha). Este novo dispositivo virtual pode ser mantido num estado offline para evitar custos adicionais. Para colocar o dispositivo virtual offline, vá para o **máquinas virtuais** secção no Portal e encerrá-la.
   11. Volte para as VMs no local e abra Gestão de discos (prima a tecla Windows + X e selecione **gestão de discos**).
   12. Vai notar algumas discos adicionais (dependendo do número de volumes que criou). Clique com o botão direito do primeiro uma, selecione **Inicializar disco**e selecione **OK**. Clique com botão direito do **Unallocated** secção, selecione **Novo Volume simples**, atribua-lhe uma letra de unidade e concluir o assistente.
   13. Repita o passo l para todos os discos. Agora, pode ver todos os discos no **este PC** no Explorador do Windows.
   14. Utilize a função Serviços de armazenamento e de ficheiros para criar partilhas de ficheiros nestes volumes.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Para criar e preparar um cofre do Azure Site Recovery
Consulte o [documentação do Azure Site Recovery](../site-recovery/site-recovery-hyper-v-site-to-azure.md) para começar a utilizar o Azure Site Recovery antes de proteger o VM do servidor de ficheiros.

#### <a name="to-enable-protection"></a>Para ativar a proteção
1. Desligar o destino de iSCSI (s) de VMs no local que pretende proteger através do Azure Site Recovery:

   1. Prima a tecla Windows + Q e procure **iSCSI**.
   2. Selecione **configurar o iniciador iSCSI**.
   3. Desligue o dispositivo StorSimple ligado anteriormente. Em alternativa, pode desactivar o servidor de ficheiros para alguns minutos quando ativar a proteção.

   > [!NOTE]
   > Isto fará com que as partilhas de ficheiros fique temporariamente indisponível.
   >
   >
2. [Ativar a proteção da máquina virtual](../site-recovery/site-recovery-hyper-v-site-to-azure.md) no servidor de ficheiros VM partir do portal do Azure Site Recovery.
3. Quando começa a sincronização inicial, poderá restabelecer a ligação destino novamente. Vá para o iniciador iSCSI, selecione o dispositivo StorSimple e clique em **Connect**.
4. Quando a sincronização estiver concluída e o estado da VM é **protegidos**, selecione a VM, selecione o **configurar** separador e atualize a rede da VM em conformidade (esta é a rede que a ativação pós-falha VM irá fazer parte de uma). Se a rede não apresentada, significa que a sincronização ainda acontecimentos.

### <a name="enable-protection-of-storsimple-volumes"></a>Ativar a proteção de volumes do StorSimple
Se não tiver selecionado o **ativar uma cópia de segurança predefinido para este volume** opção para os volumes do StorSimple, aceda a **políticas de cópia de segurança** no StorSimple Manager service e criar uma política de cópia de segurança adequada para todos os volumes. Recomendamos que defina a frequência de cópias de segurança para o objetivo de ponto de recuperação (RPO) que gostaria de ver para a aplicação.

### <a name="configure-the-network"></a>Configurar a rede
Para o servidor de ficheiros, VM, configure definições de rede no Azure Site Recovery para que as redes VM estão ligadas à rede de DR correta após a ativação pós-falha.

Pode selecionar a VM com o **replicado itens** separador para configurar as definições de rede, conforme mostrado na ilustração seguinte.

![Computação e rede](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação
Pode criar um plano de recuperação na ASR para automatizar o processo de ativação pós-falha das partilhas de ficheiros. Se ocorrer uma interrupção, pode trazer as partilhas de ficheiros dentro de alguns minutos, com apenas um único clique. Para ativar esta automatização, terá de uma conta de automatização do Azure.

#### <a name="to-create-an-automation-account"></a>Para criar uma conta de automatização
1. Aceda ao portal do Azure &gt; **automatização** secção.
2. Clique em **+ adicionar** botão, é apresentada abaixo painel.

   ![Adicionar Conta de Automatização](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)

   * Nome - introduza uma nova conta de automatização
   * Subscrição - escolha subscrição
   * Recursos de grupo - criar novo/escolher grupo de recursos existente
   * Localização - escolher localização, mantenha-a na mesma georreplicação/região na qual a aplicação de nuvem do StorSimple e contas de armazenamento foram criadas.
   * Criar conta Run As do Azure – selecione **Sim** opção.

3. Vá para a conta de automatização, clique em **Runbooks** &gt; **procurar galeria** para importar todos os runbooks necessários para a conta de automatização.
4. Adicione os seguintes runbooks ao localizar **recuperação após desastre** etiquetas na galeria:

   * Limpar de volumes do StorSimple após a ativação pós-falha de teste (TFO)
   * Contentores de volume do StorSimple de ativação pós-falha
   * Montar os volumes no dispositivo StorSimple após a ativação pós-falha
   * Desinstalar a extensão de script personalizado na VM do Azure
   * Iniciar o dispositivo Virtual StorSimple

     ![Galeria de procura](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)

5. Publicar todos os scripts, selecionando o runbook na conta de automatização e clique em **editar** &gt; **publicar** e, em seguida, **Sim** na mensagem de verificação. Após este passo, o **Runbooks** separador irá aparecer da seguinte forma:

    ![Runbooks](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)

6. Na conta de automatização, clique em **variáveis** &gt; **adicionar uma variável** e adicione as seguintes variáveis. Pode optar por encriptar estes recursos. Estas variáveis são específicas do plano de recuperação. Se planear a recuperação, que irá criar no próximo passo, nome é TestPlan, em seguida, as variáveis devem ser TestPlan-StorSimRegKey, TestPlan-AzureSubscriptionName e assim sucessivamente.

   * **BaseUrl**: O Gestor de recursos de url para a nuvem do Azure. Obter utilizando **Get-AzureRmEnvironment | Nome de Select-Object, ResourceManagerUrl** cmdlet.
   * *RecoveryPlanName***- ResourceGroupName**: grupo do Gestor de recursos que tem o recurso do StorSimple.
   * *RecoveryPlanName***- ManagerName**: StorSimple o recurso que possui o dispositivo StorSimple.
   * *RecoveryPlanName***- DeviceName**: O dispositivo StorSimple que tenha a ativação pós-falha.
   * *RecoveryPlanName***- DeviceIpAddress**: O endereço IP do dispositivo (esta pode ser encontrada no **dispositivos** separador na secção de Gestor de dispositivos do StorSimple &gt; **Definições** &gt; **rede** &gt; **as definições de DNS** grupo).
   * *RecoveryPlanName***- VolumeContainers**: uma cadeia separada por vírgulas de contentores de volume presentes no dispositivo que tem de ser falha mais; por exemplo: volcon1 volcon2, volcon3.
   * *RecoveryPlanName***- TargetDeviceName**: O dispositivo de nuvem do StorSimple em que os contentores estão a ativação pós-falha.
   * *RecoveryPlanName***- TargetDeviceIpAddress**: O endereço IP do dispositivo de destino (Isto pode ser encontrado no **Máquina Virtual** secção &gt; **definições**  grupo &gt; **redes** separador).
   * *RecoveryPlanName***- StorageAccountName**: O nome da conta de armazenamento na qual o script (que tem de executar a ativação pós-falha da VM) será armazenado. Isto pode ser uma conta de armazenamento que tenha algum espaço para armazenar temporariamente o script.
   * *RecoveryPlanName***- StorageAccountKey**: A chave de acesso para a conta de armazenamento acima.
   * *RecoveryPlanName***- VMGUIDS**: após a proteger uma VM, do Azure Site Recovery atribui cada VM um ID exclusivo, que fornece os detalhes da falha da ativação pós-falha da VM. Para obter o VMGUID, selecione o **dos serviços de recuperação** separador e clique em **Item protegido** &gt; **grupos de proteção** &gt;  **Máquinas** &gt; **propriedades**. Se tiver várias VMs, em seguida, adicione os GUIDs como uma cadeia separada por vírgulas.

    Por exemplo, se o nome do plano de recuperação é fileServerpredayRP, em seguida, o **variáveis**, **ligações** e **certificados** separador deverá aparecer da seguinte forma depois de adicionar todos os recursos.

    ![Elementos](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

7. Carregar o módulo do Runbook série 8000 do StorSimple na sua conta de automatização. Utilize os passos abaixo para adicionar um módulo:

   a. Abra o powershell, crie uma nova pasta e altere o diretório para a pasta.
    
    ```
         mkdir C:\scripts\StorSimpleSDKTools
         cd C:\scripts\StorSimpleSDKTools
    ```
   b. Transferir nuget CLI sob a mesma pasta no passo 1.
      Estão disponíveis no várias versões de nuget.exe [nuget transfere](https://www.nuget.org/downloads). Cada ligação de transferência aponta diretamente para um ficheiro de .exe, por isso, certifique-se com o botão direito e guarde o ficheiro para o seu computador em vez de executar a partir do browser.

    ```
         wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

   c. Transferir o SDK dependente

    ```
         C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
         C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
         C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

   d. Crie um módulo do Runbook de automatização do Azure para gestão de dispositivos de série de 8000 do StorSimple. Utilize o abaixo comandos para criar um ficheiro de zip do módulo de automatização.

    ```
         # set path variables
         $downloadDir = "C:\scripts\StorSimpleSDKTools"
         $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

         #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
         mkdir "$moduleDir"

         copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
         copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
         copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
         copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
         copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
         copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

         #Don't change the name of the Archive
         compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

     e. Importe o automatização do Azure ficheiro zip do módulo (Microsoft.Azure.Management.StorSimple8000Series.zip) criado nos passos acima. Isto pode ser feito ao selecionar a conta de automatização, clique em **módulos** em recursos PARTILHADOS e, em seguida, clique em **adicionar um módulo**.

    Depois de importar o módulo de série 8000 do StorSimple, o **módulos** separador deverá aparecer da seguinte forma:

    ![Módulos](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

8. Vá para o **dos serviços de recuperação** secção e selecione o Cofre de recuperação de sites do Azure que criou anteriormente.
9. Selecione o **planos de recuperação (recuperação de sites)** opção de **gerir** agrupar e criar um novo plano de recuperação da seguinte forma:

   a.  Clique em **+ plano de recuperação** botão, é apresentada abaixo painel.

      ![Criar plano de recuperação](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)

   b.  Introduza um nome do plano de recuperação, escolha os valores de modelo de origem, destino e implementação.

   c.  Selecione as VMs do grupo de proteção que pretende incluir no plano de recuperação e clique em **OK** botão.

   d.  Selecione o plano de recuperação que criou anteriormente, clique em **personalizar** botão para abrir a vista de personalização do plano de recuperação.

   e.  Clique em **encerramento do todos os grupos** e clique em **adicionar ação pre**.

   f.  Abre painel de ação de inserção, introduza um nome, selecione **lado primário** opção na onde executar a opção, selecione a conta de automatização (no qual adicionou os runbooks) e, em seguida, selecione o **Volume-contentores de ativação pós-falha-StorSimple**  runbook.

   g.  Clique em **grupo 1: Iniciar** e clique em **adicionar itens protegidos** opção, em seguida, selecione as VMs que estão a ser protegidas no plano de recuperação e clique **Ok** botão. Opcional, se estiver já selecionada VMs.

   h.  Clique em **grupo 1: Iniciar** e clique em **publique ação** opção, em seguida, adicione os scripts seguintes:

   * Runbook Start-StorSimple--aplicação Virtual
   * Efetuar a ativação pós-falha StorSimple-volume contentores runbook
   * Runbook de montagem-volumes-após-ativação pós-falha
   * Runbook desinstalar---extensão de script personalizado

   posso.  Adicionar uma ação manual depois dos scripts de 4 acima na mesma **grupo 1: pós-passos** secção. Esta ação é o ponto no qual pode verificar que tudo está a funcionar corretamente. Esta ação tem de ser adicionada apenas como parte da ativação pós-falha de teste (selecione, pelo que apenas o **ativação pós-falha de teste** caixa de verificação).

   j.  Depois da ação manual, adicione o **limpeza** script utilizando o mesmo procedimento utilizado para os outros runbooks. **Guardar** o plano de recuperação.

    > [!NOTE]
    > Ao executar uma ativação pós-falha de teste, deve verificar tudo o passo da ação manual porque os volumes do StorSimple que tinham sido clonados no dispositivo de destino serão eliminados como parte de limpeza depois de concluída a ação manual.
    >

    ![Plano de Recoery](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Execute uma ativação pós-falha de teste
Consulte o [soluções de DR de diretório do Active Directory](../site-recovery/site-recovery-active-directory.md) guia complementar para considerações específicas para o Active Directory durante a ativação pós-falha de teste. O programa de configuração no local não está em todos os disturbed quando ocorre a ativação pós-falha de teste. Os volumes do StorSimple que foram ligados à VM no local são clonados para a aplicação de nuvem do StorSimple no Azure. Uma VM para fins de teste seja colocada de cópia de segurança no Azure e os volumes clonados estão ligados à VM.

#### <a name="to-perform-the-test-failover"></a>Para efetuar a ativação pós-falha de teste
1. No portal do Azure, selecione o Cofre de recuperação de sites.
2. Clique no plano de recuperação criado para a VM do servidor de ficheiros.
3. Clique em **ativação pós-falha de teste**.
4. Selecione a rede virtual do Azure para o qual as VMs do Azure serão ligadas após a ocorrência da ativação pós-falha.

   ![Iniciar ativação pós-falha](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
5. Clique em **OK** para iniciar a ativação pós-falha. Pode controlar o progresso clicando na VM para abrir as respetivas propriedades ou no **tarefa de teste de ativação pós-falha** no nome do cofre &gt; **tarefas** &gt; **tarefas de recuperação de Site**.
6. Depois da ativação pós-falha concluir, deve também conseguir ver a réplica do Azure machine aparecer no portal do Azure &gt; **máquinas virtuais**. Pode efetuar as validações.
7. Depois de validações terminadas, clique em **validações concluída**. Isto irá remover os Volumes do StorSimple e encerrar a aplicação de nuvem do StorSimple.
8. Quando tiver terminado, clique em **ativação pós-falha de teste de limpeza** no plano de recuperação. Nas notas registar e guardar todas as observações associadas à ativação pós-falha de teste. Este procedimento eliminará a máquina virtual que foram criados durante a ativação pós-falha de teste.

## <a name="perform-a-planned-failover"></a>Efetuar uma ativação pós-falha planeada
   Durante uma ativação pós-falha planeada, o local servidor de ficheiros que VM seja encerrada corretamente e uma nuvem instantâneo de cópia de segurança dos volumes no dispositivo StorSimple. Os volumes do StorSimple são a ativação pós-falha para o dispositivo virtual, uma VM de réplica é colocada de cópia de segurança no Azure e os volumes estão ligados à VM.

#### <a name="to-perform-a-planned-failover"></a>Para efetuar uma ativação pós-falha planeada
1. No portal do Azure, selecione **dos serviços de recuperação** cofre &gt; **planos de recuperação (recuperação de sites)** &gt; **recoveryplan_name** criada para o VM do servidor de ficheiros.
2. No painel de plano de recuperação, clique em **mais** &gt; **a ativação pós-falha planeada**.

   ![Plano de recuperação](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
3. No **confirmar a ativação de pós-falha planeadas** painel, selecione de origem e localizações de destino e de rede de destino selecione e clique no ícone de verificação ✓ para iniciar o processo de ativação pós-falha.
4. Depois de máquinas virtuais de réplica são criadas estes estão no estado de confirmação pendente. Clique em **consolidação** para consolidar a ativação pós-falha.
5. Após a conclusão da replicação, as máquinas virtuais iniciam na localização secundária.

## <a name="perform-a-failover"></a>Execute uma ativação pós-falha
Durante uma ativação pós-falha não planeada, os volumes do StorSimple são a ativação pós-falha para o dispositivo virtual, uma VM de réplica será reencaminhada cópias de segurança no Azure e os volumes estão ligados à VM.

#### <a name="to-perform-a-failover"></a>Para efetuar uma ativação pós-falha
1. No portal do Azure, selecione **dos serviços de recuperação** cofre &gt; **planos de recuperação (recuperação de sites)** &gt; **recoveryplan_name** criada para o VM do servidor de ficheiros.
2. No painel de plano de recuperação, clique em **mais** &gt; **ativação pós-falha**.
3. No **confirmar ativação pós-falha** painel, selecione a origem e localizações de destino.
4. Selecione **encerrar máquinas virtuais e sincronizar os dados mais recentes** para especificar que a recuperação de sites deve tentar de encerrar a máquina virtual protegida e sincronizar os dados para que a versão mais recente dos dados será efetuada a ativação pós-falha.
5. Após a ativação pós-falha, as máquinas virtuais estão no estado de confirmação pendente. Clique em **consolidação** para consolidar a ativação pós-falha.


## <a name="perform-a-failback"></a>Efetuar uma reativação pós-falha
Durante uma reativação pós-falha, contentores de volume do StorSimple são a ativação pós-falha para o dispositivo físico após uma cópia de segurança.

#### <a name="to-perform-a-failback"></a>Para efetuar uma reativação pós-falha
1. No portal do Azure, selecione **dos serviços de recuperação** cofre &gt; **planos de recuperação (recuperação de sites)** &gt; **recoveryplan_name** criada para o VM do servidor de ficheiros.
2. No painel de plano de recuperação, clique em **mais** &gt; **ativação pós-falha planeada**.
3. Selecione as localizações de origem e de destino, selecione a sincronização de dados adequada e opções de criação de VM.
4. Clique em **OK** botão para iniciar o processo de reativação pós-falha.

   ![Iniciar a reativação pós-falha](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Melhores práticas
### <a name="capacity-planning-and-readiness-assessment"></a>Avaliação de preparação e de planeamento de capacidade
#### <a name="hyper-v-site"></a>Site Hyper-V
Utilize o [ferramenta utilizador Capacity planner](http://www.microsoft.com/download/details.aspx?id=39057) para estruturar o servidor, armazenamento e infraestrutura de rede para o seu ambiente de réplica do Hyper-V.

#### <a name="azure"></a>Azure
Pode executar o [ferramenta de avaliação de preparação de Máquina Virtual do Azure](http://azure.microsoft.com/downloads/vm-readiness-assessment/) em VMs para se certificar de que sejam compatíveis com as VMs do Azure e do Azure Site Recovery Services. A ferramenta de avaliação de preparação verifica as configurações de VM e avisa quando configurações são incompatíveis com o Azure. Por exemplo, emite um aviso se for uma unidade de c: com mais de 127 GB.

Planeamento da capacidade é constituído por, pelo menos, dois processos importantes:

* Mapeamento de local VMs de Hyper-V para tamanhos de VM do Azure (por exemplo, A6, A7, A8 e A9).
* Determinar a largura de banda de Internet necessária.

## <a name="limitations"></a>Limitações
* Atualmente, apenas 1 dispositivo StorSimple pode ser a ativação pós-falha (para um único dispositivo de nuvem do StorSimple). O cenário de um servidor de ficheiros que abranja vários dispositivos StorSimple ainda não é suportado.
* Se obtiver um erro ao ativar a proteção para uma VM, certifique-se de que tem desligada destinos iSCSI.
* Todos os contentores de volume foram agrupados em conjunto devido às políticas de cópia de segurança de expansão em contentores de volume serão a ativação pós-falha em conjunto.
* Todos os volumes nos contentores de volume que escolheu serão ativados pós-falha.
* Volumes que adicionam até 64 TB, mais do que não é possível a ativação pós-falha porque a capacidade máxima de um único dispositivo de nuvem do StorSimple é 64 TB.
* Se falha a ativação pós-falha planeadas/imprevistas e as VMs que são criadas no Azure, em seguida, não limpeza até as VMs. Em vez disso, efetue uma reativação pós-falha. Se eliminar as VMs, em seguida, as VMs no local não podem ser ativadas novamente.
* Após uma ativação pós-falha, se não for capaz de ver os volumes, vá para as VMs, abra a gestão de discos, voltar a analisar os discos e, em seguida, colocá-los online.
* Em alguns casos, as letras de unidade no site de DR podem ser diferentes de letras no local. Se isto ocorrer, terá de manualmente corrigir o problema, depois de concluída a ativação pós-falha.
* Tempo limite da tarefa de ativação pós-falha: StorSimple o script será o limite de tempo se a ativação pós-falha de contentores de volume demora mais tempo do que o limite do Azure Site Recovery por script (atualmente 120 minutos).
* Tempo limite da tarefa de cópia de segurança: StorSimple o script exceder o tempo limite se a cópia de segurança de volumes demora mais tempo do que o limite do Azure Site Recovery por script (atualmente 120 minutos).

  > [!IMPORTANT]
  > Execute a cópia de segurança manualmente a partir do portal do Azure e, em seguida, execute novamente o plano de recuperação.

* Tempo limite da tarefa de clone: StorSimple o script exceder o tempo limite se a clonagem de volumes demora mais tempo do que o limite do Azure Site Recovery por script (atualmente 120 minutos).
* Erro de sincronização de tempo: erros de saída indicar que as cópias de segurança foram sem êxito, apesar da cópia de segurança é concluída com êxito no portal de scripts do StorSimple. Uma causa possível para este poderá ser que o dispositivo StorSimple podem seres dessincronizado com a hora atual no fuso horário.

  > [!IMPORTANT]
  > O tempo de aplicação com a hora atual no fuso horário de sincronização.

* Erro de ativação pós-falha de aplicação: StorSimple o script pode falhar se ocorrer uma ativação pós-falha de dispositivo quando está a executar o plano de recuperação.

  > [!IMPORTANT]
  > Depois de concluída a aplicação de ativação pós-falha, execute novamente o plano de recuperação.


## <a name="summary"></a>Resumo
Utilizando o Azure Site Recovery, pode criar um plano de recuperação após desastre automatizado completa para um servidor de ficheiros VM ter partilhas de ficheiros alojadas no armazenamento StorSimple. Pode iniciar a ativação pós-falha em segundos a partir de qualquer lugar na eventualidade de ocorrer uma interrupção e obter a aplicação e funcional, em alguns minutos.
