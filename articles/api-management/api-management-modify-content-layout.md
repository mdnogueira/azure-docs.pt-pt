---
title: "Modificar conteúdos de páginas no portal do programador na Gestão de API do Azure | Microsoft Docs"
description: "Saiba como editar conteúdos de páginas no portal do programador na Gestão de API do Azure."
services: api-management
documentationcenter: 
author: antonba
manager: vlvinogr
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/09/2017
ms.author: antonba
ms.openlocfilehash: 708c803c36c182ed90e04731b12d4ade00ae7ffb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Modificar o conteúdo e o esquema das páginas no portal do programador na Gestão de API do Azure
Existem três formas fundamentais de personalizar o portal do programador na Gestão de API do Azure:

* [Editar o conteúdo de páginas estáticas e elementos de esquema da página][modify-content-layout] (explicado neste guia)
* [Atualizar os estilos utilizados para elementos de página em todo o portal do programador][customize-styles]
* [Modificar os modelos utilizados para páginas geradas pelo portal][portal-templates] (por exemplo, documentos de API, produtos, autenticação de utilizador, etc.)

## <a name="page-structure"> </a>Estrutura das páginas do portal do programador

O portal do programador baseia-se num sistema de gestão de conteúdos. O esquema de cada página é criado com base num conjunto de pequenos elementos de página conhecidos como widgets:

![Estrutura de páginas do portal do programador][api-management-customization-widget-structure]

Todos os widgets são editáveis. 
* Os conteúdos principais específicos de cada página individual encontram-se no widget "Conteúdos". Editar uma página significa editar o conteúdo deste widget.
* Todos os elementos de esquema de página estão contidos com os restantes widgets. As alterações feitas a estes widgets serão aplicadas a todas as páginas. Estes serão referidos como "widgets de esquema".

Na edição diária de páginas, um indivíduo normalmente modifica apenas o widget Conteúdos, que terá conteúdo diferente para cada página individual.

## <a name="modify-layout-widget"> </a>Modificar o conteúdo de um widget de esquema

O conteúdo do portal do programador é modificado através do portal do publicador, que é acedido a partir do Portal do Azure. Para o alcançar, clique em **Portal do publicador** na barra de ferramentas de serviço da sua instância de Gestão de API.

![Portal do publicador][api-management-management-console]

Para editar o conteúdo desse widget, clique em **Widgets** no menu **Portal do Programador** à esquerda. Para este exemplo, vamos modificar o conteúdo do widget Cabeçalho. Selecione o widget **Cabeçalho** na lista.

![Cabeçalho de widgets][api-management-widgets-header]

O conteúdo do cabeçalho é editável a partir do campo **Corpo**. Altere o texto conforme pretendido e clique em **Guardar** na parte inferior da página.

Agora deve conseguir ver o novo cabeçalho em todas as páginas do portal do programador.

> Para abrir o portal do programador a partir do portal do publicador, clique em **Portal do programador** na barra superior.
> 
> 

## <a name="edit-page-contents"> </a>Editar o conteúdo de uma página

Para ver a lista de todas as páginas de conteúdo existentes, clique em **Conteúdo** no menu **Portal do programador** no portal do publicador.

![Gerir conteúdo][api-management-customization-manage-content]

Clique na página **Bem-vindo** para editar o que é apresentado na home page do portal do programador. Efetue as alterações que pretender, pré-visualize-as se necessário e, em seguida, clique em **Publicar Agora** para que fiquem visíveis para todos os utilizadores.

> A home page utiliza um esquema especial que permite apresentar uma faixa na parte superior. Esta faixa não é editável a partir da secção **Conteúdo**. Para editar esta faixa, clique em **Widgets** no menu **Portal do programador**, selecione **Home page** na lista pendente **Camada Atual** e, em seguida, abra o item **Faixa** na **Secção em destaque**. O conteúdo deste widget é editável, tal como qualquer outra página.
> 
> 

## <a name="next-steps"> </a>Passos seguintes
* [Atualizar os estilos utilizados para elementos de página em todo o portal do programador][customize-styles]
* [Modificar os modelos utilizados para páginas geradas pelo portal][portal-templates] (por exemplo, documentos de API, produtos, autenticação de utilizador, etc.)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png
