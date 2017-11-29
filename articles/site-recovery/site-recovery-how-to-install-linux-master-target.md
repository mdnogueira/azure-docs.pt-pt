---
title: "Como instalar um servidor de destino principal do Linux para ativação pós-falha do Azure no local | Microsoft Docs"
description: "Antes de trocar uma máquina virtual Linux, precisa de um servidor de destino principal do Linux. Saiba como instalar um."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 11/22/2017
ms.author: rajanaki
ms.openlocfilehash: 7b2416617696e1df30b08f039ab39bfe7b57e093
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="install-a-linux-master-target-server"></a>Instalar um servidor de destino principal do Linux
Após a ativação pós-falha de máquinas virtuais, pode efetuar a cópia as máquinas virtuais para o site no local. Para falhar novamente, terá de voltar a proteger a máquina virtual do Azure para o site no local. Para que este processo, terá de um servidor de destino principal no local para receber o tráfego. 

Se a máquina virtual protegida é uma máquina virtual do Windows, em seguida, é necessário um destino principal do Windows. Para uma máquina virtual do Linux, tem um destino principal do Linux. Leia os seguintes passos para saber como criar e instalar um destino principal do Linux.

> [!IMPORTANT]
> Começando com a versão do 9.10.0 servidor de destino principal, o servidor de destino principal mais recente pode ser instalado apenas num servidor Ubuntu 16.04. Novas instalações não são permitidas em CentOS6.6 servidores. No entanto, pode continuar a atualizar os servidores de destino mestre antigo utilizando o 9.10.0 versão.

## <a name="overview"></a>Descrição geral
Este artigo fornece instruções sobre como instalar um destino principal do Linux.

