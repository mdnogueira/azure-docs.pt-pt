---
title: "Serviço de metadados de instância do Azure | Microsoft Docs"
description: "Interface rESTful para obter informações sobre computação, rede e eventos de manutenções de VM com Linux."
services: virtual-machines-linux
documentationcenter: 
author: harijayms
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/10/2017
ms.author: harijayms
ms.openlocfilehash: 1ed64ece4d05dea93fd15e24aaf9921d8614277e
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="azure-instance-metadata-service"></a>Serviço de metadados de instância do Azure


O serviço de metadados de instância do Azure fornece informações sobre as instâncias de máquina virtual que podem ser utilizadas para gerir e configurar as máquinas virtuais em execução.
Isto inclui informações como SKU, a configuração de rede e os eventos de manutenção futura. Para obter mais informações sobre o tipo de informações é disponível, consulte [categorias de metadados](#instance-metadata-data-categories).

Serviço de metadados de instância do Azure é um ponto final de REST acessível para todas as VMs de IaaS criados através de [do Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). O ponto final está disponível um endereço de IP bem conhecidos não encaminháveis internos (`169.254.169.254`) que podem ser acedidos apenas a partir de dentro da VM.

> [!IMPORTANT]
> Este serviço é **geralmente disponível** em todas as regiões do Azure.  Regularmente recebe atualizações para expor as novas informações sobre instâncias de máquina virtual. Esta página reflete o atualizados [categorias de dados](#instance-metadata-data-categories) disponíveis.

## <a name="service-availability"></a>Disponibilidade do serviço
O serviço está disponível em todos os todos os geralmente disponível regiões do Azure. Nem todos os versão de API poderão estar disponíveis em todas as regiões do Azure.

Regiões                                        | Disponibilidade?                                 | Versões suportadas
-----------------------------------------------|-----------------------------------------------|-----------------
[Todas as regiões do Azure Global geralmente disponíveis](https://azure.microsoft.com/regions/)     | Geralmente disponível   | 2017-04-02, 2017-08-01
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Geralmente disponível | 2017-04-02
[Azure China](https://www.azure.cn/)                                                           | Geralmente disponível | 2017-04-02
[Datacenters do Azure](https://azure.microsoft.com/overview/clouds/germany/)                    | Geralmente disponível | 2017-04-02

Esta tabela é atualizada quando existirem atualizações de serviço e ou existem novas versões suportadas

Para experimentar o serviço de metadados de instância, crie uma VM a partir [do Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) ou [portal do Azure](http://portal.azure.com) nas regiões acima e siga os exemplos abaixo.

## <a name="usage"></a>Utilização

### <a name="versioning"></a>Controlo de versões
O serviço de metadados de instância é com a versão. As versões são obrigatórias e a versão atual do Global Azure é `2017-08-01`. Versões suportadas atuais são (2017-04-02, 2017-08-01)

> [!NOTE] 
> Pré-visualização as versões anteriores dos eventos agendadas suportados {mais recente} como a api-version. Este formato já não é suportado e será preterido no futuro.

À medida que adiciona as versões mais recentes, as versões mais antigas podem ainda ser acedidas para compatibilidade, se os scripts terem dependências de formatos de dados específicos. No entanto, tenha em atenção que a anterior version(2017-03-01) de pré-visualização poderão não estar disponíveis assim que o serviço estiver geralmente disponível.

### <a name="using-headers"></a>Utilizar cabeçalhos
Ao consultar o serviço de metadados de instância, tem de fornecer o cabeçalho `Metadata: true` para garantir que o pedido não foi redirecionado inadvertidamente.

### <a name="retrieving-metadata"></a>Obtenção de metadados

Metadados da instância estão disponível para executar as VMs criado/geridos através de [do Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Todas as categorias de dados para uma instância de máquina virtual utilizando o seguinte pedido de acesso:

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02"
```

> [!NOTE] 
> Todas as consultas de metadados de instância diferenciam maiúsculas de minúsculas.

### <a name="data-output"></a>Saída de dados
Por predefinição, o serviço de metadados de instância devolve a data no formato JSON (`Content-Type: application/json`). No entanto, APIs diferentes devolvem dados em diferentes formatos se solicitada.
A tabela seguinte é uma referência de APIs podem suportar outros formatos de dados.

API | Formato de dados predefinido | Outros formatos
--------|---------------------|--------------
/instance | JSON | Texto
/scheduledevents | JSON | Nenhum

Para aceder a um formato de resposta não predefinido, especifique o formato de pedido como um parâmetro de cadeia de consulta no pedido. Por exemplo:

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02&format=text"
```

### <a name="security"></a>Segurança
Ponto final do serviço de metadados de instância é acessível apenas a partir de dentro de instância de máquina virtual em execução num endereço IP não encaminháveis internos. Além disso, qualquer pedido com um `X-Forwarded-For` cabeçalho é rejeitado pelo serviço.
É também necessário pedidos para conter uma `Metadata: true` cabeçalho para se certificar de que o pedido real foi diretamente pretendida e não uma parte de redirecionamento não intencional. 

### <a name="error"></a>Erro
Se existir um elemento de dados não encontrado ou um pedido com formato incorreto, o serviço de metadados de instância devolve erros de HTTP padrão. Por exemplo:

Código de estado de HTTP | Razão
----------------|-------
200 OK |
Pedido de 400 incorreta | Falta `Metadata: true` cabeçalho
404 Não Encontrado | O elemento pedido não existe 
405 Método não permitido | Apenas `GET` e `POST` os pedidos são suportados
429 demasiados pedidos | A API atualmente suporta um máximo de 5 consultas por segundo
Erro no serviço 500     | Tente novamente após algum tempo

### <a name="examples"></a>Exemplos

> [!NOTE] 
> Todas as respostas de API são cadeias JSON. Todas as respostas de exemplo seguintes são impressas pretty para legibilidade.

#### <a name="retrieving-network-information"></a>A obtenção de informações de rede

**Pedido**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Resposta**

> [!NOTE] 
> A resposta é uma cadeia JSON. A resposta de exemplo seguinte é impresso pretty para legibilidade.

```
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="retrieving-public-ip-address"></a>Obter o endereço IP público

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-04-02&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Obter todos os metadados para uma instância

**Pedido**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

**Resposta**

> [!NOTE] 
> A resposta é uma cadeia JSON. A resposta de exemplo seguinte é impresso pretty para legibilidade.

```
{
  "compute": {
    "location": "westus",
    "name": "avset2",
    "offer": "UbuntuServer",
    "osType": "Linux",
    "placementGroupId": "",
    "platformFaultDomain": "1",
    "platformUpdateDomain": "1",
    "publisher": "Canonical",
    "resourceGroupName": "myrg",
    "sku": "16.04-LTS",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "",
    "version": "16.04.201708030",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmSize": "Standard_D1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Obtenção de metadados na máquina Virtual do Windows

**Pedido**

Metadados de instância podem ser obtido no Windows através do utilitário de PowerShell `curl`: 

```
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-04-02 | select -ExpandProperty Content
```

Ou através de `Invoke-RestMethod` cmdlet:
    
```
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2017-04-02 -Method get 
```

**Resposta**

> [!NOTE] 
> A resposta é uma cadeia JSON. A resposta de exemplo seguinte é impresso pretty para legibilidade.

```
{
  "compute": {
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "MicrosoftSQLServer",
    "sku": "Enterprise",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": [
            
          ]
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="instance-metadata-data-categories"></a>Categorias de dados de metadados de instância
As seguintes categorias de dados estão disponíveis através do serviço de metadados da instância:

Dados | Descrição | Versão introduzida 
-----|-------------|-----------------------
localização | Região do Azure a VM está em execução no | 2017-04-02 
nome | Nome da VM | 2017-04-02
oferta | Disponibilizam informações para a imagem VM. Este valor só está presente para imagens implementadas a partir da Galeria de imagem do Azure. | 2017-04-02
Fabricante | Publicador de imagem de VM | 2017-04-02
SKU | SKU específico para a imagem VM | 2017-04-02
Versão | Versão da imagem VM | 2017-04-02
osType | Linux ou do Windows | 2017-04-02
platformUpdateDomain |  [Domínio de atualização](manage-availability.md) a VM está em execução no | 2017-04-02
platformFaultDomain | [Domínio de falhas](manage-availability.md) a VM está em execução no | 2017-04-02
vmId | [Identificador exclusivo](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para a VM | 2017-04-02
vmSize | [Tamanho da VM](sizes.md) | 2017-04-02
subscriptionId | Subscrição do Azure para a Máquina Virtual | 2017-08-01
etiquetas | [Etiquetas](../../azure-resource-manager/resource-group-using-tags.md) para a Máquina Virtual  | 2017-08-01
resourceGroupName | [Grupo de recursos](../../azure-resource-manager/resource-group-overview.md) para a Máquina Virtual | 2017-08-01
placementGroupId | [Grupo de posicionamento](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) do seu dimensionamento da Máquina Virtual definido | 2017-08-01
IPv4/privateIpAddress | Endereço IPv4 local da VM | 2017-04-02
IPv4/publicIpAddress | Endereço IPv4 público da VM | 2017-04-02
endereço de sub-rede / | Endereço de sub-rede da VM | 2017-04-02 
prefixo de sub-rede / | Prefixo de sub-rede, 24 de exemplo | 2017-04-02 
IPv6/ipAddress | Endereço IPv6 local da VM | 2017-04-02 
MacAddress | Endereço mac VM | 2017-04-02 
scheduledevents | Está a ser atualmente consulte de pré-visualização pública [scheduledevents](scheduled-events.md) | 2017-03-01

## <a name="example-scenarios-for-usage"></a>Exemplos de cenários de utilização  

### <a name="tracking-vm-running-on-azure"></a>Controlo de VM em execução no Azure

Como um fornecedor de serviços, poderá ser necessário para controlar o número de VMs com o seu software ou agentes que precisam de controlar exclusividade da VM. Para conseguir obter um ID exclusivo para uma VM, utilize o `vmId` campo do serviço de metadados de instância.

**Pedido**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-04-02&format=text"
```

**Resposta**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Colocação de contentores, partições de dados com base em domínio de falhas/atualização 

Alguns cenários, colocação das réplicas de dados diferentes é prime importância. Por exemplo, [colocação de réplica do HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) ou colocação contentor através de um [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) pode necessitar de conhecer a `platformFaultDomain` e `platformUpdateDomain` a VM está em execução no.
Pode consultar estes dados diretamente através do serviço de metadados de instância.

**Pedido**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-04-02&format=text" 
```

**Resposta**

```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Obter mais informações sobre a VM durante caso de suporte 

Como um fornecedor de serviços, poderá receber uma chamada de suporte em que gostaria de saber mais informações sobre a VM. Pedir ao cliente para partilhar os metadados de computação pode fornecer informações básicas para o suporte profissional conhecer o tipo de VM no Azure. 

**Pedido**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-04-02"
```

**Resposta**

> [!NOTE] 
> A resposta é uma cadeia JSON. A resposta de exemplo seguinte é impresso pretty para legibilidade.

```
{
  "compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }
}
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Exemplos de chamar o serviço de metadados utilizando idiomas diferentes dentro da VM 

Idioma | Exemplo 
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/Master/IMDSSample.RB
Aceda Lang  | https://github.com/Microsoft/azureimds/blob/Master/imdssample.go            
Python   | https://github.com/Microsoft/azureimds/blob/Master/IMDSSample.PY
C++      | https://github.com/Microsoft/azureimds/blob/Master/IMDSSample-Windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/Master/IMDSSample.CS
JavaScript | https://github.com/Microsoft/azureimds/blob/Master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/Master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/Master/IMDSSample.SH
    

## <a name="faq"></a>FAQ
1. Estou a obter o erro `400 Bad Request, Required metadata header not specified`. O que significa?
   * O serviço de metadados de instância requer o cabeçalho `Metadata: true` transmitido no pedido. Transmitir este cabeçalho na chamada REST permite o acesso ao serviço de metadados de instância. 
2. Por que razão estou posso obter não informações de computação para a minha VM?
   * O serviço de metadados de instância só suporta atualmente instâncias criadas com o Azure Resource Manager. No futuro, iremos pode adicionar suporte para as VMs de serviço de nuvem.
3. Criar um algum volta a minha máquina Virtual através do Azure Resource Manager. Por que razão estou posso não ver as informações de metadados de computação?
   * Para quaisquer VMs criadas após Sep 2016, adicione um [Tag](../../azure-resource-manager/resource-group-using-tags.md) para começar a ver metadados de computação. Para VMs anteriores (criadas antes de Sep 2016), adicionar/remover discos de dados ou extensões para a VM para atualizar os metadados.
4. Não estou a ver todos os dados preenchidos nova versão de 2017-08-01
   * Para quaisquer VMs criadas após Sep 2016, adicione um [Tag](../../azure-resource-manager/resource-group-using-tags.md) para começar a ver metadados de computação. Para VMs anteriores (criadas antes de Sep 2016), adicionar/remover discos de dados ou extensões para a VM para atualizar os metadados.
5. Por que razão estou obter o erro `500 Internal Server Error`?
   * Repetir o pedido com base no back exponencial fora do sistema. Se o problema persistir, contacte o suporte do Azure.
6. Onde posso partilham perguntas/comentários adicionais?
   * Envie os seus comentários sobre http://feedback.azure.com.
7. Isto seria funcionar para a instância de conjunto de dimensionamento de Máquina Virtual?
   * Sim metadados serviço está disponível para instâncias de conjunto de dimensionamento. 
8. Como posso obter suporte para o serviço?
   * Para obter suporte para o serviço, crie um problema de suporte no portal do Azure para a VM em que não é possível obter a resposta de metadados após várias tentativas de longas 

   ![Suporte de metadados de instância](./media/instance-metadata-service/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [agendada eventos](scheduled-events.md) API **em pré-visualização pública** fornecido pelo serviço de metadados de instância.
