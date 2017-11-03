---
title: "Configurar um anfitrião do Docker com VirtualBox | Microsoft Docs"
description: "Instruções passo a passo para configurar uma instância de Docker predefinida, utilizando o Docker máquina e VirtualBox"
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 0b1335a2-7720-42a8-8260-4e06fc00c9f6
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: e9465afb560a73d74f853c19094b3ee75b8c470c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-docker-host-with-virtualbox"></a>Configurar um anfitrião do Docker com VirtualBox
## <a name="overview"></a>Descrição geral
Este artigo orienta-o a configurar uma instância de Docker predefinida, utilizando o Docker máquina e VirtualBox. Se estiver a utilizar o [beta do Docker para Windows](http://beta.docker.com/), esta configuração não é necessária.

## <a name="prerequisites"></a>Pré-requisitos
As ferramentas seguintes têm de ser instalados.

* [Caixa de ferramentas do docker](https://www.docker.com/products/overview#/docker_toolbox)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>Configurar o cliente de Docker com o Windows PowerShell
Para configurar um cliente de Docker, basta abrir o Windows PowerShell e execute os seguintes passos:

1. Crie uma instância de anfitrião do docker predefinida.
   
    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
2. Certifique-se de que a instância predefinida está configurada e em execução. (Neste caso, deverá ver uma instância com o nome 'default' em execução.
   
    ```PowerShell
    docker-machine ls 
    ```
   
    ![saída de docker máquina ls][0]
3. Predefinir como anfitrião atual e configurar o shell.
   
    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```
4. Apresente os contentores de Docker Active Directory. A lista deve estar vazia.
   
    ```PowerShell
    docker ps
    ```
   
    ![saída de ps docker][1]

> [!NOTE]
> Sempre que reiniciar o computador de desenvolvimento, terá de reiniciar o seu anfitrião local docker.
> Para fazê-lo, emita o comando seguinte numa linha de comandos: `docker-machine start default`.
> 
> 

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png
