---
title: "Utilizar o servidor de cópia de segurança do Azure para cópia de segurança de cargas de trabalho para o portal clássico do Azure | Microsoft Docs"
description: "Certifique-se de que o seu ambiente corretamente está preparado para efetuar cópias de segurança de cargas de trabalho utilizando o servidor de cópia de segurança do Azure"
services: backup
documentationcenter: 
author: pvrk
manager: shivamg
editor: 
keywords: "servidor do backup do Azure; Cofre de cópia de segurança"
ms.assetid: d86450e8-da63-4283-8fd7-2ee629fa14ab
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: masaran;trinadhk;pullabhk;markgal
ms.openlocfilehash: db19729f652430c80ad0a7af1b630c1c5a1561d3
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="preparing-to-back-up-workloads-using-azure-backup-server"></a>Preparar a criação de cópias de segurança de cargas de trabalho com o Azure Backup Server
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Servidor do Backup do Azure (clássica)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM (clássica)](backup-azure-dpm-introduction-classic.md)
>
>

Este artigo é sobre como preparar o ambiente para fazer cópias de segurança de cargas de trabalho utilizando o servidor de cópia de segurança do Azure. Com o servidor de cópia de segurança do Azure, pode proteger cargas de trabalho de aplicações como VMs de Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange e clientes Windows de uma única consola.

> [!WARNING]
> Servidor do Backup do Azure herda a funcionalidade do Data Protection Manager (DPM) para cópia de segurança da carga de trabalho. Encontrará apontadores para documentação do DPM para algumas dessas funcionalidades. No entanto servidor de cópia de segurança do Azure não fornecer proteção em banda ou integrar com o System Center.
>
>

## <a name="1-windows-server-machine"></a>1. Computador Windows Server
![step1](./media/backup-azure-microsoft-azure-backup/step1.png)

É o primeiro passo para obter o servidor de cópia de segurança do Azure, cópias de segurança e em execução para que o computador Windows Server.