Publique comentários ou perguntas no final deste artigo ou no [fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Pré-requisitos

* Para escolher o anfitrião no qual pretenda implementar o destino principal, determine se a reativação pós-falha vai ser para uma máquina virtual no local ou para uma nova máquina virtual. 
    * Para uma máquina virtual existente, o anfitrião de destino principal deve ter acesso para os arquivos de dados da máquina virtual.
    * Se a máquina virtual no local não existe, é criada a máquina virtual de reativação pós-falha no mesmo anfitrião, como o destino principal. Pode escolher qualquer anfitrião ESXi para instalar o destino principal.
* O destino principal deve ser numa rede que possa comunicar com o servidor de processos e o servidor de configuração.
* A versão do destino principal tem de ser igual ou anterior que as versões do servidor de processos e o servidor de configuração. Por exemplo, se a versão do servidor de configuração é 9.4, a versão do destino principal pode ser 9.4 ou 9.3 mas não 9.5.
* O destino principal só pode ser uma máquina virtual VMware e não um servidor físico.

## <a name="create-the-master-target-according-to-the-sizing-guidelines"></a>Criar o destino principal, de acordo com as diretrizes de dimensionamento

Crie o destino principal de acordo com as seguintes diretrizes de dimensionamento:
- **RAM**: 6 GB ou mais
- **Tamanho do disco de SO**: 100 GB ou mais (para instalar CentOS6.6)
- **Tamanho de disco adicional para a unidade de retenção**: 1 TB
- **Núcleos de CPU**: 4 núcleos ou mais

As seguintes kernels Ubuntu suportados são suportados.


|Série de kernel  |Suporta até  |
|---------|---------|
|4.4      |4.4.0-81-Generic         |
|4.8      |4.8.0-56-Generic         |
|4.10     |4.10.0-24-Generic        |


## <a name="deploy-the-master-target-server"></a>Implementar o servidor de destino mestre

### <a name="install-ubuntu-16042-minimal"></a>Instalar Ubuntu 16.04.2 mínima

Efetuar os seguintes passos para instalar o sistema de operativo de 64 bits do Ubuntu 16.04.2.

**Passo 1:** Ir para o [transferir ligação](https://www.ubuntu.com/download/server/thank-you?version=16.04.2&architecture=amd64) e escolha o mais próximo Espelha dos qual transferir um ficheiro Ubuntu 16.04.2 mínimo 64-bit ISO.

Manter um ficheiro Ubuntu 16.04.2 mínimo 64-bit ISO na unidade de DVD e iniciar o sistema.

**Passo 2:** selecione **inglês** como linguagem preferencial e, em seguida, selecione **Enter**.

![Selecione uma Linguagem](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image1.png)

**Passo 3:** selecione **instalar Ubuntu Server**e, em seguida, selecione **Enter**.

![Selecione instalar Ubuntu Server](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image2.png)

**Passo 4:** selecione **inglês** como linguagem preferencial e, em seguida, selecione **Enter**.

![Selecione inglês como o idioma preferencial](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image3.png)

**Passo 5:** selecionar a opção adequada do **fuso horário** lista de opções e, em seguida, selecione **Enter**.

![Selecione o fuso horário correto](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image4.png)

**Passo 6:** selecione **não** (opção predefinida) e, em seguida, selecione **Enter**.


![Configurar o teclado](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image5.png)

**Passo 7:** selecione **inglês (EUA)** como o país/região de origem para o teclado e, em seguida, selecione **Enter**.

![Selecione E.U.A. como o país/região de origem](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image6.png)

**Passo 8:** selecione **inglês (EUA)** como o esquema do teclado e, em seguida, selecione **Enter**.

![Selecione inglês como o esquema do teclado](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image7.png)

**Passo 9:** introduza o nome de anfitrião para o servidor no **Hostname** caixa e, em seguida, selecione **continuar**.

![Introduza o nome de anfitrião do servidor](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image8.png)

**Passo 10:** para criar uma conta de utilizador, introduza o nome de utilizador e, em seguida, selecione **continuar**.

![Criar uma conta de utilizador](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image9.png)

**Passo 11:** introduza a palavra-passe para a nova conta de utilizador e, em seguida, selecione **continuar**.

![Introduza a palavra-passe](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image10.png)

**Passo 12:** confirme a palavra-passe para o novo utilizador e, em seguida, selecione **continuar**.

![Confirme as palavras-passe](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image11.png)

**Passo 13:** selecione **não** (opção predefinida) e, em seguida, selecione **Enter**.

![Configurar utilizadores e palavras-passe](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image12.png)

**Passo 14:** se o fuso horário que é apresentado está correto, selecione **Sim** (opção predefinida) e, em seguida, selecione **Enter**.

Para reconfigurar o seu fuso horário, selecione **não**.

![Configure o relógio](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image13.png)

**Passo 15:** entre as opções de método de criação de partições, selecione **orientado - a utilizar o disco completo**e, em seguida, selecione **Enter**.

![Selecione a opção de método de criação de partições](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image14.png)

**Passo 16:** selecione o disco adequado do **disco selecione a partição** opções e, em seguida, selecione **Enter**.


![Selecione o disco](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image15.png)

**Passo 17:** selecione **Sim** ao escrever alterações para o disco e, em seguida, selecione **Enter**.

![Escrever as alterações no disco](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image16.png)

**Passo 18:** selecione a opção predefinida, selecione **continuar**e, em seguida, selecione **Enter**.

![Selecione a opção predefinida](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image17.png)

**Passo 19:** selecionar a opção adequada para a gestão de atualizações no seu sistema e, em seguida, selecione **Enter**.

![Selecione como gerir atualizações](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image18.png)

> [!WARNING]
> Porque o servidor de destino principal do Azure Site Recovery requer uma versão muito específica do Ubuntu, terá de se certificar de que as atualizações estão desativadas para a máquina virtual de kernel. Se estes estiverem ativadas, em seguida, quaisquer atualizações regulares fazer com que o servidor de destino mestre para avaria. Certifique-se de que seleciona o **sem as atualizações automáticas** opção.


**Passo 20:** Selecionar opções predefinidas. Se pretender openSSH para estabelecer a ligação SSH, selecione o **OpenSSH servidor** opção e, em seguida, selecione **continuar**.

![Selecione o software](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image19.png)

**Passo 21:** selecione **Sim**e, em seguida, selecione **Enter**.

![O carregador de arranque GRUB de instalação](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image20.png)

**Passo 22:** selecione o dispositivo adequado para a instalação do carregador de arranque (preferencialmente, **/dev/sda**) e, em seguida, selecione **Enter**.

![Selecione um dispositivo para a instalação do carregador de arranque](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image21.png)

**Passo 23:** selecione **continuar**e, em seguida, selecione **Enter** para concluir a instalação.

![Concluir a instalação](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image22.png)

Após a conclusão da instalação, inicie sessão para a VM com as novas credenciais do utilizador. (Consulte **passo 10** para obter mais informações.)

Os passos descritos na seguinte captura de ecrã para definir a palavra-passe de utilizador de raiz. Em seguida, inicie sessão como utilizador raiz.

![Definir a palavra-passe do utilizador raiz](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image23.png)


### <a name="prepare-the-machine-for-configuration-as-a-master-target-server"></a>Preparar a máquina para configuração como um servidor de destino mestre
Em seguida, prepare a máquina para configuração como um servidor de destino principal.

Para obter o ID para cada disco de rígido SCSI de uma máquina virtual Linux, ative o **disco. EnableUUID = TRUE** parâmetro.

Para ativar este parâmetro, siga os passos seguintes:

1. Encerre a máquina virtual.

2. Clique com o botão direito a entrada para a máquina virtual no painel esquerdo e, em seguida, selecione **editar definições de**.

3. Selecione o **opções** separador.

4. No painel esquerdo, selecione **avançadas** > **geral**e, em seguida, selecione o **parâmetros de configuração** botão na parte inferior direita do ecrã.

    ![Separador Opções](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

    O **parâmetros de configuração** opção não está disponível quando a máquina está em execução. Para tornar este separador ativa, encerre a máquina virtual.

5. Ver se uma linha com **disco. EnableUUID** já existe.

    - Se o valor existe e está definido como **falso**, altere o valor para **verdadeiro**. (Os valores não são maiúsculas e minúsculas.)

    - Se o valor existe e está definido como **verdadeiro**, selecione **Cancelar**.

    - Se o valor não existe, selecione **linha adicionar**.

    - A coluna de nome, adicionar **disco. EnableUUID**e, em seguida, defina o valor **verdadeiro**.

    ![A verificar se o disco. EnableUUID já existe](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="disable-kernel-upgrades"></a>Desativar as atualizações de kernel

Servidor de destino principal do Azure Site Recovery requer uma versão específica do Ubuntu, certifique-se de que as atualizações de kernel estão desativadas para a máquina virtual.

Se as atualizações de kernel estiverem ativadas, em seguida, quaisquer atualizações regulares fazer com que o servidor de destino mestre para avaria.

#### <a name="download-and-install-additional-packages"></a>Transferir e instalar pacotes adicionais

> [!NOTE]
> Certifique-se de que tem ligação à Internet para transferir e instalar pacotes adicionais. Se não tiver conectividade à Internet, tem de localizar estes pacotes RPM e instalá-los manualmente.

```
apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx
```

### <a name="get-the-installer-for-setup"></a>Obter o instalador para a configuração

Se o destino principal tiver conectividade à Internet, pode utilizar os seguintes passos para transferir o instalador. Caso contrário, pode copiar o installer do servidor de processos e, em seguida, instalá-lo.

#### <a name="download-the-master-target-installation-packages"></a>Transferir os pacotes de instalação de destino mestre

[Transferir o bits de instalação de destino principal do Linux mais recente](https://aka.ms/latestlinuxmobsvc).

Para transferi-lo utilizando o Linux, escreva:

```
wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz
```

Certifique-se de que transfira e deszipe o instalador do seu diretório raiz. Se deszipe a **usr/Local**, a instalação falhará.


#### <a name="access-the-installer-from-the-process-server"></a>O instalador de acesso do servidor de processos

1. No servidor de processos, aceda a **C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

2. Copie o ficheiro de instalador necessário do servidor de processos e guarde-o como **latestlinuxmobsvc.tar.gz** no seu diretório raiz.


### <a name="apply-custom-configuration-changes"></a>Aplicar alterações de configuração personalizada

Para aplicar alterações de configuração personalizada, utilize os seguintes passos:


1. Execute o seguinte comando para untar o binário.
    ```
    tar -zxvf latestlinuxmobsvc.tar.gz
    ```
    ![Captura de ecrã do comando para executar](./media/site-recovery-how-to-install-linux-master-target/image16.png)

2. Execute o seguinte comando para dar permissão.
    ```
    chmod 755 ./ApplyCustomChanges.sh
    ```

3. Execute o seguinte comando para executar o script.
    ```
    ./ApplyCustomChanges.sh
    ```
> [!NOTE]
> Execute o script apenas uma vez no servidor. Encerre o servidor. Em seguida, reinicie o servidor depois de adicionar um disco, conforme descrito na secção seguinte.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Adicionar um disco de retenção para as máquinas de destino principal do Linux

Utilize os seguintes passos para criar um disco de retenção:

1. Anexe um novo disco de 1 TB à máquina virtual de destino principal do Linux e, em seguida, iniciar a máquina.

2. Utilize o **multipath -odas** comando para obter o ID de multipath do disco de retenção.

    ```
    multipath -ll
    ```
    ![O ID do disco de retenção multipath](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. Formatar o disco e, em seguida, crie um sistema de ficheiros na unidade de novo.

    ```
    mkfs.ext4 /dev/mapper/<Retention disk's multipath id>
    ```
    ![Criar um sistema de ficheiros na unidade](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. Depois de criar o sistema de ficheiros, Monte o disco de retenção.
    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```
    ![Montar o disco de retenção](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. Criar o **fstab** entrada para montar a unidade de retenção, sempre que o sistema é iniciado.
    ```
    vi /etc/fstab
    ```
    Selecione **inserir** para começar a editar o ficheiro. Criar uma nova linha e, em seguida, insira o seguinte texto. Edite o multipath um ID de disco com base no ID do multipath realçado do comando anterior.

    **/Dev/mapeador/ <Retention disks multipath id> /mnt/retenção ext4 RW novos 0 0**

    Selecione **Esc**e, em seguida, escreva **: wq** (escrever e sair) para fechar a janela do editor.

### <a name="install-the-master-target"></a>Instalar o destino principal

> [!IMPORTANT]
> A versão do servidor de destino principal tem de ser igual ou anterior que as versões do servidor de processos e o servidor de configuração. Se esta condição não for cumprida, reproteção for bem sucedida, mas a replicação falha.


> [!NOTE]
> Antes de instalar o servidor de destino mestre, verifique se o **etc/anfitriões** ficheiro na máquina virtual contém entradas que mapeiam o nome do anfitrião local para os endereços IP que estão associados a todos os adaptadores de rede.

1. Copie o frase de acesso de **C:\ProgramData\Microsoft do Azure Site Recovery\private\connection.passphrase** no servidor de configuração. Em seguida, guarde-o como **passphrase.txt** no mesmo diretório local executando o seguinte comando:

    ```
    echo <passphrase> >passphrase.txt
    ```
    Exemplo: 
    
    ```
    echo itUx70I47uxDuUVY >passphrase.txt
    ```

2. Tenha em atenção o endereço IP do servidor de configuração. Terá no próximo passo.

3. Execute o seguinte comando para instalar o servidor de destino principal e registar o servidor com o servidor de configuração.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Exemplo: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

    Aguarde pela conclusão do script. Se o destino principal regista com êxito, o destino principal está listado no **infraestrutura de recuperação de Site** página do portal.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Instalar o destino principal utilizando a instalação interativa

1. Execute o seguinte comando para instalar o destino principal. Para a função de agente, escolher **destino mestre**.

    ```
    ./install
    ```

2. Escolha a localização predefinida para a instalação e, em seguida, selecione **Enter** para continuar.

    ![Escolher uma localização predefinida para a instalação de destino mestre](./media/site-recovery-how-to-install-linux-master-target/image17.png)

Após a conclusão da instalação, registe o servidor de configuração utilizando a linha de comandos.

1. Tenha em atenção o endereço IP do servidor de configuração. Terá no próximo passo.

2. Execute o seguinte comando para instalar o servidor de destino principal e registar o servidor com o servidor de configuração.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Exemplo: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

   Aguarde pela conclusão do script. Se o destino principal está registado com êxito, o destino principal está listado no **infraestrutura de recuperação de Site** página do portal.


### <a name="upgrade-the-master-target"></a>Atualizar o destino principal

Execute o instalador. Deteta automaticamente que o agente está instalado no destino principal. Para atualizar, selecione **Y**.  Depois do programa de configuração foi concluído, verifique a versão do destino principal instalado utilizando o seguinte comando:

    ```
    cat /usr/local/.vx_version
    ```

Pode ver que o **versão** campo indica o número de versão de destino principal.

### <a name="install-vmware-tools-on-the-master-target-server"></a>Instalar as ferramentas do VMware no servidor de destino mestre

Tem de instalar as ferramentas do VMware no destino principal para que possa detetar os arquivos de dados. Se as ferramentas não estiverem instaladas, o ecrã de reproteção não está listado nos arquivos de dados. Após a instalação das ferramentas VMware, terá de reiniciar.

## <a name="next-steps"></a>Passos seguintes
Depois de concluída a instalação e o registo do destino mestre, pode ver o destino principal que aparecem no **destino mestre** secção **infraestrutura de recuperação de Site**, sob a configuração Descrição geral do servidor.

Agora pode continuar com [só](site-recovery-how-to-reprotect.md), seguido de reativação pós-falha.

## <a name="common-issues"></a>Problemas comuns

* Certifique-se de que não ative vMotion de armazenamento de quaisquer componentes de gestão tais como um destino principal. Se o destino principal move após uma reproteção com êxito, os discos da máquina virtual (VMDKs) não podem ser desligados. Neste caso, a reativação pós-falha falha.

* O destino principal não deve ter todos os instantâneos da máquina virtual. Se existirem instantâneos, irá falhar a reativação pós-falha.

* Devido a algumas configurações de NIC personalizadas em alguns clientes, a interface de rede estiver desativada durante o arranque e não é possível inicializar o agente de destino principal. Certifique-se de que as seguintes propriedades estão corretamente definidas. Verifique estas propriedades de Ethernet de cartão /etc/sysconfig/network-scripts/ifcfg do ficheiro-eth *.
    * BOOTPROTO = dhcp
    * ONBOOT = yes
