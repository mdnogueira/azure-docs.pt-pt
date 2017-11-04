---
title: "Descrição geral de segurança de recursos de infraestrutura de serviço do Azure | Microsoft Docs"
description: "Este artigo fornece uma descrição geral de segurança do Azure Service Fabric."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: 908bdaf002e42035567974b204f5b39e73e82024
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-fabric-security-overview"></a>Descrição geral de segurança do Azure Service Fabric
[Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) é uma plataforma de sistemas distribuídos que facilita a pacote, implementar e gerir micro-serviços escaláveis e fiáveis. Service Fabric aborda os desafios significativos de desenvolvimento e gestão de aplicações em nuvem. Permite, assim, que os programadores e administradores evitem problemas complexos de infraestrutura e se concentrem na implementação de cargas de trabalho exigentes e fundamentais que sejam dimensionáveis, fiáveis e geríveis.

Este artigo de descrição geral de segurança de recursos de infraestrutura do Azure Service concentra-se nas seguintes áreas:

-   Proteger o cluster
-   Noções sobre monitorização e diagnóstico
-   Criar ambientes mais seguros através da utilização de certificados
-   Utilizar o controlo de acesso baseado em funções (RBAC)
-   Proteger clusters através de segurança do Windows
-   Configurar a segurança da aplicação no Service Fabric
-   Proteger a comunicação para serviços no Azure Service Fabric 

## <a name="secure-your-cluster"></a>Proteger o seu cluster
Azure Service Fabric orquestra serviços através de um cluster de máquinas. Clusters tem de estar protegidos para impedir que utilizadores não autorizados a ligar aos mesmos, especialmente quando estiverem a executar cargas de trabalho de produção. Embora seja possível criar um cluster não segura, isto poderá permitir que utilizadores anónimos estabelecer ligação à mesma (se expõe pontos finais de gestão para a internet pública).

Esta secção fornece uma descrição geral dos cenários de segurança para clusters que sejam executados de forma autónoma ou no Azure. Também descreve as várias tecnologias que são utilizadas para implementar esses cenários. Estes são os cenários de segurança do cluster:

-   Segurança de nó de nó
-   Segurança de nó de cliente

### <a name="node-to-node-security"></a>Segurança de nó de nó
Nó de nó segurança protege a comunicação entre as VMs ou máquinas num cluster. Com a segurança do nó de nó, apenas os computadores que estejam autorizados para aderirem ao cluster podem participar em aplicações e serviços no cluster de alojamento.

