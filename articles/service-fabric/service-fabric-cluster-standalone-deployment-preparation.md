---
title: "Preparação de implementação de Cluster autónomo de recursos de infraestrutura de serviço do Azure | Microsoft Docs"
description: "Documentação relacionada com a preparação do ambiente e criar a configuração do cluster, ser considerados antes de implementar um cluster que se destina a processar uma carga de trabalho de produção."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/12/2017
ms.author: dekapur;maburlik;chackdan
ms.openlocfilehash: 67d47739c27081c4e10bf11988ed121ff02d8bb0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
<a id="preparemachines"></a>

# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Planear e preparar a implementação de cluster do Service Fabric autónomo
Execute os seguintes passos antes de criar o cluster.

## <a name="plan-your-cluster-infrastructure"></a>Planear a infraestrutura de cluster
Está prestes a criar um cluster do Service Fabric nas máquinas que é "proprietário", pelo que pode decidir que tipos de falhas de que pretende que o cluster sobreviver. Por exemplo, precisar de separar as linhas de energia ou ligações à Internet fornecidas para estas máquinas? Além disso, considere a segurança física destas máquinas. Em que as máquinas estão e quem tem acesso aos mesmos? Depois de efetuar estas decisões, pode mapear logicamente as máquinas para vários domínios de falhas (consulte o passo seguinte). A infraestrutura de planeamento de clusters de produção é mais envolvida para clusters de teste.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Determinar o número de domínios de falhas e domínios de atualização
A [ *domínio de falhas* (DF)](service-fabric-cluster-resource-manager-cluster-description.md) é uma unidade física de falha e está diretamente relacionada com a infraestrutura física nos centros de dados. Um domínio de falhas é constituído por componentes de hardware (computadores, comutadores, redes e mais) que partilham um único ponto da falha. Apesar de não há nenhum mapeamento 1:1 entre domínios de falhas e bastidores, aproximadamente ligado falando, cada bastidor pode ser considerada um domínio de falhas.

Quando especificar FDs em Clusterconfig, pode escolher o nome de cada DF. Service Fabric suporta FDs hierárquicas, pelo que pode refletir a topologia de infraestrutura nos mesmos.  Por exemplo, os FDs seguintes são válidos:

* "faultDomain": "DF: / Rack1/Room1/Machine1"
* "faultDomain": "DF: / FD1"
* "faultDomain": "DF: / Room1/Rack1/PDU1/M1"

Um *domínio de atualização* (UD) é uma unidade lógica de nós. Durante as atualizações orquestradas do Service Fabric (uma atualização da aplicação ou uma atualização de cluster), todos os nós num UD direcionados para baixo para efetuar a atualização, enquanto nós nos outros UDs permanecem disponíveis para servir pedidos. As atualizações de firmware que efetuar no seu máquinas não honrar UDs, pelo que terá de efetuá-los um computador de cada vez.

A forma mais simples de pensar sobre estes conceitos é ponderar FDs como a unidade de falha não planeada e UDs como a unidade de manutenção planeada.

Quando especificar UDs em Clusterconfig, pode escolher o nome de cada UD. Por exemplo, os seguintes nomes são válidos:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Blue"

