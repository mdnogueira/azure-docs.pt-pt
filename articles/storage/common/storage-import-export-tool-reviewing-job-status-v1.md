---
title: Rever o estado da tarefa importar/exportar do Azure - v1 | Microsoft Docs
description: "Saiba como utilizar os ficheiros de registo criados quando a tarefa de importação ou exportação foi executada para ver o estado da tarefa de importação/exportação."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: c69d1d69-6403-4eee-9949-0185faeecfa1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
ms.openlocfilehash: bdb30bc28c36ab9e969efc8be3b87b97e4027b39
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Rever o estado da tarefa de importação/exportação do Azure com os ficheiros de registo de cópia
Quando o serviço de importação/exportação do Microsoft Azure processa unidades associadas uma tarefa de importação ou exportação, escreve copiar ficheiros de registo para a conta de armazenamento para ou a partir do qual está a importar ou exportar blobs. O ficheiro de registo contém estado detalhadas sobre cada ficheiro que foi importado ou exportado. O URL para cada ficheiro de registo de cópia é devolvido quando consultar o estado de uma tarefa concluída; consulte [Get Job](/rest/api/storageservices/Get-Job3) para obter mais informações.  

## <a name="example-urls"></a>URLs de exemplo

Seguem-se os URLs de exemplo para ficheiros de registo de cópia de uma tarefa de importação com duas unidades:  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 Consulte [serviço importar/exportar formato de ficheiro de registo](../storage-import-export-file-format-log.md) para o formato dos registos de cópia e a lista completa dos códigos de estado.  
  
## <a name="next-steps"></a>Passos seguintes
 
 * [Configurar a ferramenta de importação/exportação do Azure](storage-import-export-tool-setup-v1.md)   
 * [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Reparação de uma tarefa de importação](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Reparação de uma tarefa de exportação](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Resolver problemas da Ferramenta de Importação /Exportação do Azure (Troubleshooting the Azure Import/Export Tool)](storage-import-export-tool-troubleshooting-v1.md)
