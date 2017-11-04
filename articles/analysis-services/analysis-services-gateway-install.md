---
title: Instalar o gateway de dados no local | Microsoft Docs
description: Saiba como instalar e configurar um gateway de dados no local.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 10/30/2017
ms.author: owend
ms.openlocfilehash: 9ba36c4cf932cab2e449b12a307639184feab049
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Instalar e configurar um gateway de dados no local
Um gateway de dados no local é necessário quando um ou mais servidores do Azure Analysis Services na mesma região ligarem a origens de dados no local. Para saber mais sobre o gateway, consulte [gateway de dados no local](analysis-services-gateway.md).

## <a name="prerequisites"></a>Pré-requisitos
**Requisitos mínimos:**

* 4.5 do .NET framework
* versão de 64 bits do Windows 7 / Windows Server 2008 R2 (ou posterior)

**Recomendado:**

* CPU de 8 núcleos
* 8 GB de memória
* versão de 64 bits do Windows 2012 R2 (ou posterior)

**Considerações importantes sobre:**

* Durante a configuração, quando registar o gateway com o Azure, a região predefinida para a sua subscrição está selecionada. Pode escolher uma região diferente. Se tiver servidores na região mais do que um, tem de instalar um gateway para cada região. 
* O gateway não pode ser instalado num controlador de domínio.
* Apenas um gateway pode ser instalado num único computador.
* Instale o gateway num computador que permanece no e não aceda a suspender.
* Não instale o gateway num computador wirelessly ligado à sua rede. Desempenho pode ser o seu.
* Inicie sessão no Azure com uma conta no Azure AD para o mesmo [inquilino](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) como a subscrição que está a registar o gateway. Azure B2B contas (convidado) não são suportadas quando instalar e registar um gateway.


## <a name="download"></a>Transferir
 [Transfira o gateway](https://aka.ms/azureasgateway)

## <a name="install"></a>Instalar

1. Execute a configuração.

2. Selecione uma localização, aceite os termos de licenciamento e, em seguida, clique em **instalar**.

   ![Instalar os termos de localização e a licença](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Inicie sessão no Azure. A conta tem de ser do Azure Active Directory seu inquilino. Esta conta é utilizada para o administrador do gateway. Azure B2B contas (convidado) não são suportadas quando instalar e registar o gateway.

   ![Iniciar sessão no Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Se iniciar sessão com uma conta de domínio, está mapeada para a sua conta organizacional no Azure AD. A conta institucional é utilizada como o administrador do gateway.

## <a name="register"></a>Registar
Para criar um recurso de gateway no Azure, tem de registar a instância local instalado com o serviço de nuvem do Gateway. 

1.  Selecione **registar um novo gateway neste computador**.

    ![Registar](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Escreva uma chave de recuperação e o nome do seu gateway. Por predefinição, o gateway utiliza a região de predefinida da sua subscrição. Se tem de selecionar uma região diferente, selecione **alteração região**.

    > [!IMPORTANT]
    > Guarde a chave de recuperação num local seguro. A chave de recuperação é necessário por ordem para aquisições, migrar ou restaurar um gateway. 

   ![Registar](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Crie um recurso de gateway do Azure
Depois de ter instalado e registado o gateway, terá de criar um recurso de gateway na sua subscrição do Azure. Inicie sessão no Azure com a mesma conta que utilizou quando registar o gateway.

1. No portal do Azure, clique em **criar um novo serviço** > **integração empresarial com** > **gateway de dados no local**  >   **Criar**.

   ![Crie um recurso de gateway](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. No **criar gateway de ligação**, introduza estas definições:

    * **Nome**: introduza um nome para o recurso do gateway. 

    * **Subscrição**: selecione a subscrição do Azure para associar o recurso do gateway. 
   
      A subscrição predefinida baseia-se a conta do Azure que utilizou para iniciar sessão.

    * **Grupo de recursos**: crie um grupo de recursos ou selecione um existente.

    * **Localização**: selecione a região registado o seu gateway.

    * **Nome de instalação**: se a instalação do gateway não estiver selecionada, selecione o gateway registado. 

    Quando tiver terminado, clique em **criar**.

## <a name="connect-servers"></a>Ligar servidores para o recurso de gateway

1. Na descrição geral da sua do Azure Analysis Services server, clique em **Gateway de dados no local**.

   ![Ligar o servidor de gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. No **escolher um Gateway de dados no local para estabelecer a ligação**, selecione o recurso do gateway e, em seguida, clique em **gateway selecionado Connect**.

   ![Ligar o servidor para o recurso do gateway](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Se o gateway não aparecer na lista, o servidor é provavelmente não na mesma região que a região que especificou quando registar o gateway. 

E já está. Se precisar de abrir as portas ou fazer qualquer resolução de problemas, lembre-se de que veja [gateway de dados no local](analysis-services-gateway.md).

## <a name="next-steps"></a>Passos seguintes
* [Gerir do Analysis Services](analysis-services-manage.md)   
* [Obter dados a partir do Azure Analysis Services](analysis-services-connect.md)
