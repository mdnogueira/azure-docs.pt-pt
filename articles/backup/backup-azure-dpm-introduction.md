---
title: "Utilizar o DPM para cópia de segurança de cargas de trabalho para o portal do Azure | Microsoft Docs"
description: "Uma introdução à cópia de segurança de servidores do DPM utilizando o serviço de cópia de segurança do Azure"
services: backup
documentationcenter: 
author: adigan
manager: nkolli
editor: 
keywords: "System Center Data Protection Manager, o data protection manager, a cópia de segurança do dpm"
ms.assetid: c8c322cf-f5eb-422c-a34c-04a4801bfec7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: adigan;giridham;jimpark;markgal;trinadhk
ms.openlocfilehash: 41eed9c44a226817da9ee5f324e62902bc23754c
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Preparar a criação de cópias de segurança de cargas de trabalho para o Azure com o DPM
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Servidor do Backup do Azure (clássica)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM (clássica)](backup-azure-dpm-introduction-classic.md)
>
>

Este artigo fornece uma introdução à utilização do Microsoft Azure Backup para proteger os seus servidores do System Center Data Protection Manager (DPM) e cargas de trabalho. Leia-lo, irá compreender:

* Como funciona a cópia de segurança do Azure DPM server
* Os pré-requisitos para alcançar uma experiência de cópia de segurança uniforme
* Foram encontrados típicos erros e como lidar com os mesmos
* Cenários suportados

> [!NOTE]
> O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo fornece as informações e procedimentos para restaurar VMs implementadas utilizando o modelo do Resource Manager.
>
>

