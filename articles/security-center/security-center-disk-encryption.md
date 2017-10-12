---
title: "Encriptar uma Máquina Virtual do Azure | Microsoft Docs"
description: "Este documento ajuda-o a encriptar uma Máquina Virtual do Azure depois de receber um alerta a partir do Centro de Segurança do Azure."
services: security, security-center
documentationcenter: na
author: TomShinder
manager: swadhwa
editor: 
ms.assetid: f6c28bc4-1f79-4352-89d0-03659b2fa2f5
ms.service: security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2017
ms.author: tomsh
ms.openlocfilehash: 8d39aafb0ab7b0e87afdf4d2f50f1e224b8d251f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="encrypt-an-azure-virtual-machine"></a>Encriptar uma Máquina Virtual do Azure
O Centro de Segurança do Azure alerta-o se tiver máquinas virtuais que não estão encriptadas. Estes alertas serão apresentados como de Gravidade Alta e a recomendação é de encriptar estas máquinas virtuais.

![Recomendação de encriptação de disco](./media/security-center-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> As informações neste documento aplicam-se à encriptação de máquinas virtuais sem utilizar uma Chave de Encriptação de Chave (que é necessária para criar cópias de máquinas virtuais utilizando o Azure Backup). Veja o artigo [Azure Disk Encryption para Máquinas Virtuais do Azure Windows e Linux](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption) para obter informações sobre como utilizar uma Chave de Encriptação de Chave para suportar o Azure Backup para Máquinas Virtuais do Azure encriptadas.
>
>

Para encriptar as Virtual Machines do Azure que tenham sido identificadas pelo Centro de Segurança do Azure como tendo necessidade de encriptação, recomendamos os seguintes passos:

* Instalar e configurar o Azure PowerShell. Esta operação permitirá a execução de comandos do PowerShell necessários para configurar os pré-requisitos necessários para encriptar Virtual Machines do Azure.
* Obter e executar o script do Azure PowerShell de Pré-requisitos do Azure Disk Encryption
* Encriptar as suas máquinas virtuais

O objetivo deste documento é permitir-lhe encriptar as suas máquinas virtuais, mesmo que tenha poucos ou nenhuns conhecimentos acerca do Azure PowerShell.
Este documento parte do princípio de que está a utilizar o Windows 10 como a máquina cliente a partir da qual irá configurar o Azure Disk Encryption.

Existem várias abordagens que podem ser utilizadas para configurar os pré-requisitos e para configurar a encriptação para Virtual Machines do Azure. Se já estiver bem familiarizado com o Azure PowerShell ou o CLI do Azure, pode preferir utilizar abordagens alternativas.

> [!NOTE]
> Para saber mais sobre abordagens alternativas para configurar a encriptação para máquinas virtuais do Azure, consulte [Azure Disk Encryption para Virtual Machines do Azure Windows e Linux](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).
>
>

## <a name="install-and-configure-azure-powershell"></a>Instalar e configurar o Azure PowerShell
Precisa da versão 1.2.1 ou superior do Azure PowerShell instalada no seu computador. O artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) contém todos os passos necessários para aprovisionar o computador de modo a trabalhar com o Azure PowerShell. A abordagem mais direta consiste em utilizar a abordagem de instalação do Instalador de Plataforma Web mencionada nesse artigo. Mesmo que já tenha instalado Azure PowerShell, instale-o novamente ao utilizar a abordagem do Instalador de Plataforma Web para que tenha a versão mais recente do Azure PowerShell.

## <a name="obtain-and-run-the-azure-disk-encryption-prerequisites-configuration-script"></a>Obter e executar o script de configuração de pré-requisitos do Azure Disk Encryption
O Script de Configuração de Pré-requisitos do Azure Disk Encryption irá configurar todos os pré-requisitos necessários para encriptar as suas Virtual Machines do Azure.

