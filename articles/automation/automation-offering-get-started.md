---
title: "Introdução à Automatização do Azure | Microsoft Docs"
description: "Este artigo fornece uma descrição geral do serviço de Automatização do Azure ao rever o design e os detalhes de implementação em preparação para integrar a oferta do Azure Marketplace."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/31/2017
ms.author: magoedte
ms.openlocfilehash: fad13053895c5d6e3c41835fea3cf0bdd3380cd4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-automation"></a>Introdução à Automatização do Azure

Este guia de introdução apresenta os conceitos principais relacionados com a implementação da Automatização do Azure. Se não estiver familiarizado com a Automatização no Azure ou tiver experiência com software de fluxo de trabalho de automatização, como o System Center Orchestrator, este guia ajuda-o a compreender como preparar e integrar a Automatização.  Posteriormente, será preparado para começar a desenvolver runbooks para suportar as suas necessidades de automatização de processos. 


## <a name="automation-architecture-overview"></a>Descrição geral da arquitetura de automatização

![Descrição geral da Automatização do Azure](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

A Automatização do Azure é uma aplicação de software como um serviço (SaaS) que fornece um ambiente escalável e fiável de multi-inquilino para automatizar os processos com runbooks e gerir as alterações da configuração dos sistemas Windows e Linux com a Configuração de Estado Pretendido (DSC) no Azure, outros serviços cloud ou no local. As entidades contidas na sua conta de Automatização, como os runbooks, os ativos, as contas Run são isolados de outras contas de Automatização na subscrição e outras subscrições.  

Os runbooks que executa no Azure são executados em sandboxes de Automatização, que são alojados na plataforma do Azure como máquinas virtuais como um serviço (PaaS).  As sandboxes de Automatização fornecem isolamento de inquilino para todos os aspetos da execução do runbook – módulos, armazenamento, memória, comunicação de rede, fluxos de trabalho, etc. Esta função é gerida pelo serviço e não está acessível a partir do Azure ou da conta de Automatização do Azure que controla.         

Para automatizar a implementação e gestão de recursos no seu datacenter local ou outros serviços cloud, depois de criar uma conta de Automatização, pode designar uma ou mais máquinas para executar a [Função de Trabalho de Runbook Híbrida (HRW)](automation-hybrid-runbook-worker.md).  Cada HRW requer um Agente de Gestão da Microsoft com uma ligação a uma área de trabalho do Log Analytics e uma conta de Automatização.  O Log Analytics é utilizado para iniciar a instalação, manter o Microsoft Monitoring Agent e monitorizar a funcionalidade do HRW.  O fornecimento de runbooks e as instruções apresentadas para executá-los são efetuados pelo Automatização do Azure.

Pode implementar várias HRW para fornecer elevada disponibilidade aos runbooks, tarefas de runbook de balanceamento de carga e, em alguns casos, dedicá-los para ambientes ou cargas de trabalho específicas.  O Microsoft Monitoring Agent no HRW inicia a comunicação com o serviço Automatização através da porta TCP 443 e não há requisitos de firewall de entrada.  Assim que o runbook for executado num HRW no ambiente e pretender que execute tarefas de gestão noutras máquinas ou serviços nesse ambiente, poderão existir outras portas a que o runbook precisa de acesso.  Se as políticas de segurança de TI não permitirem que os computadores na sua rede estabeleçam uma ligação à Internet, consulte [OMS Gateway (Gateway do OMS)](../log-analytics/log-analytics-oms-gateway.md), que funciona como um proxy para o HRW recolher o estado da tarefa e receber informações de configuração da sua conta de Automatização.

Runbooks em execução num HRW no contexto da conta do Sistema local no computador, que é o contexto de segurança recomendado ao efetuar ações administrativas no computador local do Windows. Se pretender que o runbook execute tarefas relativamente aos recursos fora do computador local, poderá ter de definir ativos seguros de credenciais na conta de Automatização que pode aceder a partir do runbook e utilizar para se autenticar com o recurso externo. Pode utilizar ativos da [Credencial](automation-credentials.md), do [Certificado](automation-certificates.md) e da [Ligação](automation-connections.md) no runbook com cmdlets que lhe permitem especificar as credenciais para poder autenticá-las.

As configurações DSC armazenadas na Automatização do Azure podem ser aplicadas diretamente a máquinas virtuais do Azure. Outras máquinas físicas e virtuais podem solicitar configurações do servidor de solicitação do Automation DSC do Azure.  Para gerir configurações dos seus sistemas Windows e Linux físicos ou virtuais no local, não precisa de implementar qualquer infraestrutura para suportar o servidor de solicitação do Automation DSC, apenas acesso de saída à Internet de cada sistema para ser gerido pelo Automation DSC, comunicando através da porta TCP 443 para o serviço do OMS.   

## <a name="prerequisites"></a>Pré-requisitos

### <a name="automation-dsc"></a>Automation DSC
O Azure Automation DSC pode ser utilizado para gerir várias máquinas:

* Máquinas virtuais do Azure (clássicas) com o Windows ou Linux
* Máquinas virtuais do Azure com o Windows ou Linux
* Máquinas virtuais dos Serviços Web Amazon (AWS) com o Windows ou Linux
* Computadores Windows físicos/virtuais no local ou numa cloud diferente do Azure ou AWS
* Computadores Linux físicos/virtuais no local ou numa cloud diferente do Azure ou AWS

A versão mais recente do WMF 5 tem de estar instalada para o agente do PowerShell DSC para o Windows para conseguir comunicar com a Automatização do Azure. A versão mais recente do [agente DSC de PowerShell para Linux](https://www.microsoft.com/en-us/download/details.aspx?id=49150) tem de estar instalada para o Linux ser capaz de comunicar com a Automatização do Azure.

### <a name="hybrid-runbook-worker"></a>Função de Trabalho de Runbook Híbrida  
Ao designar um computador para executar tarefas de runbook híbridas, este computador tem de ter o seguinte:

* Windows Server 2012 ou posterior
* Windows PowerShell 4.0 ou posterior.  Recomendamos que instale o Windows PowerShell 5.0 no computador para maior fiabilidade. Pode transferir a nova versão do [Centro de Transferências da Microsoft](https://www.microsoft.com/download/details.aspx?id=50395)
* Mínimo de dois núcleos
* Mínimo de 4 GB de RAM

### <a name="permissions-required-to-create-automation-account"></a>Permissões necessárias para criar uma conta de Automatização
Para criar ou atualizar uma conta de Automatização, tem de ter os seguintes privilégios específicos e as permissões necessárias para concluir este tópico.   
 
* Para criar uma conta de Automatização, a sua conta de utilizador do AD tem de ser adicionada a uma função com permissões equivalentes à função de Proprietário para recursos Microsoft.Automation, conforme descrito no artigo [Controlo de acesso baseado em funções na Automatização do Azure](automation-role-based-access-control.md).  
* Se a definição dos registos da Aplicação for **Sim**, os utilizadores não administradores no inquilino do Azure AD podem [registar aplicações do AD](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions).  Se a definição dos registos da aplicação for **Não**, o utilizador que executa esta ação tem de ser um administrador global no Azure AD. 

Se não for membro da instância do Active Directory da subscrição antes de ser adicionado à função de administrador global/coadministrador da mesma, é adicionado ao Active Directory como convidado. Nesta situação, recebe o aviso "Não tem permissões para criar..." no painel **Adicionar Conta de Automatização**. Os utilizadores que foram adicionados primeiro à função de administrador global/coadministrador podem ser removidos da instância do Active Directory da subscrição e adicionados novamente, para que se tornem em Utilizadores completos no Active Directory. Para verificar esta situação, no painel **Azure Active Directory**, no portal do Azure, selecione **Utilizadores e grupos**, **Todos os utilizadores** e, depois de selecionar o utilizador específico, selecione **Perfil**. O valor do atributo **Tipo de utilizador** sob o perfil de utilizadores não deve ser igual a **Convidado**.

## <a name="authentication-planning"></a>Planeamento da autenticação
A Automatização do Azure permite-lhe automatizar tarefas relativamente aos recursos no Azure, no local e outros fornecedores de serviços cloud.  Para um runbook efetuar as ações necessárias, tem de ter permissões para aceder de forma segura aos recursos com os direitos mínimos necessários dentro da subscrição.  

### <a name="what-is-an-automation-account"></a>O que é uma conta de Automatização 
Todas as tarefas de automatização que executa relativamente aos recursos que utilizam os cmdlets do Azure na Automatização do Azure são autenticados no Azure utilizando a autenticação com base em credenciais de identidade organizacional do Azure Active Directory.  Uma conta de Automatização é separada da conta que utiliza para iniciar sessão no portal para configurar e utilizar recursos do Azure.  Os recursos de automatização incluídos numa conta são os seguintes:

* **Certificados** - contém um certificado utilizado para autenticação a partir de um runbook ou configuração DSC.
* **Ligações** - contém informações de autenticação e configuração necessárias para ligar a uma aplicação ou serviço externo a partir de um runbook ou configuração DSC.
* **Credenciais** - objeto PSCredential que contém credenciais de segurança, como um nome de utilizador e palavra-passe necessária para autenticar a partir de um runbook ou configuração DSC.
* **Módulos de integração** - módulos do PowerShell incluídos numa conta de Automatização do Azure para utilizar cmdlets em runbooks e configurações DSC.
* **Agendas** - contém agendas que iniciam ou interrompem um runbook numa hora especificada, incluindo frequências recorrentes.
* **Variáveis** - contêm valores disponíveis a partir de um runbook ou configuração DSC.
* **Configurações de DSC** - são scripts do PowerShell que descrevem como configurar uma funcionalidade ou definição do sistema operativo ou instalar uma aplicação num computador Windows ou Linux.  
* **Runbooks** -são um conjunto de tarefas que efetuam algum processo automatizado na automatização do Azure, com base no Windows PowerShell.    

Os recursos de Automatização de cada conta de Automatização estão associados a uma única região do Azure, mas as contas de Automatização podem gerir todos os recursos na sua subscrição. Crie contas de Automatização em diferentes regiões se tiver políticas que exigem que os dados e os recursos estejam isolados numa região específica.

> [!NOTE]
> As contas de Automatização e os recursos que contêm que são criados no portal do Azure não podem ser acedidos no portal clássico do Azure. Se pretende gerir estas contas ou os respetivos recursos com o Windows PowerShell, tem de utilizar os módulos do Azure Resource Manager.
> 

Quando cria uma conta de Automatização no portal do Azure, são criadas automaticamente duas entidades de autenticação:

* Uma conta Run As. Esta conta cria um principal de serviço no Azure Active Directory (Azure AD) e um certificado. Também atribui o controlo de acesso baseado em funções (RBAC) Contribuinte, que gere os recursos do Resource Manager mediante a utilização de runbooks.
* Uma conta Run As Clássica. Esta conta carrega um certificado de gestão, que é utilizado para gerir recursos clássicos mediante a utilização de runbooks.

O controlo de acesso baseado em funções está disponível no Azure Resource Manager para conceder ações permitidas a uma conta de utilizador do Azure AD e uma conta Run As e autenticar esse principal de serviço.  Leia [o artigo Role-based access control in Azure Automation (Controlo de acesso baseado em funções na Automatização do Azure)](automation-role-based-access-control.md) para obter mais informações ajudar a desenvolver o seu modelo para a gestão de permissões de Automatização.  

#### <a name="authentication-methods"></a>Métodos de autenticação
A tabela seguinte resume os métodos de autenticação diferentes para cada ambiente suportado pela Automatização do Azure.

| Método | Ambiente 
| --- | --- | 
| Conta Run As e Run As Clássica |Implementação clássica do Azure e Azure Resource Manager |  
| Conta de Utilizador do Azure AD |Implementação clássica do Azure e Azure Resource Manager |  
| Autenticação do Windows |Datacenter local ou de outro fornecedor de cloud com a Função de Trabalho de Runbook Híbrida |  
| Credenciais AWS |Amazon Web Services |  

Na secção **How to\Authentication and Security (Procedimento\Autenticação e segurança)** encontram-se artigos que fornecem passos de descrição geral e de implementação para configurar a autenticação desses ambientes, qualquer um deles com uma conta nova ou existente que dedica nesse ambiente.  Para a conta Run As do Azure e Run As Clássica, o tópico [Update Automation Run As account (Atualizar conta Run As de Automatização)](automation-create-runas-account.md) descreve como atualizar a sua conta de Automatização existente com as contas Run As a partir do portal ou através do PowerShell, se não foi originalmente configurado com uma conta Run As ou uma conta Run As Clássica. Se quiser criar uma conta Run As e Run As Clássica com um certificado emitido pela sua autoridade de certificação empresarial (AC), reveja este artigo para saber como criar as contas através desta configuração.     
 
## <a name="network-planning"></a>Planeamento da rede
Para a Função de Trabalho de Runbook Híbrida ligar e registar com o Microsoft Operations Management Suite (OMS), tem de ter acesso ao número da porta e aos URLs descritos abaixo.  Isto é outra forma de as [portas e URLs necessários para o Microsoft Monitoring Agent](../log-analytics/log-analytics-windows-agents.md#network) ligarem ao OMS. Se utilizar um servidor proxy para comunicação entre o agente e o serviço OMS, tem de assegurar que os recursos adequados estão acessíveis. Se utilizar uma firewall para restringir o acesso à Internet, terá de configurar a firewall para permitir acesso.

As informações abaixo listam as portas e os URLs que são necessários para a Função de Trabalho de Runbook Híbrida comunicar com a Automatização.

* Porta: apenas a TCP 443 é necessária para acesso de saída à Internet
* URL global:  *.azure-automation.net

Se tiver uma conta de Automatização definida para uma região específica e pretender restringir a comunicação com esse datacenter regional, a tabela seguinte fornece o registo DNS para cada região.

| **Região** | **Registo DNS** |
| --- | --- |
| EUA Centro-Sul |scus-jobruntimedata-prod-su1.azure-automation.net |
| EUA Leste 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| EUA Centro-Oeste | wcus-jobruntimedata-prod-su1.azure-automation.net |
| Europa Ocidental |we-jobruntimedata-prod-su1.azure-automation.net |
| Europa do Norte |ne-jobruntimedata-prod-su1.azure-automation.net |
| Canadá Central |cc-jobruntimedata-prod-su1.azure-automation.net |
| Sudeste Asiático |sea-jobruntimedata-prod-su1.azure-automation.net |
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net |
| Leste do Japão |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Sudeste da Austrália |ase-jobruntimedata-prod-su1.azure-automation.net |
| Reino Unido Sul | uks-jobruntimedata-prod-su1.azure-automation.net |
| Gov (US) - Virginia | usge-jobruntimedata-prod-su1.azure-automation.us |

Para obter uma lista de endereços IP em vez de nomes, transfira e reveja o ficheiro xml do [endereço IP do Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653) a partir do Centro de Transferências da Microsoft. 

> [!NOTE]
> Este ficheiro contém os intervalos de endereços IP (incluindo intervalos de Computação, SQL e Armazenamento) utilizados no Centro de Transferências da Microsoft. Um ficheiro atualizado é publicado semanalmente, refletindo os intervalos implementados atualmente e as alterações futuras para os intervalos IP. Os novo intervalos que aparecem no ficheiro não serão utilizados nos centros de dados durante, pelo menos, uma semana. Transfira o ficheiro xml novo todas as semanas e efetue as alterações necessárias no seu site para identificar corretamente os serviços em execução no Azure. Os utilizadores Express Route poderão notar este ficheiro utilizado para atualizar o anúncio BGP do espaço Azure na primeira semana de cada mês. 
> 

## <a name="creating-an-automation-account"></a>Criar uma conta de Automatização

Existem formas diferentes de criar uma conta de Automatização no portal do Azure.  A tabela seguinte apresenta cada tipo de experiência de implementação e as diferenças entre as mesmas.  

|Método | Descrição |
|-------|-------------|
| Selecione Automatização & Controlo a partir do Mercado | Uma oferta que cria uma conta de Automatização e uma área de trabalho do OMS ligados entre si no mesmo grupo de recursos e região.  A integração no OMS também inclui a vantagem de utilizar o Log Analytics para monitorizar e analisar o estado dos trabalhos de runbook e os fluxos de trabalho ao longo do tempo e utilizar funcionalidades avançadas para escalar ou investigar problemas. A oferta também implementa as soluções de Gestão de Atualizações e Controlo de Alterações, que estão ativadas por predefinição. |
| Selecione Automatização a partir do Mercado | Cria uma conta de Automatização num grupo de recursos novo ou existente que não está ligado a uma área de trabalho do OMS e não inclui as soluções disponíveis da oferta de Automatização & Controlo. Esta é uma configuração básica que lhe apresenta a Automatização e pode ajudá-lo a aprender a escrever runbooks, configurações DSC e a utilizar as capacidades do serviço. |
| Soluções de Gestão Selecionadas | Se selecionar uma solução – **[Gestão de Atualizações](../operations-management-suite/oms-solution-update-management.md)**,  **[Iniciar/Parar VMs durante as horas de inatividade](automation-solution-vm-management.md)** ou **[Controlo de Alterações](../log-analytics/log-analytics-change-tracking.md)**  ser-lhe-á pedido para selecionar uma área de trabalho existente de Automatização e OMS ou oferecida a opção para criar ambos, conforme necessário para a solução ser implementada na sua subscrição. |

Este tópico explica-lhe como criar uma conta de Automatização e uma área de trabalho do OMS ao integrar a oferta de Controlo & Automatização.  Para criar uma conta de Automatização para fins de teste ou para pré-visualizar o serviço, reveja o seguinte artigo [Create standalone Automation account (Criar conta de Automatização autónoma)](automation-create-standalone-account.md).  

### <a name="create-automation-account-integrated-with-oms"></a>Criar conta de Automatização integrada no OMS
O método recomendado para carregar a Automatização é selecionar a oferta de Automatização & Controlo do Mercado.  Este procedimento cria uma conta de Automatização e estabelece a integração com uma área de trabalho do OMS, incluindo a opção de instalar soluções de gestão que estão disponíveis com a oferta.  

1. Inicie sessão no portal do Azure com uma conta que seja membro da função Administradores da Subscrição e coadministrador da subscrição.

2. Clique em **Novo**.<br><br> ![Selecione a Nova opção no portal do Azure](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  

3. Procure a **Automatização** e, em seguida, nos resultados da pesquisa selecione **Automatização & Controlo***.<br><br> ![Procure e selecione Automatização & Controlo a partir do Mercado](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png).<br>   

4. Depois de ler a descrição da oferta, clique em **Criar**.  

5. No painel de definições **Automatização & Controlo**, selecione **Área de trabalho do OMS**.  No painel **Áreas de Trabalho do OMS**, selecione uma área de trabalho do OMS ligada à mesma subscrição do Azure da qual a conta de Automatização faz parte ou crie uma área de trabalho do OMS.  Se não tiver uma área de trabalho do OMS, selecione **Criar Nova Área de Trabalho** e, no painel **Área de Trabalho do OMS** , faça o seguinte: 
   - Especifique um nome para a **Área de Trabalho do OMS**.
   - Selecione uma **Subscrição** à qual ligar ao escolher na lista pendente se a predefinição selecionada não é adequada.
   - Em **Grupo de Recursos**, pode criar um grupo de recursos ou selecionar um já existente.  
   - Selecione uma **Localização**.  Para obter mais informações, veja em que [regiões está disponível a Automatização do Azure](https://azure.microsoft.com/regions/services/).  As soluções são oferecidas em dois escalões: gratuito e escalão Por Nó (OMS).  O escalão gratuito tem um limite quanto à quantidade de dados recolhidos diariamente, ao período de retenção e aos minutos de tempo de execução de trabalhos de runbook.  O escalão Por Nó (OMS) não tem limite quanto à quantidade de dados recolhidos diariamente.  
   - Selecione **Conta de Automatização**.  Se estiver a criar uma área de trabalho do OMS novo, tem também de criar uma conta de Automatização, que é associada à área de trabalho do OMS especificada anteriormente, incluindo a subscrição, o grupo de recursos e a região do Azure.  Pode selecionar **Criar conta de Automatização** e, no painel **Conta de Automatização**, indique o seguinte: 
  - No campo **Nome**, introduza o nome da conta de Automatização.

    Todas as outras opções são preenchidas automaticamente com base na área de trabalho do OMS selecionada e não podem ser modificadas.  O método de autenticação predefinido para a oferta é a conta Run As do Azure.  Depois de clicar em **OK**, as opções de configuração são validadas e a conta de Automatização é criada.  Pode acompanhar o progresso em **Notificações**, no menu. 

    Caso contrário, selecione uma conta Run As de Automatização existente.  A conta que selecionar não pode já estar associada a outra área de trabalho do OMS, caso contrário, é apresentada uma mensagem de notificação no painel.  Se já estiver associada, tem de selecionar uma conta Run As de Automatização diferente ou criar uma.

    Depois de preencher as informações necessárias, clique em **Criar**.  As informações são verificadas e as contas Run As e de Automatização são criadas.  É reencaminhado para o painel **Área de trabalho do OMS** automaticamente.  

6. Depois de fornecer as informações necessárias no painel **Área de Trabalho do OMS**, clique em **Criar**.  Enquanto as informações são confirmadas e a área de trabalho criada, pode acompanhar o progresso em **Notificações**, no menu.  É reencaminhado para o painel **Adicionar Solução**.  

7. No painel de definições **Automatização & Controlo**, confirme que pretende instalar as soluções pré-selecionadas recomendadas. Se desmarcar qualquer uma, pode instalá-las individualmente mais tarde.  

8. Clique em **Criar** para continuar com a integração da Automatização e uma área de trabalho do OMS. Todas as definições são validadas e, em seguida, tenta implementar a oferta na sua subscrição.  Este processo pode demorar alguns segundos até ser concluído e pode acompanhar o progresso em **Notificações**, no menu. 

Após a integração da oferta, pode começar a criar runbooks, trabalhar com soluções de gestão ativadas por si, implementar uma [Função de Trabalho de Runbook Híbrida](automation-hybrid-runbook-worker.md) ou começar a trabalhar com o [Log Analytics](https://docs.microsoft.com/azure/log-analytics) para recolher dados gerados pelos recursos em ambientes de cloud ou no local.   

## <a name="next-steps"></a>Passos seguintes
* Pode confirmar que a sua nova conta de Automatização pode autenticar em relação a recursos do Azure, através da consulta de [test Azure Automation Run As account authentication (testar a autenticação da conta Run As de Automatização do Azure)](automation-verify-runas-authentication.md).
* Para começar a criação de runbooks, reveja primeiro os [tipos de automatização de runbooks](automation-runbook-types.md) suportados e as considerações relacionadas antes de começar a criar.


