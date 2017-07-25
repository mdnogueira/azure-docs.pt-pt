---
title: "Introdução ao Azure Service Fabric e à CLI 2.0 do Azure"
description: "Saiba como utilizar o módulo de comandos do Azure Service Fabric na CLI do Azure, versão 2.0. Saiba como ligar a um cluster e como gerir aplicações."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: ee3302b984ca2f5509755dc17b0a5fd06ace0afe
ms.contentlocale: pt-pt
ms.lasthandoff: 07/14/2017

---
# <a name="azure-service-fabric-and-azure-cli-20"></a>Azure Service Fabric e CLI 2.0 do Azure

A versão 2.0 da ferramenta de linha de comandos do Azure (CLI do Azure) inclui comandos que o ajudam a gerir os clusters do Azure Service Fabric. Aprenda a utilizar a CLI do Azure e o Service Fabric.

## <a name="install-azure-cli-20"></a>Instalar Azure CLI 2.0

Pode utilizar os comandos da CLI 2.0 do Azure para interagir e gerir os clusters do Service Fabric. Para obter a versão mais recente da CLI do Azure, siga o [processo de instalação padrão da CLI 2.0 do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

Para obter mais informações, veja [Azure CLI 2.0 overview](https://docs.microsoft.com/en-us/cli/azure/overview) (Descrição geral da CLI 2.0 do Azure).

## <a name="azure-cli-syntax"></a>Sintaxe da CLI do Azure

Na CLI do Azure, todos os comandos do Service Fabric têm o prefixo `az sf`. Para obter informações gerais sobre os comandos que pode utilizar, utilize `az sf -h`. Para obter ajuda com comandos individuais, utilize `az sf <command> -h`.

Os comandos do Service Fabric na CLI do Azure seguem este padrão de nomenclatura:

```azurecli
az sf <object> <action>
```

`<object>` é o destino de `<action>`.

## <a name="select-a-cluster"></a>Selecionar um cluster

Antes de executar qualquer operação, tem de selecionar um cluster ao qual se deve ligar. Para obter um exemplo, veja o código abaixo. O código liga-se a um cluster não seguro.

> [!WARNING]
> Não utilize clusters do Service Fabric não seguros em ambientes de produção.

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

O ponto final do cluster tem de ter o prefixo `http` ou `https`. Tem de incluir a porta para o gateway HTTP. A porta e o endereço são os mesmos que o URL do Service Fabric Explorer.

Relativamente aos clusters que são protegidos com certificado, pode utilizar ficheiros .pem não encriptados ou ficheiros .crt e .key. Por exemplo:

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Para obter mais informações, veja [Connect to a secure Azure Service Fabric cluster](service-fabric-connect-to-secure-cluster.md) (Ligar a um cluster do Azure Service Fabric seguro).

> [!NOTE]
> O comando `select` não age sobre nenhum pedido antes de devolver. Para confirmar que especificou um cluster corretamente, utilize um comando como `az sf cluster health`. Certifique-se de que o comando não devolve erros.

## <a name="basic-operations"></a>Operações básicas

As informações de ligação do cluster estão presentes em diferentes sessões da CLI do Azure. Depois de selecionar um cluster do Service Fabric, pode executar qualquer comando do Service Fabric no mesmo.

Por exemplo, para obter o estado de funcionamento do cluster do Service Fabric, utilize o comando seguinte:

```azurecli
az sf cluster health
```

O comando resulta na seguinte saída (pressupondo que a saída JSON é especificada na configuração da CLI do Azure):

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

## <a name="tips-and-troubleshooting"></a>Sugestões e resolução de problemas

Se se deparar com problemas ao utilizar os comandos do Service Fabric na CLI do Azure, as informações seguintes poderão ser úteis.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Converter um certificado no formato PFX em PEM

A CLI do Azure suporta ficheiros de certificados de cliente como PEM (extensão .pem). Se utilizar ficheiros PFX do Windows, tem de converter esses certificados no formato PEM. Para converter um ficheiro PFX para ficheiro PEM, utilize o comando seguinte:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Para obter mais informações, veja a [documentação de OpenSSL](https://www.openssl.org/docs/).

### <a name="connection-issues"></a>Problemas de ligação

Algumas operações poderão gerar a mensagem seguinte:

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Verifique se o ponto final do cluster especificado está disponível e à escuta. Confirme, também, se a IU do Service Fabric Explorer está disponível no anfitrião e na porta. Para atualizar o ponto final, utilize `az sf cluster select`.

### <a name="detailed-logs"></a>Registos detalhados

Muitas vezes, os registos detalhados são úteis para depurar ou comunicar problemas. A CLI do Azure oferece um sinalizador `--debug` global que aumenta a verbosidade dos ficheiros de registo.

### <a name="command-help-and-syntax"></a>Ajuda de comandos e sintaxe

Os comandos do Service Fabric seguem as mesmas convenções que a CLI do Azure. Para obter ajuda com um comando específico ou um grupo de comandos, utilize o sinalizador `-h`:

```azurecli
az sf application -h
```

Segue-se outro exemplo:

```azurecli
az sf application create -h
```