1. Aceda à página do GitHub que possui o [Script de Configuração de Pré-requisitos do Azure Disk Encryption](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
2. Na página do GibHub, clique no botão **Raw**.
3. Utilize **CTRL-A** para selecionar todo o texto na página e, em seguida, utilize **CTRL-C** para copiar todo o texto da página para a área de transferência.
4. Abra o **Bloco de notas** e cole o texto copiado no Bloco de notas.
5. Crie uma nova pasta na sua unidade C: com o nome **AzureADEScript**.
6. Guarde o ficheiro do Bloco de notas – clique em **Ficheiro** e, em seguida, clique em **Guardar Como**. Na caixa de texto Nome de ficheiro, introduza **“ADEPrereqScript.ps1”** e clique em **Guardar**. (certifique-se de que coloca as aspas à volta o nome, caso contrário, irá guardar o ficheiro com uma extensão de ficheiro .txt).

Agora que o conteúdo do script está guardado, abra o script no ISE do PowerShell:

1. No Menu Iniciar, clique em **Cortana**. Peça à **Cortana** “PowerShell” ao escrever **PowerShell** na caixa de texto de pesquisa da Cortana.
2. Clique com o botão direito do rato em **ISE do Windows PowerShell** e clique em **Executar como administrador**.
3. Na janela **Administrador: ISE do Windows PowerShell**, clique em **Ver** e, em seguida, clique em **Mostrar Painel de Script**.
4. Se vir o painel **Comandos** no lado direito da janela, clique no **“x”** no canto superior direito do painel de para fechá-lo. Se o texto for demasiado pequeno para ver, utilize **CTRL+Adicionar** (“Adicionar” é o sinal “+”). Se o texto for demasiado grande, utilize **CTRL+Subtrair** (Subtrair é o sinal “-”).
5. Clique em **Ficheiro** e, em seguida, clique em **Abrir**. Navegue para a pasta **C:\AzureADEScript** e o faça duplo clique no **ADEPrereqScript**.
6. O conteúdo do **ADEPrereqScript** deverá agora aparecer no ISE do PowerShell e é codificado por cores para o ajudar a ver mais facilmente diversos componentes, tais como comandos, parâmetros e variáveis.

Deverá agora verá algo semelhante à figura abaixo.

![Janela ISE do PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig2.png)

O painel superior é designado por “painel de script” e o painel inferior é designado por “consola”. Utilizaremos estes termos posteriormente neste artigo.

## <a name="run-the-azure-disk-encryption-prerequisites-powershell-command"></a>Executar o comando do PowerShell de pré-requisitos do Azure Disk Encryption
O script de Pré-requisitos do Azure Disk Encryption irá pedir-lhe as seguintes informações depois de iniciar o script:

* **Nome do Grupo de Recursos** – Nome do Grupo de Recursos no qual pretende colocar o Cofre de Chaves.  Será criado um novo Grupo de Recursos com o nome que introduziu se não existir já um com esse nome criado. Se já tiver um Grupo de Recursos que pretende utilizar nesta subscrição, introduza o nome desse Grupo de Recursos.
* **Nome do Cofre de Chaves** – Nome do Cofre de Chaves no qual vão ser colocadas chaves de encriptação. Será criado um novo Cofre de Chaves com este nome se ainda não tiver um Cofre de Chaves com este nome. Se já tiver um Cofre de Chaves que pretende utilizar, introduza o nome do Cofre de Chaves existente.
* **Localização** – Localização do Cofre de Chaves. Certifique-se de que o Cofre de Chaves e as VMs a encriptar estão na mesma localização. Se não souber a localização, existem passos neste artigo que irão mostrar-lhe como pode descobrir.
* **Nome da Aplicação do Azure Active Directory** – Nome da aplicação do Azure Active Directory que será utilizada para escrever segredos para o Cofre de Chaves. Será criada uma nova aplicação com este nome, caso ainda não exista. Se já tiver uma aplicação do Azure Active Directory que pretende utilizar, introduza o nome dessa aplicação do Azure Active Directory.

> [!NOTE]
> Se estiver com curiosidade relativamente à razão pela qual tem de criar uma aplicação do Azure Active Directory, consulte a secção *Registar uma aplicação com o Azure Active Directory* no artigo [Introdução ao Cofre de Chaves do Azure](../key-vault/key-vault-get-started.md).
>
>

Execute os seguintes passos para encriptar uma Máquina Virtual do Azure:

1. Se fechou o ISE do PowerShell, abra uma instância elevada do ISE do PowerShell. Se o ISE do PowerShell ainda não estiver aberto, siga as instruções anteriormente no artigo. Se tiver fechado o script, abra o **ADEPrereqScript.ps1** ao clicar em **Ficheiro** e, em seguida, em **Abrir** e selecionar o script da pasta **c:\AzureADEScript**. Se seguiu este artigo desde o início, continue para o passo seguinte.
2. Na consola do ISE do PowerShell (o painel inferior do ISE do PowerShell), altere o foco para o local do script ao escrever **cd c:\AzureADEScript** e prima **ENTER**.
3. Defina a política de execução no seu computador para que possa executar o script. Escreva **Set-ExecutionPolicy Unrestricted** na consola e, em seguida, prima ENTER. Se vir uma caixa de diálogo a informar acerca dos efeitos da alteração na política de execução, clique em **Sim para todos** ou em **Sim** (se vir **Sim para todos**, selecione essa opção; se não vir **Sim para todos**, clique em **Sim**).
4. Inicie sessão na sua conta do Azure. Na consola, escreva **Login-AzureRmAccount** e prima **ENTER**. É apresentada uma caixa de diálogo na qual introduz as suas credenciais (certifique-se de que tem direitos para alterar as máquinas virtuais; se não tiver direitos, não conseguirá encriptá-las. Se não tiver a certeza, pergunte ao proprietário ou administrador da subscrição). Deverá ver informações sobre o seu **Ambiente**, **Conta**, **TenantId**, **SubscriptionId** e **CurrentStorageAccount**. Copie o **SubscriptionId** para o Bloco de notas. Terá de utilizar isto no passo n.º 6.
5. Determine a que subscrição pertence a sua máquina virtual e a respetiva localização. Aceda a [https://portal.azure.com](ttps://portal.azure.com) e inicie sessão.  No lado esquerdo da página, clique em **Virtual Machines**. Irá ver uma lista das suas máquinas virtuais e das subscrições a que pertencem.

   ![Virtual Machines](./media/security-center-disk-encryption/security-center-disk-encryption-fig3.png)
6. Volte ao ISE do PowerShell. Defina o contexto de subscrição no qual o script será executado. Na consola, escreva **Select-AzureRmSubscription –SubscriptionId <your_subscription_Id>** (substitua **< your_subscription_Id >** pelo seu ID de Subscrição real) e prima **ENTER**. Deverá ver informações sobre o Ambiente, **Conta**, **TenantId**, **SubscriptionId** e **CurrentStorageAccount**.
7. Agora está pronto para executar o script. Clique no botão **Executar Script** ou prima **F5** no teclado.

   ![Executar Script do PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig4.png)
8. O script pede-lhe o **resourceGroupName:** – introduza o nome do *Grupo de Recursos* que pretende utilizar e, em seguida, prima **ENTER**. Se não tiver um, introduza um nome que pretende utilizar para um novo. Se já tiver um *Grupo de Recursos* que pretende utilizar (tal como aquele onde está a sua máquina virtual), introduza o nome do Grupo de Recursos existente.
9. O script pede-lhe o **keyVaultName:** –introduza o nome do *Cofre de Chaves* que pretende utilizar e, em seguida, prima ENTER. Se não tiver um, introduza um nome que pretende utilizar para um novo. Se já tiver um Cofre de Chaves que pretende utilizar, introduza o nome do *Cofre de Chaves* existente.
10. O script pede-lhe a **localização:** – introduza o nome da localização em que a VM que pretende encriptar se encontra e, em seguida, prima **ENTER**. Não se lembra da localização, volte ao passo n.º 5.
11. O script pede-lhe o **aadAppName:** – introduza o nome da aplicação do *Azure Active Directory* que pretende utilizar e, em seguida, prima **ENTER**. Se não tiver um, introduza um nome que pretende utilizar para um novo. Se já tiver uma *aplicação do Azure Active Directory* que pretende utilizar, introduza o nome da *aplicação do Azure Active Directory* existente.
12. Será apresentada um caixa de diálogo de início de sessão. Indique as suas credenciais (sim, iniciou sessão uma vez, mas agora tem de fazê-lo novamente).
13. O script é executado e, após concluído, irá pedir-lhe para copiar os valores de **aadClientID**, **aadClientSecret**, **diskEncryptionKeyVaultUrl** e **keyVaultResourceId**. Copie cada um destes valores para a área de transferência e cole-os no Bloco de notas.
14. Volte ao ISE do PowerShell e coloque o cursor no fim da última linha e, em seguida, prima **ENTER**.

O resultado do script deve ter um aspeto semelhante ao do ecrã abaixo:

![Saída do PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig5.png)

## <a name="encrypt-the-azure-virtual-machine"></a>Encriptar a máquina virtual do Azure
Agora está pronto para encriptar a sua máquina virtual. Se a máquina virtual está localizada no mesmo Grupo de Recursos como o seu Cofre de Chaves, pode mover-se para a secção de passos de encriptação. No entanto, se a máquina virtual não está no mesmo Grupo de Recursos como o seu Cofre de Chaves, terá de introduzir o seguinte na consola no ISE do PowerShell:

**$resourceGroupName = <’Virtual_Machine_RG’>**

Substitua **< Virtual_Machine_RG >** pelo nome do Grupo de Recursos em que as máquinas virtuais estão contidas, rodeado por uma aspa simples. Em seguida, prima **ENTER**.
Para confirmar que foi introduzido o nome do Grupo de Recursos correto, escreva o seguinte na consola do ISE do PowerShell:

**$resourceGroupName**

Prima **ENTER**. Deverá ver o nome do Grupo de Recursos em que as máquinas virtuais estão localizadas. Por exemplo:

![Saída do PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig6.png)

### <a name="encryption-steps"></a>Passos de encriptação
Primeiro, tem de facultar ao PowerShell o nome da máquina virtual que pretende encriptar. Na consola, escreva:

**$vmName = <’your_vm_name’>**

Substitua **<’your_vm_name’>** pelo nome da sua VM (certifique-se de que o nome está rodeado por uma aspa simples) e, em seguida, prima **ENTER**.

Para confirmar que foi introduzido o nome correto da VM, escreva:

**$vmName**

Prima **ENTER**. Deverá ver o nome da máquina virtual que pretende encriptar. Por exemplo:

![Saída do PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig7.png)

Existem dois métodos para executar o comando de encriptação para encriptar todas as unidades na máquina virtual. O primeiro método é escrever o seguinte comando na consola do ISE do PowerShell:

~~~
Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType All
~~~

Depois de escrever este comando, prima **ENTER**.

O segundo método é clicar no painel de script (o painel superior do ISE do PowerShell) e desloque para baixo até à parte inferior do script. Realce o comando listado acima e, em seguida, clique com o botão direito do rato no mesmo e clique em **Executar Seleção** ou prima **F8** no teclado.

![ISE do PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig8.png)

Independentemente do método que utilizar, é apresentada uma caixa de diálogo a informá-lo que a operação irá demorar 10 a 15 minutos a ser concluída. Clique em **Sim**.

Enquanto o processo de encriptação está a decorrer, pode voltar ao Portal do Azure e ver o estado da máquina virtual. No lado esquerdo da página, clique em **Virtual Machines** e, em seguida, no painel **Virtual Machines**, clique no nome da máquina virtual que está a encriptar. No painel que aparece, verá que o **Estado** indica que está **A atualizar**. Isto demonstra que a encriptação está em curso.

![Mais detalhes sobre a VM](./media/security-center-disk-encryption/security-center-disk-encryption-fig9.png)

Volte ao ISE do PowerShell. Quando o script estiver concluído, verá o que é apresentado na imagem abaixo.

![Saída do PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig10.png)

Para demonstrar que a máquina virtual já está encriptada, regresse ao Portal do Azure e clique em **Virtual Machines** no lado esquerdo da página. Clique no nome da máquina virtual que encriptou. No painel **Definições**, clique em **Discos**.

![Opções de definições](./media/security-center-disk-encryption/security-center-disk-encryption-fig11.png)

No painel **Discos**, verá que **Encriptação** está **Ativada**.

![Propriedades do disco](./media/security-center-disk-encryption/security-center-disk-encryption-fig12.png)

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a encriptar uma Máquina Virtual do Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Monitorização do estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança
* [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure
