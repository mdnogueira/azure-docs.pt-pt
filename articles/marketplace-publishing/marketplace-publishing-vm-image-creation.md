---
title: "Criar uma imagem de máquina virtual para o Azure Marketplace | Microsoft Docs"
description: "Instruções detalhadas sobre como criar uma imagem de máquina virtual para o Azure Marketplace para outras pessoas comprar."
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5c937b8e-e28d-4007-9fef-624046bca2ae
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio; v-divte
ms.openlocfilehash: e37c55dbcc8de49aee32272b2f51b0792bef132c
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="guide-to-create-a-virtual-machine-image-for-the-azure-marketplace"></a>Guia para criar uma imagem de máquina virtual para o Azure Marketplace
Neste artigo, **passo 2**, explica como preparar os discos rígidos virtuais (VHDs) que vai implementar no Azure Marketplace. Os VHDs são a base do SKU. O processo é diferente dependendo se está a fornecer um SKU baseado em Windows ou baseado em Linux. Este artigo abrange ambos os cenários. Este processo pode ser executado em paralelo com [conta criação e registo][link-acct-creation].

## <a name="1-define-offers-and-skus"></a>1. Definir ofertas e SKUs
Nesta secção, saiba definir as ofertas e os respetivos SKUs associados.

Uma oferta funciona como o "elemento principal" de todos os SKUs correspondentes. Pode ter várias ofertas. A forma como as ofertas são estruturadas é uma questão que lhe cabe a si decidir. Quando uma oferta é emitida para teste, a emissão é propagada por todos os SKUs correspondentes. Considere cuidadosamente os identificadores SKU, porque estará visíveis no URL:

* Azure.com: http://azure.microsoft.com/marketplace/partners/ {PartnerNamespace} / {OfferIdentifier}-{SKUidentifier}
* Portal de pré-visualização do Azure: https://portal.azure.com/#gallery/ {PublisherNamespace}. {OfferIdentifier} {SKUIDdentifier}  

Um SKU é o nome comercial para uma imagem de VM. Uma imagem de VM contém disco de um sistema operativo e zero ou mais discos de dados. Trata-se, no fundo, do perfil de armazenamento completo de uma máquina virtual. É necessário um VHD por disco. Os discos de dados, mesmo em branco precisam de um VHD ser criado.

Independentemente do sistema operativo utilizado, adicione apenas o número mínimo de discos de dados necessários ao SKU. Os clientes não é possível remover discos que fazem parte de uma imagem no momento da implementação, mas podem sempre adicionar discos durante ou após a implementação se precisam.

> [!IMPORTANT]
> **Não altere a contagem de discos de uma nova versão da imagem.** Se tem de reconfigurar os discos de dados na imagem, defina um SKU de novo. Publicar uma nova versão de imagem com as contagens de outro disco será o potencial de quebra nova implementação com base na nova versão de imagem em casos de dimensionamento automático, automáticas implementações das soluções através de modelos ARM e outros cenários.
>
>

### <a name="11-add-an-offer"></a>1.1 adicionar uma oferta
1. Iniciar sessão para o [Portal publicação] [ link-pubportal] utilizando a sua conta vendedor.
2. Selecione o **máquinas virtuais** separador do Portal de publicação. No campo do pedido de entrada, introduza o nome da oferta. A oferta é, normalmente, o nome do produto ou serviço que planear propor no Azure Marketplace.
3. Selecione **Criar**.

### <a name="12-define-a-sku"></a>1.2 definir um SKU
Depois de ter adicionado uma oferta, terá de definir e identificar os SKUs. Pode ter vários ofertas e cada oferta pode ter vários SKUs sob a mesma. Quando uma oferta é emitida para teste, a emissão é propagada por todos os SKUs correspondentes.

1. **Adicione um SKU.** O SKU não requer um identificador, o que é utilizado no URL. O identificador tem de ser exclusivo no seu perfil de publicação, mas não existe nenhum risco de colisão identificador com outros fabricantes.

   > [!NOTE]
   > A oferta e identificadores SKU são apresentados no URL oferta no Marketplace.
   >
   >
2. **Adicione uma descrição sumária para o SKU.** Resumidas descrições são visíveis para os clientes, pelo que deve torná-los facilmente legível. Estas informações não precisam de ser bloqueado até que a fase de "Push para transição". Até lá, pode editá-las conforme necessário.
3. Se estiver a utilizar SKUs baseadas no Windows, siga as ligações sugeridas para obter as versões aprovadas do Windows Server.

## <a name="2-create-an-azure-compatible-vhd-linux-based"></a>2. Criar um VHD compatível com o Azure (baseado em Linux)
Esta secção está centrado nas melhores práticas para criar uma imagem VM baseadas em Linux para o Azure Marketplace. Para obter instruções passo a passo, consulte a seguinte documentação: [criar e carregar um disco rígido Virtual que contém o sistema operativo Linux](../virtual-machines/linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="3-create-an-azure-compatible-vhd-windows-based"></a>3. Criar um VHD compatível com o Azure (baseado no Windows)
Esta secção centra-se os passos para criar um SKU baseado no Windows Server para o Azure Marketplace.

### <a name="31-ensure-that-you-are-using-the-correct-base-vhds"></a>3.1 Certifique-se de que está a utilizar os VHDs base corretos
O VHD de sistema operativo para a imagem VM tem de ser baseada em imagem base aprovado do Azure que contém o Windows Server ou SQL Server.

Para começar, crie uma VM a partir de um dos seguintes imagens, localizado no [portal do Microsoft Azure][link-azure-portal]:

* Windows Server ([2012 R2 Datacenter][link-datactr-2012-r2], [2012 Datacenter][link-datactr-2012], [2008 R2 SP1] [link-datactr-2008-r2])
* SQL Server 2014 ([Enterprise][link-sql-2014-ent], [padrão][link-sql-2014-std], [Web] [ link-sql-2014-web])
* SQL Server 2012 SP2 ([Enterprise][link-sql-2012-ent], [padrão][link-sql-2012-std], [Web] [ link-sql-2012-web])

Estas ligações encontram-se igualmente disponíveis no Portal de Publicação, na página SKU.

> [!TIP]
> Se estiver a utilizar o portal do Azure atual ou o PowerShell, imagens do Windows Server publicadas no 8 de Setembro de 2014 e posteriores são aprovadas.
>
>

### <a name="32-create-your-windows-based-vm"></a>3.2 criar a VM com base no Windows
Do portal do Microsoft Azure, pode criar a VM com base numa imagem base aprovada em apenas alguns passos simples. Segue-se uma descrição geral do processo:

1. Na página de imagem de base, selecione **criar Máquina Virtual** para ser direcionado para o novo [portal do Microsoft Azure][link-azure-portal].

    ![desenho][img-acom-1]
2. Inicie sessão no portal com a conta Microsoft e a palavra-passe para a subscrição do Azure que pretende utilizar.
3. Siga as instruções para criar uma VM utilizando a imagem de base que selecionou. Tem de fornecer um anfitrião nome (nome do computador), o nome de utilizador (registado como administrador) e palavra-passe para a VM.

    ![desenho][img-portal-vm-create]
4. Selecione o tamanho da VM para implementar:

    a.    Se planear desenvolver o VHD no local, o tamanho não importa. Pondere utilizar uma das VMs mais pequenas.

    b.    Se tenciona desenvolver a imagem no Azure, pondere utilizar um dos tamanhos de VM recomendados para a imagem selecionada.

    c.    Para obter informações sobre preços, consulte o **recomendado escalões de preço** Seletor apresentado no portal. Ser-lhe-ão indicados os três tamanhos recomendados fornecidos pelo editor. (Neste caso, o editor é a Microsoft.)

    ![desenho][img-portal-vm-size]
5. Defina as propriedades:

    a.    Para uma implementação rápida, pode deixar os valores predefinidos para as propriedades em **configuração opcional** e **grupo de recursos**.

    b.    Em **conta de armazenamento**, opcionalmente, pode selecionar a conta de armazenamento no qual será armazenado o VHD de sistema operativo.

    c.    Em **grupo de recursos**, opcionalmente, pode selecionar o grupo lógico onde colocar a VM.
6. Selecione o **localização** para a implementação:

    a.    Se planear desenvolver o VHD no local, a localização é importante porque irá carregar a imagem para o Azure mais tarde.

    b.    Se tenciona desenvolver a imagem no Azure, pondere utilizar uma das regiões do Microsoft Azure baseadas nos E.U.A. desde o início. Isto acelera o processo de cópia do VHD que efetua a Microsoft em seu nome ao submeter a imagem de certificação.

    ![desenho][img-portal-vm-location]
7. Clique em **Criar**. A VM começa a implementar. No espaço de alguns minutos, terá uma implementação bem-sucedida à sua disposição, podendo começar a criar a imagem para o SKU.

### <a name="33-develop-your-vhd-in-the-cloud"></a>3.3 desenvolver o VHD na nuvem
Recomendamos vivamente que desenvolver o VHD na nuvem utilizando o protocolo RDP (Remote Desktop Protocol). Ligar para o RDP com o nome de utilizador e palavra-passe especificada durante o aprovisionamento.

> [!IMPORTANT]
> Se desenvolver o VHD no local (que não seja recomendado), consulte [criar uma imagem de máquina virtual no local](marketplace-publishing-vm-image-creation-on-premise.md). Transferir o VHD não é necessário se estiver a desenvolver na nuvem.
>
>

**Ligar através de RDP a utilizar o [portal do Microsoft Azure][link-azure-portal]**

1. Selecione **procurar** > **VMs**.
2. É aberto o painel de máquinas virtuais. Certifique-se de que a VM que pretende estabelecer ligação com está em execução e, em seguida, selecione-o da lista de VMs implementadas.
3. É aberto um painel que descreve a VM selecionada. Na parte superior, clique em **Connect**.
4. Lhe for pedido que introduza o nome de utilizador e palavra-passe que especificou durante o aprovisionamento.

**Ligar através de RDP com o PowerShell**

Para transferir um ficheiro de ambiente de trabalho remoto para um computador local, utilize o [cmdlet Get-AzureRemoteDesktopFile][link-technet-2]. Para utilizar este cmdlet, terá de saber o nome do serviço e nome da VM. Se tiver criado a VM a partir de [portal do Microsoft Azure][link-azure-portal], pode encontrar estas informações em propriedades VM:

1. No portal do Microsoft Azure, selecione **procurar** > **VMs**.
2. É aberto o painel de máquinas virtuais. Selecione a VM que tenha implementado.
3. É aberto um painel que descreve a VM selecionada.
4. Clique em **Propriedades**.
5. A primeira parte do nome de domínio corresponde ao nome do serviço. O nome do anfitrião é o nome da VM.

    ![desenho][img-portal-vm-rdp]
6. Segue-se o cmdlet para transferir o ficheiro RDP para a VM criada ao ambiente de trabalho do administrador local.

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

Podem encontrar mais informações sobre RDP no MSDN no artigo [ligar a uma VM do Azure com RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx).

**Configurar uma VM e criar o SKU**

Depois do sistema operativo que é transferida VHD, utilize o Hyper-v e configurar uma VM para começar a criar o SKU. Os passos detalhados podem ser encontrados na seguinte hiperligação do TechNet: [instalar o Hyper-v e configurar uma VM](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="34-choose-the-correct-vhd-size"></a>3.4 Escolha o tamanho do VHD correto
O Windows VHD de sistema operativo da imagem do VM deve ser criado como um VHD de formato fixo de 128 GB.  

Se o tamanho físico é inferior a 128 GB, o VHD deve ser disperso. As imagens do Windows e o SQL Server base fornecidas já cumpra estes requisitos, por isso, não altere o formato ou o tamanho do VHD obtido.  

Os discos de dados podem ser igual a 1 TB. Quando decidir sobre o tamanho do disco, lembre-se de que os clientes não podem redimensionar os VHDs dentro de uma imagem no momento da implementação. VHDs de disco de dados devem ser criados como um VHD de formato fixo. Também deve ser dispersos. Os discos de dados podem estar vazios ou conter dados.

### <a name="35-install-the-latest-windows-patches"></a>3.5 instalar as correções mais recentes do Windows
As imagens base contêm os patches mais recentes até à data da publicação das mesmas. Antes de publicar o sistema operativo VHD que criou, certifique-se de que o Windows Update foi executada e de que todas as atualizações mais recentes crítico e importante segurança tem sido instaladas.

### <a name="36-perform-additional-configuration-and-schedule-tasks-as-necessary"></a>3.6 efetuar tarefas adicionais de configuração e a agenda conforme necessário
Se não for necessária configuração adicional, considere a utilização de uma tarefa agendada que é executado no arranque para efetuar quaisquer alterações finais para a VM, depois de foi implementada:

* É considerada uma melhor prática que a tarefa esteja programada para se eliminar automaticamente após a execução bem-sucedida da mesma.
* Nenhuma configuração deverá confiar em unidades diferentes unidades C ou D, porque estas são as unidades apenas dois sempre garantidos existir. Unidade C é o disco de sistema operativo, não sendo unidade D temporário disco local.

### <a name="37-generalize-the-image"></a>3.7 generalize a imagem
Todas as imagens no Azure Marketplace tem de ser reutilizáveis de forma genérica. Por outras palavras, deve ser generalizado o VHD de sistema operativo:

* Para o Windows, a imagem deve ser "processado pelo Sysprep" e devem ser efetuadas não configurações que não suportam o **sysprep** comando.
* Pode executar o seguinte comando a partir do diretório % windir%\System32\Sysprep.

        sysprep.exe /generalize /oobe /shutdown

  Orientações sobre a forma como a preparação de sistema o sistema operativo é fornecido no passo do seguinte artigo do MSDN: [criar e carregar um VHD do Windows Server para o Azure](../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="4-deploy-a-vm-from-your-vhds"></a>4. Implementar uma VM a partir do seu VHDs
Depois de ter carregados os VHDs (o VHD de sistema operativo generalizado e zero ou mais dados em disco VHDs) a uma conta de armazenamento do Azure, pode registá-los como uma imagem VM de utilizador. Em seguida, pode testar essa imagem. Tenha em atenção que porque o sistema operativo VHD seja generalizado, não é possível diretamente implemente a VM ao fornecer o URL de VHD.

Para mais informações sobre imagens VM, reveja as seguintes mensagens de blogue:

* [Imagem de VM](https://azure.microsoft.com/blog/vm-image-blog-post/)
* [VM imagem do PowerShell como](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
* [Sobre as imagens VM no Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### <a name="set-up-the-necessary-tools-powershell-and-azure-cli"></a>Configurar as ferramentas necessárias, PowerShell e da CLI do Azure
* [Como configurar o PowerShell](/powershell/azure/overview)
* [Como configurar a CLI do Azure](../cli-install-nodejs.md)

### <a name="41-create-a-user-vm-image"></a>4.1 criar uma imagem VM de utilizador
#### <a name="capture-vm"></a>Captura de VM
Leia as ligações indicadas abaixo para obter orientações sobre a VM com o PowerShell/API/Azure CLI a capturar.

* [API](https://msdn.microsoft.com/library/mt163560.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI do Azure](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="generalize-image"></a>Generalize a imagem
Leia as ligações indicadas abaixo para obter orientações sobre a VM com o PowerShell/API/Azure CLI a capturar.

* [API](https://msdn.microsoft.com/library/mt269439.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI do Azure](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="42-deploy-a-vm-from-a-user-vm-image"></a>4.2 implementar uma VM a partir de uma imagem VM de utilizador
Para implementar uma VM a partir de uma imagem VM de utilizador, pode utilizar atual [portal do Azure](https://manage.windowsazure.com) ou PowerShell.

**Implementar uma VM a partir do portal do Azure**

1. Aceda a **novo** > **computação** > **Máquina Virtual** > **da galeria**.

    ![desenho][img-manage-vm-new]
2. Aceda a **minhas imagens**e, em seguida, selecione a imagem VM a partir da qual pode implementar uma VM:

   1. Preste especial atenção para a imagem que seleciona, porque o **minhas imagens** vista apresenta uma lista de imagens do sistema operativo e imagens VM.
   2. Observar o número de discos pode ajudar a determinar o tipo de imagem que estiver a implementar, porque a maioria das imagens da VM tem mais de um disco. No entanto, é possível ter uma imagem de VM com apenas um único disco de sistema operativo, que, em seguida, teria **número de discos** definido como 1.

      ![desenho][img-manage-vm-select]
3. Siga o Assistente de criação de VM e especifique a VM nome, VM tamanho, localização, nome de utilizador e palavra-passe.

**Implementar uma VM a partir do PowerShell**

Para implementar uma VM de grandes dimensões da imagem de VM generalizada acabou de criar, pode utilizar os seguintes cmdlets.

    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot

> [!IMPORTANT]
> Consulte [Resolução de problemas comuns quais os problemas encontrados durante a criação do VHD] para obter assistência adicional.
>
>

## <a name="5-obtain-certification-for-your-vm-image"></a>5. Obter a certificação para a imagem VM
A preparar a imagem VM para o Azure Marketplace o passo seguinte é para que o certificado.

Este processo inclui a executar uma ferramenta de certificação especial, carregar os resultados de verificação para o contentor do Azure onde residem os VHDs, adicionar uma oferta, definir o SKU e submeter a imagem VM de certificação.

### <a name="51-download-and-run-the-certification-test-tool-for-azure-certified"></a>5.1 transfira e execute a ferramenta de teste de certificação para o certificado do Azure
A ferramenta de certificação é executada numa VM em execução, aprovisionada a partir da imagem de VM de utilizador, para garantir que a imagem VM é compatível com o Microsoft Azure. A ferramenta irá verificar se as orientações e os requisitos inerentes à preparação do VHD foram cumpridos. O resultado da ferramenta é um relatório de compatibilidade, que deve ser carregado no Portal de publicação ao requerente de certificação.

A ferramenta de certificação pode ser utilizada com o Windows e VMs com Linux. Liga-se para as VMs baseadas em Windows através do PowerShell e liga-se para VMs com Linux através do SSH.Net:

1. Em primeiro lugar, transfira a ferramenta de certificação no [site de transferências da Microsoft][link-msft-download].
2. Abrir a ferramenta de certificação e, em seguida, clique em de **Iniciar novo teste** botão.
3. Do **testar informações** ecrã, introduza um nome para o execução do teste.
4. Indique se a VM reporta ao Linux ou ao Windows. Dependendo da escolha efetuada, selecione as opções subsequentes.

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>**Ligar a ferramenta de certificação para uma imagem de VM com Linux**
1. Selecione o modo de autenticação de SSH: palavra-passe ou ficheiro de chave.
2. Se utilizar autenticação baseada em palavra-passe, introduza o nome de sistema de nomes de domínio (DNS), nome de utilizador e palavra-passe.
3. Se utilizar a autenticação de ficheiro de chave, introduza o nome DNS, o nome de utilizador e a localização da chave privada.

   ![Autenticação de palavra-passe da imagem de VM do Linux][img-cert-vm-pswd-lnx]

   ![Autenticação de chave de ficheiro de imagem de VM do Linux][img-cert-vm-key-lnx]

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Ligar a ferramenta de certificação para uma imagem VM baseadas em Windows**
1. Introduza o nome de VM DNS completamente qualificado (por exemplo, MyVMName.Cloudapp.net).
2. Introduza o nome de utilizador e palavra-passe.

   ![Autenticação de palavra-passe da imagem de VM do Windows][img-cert-vm-pswd-win]

Depois de selecionar as opções corretas para a imagem VM de baseados em Windows ou Linux, selecione **Testar ligação** para garantir que SSH.Net ou o PowerShell tem uma ligação válida para fins de teste. Depois de é estabelecida uma ligação, selecione **seguinte** para iniciar o teste.

Quando o teste estiver concluído, receberá os resultados (Pass/Fail/Warning) para cada elemento de teste.

![Casos de teste para a imagem de VM do Linux][img-cert-vm-test-lnx]

![Casos de teste para a imagem de VM do Windows][img-cert-vm-test-win]

Se algum dos testes falhar, a imagem não irá ser certificada. Se isto ocorrer, reveja os requisitos e efetue as alterações necessárias.

Depois do teste automatizado, é-lhe pedido para fornecer informações adicionais sobre a imagem VM através de um ecrã questionário.  Conclua as perguntas e, em seguida, selecione **seguinte**.

![Questionário da ferramenta de certificação][img-cert-vm-questionnaire]

![Questionário da ferramenta de certificação][img-cert-vm-questionnaire-2]

Depois de concluir o questionário, pode fornecer informações adicionais, tais como informações de acesso SSH para a VM com Linux imagem e uma explicação para qualquer avaliações de falha. Pode transferir os resultados do teste e os ficheiros para os casos de teste executados para além das suas respostas ao questionário. Guarde os resultados no mesmo contentor que os VHDs.

![Guardar resultados do teste de certificação][img-cert-vm-results]

### <a name="52-get-the-shared-access-signature-uri-for-your-vm-images"></a>5.2 obter a URI de assinatura de acesso partilhado para as imagens VM
Durante o processo de publicação, especifique os identificadores de recurso uniforme (URI) que levar a cada um dos VHDs que criou para o SKU. A Microsoft precisa de aceder a estes VHDs durante o processo de certificação. Por conseguinte, terá de criar uma URI de assinatura de acesso partilhado para cada VHD. Este é o URI que deve ser introduzido no **imagens** separador no Portal de publicação.

A assinatura de acesso partilhado que URI criado deve cumprir os requisitos seguintes:

Nota: as instruções seguintes são aplicáveis apenas para os discos não geridos que são o único tipo suportado.

* Quando a gerar a assinatura de acesso partilhado URIs para os seus VHDs, permissões de leitura e de lista são suficientes. Não forneça acesso de Escrita ou de Eliminação.
* A duração de acesso deve ter um mínimo de três (3) semanas a partir do quando é criada a assinatura de acesso partilhado URI.
* Para salvaguardar para a hora UTC, selecione o dia anterior à data atual. Por exemplo, se a data atual for 6 de Outubro de 2014, selecione 5/10/2014.

É possível gerar o SAS URL de várias formas para partilhar o VHD para o Azure Marketplace.
Seguem-se as 3 ferramentas recomendadas:

1.  Explorador do Storage do Azure
2.  Explorador de armazenamento da Microsoft
3.  CLI do Azure

**Explorador de armazenamento do Azure (recomendado para utilizadores do Windows)**

Seguem-se os passos para gerar o SAS URL utilizando o Explorador de armazenamento do Azure

1. Transferir [pré-visualização 6 do Explorador de armazenamento do Azure 3](https://azurestorageexplorer.codeplex.com/) de CodePlex. Aceda a [pré-visualização de 6 do Explorador de armazenamento de Azure](https://azurestorageexplorer.codeplex.com/) e clique em **"Transferir"**.

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_01.png)

2. Transferir [AzureStorageExplorer6Preview3.zip](https://azurestorageexplorer.codeplex.com/downloads/get/891668) e instalar após unzipping-lo.

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_02.png)

3. Depois de ser instalado, abra a aplicação.
4. Clique em **adicionar conta**.

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_03.png)

5. Especifique o nome da conta de armazenamento, a chave de conta de armazenamento e o domínio de pontos finais de armazenamento. Esta é a conta de armazenamento na sua subscrição do Azure onde tiver mantido o VHD no portal do Azure.

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_04.png)

6. Depois do Explorador de armazenamento do Azure está ligado à sua conta de armazenamento específico, irá iniciar que mostra todas as contém dentro da conta de armazenamento. Selecione o contentor onde copiou o ficheiro VHD do disco do sistema operativo (também discos de dados se estas forem aplicáveis para o seu cenário).

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_05.png)

7. Depois de selecionar o contentor de blob, é iniciado Explorador de armazenamento do Azure que mostra os ficheiros dentro do contentor. Selecione o ficheiro de imagem (. vhd) que tem de ser submetidos.

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_06.png)

8.  Depois de selecionar o ficheiro. vhd no contentor, clique em de **segurança** separador.

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_07.png)

9.  No **segurança de contentor do Blob** diálogo caixa, deixe as predefinições no **nível de acesso** separador e, em seguida, clique em **assinaturas de acesso partilhado** separador.

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_08.png)

10. Siga os passos abaixo para gerar uma assinatura de acesso partilhado URI para a imagem de VHD:

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_09.png)

    a. **Permissão de acesso do:** para salvaguardar para a hora UTC, selecione o dia anterior à data atual. Por exemplo, se a data atual for 6 de Outubro de 2014, selecione 5/10/2014.

    b. **Permissão de acesso à entidade:** Selecione uma data que seja, pelo menos, 3 semanas após o **permissão de acesso do** data.

    c. **As ações permitidas:** selecionar o **lista** e **leitura** permissões.

    d. Se tiver selecionado corretamente o ficheiro. vhd, em seguida, o ficheiro é apresentado no **nome do Blob para aceder à** com extensão VHD.

    e. Clique em **gerar a assinatura**.

    f. No **gerado partilhado acesso assinatura URI neste contentor**, verifique o seguinte conforme realçado acima:

       - Certifique-se de que a imagem de nome de ficheiro e **". vhd"** são no URI.
       - No final da assinatura, certifique-se de que **"= rl"** aparece. Isto demonstra que o acesso de leitura e lista foi fornecido com êxito.
       - No meio da assinatura, certifique-se de que **"sr = c"** aparece. Isto demonstra que tem acesso ao nível do contentor

11. Para garantir que o gerado partilhadas funciona URI de assinatura de acesso, clique em **teste no Browser**. Este deve iniciar o processo de transferência.

12. Copie a URI de assinatura de acesso partilhado. Este é o URI que deverá colar no Portal de Publicação.

13. Repita os passos 6-10 para cada VHD no SKU.

**Explorador de armazenamento do Microsoft Azure (Windows/MAC/Linux)**

Seguem-se os passos para gerar o SAS URL utilizando o Explorador de armazenamento do Microsoft Azure

1.  Transferir o formulário do Explorador de armazenamento do Microsoft Azure [http://storageexplorer.com/](http://storageexplorer.com/) Web site. Aceda a [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com/releasenotes.html) e clique em **"Transferir para Windows"**.

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_10.png)

2.  Depois de ser instalado, abra a aplicação.

3.  Clique em **adicionar conta**.

4.  Configurar o Explorador de armazenamento do Microsoft Azure à sua subscrição, inicie sessão na sua conta

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_11.png)

5.  Ir para a conta de armazenamento e selecione o contentor

6.  Selecione **"Obter assinatura de acesso de partilha.."** ao utilizar clique direito do **contentor**

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_12.png)

