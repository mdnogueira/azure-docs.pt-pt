---
title: "Implementação Resource Manager e clássico | Microsoft Docs"
description: "Descreve as diferenças entre o modelo de implementação Resource Manager e clássica (ou de gestão do serviço) modelo de implementação."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 7ae0ffa3-c8da-4151-bdcc-8f4f69290fb4
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/09/2017
ms.author: tomfitz
ms.openlocfilehash: 060680fd4a7ce6e0cde406cc4a8f6f3a21d3c588
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>O Azure Resource Manager vs. de implementação clássica: compreender o estado dos seus recursos e modelos de implementação
Neste tópico, pode obter informações sobre o Azure Resource Manager e modelos de implementação clássica, o estado dos seus recursos, e a razão pela qual os recursos que foram implementados com um ou outro. Modelos de implementação clássica e Resource Manager representam duas formas diferentes de implementar e gerir as suas soluções do Azure. Trabalhar com eles através de dois conjuntos diferentes de API e os recursos implementados podem conter diferenças importantes. Os dois modelos não são totalmente compatíveis entre si. Este tópico descreve as diferenças.

Para simplificar a implementação e gestão de recursos, a Microsoft recomenda que utilize o Gestor de recursos para todos os novos recursos. Se for possível, a Microsoft recomenda que Reimplementar recursos existentes através do Resource Manager.

Se não estiver familiarizado no Resource Manager, poderá querer rever primeiro a terminologia definida no [descrição geral do Azure Resource Manager](resource-group-overview.md).

## <a name="history-of-the-deployment-models"></a>Histórico dos modelos de implementação
O modelo de implementação clássica fornecido originalmente pelo Azure. Neste modelo, cada recurso existia independentemente; não ocorreu nenhuma forma de agrupar os recursos relacionados. Em vez disso, era necessário manualmente controlar quais os recursos que efetuou a sua solução ou uma aplicação e não se esqueça de geri-los numa abordagem coordenada. Para implementar uma solução, era necessário que criar cada recurso individualmente através do portal clássico ou criar um script que implementados todos os recursos na ordem correta. Para eliminar uma solução, era necessário eliminar cada recurso individualmente. Facilmente não foi possível aplicar e atualizar as políticas de controlo de acesso para recursos relacionados. Por fim, não é possível aplicar etiquetas a recursos a etiqueta-los com os termos que o ajudam a monitorizar os recursos e gerir a faturação.

Em 2014, o Azure introduzida qual adicionou o conceito de um grupo de recursos do Resource Manager. Um grupo de recursos é um contentor para os recursos que partilham um ciclo de vida comuns. O modelo de implementação Resource Manager oferece várias vantagens:

* Pode implementar, gerir e monitorizar todos os serviços para a sua solução como um grupo, em vez de processamento estes serviços individualmente.
* Pode implementar a solução durante todo o ciclo de vida repetidamente e tem confiança em como os recursos são implementados num estado consistente.
* Pode aplicar o controlo de acesso a todos os recursos no seu grupo de recursos e as políticas são aplicadas automaticamente quando são adicionados novos recursos ao grupo de recursos.
* Pode aplicar etiquetas a recursos para organizar logicamente todos os recursos na sua subscrição.
* Pode utilizar o JavaScript Object Notation (JSON) para definir a infraestrutura para a sua solução. O ficheiro JSON é conhecido como um modelo do Resource Manager.
* Pode definir as dependências entre os recursos, de modo a que sejam implementados na ordem correta.

Quando o Gestor de recursos foi adicionado, todos os recursos retroactively foram adicionados a grupos de recursos predefinido. Se criar um recurso através de implementação clássica agora, o recurso é criado automaticamente dentro de um grupo de recursos predefinido para esse serviço, apesar de não especificou nesse grupo de recursos na implementação. No entanto, acabou existente dentro de um grupo de recursos não significa que o recurso foi convertido para o modelo Resource Manager. Vamos ver como cada serviço processa os modelos de implementação de dois na secção seguinte. 

