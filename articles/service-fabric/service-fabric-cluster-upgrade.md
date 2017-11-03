---
title: Atualizar um cluster do Service Fabric do Azure | Microsoft Docs
description: "Atualize o código de Service Fabric e/ou a configuração que executa um cluster do Service Fabric, incluindo a definição do modo de atualização do cluster, atualizar certificados, a adição de portas de aplicação, se o fizer patches de SO, e assim sucessivamente. O que esperar quando as atualizações são executadas?"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/10/2017
ms.author: chackdan
ms.openlocfilehash: 7ea71ab891583c51b3c07a4d0a9f0b4f54e56669
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-an-azure-service-fabric-cluster"></a>Atualizar um cluster do Service Fabric do Azure
> [!div class="op_single_selector"]
> * [Cluster do Azure](service-fabric-cluster-upgrade.md)
> * [Cluster autónomo](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

Para qualquer sistema moderno, estruturar para upgradability é chave para alcançar o sucesso de longo prazo do produto. Um cluster do Service Fabric do Azure é um recurso que possui, mas está parcialmente gerida pela Microsoft. Este artigo descreve o que é gerido automaticamente e o que pode configurar a próprio.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controlar a versão de recursos de infraestrutura que é executado no seu Cluster
Pode definir o cluster para receber atualizações de recursos de infraestrutura automática à medida que são lançadas pela Microsoft ou pode selecionar uma versão suportada de recursos de infraestrutura que pretende que o cluster na.

Pode fazê-lo ao definir a configuração do cluster "upgradeMode" no portal ou com o Resource Manager no momento da criação ou mais tarde um cluster em direto 

> [!NOTE]
> Certifique-se de que mantém o seu cluster sempre a executar uma versão suportada de recursos de infraestrutura. Como e quando é anunciar o lançamento de uma nova versão dos recursos de infraestrutura de serviço, a versão anterior está marcada para fim de suporte após 60 dias dessa data, no mínimo. os novos lançamentos sejam anunciados [no blogue de equipa do service fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). A nova versão está disponível para, em seguida, escolha. 
> 
> 

14 dias antes da expiração da versão do que seu cluster está em execução, estado de funcionamento é gerado um evento que coloca o cluster para um Estado de funcionamento de aviso. O cluster permanecer num Estado de aviso enquanto não atualizar para uma versão suportada de recursos de infraestrutura.

### <a name="setting-the-upgrade-mode-via-portal"></a>Definir o modo de atualização através do portal
Pode definir o cluster para manual ou automática quando estiver a criar o cluster.

![Create_Manualmode][Create_Manualmode]

Pode configurar o cluster para automático ou manual quando num cluster em direto, utilizando a experiência de gerir. 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Atualizar para uma nova versão de um cluster que está definido para o modo Manual através do portal.
Para atualizar para uma nova versão, tudo o que precisa de fazer é selecione a versão disponível na lista pendente e guardar. A atualização do Fabric obtém arrancou automaticamente. As políticas de estado de funcionamento de cluster (uma combinação de estado de funcionamento do nó e o estado de funcionamento todas as aplicações em execução no cluster) aderiu a durante a atualização.

Se não forem satisfeitas as políticas de estado de funcionamento do cluster, a atualização é revertida. Desloque para baixo deste documento para ler mais sobre como definir as políticas de estado de funcionamento personalizados. 

Depois de corrigir os problemas que resultaram na reversão, terá de iniciar a atualização novamente, ao seguir os mesmos passos.

![Manage_Automaticmode][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>Definir o modo de atualização através de um modelo do Resource Manager
Adicionar a configuração de "upgradeMode" para a definição do recurso Microsoft.ServiceFabric/clusters e defina "clusterCodeVersion" para uma das versões suportadas dos recursos de infraestrutura, conforme mostrado abaixo e, em seguida, implementar o modelo. Os valores válidos para "upgradeMode" são "Manual" ou "Automático"

![ARMUpgradeMode][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Atualizar para uma nova versão de um cluster que está definido para o modo Manual através de um modelo do Resource Manager.
Quando o cluster está no modo Manual, para atualizar para uma nova versão, altere o "clusterCodeVersion" para uma versão suportada e implementá-la. A implementação do modelo, kicks da atualização do Fabric obtém arrancou automaticamente. As políticas de estado de funcionamento de cluster (uma combinação de estado de funcionamento do nó e o estado de funcionamento todas as aplicações em execução no cluster) aderiu a durante a atualização.

Se não forem satisfeitas as políticas de estado de funcionamento do cluster, a atualização é revertida. Desloque para baixo deste documento para ler mais sobre como definir as políticas de estado de funcionamento personalizados. 

Depois de corrigir os problemas que resultaram na reversão, terá de iniciar a atualização novamente, ao seguir os mesmos passos.

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>Obter a lista de todas as versões disponíveis para todos os ambientes para uma determinada subscrição
Execute o seguinte comando e, deve obter um resultado semelhante a isto.

"supportExpiryUtc" indica o quando uma determinada versão está prestes a expirar ou expirou. A versão mais recente não tem uma data válida - tem um valor de "9999-12-31T23:59:59.9999999", que tal significa que a data de expiração não estiver ainda definida.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }


```

## <a name="fabric-upgrade-behavior-when-the-cluster-upgrade-mode-is-automatic"></a>Comportamento de atualização de recursos de infraestrutura quando o modo de atualização do cluster é automático
A Microsoft tem o código de recursos de infraestrutura e a configuração que é executado num cluster do Azure. As atualizações automáticas monitorizadas para o software, efetuar numa base como necessário. Estas atualizações podem ser código, configuração ou ambos. Para se certificar de que a aplicação não sofrerá nenhum impacto ou um impacto mínimo devido a estas atualizações, iremos efetuar as atualizações nas seguintes fases:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fase 1: Efetuar uma atualização através da utilização de todas as políticas de estado de funcionamento de cluster
Durante esta fase, as atualizações continuar um domínio de atualização a uma hora e as aplicações que estavam em execução no cluster continuam em execução sem qualquer período de inatividade. As políticas de estado de funcionamento de cluster (uma combinação de estado de funcionamento do nó e o estado de funcionamento todas as aplicações em execução no cluster) aderiu a durante a atualização.

Se não forem satisfeitas as políticas de estado de funcionamento do cluster, a atualização é revertida. Em seguida, é enviado um e-mail para o proprietário da subscrição. O e-mail contém as seguintes informações:

* Notificação tivemos que reverter uma atualização do cluster.
* Sugerida toma ações, se aplicável.
* O número de dias (n) até que iremos executar a fase 2.

Estamos a tentar executar a mesma atualização algumas vezes mais no caso de quaisquer atualizações falhou por motivos de infraestrutura. Depois dos de n dias a contar da data de que mensagem de correio eletrónico foi enviada, podemos avançar para a fase 2.

Se forem satisfeitas as políticas de estado de funcionamento do cluster, a atualização é considerada concluída com êxito e marcada como completa. Esta situação pode ocorrer durante a atualização inicial ou qualquer uma da atualização volta executar nesta fase. Não há nenhum confirmação de e-mail de uma execução bem sucedida. Isto serve para evitar o envio de que existem demasiadas mensagens de correio eletrónico – receber uma mensagem de e-mail deve ser vistos como uma exceção à normal. Esperamos que seja maior parte da atualização do cluster seja bem sucedida sem afetar a disponibilidade de aplicações.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fase 2: Uma atualização é realizada utilizando apenas o políticas de estado de funcionamento predefinida
As políticas de estado de funcionamento nesta fase são definidas de forma a que o número de aplicações que foram bom estado de funcionamento no início da atualização foi alterada durante a duração do processo de atualização. Tal como em fase 1, as atualizações de fase 2 prosseguir um domínio de atualização a uma hora e as aplicações que estavam em execução no cluster continuam em execução sem qualquer período de inatividade. As políticas de estado de funcionamento de cluster (uma combinação de estado de funcionamento do nó e o estado de funcionamento todas as aplicações em execução no cluster) aderiu a durante a atualização.

Se as políticas de estado de funcionamento de cluster em vigor não são cumpridas, a atualização é revertida. Em seguida, é enviado um e-mail para o proprietário da subscrição. O e-mail contém as seguintes informações:

* Notificação tivemos que reverter uma atualização do cluster.
* Sugerida toma ações, se aplicável.
* O número de dias (n) até que iremos executar a fase 3.

Estamos a tentar executar a mesma atualização algumas vezes mais no caso de quaisquer atualizações falhou por motivos de infraestrutura. É enviado um e-mail de lembrete alguns dias antes de dias n estão operacionais. Depois dos de n dias a contar da data de que mensagem de correio eletrónico foi enviada, podemos avançar para a fase 3. As mensagens de correio eletrónico a que enviar-lhe-na fase 2 devem ser colocadas muito a sério e toma ações devem ser colocadas.

Se forem satisfeitas as políticas de estado de funcionamento do cluster, a atualização é considerada concluída com êxito e marcada como completa. Esta situação pode ocorrer durante a atualização inicial ou qualquer uma da atualização volta executar nesta fase. Não há nenhum confirmação de e-mail de uma execução bem sucedida.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Passo 3: Efetuar uma atualização ao utilizar políticas de estado de funcionamento agressiva
Estas políticas de estado de funcionamento nesta fase são adaptadas para a conclusão da atualização em vez do Estado de funcionamento das aplicações. As atualizações de cluster muito poucos acabar nesta fase. Se o cluster obtém para esta fase, é uma boa possibilidade que a aplicação fica em mau estado de funcionamento e/ou perder a disponibilidade.

Semelhante de outras duas fases, atualizações de fase 3 continuar a um domínio de atualização num momento.

Se não forem satisfeitas as políticas de estado de funcionamento do cluster, a atualização é revertida. Estamos a tentar executar a mesma atualização algumas vezes mais no caso de quaisquer atualizações falhou por motivos de infraestrutura. Depois disso, o cluster está afixado, para que já não irá receber suporte e/ou as atualizações.

É enviado um e-mail com estas informações para o proprietário da subscrição, juntamente com as ações que toma. Esperamos que não seja quaisquer outros clusters para colocar num Estado em que fase 3 falhou.

Se forem satisfeitas as políticas de estado de funcionamento do cluster, a atualização é considerada concluída com êxito e marcada como completa. Esta situação pode ocorrer durante a atualização inicial ou qualquer uma da atualização volta executar nesta fase. Não há nenhum confirmação de e-mail de uma execução bem sucedida.

## <a name="cluster-configurations-that-you-control"></a>Configurações de cluster que controla
Para além da capacidade de definir o cluster de modo de atualização, seguem-se as configurações que podem ser alteradas num cluster em direto.

### <a name="certificates"></a>Certificados
Pode adicionar novos ou eliminar facilmente certificados para o cluster e o cliente através do portal. Consulte [neste documento para obter instruções detalhadas](service-fabric-cluster-security-update-certs-azure.md)

![Captura de ecrã que mostra os thumbprints de certificado no portal do Azure.][CertificateUpgrade]

### <a name="application-ports"></a>Portas de aplicação
Pode alterar as portas de aplicação alterando as propriedades de recurso de Balanceador de carga que estão associadas com o tipo de nó. Pode utilizar o portal ou pode utilizar o PowerShell do Resource Manager diretamente.

Para abrir uma nova porta em todas as VMs num tipo de nó, efetue o seguinte:

1. Adicione uma pesquisa de novo para o Balanceador de carga adequado.
   
    Se implementou o cluster através do portal, os balanceadores de carga são denominados "LB-name do grupo de recursos-NodeTypename", um para cada tipo de nó. Uma vez que os nomes de Balanceador de carga são exclusivos apenas dentro de um grupo de recursos, é melhor se procurá-los sob um grupo de recursos específico.
   
    ![Captura de ecrã que mostra a adição de uma sonda para um balanceador de carga no portal.][AddingProbes]
2. Adicione uma nova regra para o Balanceador de carga.
   
    Adicione uma nova regra para o mesmo Balanceador de carga, utilizando a pesquisa que criou no passo anterior.
   
    ![Adicionar uma nova regra para um balanceador de carga no portal.][AddingLBRules]

### <a name="placement-properties"></a>Propriedades de colocação
Para cada um dos tipos de nó, pode adicionar propriedades de colocação personalizada que pretende utilizar nas suas aplicações. NodeType é uma propriedade predefinida que pode utilizar sem adicionar explicitamente.

> [!NOTE]
> Para obter detalhes sobre a utilização de restrições de posicionamento, propriedades de nó e como defini-las, consulte a secção "Colocação restrições e propriedades de nó" no documento de Gestor de recursos de Cluster do Service Fabric no [que descrevem o seu Cluster](service-fabric-cluster-resource-manager-cluster-description.md).
> 
> 

### <a name="capacity-metrics"></a>Métricas de capacidade
Para cada um dos tipos de nó, pode adicionar métricas de capacidade personalizados que pretende utilizar nas suas aplicações para comunicar carga. Para obter detalhes sobre a utilização das métricas de capacidade para comunicar carga, consulte os documentos de Gestor de recursos de Cluster do serviço de recursos de infraestrutura em [que descrevem o seu Cluster](service-fabric-cluster-resource-manager-cluster-description.md) e [métricas e carga](service-fabric-cluster-resource-manager-metrics.md).

### <a name="fabric-upgrade-settings---health-polices"></a>Políticas de estado de funcionamento de definições de atualização de recursos de infraestrutura-
Pode especificar políticas de estado de funcionamento personalizado para atualização do fabric. Se tiver definido o cluster para as atualizações automáticas de recursos de infraestrutura, em seguida, estas políticas são aplicadas a fase 1 das automática das atualizações do fabric.
Se tiver definido o cluster para recursos de infraestrutura Manual atualizações, estas políticas ser aplicadas sempre que selecione uma nova versão acionar o sistema para iniciar a atualização do fabric no seu cluster. Se não substituir as políticas, são utilizadas as predefinições.

Pode especificar as políticas de estado de funcionamento personalizado ou rever as definições atuais sob o painel "atualização do fabric", ao selecionar as definições avançadas de atualização. Reveja a imagem seguinte sobre a. 

![Gerir políticas de estado de funcionamento personalizado][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>Personalizar as definições de recursos de infraestrutura para o cluster
Consulte [definições de recursos de infraestrutura de cluster de recursos de infraestrutura do serviço](service-fabric-cluster-fabric-settings.md) em que e como pode personalizá-las.

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>Patches de SO em VMs que compõem o cluster
Consulte [Patch Orchestration aplicação](service-fabric-patch-orchestration-application.md) que pode ser implementado no seu cluster a instalação de patches do Windows Update de forma orchestrated, mantendo os serviços de sempre. 

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>Atualizações do SO em VMs que compõem o cluster
Se tem de atualizar a imagem do SO nas máquinas virtuais do cluster, deve fazê-lo uma VM cada vez. É responsáveis para esta atualização – não está actualmente sem automatização para este.

## <a name="next-steps"></a>Passos seguintes
* Saiba como personalizar algumas do [definições de recursos de infraestrutura de cluster de recursos de infraestrutura do serviço](service-fabric-cluster-fabric-settings.md)
* Saiba como [reduzir e ampliar o seu cluster](service-fabric-cluster-scale-up-down.md)
* Saiba mais sobre [as atualizações de aplicações](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
