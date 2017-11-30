---
title: "Como criar cópias de segurança e restaurar um servidor na base de dados do Azure para MySQL | Microsoft Docs"
description: "Saiba como criar cópias de segurança e restaurar um servidor na base de dados do Azure para MySQL, utilizando a CLI do Azure."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/28/2017
ms.openlocfilehash: 44b3c68b8df4006d3fe087e5ad4118d7616d3d9a
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mysql-by-using-the-azure-cli"></a>Como criar cópias de segurança e restaurar um servidor na base de dados do Azure para MySQL, utilizando a CLI do Azure

Utilize base de dados do Azure para o MySQL para restaurar uma base de dados do servidor para uma data anterior que abrange 7 para 35 dias.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de procedimentos, tem de:
- Um [base de dados do Azure para o servidor de MySQL e base de dados](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Se instalar e utilizar a CLI do Azure localmente, este guia de procedimentos é necessário utilizar a CLI do Azure versão 2.0 ou posterior. Para confirmar a versão, a linha de comandos da CLI do Azure, introduza `az --version`. Para instalar ou atualizar, consulte o artigo [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="backup-happens-automatically"></a>Cópia de segurança ocorre automaticamente
Quando utiliza a base de dados do Azure para MySQL, o serviço de base de dados efetua automaticamente uma cópia de segurança do serviço de cada 5 minutos. 

Para o escalão básico, estão disponíveis as cópias de segurança durante 7 dias. Para o escalão Standard, as cópias de segurança estão disponíveis para 35 dias. Para obter mais informações, consulte [base de dados do Azure para MySQL escalões de preço](concepts-service-tiers.md).

Com esta funcionalidade de cópia de segurança automática, pode restaurar o servidor e as respetivas bases de dados para uma data anterior ou o ponto no tempo.

## <a name="restore-a-database-to-a-previous-point-in-time-by-using-the-azure-cli"></a>Restaurar uma base de dados para um ponto anterior no tempo utilizando a CLI do Azure
Utilize base de dados do Azure para o MySQL para restaurar o servidor para um ponto anterior no tempo. Os dados restaurados são copiados para um novo servidor e o servidor existente for deixado como está. Por exemplo, se uma tabela acidentalmente for removida, ao meio-dia hoje em dia, pode restaurar a hora imediatamente antes ao meio-dia. Em seguida, pode obter a tabela em falta e os dados da cópia de restaurada do servidor. 

Para restaurar o servidor, utilizar a CLI do Azure [restauro do servidor az mysql](/cli/azure/mysql/server#az_mysql_server_restore) comando.

### <a name="run-the-restore-command"></a>Execute o comando de restauro

Para restaurar o servidor, na linha de comandos da CLI do Azure, introduza o seguinte comando:

```azurecli-interactive
az mysql server restore --resource-group myResourceGroup --name myserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server myserver4demo
```

O `az mysql server restore` comando requer os seguintes parâmetros:
| Definição | Valor sugerido | Descrição  |
| --- | --- | --- |
| grupo de recursos | myResourceGroup |  O grupo de recursos onde o servidor de origem existe.  |
| nome | restaurar o MyServer | O nome do novo servidor que é criado pelo comando restore. |
| restauro ponto no tempo | 2017-04-13T13:59:00Z | Selecione um ponto no tempo para restaurar. Esta data e hora tem de ser dentro do período de retenção de cópias de segurança do servidor de origem. Utilize o formato ISO8601 de data e hora. Por exemplo, pode utilizar o seus próprios fuso horário local, como `2017-04-13T05:59:00-08:00`. Também pode utilizar o formato UTC Zulu, por exemplo, `2017-04-13T13:59:00Z`. |
| servidor de origem | myserver4demo | O nome ou ID do servidor de origem para restaurar a partir de. |

Quando restaurar um servidor para um ponto anterior no tempo, é criado um novo servidor. O servidor original e respetivas bases de dados a partir do ponto no tempo especificado são copiados para o novo servidor.

A localização e o preço de camada de valores para o servidor restaurado permanecerem o mesmo que o servidor original. 

O `az mysql server restore` comando é síncrono. Depois do servidor é restaurado, pode utilizá-lo novamente para repetir o processo para um outro ponto no tempo. 

Depois de concluir o processo de restauro, localize o novo servidor e certifique-se de que a serem restaurados conforme esperado.

## <a name="next-steps"></a>Passos seguintes
[Bibliotecas de ligação para base de dados do Azure para MySQL](concepts-connection-libraries.md)
