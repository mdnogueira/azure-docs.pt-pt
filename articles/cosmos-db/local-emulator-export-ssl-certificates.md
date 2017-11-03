---
title: Exportar os certificados de emulador de BD do Cosmos do Azure | Microsoft Docs
description: "Quando desenvolver em idiomas e tempos de execução que não utilizem o arquivo de certificados do Windows, terá de exportar e gerir os certificados SSL. Esta mensagem fornece instruções passo a passo."
services: cosmos-db
documentationcenter: 
keywords: Emulador do Cosmos BD do Azure
author: voellm
manager: jhubbard
editor: 
ms.assetid: ef43deda-c2e9-4193-99e2-7f6a88a0319f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2017
ms.author: tvoellm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bafbd43d65a0f46f87a13cea4a2a6299d3bf43ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Exportar os certificados de emulador de BD do Cosmos do Azure para utilização com o Java, Python e Node.js

[**Transferir o emulador**](https://aka.ms/cosmosdb-emulator)

O emulador de BD do Cosmos do Azure fornece um ambiente local que emula o serviço de base de dados do Azure Cosmos para fins de desenvolvimento, incluindo a sua utilização de ligações de SSL. Esta mensagem demonstra como exportar os certificados SSL para utilização em idiomas e tempos de execução que se integra com o arquivo de certificados do Windows, tais como o Java que utiliza o seu próprio [arquivo de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) e Python que utiliza [socket wrappers](https://docs.python.org/2/library/ssl.html) e Node.js que utiliza [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Pode ler mais sobre o emulador na [utilizar o emulador de BD do Cosmos do Azure para desenvolvimento e testes](./local-emulator.md).

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Rotação dos certificados
> * Exportar o certificado SSL
> * Aprender a utilizar o certificado em Java, Python e Node.js

## <a name="certification-rotation"></a>Rotação de certificação

Certificados no emulador Local do Azure Cosmos DB são gerados pela primeira vez que o emulador é executado. Existem dois certificados. Um utilizadas para ligar para o emulador local e outra para a gestão de segredos no emulador. O certificado que pretende exportar é o certificado de ligação com o nome amigável "DocumentDBEmulatorCertificate".

Ambos os certificados podem ser novamente gerados clicando **Repor dados** conforme mostrado abaixo do emulador de BD do Cosmos do Azure em execução no tabuleiro do Windows. Se voltar a gerar os certificados e tiver instalado-los para o arquivo de certificados de Java ou mesmos noutro local, terá de atualizá-las, caso contrário, a aplicação já não se ligará para o emulador local.

![Dados de reposição de emulador local do Cosmos BD do Azure](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-ssl-certificate"></a>Como exportar o certificado SSL de BD do Cosmos do Azure

1. Iniciar o Gestor de certificados do Windows em execução certlm.msc e navegue para a pasta de certificados pessoais-> e abra o certificado com o nome amigável **DocumentDbEmulatorCertificate**.

    ![Passo 1 de exportação do emulador local do Cosmos BD do Azure](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. Clique em **detalhes** , em seguida, **OK**.

    ![Passo 2 de exportação do emulador local do Cosmos BD do Azure](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. Clique em **copiar para ficheiro...** .

    ![Passo 3 de exportação do emulador local do Cosmos BD do Azure](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. Clique em **Seguinte**.

    ![Passo 4 de exportação do emulador local do Cosmos BD do Azure](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png)

5. Clique em **não exportar chave privada**, em seguida, clique em **seguinte**.

    ![Passo 5 de exportação do emulador local do Cosmos BD do Azure](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png)

6. Clique em **x. 509 com codificação Base 64 (. CER)** e, em seguida, **seguinte**.

    ![Passo 6 de exportação do emulador local do Cosmos BD do Azure](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png)

7. Dê um nome de certificado. Neste caso **documentdbemulatorcert** e, em seguida, clique em **seguinte**.

    ![Passo 7 de exportação do emulador local do Cosmos BD do Azure](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png)

8. Clique em **Concluir**.

    ![Passo 8 de exportação do emulador local do Cosmos BD do Azure](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Como utilizar o certificado em Java

Quando executar aplicações de Java ou MongoDB aplicações que utilizam o cliente de Java é mais fácil instalar o certificado para o arquivo de certificados predefinido de Java que passou o "-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword = "<password>" sinalizadores. Por exemplo o incluídos [aplicação de demonstração de Java](https://localhost:8081/_explorer/index.html) depende do arquivo de certificados predefinido.

Siga as instruções no [a adição de um certificado para o arquivo de certificados de AC do Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store) para importar o certificado x. 509 para o arquivo de certificados de Java predefinido. Tenha em mind, irá funcionar no diretório % JAVA_HOME % quando em execução keytool.

Uma vez a "CosmosDBEmulatorCertificate" SSL certificado é instalado a aplicação deve ser capaz de ligar e utilizar o emulador de base de dados de Cosmos local do Azure. Se continuar a ter problemas em poderá ser útil seguir o [depuração ligações de SSL/TLS](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) artigo. É muito provável que o certificado não está instalado para o arquivo de %JAVA_HOME%/jre/lib/security/cacerts. Para o exemplo, se tiver várias versões instaladas do Java a aplicação poderá estar a utilizar um arquivo de cacerts diferentes que um atualizar.

## <a name="how-to-use-the-certificate-in-python"></a>Como utilizar o certificado no Python

Por predefinição o [Python SDK(version 2.0.0 or higher)](documentdb-sdk-python.md) para a API do DocumentDB não tente e utilizar o certificado SSL ao estabelecer ligação com o emulador local. Se, no entanto, o que pretende utilizar validação de SSL pode seguir os exemplos de [Python socket wrappers](https://docs.python.org/2/library/ssl.html) documentação.

## <a name="how-to-use-the-certificate-in-nodejs"></a>Como utilizar o certificado no Node.js

Por predefinição o [Node.js SDK(version 1.10.1 or higher)](documentdb-sdk-node.md) para a API do DocumentDB não tente e utilizar o certificado SSL ao estabelecer ligação com o emulador local. Se, no entanto, o que pretende utilizar validação de SSL pode seguir os exemplos de [Node.js documentação](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Rotação de certificados
> * Exportar o certificado SSL
> * Aprendeu a utilizar o certificado em Java, Python e Node.js

Agora pode avançar para criar um acionador de HTTP de funções do Azure com tutorial de enlace de entrada uma base de dados do Azure Cosmos.

> [!div class="nextstepaction"]
> [Criar uma função do Azure com informações da base de dados do Azure Cosmos](tutorial-functions-http-trigger.md) 
