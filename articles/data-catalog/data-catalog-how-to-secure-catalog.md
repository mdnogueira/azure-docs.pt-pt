---
title: "Como proteger o acesso ao catálogo de dados do Azure | Microsoft Docs"
description: "Este artigo explica como proteger o catálogo de dados e os respetivos recursos de dados."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 11/01/2017
ms.author: maroche
ms.openlocfilehash: 0950d453e53a70f96f59de572ac10326ef030444
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Como proteger o acesso ao catálogo de dados e recursos de dados
> [!IMPORTANT]
> Esta funcionalidade está disponível apenas na edição standard do catálogo de dados do Azure.

Catálogo de dados do Azure permite-lhe especificar quem pode aceder ao catálogo de dados e as operações que (registar, anotar, assumir a propriedade) podem executar nos metadados no catálogo. 

## <a name="catalog-users-and-permissions"></a>Os utilizadores do catálogo e permissões
Para conceder um utilizador ou um grupo de acesso a um catálogo de dados e definir permissões:

1. No [home page do catálogo de dados](http://www.azuredatacatalog.com), clique em **definições** na barra de ferramentas.

    ![catálogo de dados - definições](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)
2. Na página de definições, expanda o **os utilizadores do catálogo** secção.
    ![Catálogo utilizadores - adicionar](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)
3. Clique em **Adicionar**.
4. Introduza o completamente qualificado **nome de utilizador** ou nome do **grupo de segurança** no Azure Active Directory (AAD) associados com o catálogo. Utilize a vírgula (', ') como um separador se estiver a adicionar mais do que um utilizador ou grupo.
    ![Utilizadores - os utilizadores ou grupos de catálogo](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)
5. Prima **ENTER** ou **SEPARADOR** fora da caixa de texto. 
6.  Confirme que todas as permissões (**Annotate**, **registar**, e **obter propriedade**) são atribuídas a estes utilizadores ou grupos, por predefinição. Ou seja, o utilizador ou grupo pode [registar recursos de dados]( data-catalog-how-to-register.md), [anotar recursos de dados]( data-catalog-how-to-annotate.md), e [assumir a propriedade dos recursos de dados]( data-catalog-how-to-manage.md). 
    ![Utilizadores do catálogo - permissões predefinidas](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)
7.  Para dar um utilizador ou grupo apenas o acesso de leitura para o catálogo, desmarque a **anotar** opção para esse utilizador ou grupo. Ao fazê-lo, o utilizador ou grupo não é possível anotar recursos de dados no catálogo, mas podem visualizá-los. 
8.  Para negar um utilizador ou grupo de recursos de dados a registar, desmarque a **registar** opção para esse utilizador ou grupo.
9.  Para negar um utilizador da propriedade de um recurso de dados, limpe o **assumir a propriedade** opção para esse utilizador ou grupo. 
10. Para eliminar um utilizador/grupo de utilizadores do catálogo, clique em **x** do grupo de utilizadores/na parte inferior da lista. 
    ![Os utilizadores do catálogo - eliminar utilizador](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

    > [!IMPORTANT]
    > Recomendamos que adicione os grupos de segurança diretamente do catálogo utilizadores em vez de adicionar utilizadores e atribuir as permissões. Em seguida, adicione utilizadores para grupos de segurança que coincidem com as respetivas funções e o acesso necessário para o catálogo.

## <a name="special-considerations"></a>Considerações especiais

- As permissões atribuídas a grupos de segurança são cumulativas. Diga, um utilizador estiver em dois grupos. Um grupo tem anotar permissões e não ter anotar outro grupo de permissões. Em seguida, o utilizador tem anotar permissões. 
- As permissões atribuídas explicitamente para um utilizador substituem as permissões atribuídas a grupos a que o utilizador pertence. No exemplo anterior, diga, explicitamente adicionou o utilizador de utilizadores do catálogo e não atribuir anotar permissões. O utilizador não é possível anotar recursos de dados, apesar do utilizador é membro de um grupo que tenham anotar permissões.

## <a name="next-steps"></a>Passos seguintes
- [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md)