Clusters que estão em execução no Azure ou autónomo clusters que executem Windows podem utilizar um [certificado segurança](https://msdn.microsoft.com/library/ff649801.aspx) ou [segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx) para máquinas de Windows Server.

**Compreender a segurança do certificado do nó de nó**

O Service Fabric utiliza certificados de servidor de x. 509 que especificou quando cria um cluster. Para uma descrição geral rápida do que estes certificados são e como pode adquirir ou criá-los, consulte [trabalhar com certificados](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

Configurar a segurança de certificados quando criar o cluster, através do portal do Azure, modelos Azure Resource Manager ou um modelo JSON autónomo. Pode especificar um certificado principal e um certificado secundário opcional que é utilizado para rollovers de certificado. Os certificados primários e secundários que especificar devem ser diferentes do cliente de administrador só de leitura certificados de cliente e que especificar para [segurança de cliente para o nó](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-security).

### <a name="client-to-node-security"></a>Segurança de nó de cliente
Configurar a segurança de nó de cliente através da utilização de identidades de cliente. Estabelecer confiança entre um cliente e um cluster, tem de configurar o cluster de saber qual cliente identidades pode confiar. Pode fazê-lo de duas formas diferentes:

-   Especifique os utilizadores do grupo de domínio que possam ligar. 
-   Especifique os utilizadores de nó de domínio podem ligar-se.

Recursos de infraestrutura de serviço suporta dois tipos de controlo de acesso diferentes para clientes que estão ligados a um cluster do Service Fabric:

-   Administrador
-   Utilizador

Ao utilizar o controlo de acesso, os administradores de cluster podem limitar o acesso a determinados tipos de operações de cluster. Isto faz com que o cluster mais segura.

 Os administradores têm acesso total às capacidades de gestão (incluindo as capacidades de leitura/escrita). Por predefinição, os utilizadores, tem apenas acesso de leitura às capacidades de gestão (por exemplo, capacidades de consulta) e a capacidade para resolver a aplicações e serviços.

**Compreender a segurança do certificado de cliente para o nó**

Configurar a segurança de certificados de cliente para o nó quando cria um cluster através do portal do Azure, modelos do Resource Manager ou um modelo JSON autónomo. Tem de especificar um certificado de cliente de administrador e/ou um certificado de cliente utilizador. 

Os cliente e utilizador certificados de cliente administrativo que especificar devem ser diferentes de certificados primários e secundários que especificar para segurança de nó de nó.

Os clientes que ligam o cluster utilizando o certificado de admin têm acesso total para as capacidades de gestão. Os clientes que ligam o cluster utilizando o certificado de cliente de utilizador só de leitura têm acesso de leitura para as capacidades de gestão. Por outras palavras, estes certificados são utilizados para RBAC.

Para saber como configurar o certificado de segurança num cluster, consulte [configurar um cluster utilizando um modelo Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

**Compreender a segurança do Azure Active Directory de nó de cliente no Azure**

Clusters que sejam executados no Azure também podem proteger o acesso para os pontos finais de gestão utilizando o Azure Active Directory (Azure AD). Para obter informações sobre como criar os artefactos necessários do Azure Active Directory, como preenchê-los durante a criação do cluster e como ligar a esses clusters, consulte [configurar um cluster utilizando um modelo Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Azure AD permite às organizações (conhecidas como inquilinos) para gerir o acesso de utilizador para aplicações. Existem aplicações com uma baseada na web início de sessão da IU e aplicações com uma experiência de cliente nativo.

Um cluster do Service Fabric oferece vários pontos de entrada para a funcionalidade de gestão, incluindo o Service Fabric Explorer e Visual Studio baseada na web. Como resultado, pode cria duas aplicações do Azure AD para controlar o acesso ao cluster: web de uma aplicação e uma aplicação nativa.

Para os clusters do Azure, recomendamos que utilize a segurança do Azure AD para autenticar clientes e certificados de segurança do nó de nó.

Para clusters de servidores de Windows de autónomos com Windows Server 2012 R2 e do Active Directory, recomendamos que utilize a segurança do Windows com contas geridas de grupo.  Caso contrário, utilize a segurança do Windows com contas do Windows.

## <a name="understand-monitoring-and-diagnostics-in-azure-service-fabric"></a>Compreender a monitorização e diagnóstico no Azure Service Fabric
[Monitorização e diagnóstico](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) são críticos para desenvolver, testar e implementar aplicações e serviços em qualquer ambiente. Soluções de Service Fabric funcionam melhor quando implementar a monitorização e diagnóstico para se certificar de que as aplicações e serviços funcionam conforme esperado no ambiente de desenvolvimento local ou na produção.

Numa perspetiva de segurança, os objetivos principais da monitorização e diagnóstico são:

-   Detetar e diagnosticar problemas de hardware e a infraestrutura que podem ser causados por um evento de segurança.
-   Deteta problemas de software e aplicações que pode ser um indicador de compromisso (IoC).
-   Compreenda o consumo de recursos para ajudar a evitar inadvertida recusa de serviço.

O fluxo de trabalho geral de monitorização e diagnóstico consiste em três passos:

-   **Geração de eventos:** geração de eventos inclui eventos (registos de rastreios, eventos personalizados) de infraestrutura (cluster) e o nível de serviço/aplicação. Leia mais sobre [eventos ao nível da infraestrutura](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) e [eventos ao nível da aplicação](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) para compreender o que é fornecido e como adicionar mais instrumentação.

-   **Agregação de evento:** Generated eventos tem de ser recolhidos e agregados antes que podem ser apresentados. Normalmente, recomendamos que utilize [diagnósticos do Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (semelhantes a recolha de registos com base no agente) ou [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (-processo de recolha de registos).

-   **Análise:** eventos tem de ser visualizadas e acessível algumas formato, para permitir a análise e da apresentação. Existem várias plataformas para a análise e visualização de dados de monitorização e diagnóstico. Os dois que recomendamos são [Operations Management Suite](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) e [Azure Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) devido ao respetiva bom integração com o Service Fabric.

Também pode utilizar [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) monitorizar muitos dos recursos do Azure no qual baseia-se um cluster do Service Fabric.

Um watchdog do é um serviço separado que pode ver o estado de funcionamento, carregar nos vários serviços e comunicar o estado de funcionamento para qualquer coisa na hierarquia do modelo de estado de funcionamento. Utilizar um watchdog do pode ajudar a evitar erros que não seriam possível detetar com base na vista de um único serviço. 

Watchdogs também são um bom local para o código de anfitrião que executa toma ações sem interação do utilizador (por exemplo, da limpeza dos ficheiros de registo no armazenamento em determinados intervalos de tempo). Pode encontrar uma implementação de serviço de watchdog de exemplo no [exemplo do Azure Service Fabric watchdog](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/).

## <a name="understand-how-to-secure-communication-by-using-certificates"></a>Compreender a forma de segurar a comunicação através da utilização de certificados
Certificados de ajudam a proteger a comunicação entre os vários nós do cluster autónomo Windows. Ao utilizar certificados x. 509, também pode autenticar os clientes que estão a ligar a este cluster. Isto garante que apenas utilizadores autorizados podem aceder ao cluster. Recomendamos que ative um certificado no cluster aquando da respetiva criação.

### <a name="x509-certificates-and-service-fabric"></a>Certificados x. 509 e o Service Fabric
X. 509 os certificados digitais são frequentemente utilizados para autenticar servidores e clientes. Também são utilizados para encriptar e assinar digitalmente mensagens.

A tabela seguinte lista os certificados que necessita na sua configuração de cluster:

|Definição de informações do certificado |Descrição|
|-------------------------------|-----------|
|ClusterCertificate|    Este certificado é necessário para proteger a comunicação entre os nós num cluster. Pode utilizar duas diferentes certificados: um certificado principal e secundária para a atualização.|
|ServerCertificate| Este certificado é apresentado para o cliente ao tentar ligar a este cluster. Pode utilizar dois certificados de servidor diferente: um certificado principal e secundária para a atualização.|
|ClientCertificateThumbprints|  Este é um conjunto de certificados a instalar nos clientes autenticados.|
|ClientCertificateCommonNames|  Este é o nome comum do certificado de cliente primeiro para CertificateCommonName. CertificateIssuerThumbprint é o thumbprint do emissor deste certificado.|
|ReverseProxyCertificate|   Este é um certificado opcional que pode ser especificado para proteger a sua [proxy inverso](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).|

Para obter mais informações sobre como proteger certificados, consulte [proteger um cluster autónomo no Windows utilizando certificados x. 509](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="understand-role-based-access-control"></a>Compreender o controlo de acesso baseado em funções
Controlo de acesso permite ao administrador de cluster limitar o acesso a determinadas operações de cluster para diferentes grupos de utilizadores, efetuar, por conseguinte, o cluster mais segura. São suportados dois tipos de controlo de acesso diferentes para os clientes que estiverem a ligar a um cluster: 

- Função de administrador
- função de utilizador

Os administradores têm acesso total às capacidades de gestão (incluindo as capacidades de leitura/escrita). Por predefinição, os utilizadores, tem apenas acesso de leitura às capacidades de gestão (por exemplo, capacidades de consulta) e a capacidade para resolver a aplicações e serviços.

Especificar as funções de administrador e utilizador do cliente no momento da criação do cluster, fornecendo identidades separadas (incluindo certificados) para cada um. Para obter mais informações sobre as predefinições de controlo de acesso e como alterar as predefinições, consulte [controlo de acesso baseado em funções para clientes de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-cluster-by-using-windows-security"></a>Proteger o cluster autónomo utilizando a segurança do Windows
Para impedir o acesso não autorizado para um cluster do Service Fabric, tem de proteger o cluster. Segurança é especialmente importante quando o cluster executa cargas de trabalho de produção. Descreve como configurar a segurança de nó de nó e nó de cliente através da utilização de segurança do Windows no ficheiro Clusterconfig.

**Configurar a segurança do Windows, utilizando a gMSA**

Quando o Service Fabric tem de ser executado sob a gMSA, configurar a segurança de nó de nó definindo [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security). Para criar relações de confiança entre os nós, estes têm de ser efetuadas em consideração entre si.

Configurar a segurança de nó de cliente utilizando ClientIdentities. Estabelecer confiança entre um cliente e o cluster, tem de configurar o cluster para reconhecer que identidades de cliente pode confiar.

**Configurar a segurança do Windows através da utilização de um grupo de máquinas**

Se pretender utilizar um grupo de computador dentro de um domínio do Active Directory, pode configura a segurança de nó de nó definindo ClusterIdentity. Para obter mais informações, consulte [criar um grupo de computador no Active Directory](https://msdn.microsoft.com/library/aa545347).

Configurar a segurança de nó de cliente utilizando ClientIdentities. Estabelecer confiança entre um cliente e o cluster, tem de configurar o cluster reconheça as identidades de cliente que o cluster pode confiar. Pode estabelecer confiança de duas formas diferentes:

-   Especifique os utilizadores do grupo de domínio que possam ligar.
-   Especifique os utilizadores de nó de domínio podem ligar-se.

## <a name="configure-application-security-in-service-fabric"></a>Configurar a segurança da aplicação no Service Fabric
### <a name="manage-secrets-in-service-fabric-applications"></a>Gerir os segredos em aplicações de Service Fabric
Este método ajuda a gerir os segredos numa aplicação de Service Fabric. Os segredos podem ser qualquer informações confidenciais, tais como cadeias de ligação de armazenamento, as palavras-passe ou outros valores que não devem ser processados em texto simples.

Esta abordagem utiliza [Cofre de chaves do Azure](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) para gerir as chaves e segredos. No entanto, a utilização de segredos de uma aplicação é agnóstica de plataforma em nuvem. Isto significa que é possível implementar aplicações para um cluster que está alojado em qualquer lugar. Existem quatro passos principais deste fluxo:

-   Obter um certificado de encriptação de dados.
-   Instale o certificado no seu cluster.
-   Encriptar o segredo valores quando implementar uma aplicação com o certificado e inseri-los no ficheiro de configuração de Settings.xml de um serviço.
-   Leitura de valores encriptados Settings.xml ao desencriptá-los com o mesmo certificado de encriptação.

>[!NOTE]
>Saiba mais sobre [gerir segredos em aplicações de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="configure-security-policies-for-your-application"></a>Configurar políticas de segurança para a sua aplicação
Ao utilizar a segurança do Azure Service Fabric, pode ajudar a proteger aplicações em execução no cluster em contas de utilizador diferente. Segurança do serviço de recursos de infraestrutura também ajuda a proteger os recursos utilizados por aplicações no momento da implementação sob as contas de utilizador - por exemplo, ficheiros, diretórios e certificados. Isto faz com que aplicações em execução, mesmo num ambiente alojado partilhado, mais segura.

Os passos incluem:

-   Configurar a política para um ponto de entrada de configuração do serviço.
-   A comandos do PowerShell a partir de um ponto de entrada de configuração.
-   Utilizar o redirecionamento de consola para depuração local.
-   Configurar uma política para pacotes de código do serviço.
-   Atribuir uma política de acesso de segurança para pontos finais HTTP e HTTPS.

## <a name="secure-communication-for-services-in-azure-service-fabric-security"></a>Comunicação segura para os serviços de segurança do Azure Service Fabric
A segurança é um dos aspetos mais importantes de comunicação. A estrutura da aplicação Reliable Services fornece alguns pilhas de comunicação prebuilt e ferramentas que podem ser utilizadas para melhorar a segurança.

-   [Ajudar a proteger um serviço quando estiver a utilizar a comunicação remota do serviço](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication)
-   [Ajudar a proteger um serviço quando estiver a utilizar uma pilha de comunicação baseada em WCF](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication#help-secure-a-service-when-youre-using-a-wcf-based-communication-stack)

## <a name="next-steps"></a>Passos seguintes
- Para obter informações concetuais sobre a segurança do cluster, consulte [criar um cluster do Service Fabric com o Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) e [portal do Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- Para saber mais sobre a segurança do cluster no Service Fabric, consulte [segurança de cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).
