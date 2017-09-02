---
title: "Introdução à CLI do Azure Service Fabric (sfctl)"
description: "Saiba como utilizar a CLI do Azure Service Fabric. Saiba como ligar a um cluster e como gerir aplicações."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 5ce9adf6c82e3a5521883c5de1e0689d5bf0d94e
ms.contentlocale: pt-pt
ms.lasthandoff: 08/24/2017

---
# <a name="azure-service-fabric-command-line"></a>Linha de comandos do Azure Service Fabric

A CLI do Azure Service Fabric (sfctl) é um utilitário de linha de comandos para interagir e gerir entidades do Azure Service Fabric. O Sfctl pode ser utilizado com clusters do Windows ou Linux. O Sfctl é executado em qualquer plataforma onde o python seja suportado.

## <a name="prerequisites"></a>Pré-requisitos

Antes da instalação, certifique-se de que o seu ambiente tem o python e pip instalados. Para obter mais informações, veja a [documentação de início rápido do pip](https://pip.pypa.io/en/latest/quickstart/)e a [documentação para instalar o python](https://wiki.python.org/moin/BeginnersGuide/Download) oficial.

Embora o python 2.7 e 3.6 sejam suportados, recomenda-se a utilização do python 3.6.

## <a name="install"></a>Instalar

A CLI do Azure Service Fabric (sfctl) é empacotada como um pacote do python. Para instalar o ficheiro de execução da versão mais recente:

```bash
pip install sfctl
```

Após a instalação, execute `sfctl -h` para obter informações sobre os comandos disponíveis.

## <a name="cli-syntax"></a>Sintaxe da CLI

Aos comandos é sempre adicionado o prefixo `sfctl`. Para obter informações gerais sobre todos os comandos que pode utilizar, utilize `sfctl -h`. Para obter ajuda com comandos individuais, utilize `sfctl <command> -h`.

Os comandos seguem uma estrutura repetível, com o destino do comando antes do verbo ou da ação:

```azurecli
sfctl <object> <action>
```

Neste exemplo, `<object>` é o destino de `<action>`.

## <a name="select-a-cluster"></a>Selecionar um cluster

Antes de executar qualquer operação, tem de selecionar um cluster ao qual se deve ligar. Por exemplo, execute o seguinte para selecionar e ligar ao cluster com o nome `testcluster.com`.

> [!WARNING]
> Não utilize clusters do Service Fabric não seguros em ambientes de produção.

```azurecli
sfctl cluster select --endpoint http://testcluster.com:19080
```

O ponto final do cluster tem de ter o prefixo `http` ou `https`. Tem de incluir a porta para o gateway HTTP. A porta e o endereço são os mesmos que o URL do Service Fabric Explorer.

Para os clusters que estão protegidos por um certificado, pode especificar um certificado PEM codificado. O certificado pode ser especificado como um único ficheiro ou com um certificado e par de chaves.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Para obter mais informações, veja [Connect to a secure Azure Service Fabric cluster](service-fabric-connect-to-secure-cluster.md) (Ligar a um cluster do Azure Service Fabric seguro).

## <a name="basic-operations"></a>Operações básicas

As informações de ligação do cluster estão presentes em diferentes sessões de sfctl. Depois de selecionar um cluster do Service Fabric, pode executar qualquer comando do Service Fabric no mesmo.

Por exemplo, para obter o estado de funcionamento do cluster do Service Fabric, utilize o comando seguinte:

```azurecli
sfctl cluster health
```

O comando resulta na seguinte saída:

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

Algumas sugestões e dicas para resolver problemas comuns.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Converter um certificado no formato PFX em PEM

A CLI do Service Fabric suporta ficheiros de certificados de cliente como PEM (extensão .pem). Se utilizar ficheiros PFX do Windows, tem de converter esses certificados no formato PEM. Para converter um ficheiro PFX para ficheiro PEM, utilize o comando seguinte:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Para obter mais informações, veja a [documentação de OpenSSL](https://www.openssl.org/docs/).

### <a name="connection-issues"></a>Problemas de ligação

Algumas operações poderão gerar a mensagem seguinte:

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Verifique se o ponto final do cluster especificado está disponível e à escuta. Confirme, também, se a IU do Service Fabric Explorer está disponível no anfitrião e na porta. Para atualizar o ponto final, utilize `sfctl cluster select`.

### <a name="detailed-logs"></a>Registos detalhados

Muitas vezes, os registos detalhados são úteis para depurar ou comunicar problemas. Existe um sinalizador `--debug` global que aumenta a verbosidade dos ficheiros de registo.

### <a name="command-help-and-syntax"></a>Ajuda de comandos e sintaxe

Para obter ajuda com um comando específico ou um grupo de comandos, utilize o sinalizador `-h`:

```azurecli
sfctl application -h
```

Outro exemplo:

```azurecli
sfctl application create -h
```

## <a name="next-steps"></a>Passos seguintes

* [Implementar uma aplicação com a CLI do Azure Service Fabric](service-fabric-application-lifecycle-sfctl.md)
* [Introdução ao Service Fabric no Linux](service-fabric-get-started-linux.md)

