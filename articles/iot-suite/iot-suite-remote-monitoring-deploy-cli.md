---
title: "Implementar o Java solução - Azure de monitorização remota | Microsoft Docs"
description: "Este tutorial mostra como aprovisionar a solução pré-configurada monitorização remota microsoervices Java utilizando a CLI."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 10/15/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 18e754697a7f2108b3095313dc47a65029863262
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution-using-the-cli"></a>Implementar a solução pré-configurada monitorização remota utilizando a CLI

Este tutorial mostra-lhe como aprovisionar a solução pré-configurada de monitorização remota. Implementar a solução utilizando a CLI. Também pode implementar a solução utilizando a IU baseada na web em azureiotsuite.com, para saber mais sobre esta opção, consulte [implementar a solução pré-configurada de monitorização remota](iot-suite-remote-monitoring-deploy.md).

## <a name="prerequisites"></a>Pré-requisitos

Para implementar a solução pré-configurada de monitorização remota, terá de uma subscrição do Azure Active Directory.

Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

Para executar o CLI, terá de [Node.js](https://nodejs.org/) instalado no seu computador local.

## <a name="install-the-cli"></a>Instalar a CLI

Para instalar a CLI, execute o seguinte comando no seu ambiente de linha de comandos:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Iniciar sessão para a CLI

Antes de poder implementar a solução pré-configurada, tem de iniciar sessão sua subscrição do Azure utilizando a CLI do seguinte modo:

```cmd/sh
pcs login
```

Siga o ecrã instruções para concluir o processo de início de sessão.

## <a name="deployment-options"></a>Opções de Implementação

Quando implementar a solução pré-configurada, existem várias opções que configuram o processo de implementação:

| Opção | Valores | Descrição |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard` | A _básico_ implementação destina-se de teste e demonstrações, implementa todos os micro-serviços uma única máquina virtual. A _padrão_ implementação destina-se para produção, implementa os micro-serviços várias máquinas virtuais. |
| Tempo de execução | `dotnet`, `java` | Seleciona a implementação de idioma dos micro-serviços. |

## <a name="deploy-the-preconfigured-solution"></a>Implementar a solução pré-configurada

### <a name="example-deploy-net-version"></a>Exemplo: implementar a versão do .NET

O exemplo seguinte mostra como implementar a versão do .NET básica, da solução pré-configurada de monitorização remota:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Exemplo: implementar a versão de Java

O exemplo seguinte mostra como implementar a versão de Java padrão, da solução pré-configurada de monitorização remota:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>Opções de comando de PCs

Quando executa o `pcs` comando para implementar uma solução, é-lhe pedido para:

- Um nome para a sua solução. Este nome tem de ser exclusivo.
- A subscrição do Azure que deve utilizar.
- Uma localização.
- As credenciais para as máquinas virtuais que alojam os micro-serviços. Pode utilizar estas credenciais para aceder as máquinas virtuais para resolução de problemas.

Quando o `pcs` comando estiver concluído, apresenta o URL da sua nova implementação da solução pré-configurada. O `pcs` comando também cria um ficheiro `{deployment-name}-output.json` com informações adicionais, como o nome do IoT Hub que tenha sido aprovisionado para si.

Para obter mais informações sobre os parâmetros da linha de comandos, execute:

```cmd/sh
pcs -h
```

Para obter mais informações sobre a CLI, consulte [como utilizar a CLI](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configurar a solução pré-configurada
> * Implementar a solução pré-configurada
> * Iniciar sessão para a solução pré-configurada

Agora que implementou a solução de monitorização remota, o passo seguinte consiste em [explorar as funcionalidades do dashboard de solução](./iot-suite-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->