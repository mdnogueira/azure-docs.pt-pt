---
title: "Implementar o serviço de aplicações num ambiente offline: pilha do Azure | Microsoft Docs"
description: "Orientações detalhadas sobre como implementar o serviço de aplicações num ambiente desligado de pilha do Azure protegidos pelo AD FS."
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2017
ms.author: anwestg
ms.openlocfilehash: d2a9b9fbe2a057a6d36e80c89af83a543e90d3be
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2017
---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>Adicionar um fornecedor de recursos do serviço de aplicações para um ambiente desligado de pilha do Azure protegido pelo AD FS

Ao seguir as instruções neste artigo, pode instalar o [fornecedor de recursos do serviço de aplicações](azure-stack-app-service-overview.md) num ambiente de pilha do Azure é:
- não ligado à internet
- protegidos por serviços de Federação do Active Directory (AD FS).

Para adicionar o fornecedor de recursos do serviço de aplicações para a sua implementação do Azure pilha offline, tem de concluir estas tarefas de nível superior:

1. Concluir o [os passos dos pré-requisitos](azure-stack-app-service-before-you-get-started.md) (como comprar certificados, o que pode demorar de alguns dias para receber).
2. [Transferir e extraia os ficheiros de instalação e do programa auxiliar](azure-stack-app-service-before-you-get-started.md) a uma máquina ligada à internet.
3. Crie um pacote de instalação offline.
4. Execute o ficheiro de instalador appservice.exe.

## <a name="create-an-offline-installation-package"></a>Criar um pacote de instalação offline

Para implementar o serviço de aplicações num ambiente desligado, primeiro tem de criar um pacote de instalação offline num computador que está ligado à Internet.

1. Execute o instalador de AppService.exe num computador que está ligado à Internet.

2. Clique em **avançadas** > **criar pacote de instalação offline**.

    ![Instalador do serviço de aplicações](media/azure-stack-app-service-deploy-offline/image01.png)   

3. O instalador do serviço de aplicações, cria um pacote de instalação offline e apresenta o caminho para o mesmo. Pode clicar em **Abrir pasta** para abrir a pasta no Explorador de ficheiros.

    ![Instalador do serviço de aplicações](media/azure-stack-app-service-deploy-offline/image02.png)   

4. Copie o instalador (AppService.exe) e o pacote de instalação offline para a máquina de anfitrião de pilha do Azure.

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>Concluir a instalação offline do serviço de aplicações na pilha do Azure

1. No computador anfitrião do Azure pilha desligado, run appservice.exe azurestack\clouadmin.

2. Clique em **avançadas** > **concluir a instalação offline**.

    ![Instalador do serviço de aplicações](media/azure-stack-app-service-deploy-offline/image03.png)   

3. Navegue para a localização do pacote de instalação offline que criou anteriormente e, em seguida, clique em **seguinte**.

    ![Instalador do serviço de aplicações](media/azure-stack-app-service-deploy-offline/image04.png)   

4. Reveja e aceite os termos de licenciamento de Software Microsoft e, em seguida, clique em **seguinte**.

5. Reveja e aceite os termos de licença de terceiros e, em seguida, clique em **seguinte**.

6. Certifique-se de que as informações de configuração do serviço de aplicações em nuvem estão corretas. Se utilizou as predefinições durante a implementação do Kit de desenvolvimento de pilha do Azure, pode aceitar os valores predefinidos aqui. No entanto, se as opções personalizado quando implementou a pilha do Azure, tem de editar os valores nesta janela para refletir que. Por exemplo, se utilizar o mycloud.com de sufixo de domínio, o ponto final tem de alterar a management.mycloud.com. Depois de confirmar as suas informações, clique em **seguinte**.

    ![Instalador do serviço de aplicações](media/azure-stack-app-service-deploy/image02.png)

7. Na página seguinte:
    1. Clique em de **Connect** junto ao **subscrições de pilha do Azure** caixa.
        - Se estiver a utilizar o Azure Active Directory (Azure AD), introduza a conta de administrador do Azure AD e a palavra-passe que forneceu quando implementou a pilha do Azure. Clique em **sessão**.
        - Se estiver a utilizar serviços de Federação do Active Directory (AD FS), forneça a sua conta de administrador. Por exemplo, cloudadmin@azurestack.local. Introduza a palavra-passe e clique em **sessão**.
    2. No **subscrições de pilha do Azure** caixa, selecione a sua subscrição.
    3. No **localizações de pilha do Azure** caixa, selecione a localização que corresponde à região estiver a implementar. Por exemplo, seleccione **local** se a implementar o Kit de desenvolvimento de pilha do Azure.
    4. Introduza um **nome do grupo de recursos** para a sua implementação do serviço de aplicações. Por predefinição, está definido como **APPSERVICE LOCAL**.
    5. Introduza o **nome da conta de armazenamento** que pretende que o serviço de aplicações para criar como parte da instalação. Por predefinição, está definido como **appsvclocalstor**.
    6. Clique em **Seguinte**.

    ![Instalador do serviço de aplicações](media/azure-stack-app-service-deploy/image03.png)