7.  Hora de início de atualização, a hora de expiração e as permissões de acordo com o seguinte

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_13.png)

    a.  **Hora de início:** para salvaguardar para a hora UTC, selecione o dia anterior à data atual. Por exemplo, se a data atual for 6 de Outubro de 2014, selecione 5/10/2014.

    b.  **Hora de expiração:** Selecione uma data que seja, pelo menos, 3 semanas após o **hora de início** data.

    c.  **Permissões:** selecionar o **lista** e **leitura** permissões

8.  Copie a assinatura de acesso partilhado do contentor URI

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_14.png)

    É gerado URL SAS para o contentor de nível e agora precisamos de adicionar o nome do VHD no mesmo.

    Formato do URL de SAS ao nível do contentor:`https://testrg009.blob.core.windows.net/vhds?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Insira o nome do VHD após o nome de contentor no URL de SAS, tal como indicado abaixo`https://testrg009.blob.core.windows.net/vhds/<VHD NAME>?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Exemplo:

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_15.png)

    TestRGVM201631920152.vhd é o nome do VHD, em seguida, será o URL de SAS do VHD`https://testrg009.blob.core.windows.net/vhds/TestRGVM201631920152.vhd?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    - Certifique-se de que a imagem de nome de ficheiro e **". vhd"** são no URI.
    - No meio da assinatura, certifique-se de que **"SP2 = rl"** aparece. Isto demonstra que o acesso de leitura e lista foi fornecido com êxito.
    - No meio da assinatura, certifique-se de que **"sr = c"** aparece. Isto demonstra que tem acesso ao nível do contentor

9.  Para garantir que o gerado partilhadas funciona URI de assinatura de acesso, testá-lo no browser. Este deve iniciar o processo de transferência

10. Copie a URI de assinatura de acesso partilhado. Este é o URI que deverá colar no Portal de Publicação.

11. Repita estes passos para cada VHD no SKU.

**CLI do Azure (recomendado para a integração contínua & de não Windows)**

Seguem-se os passos para gerar o URL de SAS, utilizando a CLI do Azure

1.  Transferir o Microsoft Azure CLI de [aqui](https://azure.microsoft.com/en-in/documentation/articles/xplat-cli-install/). Também pode encontrar ligações diferentes para  **[Windows](http://aka.ms/webpi-azure-cli)**  e  **[MAC OS](http://aka.ms/mac-azure-cli)**.

2.  Uma vez é transferido, instale o

3.  Criar um PowerShell (ou outro executável de script) de ficheiros com o seguinte código e guardá-lo localmente

          $conn="DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<Storage Account Key>"
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl <Permission End Date> -c $conn --start <Permission Start Date>  

    Atualize os parâmetros seguintes no acima

    a. **`<StorageAccountName>`**: Dê o nome da sua conta de armazenamento

    b. **`<Storage Account Key>`**: Dê a sua chave de conta do storage

    c. **`<Permission Start Date>`**: Para salvaguardar para a hora UTC, selecione o dia anterior à data atual. Por exemplo, se a data atual for 26 de Outubro de 2016, em seguida, valor deve ser 25/10/2016. Se utilizar o Azure CLI 2.0 (az command), forneça a data e a hora no início e fim de datas, por exemplo: 10-25-2016T00:00:00Z.

    d. **`<Permission End Date>`**: Selecione uma data que seja, pelo menos, 3 semanas após o **data de início**. O valor deve ser **11/02/2016**. Se utilizar o Azure CLI 2.0 (az command), forneça a data e a hora no início e fim de datas, por exemplo: 11-02-2016T00:00:00Z.

    Segue-se o código de exemplo depois de atualizar os parâmetros corretos

          $conn="DefaultEndpointsProtocol=https;AccountName=st20151;AccountKey=TIQE5QWMKHpT5q2VnF1bb+NUV7NVMY2xmzVx1rdgIVsw7h0pcI5nMM6+DVFO65i4bQevx21dmrflA91r0Vh2Yw=="
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl 11/02/2016 -c $conn --start 10/25/2016  

4.  Abra o editor de Powershell com o modo de "Executar como administrador" e abra o ficheiro no passo 3 de #. Pode utilizar qualquer editor de scripts que está disponível no seu SO.

5.  Execute o script e é-lhe o URL de SAS para o acesso ao nível do contentor

    Seguinte será a saída da assinatura SAS e copie a parte realçada num bloco de notas

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_16.png)

6.  Agora, irá obter SAS URL ao nível do contentor e tem de adicionar o nome do VHD no mesmo.

    URL do contentor de nível SAS #

    `https://st20151.blob.core.windows.net/vhds?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

