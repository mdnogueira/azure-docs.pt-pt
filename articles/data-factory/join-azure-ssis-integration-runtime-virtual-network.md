---
title: "Associar o tempo de execução de integração de SSIS do Azure para uma VNET | Microsoft Docs"
description: "Saiba como associar o tempo de execução de integração de SSIS do Azure a uma rede virtual do Azure."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: spelluru
ms.openlocfilehash: 8a58f55bd627594145661e1c8d5c1da360cd1e30
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Aderir a um tempo de execução de integração do Azure-SSIS a uma rede virtual
Tem de associar o tempo de execução de integração do Azure-SSIS (IR) a uma rede virtual do Azure (VNet) se uma das seguintes condições for verdadeira: 

- Está a alojar a base de dados do Catálogo de SSIS numa Instância Gerida do SQL Server (pré-visualização privada) que faz parte de uma VNet.
- Pretende ligar a arquivos de dados no local a partir de pacotes de SSIS em execução no integration runtime do Azure-SSIS.

 O Azure Data Factory versão 2 (Pré-visualização) permite-lhe associar o seu runtime de integração Azure-SSIS a uma VNet clássica. Atualmente, VNet do Azure Resource Manager ainda não é suportado. No entanto, pode trabalhá-lo em torno conforme indicado na secção seguinte. 

 > [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço Data Factory, que está disponível em geral (GA), veja [Data Factory version 1 documentation](v1/data-factory-introduction.md) (Documentação da versão 1 do Data Factory).

Se acedem a pacotes SSIS arquivos de dados de nuvem pública apenas, não precisa de associar Azure SSIS IR para uma VNet. Se acedem a pacotes SSIS arquivos de dados no local, tem de associar Azure SSIS IR para uma VNet que está ligada à rede no local. Se o catálogo de SSIS está alojado na base de dados do SQL do Azure que não esteja na VNet, terá de abrir as portas adequadas. Se o catálogo de SSIS estiver alojado no Azure geridos instância do SQL que está a ser uma VNet clássica, pode associar Azure SSIS IR para a mesma VNet clássica (ou) uma VNet clássica diferente que tenha uma ligação de VNet de classe para clássico com um que tenha a instância de gerida do Azure SQL. As secções seguintes fornecem mais detalhes.  

## <a name="access-on-premises-data-stores"></a>Acesso local arquivos de dados
Se os pacotes SSIS aceder aos arquivos de dados no local, associe o seu tempo de execução de integração do Azure SSIS para uma VNet que está ligada à sua rede no local. Eis alguns pontos importantes a ter em atenção: 

- Se não existir nenhum VNet existente ligados à sua rede no local, primeiro crie um [VNet clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para o tempo de execução de integração do Azure-SSIS associar. Em seguida, configure um site para site [ligação de gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)/[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) ligação a partir desse VNet à sua rede no local.
- Se existir uma VNet clássica existente à sua rede no local na mesma localização do seu tempo de execução de integração de SSIS do Azure, pode associar o tempo de execução de integração do Azure SSIS ao mesmo.
- Se existir uma existente clássica VNet ligado à sua rede no local numa localização diferente a partir do seu tempo de execução de integração de SSIS do Azure, pode criar primeiro um [VNet clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para o tempo de execução de integração do Azure-SSIS associar. Em seguida, configure um [VNet clássica para clássico](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) ligação.
- Se existir uma VNet do Gestor de recursos do Azure existente ligados à sua rede no local, primeiro crie um [VNet clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para o tempo de execução de integração do Azure-SSIS associar. Em seguida, configure um [clássica para o Azure Resource Manager VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) ligação.

## <a name="domain-name-services-server"></a>Servidor de serviços de nome de domínio 
Se precisar de utilizar o seu próprio servidor de serviços de nomes de domínio (DNS) numa VNet associada ao seu tempo de execução de integração de SSIS do Azure, siga as orientações para [Certifique-se de que os nós do seu tempo de execução de integração do Azure-SSIS na VNet podem resolver pontos finais Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="network-security-group"></a>Grupo de segurança de rede
Se precisar de implementar o grupo de segurança de rede (NSG) numa VNet associada ao seu tempo de execução de integração do Azure SSIS, permita a entrada/saída traffics através as seguintes portas:

| Portas | Direção | Protocolo de transporte | Objetivo | Destino origem entrada/saída |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100<br/>20100<br/>30100  | Entrada | TCP | Serviços do Azure utilizam estas portas para comunicar com os nós do seu tempo de execução de integração do Azure-SSIS na VNet. | Internet | 
| 443 | Saída | TCP | Os nós do seu tempo de execução de integração do Azure-SSIS na VNet utilizam esta porta para aceder aos serviços do Azure, por exemplo, de armazenamento do Azure, Hub de eventos, etc. | INTERNET | 
| 1433<br/>11000-11999<br/>14000-14999  | Saída | TCP | Os nós do seu tempo de execução de integração do Azure-SSIS na VNet utilizam estas portas para aceder a SSISDB alojada pelo seu servidor de SQL Database do Azure (não aplicável a SSISDB hospedada geridos instância do SQL do Azure). | Internet | 

## <a name="script-to-configure-vnet"></a>Script para configurar a VNet 
Pode utilizar o script do PowerShell orientado [neste artigo](create-azure-ssis-integration-runtime.md) para aprovisionar um tempo de execução de integração do Azure-SSIS numa VNet. O script configura automaticamente definições e as permissões de VNet, para que possa juntar-Azure-SSIS integração runtime para a VNet.  


## <a name="use-portal-to-configure-vnet"></a>Utilize o portal para configurar a VNet
Executar o script é a forma mais fácil de configurar a VNet. Se não tiver acesso para configurar que a configuração automática de VNet a falhar, o proprietário dessa VNet / pode configurá-las manualmente nos passos seguintes:

### <a name="find-the-resource-id-for-your-azure-vnet"></a>Localize o ID de recurso para o seu Azure VNet.
 
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **mais serviços**. Filtrar e selecione **redes virtuais (clássicas)**.
3. Filtrar e selecione o **rede virtual** na lista. 
4. Na página de rede (clássica) Virtual, selecione **propriedades**. 

    ![ID de recurso de VNet clássico](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. Clique no botão Copiar para o **ID de recurso** para copiar o ID de recurso para a rede clássico para a área de transferência. Guarde o ID da área de transferência no OneNote ou um ficheiro.
6. Clique em **sub-redes** no menu da esquerda e certifique-se de que o número de **endereços disponíveis** é maior do que os nós no seu tempo de execução de integração de SSIS do Azure.

    ![Número de endereços disponíveis na VNet](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. Associar **MicrosoftAzureBatch** para **clássico contribuinte de Máquina Virtual** função para a VNet. 
    1. Clique em controlo de acesso (IAM), no menu da esquerda e clique em **adicionar** na barra de ferramentas.
    
        ![Controlo de acesso -> Adicionar](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png) 
    2. No **adicionar permissões** página, selecione **clássico contribuinte de Máquina Virtual** para **função**. Tipo **MicrosoftAzureBatch** no **selecione** caixa de texto e, em seguida, selecione **MicrosoftAzureBatch** da lista de resultados da pesquisa. 
    
        ![Adicionar permissões – pesquisa](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)
    3. Clique em Guardar para guardar as definições e fechar a página.
    
        ![Guardar as definições de acesso](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)
    4. Confirme que vê **MicrosoftAzureBatch** na lista de contribuintes.
    
        ![Confirmar AzureBatch de acesso](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)
5. Certifique-se de que esse fornecedor do Azure Batch está registado na subscrição do Azure com a VNet ou registar o fornecedor do Azure Batch. Se já tiver uma conta do Azure Batch na sua subscrição, em seguida, a sua subscrição está registada para o Azure Batch.
    1. No portal do Azure, clique em **subscrições** no menu da esquerda. 
    2. Selecione o **subscrição**. 
    3. Clique em **fornecedores de recursos** à esquerda e confirme que `Microsoft.Batch` é um fornecedor registado. 
    
        ![confirmação-batch-registado](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    Se não vir `Microsoft.Batch` está na lista, para registá-lo, [criar uma conta do Azure Batch vazia](../batch/batch-account-create-portal.md) na sua subscrição. Pode eliminá-lo mais tarde. 
         


## <a name="next-steps"></a>Passos seguintes
Para mais informações sobre o tempo de execução de SSIS do Azure, consulte os tópicos seguintes: 

- [Tempo de execução do Azure-SSIS integração](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações concetuais sobre tempos de execução de integração em geral, incluindo o IR. SSIS do Azure 
- [Tutorial: implementar pacotes do SSIS no Azure](tutorial-deploy-ssis-packages-azure.md). Este artigo disponibiliza instruções passo a passo para criar um IR Azure-SSIS e utiliza uma base de dados da Base de Dados SQL do Azure para alojar o catálogo do SSIS. 
- [How to: Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Como criar um runtime de integração do Azure-SSIS). Este artigo é uma continuação do tutorial e mostra instruções sobre como utilizar a Instância Gerida do SQL do Azure (pré-visualização privada) e associar o IR a uma VNet. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Monitorizar um IR Azure-SSIS) Este artigo mostra como obter informações sobre um IR Azure-SSIS e descrições dos estados nas informações devolvidas. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como parar, iniciar ou remover um IR Azure-SSIS. Mostra também como aumentá-lo horizontalmente mediante a adição de mais nós ao mesmo. 
