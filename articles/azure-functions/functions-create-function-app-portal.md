---
title: "Criar uma aplicação de função do Portal do Azure | Microsoft Docs"
description: "Crie uma nova aplicação de função no App Service do Azure a partir do portal."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/11/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 4f9882ebc86b8119e6e683c7442ee3448a8d111b
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Criar uma aplicação de função a partir do portal do Azure

As aplicações de função do Azure utiliza a infraestrutura de App Service do Azure. Este tópico mostra como criar uma aplicação de função no portal do Azure. Uma aplicação de função é o contentor que aloja a execução das funções individuais. Quando cria uma aplicação de função no plano de alojamento do serviço de aplicações, a sua aplicação de função pode utilizar todas as funcionalidades do App Service.

## <a name="create-a-function-app"></a>Criar uma aplicação de função

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Quando criar uma aplicação de função, forneça um **nome da aplicação**, que pode conter apenas letras, números e hífenes. O caráter de sublinhado (**_**) não é um caráter permitido.

Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. O nome da sua conta do Storage tem de ser exclusivo no Azure. 

Depois da aplicação de função é criada, pode criar funções individuais num ou mais idiomas diferentes. Criar funções [através do portal](functions-create-first-azure-function.md#create-function), [a implementação contínua](functions-continuous-deployment.md), ou por [carregar com FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Planos de serviço

As funções do Azure tem dois planos de serviço diferente: plano de consumo e o plano de serviço de aplicações. O plano de consumo atribui automaticamente a capacidade de computação quando o código está em execução, escalamento escalas conforme necessário para processar carga e, em seguida, escalas-ao código não está em execução. Plano do App Service permite o acesso de aplicação de função para todas as instalações do App Service. Tem de escolher o plano de serviço quando é criada a sua aplicação de função e atualmente não pode ser alterada. Para obter mais informações, consulte [escolha das funções do Azure plano de alojamento](functions-scale.md).

Se estiver a planear executar funções JavaScript um plano de serviço de aplicações, deve escolher um plano com menos núcleos. Para obter mais informações, consulte o [referência de JavaScript para funções](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Requisitos de conta de armazenamento

Quando criar uma aplicação de função no serviço de aplicações, tem de criar ou associar a uma conta de armazenamento do Azure para fins gerais que suporta o armazenamento de tabela, fila e Blob. Internamente, funções utiliza o armazenamento para operações como a gestão de acionadores e execuções de função de registo. Algumas contas de armazenamento não suportam as filas e tabelas, tais como contas de armazenamento apenas de BLOBs, Premium Storage do Azure e contas de armazenamento para fins gerais com replicação ZRS. Estas contas são filtradas do painel de conta de armazenamento ao criar uma aplicação de função.

>[!NOTE]
>Ao utilizar o consumo de plano de alojamento, os ficheiros de configuração de código e o enlace de função são armazenados no File storage do Azure na conta de armazenamento principal. Ao eliminar a conta de armazenamento principal, este conteúdo é eliminado e não pode ser recuperado.

Para saber mais sobre os tipos de conta de armazenamento, consulte o artigo [introduzir os serviços de armazenamento do Azure](../storage/common/storage-introduction.md#introducing-the-azure-storage-services). 

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]