7.  Insira o nome do VHD após o nome do contentor no SAS URL conforme mostrado abaixo`https://st20151.blob.core.windows.net/vhds/<VHDName>?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    Exemplo:

    TestRGVM201631920152.vhd é o nome do VHD, em seguida, será o URL de SAS do VHD

    `https://st20151.blob.core.windows.net/vhds/ TestRGVM201631920152.vhd?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    - Certifique-se de que o nome de ficheiro de imagem e ". vhd" no URI.
    -   No meio da assinatura, certifique-se de que "SP2 = rl" aparece. Isto demonstra que o acesso de leitura e lista foi fornecido com êxito.
    -   No meio da assinatura, certifique-se de que "sr = c" é apresentada. Isto demonstra que tem acesso ao nível do contentor

8.  Para garantir que o gerado partilhadas funciona URI de assinatura de acesso, testá-lo no browser. Este deve iniciar o processo de transferência

9.  Copie a URI de assinatura de acesso partilhado. Este é o URI que deverá colar no Portal de Publicação.

10. Repita estes passos para cada VHD no SKU.


### <a name="53-provide-information-about-the-vm-image-and-request-certification-in-the-publishing-portal"></a>5.3 fornecem informações sobre a imagem VM e solicitar certificação no Portal de publicação
Depois de ter criado a sua oferta e SKU, deverá introduzir os detalhes da imagem associados que SKU:

1. Vá para o [Portal publicação][link-pubportal]e, em seguida, inicie sessão com a sua conta vendedor.
2. Selecione o **imagens da VM** separador.
3. O identificador listado na parte superior da página é, na verdade, o identificador de oferta e não o identificador SKU.
4. Preencha as propriedades sob o **SKUs** secção.
5. Em **família de sistemas operativos**, clique no tipo de sistema operativo associado ao sistema operativo VHD.
6. No **sistema operativo** caixa, descrevem o sistema operativo. Considere um formato como família do sistema operativo, tipo, versão e atualizações. Um exemplo é "Windows Server Datacenter 2014 R2".
7. Selecione até seis tamanhos de recomendado da máquina virtual. Estes são recomendações são apresentadas para o cliente no painel de escalão de preço no Portal do Azure quando se optam por adquirir e implementar a imagem. **Estes são apenas recomendações. O cliente é capaz de selecionar o tamanho VM que permite a discos especificados na imagem.**
8. Introduza a versão. O campo de versão encapsula uma versão semântica para identificar o produto e respetivas atualizações:
   * Versões devem ter o formato X.Y.Z, onde X, Y e Z são números inteiros.
   * Imagens de SKUs diferentes podem ter diferentes versões principais e secundárias.
   * Versões dentro de um SKU só devem ser as alterações incrementais, aumentam a versão de patch (Z do X.Y.Z).
