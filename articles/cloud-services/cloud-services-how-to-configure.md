---
title: "Como configurar um serviço em nuvem (portal clássico) | Microsoft Docs"
description: "Saiba como configurar os serviços em nuvem no Azure. Saiba como atualizar a configuração do serviço de nuvem e configurar o acesso remoto para instâncias de função."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 4902f79d-ea91-41ca-89a4-7c818180ee5f
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 39bb294c96ce0c12d91cf8b3488ac3e1a7b2f7b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-cloud-services"></a>Como configurar os serviços Cloud
> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-how-to-configure-portal.md)
> * [Portal Clássico do Azure](cloud-services-how-to-configure.md)
> 
> 

Pode configurar as definições utilizadas com mais frequência para um serviço em nuvem no portal clássico do Azure. Ou, se pretender atualizar diretamente os seus ficheiros de configuração, transfira um ficheiro de configuração de serviços para atualizar e, em seguida, carregue o ficheiro atualizado e atualize o serviço cloud com as alterações de configuração. De uma forma ou de outra, as atualizações de configuração são integradas em todas as instâncias de funções.

Portal clássico do Azure também lhe permite [ativar a ligação de ambiente de trabalho remoto para uma função na Cloud Services do Azure](cloud-services-role-enable-remote-desktop.md)

Azure apenas pode garantir uma disponibilidade do serviço de percentagem de 99,95 durante as atualizações de configuração se tiver, pelo menos, duas instâncias de função para cada função. Permite que uma máquina virtual processar pedidos de cliente, enquanto o outro está a ser atualizado. Para obter mais informações, consulte [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Alterar um serviço em nuvem
1. No [portal clássico do Azure](http://manage.windowsazure.com/), clique em **serviços em nuvem**, clique no nome do serviço de nuvem e, em seguida, clique em **configurar**.
   
    ![Página de configuração](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)
   
    No **configurar** página, pode configurar a monitorização, atualização de definições da função e escolha o sistema operativo convidado e família para instâncias de função. 
2. No **monitorização**, defina o nível de monitorização verboso ou mínima e configurar as cadeias de ligação de diagnóstico que são necessárias para a monitorização verboso.
3. Para funções de serviço (agrupadas por função), pode atualizar as seguintes definições:
   
    * **Definições** modifique os valores das definições de configuração diversas especificados no *ConfigurationSettings* elementos do serviço (. cscfg) do ficheiro de configuração.

    * **Certificados**  
        Altere o thumbprint do certificado que está a ser utilizado para encriptação SSL para uma função. Para alterar um certificado, primeiro tem de carregar o novo certificado (no **certificados** página). Em seguida, atualize o thumbprint na cadeia de certificados apresentado nas definições de função.
4. No **sistema operativo**, pode alterar a família do sistema operativo ou a versão para instâncias de função ou escolha **automática** para ativar as atualizações automáticas da versão atual do sistema operativo. As definições do sistema operativo aplicam-se para funções da web e funções de trabalho, mas não afetam as máquinas virtuais.
   
    Durante a implementação, a versão mais recente do sistema operativo está instalada em todas as instâncias de função e os sistemas operativos são atualizados automaticamente por predefinição. 
   
    Se for necessário para o seu serviço de nuvem ser executada numa versão de sistema operativo diferente devido aos requisitos de compatibilidade no seu código, pode escolher uma família de sistemas operativos e versão. Ao escolher uma versão de sistema operativo específico, são suspensas atualizações automáticas do sistema operativo para o serviço em nuvem. Terá de Certifique-se de que os sistemas operativos receber atualizações.
   
    Se resolver todos os problemas de compatibilidade com as suas aplicações com a versão mais recente do sistema operativo, pode ativar atualizações automáticas do sistema operativo, definindo a versão do sistema operativo como **automática**. 
   
    ![Definições de SO](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)
5. Para guardar as definições de configuração e emiti-las para as instâncias de função, clique em **guardar**. (Clique **rejeitar** para cancelar as alterações.) **Guardar** e **rejeitar** são adicionados à barra de comando depois de alterar uma definição.

## <a name="update-a-cloud-service-configuration-file"></a>Atualizar um ficheiro de configuração do serviço de nuvem
1. Transferir um ficheiro de configuração de serviço (. cscfg) de nuvem com a configuração atual. No **configurar** página para o serviço em nuvem, clique em **transferir**. Em seguida, clique em **guardar**, ou clique em **guardar como** para guardar o ficheiro.
2. Depois de atualizar o ficheiro de configuração de serviço, carregar e aplicar as atualizações de configuração:
   
   1. No **configurar** página, clique em **carregar**.
      
       ![Carregar a configuração](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)
   2. No **ficheiro de configuração**, utilize **procurar** para selecionar o ficheiro. cscfg atualizado.
   3. Se o serviço de nuvem contém quaisquer funções que tenham apenas uma instância, selecione o **aplicar a configuração, mesmo se uma ou mais funções contenham uma única instância** caixa de verificação para ativar as atualizações de configuração para as funções continuar.
      
       A menos que definir, pelo menos, duas instâncias de cada função, Azure não pode garantir que, pelo menos, 99,95 percentagem disponibilidade do serviço da nuvem durante as atualizações de configuração do serviço. Para obter mais informações, consulte [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/).
   4. Clique em **OK** (marca de verificação). 

## <a name="next-steps"></a>Passos seguintes
* Saiba como [implementar um serviço em nuvem](cloud-services-how-to-create-deploy.md).
* Configurar um [nome de domínio personalizado](cloud-services-custom-domain-name.md).
* [Gerir o serviço de nuvem](cloud-services-how-to-manage.md).
* [Ativar a ligação de ambiente de trabalho remoto para uma função nos serviços em nuvem do Azure](cloud-services-role-enable-remote-desktop.md)
* Configurar [certificados ssl](cloud-services-configure-ssl-certificate.md).