Para obter informações mais detalhadas sobre FDs e UDs, consulte [que descrevem um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

Um cluster em produção deve abranger FDs, pelo menos, três para ser suportado num ambiente de produção, se tiver de controlo total sobre a manutenção e gestão de nós, ou seja, é responsáveis pela atualização e substituir máquinas. Para clusters em execução em ambientes (ou seja, instâncias de VM do Amazon Web Services) em que não têm controlo total sobre as máquinas, deve ter um mínimo de cinco FDs no seu cluster. Cada DF pode ter um ou mais nós. Este procedimento impede que problemas causados por atualizações de máquina e atualizações que, consoante os respetivos temporização podem intefere com a execução de aplicações e serviços nos clusters.

## <a name="determine-the-initial-cluster-size"></a>Determinar o tamanho de cluster inicial

Geralmente, o número de nós do cluster é determinado com base nas suas necessidades empresariais, ou seja, o número de serviços e contentores estarão em execução no cluster e a quantidade de recursos tem de suportar as cargas de trabalho. Para clusters de produção, recomendamos que ter, pelo menos, 5 nós do cluster, 5 FDs de expansão. No entanto, como descrito acima, se tiver o controlo total sobre os nós e pode abranger três FDs, em seguida, três nós devem também efetuar a tarefa.

Executar cargas de trabalho com monitorização de estado de clusters de teste devem ter três nós, enquanto que os clusters de teste apenas em execução alojem cargas de trabalho só precisam de um nó. Deve também ser salientado que, para fins de desenvolvimento, pode ter mais de um nó num determinado computador. No entanto, num ambiente de produção Service Fabric suporta apenas um nó por máquina física ou virtual.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Preparar as máquinas que irão servir como nós

Seguem-se algumas especificações recomendadas para cada máquina que pretende adicionar ao cluster:

* Um mínimo de 16 GB de RAM
* Um mínimo de 40 GB de espaço em disco disponível
* Um 4 núcleos ou CPU superior
* Conectividade a uma rede segura ou redes para todas as máquinas
* Windows Server 2012 R2 ou Windows Server 2016
* [.NET framework 4.5.1 ou superior](https://www.microsoft.com/download/details.aspx?id=40773), instalação completa
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell)
* O [RemoteRegistry serviço](https://technet.microsoft.com/library/cc754820) deve ser executado em todas as máquinas

O administrador do cluster, implementar e configurar o cluster tem de ter [privilégios de administrador](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) em cada uma das máquinas. Não pode instalar o Service Fabric num controlador de domínio.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Transferir o pacote de autónoma do Service Fabric para o Windows Server
[Transferir a ligação - pacote de autónomo do Service Fabric - Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) e deszipe o pacote, a uma máquina de implementação que não faz parte do cluster ou para uma das máquinas que serão uma parte do cluster.

## <a name="modify-cluster-configuration"></a>Modificar configuração de cluster
Para criar um cluster autónomo tem de criar um cluster configuração Clusterconfig um ficheiro autónomo, que descreve a especificação de cluster. Pode baseá o ficheiro de configuração nos modelos de localizar a ligação abaixo. <br>
[Configurações de Cluster autónomo](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Para obter detalhes sobre as secções neste ficheiro, consulte [definições de configuração do cluster do Windows autónomo](service-fabric-cluster-manifest.md).

Abra um dos ficheiros Clusterconfig do pacote que transferiu e modificar as definições seguintes:
| **Definição de configuração** | **Descrição** |
| --- | --- |
| **NodeTypes** |Tipos de nó permitem-lhe separar os nós do cluster em vários grupos. Um cluster tem de ter pelo menos um NodeType. Todos os nós de um grupo têm as seguintes características comuns: <br> **Nome** -este é o nome de tipo de nó. <br>**Portas de pontos finais** - estes são denominados vários pontos finais (portas) que estão associados este tipo de nó. Pode utilizar qualquer número de porta que desejar, desde não entram em conflito com mais nada neste manifesto e já não estão a ser utilizada por qualquer outra aplicação em execução na máquina/VM. <br> **As propriedades de colocação** -descrevem estas propriedades para este tipo de nó que utilizar como restrições de posicionamento para os serviços do sistema ou os serviços. Estas propriedades são definidos pelo utilizador pares chave-valor que fornecem dados meta adicionais para um determinado nó. Exemplos de propriedades de nó seria se o nó tem um disco rígido ou cartão de gráficos, o número de spindles no respetivo disco rígido, núcleos e outras propriedades físicas. <br> **As capacidades** -as capacidades de nó definem o nome e a quantidade de um recurso específico que um determinado nó tem disponível para consumo. Por exemplo, um nó pode definir que tem capacidade para uma métrica chamada "MemoryInMb" e que tem de 2048 MB disponível por predefinição. Estes capacidades são utilizadas no tempo de execução para garantir que os serviços que necessitem de determinado quantidades de recursos são colocados em nós com esses recursos disponíveis nas quantidades necessárias.<br>**IsPrimary** – se tiver mais do que um NodeType definido Certifique-se de que apenas um está definido como principal com o valor *verdadeiro*, que é onde o sistema de serviços executar. Todos os outros tipos de nó devem ser definidos como o valor *false* |
| **Nós** |Estes são os detalhes para cada um de nós que fazem parte do cluster (tipo de nó, o nome do nó, endereço IP, o domínio de falhas e o domínio de atualização do nó). As máquinas que pretende que o cluster ser criado no têm de ser indicados aqui com os respetivos endereços IP. <br> Se utilizar o mesmo endereço IP para todos os nós, em seguida, uma caixa de um cluster for criada, que pode utilizar para fins de teste. Não utilize clusters de uma caixa para implementar cargas de trabalho de produção. |

Depois da configuração do cluster tenha tido todas as definições configuradas para o ambiente, pode ser testada no ambiente de cluster (passo 7).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Configuração do ambiente

Quando um administrador de cluster configura um cluster do Service Fabric autónomo, o ambiente tem de ser configurado com os seguintes critérios: <br>
1. O utilizador criar o cluster deve ter privilégios de segurança ao nível do administrador para todas as máquinas que estão listados como nós do ficheiro de configuração de cluster.
2. Máquina a partir do qual o cluster for criado, bem como em cada computador nó do cluster tem de:
* SDK de Service Fabric foi desinstalado com
* Ter tempo de execução do Service Fabric desinstalado 
* Ativou o serviço de Windows Firewall (mpssvc)
* Ativou o serviço registo remoto (remoteregistry)
* Ter o ficheiro que partilha (SMB) ativado
* Tem as portas necessárias abertas, com base nas portas de configuração de cluster
* Tem as portas necessárias abertas para o serviço Windows SMB e de registo remoto: 135, 137, 138, 139 e 445
* Tem conectividade de rede para outro
3. Nenhuma das máquinas de nó de cluster deve ser um controlador de domínio.
4. Se o cluster seja implementado é um cluster seguro, confirme a pré-requisitos estão colocar e estão configurados corretamente com a configuração de segurança necessários.
5. Se as máquinas de cluster não estão acessíveis à internet, defina o seguinte na configuração do cluster:
* Desativar a telemetria: em *propriedades* definir *"ativar telemetria": false*
* Desativar a transferência de versão dos recursos de infraestrutura automática & notificações que a versão atual do cluster está prestes a fim de suporte: em *propriedades* definir *"fabricClusterAutoupgradeEnabled": false*
* Em alternativa, se o acesso à internet de rede está limitado aos domínios indicados em branco, os domínios abaixo são necessários para a atualização automática: go.microsoft.com download.microsoft.com

6. Defina as exclusões de antivírus Service Fabric adequadas:

| **Diretórios de excluídas antivírus** |
| --- |
| Programa c:\Programas\Microsoft Service Fabric |
| A variável FabricDataRoot (da configuração de cluster) |
| FabricLogRoot (da configuração de cluster) |

| **Antivírus excluídos processos** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |

## <a name="validate-environment-using-testconfiguration-script"></a>Validar ambiente utilizando o script de TestConfiguration
O script de TestConfiguration.ps1 pode ser encontrado no pacote de autónomo. Este é utilizado como um analisador de melhores práticas para validar alguns dos critérios acima e deve ser utilizada como uma verificação de sanity para validar se um cluster pode ser implementado num determinado ambiente. Se não houver qualquer falha, consulte a lista de em [a configuração do ambiente](service-fabric-cluster-standalone-deployment-preparation.md) para resolução de problemas. 

Este script pode ser executado em qualquer computador que tenha acesso de administrador para todas as máquinas que estão listados como nós do ficheiro de configuração de cluster. A máquina em que este script é executado no tem de ser parte do cluster.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True
```

Atualmente este módulo de testes de configuração não valida a configuração de segurança para que este tem de ser feito de forma independente.  

> [!NOTE]
> Estamos constantemente a efetuar melhoramentos para tornar este módulo mais robusto, para que o se se dá o caso em falta ou defeituoso que achar não é atualmente detetados pelo TestConfiguration, indique através do nosso [suporta canais](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-support).   
> 
> 

## <a name="next-steps"></a>Passos seguintes
* [Criar um cluster autónomo em execução no Windows Server](service-fabric-cluster-creation-for-windows-server.md)