9. No **URL de VHD de SO** box, introduza a assinatura de acesso partilhado URI criado para o VHD de sistema operativo.
10. Se existirem discos de dados associados este SKU, selecione o número de unidade lógica (LUN) ao qual pretende que este disco de dados seja montado após a implementação.
11. No **LUN X VHD URL** box, introduza a URI criado para os primeiros dados VHD de assinatura de acesso partilhado.

    ![desenho](media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-3.png)


## <a name="common-sas-url-issues--fixes"></a>URL de SAS comum emite & corrige

|Problema|Mensagem de falha|Corrigir|Ligação de documentação|
|---|---|---|---|
|Falha ao copiar imagens - "?" não foi encontrado no SAS url|Falha: Copiar as imagens. Não é possível transferir BLOBs utiliza fornecidas Uri de SAS.|Atualizar o SAS Url utilizando recomendado ferramentas|[https://Azure.microsoft.com/en-us/Documentation/articles/Storage-DotNet-Shared-Access-Signature-Part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Falha ao copiar imagens - "st" e "zar" parâmetros não SAS url|Falha: Copiar as imagens. Não é possível transferir BLOBs utiliza fornecidas Uri de SAS.|Atualizar o Url de SAS com datas de início e fim no mesmo|[https://Azure.microsoft.com/en-us/Documentation/articles/Storage-DotNet-Shared-Access-Signature-Part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Falha ao copiar imagens - "SP2 = rl" não está num SAS url|Falha: Copiar as imagens. Não é possível transferir BLOBs utiliza fornecidas Uri de SAS|Atualizar o Url de SAS com definido como "Leitura" & "lista de permissões|[https://Azure.microsoft.com/en-us/Documentation/articles/Storage-DotNet-Shared-Access-Signature-Part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Falha ao copiar imagens - SAS url tem espaços em branco no nome do vhd|Falha: Copiar as imagens. Não é possível transferir BLOBs utiliza fornecidas Uri de SAS.|Atualizar o Url de SAS, sem espaços em branco|[https://Azure.microsoft.com/en-us/Documentation/articles/Storage-DotNet-Shared-Access-Signature-Part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Falha ao copiar imagens – erro de autorização de Url SAS|Falha: Copiar as imagens. Não é possível transferir BLOBs devido a erro de autorização|Voltar a gerar o Url SAS|[https://Azure.microsoft.com/en-us/Documentation/articles/Storage-DotNet-Shared-Access-Signature-Part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Falha ao copiar imagens – o Url de SAS "st" e "zar" parâmetros não dispõe de especificação de data / hora completa|Falha: Copiar as imagens. Não é possível transferir BLOBs devido ao Url de SAS incorreto |Parâmetros de início do Url de SAS e a data de fim ("st", "zar") são necessários para ter a especificação de completa de data / hora, tal como 11-02-2017T00:00:00Z e não apenas a data ou versões reduzidas durante o período de tempo. É possível encontrar este cenário através do Azure CLI 2.0 (az command). Lembre-se de que fornecem a especificação de data / hora completa e voltar a gerar o Url de SAS.|[https://Azure.microsoft.com/Documentation/articles/Storage-DotNet-Shared-Access-Signature-Part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|

## <a name="next-step"></a>Passo seguinte
Depois de terminar com os detalhes do SKU, pode avançar para o [Guia do Azure Marketplace marketing conteúdo][link-pushstaging]. Esse passo do processo de publicação, deve fornecer o conteúdo de marketing, preço e outras informações necessárias antes **passo 3: testar a sua VM oferecem no modo de transição**, onde testar vários cenários de caso de utilização antes de implementar o oferta no Azure Marketplace para visibilidade pública e compra.  

## <a name="see-also"></a>Consultar também
* [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

[img-acom-1]:media/marketplace-publishing-vm-image-creation/vm-image-acom-datacenter.png
[img-portal-vm-size]:media/marketplace-publishing-vm-image-creation/vm-image-portal-size.png
[img-portal-vm-create]:media/marketplace-publishing-vm-image-creation/vm-image-portal-create-vm.png
[img-portal-vm-location]:media/marketplace-publishing-vm-image-creation/vm-image-portal-location.png
[img-portal-vm-rdp]:media/marketplace-publishing-vm-image-creation/vm-image-portal-rdp.png
[img-azstg-add]:media/marketplace-publishing-vm-image-creation/vm-image-storage-add.png
[img-manage-vm-new]:media/marketplace-publishing-vm-image-creation/vm-image-manage-new.png
[img-manage-vm-select]:media/marketplace-publishing-vm-image-creation/vm-image-manage-select.png
[img-cert-vm-key-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-keyfile-linux.png
[img-cert-vm-pswd-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-linux.png
[img-cert-vm-pswd-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-win.png
[img-cert-vm-test-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-linux.png
[img-cert-vm-test-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-win.png
[img-cert-vm-results]:media/marketplace-publishing-vm-image-creation/vm-image-certification-results.png
[img-cert-vm-questionnaire]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire.png
[img-cert-vm-questionnaire-2]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire-2.png
[img-pubportal-vm-skus]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus.png
[img-pubportal-vm-skus-2]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-2.png

[link-pushstaging]:marketplace-publishing-push-to-staging.md
[link-github-waagent]:https://github.com/Azure/WALinuxAgent
[link-azure-codeplex]:https://azurestorageexplorer.codeplex.com/
[link-azure-2]:../storage/blobs/storage-dotnet-shared-access-signature-part-2.md
[link-azure-1]:../storage/common/storage-dotnet-shared-access-signature-part-1.md
[link-msft-download]:http://www.microsoft.com/download/details.aspx?id=44299
[link-technet-3]:https://technet.microsoft.com/library/hh846766.aspx
[link-technet-2]:https://msdn.microsoft.com/library/dn495261.aspx
[link-azure-portal]:https://portal.azure.com
[link-pubportal]:https://publish.windowsazure.com
[link-sql-2014-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/
[link-sql-2014-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/
[link-sql-2014-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/
[link-sql-2012-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/
[link-sql-2012-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/
[link-sql-2012-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/
[link-datactr-2012-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/
[link-datactr-2012]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/
[link-datactr-2008-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-technet-1]:https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]:./virtual-machines-linux-agent-user-guide/
[link-openssl]:https://www.openssl.org/
[link-intsvc]:http://www.microsoft.com/download/details.aspx?id=41554
[link-python]:https://www.python.org/
