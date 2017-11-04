---
title: "Resolução de problemas da ferramenta de importação/exportação do Azure | Microsoft Docs"
description: "Saiba mais sobre alguns dos problemas comuns vistos ao utilizar a ferramenta de importação/exportação do Azure e como lidar com os mesmos."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: b91ca5eb-c557-460a-9afc-0590b38471f9
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 7bfda602dbc0ea47828a7c9243b8b9b09ec78432
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Resolver problemas da Ferramenta de Importação /Exportação do Azure
A ferramenta de importação/exportação do Microsoft Azure devolve mensagens de erro se ficar para problemas. Este tópico apresenta alguns problemas comuns que os utilizadores podem executar no.  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>Uma sessão de cópia falha, o que devo fazer?  
 Quando uma sessão de cópia falha, existem duas opções:  
  
 Se o erro não permanente, por exemplo, se a partilha de rede estiver offline durante um período pequeno e está agora novamente online, pode retomar a sessão de cópia. Se o erro de não repetição, por exemplo, se especificou o diretório de ficheiros de origem errado nos parâmetros de linha de comandos, terá de abortar a sessão de cópia. Consulte [preparar os discos rígidos de uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md) para obter mais informações sobre a retomar e abortar sessões de cópia.  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>Não consigo retomar ou abortar uma sessão de cópia.  
 Se a sessão de cópia é a primeira sessão de cópia para uma unidade, em seguida, deve especificar a mensagem de erro: "a primeira sessão de cópia não pode ser retomada ou abortada." Neste caso, pode eliminar o ficheiro de diário antiga e volte a executar o comando.  
  
 Se uma sessão de cópia não é o primeiro para uma unidade, pode ser retomado sempre ou abortada.  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Posso perdeu o ficheiro do diário de alterações, pode ainda criar a tarefa?  
 O ficheiro de diário de alterações para uma unidade contém as informações de conclua de cópia de dados a esta unidade e é necessário para adicionar mais ficheiros para a unidade e será utilizado para criar uma tarefa de importação. Se o ficheiro do diário de alterações se tenha perdido, terá de Refazer todas as sessões de cópia para a unidade.  
  
## <a name="next-steps"></a>Passos seguintes
 
* [Configurar a ferramenta de importação/exportação do azure](../storage-import-export-tool-setup-v1.md)   
* [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Revisão do estado da tarefa com ficheiros de registo de cópia](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparação de uma tarefa de importação](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Reparação de uma tarefa de exportação](../storage-import-export-tool-repairing-an-export-job-v1.md)
