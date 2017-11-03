---
title: "Azure Service Fabric melhores práticas de segurança | Microsoft Docs"
description: "Este artigo fornece um conjunto de melhores práticas de segurança do Azure Service Fabric."
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
ms.date: 11/01/2017
ms.author: tomsh
ms.openlocfilehash: 682ad79cc5fe4f08051477b7b90ae80981e5d595
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="azure-service-fabric-security-best-practices"></a>Azure Service Fabric melhores práticas de segurança
Implementar uma aplicação no Azure é rápido, fácil e económica. Antes de implementar a aplicação de nuvem em produção, consulte a nossa lista de essenciais e recomendadas melhores práticas para implementar clusters seguras na sua aplicação.

O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços dimensionáveis e fiáveis. O Service Fabric também faz face aos desafios significativos no desenvolvimento e na gestão de aplicações na cloud. Permite, assim, que os programadores e administradores evitem problemas complexos de infraestrutura e se concentrem na implementação de cargas de trabalho exigentes e fundamentais que sejam dimensionáveis, fiáveis e geríveis. 

Para cada melhor prática, vamos explicar:

-   O que é a melhor prática.
-   Por que motivo deve implementar a melhor prática.
-   O que pode acontecer se não implementar a melhor prática.
-   Como pode saber implementar a melhor prática.

Recomendamos que a segurança do Azure Service Fabric seguinte melhores práticas:

-   Utilize modelos Azure Resource Manager e o módulo do PowerShell de Service Fabric para criar clusters seguros.
-   Utilize certificados x. 509.
-   Configure políticas de segurança.
-   Implemente a configuração de segurança de Reliable Actors.
-   Configure o SSL para recursos de infraestrutura de serviço do Azure.
-   Utilizar o isolamento de rede e de segurança com o Azure Service Fabric.
-   Configure o Cofre de chaves do Azure para segurança.
-   Atribua utilizadores a funções.


## <a name="best-practices-for-securing-your-clusters"></a>Melhores práticas para proteger os clusters

Utilize sempre um cluster seguro:
-   Implementar a segurança do cluster utilizando certificados.
-   Fornecer acesso de cliente (administrador só de leitura) utilizando o Azure Active Directory (Azure AD).

Utilize implementações automatizadas:
-   Utilize scripts para gerar, implementar e rollover os segredos.
-   Armazene os segredos no Cofre de chaves do Azure e utilizar o Azure AD para todos os outro acesso de cliente.
-   Exigir a autenticação para acesso humano para os segredos.

Além disso, considere as seguintes opções de configuração:
-   Crie redes de perímetro (também conhecido como desmilitarizada zonas, DMZ e sub-redes filtradas) utilizando grupos de segurança de rede do Azure (NSGs).
-   Aceder a máquinas virtuais de cluster (VMs) ou gerir o cluster utilizando servidores ir com ligação ao ambiente de trabalho remoto.

Os clusters tem de estar protegidos para impedir que utilizadores não autorizados a ligar, especialmente quando um cluster está em execução na produção. Embora seja possível criar um cluster não segura, os utilizadores anónimos podem ligar ao cluster, se o cluster expõe pontos finais de gestão para a internet pública.

Existem três [cenários](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) para implementar a segurança do cluster utilizando várias tecnologias:

-   Segurança de nó de nó: este cenário protege a comunicação entre as VMs e os computadores no cluster. Esta forma de segurança garante que apenas os computadores que estejam autorizados a associar o cluster podem alojar aplicações e serviços no cluster.
Neste cenário, os clusters que são executadas no Azure ou clusters autónomos que são executados no Windows, podem utilizar [certificado segurança](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) ou [segurança do Windows](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-windows-security) para máquinas de Windows Server.
-   Segurança de cliente para o nó: este cenário protege a comunicação entre um cliente de Service Fabric e os nós do cluster individuais.
-   Baseada em funções controlo de acesso (RBAC): este cenário utiliza identidades separadas (certificados, do Azure AD, e assim sucessivamente) para cada função de cliente de administrador e utilizador que acede ao cluster. Especifique as identidades de função quando criar o cluster.

