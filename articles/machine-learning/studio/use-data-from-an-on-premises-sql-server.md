---
title: Utilizar um SQL Server no local no Azure Machine Learning | Microsoft Docs
description: "Utilize dados a partir de uma base de dados do SQL Server no local para efetuar análises avançadas com o Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 08e4610d-02b6-4071-aad7-a2340ad8e2ea
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: garye;krishnan
ms.openlocfilehash: 79ae5cd78ce07fcc84be49c2693773d58a15771e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="perform-advanced-analytics-with-azure-machine-learning-using-data-from-an-on-premises-sql-server-database"></a>Fazer análises avançadas com o Azure Machine Learning ao utilizar dados de uma base de dados do SQL Server no local

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

Muitas vezes, as empresas que funcionam com dados no local pretende tirar partido da escala e a agilidade da nuvem para as respetivas cargas de trabalho de aprendizagem. Mas não pretende interromper as respetivas atual processos empresariais e fluxos de trabalho, movendo os seus dados no local para a nuvem. O Azure Machine Learning suporta agora ao ler os dados a partir de uma base de dados do SQL Server no local e, em seguida, formação e classificação de um modelo com estes dados. Já não tem de copiar e sincronizar os dados entre a nuvem e o servidor no local manualmente. Em vez disso, o **importar dados** módulo no Azure Machine Learning Studio agora pode ler diretamente a partir da sua base de dados do SQL Server no local para fins de formação e classificação de tarefas.

Este artigo fornece uma descrição geral de como entrada no local dados do SQL Server no Azure Machine Learning. Pressupõe que está familiarizado com conceitos de Azure Machine Learning áreas de trabalho, módulos, conjuntos de dados, as experimentações *etc.*.

> [!NOTE]
> Esta funcionalidade não está disponível para áreas de trabalho gratuitas. Para obter mais informações sobre preços do Machine Learning e camadas, consulte [preços do Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="install-the-microsoft-data-management-gateway"></a>Instalar o Microsoft Data Management Gateway
Para aceder a uma base de dados do SQL Server no local no Azure Machine Learning, terá de transferir e instalar o Microsoft Data Management Gateway. Quando configurar a ligação de gateway no Machine Learning Studio, terá a oportunidade para transferir e instalar o gateway com o **transferência e gateway de dados de registo** diálogo descritas abaixo.

Também pode instalar o Data Management Gateway antecedência ao descarregar e executar o programa de configuração porque o pacote MSI do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717).
Escolha a versão mais recente, a seleção de 32 bits ou 64 bits conforme apropriado para o seu computador. O MSI também pode ser utilizado para atualizar um Gateway de gestão de dados existente para a versão mais recente, com todas as definições preservadas.

O gateway tem os seguintes pré-requisitos:

* As versões de sistema operativo do Windows suportadas são Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.
* A configuração recomendada para a máquina do gateway é, pelo menos, 2 GHz, 4 núcleos, 8GB de RAM e o disco de 80GB.
* Se o computador anfitrião hibernates, o gateway não responde a pedidos de dados. Por conseguinte, configure uma esquema de energia adequadas no computador antes de instalar o gateway. Se o computador está configurado para hibernação, a instalação do gateway apresenta uma mensagem.
* Porque a atividade de cópia ocorre uma frequência específica, a utilização de recursos (CPU, memória) na máquina segue também o mesmo padrão das horas de ponta e tempos de inatividade. Utilização de recursos também depende descontos elevados a quantidade de dados a ser movidos. Quando várias tarefas de cópia estão em curso, irá reparar a utilização de recursos, aceda a cópia de segurança durante as horas de pico. Enquanto a configuração mínima listada acima é tecnicamente suficiente, poderá ter uma configuração com mais recursos do que a configuração mínima consoante a carga específica para o movimento de dados.

Considere o seguinte quando configurar e utilizar um Data Management Gateway:

* Pode instalar apenas uma instância do Data Management Gateway num único computador.
* Pode utilizar um único gateway para várias origens de dados no local.
* Pode ligar a vários gateways em computadores diferentes para a mesma origem de dados no local.
* Configurar um gateway para a área de trabalho apenas um de cada vez. Atualmente, os gateways não podem ser partilhados em áreas de trabalho.
* Pode configurar vários gateways para uma única área de trabalho. Por exemplo, poderá utilizar um gateway que está ligado a origens de dados de teste durante o desenvolvimento e um gateway de produção quando estiver pronto para operacionalizar.
* O gateway não precisa de estar no mesmo computador como origem de dados. Mas, permanecendo próximo à origem de dados reduz o tempo para o gateway ligar à origem de dados. Recomendamos que instale o gateway num computador diferente daquele que aloja a origem de dados no local para que a origem de dados e de gateway não competem ter para os recursos.
* Se já tiver um gateway instalado no seu computador, que serve o Power BI ou do Azure Data Factory cenários, instale um gateway separado para o Azure Machine Learning noutro computador.

  > [!NOTE]
  > Não é possível executar o Data Management Gateway e o Power BI Gateway no mesmo computador.
  >
  >
