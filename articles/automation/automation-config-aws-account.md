---
title: "Configurar a Autenticação com os Amazon Web Services | Microsoft Docs"
description: "Este artigo descreve como criar e validar uma credencial AWS para runbooks na Automatização do Azure gerindo recursos AWS."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
keywords: "autenticação aws, configurar aws"
ms.assetid: b6dde4bb-26ac-4876-9aa9-e586bed30d6b
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: 0802bb0f2c4d5b761c5833d7231bd3d2d1002cfc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Autenticar Runbooks com Amazon Web Services
Automatizar tarefas comuns com recursos nos Amazon Web Services (AWS) pode ser efetuado com runbooks de Automatização no Azure.  Pode automatizar muitas tarefas no AWS através de runbooks de Automatização, tal como o pode fazer com recursos no Azure.  Apenas são necessárias duas coisas:

* Uma subscrição do AWS e um conjunto de credenciais.  Mais especificamente, a Chave de Acesso do AWS e a Chave Secreta.  Para obter mais informações, veja o artigo [Using AWS Credentials (Com Credenciais AWS)](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Uma subscrição do Azure e a conta de Automatização.  Para obter mais informações sobre como configurar uma conta de automatização do Azure, veja [Authentication planning (Planeamento de autenticação)](automation-offering-get-started.md#authentication-planning).  

Para efetuar a autenticação com o AWS, tem de especificar um conjunto de credenciais do AWS para autenticar os runbooks a partir da Automatização do Azure. Se já tiver uma conta de Automatização criada e pretende utilizá-la para efetuar a autenticação com o AWS, pode seguir os passos na secção seguinte.  Se pretender dedicar uma conta para runbooks direcionados para recursos do AWS, deve primeiro criar uma nova [Conta de Automatização](automation-offering-get-started.md) (ignore a opção para criar um principal de serviço) e, em seguida, siga os passos abaixo.

## <a name="configure-automation-account"></a>Configurar conta de Automatização
Para a Automatização do Azure comunicar com o AWS, primeiro tem de obter as suas credenciais do AWS e armazená-las como recursos na Automatização do Azure.  Execute os seguintes passos documentados no documento AWS [Gerir Chaves de Acesso para a sua Conta AWS](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) para criar uma Chave de Acesso e copiar o **ID da Chave de Acesso** e **Chave de Acesso Secreta** (opcionalmente, transfira o ficheiro de chave para o armazenar num local seguro).

Depois de ter criado e copiado as chaves de segurança AWS, tem de criar um recurso de Credencial com uma conta de Automatização do Azure para as armazenar em segurança as e referenciar com os seus runbooks.  Siga os passos na secção **Para criar uma nova credencial** nos [Recursos de credencial na Automatização do Azure](automation-credentials.md#to-create-a-new-credential-asset-with-the-azure-portal) e introduza as seguintes informações:

1. Na caixa **Nome**, introduza **AWScred** ou um valor adequado a seguir às normas de nomenclatura.  
2. Na caixa **Nome de utilizador**, escreva o **ID de Acesso** e a **Chave de Acesso Secreta** na caixa **Palavra-passe** e **Confirmar palavra-passe**.   

## <a name="next-steps"></a>Passos seguintes
* Leia o artigo de solução [Automating deployment of a VM in Amazon Web Services (Automatizar a implementação de uma VM nos Amazon Web Services)](automation-scenario-aws-deployment.md) para saber como criar runbooks para automatizar as tarefas no AWS.

