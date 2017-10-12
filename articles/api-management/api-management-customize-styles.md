---
title: "Personalizar estilos no portal do programador na Gestão de API do Azure | Microsoft Docs"
description: "Saiba como modificar os estilos utilizados para qualquer página do portal do programador na Gestão de API do Azure."
services: api-management
documentationcenter: 
author: vladvino
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
ms.openlocfilehash: 89baf60d0204a1701e93309f09b90bc94c4ca57b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="customize-the-styling-of-the-developer-portal-in-azure-api-management"></a>Personalizar os estilos do portal do programador na Gestão de API do Azure
Existem três formas fundamentais de personalizar o portal do programador na Gestão de API do Azure:

* [Editar o conteúdo de páginas estáticas e elementos de esquema da página][modify-content-layout]
* [Atualizar os estilos utilizados para elementos de página em todo o portal de programador][customize-styles] (explicado neste guia)
* [Modificar os modelos utilizados para páginas geradas pelo portal][portal-templates] (por exemplo, documentos de API, produtos, autenticação de utilizador, etc.)

## <a name="change-headers-styling"> </a>Alterar os estilos de elementos da página

As cores, tipos de letra, tamanhos, espaços e outros elementos relacionados com o estilo de qualquer página no portal são definidos por regras de estilo. 

A edição das regras de estilo é feita a partir do **Portal do programador** enquanto tem sessão iniciada como administrador. Para aceder a este portal, abra o Portal do Azure e clique em **Portal do publicador** a partir da barra de ferramentas de serviço da instância da Gestão de API.

![Portal do publicador][api-management-management-console]

Em seguida, clique em **Portal do programador** no canto superior direito. 

![Hiperligação do portal do programador no portal do publicador][api-management-pp-dp-link]

Para abrir a barra de ferramentas de personalização, mova o rato sobre o ícone de personalização (ou selecione-o) e, em seguida, clique em "estilos" na barra de ferramentas.

![Botão da barra de ferramentas de personalização][api-management-customization-toolbar-button]

Existem duas formas principais de editar regras de estilo - pode consultar a lista de todas as regras de estilo utilizadas em qualquer local que é apresentada por predefinição e modificar um estilo, conforme necessário, ou pode escolher **Selecionar um elemento na página** e, em seguida, clicar em qualquer parte da página para ver apenas os estilos desse elemento.

![Barra de ferramentas de personalização][api-management-customization-toolbar]

Clique na opção **Selecionar um elemento na página** para este exemplo.  Os elementos ficam agora realçados à medida que paira o rato sobre os mesmos, de modo a indicar os estilos do elemento que começaria a editar se clicasse. Mova o rato sobre o texto no cabeçalho (normalmente, aqui encontra-se o nome da empresa) e, em seguida, clique no mesmo. Um conjunto de regras de estilo nomeadas e categorizadas aparece no editor de estilo. Cada regra representa uma propriedade de estilo do elemento selecionado. Por exemplo, para o texto do cabeçalho selecionado acima, o tamanho do texto é @font-size-h1, enquanto o nome do tipo de letra com alternativas é @headings-font-family.

> Se estiver familiarizado com [bootstrap][bootstrap], estas regras são de facto [variáveis LESS][LESS variables] dentro do tema de bootstrap utilizado pelo portal do programador.
> 
> 

Vamos alterar a cor do texto do cabeçalho. Selecione a entrada no campo **@headings-color** e tipo **#000000**. Este é o código hexadecimal para a cor preta. Ao fazê-lo, verá que é apresentado um indicador de cor quadrado no final da caixa de texto. Se clicar neste indicador, um seletor de cores permite-lhe selecionar uma cor.

![Seletor de cores][api-management-customization-toolbar-color-picker]

As alterações são pré-visualizadas em tempo real enquanto são realizadas, mas são visíveis apenas para os administradores. Para tornar estas alterações visíveis para todos, clique no botão **Publicar** no editor de estilo e confirme as alterações.

![Menu Publicar][api-management-customization-toolbar-publish-form]

> Para alterar as regras de estilo que se aplicam a qualquer outro elemento na página, siga o mesmo procedimento como fez para o cabeçalho. Clique em **Selecionar um elemento na página** no editor de estilos, selecione o elemento no qual está interessado e comece a modificar os valores das regras de estilo apresentadas no ecrã.
> 
> 


## <a name="next-steps"> </a>Passos seguintes
* Saiba como personalizar o conteúdo das páginas dos portais de programador com [Modelos de portais de programador](api-management-developer-portal-templates.md).

[Change the styling of the headers]: #change-headers-styling
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-styles/api-management-management-console.png
[api-management-pp-dp-link]: ./media/api-management-customize-styles/api-management-pp-dp-link.png
[api-management-customization-toolbar-button]: ./media/api-management-customize-styles/api-management-customization-toolbar-button.png
[api-management-customization-toolbar]: ./media/api-management-customize-styles/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-styles/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-styles/api-management-customization-toolbar-publish-form.png

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/