8. Introduza as informações para a partilha de ficheiros e, em seguida, clique em **seguinte**. O endereço da partilha de ficheiros tem de utilizar o nome de domínio completamente qualificado do servidor de ficheiros, por exemplo \\\appservicefileserver.local.cloudapp.azurestack.external\websites ou o endereço IP, por exemplo \\\10.0.0.1\websites.

    ![Instalador do serviço de aplicações](media/azure-stack-app-service-deploy/image04.png)

9. Na página seguinte:
    1. No **ID da aplicação de identidade** box, introduza o GUID para a aplicação estiver a utilizar para a identidade.
    2. No **ficheiro de certificado de identidade aplicação** caixa, introduza (ou navegue até à) a localização do ficheiro de certificado.
    3. No **palavra-passe de certificado de identidade aplicação** caixa, introduza a palavra-passe do certificado. Esta palavra-passe é aquele que anotou quando utilizou o script para criar os certificados.
    4. No **ficheiro de certificado de raiz do Azure Resource Manager** caixa, introduza (ou navegue até à) a localização do ficheiro de certificado.
    5. Clique em **Seguinte**.

    ![Instalador do serviço de aplicações](media/azure-stack-app-service-deploy/image05.png)

10. Para cada uma a três caixas de ficheiro de certificado, clique em **procurar** e navegue para o ficheiro de certificado adequado e escreva uma palavra-passe. Estes certificados são aqueles que criou no [passo de certificados necessários criar](azure-stack-app-service-deploy.md). Clique em **seguinte** após introduzir todas as informações.

    | Box | Exemplo de nome de ficheiro de certificado |
    | --- | --- |
    | **Ficheiro de certificado SSL do serviço de aplicações predefinido** | \_. appservice.local.AzureStack.external.pfx |
    | **Ficheiro de certificado SSL de API do serviço de aplicações** | api.appservice.local.AzureStack.external.pfx |
    | **Ficheiro de certificado de SSL de publicador do serviço de aplicações** | ftp.appservice.local.AzureStack.external.pfx |

    Se utilizou um sufixo de domínio diferente ao criar os certificados, não utilizem os nomes de ficheiro de certificado *local. AzureStack.external*. Em alternativa, utilize as informações de domínio personalizado.

    ![Instalador do serviço de aplicações](media/azure-stack-app-service-deploy/image06.png)    

11. Introduza os detalhes do SQL Server para a instância de servidor utilizada para alojar as bases de dados do fornecedor de recursos do serviço de aplicações e, em seguida, clique em **seguinte**. O instalador valida as propriedades de ligação do SQL Server.

    ![Instalador do serviço de aplicações](media/azure-stack-app-service-deploy/image07.png)    

12. Reveja as opções de SKU e a instância de função. As predefinições são preenchidas com o número mínimo de instância e o SKU mínimo para cada função numa implementação ASDK. É fornecido um resumo dos requisitos vCPU e memória para o ajudar a planear a implementação. Depois de efetuar as seleções, clique em **seguinte**.

     > [!NOTE]
     > Para implementações de produção, seguir as orientações no [planeamento de capacidade de funções de servidor do App Service do Azure na pilha de Azure](azure-stack-app-service-capacity-planning.md).
     > 
     >

    | Função | Instâncias mínimas | SKU mínima | Notas |
    | --- | --- | --- | --- |
    | Controlador | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Gere e mantém o estado de funcionamento da nuvem do serviço de aplicações. |
    | Gestão | 1 | Standard_A2 - (vCPUs 2, 3584 MB) | Gere os pontos finais App Service do Azure Resource Manager e API, extensões portais (administrador inquilino, portal das funções) e o serviço de dados. Para suportar a ativação pós-falha, aumentar as instâncias recomendadas para 2. |
    | Publicador | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Publica conteúdo através da implementação web e FTP. |
    | FrontEnd | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Encaminha os pedidos a aplicações de serviço de aplicações. |
    | Trabalho partilhado | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Anfitriões ou aplicações API aplicações web e funções do Azure. Pode querer adicionar mais instâncias. Como um operador, pode definir a sua oferta e escolha qualquer camada SKU. As camadas tem de ter um mínimo de um vCPU. |

    ![Instalador do serviço de aplicações](media/azure-stack-app-service-deploy/image08.png)    

    > [!NOTE]
    > **Windows Server 2016 Core não é uma imagem de plataforma suportada para utilização com o Azure App Service na pilha de Azure**.