>[!NOTE]
>**Recomendação de segurança para os clusters do Azure:** segurança de utilização do Azure AD para autenticar clientes e certificados de segurança do nó de nó.

Para configurar um cluster do Windows autónomo, consulte [configurar definições para um cluster do Windows autónomo](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Utilize modelos Azure Resource Manager e o módulo do PowerShell de Service Fabric para criar um cluster seguro.
Para obter instruções passo a passo Criar um cluster do Service Fabric seguro utilizando os modelos Azure Resource Manager, consulte [criar um cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Utilize o modelo Azure Resource Manager:
-   Personalize o seu cluster utilizando o modelo para configurar o armazenamento gerido para a VM de discos rígidos virtuais (VHDs).
-   Unidade de alterações para o grupo de recursos utilizando o modelo para a gestão de configuração fácil e auditoria.

Trate a configuração do cluster como o código:
-   Ser detalhado ao verificar a sua configurações de implementação.
-   Evite utilizar comandos implícitos para modificar diretamente os seus recursos.

Muitos aspetos do [ciclo de vida de aplicação de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) podem ser automatizadas. O [módulo do PowerShell de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package) automatiza tarefas comuns para implementar, atualizar, remover e testar aplicações do Azure Service Fabric. APIs de HTTP para gestão de aplicações e APIs geridas também estão disponíveis.

## <a name="use-x509-certificates"></a>Utilize certificados x. 509
Proteja sempre os clusters utilizando certificados x. 509 ou de segurança do Windows. Segurança está configurada apenas no momento de criação do cluster. Não é possível ativar segurança após a criação do cluster.

Para especificar um [certificado de cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security), defina o valor da **ClusterCredentialType** propriedade X509. Para especificar um certificado de servidor para ligações externos, defina o **ServerCredentialType** propriedade X509.

Além disso, siga estas práticas:
-   Crie os certificados para clusters de produção, utilizando um serviço de certificado de servidor Windows corretamente configurado. Também pode obter os certificados de uma autoridade de certificação aprovados (AC).
-   Nunca utilize um temporário ou o certificado para clusters de produção de teste se o certificado foi criado utilizando o MakeCert.exe ou uma ferramenta semelhante.
-   Utilize um certificado autoassinado para clusters de teste, mas não para clusters de produção.

Se o cluster é não seguro, qualquer pessoa pode ligar ao cluster anonimamente e efetuar operações de gestão. Por este motivo, proteja sempre os clusters de produção, utilizando certificados x. 509 ou de segurança do Windows.

Para saber mais sobre como utilizar certificados x. 509, consulte [adicionar ou remover certificados para um cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure).

## <a name="configure-security-policies"></a>Configurar políticas de segurança
Serviço de recursos de infraestrutura também protege os recursos que são utilizados pelas aplicações. Recursos como ficheiros, diretórios, e certificados são armazenados em contas de utilizador quando a aplicação é implementada. Esta funcionalidade torna aplicações em execução mais segura entre si, mesmo num ambiente alojado partilhado.

-   Utilize um utilizador ou grupo de domínio do Active Directory: executar o serviço sob as credenciais para uma conta de utilizador ou grupo do Active Directory. Lembre-se de que utiliza o Active Directory no local dentro do seu domínio e não do Azure Active Directory. Aceder a outros recursos no domínio que foram concedidos permissões através da utilização de um utilizador de domínio ou grupo. Por exemplo, recursos, tais como partilhas de ficheiros.

-   Atribuir uma política de acesso de segurança para pontos finais HTTP e HTTPS: Especifique o **SecurityAccessPolicy** propriedade para aplicar um **RunAs** política para um serviço de recursos de ponto final de declara o manifesto de serviço com o protocolo HTTP. Portas alocadas para os pontos finais HTTP são listas corretamente controlado de acesso para a conta de utilizador de RunAs que é executado o serviço. Quando a política não estiver configurada, o http.sys não tem acesso ao serviço e pode obter falhas com as chamadas do cliente.

Para saber como utilizar políticas de segurança de um cluster do Service Fabric, consulte [configurar políticas de segurança para a sua aplicação](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security).

## <a name="implement-the-reliable-actors-security-configuration"></a>Implementar a configuração de segurança Reliable Actors
Serviço de recursos de infraestrutura Reliable Actors é uma implementação do padrão de conceção de atores. Tal como acontece com quaisquer padrão de conceção de software, a decisão de utilizar um padrão específico baseia se um problema de software se adequa padrão.

Em geral, utilize o padrão de conceção de ator para soluções de modelo para os seguintes problemas de software ou cenários de segurança:
-   O espaço de problema envolve um grande número (milhares ou mais) de pequenas, independentes e isoladas unidades de estado e a lógica.
-   Está a trabalhar com objetos único thread que não necessitam de significativa interação de componentes externos, incluindo a consultar o estado através de um conjunto de atores.
-   As instâncias de ator não bloquear os chamadores com atrasos imprevisíveis ao emitir operações de e/s.

No Service Fabric, atores são implementados na estrutura da aplicação Reliable Actors. Esta estrutura é com base num padrão de ator e incorporada de [Service Fabric Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction). Cada serviço de atores fiável escreve é um serviço de fiável com monitorização de estado particionado.

Cada ator está definido como uma instância de um tipo de ator idêntico para a forma como objeto .NET é uma instância de um tipo .NET. Por exemplo, um **tipo ator** que implementa a funcionalidade de um cálculo pode ter muitos atores desse tipo que estão distribuídos em vários nós de um cluster. Cada um de atores distribuídos exclusivamente é caracterizada por um identificador de atores.

[Configurações de segurança do replicador](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration) são utilizados para proteger o canal de comunicação que é utilizado durante a replicação. Esta configuração impede que os serviços de ver o tráfego de replicação entre si e assegura que os dados altamente disponíveis são segurados. Por predefinição, uma secção de configuração de segurança vazio impede a segurança da replicação.
Configurações do replicador configurar replicador que é responsável por verificar o estado do fornecedor de estado de Ator altamente fiável.

## <a name="configure-ssl-for-azure-service-fabric"></a>Configurar o SSL para recursos de infraestrutura de serviço do Azure
O processo de autenticação de servidor [autentica](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) pontos finais de gestão de cluster para um cliente de gestão. O cliente de gestão, em seguida, reconhece que está a comunicar o cluster real. Este certificado também fornece um [SSL](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm) para a API de gestão HTTPS e para o Service Fabric Explorer através de HTTPS.
Tem de obter um nome de domínio personalizado para o cluster. Quando solicita um certificado de uma autoridade de certificação, o nome de requerente do certificado tem de corresponder ao nome de domínio personalizado que utilizar para o cluster.

Para configurar o SSL para uma aplicação, terá primeiro de obter um certificado SSL assinado por uma AC. A AC é uma terceiros fidedigna, que emite certificados para fins de segurança SSL. Se ainda não tiver um certificado SSL, tem de obter um de uma empresa que sells certificados SSL.

O certificado tem de cumprir os seguintes requisitos para certificados SSL no Azure:
-   O certificado tem de conter uma chave privada.

-   O certificado tem de ser criado para a troca de chaves e ser exportável para um ficheiro do exchange (. pfx) de informações pessoais.

-   Nome do requerente do certificado tem de corresponder ao nome de domínio que é utilizado para aceder ao seu serviço em nuvem.

    - Adquirir um nome de domínio personalizado para utilizar para aceder ao seu serviço em nuvem.
    - Pedir um certificado de AC com um nome de requerente que corresponde ao nome de domínio personalizado do seu serviço. Por exemplo, se o nome de domínio personalizado for __contoso__**empresa>.com**, o certificado da AC deve ter o nome do requerente **. contoso.com** ou __www__ **. contoso.com**.

    >[!NOTE]
    >Não é possível obter um certificado SSL a partir de uma AC para o __cloudapp__**.net** domínio.
    
-   O certificado tem de utilizar um mínimo de encriptação 2,048-bit.

É o protocolo HTTP não seguros e estão sujeitos a ataques de espionagem. Dados que são transmitidos através de HTTP são enviados como texto simples a partir do browser para o servidor web ou entre os outros pontos finais. Os atacantes podem intercetar e ver os dados confidenciais que são enviados através de HTTP, tais como os detalhes do cartão de crédito e inícios de sessão de conta. Quando os dados são enviados ou publicados através de um browser através de HTTPS, SSL assegura que as informações confidenciais são encriptado e seguros a partir de interception.

Para saber mais sobre como utilizar certificados SSL, consulte [configurar o SSL para aplicações do Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Utilizar o isolamento de rede e de segurança com o Azure Service Fabric
Configurar um cluster segura 3 nodetype utilizando a [modelo Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) como uma amostra. Controla o tráfego de rede de entrada e saída utilizando o modelo e os grupos de segurança de rede.

O modelo tem um NSG para cada um dos conjuntos de dimensionamento de máquina virtual e é utilizado para controlar o tráfego que entra e sai do conjunto. As regras são configuradas por predefinição, para permitir todo o tráfego necessário para os serviços do sistema e as portas de aplicação especificadas no modelo. Rever estas regras e efetue as alterações para se ajustarem às suas necessidades, incluindo a adição de novas regras para as suas aplicações.

Para obter mais informações, consulte [cenários comuns de rede do Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

## <a name="set-up-azure-key-vault-for-security"></a>Configurar o Cofre de chaves do Azure para a segurança
O Service Fabric utiliza certificados para fornecer autenticação e encriptação para proteger um cluster e respetivas aplicações.

O Service Fabric utiliza certificados x. 509 para proteger um cluster e para fornecer funcionalidades de segurança da aplicação. Utilizar o Cofre de chaves do Azure para [gerir certificados](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure) para clusters de Service Fabric no Azure. O fornecedor de recursos do Azure que cria os clusters obtém os certificados a partir de um cofre de chaves. O fornecedor instala, em seguida, os certificados nas VMs, quando o cluster é implementado no Azure.

Existe uma relação de certificado entre [Cofre de chaves do Azure](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), o cluster do Service Fabric e o fornecedor de recursos utiliza os certificados. Quando o cluster for criado, informações sobre a relação de certificado são armazenadas no Cofre de chaves.

Existem dois passos básicos para configurar um cofre de chaves:
1. Crie um grupo de recursos especificamente para o seu Cofre de chaves.

    Recomendamos que coloque o Cofre de chaves no seu próprio grupo de recursos. Esta ação ajuda a evitar a perda das chaves e segredos, se forem removidos outros grupos de recursos, tais como o armazenamento, computação ou o grupo que contém o cluster. O grupo de recursos que contém o seu Cofre de chaves deve estar na mesma região que o cluster que está a ser utilizado.

2. Crie um cofre de chaves no novo grupo de recursos.

    O Cofre de chaves deve estar ativado para implementação. O fornecedor de recursos de computação, em seguida, pode obter os certificados a partir do cofre e instalá-los nas instâncias de VM.

Para obter mais informações sobre como configurar um cofre de chaves, consulte o artigo [introdução ao Cofre de chaves do Azure](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

## <a name="assign-users-to-roles"></a>Atribuir utilizadores a funções
Depois de criar as aplicações para representar o cluster, atribuir os seus utilizadores para as funções que são suportadas pelo Service Fabric: só de leitura e administrador. Pode atribuir estas funções utilizando o portal do Azure.

>[!NOTE]
> Para obter mais informações sobre como utilizar funções no Service Fabric, consulte [controlo de acesso baseado em funções para clientes de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

Recursos de infraestrutura de serviço do Azure suporta dois tipos de controlo de acesso para clientes que estão ligados a um [cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm): administrador e utilizador. O administrador do cluster pode utilizar o controlo de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de utilizadores. Controlo de acesso faz com que o cluster mais segura.

## <a name="next-steps"></a>Passos seguintes
- Configurar o Service Fabric [ambiente de desenvolvimento](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started).
- Saiba mais sobre [as opções de suporte do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
