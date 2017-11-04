---
title: Elevada disponibilidade com o data management gateway no Azure Data Factory | Microsoft Docs
description: "Este artigo explica como pode ampliar um data management gateway ao adicionar mais nós e escala até ao aumentar o número de tarefas simultâneas que podem ser executadas num nó."
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: fda3c7a9a369eec1b9033ee7077a5f3770647c9f
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>O Data Management Gateway - elevada disponibilidade e escalabilidade (pré-visualização)
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [autoalojado integração tempo de execução na versão 2](../create-self-hosted-integration-runtime.md). 


Este artigo ajuda-o a configurar solução elevada de disponibilidade e escalabilidade com o Data Management Gateway / integração.    

> [!NOTE]
> Este artigo pressupõe que já estiver familiarizado com as noções básicas do tempo de execução de integração (anteriores Data Management Gateway). Se não estiver, consulte o artigo [Data Management Gateway](data-factory-data-management-gateway.md).

>**Esta funcionalidade de pré-visualização é oficialmente suportada 2.12.xxxx.x de versão do Data Management Gateway em superior**. Certifique-se estiver a utilizar a versão 2.12.xxxx.x ou superior. Transfira a versão mais recente do Data Management Gateway [aqui](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Descrição geral
Pode associar os gateways de gestão de dados que são instalados em várias máquinas no local com um único gateway lógico do portal. Estas máquinas são denominadas **nós**. Pode ter até **quatro nós** associado um gateway lógico. Os benefícios de ter vários nós (máquinas no local com o gateway instalado) para um gateway lógico são:  

- Melhorar o desempenho de movimento de dados entre no local e nuvem arquivos de dados.  
- Se um de nós de ficar inativo, por alguma razão, outros nós ainda estão disponíveis para mover os dados. 
- Se um de nós tem de ser colocado offline para manutenção, outros nós ainda estão disponíveis para mover os dados.

Também pode configurar o número de **tarefas de movimento de dados simultâneas** que podem ser executadas num nó de aumentar verticalmente a capacidade de mover dados entre no local e nuvem arquivos de dados. 

No portal do Azure, pode monitorizar o estado de em nós, que ajuda a decidir se pretende adicionar ou remover um nó de gateway lógico. 

## <a name="architecture"></a>Arquitetura 
O diagrama seguinte fornece a descrição geral da arquitetura de escalabilidade e funcionalidade de disponibilidade do Data Management Gateway: 

![O Data Management Gateway - elevada disponibilidade e escalabilidade](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

A **gateway lógico** é o gateway de adicionar a uma fábrica de dados no portal do Azure. Anteriormente, pode associar apenas um computador do Windows no local com o Data Management Gateway instalado com um gateway lógico. Isto-local no computador gateway denomina-se um nó. Agora, pode associar até **quatro nós físicos** com um gateway lógico. Um gateway lógico com vários nós é chamado um **com vários nós gateway**.  

Todos os estes nós estejam **Active Directory**. Todos eles podem processar trabalhos de movimento de dados para mover dados entre no local e na nuvem arquivos de dados. Um de nós atuar como distribuidor e de trabalho. Outros nós nos grupos são nós de trabalho. A **dispatcher** nós obtém dados movimento tarefas/tarefas a partir do serviço em nuvem e dispatches-los a nós de trabalho (incluindo o próprio). A **trabalho** nó executa tarefas de movimento de dados para mover dados entre no local e na nuvem arquivos de dados. Todos os nós são trabalhadores. Apenas um nó pode ser distribuição e de trabalho.    

Normalmente, pode começar com um nó e **aumentar horizontalmente** para adicionar mais nós como nós de existentes são overwhelmed com a carga de movimento de dados. Também pode **aumentar verticalmente** a capacidade de movimento de dados de um nó de gateway aumento do número de tarefas simultâneas que podem ser executados no nó. Esta capacidade também está disponível com um gateway de nó único (mesmo quando a funcionalidade de escalabilidade e disponibilidade não está ativada). 

Um gateway com vários nós mantém as credenciais do arquivo de dados sincronizadas em todos os nós. Se existir um problema de conectividade do nó de nó, as credenciais podem ser sincronizadas. Quando definir as credenciais para um arquivo de dados no local que utiliza um gateway, guarda as credenciais no nó de trabalho/dispatcher. O dispatcher nó sincroniza-se com outros nós de trabalho. Este processo é conhecido como **credenciais sincronização**. O canal de comunicação entre os nós pode ser **encriptados** por um certificado SSL/TLS público. 

## <a name="set-up-a-multi-node-gateway"></a>Configurar um gateway de vários nós
Esta secção assume que já leu os seguintes dois artigos ou estiver familiarizado com conceitos nestes artigos: 

- [O Data Management Gateway](data-factory-data-management-gateway.md) -fornece uma descrição geral detalhada do gateway.
- [Mover dados entre no local e na nuvem arquivos de dados](data-factory-move-data-between-onprem-and-cloud.md) -contém instruções com instruções passo a passo para utilizar um gateway com um único nó.  

> [!NOTE]
> Antes de instalar um data management gateway num computador Windows no local, consulte os pré-requisitos listados [o artigo principal](data-factory-data-management-gateway.md#prerequisites).

1. No [instruções](data-factory-move-data-between-onprem-and-cloud.md#create-gateway), ao criar um gateway lógico, ativar o **elevada disponibilidade e escalabilidade** funcionalidade. 

    ![O Data Management Gateway - ativar a elevada disponibilidade e escalabilidade](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. No **configurar** página, utilize a **configuração Express** ou **configuração Manual** ligação para instalar um gateway no primeiro nó (uma máquina de Windows no local).

    ![O Data Management Gateway - configuração rápida ou manual](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Se utilizar a opção de configuração rápida, a comunicação de nó de nó é efetuada sem encriptação. O nome do nó é igual ao nome da máquina. Utilize a configuração manual se a comunicação de nó de nó tem de ser encriptados ou se pretender especificar um nome de nó à sua escolha. Os nomes de nó não podem ser editados mais tarde.
3. Se optar por **configuração rápida**
    1. Consulte a mensagem seguinte depois do gateway é instalado com êxito:

        ![O Data Management Gateway - êxito de configuração rápida](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Iniciar o Gestor de configuração do Data Management Gateway seguindo [estas instruções](data-factory-data-management-gateway.md#configuration-manager). Pode ver o nome do gateway, o nome do nó, estado, etc.

        ![O Data Management Gateway - instalação com êxito](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Se optar por **configuração manual**:
    1. Transferir o pacote de instalação a partir do Microsoft Download Center, executá-la para instalar o gateway no seu computador.
    2. Utilize o **chave de autenticação** do **configurar** página para registar o gateway.
    
        ![O Data Management Gateway - instalação com êxito](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. No **novo nó de gateway** página, pode fornecer um personalizado **nome** para o nó de gateway. Por predefinição, o nome de nó é igual ao nome da máquina.    

        ![O Data Management Gateway - especifique o nome](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. Na página seguinte, pode escolher se pretende **ativar a encriptação para comunicação de nó de nó**. Clique em **ignorar** para desativar a encriptação (predefinição).

        ![O Data Management Gateway - ativar encriptação](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Só é suportada a alteração do modo de encriptação quando tiver um nó de gateway único no gateway lógico. Para alterar o modo de encriptação quando um gateway tem vários nós, efetue os seguintes passos: eliminar todos os nós exceto um nó, altere o modo de encriptação e, em seguida, adicione novamente os nós.
        > 
        > Consulte [requisitos de certificado TLS/SSL](#tlsssl-certificate-requirements) secção para obter uma lista de requisitos para utilizar um certificado TLS/SSL. 
    5. Depois do gateway é instalado com êxito, clique em iniciar o Gestor de configuração:
    
        ![Configuração manual - início do configuration manager](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. consulte o Gestor de configuração do Data Management Gateway no nó (máquina Windows no local), que mostra o estado de conectividade, **nome do gateway**, e **nome de nó**.  

        ![O Data Management Gateway - instalação com êxito](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Se estiver a aprovisionar o gateway numa VM do Azure, pode utilizar [este modelo Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Este script cria um gateway lógico, configura as VMs com o software do Data Management Gateway instalado e regista-los com o gateway lógico. 
6. No portal do Azure, inicie o **Gateway** página: 
    1. Na página inicial de data factory no portal, clique em **serviços ligados**.
    
        ![Home page da fábrica de dados](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. Selecione o **gateway** para ver o **Gateway** página:
    
        ![Home page da fábrica de dados](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Pode ver o **Gateway** página:   

        ![Gateway de vista de nó único](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Clique em **adicionar nó** na barra de ferramentas para adicionar um nó para o gateway lógico. Se estiver a planear utilizar a configuração rápida, efetue este passo da máquina no local que será adicionada como um nó para o gateway. 

    ![O Data Management Gateway - adicionar o menu de nó](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Os passos são semelhantes para configurar o primeiro nó. A IU do Gestor de configuração permite-lhe definir o nome de nó, se escolher a opção de instalação manual: 

    ![Gateway de segundo instalar o Configuration Manager-](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Depois do gateway é instalado com êxito no nó, a ferramenta Gestor de configuração apresenta o ecrã seguinte:  

    ![Gateway de segundo da instalação com êxito o Configuration Manager-](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Se abrir o **Gateway** página no portal, verá dois nós de gateway agora: 

    ![Gateway de dois nós no portal](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Para eliminar um nó de gateway, clique em **eliminar nó** na barra de ferramentas, selecione o nó que pretende eliminar e, em seguida, clique em **eliminar** da barra de ferramentas. Esta ação elimina o nó selecionado do grupo. Tenha em atenção que esta ação não desinstala o software de gateway de gestão de dados do nó (máquina no local do Windows). Utilize **adicionar ou remover programas** no painel de controlo no no local para desinstalar o gateway. Desinstalar gateway a partir do nó, são eliminado automaticamente no portal.   

## <a name="upgrade-an-existing-gateway"></a>Atualizar um gateway existente
Pode atualizar um gateway existente ao utilizar a funcionalidade de elevada disponibilidade e escalabilidade. Esta funcionalidade funciona apenas connosco que tenham o data management gateway da versão de > = 2.12.xxxx. Pode ver a versão do data management gateway instalado numa máquina o **ajudar** separador de Gestor configuração do Data Management Gateway. 

1. Atualizar o gateway na máquina no local para a versão mais recente seguindo ao transferir e executar um MSI pacote a partir de programa de configuração a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Consulte [instalação](data-factory-data-management-gateway.md#installation) secção para obter detalhes.  
2. Navegue para o portal do Azure. Inicie o **page da fábrica de dados** da fábrica de dados. Clique no mosaico serviços ligados para iniciar o **página Serviços ligados**. Selecione o gateway para iniciar o **página gateway**. Clique e ativar **funcionalidade de pré-visualização** conforme mostrado na imagem seguinte: 

    ![O Data Management Gateway - funcionalidade de pré-visualização de ativação](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Depois da funcionalidade de pré-visualização está ativada no portal, feche todas as páginas. Reabra o **página gateway** para ver a nova interface de utilizador de pré-visualização (IU).
 
    ![O Data Management Gateway - êxito de funcionalidade de pré-visualização de ativação](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![O Data Management Gateway - pré-visualizar a IU](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Durante a atualização, o nome do primeiro nó é o nome da máquina. 
3. Agora, adicione um nó. No **Gateway** página, clique em **adicionar nó**.  

    ![O Data Management Gateway - adicionar o menu de nó](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Siga as instruções da secção anterior para configurar o nó. 

### <a name="installation-best-practices"></a>Melhores práticas de instalação

- Configure o esquema de energia no computador anfitrião para o gateway para que a máquina não hibernação. Se o computador anfitrião hibernates, o gateway não responde a pedidos de dados.
- Fazer uma cópia de segurança do certificado associado ao gateway.
- Certifique-se de que todos os nós estão da configuração semelhante (recomendada) para um desempenho ideal. 
- Adicione, pelo menos, dois nós para assegurar a elevada disponibilidade.  

### <a name="tlsssl-certificate-requirements"></a>Requisitos de certificado TLS/SSL
Eis os requisitos para o certificado TLS/SSL que é utilizado para proteger as comunicações entre integração nós de tempo de execução:

- O certificado tem de ser um publicamente fidedigna X509 v3 certificado. Recomendamos que utilize certificados que são emitidos por uma autoridade de certificação (terceiros) pública (AC).
- Cada nó de tempo de execução de integração tem de confiar este certificado, bem como o computador cliente que está a executar a aplicação do Gestor de credenciais. 
> [!NOTE]
> Aplicação do Gestor de credenciais é utilizada durante a definição de forma segura credenciais do Assistente para copiar / Portal do Azure. Isto pode ser acionado a partir de qualquer máquina dentro da mesma rede, como no local / privada arquivo de dados.
- São suportados certificados de caráter universal. Se o nome FQDN for **node1.domain.contoso.com**, pode utilizar ***. domain.contoso.com** como nome do requerente do certificado.
- Certificados SAN não são recomendados uma vez que será utilizado apenas o último item dos nomes de alternativo do requerente e todos os outros serão ignorados devido a limitação atual. Por exemplo, tem um certificado SAN cujo SAN é **node1.domain.contoso.com** e **node2.domain.contoso.com**, só pode utilizar este certificado na máquina cujo FQDN é **node2.domain.contoso.com**.
- Suporta qualquer tamanho da chave suportado pelo Windows Server 2012 R2 para certificados SSL.
- Certificados CNG a utilizar as chaves não são suportadas. Doesrted DoesDoes não suporta certificados que utilizar chaves CNG.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>FAQ: Quando seria posso não ativar esta encriptação?
Ativar a encriptação pode adicionar determinados custo à sua infraestrutura (proprietário certificado público), por conseguinte, pode ignorar ativar encriptação no abaixo casos:
- Quando o tempo de execução de integração está em execução numa rede fidedigna ou uma rede com a encriptação transparente de como o IP/seg. Uma vez que esta comunicação de canal só está limitado dentro da rede fidedigna, pode não necessitar de encriptação adicionais.
- Quando o tempo de execução de integração não está em execução num ambiente de produção. Isto pode ajudar a reduzir o custo de certificado TLS/SSL.


## <a name="monitor-a-multi-node-gateway"></a>Monitorizar um gateway de vários nós
### <a name="multi-node-gateway-monitoring"></a>Monitorização de vários nós gateway
No portal do Azure, pode ver o instantâneo de quase em tempo real de utilização de recursos (CPU, memória, network(in/out), etc.) em cada nó, juntamente com os Estados de nós de gateway. 

![O Data Management Gateway - monitorização de nó vários](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Pode ativar **definições avançadas** no **Gateway** página para ver as métricas avançadas, como **rede**(entrada/saída), **função & credencial estado**, que é útil em problemas de gateway, a depuração e **tarefas simultâneas** (em execução / limitar) que pode ser modificado / alterado em conformidade durante a otimização de desempenho. A tabela seguinte fornece descrições das colunas do **nós de Gateway** lista:  

Propriedade de monitorização | Descrição
:------------------ | :---------- 
Nome | Nome do gateway lógico e nós associadas ao gateway.  
Estado | Estado do gateway a lógico e os nós de gateway. Exemplo: Online/Offline/limitado/etc. Para obter informações sobre estes Estados terem, consulte [estado do Gateway](#gateway-status) secção. 
Versão | Mostra a versão do gateway lógico e cada nó de gateway. A versão do gateway lógico é determinada com base na versão de maioria de nós no grupo. Se existirem nós com diferentes versões no programa de configuração do gateway lógico, apenas os nós com o mesmo número de versão que a função de gateway lógico corretamente. Outras pessoas estão no modo de limitado e tem de ser atualizados manualmente (apenas no caso de falha de atualização automática). 
Memória disponível | Memória disponível no nó de gateway. Este valor é um instantâneo em tempo real near. 
Utilização da CPU | Utilização da CPU de um nó de gateway. Este valor é um instantâneo em tempo real near. 
Funcionamento em rede (na/saída) | Utilização de um nó de gateway de rede. Este valor é um instantâneo em tempo real near. 
Tarefas simultâneas (em execução / limitar) | Número de tarefas ou tarefas em execução em cada nó. Este valor é um instantâneo em tempo real near. Limite representa as máximas tarefas simultâneas para cada nó. Este valor é definido com base no tamanho da máquina. Pode aumentar o limite de aumentar verticalmente a execução da tarefa em simultâneo em cenários avançados, onde CPU / memória / rede é em utilizados, mas as atividades são exceder o tempo limite. Esta capacidade também está disponível com um gateway de nó único (mesmo quando a funcionalidade de escalabilidade e disponibilidade não está ativada). Para obter mais informações, consulte [Dimensionar considerações](#scale-considerations) secção. 
Função | Existem dois tipos de funções – emissor e de trabalho. Todos os nós são trabalhadores, o que significa que podem todos ser utilizados para executar tarefas. Não há apenas um nó de emissor, o que é utilizado para extrair as tarefas/tarefas dos serviços cloud e emiti-los a nós de trabalho diferente (incluindo o próprio). 

![O Data Management Gateway - avançadas de monitorização de nó vários](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Estado do gateway

A tabela seguinte fornece os possíveis Estados de um **o nó de gateway**: 

Estado  | Comentários/cenários
:------- | :------------------
Online | Nó ligado ao serviço Data Factory.
Offline | Nó está offline.
A atualizar | O nó está a ser atualizados automaticamente.
Limitado | Devido ao problema de conectividade. Pode ser devido ao problema de 8050 de porta HTTP, o problema de conectividade do barramento de serviço ou o problema de sincronização de credenciais. 
Inativos | Nó está a ser uma configuração diferente da configuração dos outros nós da maioria.<br/><br/> Um nó pode ficar inativo quando não é possível ligar a outros nós. 


A tabela seguinte fornece os possíveis Estados de um **gateway lógico**. O estado do gateway depende Estados de nós de gateway. 

Estado | Comentários
:----- | :-------
Tem de ser registado | Nenhum nó ainda está registada para este gateway lógico
Online | Nós de gateway estão online
Offline | Nenhum nó num estado online.
Limitado | Nem todos os nós deste gateway estão em bom estado de funcionamento. Este estado é um aviso que algumas nó pode estar inativa! <br/><br/>Pode ser devido a um problema de sincronização no nó emissor/trabalho credencial. 

### <a name="pipeline-activities-monitoring"></a>Pipeline / atividades de monitorização
O portal do Azure fornece um pipeline monitorização experiência com detalhes de nível de nó granular. Por exemplo, mostra as atividades executaram no nó que. Esta informação pode ser útil compreender os problemas de desempenho num determinado nó, diga devido à limitação de rede. 

![O Data Management Gateway - monitorização para pipelines de vários nós](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![O Data Management Gateway - detalhes de pipeline](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Considerações de dimensionamento

### <a name="scale-out"></a>Aumentar horizontalmente
Quando o **memória disponível é baixa** e **a utilização da CPU é elevada**, adicionar um novo nó ajuda a aumentar horizontalmente a carga entre máquinas. Se as atividades estão a falhar devido a tempo limite ou de gateway nó estar offline,-ajuda-o se adicionar um nó para o gateway.
 
### <a name="scale-up"></a>Aumentar verticalmente
Quando a memória disponível e a CPU não são bem utilizados, mas a capacidade de inatividade é 0, deve aumentar verticalmente aumento do número de tarefas simultâneas que podem ser executadas num nó. Também poderá dimensionar quando as atividades são exceder o tempo limite porque o gateway está sobrecarregado. Como é mostrado na imagem seguinte, pode aumentar a capacidade máxima para um nó. Sugerimos que duplicando-lo para começar.  

![O Data Management Gateway - considerações de dimensionamento](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Problemas conhecida/quebra alterações

- Atualmente, pode ter até quatro nós de gateway físico para um único gateway lógico. Se precisar de mais de quatro nós por motivos de desempenho, envie um e-mail para [ DMGHelp@microsoft.com ](mailto:DMGHelp@microsoft.com).
- Não é possível voltar a registar um nó de gateway com a chave de autenticação a partir de outro gateway lógico para mudar do gateway lógico atual. Para voltar a registar, desinstalar o gateway a partir do nó, reinstale o gateway e registá-lo com a chave de autenticação para o gateway de lógica. 
- Se o proxy HTTP é necessário para todos os nós de gateway, definir o proxy no diahost.exe.config e diawp.exe.config e utilize o Gestor de servidor para certificar-se de que todos os nós têm o mesmo diahost.exe.config e diawip.exe.config. Consulte [configurar definições de proxy](data-factory-data-management-gateway.md#configure-proxy-server-settings) secção para obter detalhes. 
- Para alterar o modo de encriptação para a comunicação de nó de nó no Gestor de configuração do Gateway, elimine todos os nós no portal, exceto um. Em seguida, adicione nós novamente depois de alterar o modo de encriptação.
- Utilize um certificado SSL oficial se optar por encriptar o canal de comunicação de nó de nó. Certificado autoassinado pode causar problemas de conectividade, como o mesmo certificado poderá não ser fidedigna na lista de autoridades de certificação nas outras máquinas. 
- Não é possível registar um nó de gateway para um gateway lógico quando a versão do nó é inferior à versão do gateway lógico. Eliminar todos os nós do gateway lógico a partir do portal, de modo a que pode registar um node(downgrade) versão inferior-lo. Se eliminar todos os nós de um gateway lógico, instalar manualmente e registar novos nós para esse gateway lógico. Configuração rápida não é suportada neste caso.
- Não é possível utilizar a configuração rápida para instalar nós para um gateway lógico existente, que ainda está a utilizar credenciais em nuvem. Pode verificar onde as credenciais são armazenadas do Gestor de configuração do Gateway no separador Definições.
- Não é possível utilizar a configuração rápida para instalar nós para um gateway lógico existente, que tem o nó para o nó a encriptação ativada. Como definir o modo de encriptação envolve adicionar manualmente os certificados, está sem mais uma opção de instalação rápida. 
- Para obter uma cópia de ficheiros de ambiente no local, não deve utilizar \\localhost ou C:\files já desde localhost ou unidade local poderá não estar acessível através de todos os nós. Em alternativa, utilize \\ServerName\files para especificar a localização dos ficheiros.


## <a name="rolling-back-from-the-preview"></a>A reverter a partir da pré-visualização 
Para reverter a partir da pré-visualização, elimine todos os nós, mas um nó. É irrelevante quais os nós eliminar, mas certifique-se de que tem pelo menos um nó no gateway lógico. Pode eliminar um nó por desinstalar o gateway no computador ou utilizando o portal do Azure. No portal do Azure, no **Data Factory** página, clique em serviços ligados para iniciar o **serviços ligados** página. Selecione o gateway para iniciar o **Gateway** página. Na página do Gateway, pode ver os nós associadas ao gateway. A página permite eliminar um nó de gateway.
 
Depois de eliminar, clique em **funcionalidades de pré-visualização** na mesma página do portal do Azure e desativar a funcionalidade de pré-visualização. Tem de repor o gateway para um gateway de (disponibilidade geral) de GA de nó.


## <a name="next-steps"></a>Passos seguintes
Reveja os artigos seguintes:
- [O Data Management Gateway](data-factory-data-management-gateway.md) -fornece uma descrição geral detalhada do gateway.
- [Mover dados entre no local e na nuvem arquivos de dados](data-factory-move-data-between-onprem-and-cloud.md) -contém instruções com instruções passo a passo para utilizar um gateway com um único nó. 