13. No **selecione a imagem de plataforma** caixa, selecione a imagem de máquina virtual de implementação do Windows Server 2016 das disponíveis no fornecedor de recursos de computação para a nuvem de serviço de aplicações. Clique em **Seguinte**.

14. Na página seguinte:
     1. Introduza o nome de utilizador de administrador de máquina virtual de função de trabalho e a palavra-passe.
     2. Introduza o nome de utilizador de administrador de máquina virtual de outras funções e a palavra-passe.
     3. Clique em **Seguinte**.

    ![Instalador do serviço de aplicações](media/azure-stack-app-service-deploy/image09.png)    

15. Na página de resumo:
    1. Certifique-se seleções que fez. Para efetuar alterações, utilize o **anterior** botões para visitar às páginas anteriores.
    2. Se as configurações estão corretas, selecione a caixa de verificação.
    3. Para iniciar a implementação, clique em **seguinte**.

    ![Instalador do serviço de aplicações](media/azure-stack-app-service-deploy/image10.png)    

16. Na página seguinte:
    1. Controle o progresso da instalação. Serviço de aplicações na pilha de Azure demora cerca de 60 minutos para implementar com base nas seleções predefinido.
    2. Depois do programa de instalação for concluída com êxito, clique em **saída**.

    ![Instalador do serviço de aplicações](media/azure-stack-app-service-deploy/image11.png)    


## <a name="validate-the-app-service-on-azure-stack-installation"></a>Validar o serviço de aplicações numa instalação de pilha do Azure

1. No portal de administração de pilha do Azure, aceda a **administração - App Service**.

2. Na descrição geral em estado, certifique-se que o **estado** mostra **todas as funções, estará pronto**.

    ![Gestão de serviço de aplicações](media/azure-stack-app-service-deploy/image12.png)    


## <a name="test-drive-app-service-on-azure-stack"></a>Testar o serviço de aplicações na pilha do Azure

Depois de implementar e registar o fornecedor de recursos do serviço de aplicações, testá-lo para se certificar de que os utilizadores podem implementar web e API apps.

> [!NOTE]
> Terá de criar uma oferta que tenha o espaço de nomes Microsoft. Web dentro do plano. Em seguida, terá de ter uma subscrição de inquilino subscreve esta oferta. Para obter mais informações, consulte [criar oferta](azure-stack-create-offer.md) e [criar plano](azure-stack-create-plan.md).
>
*Tem* ter uma subscrição de inquilino para criar aplicações que utilizam o serviço de aplicações na pilha do Azure. As capacidades de apenas um administrador de serviço pode ser no portal de administração estão relacionadas com a administração de fornecedor de recursos do App Service. Estas capacidades incluem adicionar capacidade, configurar origens de implementação e adicionar camadas de trabalho e SKUs.
>
A partir do technical preview terceiro criar web, a API e o Azure funciona aplicações, tem de utilizar o portal de inquilinos e ter uma subscrição de inquilino.

1. No portal de inquilinos pilha do Azure, clique em **novo** > **Web + móvel** > **aplicação Web**.

2. No **aplicação Web** painel, escreva um nome no **aplicação Web** caixa.

3. Em **grupo de recursos**, clique em **novo**. Escreva um nome no **grupo de recursos** caixa.

4. Clique em **plano do Serviço de Aplicações/Localização** > **Criar Novo**.

5. No **plano do App Service** painel, escreva um nome no **plano do App Service** caixa.

6. Clique em **escalão de preço** > **livres partilhados** ou **partilhados partilhados** > **selecione**  >   **OK** > **criar**.

7. Na sob um minuto, um mosaico para a nova aplicação web aparece no dashboard. Clique no mosaico.

8. No **aplicação Web** painel, clique em **procurar** para ver o Web site predefinido para esta aplicação.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Implementar um site WordPress, DNN ou Django (opcional)

1. No portal de inquilinos pilha do Azure, clique em  **+** , vá para o Azure Marketplace, implementar um site do Django e aguarde pela conclusão com êxito. A plataforma de web Django utiliza um ficheiro com base no sistema base de dados do. Não requer quaisquer fornecedores de recursos adicionais, tais como SQL Server ou MySQL.

2. Se implementou também um fornecedor de recursos do MySQL, pode implementar um site WordPress no Marketplace. Quando lhe for pedida para parâmetros de base de dados, introduza o nome de utilizador como  *User1@Server1* , com o nome de utilizador e o nome do servidor da sua preferência.

3. Se implementou também um fornecedor de recursos do SQL Server, pode implementar um site DNN do Marketplace. Quando lhe for pedida para parâmetros de base de dados, escolha uma base de dados no computador que executa o SQL Server que está ligada ao seu fornecedor de recursos.

## <a name="next-steps"></a>Passos seguintes

Também pode experimentar o outro [plataforma como dos serviços de serviço (PaaS)](azure-stack-tools-paas-services.md).

- [Fornecedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [Fornecedor de recursos de MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