## <a name="understand-support-for-the-models"></a>Compreender o suporte para os modelos
Quando decidir o modelo de implementação a utilizar para os seus recursos, existem três cenários a ter em consideração:

1. O serviço suporta Resource Manager e fornece um único tipo.
2. O serviço Gestor de recursos de suporte, mas fornece dois tipos - uma para o Gestor de recursos e outro para clássico. Este cenário só se aplica a máquinas virtuais, contas de armazenamento e redes virtuais.
3. O serviço não suporta o Gestor de recursos.

Para detetar se um serviço suporta Resource Manager, consulte [fornecedores de recursos e tipos](resource-manager-supported-services.md).

Se o serviço que pretende utilizar não suporta o Gestor de recursos, deve continuar a utilizar a implementação clássica.

Se o serviço suporta Resource Manager e **não é** uma máquina virtual, a conta de armazenamento ou a rede virtual, pode utilizar o Gestor de recursos sem qualquer complicações.

Para máquinas virtuais, contas de armazenamento e redes virtuais, se o recurso tiver sido criado através de implementação clássica, tem de continuar a funcionar através de operações clássicas. Se a máquina virtual, a conta de armazenamento ou a rede virtual foi criada através da implementação do Resource Manager, deve continuar a utilizar operações de Gestor de recursos. Este distinção pode obter confuso quando a sua subscrição contém uma mistura de recursos criado através da implementação clássica e Resource Manager. Esta combinação de recursos pode criar resultados inesperados, porque os recursos não suportam as mesmas operações.

Em alguns casos, um comando de Gestor de recursos pode obter informações sobre um recurso criado através da implementação clássica, ou pode realizar uma tarefa administrativa, tal como mover um recurso clássico para outro grupo de recursos. No entanto, estes casos não deverá dar-a que o tipo de suporte operações de Gestor de recursos de impressão. Por exemplo, suponha que tem um grupo de recursos que contém uma máquina virtual que foi criada com a implementação clássica. Se executar o seguinte comando do PowerShell do Gestor de recursos:

```powershell
Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines
```

Devolve a máquina virtual:

```powershell
Name              : ExampleClassicVM
ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
ResourceName      : ExampleClassicVM
ResourceType      : Microsoft.ClassicCompute/virtualMachines
ResourceGroupName : ExampleGroup
Location          : westus
SubscriptionId    : {guid}
```

No entanto, o cmdlet do Gestor de recursos **Get-AzureRmVM** só devolve máquinas virtuais implementadas através do Resource Manager. O seguinte comando não devolveu a máquina virtual criada através da implementação clássica.

```powershell
Get-AzureRmVM -ResourceGroupName ExampleGroup
```

Apenas os recursos criados através de etiquetas de suporte do Gestor de recursos. Não é possível aplicar etiquetas a recursos clássicos.

## <a name="resource-manager-characteristics"></a>Características de Gestor de recursos
Para o ajudar a compreender os dois modelos, vamos analisar as características dos tipos de Gestor de recursos:

