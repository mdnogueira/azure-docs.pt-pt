---
title: "Personalizar o portal do programador na Gestão de API do Azure | Microsoft Docs"
description: Saiba como personalizar o portal do programador na API Management do Azure.
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 13431648e87d085161ad278dc991d49f7872be34
ms.openlocfilehash: 60213f885020a5ba36d6ada0812f755e06b3c48b


---
# <a name="customize-the-developer-portal-in-azure-api-management"></a>Personalizar o portal do programador na API Management do Azure
Este guia mostra como modificar o aspeto e a funcionalidade do portal do programador na API Management do Azure para manter a consistência com a sua marca.

## <a name="change-page-headers"> </a>Alterar o texto ou logótipo no cabeçalho da página
Um dos aspetos fundamentais da personalização do portal é substituir o texto na parte superior de todas as páginas pelo nome ou logótipo da sua empresa.

O conteúdo do portal do programador é modificado através do portal do publicador, que é acedido a partir do Portal do Azure. Para o alcançar, clique em **Portal do publicador** na barra de ferramentas de serviço da sua instância de Gestão de API.

![Portal do publicador][api-management-management-console]

O portal do programador baseia-se num sistema de gestão de conteúdo ou CMS. O cabeçalho que aparece em todas as páginas é um tipo especial de conteúdo conhecido como um widget. Para editar o conteúdo desse widget, clique em **Widgets** no menu **Portal do Programador** à esquerda e, em seguida, selecione o widget **Cabeçalho** na lista.

![Cabeçalho de widgets][api-management-widgets-header]

O conteúdo do cabeçalho é editável a partir do campo **Corpo**. Altere o texto para “Portal do Programador da Fabrikam” e, em seguida, clique em **Guardar** na parte inferior da página.

Agora deve conseguir ver o novo cabeçalho em todas as páginas do portal do programador.

> Para abrir o portal do programador a partir do portal do publicador, clique em **Portal do programador** na barra superior.
> 
> 

## <a name="change-headers-styling"> </a>Alterar o estilo dos cabeçalhos
As cores, tipos de letra, tamanhos, espaços e outros elementos relacionados com o estilo de qualquer página no portal são definidos por regras de estilo. Para editar os estilos, enquanto estiver no **Portal de programador**, abra a barra de ferramentas de personalização no lado esquerdo, movendo sobre o ícone de personalização e, em seguida, selecione "estilos" na barra de ferramentas.

![Botão da barra de ferramentas de personalização][api-management-customization-toolbar-button]

Existem duas formas principais de editar regras de estilo - pode consultar a lista de todas as regras de estilo utilizadas em qualquer local que é apresentada por predefinição e modificar um estilo, conforme necessário, ou pode escolher **Selecionar um elemento na página** e, em seguida, clicar em qualquer parte da página para ver apenas os estilos desse elemento.

Nesta secção, pretendemos alterar o estilo apenas dos cabeçalhos. Clique na opção **Selecionar um elemento na página** da barra de ferramentas do editor de estilo. 

![Barra de ferramentas de personalização][api-management-customization-toolbar]

Os elementos ficam agora realçados à medida que paira o rato sobre os mesmos, de modo a indicar os estilos do elemento que começaria a editar se clicasse. Mova o rato sobre o texto que representa o nome da empresa no cabeçalho (“Portal do Programador da Fabrikam” se seguiu as instruções da secção anterior) e, em seguida, clique no mesmo. Um conjunto de regras de estilo nomeadas e categorizadas aparece no editor de estilo. Cada regra representa uma propriedade de estilo do elemento selecionado. Por exemplo, para o texto do cabeçalho selecionado acima, o tamanho do texto é @font-size-h1, enquanto o nome do tipo de letra com alternativas é @headings-font-family.

> Se estiver familiarizado com [bootstrap][bootstrap], estas regras são de facto [variáveis LESS][LESS variables] dentro do tema de bootstrap utilizado pelo portal do programador.
> 
> 

Vamos alterar a cor do texto do cabeçalho. Selecione a entrada no campo **@headings-color** e tipo **#000000**. Este é o código hexadecimal para a cor preta. Ao fazê-lo, verá que é apresentado um indicador de cor quadrado no final da caixa de texto. Se clicar neste indicador, um seletor de cores permite-lhe selecionar uma cor.

![Seletor de cores][api-management-customization-toolbar-color-picker]

As alterações são pré-visualizadas em tempo real enquanto são realizadas, mas são visíveis apenas para os administradores. Para tornar estas alterações visíveis para todos, clique no botão **Publicar** no editor de estilo e confirme as alterações.

![Menu Publicar][api-management-customization-toolbar-publish-form]

> Para alterar as regras de estilo que se aplicam a qualquer outro elemento na página, siga o mesmo procedimento como fez para o cabeçalho. Clique em **Escolher um elemento** no editor de estilo, selecione o elemento em que está interessado e comece a modificar os valores das regras de estilo apresentadas no ecrã.
> 
> 

## <a name="edit-page-contents"> </a>Editar o conteúdo de uma página
O portal do programador é constituído por páginas geradas automaticamente como APIs, Produtos, Aplicações, Problemas e conteúdo escrito manualmente. Uma vez que é baseado num sistema de gestão de conteúdo, pode criar estes conteúdos conforme necessário.

Para ver a lista de todas as páginas de conteúdo existentes, clique em **Conteúdo** no menu **Portal do programador** no portal do publicador.

![Gerir conteúdo][api-management-customization-manage-content]

Clique na página **Bem-vindo** para editar o que é apresentado na home page do portal do programador. Efetue as alterações que pretender, pré-visualize-as se necessário e, em seguida, clique em **Publicar Agora** para que fiquem visíveis para todos os utilizadores.

> A home page utiliza um esquema especial que permite apresentar uma faixa na parte superior. Esta faixa não é editável a partir da secção **Conteúdo**. Para editar esta faixa, clique em **Widgets** no menu **Portal do programador**, selecione **Home page** na lista pendente **Camada Atual** e, em seguida, abra o item **Faixa** na **Secção em destaque**. O conteúdo deste widget é editável, tal como qualquer outra página.
> 
> 

## <a name="next-steps"> </a>Passos seguintes
* Saiba como personalizar o conteúdo das páginas dos portais de programador com [Modelos de portais de programador](api-management-developer-portal-templates.md).

[Change the text/logo in the page headers]: #change-page-headers
[Change the styling of the headers]: #change-headers-styling
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-portal/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-customize-portal/api-management-widgets-header.png
[api-management-customization-toolbar-button]: ./media/api-management-customize-portal/api-management-customization-toolbar-button.png
[api-management-customization-toolbar]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
[api-management-customization-manage-content]: ./media/api-management-customize-portal/api-management-customization-manage-content.png


[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/



<!--HONumber=Feb17_HO1-->