[O System Center DPM](https://docs.microsoft.com/en-us/system-center/dpm/dpm-overview) efetua cópias de segurança de ficheiros e dados de aplicações. Podem encontrar mais informações sobre cargas de trabalho suportadas [aqui](https://docs.microsoft.com/en-us/system-center/dpm/dpm-protection-matrix). Dados de cópia de segurança do DPM podem ser armazenados na banda, disco ou uma cópia de segurança para o Azure com cópia de segurança do Microsoft Azure. O DPM interage com o Backup do Azure da seguinte forma:

* **DPM implementado como uma máquina virtual do servidor ou no local física** — se o DPM é implementado como um servidor físico ou como uma máquina de virtual de Hyper-V no local que pode fazer cópias de segurança dados para um cofre dos serviços de recuperação para além do disco e banda cópia de segurança.
* **DPM implementado como uma máquina virtual do Azure** — do System Center 2012 R2 com Update 3, o DPM pode ser implementado como uma máquina virtual do Azure. Se o DPM for implementado como uma máquina virtual do Azure, que pode fazer uma cópia dos dados nos discos do Azure anexados à máquina virtual do DPM Azure ou pode descarregar o armazenamento de dados através de cópias de até um cofre dos serviços de recuperação.

## <a name="why-backup-from-dpm-to-azure"></a>Por que motivo de cópia de segurança do DPM para o Azure?
As vantagens empresariais do utilizando a cópia de segurança do Azure para cópia de segurança de servidores do DPM incluem:

* Para implementação do DPM no local, pode utilizar o Azure como uma alternativa à implementação de longa duração em banda.
* Para implementações do DPM no Azure, cópia de segurança do Azure permite-lhe descarregar o armazenamento do disco do Azure, permitindo-lhe aumentar verticalmente ao armazenar os dados antigos no Cofre de serviços de recuperação e novos dados em disco.

## <a name="prerequisites"></a>Pré-requisitos
Prepare a cópia de segurança do Azure para cópia de segurança de dados do DPM da seguinte forma:

1. **Criar um cofre dos serviços de recuperação** — criar um cofre, no portal do Azure.
2. **Transferir as credenciais do cofre** — transferir as credenciais que utiliza para registar o servidor do DPM para o Cofre de serviços de recuperação.
3. **Instalar o agente de cópia de segurança do Azure** — da cópia de segurança do Azure, instale o agente em cada servidor do DPM.
4. **Registar o servidor** — registar o servidor do DPM para o Cofre de serviços de recuperação.

## <a name="key-definitions"></a>Definições-chave
Seguem-se algumas definições de chaves para cópia de segurança do Azure para o DPM:

1. **Credenciais do cofre** — as credenciais do cofre são necessárias para autenticar a máquina para enviar dados de cópia de segurança para um cofre identificado no serviço de cópia de segurança do Azure. Este pode ser transferido a partir do cofre e é válido para 48hrs.
2. **Frase de acesso** — frase de acesso é utilizado para encriptar as cópias de segurança para a nuvem. Guarde o ficheiro numa localização segura conforme necessário durante uma operação de recuperação.
3. **Segurança PIN** — se tiver ativado o [definições de segurança](https://docs.microsoft.com/en-us/azure/backup/backup-azure-security-feature) do cofre, de segurança de PIN é necessário para efetuar operações de cópia de segurança críticas. Esta autenticação multifator adiciona outra camada de segurança. 
4. **Pasta de recuperação** — é o frase de acesso que as cópias de segurança da nuvem estão temporariamente transferidas para durante as recuperações de nuvem. O tamanho de aproximadamente deve ser igual ao tamanho dos itens de cópia de segurança que pretende recuperar em paralelo.


### <a name="1-create-a-recovery-services-vault"></a>1. Criar um cofre dos serviços de recuperação
Para criar um cofre dos serviços de recuperação:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu Hub, clique em **Procurar** e na lista de recursos, escreva **Serviços de Recuperação**. À medida que começa a escrever, irá filtrar a lista com base na sua entrada. Clique em **Cofre dos Serviços de Recuperação**.

    ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-azure-dpm-introduction/open-recovery-services-vault.png)

    É apresentada a lista de cofres dos serviços de recuperação.
3. No menu **Cofres dos Serviços de Recuperação**, clique em **Adicionar**.

    ![Passo 2 da Criação do Cofre dos Serviços de Recuperação](./media/backup-azure-dpm-introduction/rs-vault-menu.png)

    O painel do cofre dos Serviços de Recuperação Cofre abre-se, pedindo-lhe que forneça um **Nome**, **Subscrição**, **Grupo de recursos** e **Localização**.

    ![Passo 5 da Criação do cofre dos Serviços de Recuperação](./media/backup-azure-dpm-introduction/rs-vault-attributes.png)
4. Para o **Nome**, introduza um nome amigável para identificar o cofre. O nome tem de ser exclusivo para a subscrição do Azure. Escreva um nome que contenha entre 2 e 50 carateres. Tem de começar com uma letra e pode conter apenas letras, números e hífenes.
5. Clique em **Subscrição** para ver a lista de subscrições disponíveis. Se não tiver a certeza de que subscrição utilizar, utilize a subscrição predefinida (ou sugerida). Apenas haverá várias escolhas se a sua conta organizacional estiver associada a várias subscrições do Azure.
6. Clique em **Grupo de recursos** para ver a lista de Grupos de recursos disponíveis ou clique em **Novo** para criar um novo Grupo de recursos. Para mais informações mais completas sobre os grupos de Recursos, veja [Descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
7. Clique em **Localização** para selecionar a região geográfica do cofre.
8. Clique em **Criar**. Pode demorar algum tempo até que o cofre dos Serviços de Recuperação seja criado. Monitorize as notificações de estado na área superior direita no portal.
   Assim que o Cofre for criado, abre-se no portal.

### <a name="set-storage-replication"></a>Definir Replicação de Armazenamento
A opção de replicação de armazenamento permite-lhe escolher entre o armazenamento georredundante e o armazenamento localmente redundante. Por predefinição, o seu cofre tem um armazenamento georredundante. Deixe a opção definida para armazenamento georredundante se for a sua cópia de segurança primária. Escolha armazenamento localmente redundante se pretende uma opção mais barata que não é tão durável. Leia mais sobre as opções de armazenamento [georredundante](../storage/common/storage-redundancy.md#geo-redundant-storage) e [localmente redundante](../storage/common/storage-redundancy.md#locally-redundant-storage) na [descrição geral da replicação de Armazenamento do Azure](../storage/common/storage-redundancy.md).

Para editar a definição de replicação de armazenamento:

1. Selecione o cofre para abrir o dashboard do cofre e o painel de Definições. Se o painel **Definições** não abrir, clique em **Todas as definições** no dashboard do cofre.
2. No painel **Definições**, clique em **Infraestrutura de Cópia de Segurança** > **Configuração de Cópia de Segurança** para abrir o painel **Configuração de Cópia de Segurança**. No painel **Configuração de Cópia de Segurança**, escolha a opção de replicação de armazenamento para o cofre.

    ![Lista de cofres de cópia de segurança](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Após escolher a opção de armazenamento para o cofre, está pronto para associar a VM com o cofre. Para começar a associação, detete e registe as máquinas virtuais do Azure.

### <a name="2-download-vault-credentials"></a>2. Transferir as credenciais do cofre
O ficheiro de credenciais do cofre é um certificado gerado pelo portal para cada cofre de cópia de segurança. O portal, em seguida, carrega a chave pública para o Access Control Service (ACS). A chave privada do certificado é disponibilizada para o utilizador como parte do fluxo de trabalho que está indicado como uma entrada no fluxo de trabalho de registo da máquina. Isto autentica a máquina para enviar dados de cópia de segurança para um cofre identificado no serviço de cópia de segurança do Azure.

As credenciais do cofre são utilizadas apenas durante o fluxo de trabalho do registo. É da responsabilidade do utilizador para se certificar de que o ficheiro de credenciais do Cofre não fiquem comprometido. Se ficarem às mãos de qualquer utilizador não autorizado, o ficheiro de credenciais do cofre pode ser utilizado para registar outras máquinas contra o mesmo cofre. No entanto, como os dados de cópia de segurança são encriptados utilizando uma frase de acesso que pertence ao cliente, os dados de cópia de segurança existentes não podem ficar comprometidos. Para atenuar esta preocupação, as credenciais do cofre estão definidas para expirar em 48hrs. Pode transferir as credenciais do cofre dos serviços de recuperação qualquer número de vezes, mas apenas o ficheiro de credenciais de cofre mais recente é aplicável durante o fluxo de trabalho do registo.

O ficheiro de credenciais do Cofre é transferido através de um canal seguro do portal do Azure. O serviço de cópia de segurança do Azure não tem conhecimento da chave privada do certificado e a chave privada não é continuada no portal ou o serviço. Utilize os seguintes passos para transferir o ficheiro de credenciais do cofre para um computador local.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Abra cofre dos serviços de recuperação para o qual a qual pretende registar o DPM do computador.
3. Abre o painel de definições de cópia de segurança por predefinição. Se estiver fechado, clique em **definições** no dashboard do cofre para abrir o painel de definições. No painel Definições, clique em **propriedades**.

    ![Abrir painel do cofre](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
4. Na página de propriedades, clique em **transferir** em **credenciais de cópia de segurança**. O portal gera o ficheiro de credenciais do cofre, que é disponibilizado para transferência.

    ![Transferência](./media/backup-azure-dpm-introduction/vault-credentials.png)

O portal irá gerar uma credencial de cofre utilizando uma combinação de nome do cofre e a data atual. Clique em **guardar** para transferir as credenciais do cofre para a pasta de transferências da conta local, ou selecione guardar como no menu Guardar para especificar uma localização para as credenciais do cofre. Este irá demorar até um minuto para o ficheiro a ser gerado.

### <a name="note"></a>Nota
* Certifique-se de que o ficheiro de credenciais do Cofre é guardado numa localização que pode ser acedida a partir do seu computador. Se esta está armazenada numa partilha de ficheiros/SMB, verifique as permissões de acesso.
* O ficheiro de credenciais do Cofre é utilizado apenas durante o fluxo de trabalho do registo.
* O ficheiro de credenciais do cofre expira após 48hrs e pode ser transferido a partir do portal.

### <a name="3-install-backup-agent"></a>3. Instalar o agente de cópia de segurança
Depois de criar o Cofre de cópia de segurança do Azure, deve ser instalado um agente em cada uma das suas máquinas do Windows (Windows Server, cliente Windows, o servidor do System Center Data Protection Manager ou máquina do servidor de cópia de segurança do Azure) que permite fazer cópias de segurança de dados e aplicações para o Azure .

1. Abra cofre dos serviços de recuperação para o qual a qual pretende registar o DPM do computador.
2. Abre o painel de definições de cópia de segurança por predefinição. Se estiver fechado, clique em **definições** para abrir o painel de definições. No painel Definições, clique em **propriedades**.

    ![Abrir painel do cofre](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Na página Definições, clique em **transferir** em **Azure Backup Agent**.

    ![Transferência](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

   Depois do agente é transferido, faça duplo clique MARSAgentInstaller.exe para iniciar a instalação do agente do Backup do Azure. Escolha a pasta de instalação e a pasta scratch necessário para o agente. A localização da cache especificada tem de ter espaço livre que é, pelo menos, 5% dos dados de cópia de segurança.
4. Se utilizar um servidor proxy para ligar à internet, o **configuração de Proxy** ecrã, introduza os detalhes do servidor proxy. Se utilizar um proxy autenticado, introduza os detalhes de nome e palavra-passe do utilizador neste ecrã.
5. O agente do Backup do Azure instala .NET Framework 4.5 e o Windows PowerShell (se ainda não estiver disponível) para concluir a instalação.
6. Assim que o agente estiver instalado, **fechar** a janela.

   ![Fechar](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)
7. Para **registar o servidor de DPM** para o cofre, no **gestão** separador, clique em **Online**. Em seguida, selecione **registar**. É aberto o Assistente de configuração do registo.
8. Se utilizar um servidor proxy para ligar à internet, o **configuração de Proxy** ecrã, introduza os detalhes do servidor proxy. Se utilizar um proxy autenticado, introduza os detalhes de nome e palavra-passe do utilizador neste ecrã.

    ![Configuração de proxy](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. No ecrã de credenciais de cofre, procure e selecione o ficheiro de credenciais do cofre que foi anteriormente transferido.

    ![Credenciais do Cofre](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

    O ficheiro de credenciais do cofre só é válido para 48 horas (após a transferência do portal). Se encontrar qualquer erro este ecrã (por exemplo, "as credenciais do cofre ficheiro fornecido expirou"), inicie sessão no portal do Azure e transfira que as credenciais do cofre ficheiro novamente.

    Certifique-se de que o ficheiro de credenciais do Cofre está disponível numa localização que pode ser acedida pela aplicação de configuração. Se ocorrerem erros relacionados com de acesso, copie o ficheiro de credenciais do cofre para uma localização temporária nesta máquina e repita a operação.

    Se ocorrer um erro de credenciais de cofre inválida (por exemplo, "Cofre inválidas credenciais fornecidas") o ficheiro está danificado ou não tiver as credenciais mais recentes associado o serviço de recuperação. Repita a operação depois de transferir um novo ficheiro de credenciais do cofre a partir do portal. Este erro normalmente é utilizado se o utilizador clica no **credenciais do Cofre de transferência** opção no portal do Azure, sucessivamente rápida. Neste caso, apenas o ficheiro de credenciais de cofre segundo é válido.
10. Para controlar a utilização de largura de banda de rede durante o trabalho e horas de descanso do **definição de limitação** ecrã, pode definir os limites de utilização de largura de banda e definir as horas de trabalho e de descanso.

    ![Definição de limitação](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)
11. No **recuperação pasta definição** ecrã, navegue para a pasta onde os ficheiros transferidos a partir do Azure será testada temporariamente.

    ![Definição de pasta de recuperação](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)
12. No **definição de encriptação** ecrã, pode gerar uma frase de acesso ou fornecer uma frase de acesso (mínimo de 16 carateres). Lembre-se guardar o frase de acesso numa localização segura.

    ![Encriptação](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Se o frase de acesso de perda ou esquecimento; Não pode ajudar a Microsoft recuperar os dados de cópia de segurança. O utilizador final possui a frase de acesso de encriptação e a Microsoft não têm visibilidade para a frase de acesso utilizada pelo utilizador final. Guarde o ficheiro numa localização segura conforme necessário durante uma operação de recuperação.
    >
    >
13. Assim que clicar no **registar** botão, a máquina está registada com êxito para o Cofre e está agora pronto para iniciar a cópia de segurança em Microsoft Azure.
14. Ao utilizar o Data Protection Manager, pode modificar as definições especificadas durante o fluxo de trabalho de registo clicando a **configurar** opção selecionando **Online** sob o **gestão**  Separador.

## <a name="requirements-and-limitations"></a>Requisitos (e limitações)
* O DPM pode estar em execução como um servidor físico ou uma máquina virtual de Hyper-V instalada no System Center 2012 SP1 ou System Center 2012 R2. Também pode executar como uma máquina virtual do Azure em execução no System Center 2012 R2 com, pelo menos, o DPM 2012 R2 Update Rollup 3 ou uma máquina virtual do Windows no VMWare, pelo menos, a ser executado no System Center 2012 R2 com Update Rollup 5.
* Se estiver a executar o DPM com o System Center 2012 SP1 deverá instalar Update Roll up 2 para o System Center Data Protection Manager SP1. Isto é necessário antes de poder instalar o agente de cópia de segurança do Azure.
* O servidor DPM deve ter o Windows PowerShell e .net Framework 4.5 instalados.
* O DPM pode criar cópias de segurança a maioria das cargas de trabalho para cópia de segurança do Azure. Para obter uma lista completa de que tem suportado consulte a cópia de segurança do Azure suporta itens abaixo.
* Não não possível recuperar os dados armazenados em cópia de segurança do Azure com a opção "copiar para banda".
* Irá precisar de uma conta do Azure com a funcionalidade de cópia de segurança do Azure ativada. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Leia sobre [preços da cópia de segurança do Azure](https://azure.microsoft.com/pricing/details/backup/).
* Utilizando a cópia de segurança do Azure requer que o agente de cópia de segurança do Azure ser instalada nos servidores que pretende criar cópias de segurança. Cada servidor tem de ter, pelo menos, 5% do tamanho dos dados que está a ser efetuados, disponível como armazenamento local livre. Por exemplo, a cópia de segurança de 100 GB de dados requer um mínimo de 5 GB de espaço livre na localização scratch.
* Dados serão armazenados no armazenamento do cofre do Azure. Não existe nenhum limite para a quantidade de dados que pode criar uma cópia de segurança uma cópia de segurança do Azure do cofre mas o tamanho de uma origem de dados (por exemplo uma máquina virtual ou a base de dados) não deve exceder 54400 GB.

Estes tipos de ficheiro são suportados para criar uma cópia de segurança do Azure:

* Encriptados (cópias de segurança completas só)
* Comprimidos (cópias de segurança incrementais suportadas)
* Dispersos (cópias de segurança incrementais suportadas)
* Comprimidos e dispersos (tratados como dispersos)

E estes não são suportadas:

* Não são suportados servidores em sistemas de ficheiros sensíveis a maiúsculas.
* Ligações diretas (ignorados)
* (Ignorados) de pontos de reanálise
* Encriptados e comprimidos (ignorados)
* Encriptados e dispersos (ignorados)
* Sequência comprimida
* Sequência dispersa

> [!NOTE]
> No System Center 2012 DPM com SP1, pode efetuar cópias de segurança segurança de cargas de trabalho protegidas pelo DPM no Azure utilizando a cópia de segurança do Microsoft Azure.
>
>