* Criado através da [portal do Azure](https://portal.azure.com/).
  
     ![Portal do Azure](./media/resource-manager-deployment-model/portal.png)
  
     Para computação, armazenamento e os recursos de rede, tem a opção de implementação Resource Manager ou clássico a utilizar. Selecione **do Resource Manager**.
  
     ![Implementação do Resource Manager](./media/resource-manager-deployment-model/select-resource-manager.png)
* Criados com a versão do Gestor de recursos dos cmdlets do PowerShell do Azure. Estes comandos têm o formato *verbo AzureRmNoun*.

  ```powershell
  New-AzureRmResourceGroupDeployment
  ```

* Criado através da [API de REST do Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) para as operações REST.
* Criados através de comandos da CLI do Azure executar o **arm** modo.
  
  ```azurecli
  azure config mode arm
  azure group deployment create
  ```

* O tipo de recurso não inclui **(clássica)** no nome. A imagem seguinte mostra o tipo como **conta de armazenamento**.
  
    ![aplicação Web](./media/resource-manager-deployment-model/resource-manager-type.png)

## <a name="classic-deployment-characteristics"></a>Características de implementação clássica
Também pode saber o modelo de implementação clássica como o modelo de serviço de gestão.

Recursos criados no modelo de implementação clássica partilhar as seguintes características:

* Criado através da [portal clássico](https://manage.windowsazure.com)
  
     ![Portal clássico](./media/resource-manager-deployment-model/classic-portal.png)
  
     Em alternativa, o portal do Azure e especificar **clássico** implementação (para computação, armazenamento e de rede).
  
     ![Implementação clássica](./media/resource-manager-deployment-model/select-classic.png)
* Criado através da versão a gestão de serviço dos cmdlets do PowerShell do Azure. Estes nomes de comando de ter o formato *verbo AzureNoun*.

  ```powershell
  New-AzureVM
  ```

* Criado através da [API de REST de gestão do serviço](https://msdn.microsoft.com/library/azure/ee460799.aspx) para as operações REST.
* Criados através de comandos da CLI do Azure executar **asm** modo.

  ```azurecli
  azure config mode asm
  azure vm create
  ```
   
* O tipo de recurso inclui **(clássica)** no nome. A imagem seguinte mostra o tipo como **conta de armazenamento (clássica)**.
  
    ![tipo clássico](./media/resource-manager-deployment-model/classic-type.png)

Pode utilizar o portal do Azure para gerir os recursos que foram criados através de implementação clássica.

## <a name="changes-for-compute-network-and-storage"></a>Alterações para computação, rede e armazenamento
O diagrama seguinte apresenta recursos de computação, rede e armazenamento implementados através do Resource Manager.

![Arquitetura do Gestor de Recursos](./media/resource-manager-deployment-model/arm_arch3.png)

Tenha em atenção as relações entre os recursos seguintes:

* Todos os recursos existem dentro de um grupo de recursos.
* A máquina virtual depende de uma conta de armazenamento específica definida no fornecedor de recursos de armazenamento para armazenar os respetivos discos no armazenamento de BLOBs (obrigatório).
* A máquina virtual faz referência a um NIC específico definido no fornecedor de recursos de rede (obrigatório) e um conjunto de disponibilidade definido no fornecedor de recursos de computação (opcional).
* O NIC referencia endereço IP atribuído a máquina virtual (obrigatório), a sub-rede da rede virtual para a máquina virtual (obrigatório) e a um grupo de segurança de rede (opcional).
* A sub-rede dentro de uma rede virtual faz referência a um grupo de segurança de rede (opcional).
* A instância de Balanceador de carga referencia o conjunto de back-end de endereços IP que incluem a NIC de uma máquina virtual (opcional) e faz referência um endereço Balanceador de carga público ou privado IP (opcional).

Seguem-se os componentes e as respetivas relações de implementação clássica:

![arquitetura clássica](./media/resource-manager-deployment-model/arm_arch1.png)

A solução clássica para alojar uma máquina virtual inclui:

* Um serviço de nuvem necessárias que age como um contentor para o alojamento de máquinas virtuais (computação). Máquinas virtuais são automaticamente fornecidas com uma placa de interface de rede (NIC) e um endereço IP atribuído pelo Azure. Além disso, o serviço em nuvem contém uma instância de Balanceador de carga externo, um endereço IP público e pontos finais predefinidos para permitir tráfego remoto de PowerShell de ambiente de trabalho e remoto para máquinas virtuais baseadas no Windows e o tráfego de Secure Shell (SSH) para máquinas virtuais baseadas em Linux.
* Uma conta de armazenamento necessária que armazena os VHDs para uma máquina virtual, incluindo o sistema operativo, discos de dados temporário e adicionais (armazenamento).
* Uma rede virtual opcional que age como um contentor adicional, em que pode criar uma estrutura subnetted e designar a sub-rede onde está localizada a máquina virtual (rede).

A tabela seguinte descreve as alterações na forma como interagem fornecedores de recursos de computação, rede e armazenamento:

| Item | Clássica | Resource Manager |
| --- | --- | --- |
| Serviço em Nuvem para Máquinas Virtuais |O Serviço em Nuvem era um contentor para manter máquinas virtuais que exigiam Disponibilidade a partir de plataforma e o Balanceamento de Carga. |O Serviço em Nuvem já não é um objeto necessário para criar uma Máquina Virtual com o novo modelo. |
| Redes Virtuais |Uma rede virtual é opcional para a máquina virtual. Caso, não é possível implementar a rede virtual com o Resource Manager. |Máquina virtual necessita de uma rede virtual que tenha sido implementada com o Resource Manager. |
| Contas de Armazenamento |A máquina virtual requer uma conta de armazenamento que armazena os VHDs para o sistema operativo, os discos de dados temporário e adicionais. |A máquina virtual requer uma conta de armazenamento para armazenar os respetivos discos no armazenamento de Blobs. |
| Conjuntos de Disponibilidade |A Disponibilidade para a plataforma era indicada configurando o mesmo “AvailabilitySetName” nas Máquinas Virtuais. A contagem máxima de domínios de falhas era 2. |O Conjunto de Disponibilidade é um recurso exposto pelo Fornecedor Microsoft.Compute. As Máquinas Virtuais que requerem elevada disponibilidade têm de ser incluídas no Conjunto de Disponibilidade. A contagem máxima de domínios de falhas é agora 3. |
| Grupos de Afinidade |Os Grupos de Afinidade eram necessários para criar Redes Virtuais. No entanto, com a introdução das Redes Virtuais Regionais, deixaram de ser necessários. |Para simplificar, o conceito de Grupos de Afinidade não existe nas APIs expostas através do Azure Resource Manager. |
| Balanceamento de Carga |A criação de um Serviço em Nuvem fornece um balanceador de carga implícito para as Máquinas Virtuais implementadas. |O Balanceador de Carga é um recurso exposto pelo fornecedor Microsoft.Network. A interface de rede primária das Máquinas Virtuais que precisam de balanceamento de carga deve mencionar o balanceador de carga. Os Balanceadores de Carga podem ser internos ou externos. Uma instância de Balanceador de carga referencia o conjunto de back-end de endereços IP que incluem a NIC de uma máquina virtual (opcional) e faz referência um endereço Balanceador de carga público ou privado IP (opcional). [Leia mais.](../virtual-network/resource-groups-networking.md) |
| Endereço IP Virtual |Serviços cloud obtém um VIP predefinido (endereço de IP Virtual) quando é adicionada uma VM para um serviço em nuvem. O Endereço IP Virtual é o endereço associado ao balanceador de carga implícito. |O endereço IP público é um recurso exposto pelo fornecedor Microsoft.Network. O Endereço IP Público pode ser Estático (Reservado) ou Dinâmico. Os IPs Públicos Dinâmicos podem ser atribuídos a um Balanceador de Carga. Os IPs Públicos podem ser protegidos utilizando Grupos de Segurança. |
| Endereço IP Reservado |Pode reservar um Endereço IP no Azure e associá-lo a um Serviço em Nuvem para garantir que o Endereço IP é temporário. |Um Endereço IP Público pode ser criado no modo “Estático” e oferecer a mesma capacidade de um “Endereço IP Reservado”. De momento, os IPs Públicos Estáticos só podem ser atribuídos a um Balanceador de carga. |
| Endereço IP Público (PIP) por VM |Endereços IP públicos também pode ser associados a uma VM diretamente. |O endereço IP público é um recurso exposto pelo fornecedor Microsoft.Network. O Endereço IP Público pode ser Estático (Reservado) ou Dinâmico. No entanto, atualmente só os IPs Públicos Dinâmicos podem ser atribuídos a uma Interface de Rede para obter um IP Público por VM. |
| Pontos Finais |Os Pontos Finais de Entrada tinham de ser configurados numa Máquina Virtual para estarem abertos à conectividade para determinadas portas. Um dos modos comuns de ligar a máquinas virtuais era configurando pontos finais de entrada. |As Regras NAT de Entrada podem ser configuradas em Balanceadores de Carga para obter a mesma capacidade de ativação de pontos finais em portas específicas para estabelecer ligação às VMs. |
| Nome DNS |Um serviço em nuvem teria de obter um nome DNS implícito globalmente exclusivo. Por exemplo: `mycoffeeshop.cloudapp.net`. |Os Nomes DNS são parâmetros opcionais que podem ser especificados num recurso de Endereço IP Público. O FQDN é o seguinte formato - `<domainlabel>.<region>.cloudapp.azure.com`. |
| Interfaces de Rede |As Interfaces de Rede Primária e Secundária e as respetivas propriedades eram definidas como configuração de rede de uma Máquina Virtual. |A Interface de Rede é um recurso exposto pelo Fornecedor Microsoft.Network. O ciclo de vida da Interface de Rede não está associado a uma Máquina Virtual. Referencia endereço IP atribuído a máquina virtual (obrigatório), a sub-rede da rede virtual para a máquina virtual (obrigatório) e a um grupo de segurança de rede (opcional). |

Para saber mais sobre como ligar redes virtuais a partir de modelos de implementação diferentes, consulte [ligar redes virtuais a partir de modelos de implementação diferentes no portal do](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="migrate-from-classic-to-resource-manager"></a>Migrar do clássico para o Resource Manager
Se estiver pronto para migrar os recursos de implementação clássica para implementação do Resource Manager, consulte:

1. [Técnica descrição profunda sobre a migração de plataforma suportada do clássico para o Azure Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-deep-dive.md)
2. [Migração de plataforma suportada dos recursos IaaS do clássico para o Azure Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-overview.md)
3. [Migrar os recursos de IaaS do clássico para o Azure Resource Manager, utilizando o Azure PowerShell](../virtual-machines/windows/migration-classic-resource-manager-ps.md)
4. [Migrar recursos IaaS do clássico para o Azure Resource Manager, utilizando a CLI do Azure](../virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes
**Pode criar uma máquina virtual utilizando o Azure Resource Manager para implementar numa rede virtual criada com a implementação clássica?**

Não é suportada. Não é possível utilizar o Azure Resource Manager para implementar uma máquina virtual numa rede virtual que foi criada utilizando a implementação clássica.

**Pode criar uma máquina virtual utilizando o Azure Resource Manager a partir de uma imagem de utilizador que foi criada com as APIs de gestão de serviço do Azure?**

Não é suportada. No entanto, pode copiar os ficheiros VHD de uma conta de armazenamento que foi criada com as APIs de gestão de serviço e adicioná-los para uma nova conta criada através do Azure Resource Manager.

**O que é o impacto na quota da minha subscrição?**

As quotas para as máquinas virtuais, redes virtuais e contas de armazenamento criadas através do Azure Resource Manager são separadas das quotas de outras. Cada subscrição recebe quotas para criar os recursos com as novas APIs. Pode ler mais sobre as quotas adicionais [aqui](../azure-subscription-service-limits.md).

**Pode continuar a utilizar os meus scripts automatizados para o aprovisionamento de máquinas virtuais, redes virtuais e contas de armazenamento através das APIs do Resource Manager?**

Toda a automatização e os scripts que criou continuarem a trabalhar para as máquinas virtuais existentes, redes virtuais criadas em modo de gestão de serviço do Azure. No entanto, os scripts têm de ser atualizadas para utilizar o novo esquema para criarem os mesmos recursos através do modo Resource Manager.

**Onde posso encontrar exemplos de modelos Azure Resource Manager?**

Um conjunto abrangente de modelos de arranque pode ser encontrado no [modelos de início rápido do Azure Resource Manager](https://azure.microsoft.com/documentation/templates/).

## <a name="next-steps"></a>Passos seguintes
* A guiá-lo através da criação de modelo que define uma máquina virtual, a conta de armazenamento e a rede virtual, consulte [instruções do modelo do Resource Manager](resource-manager-template-walkthrough.md).
* Para ver os comandos para implementar um modelo, consulte [implementar uma aplicação com o modelo Azure Resource Manager](resource-group-template-deploy.md).