| Localização | Requisitos mínimos | Instruções adicionais |
| --- | --- | --- |
| Azure |Máquina de virtual IaaS do Azure<br><br>A2 Padrão: 2 núcleos, 3.5GB de RAM |Pode começar com uma imagem de galeria simples do Windows Server 2012 R2 Datacenter. [Proteger cargas de trabalho de IaaS com o servidor de cópia de segurança do Azure (DPM)](https://technet.microsoft.com/library/jj852163.aspx) tem muitas nuances. Certifique-se de que lê o artigo completamente antes de implementar a máquina. |
| Local |VM de Hyper-V,<br> VM de VMWare<br> ou um anfitrião físico.<br><br>2 núcleos e 4GB de RAM |Pode eliminar a duplicados do armazenamento do DPM utiliza a eliminação de duplicados do Windows Server. Saiba mais sobre como [DPM e eliminação de duplicados](https://technet.microsoft.com/library/dn891438.aspx) funcionam em conjunto, quando implementados em VMs de Hyper-V. |

> [!NOTE]
> Recomenda-se que o servidor de cópia de segurança do Azure seja instalado num computador com o Windows Server 2012 R2 Datacenter. Muitos dos pré-requisitos descritos automaticamente com a versão mais recente do sistema operativo Windows.
>
>

Se pretender associar o servidor de cópia de segurança do Azure a um domínio, é recomendado que pode associa o servidor físico ou máquina virtual ao domínio antes de instalar o software de servidor de cópia de segurança do Azure. Mover um servidor de cópia de segurança do Azure para um novo domínio, após a implementação, é *não suportado*.

## <a name="2-backup-vault"></a>2. Cofre de cópia de segurança
![step2](./media/backup-azure-microsoft-azure-backup/step2.png)

Quer enviar dados de cópia de segurança para o Azure ou mantenha-o localmente, o servidor de cópia de segurança do Azure tem de ser registado para um cofre. Se for um novo utilizador de cópia de segurança do Azure e pretender utilizar o servidor de cópia de segurança do Azure, consulte a versão do portal do Azure deste artigo - [preparar a cópia de segurança de cargas de trabalho utilizando o servidor de cópia de segurança do Azure](backup-azure-microsoft-azure-backup.md).

> [!IMPORTANT]
> A partir de março de 2017, já não pode utilizar o portal clássico para criar cofres de Cópia de Segurança.
> Agora pode atualizar os cofres de Cópia de Segurança para cofres dos Serviços de Recuperação. Para obter detalhes, veja o artigo [Atualizar um cofre de Cópia de Segurança para um cofre dos Serviços de Recuperação](backup-azure-upgrade-backup-to-recovery-services.md). Recomendamos que atualize os cofres de Cópia de Segurança para cofres dos Serviços de Recuperação.<br/> Após 30 de Novembro de 2017, não poderá utilizar o PowerShell para criar cofres de cópia de segurança. **Até 30 de Novembro de 2017**:
>- Todos os cofres de Cópia e Segurança restantes serão atualizados automaticamente para cofres dos Serviços de Recuperação.
>- Não vai conseguir aceder aos dados de cópia de segurança no portal clássico. Em vez disso, utilize o portal do Azure para aceder aos dados de cópia de segurança em cofres dos Serviços de Recuperação.
>



## <a name="3-software-package"></a>3. Pacote de software
![passo 3](./media/backup-azure-microsoft-azure-backup/step3.png)

### <a name="downloading-the-software-package"></a>Transferir o pacote de software
Semelhante para as credenciais do cofre, pode transferir a cópia de segurança do Microsoft Azure para cargas de trabalho de aplicação do **página início rápido** do Cofre de cópia de segurança.

1. Clique em **para cargas de trabalho de aplicação (disco para disco para a nuvem)**. Isto leva-o para a página de centro de transferências a partir de onde o pacote de software pode ser transferido.

    ![Ecrã de boas-vindas cópia de segurança do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/dpm-venus1.png)
2. Clique em **Transferir**.

    ![1 do Centro de transferências](./media/backup-azure-microsoft-azure-backup/downloadcenter1.png)
3. Selecione todos os ficheiros e clique em **seguinte**. Transferir todos os ficheiros feitos página de transferência da cópia de segurança do Microsoft Azure e coloque todos os ficheiros na mesma pasta.
   ![1 do Centro de transferências](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Uma vez que o tamanho de transferência dos ficheiros em conjunto > 3G, um 10 Mbps transferi-ligação pode demorar até 60 minutos para a transferência concluir.

### <a name="extracting-the-software-package"></a>Extrair o pacote de software
Depois de transferir todos os ficheiros, clique em **MicrosoftAzureBackupInstaller.exe**. Isto iniciará a **Assistente de configuração de cópia de segurança do Microsoft Azure** para extrair os ficheiros de configuração para uma localização especificada por si. Continuar com o assistente e clique em de **extrair** botão para iniciar o processo de extração.

> [!WARNING]
> É necessário, pelo menos, 4GB de espaço livre para extrair os ficheiros de configuração.
>
>

![Assistente de configuração de cópia de segurança do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Depois do processo de extração completo, selecione a caixa para iniciar a raiz extraídos *setup.exe* para começar a instalar o servidor de cópia de segurança do Microsoft Azure e clique em de **concluir** botão.

### <a name="installing-the-software-package"></a>Instalar o pacote de software
1. Clique em **cópia de segurança do Microsoft Azure** para iniciar o Assistente de configuração.

    ![Assistente de configuração de cópia de segurança do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. No ecrã de boas-vindas, clique o **seguinte** botão. Isto leva-o para o *verificações de pré-requisitos* secção. Neste ecrã, clique em de **verifique** botão para determinar se tiverem sido cumpridos os pré-requisitos de hardware e software para o servidor de cópia de segurança do Azure. Se todos os pré-requisitos foram cumpridos com êxito, verá uma mensagem a indicar que o computador cumpre os requisitos. Clique em de **seguinte** botão.

    ![Verificação do servidor de cópia de segurança do Azure - boas-vindas e pré-requisitos](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Servidor de cópia de segurança do Microsoft Azure requer o SQL Server Standard e o pacote de instalação do servidor de cópia de segurança do Azure integra integrados nos binários do SQL Server adequados necessários. Ao iniciar com uma nova instalação do servidor de cópia de segurança do Azure, deve escolher a opção **instalar um novo instância do SQL Server com esta configuração** e clique em de **verificar e instalar** botão. Depois dos pré-requisitos estão instalados com êxito, clique em **seguinte**.

    ![Servidor de cópia de segurança do Azure - verificação do SQL Server](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Se ocorrer uma falha com uma recomendação para reiniciar a máquina, fazê-lo e clique em **verifique novamente**.

   > [!NOTE]
   > Servidor de cópia de segurança do Azure não irá funcionar com uma instância remota do SQL Server. A instância que está a ser utilizada pelo servidor de cópia de segurança do Azure tem de ser local.
   >
   >

4. Forneça uma localização para a instalação de ficheiros do servidor de cópia de segurança do Microsoft Azure e clique em **seguinte**.

    ![PreReq2 de cópia de segurança do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    A localização scratch é um requisito para cópia de segurança em do Azure. Certifique-se de que a localização de scratch é, pelo menos, 5% dos dados a ser planeados a cópia de segurança para a nuvem. Para a proteção de disco, discos separados tem de ser configurado depois de concluída a instalação. Para obter mais informações sobre agrupamentos de armazenamento, consulte [configurar agrupamentos de armazenamento e armazenamento em disco](https://technet.microsoft.com/library/hh758075.aspx).
5. Forneça uma palavra-passe segura para contas de utilizador locais restritas e clique em **seguinte**.

    ![PreReq2 de cópia de segurança do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Selecione se pretende utilizar *Microsoft Update* para procurar atualizações e clique em **seguinte**.

   > [!NOTE]
   > Recomenda-se ter o Windows Update redirecionar para o Microsoft Update oferece segurança e atualizações importantes para o Windows e outros produtos, como o servidor de cópia de segurança do Microsoft Azure.
   >
   >

    ![PreReq2 de cópia de segurança do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Reveja o *resumo de definições* e clique em **instalar**.

    ![PreReq2 de cópia de segurança do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. A instalação ocorre em fases. Na primeira fase, o Microsoft Azure Recovery Services Agent está instalado no servidor. O assistente também verifica a conectividade à Internet. Se a conectividade à Internet está disponível para poder continuar com a instalação, caso contrário, é necessário fornecer os detalhes do proxy para estabelecer ligação à Internet.

    O passo seguinte consiste em configurar o Microsoft Azure Recovery Services Agent. Como parte da configuração, terá de fornecer que tiver as credenciais do cofre para registar a máquina para o Cofre de cópia de segurança. Também irá fornecer uma frase de acesso para encriptar/desencriptar os dados enviados entre o Azure e o local. Automaticamente pode gerar uma frase de acesso ou fornecer a suas próprias mínimo frase de acesso de 16 caracteres. Continue com o assistente até que o agente tiver sido configurado.

    ![PreReq2 Serer de cópia de segurança do Azure](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Após a conclusão com êxito do registo do servidor de cópia de segurança do Microsoft Azure, o Assistente de configuração geral prossegue para a instalação e configuração do SQL Server e os componentes de servidor de cópia de segurança do Azure. Uma vez concluída a instalação de componentes do SQL Server, os componentes de servidor de cópia de segurança do Azure estão instalados.

    ![Servidor do Backup do Azure](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Quando concluir o passo de instalação, os ícones de ambiente de trabalho do produto irão ter sido criados, bem como. Apenas, faça duplo clique no ícone para iniciar o produto.

### <a name="add-backup-storage"></a>Adicionar armazenamento de cópia de segurança
A primeira cópia de segurança é mantida no armazenamento ligado à máquina do servidor de cópia de segurança do Azure. Para obter mais informações sobre a adição de discos, consulte [configurar agrupamentos de armazenamento e armazenamento em disco](https://technet.microsoft.com/library/hh758075.aspx).

> [!NOTE]
> Terá de adicionar o armazenamento de cópia de segurança mesmo que se pretende enviar dados para o Azure. A arquitetura atual do servidor de cópia de segurança do Azure, o Cofre de cópia de segurança do Azure mantém o *segundo* cópia dos dados enquanto o armazenamento local contém a cópia de segurança primeiro (e obrigatória).  
>
>

## <a name="4-network-connectivity"></a>4. Conectividade de rede
![step4](./media/backup-azure-microsoft-azure-backup/step4.png)

Servidor de cópia de segurança do Azure necessita de conectividade para o serviço de cópia de segurança do Azure para o produto a funcionar com êxito. Para validar se o computador tem conectividade para o Azure, utilize o ```Get-DPMCloudConnection``` commandlet na consola do PowerShell de servidor de cópia de segurança do Azure. Se a saída do commandlet for TRUE, em seguida, existe a conectividade, caso contrário, há sem conectividade.

Ao mesmo tempo, a subscrição do Azure tem de estar em bom estado de funcionamento. Para saber o estado da sua subscrição e geri-lo, inicie sessão para o [portal subscrição](https://account.windowsazure.com/Subscriptions).

Depois de saber o estado da conetividade do Azure e da subscrição do Azure, pode utilizar a tabela abaixo para descobrir o impacto sobre a funcionalidade de cópia de segurança/restauro fornecida.

| Estado de conectividade | Subscrição do Azure | Cópia de segurança do Azure | Cópia de segurança em disco | Restaurar a partir do Azure | Restaurar a partir do disco |
| --- | --- | --- | --- | --- | --- |
| Ligado |Ativa |Permitido |Permitido |Permitido |Permitido |
| Ligado |Expirou |Parada |Parada |Permitido |Permitido |
| Ligado |Desaprovisionada |Parada |Parada |Pontos de recuperação de paragem e o Azure eliminados |Parada |
| Conectividade perdida > 15 dias |Ativa |Parada |Parada |Permitido |Permitido |
| Conectividade perdida > 15 dias |Expirou |Parada |Parada |Permitido |Permitido |
| Conectividade perdida > 15 dias |Desaprovisionada |Parada |Parada |Pontos de recuperação de paragem e o Azure eliminados |Parada |

### <a name="recovering-from-loss-of-connectivity"></a>Recuperar a partir de perda de conectividade
Se tiver uma firewall ou um proxy que está a impedir o acesso ao Azure, terá de lista branca os seguintes endereços de domínio no perfil da firewall/proxy:

* www.msftncsi.com
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Depois de conectividade para o Azure foi restaurada para a máquina do servidor de cópia de segurança do Azure, as operações que podem ser efetuadas são determinadas pelo Estado de subscrição do Azure. A tabela acima tem os detalhes sobre as operações permitidos depois da máquina é "ligada".

### <a name="handling-subscription-states"></a>Processamento de Estados de subscrição
É possível efetuar uma subscrição do Azure de um *expirado* ou *Deprovisioned* estado para o *Active Directory* estado. No entanto isto tem algumas implicações sobre o comportamento do produto enquanto o estado não é *Active Directory*:

* A *Deprovisioned* subscrição perde a funcionalidade para o período em que é desaprovisionada. No ativar *Active Directory*, a funcionalidade de produto de cópia de segurança/restauro é revived. Os dados de cópia de segurança no disco local também podem ser obtidos se de que foi guardado com um período de retenção suficientemente grande. No entanto, os dados de cópia de segurança no Azure são irretrievably perdidos depois da subscrição introduz o *Deprovisioned* estado.
* Um *expirado* subscrição apenas perde a funcionalidade para até que foi efetuada *Active Directory* novamente. As cópias de segurança agendadas para o período de que a subscrição foi *expirado* não será executado.

## <a name="troubleshooting"></a>Resolução de problemas
Se o servidor de cópia de segurança do Microsoft Azure falhar com erros durante a fase de configuração (ou cópia de segurança ou restauro), consulte este [documento de códigos de erro](https://support.microsoft.com/kb/3041338) para obter mais informações.
Também pode consultar [perguntas mais frequentes relacionadas com a cópia de segurança do Azure](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Passos seguintes
Pode obter informações detalhadas [preparar o ambiente para o DPM](https://technet.microsoft.com/library/hh758176.aspx) no site Microsoft TechNet. Também contém informações sobre configurações suportadas em que servidor de cópia de segurança do Azure podem ser implementado e utilizado.

Pode utilizar estes artigos para obter uma compreensão mais aprofundada da proteção de carga de trabalho utilizando o servidor de cópia de segurança do Microsoft Azure.

* [Cópia de segurança do SQL Server](backup-azure-backup-sql.md)
* [Cópia de segurança do SharePoint server](backup-azure-backup-sharepoint.md)
* [Cópia de segurança do servidor alternativo](backup-azure-alternate-dpm-server.md)
