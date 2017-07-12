---
title: "Introdução ao Service Fabric e à CLI do Azure 2.0"
description: "Como utilizar o módulo de comandos do Service Fabric na CLI do Azure versão 2.0 – inclui ligar-se ao cluster e gerir as aplicações"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: c5cc6e54acf27456185eeb48858c4d981aa46b4b
ms.contentlocale: pt-pt
ms.lasthandoff: 07/01/2017

---
<a id="service-fabric-and-azure-cli-20" class="xliff"></a>

# Service Fabric e CLI do Azure 2.0

A nova CLI do Azure 2.0 inclui agora os comandos para gerir os clusters do Service Fabric. Esta documentação inclui os passos para iniciar a CLI do Azure.

<a id="install-azure-cli-20" class="xliff"></a>

## Instalar Azure CLI 2.0

A CLI do Azure inclui agora os comandos para interagir e gerir os clusters do Service Fabric. Pode efetuar o [processo de instalação padrão](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) para obter a CLI do Azure mais recente.

Para obter mais informações, consulte a [documentação da CLI do Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/overview)

<a id="cli-syntax" class="xliff"></a>

## Sintaxe da CLI

Todos os comandos do Azure Service Fabric possuem um prefixo com `az sf` na CLI do Azure. Para obter mais informações sobre os comandos disponíveis, pode executar `az sf -h` para obter informações gerais. Ou pode executar `az sf <command> -h` para obter ajuda mais detalhada sobre um único comando.

Os comandos do Azure Service Fabric na CLI seguem um padrão de nomenclatura

```azurecli
az sf <object> <action>
```

Aqui, `<object>` é o destino de `<action>`.

<a id="selecting-a-cluster" class="xliff"></a>

## Selecionar um cluster

Antes de executar qualquer operação, tem de selecionar um cluster ao qual deve estabelecer ligação. Por exemplo, consulte o seguinte fragmento de código para se ligar a um cluster não seguro.

> [!WARNING]
> Não utilizee clusters do Service Fabric não seguros para ambientes de produção

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

O ponto final do cluster deve ser antecedido por `http` ou `https`e deve incluir a porta para o gateway HTTP. Essa porta e o endereço são os mesmos que o URL do Service Fabric Explorer.

Para proteger os clusters com um certificado, são suportados os ficheiros `pem` ou `crt` e `key` não encriptados.

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Para obter mais informações, consulte o [documento detalhado sobre como se ligar a clusters seguros](service-fabric-connect-to-secure-cluster.md).

> [!NOTE]
> O comando select não executa nenhuma solicitação antes de retornar. Para verificar se um cluster foi especificado corretamente, execute um comando como `az sf cluster health` e verifique se o comando retorna sem erros.

<a id="performing-basic-operations" class="xliff"></a>

## Executar operações básicas

As informações de ligação ao cluster estão presentes em diferentes sessões da CLI do Azure. Quando um cluster do Service Fabric for selecionado, pode executar qualquer comando do Service Fabric.

Por exemplo, para obter o estado de funcionamento do cluster do Service Fabric, execute o seguinte comando

```azurecli
az sf cluster health
```

O comando resulta na seguinte saída, pressupondo que a saída JSON é especificada na configuração da CLI do Azure

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

<a id="tips-and-faq" class="xliff"></a>

## Sugestões e perguntas frequentes

Eis algumas informações que podem ser úteis para a resolução de problemas na utilização dos comandos do Service Fabric na CLI do Azure

<a id="converting-a-certificate-from-pfx-to-pem" class="xliff"></a>

### Converter um certificado de PFX em PEM

A CLI do Azure suporta ficheiros de certificados de cliente como PEM (extensão `.pem`). Se utilizar ficheiros PFX do Windows, esses certificados têm de ser convertidos no formato PEM. Para a conversão de um ficheiro PFX num ficheiro PEM, utilize o seguinte comando:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Consulte a [documentação OpenSSL](https://www.openssl.org/docs/man1.0.1/apps/pkcs12.html) para obter mais detalhes.

<a id="connection-issues" class="xliff"></a>

### Problemas de ligação

Ao executar as operações, pode encontrar o seguinte erro:

> `Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Neste caso, verifique se o ponto final do cluster especificado está acessível e a escutar. Verifique também se a interface do utilizador do Service Fabric Explorer está acessível no sistema anfitrião e na porta. Utilize `az sf cluster select` para atualizar o ponto final.

<a id="getting-detailed-logs" class="xliff"></a>

### Obter registos detalhados

Quando depurar ou comunicar um problema, é útil incluir registos detalhados. A CLI do Azure inclui um sinalizador `--debug` global que aumenta a verbosidade dos registos.

<a id="command-help-and-syntax" class="xliff"></a>

### Ajuda de comandos e sintaxe

Os comandos do Service Fabric seguem a mesma convenção da CLI do Azure. Especifique o sinalizador `-h` para obter ajuda sobre um comando específico ou um grupo de comandos. Por exemplo:

```azurecli
az sf application -h
```

ou

```azurecli
az sf application create -h
```

