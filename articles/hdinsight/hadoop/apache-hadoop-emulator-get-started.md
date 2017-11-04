---
title: "Saiba através de uma sandbox Hadoop - emulador - Azure HDInsight | Microsoft Docs"
description: "Para iniciar a aprendizagem sobre a utilização do ecossistema do Hadoop, pode configurar um sandbox de Hadoop do Hortonworks numa máquina virtual do Azure. "
keywords: emulador do hadoop, hadoop sandbox
editor: cgronlun
manager: jhubbard
services: hdinsight
author: nitinme
documentationcenter: 
tags: azure-portal
ms.assetid: 6ad5bb58-8215-4e3d-a07f-07fcd8839cc6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 77973ac4224e439377f011a77106534b325b97f9
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="get-started-with-a-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Começar com uma sandbox de Hadoop, um emulador numa máquina virtual

Saiba como instalar o sandbox de Hadoop do Hortonworks numa máquina virtual para saber mais sobre a ecossistema do Hadoop. A sandbox disponibiliza um ambiente de desenvolvimento local para saber mais sobre Hadoop, distribuída ficheiro sistema Hadoop (HDFS) e a submissão da tarefa. Depois de se familiarizar com o Hadoop, pode começar a utilizar o Hadoop no Azure através da criação de um cluster do HDInsight. Para obter mais informações sobre como começar a utilizar, consulte [começar com o Hadoop no HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Pré-requisitos
* [Oracle VirtualBox](https://www.virtualbox.org/). Transfira e instale-o de [aqui](https://www.virtualbox.org/wiki/Downloads).



## <a name="download-and-install-the-virtual-machine"></a>Transfira e instale a máquina virtual
1. Navegue para o [Hortonworks transfere](http://hortonworks.com/downloads/#sandbox).

2. Clique em **transferir para VIRTUALBOX** para transferir os mais recentes Hortonworks Sandbox numa VM. É solicitado que registe com Hortonworks antes de começa a transferência. Demora uma ou duas horas para transferir, consoante a velocidade de rede.
   
    ![Imagem de ligação para transferir Hortonworks Sandbox para VirtualBox](./media/apache-hadoop-emulator-get-started/download-sandbox.png)
3. Da mesma página web, clique em de **importar na caixa Virtual** hiperligação para transferir um PDF que contém as instruções de instalação para a máquina virtual.

Para transferir um sandbox de versão mais antiga do HDP, expanda o arquivo:

![Arquivo de Hortonworks Sandbox](./media/apache-hadoop-emulator-get-started/hortonworks-sandbox-archive.png)


## <a name="start-the-virtual-machine"></a>Iniciar a máquina virtual

1. Abra VirtualBox VM Oracle.
2. Do **ficheiro** menu, clique em **importar aplicação**e, em seguida, especifique a imagem do Hortonworks Sandbox.
1. Selecione o Hortonworks Sandbox, clique em **iniciar**e, em seguida, **Normal iniciar**. Depois da máquina virtual tem de terminar o processo de arranque, apresenta instruções de início de sessão.
   
    ![Início normal](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. Abra um browser e navegue para o URL apresentado (normalmente http://127.0.0.1:8888).

## <a name="set-sandbox-passwords"></a>Definir palavras-passe de Sandbox

1. Do **começar** passo da página Hortonworks Sandbox, selecione **opções avançadas de vista**. Utilize as informações nesta página para iniciar sessão para a sandbox utilizando SSH. Utilize o nome e a palavra-passe fornecida.
   
   > [!NOTE]
   > Se não tiver um cliente SSH instalado, pode utilizar o SSH baseado na web fornecido pela máquina virtual em **http://localhost:4200 /**.
   > 
   
    Na primeira vez que o se ligar através do SSH, lhe for pedido para alterar a palavra-passe para a conta raiz. Introduza uma nova palavra-passe, que utilizar quando iniciar sessão utilizando SSH.

2. Depois de a sessão, introduza o seguinte comando:
   
        ambari-admin-password-reset
   
    Quando solicitado, forneça uma palavra-passe para a conta de administrador do Ambari. Isto é utilizado ao aceder a IU da Web do Ambari.

## <a name="use-hive-commands"></a>Utilizar comandos de ramo de registo

1. A partir de uma ligação SSH ao sandbox, utilize o seguinte comando para iniciar a shell do Hive:
   
        hive
2. Depois de ter iniciado o shell, utilize o seguinte para ver as tabelas que são fornecidas com o sandbox:
   
        show tables;
3. Utilize o seguinte para obter 10 linhas do `sample_07` tabela:
   
        select * from sample_07 limit 10;

## <a name="next-steps"></a>Passos seguintes
* [Saiba como utilizar o Visual Studio com a Hortonworks Sandbox](../hdinsight-hadoop-emulator-visual-studio.md)
* [Learning ropes de Hortonworks Sandbox](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Tutorial do Hadoop - introdução HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