* Tem de utilizar o Data Management Gateway para o Azure Machine Learning, mesmo se estiver a utilizar o Azure ExpressRoute para outros dados. Deve tratar a origem de dados como uma origem de dados no local (que é protegido por uma firewall), mesmo quando a utiliza o ExpressRoute. Utilize o Data Management Gateway para estabelecer conectividade entre o Machine Learning e a origem de dados.

Pode encontrar informações detalhadas sobre os pré-requisitos da instalação, os passos de instalação e sugestões de resolução de problemas no artigo [Data Management Gateway](../../data-factory/v1/data-factory-data-management-gateway.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-idtoc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Dados de entrada da sua base de dados do SQL Server no local para o Azure Machine Learning
Esta explicação passo a passo, irá configurar um Data Management Gateway numa área de trabalho do Azure Machine Learning, configurá-lo e, em seguida, ler dados a partir de uma base de dados do SQL Server no local.

> [!TIP]
> Antes de começar, desativar o Bloqueador de janelas de pop-up do browser para `studio.azureml.net`. Se estiver a utilizar o browser Google Chrome, transfira e instale um dos vários plug-ins disponíveis no Google Chrome WebStore [clique uma vez extensão de aplicação](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
>

### <a name="step-1-create-a-gateway"></a>Passo 1: Criar um gateway
O primeiro passo consiste em criar e configurar o gateway para aceder à sua base de dados do SQL Server no local.

1. Inicie sessão no [Azure Machine Learning Studio](https://studio.azureml.net/Home/) e selecione a área de trabalho à qual pretende trabalhar.
2. Clique no **definições** painel da esquerda e, em seguida, clique no **GATEWAYS de dados** separador no topo.
3. Clique em **novo GATEWAY de dados** na parte inferior do ecrã.

    ![Novo Gateway de dados](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. No **novo gateway de dados** caixa de diálogo, introduza o **nome do Gateway** e, opcionalmente, adicione um **Descrição**. Clique na seta no canto inferior direito para ir para o passo seguinte da configuração.

    ![Introduzir descrição e nome de gateway](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. A transferência e registar dados gateway caixa de diálogo, copie a chave de registo de GATEWAY para a área de transferência.

    ![Transferir e registar o gateway de dados](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Se ainda não tenham transferido e instalado o Microsoft Data Management Gateway, em seguida, clique em **transferências o data management gateway**. Isto leva-o para a Microsoft Download Center onde pode selecionar a versão do gateway que precisa, transfira-o e instalá-lo. Pode encontrar informações detalhadas sobre os pré-requisitos da instalação, os passos de instalação e sugestões de resolução de problemas nas secções início do artigo [mover dados entre origens no local e nuvem com o Data Management Gateway](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md).
7. Depois do gateway está instalado, o Gestor de configuração do Data Management Gateway será aberto e **registar gateway** é apresentada a caixa de diálogo. Colar o **chave de registo de Gateway** que copiou para a área de transferência e clique em **registar**.
8. Se já tiver um gateway instalado, execute o Gestor de configuração do Data Management Gateway. Clique em **alterar chave**, cole o **chave de registo de Gateway** copiados para a área de transferência no passo anterior e clique em **OK**.
9. Quando a instalação estiver concluída, o **registar gateway** é apresentada a caixa de diálogo para o Microsoft Data Management Gateway Configuration Manager. Cole a chave de registo de GATEWAY que copiou para a área de transferência num passo anterior e clique em **registar**.

    ![Registar o gateway](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. A configuração do gateway está concluída quando os valores seguintes estão definidos no **home page** separador no Microsoft Data Management Gateway do Configuration Manager:

    * **O nome do gateway** e **nome da instância** estão definidos para o nome do gateway.
    * **Registo** está definido como **registada**.
    * **Estado** está definido como **iniciado**.
    * Barra de estado na apresenta a parte inferior **ligado ao serviço de nuvem do Data Management Gateway** juntamente com uma marca de verificação verde.

      ![Gestor de Gateway de gestão de dados](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Azure Machine Learning Studio também obtém atualizado quando o registo é concluída com êxito.

    ![Registo de gateway com êxito](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. No **transferir e registar o gateway de dados** caixa de diálogo, clique na marca de verificação para concluir a configuração. O **definições** página apresenta o estado do gateway como "Online". No painel da direita, encontrará estado e outras informações úteis.

    ![Definições do gateway](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. No Gestor de configuração de Gateway do Microsoft Data Management mudar para o **certificado** separador. O certificado especificado neste separador é utilizado para encriptar/desencriptar as credenciais para o arquivo de dados no local que especificou no portal. Este certificado é o certificado predefinido. A Microsoft recomenda alterar isto para os seus próprios certificados que criar cópias de segurança no seu sistema de gestão de certificados. Clique em **alteração** para utilizar em vez disso, o seu próprio certificado.

    ![Certificado de gateway de alteração](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. (opcional) Se pretender ativar o registo verboso para resolver problemas com o gateway, no Gestor de configuração de Gateway do Microsoft Data Management mudar para o **diagnóstico** separador e verifique o **ativar registo verboso para fins de resolução de problemas** opção. As informações de registo podem ser encontradas no Visualizador de eventos do Windows, sob o **registos de serviços e aplicações**  - &gt; **Data Management Gateway** nós. Também pode utilizar o **diagnóstico** separador para testar a ligação a uma origem de dados no local utilizando o gateway.

    ![Ativar o registo verboso](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Este passo conclui o processo de configuração do gateway no Azure Machine Learning.
Agora, está pronto a utilizar os dados no local.

Pode criar e configurar vários gateways no Studio para cada área de trabalho. Por exemplo, pode ter um gateway que pretende ligar às suas origens de dados de teste durante o desenvolvimento e um gateway diferentes para as origens de dados de produção. O Azure Machine Learning dá-lhe a flexibilidade para configurar a vários gateways consoante o seu ambiente empresarial. Atualmente, não é possível partilhar um gateway entre as áreas de trabalho e apenas um gateway pode ser instalado num único computador. Para obter mais informações, consulte [mover dados entre origens no local e nuvem com o Data Management Gateway](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Passo 2: Utilizar o gateway para ler dados a partir de uma origem de dados no local
Depois de configurar o gateway, pode adicionar um **importar dados** módulo para uma experimentação que entradas os dados da base de dados do SQL Server no local.

1. No Machine Learning Studio, selecione o **EXPERIMENTAÇÕES** separador, clique em **+ novo** no canto inferior esquerdo e selecione **experimentação em branco** (ou selecione uma das várias experimentações de exemplo disponíveis).
2. Localize e arraste o **importar dados** módulo para a tela de experimentação.
3. Clique em **guardar como** abaixo da tela. Introduza "Azure local no SQL Server Tutorial do Machine Learning" para o nome da experimentação, selecione a área de trabalho e clique em de **OK** marca de verificação.

   ![Guardar experimentação com um novo nome](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Clique no **importar dados** módulo para a selecionar, em seguida, no **propriedades** painel à direita da tela, selecione "Base de dados do SQL Server no local" o **origem de dados** na lista pendente.
5. Selecione o **gateway de dados** instalado e registado. Pode configurar o outro gateway ao selecionar "(adicionar novo Gateway de dados...)".

   ![Selecione o gateway de dados para o módulo de importar dados](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Introduza o SQL **nome do servidor de base de dados** e **nome de base de dados**, juntamente com o SQL **consulta de base de dados** que pretende executar.
7. Clique em **introduza valores** em **nome de utilizador e palavra-passe** e introduza as credenciais da sua base de dados. Pode utilizar a autenticação integrada do Windows ou a autenticação de servidor de SQL consoante o modo como o SQL Server no local está configurado.

   ![Introduza as credenciais da base de dados](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   As mensagem "valores necessários" as alterações a "conjunto de valores" com uma marca de verificação verde. Basta introduzir as credenciais de uma vez, a menos que as informações de base de dados ou a palavra-passe é alterada. O Azure Machine Learning utiliza o certificado fornecido quando instalou o gateway para encriptar as credenciais na nuvem. Azure nunca armazena as credenciais no local sem encriptação.

   ![Importar as propriedades do módulo de dados](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Clique em **executar** para executar a experimentação.

Depois da experimentação ser concluída em execução, pode visualizar os dados importados da base de dados clicando na porta de saída do **importar dados** módulo e selecionando **visualizar**.

Depois de concluir a desenvolver a sua experimentação, pode implementar e operacionalizar o seu modelo. Base de dados utilizando o serviço de execução do Batch, dados do SQL Server no local configurada no **importar dados** módulo será ler e utilizado para classificação. Apesar de poder utilizar o serviço de resposta do pedido para a classificação de dados no local, a Microsoft recomenda a utilização de [suplemento do Excel](excel-add-in-for-web-services.md) em vez disso. Atualmente, escrever para uma base de dados do SQL Server no local através de **exportar dados** não é suportado nas suas experimentações ou publicado web serviços.
