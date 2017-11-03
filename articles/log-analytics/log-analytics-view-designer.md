---
title: "Criar vistas para analisar dados de análise de registos do OMS | Microsoft Docs"
description: "Estruturador de vistas no Log Analytics permite-lhe criar vistas personalizadas que são apresentadas no portal do OMS e o Azure e conter visualizações diferentes dos dados no repositório de OMS. Este artigo contém uma descrição geral do estruturador de vistas e procedimentos para criar e editar vistas personalizadas."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: bwren
ms.openlocfilehash: e3c463d749dc4179df58286b9bb75584880a6bc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-view-designer-to-create-custom-views-in-log-analytics"></a>Utilize o estruturador de vistas para criar vistas personalizadas no Log Analytics
O estruturador de vistas no [Log Analytics](log-analytics-overview.md) permite-lhe criar vistas personalizadas na consola do OMS que contêm visualizações diferentes dos dados no repositório de OMS. Este artigo contém uma descrição geral do estruturador de vistas e procedimentos para criar e editar vistas personalizadas.

Outros artigos disponíveis para o estruturador de vistas são:

* [Mosaico referência](log-analytics-view-designer-tiles.md) -referência das definições para cada um dos mosaicos disponíveis para utilização na sua vistas personalizadas.
* [Referência de parte de visualização](log-analytics-view-designer-parts.md) -referência das definições para cada um dos mosaicos disponíveis para utilização na sua vistas personalizadas.

>[!NOTE]
> Se a sua área de trabalho tiver sido atualizada para o [idioma de consulta de análise de registos nova](log-analytics-log-search-upgrade.md), e consultas em todas as vistas têm de ser escritas [novo idioma de consulta](https://go.microsoft.com/fwlink/?linkid=856078).  Todas as vistas que foram criadas para a área de trabalho foi atualizada será automtically convertido.

## <a name="concepts"></a>Conceitos
As vistas criadas com o estruturador de vistas contém os elementos na seguinte tabela.

| Parte | Descrição |
|:--- |:--- |
| Mosaico |Apresentado no dashboard de descrição geral da análise do registo principal.  Inclui um visual resumir as informações contidas na vista personalizada.  Diferentes tipos de mosaico fornecem visualizações diferentes dos registos no repositório de OMS.  Clique no mosaico para abrir a vista personalizada. |
| Vista personalizada |Apresentada quando o utilizador clica no mosaico.  Contém um ou mais peças de visualização. |
| Partes de visualização |Visualização de dados no repositório de OMS com base num ou vários [pesquisas de registo](log-analytics-log-searches.md).  A maioria das partes irão incluir um cabeçalho que fornece uma visualização de alto nível e uma lista dos resultados da parte superiores.  Tipos de outra parte fornecem visualizações diferentes dos registos no repositório de OMS.  Clique nos elementos na parte para efetuar uma pesquisa de registo que fornece registos detalhados. |

![Descrição geral do Designer de vista](media/log-analytics-view-designer/overview.png)

## <a name="add-view-designer-to-your-workspace"></a>Adicionar o estruturador de vistas a sua área de trabalho
Enquanto o estruturador de vistas está em pré-visualização, deve adicioná-lo à sua área de trabalho selecionando **funcionalidades de pré-visualização** no **definições** secção do portal do OMS.

![Ativar a pré-visualização](media/log-analytics-view-designer/preview.png)

## <a name="creating-and-editing-views"></a>Criação e edição de vistas
### <a name="create-a-new-view"></a>Criar uma nova vista
Abra uma nova vista na **estruturador de vistas** clicando no mosaico do estruturador de vistas no dashboard principal do OMS.

![Mosaico de vista de Designer](media/log-analytics-view-designer/view-designer-tile.png)

### <a name="edit-an-existing-view"></a>Editar uma vista existente
Para editar uma vista existente no estruturador de vistas, abra a vista, clicando no seu mosaico no dashboard principal do OMS.  Em seguida, clique o **editar** botão para abrir a vista no estruturador de vistas.

![Editar uma vista](media/log-analytics-view-designer/menu-edit.png)

### <a name="clone-an-existing-view"></a>Clonar uma vista existente
Ao clonar uma vista, cria uma nova vista e abre-o no Designer de vista.  A nova vista terão o mesmo nome que o original com "copiar" anexado ao fim do mesmo.  A clonagem de uma vista, abra a vista existente ao clicar no seu mosaico no dashboard principal do OMS.  Em seguida, clique o **Clone** botão para abrir a vista no estruturador de vistas.

![Clonar uma vista](media/log-analytics-view-designer/edit-menu-clone.png)

### <a name="delete-an-existing-view"></a>Eliminar uma vista existente
Para eliminar uma vista existente, abra a vista, clicando no seu mosaico no dashboard principal do OMS.  Em seguida, clique o **editar** botão para abrir a vista no estruturador de vistas e clique em **Eliminar vista**.

![Eliminar uma vista](media/log-analytics-view-designer/edit-menu-delete.png)

### <a name="export-an-existing-view"></a>Exportar uma vista existente
Pode exportar uma vista para um ficheiro JSON que pode importar para outra área de trabalho ou utilize um [modelo Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).  Para exportar uma vista existente, abra a vista, clicando no seu mosaico no dashboard principal do OMS.  Em seguida, clique o **exportar** botão para criar um ficheiro na pasta de transferência do browser.  O nome do ficheiro será o nome da vista com a extensão *omsview*.

![Exportar uma vista](media/log-analytics-view-designer/edit-menu-export.png)

### <a name="import-an-existing-view"></a>Importar uma vista existente
Pode importar um *omsview* ficheiro que tenha exportado a partir de outro grupo de gestão.  Para importar uma vista existente, primeiro de criar uma nova vista.  Em seguida, clique o **importação** botão e selecione o *omsview* ficheiro.  A configuração no ficheiro será copiada para a vista existente.

![Exportar uma vista](media/log-analytics-view-designer/edit-menu-import.png)

## <a name="working-with-view-designer"></a>Trabalhar com o estruturador de vistas
O estruturador de vistas tem três painéis.  O **Design** painel representa a vista personalizada.  Ao adicionar mosaicos e partes do **controlo** painel para o **Design** que forem adicionadas para a vista de painel.  O **propriedades** painel irá apresentar as propriedades para o mosaico ou peça selecionada.

![Estruturador de Vista](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>Configurar o mosaico de vista
Uma vista personalizada pode ter apenas um único mosaico.  Selecione o **mosaico** separador o **controlo** painel para ver o mosaico atual ou selecione um alternativo.  O **propriedades** painel irá apresentar as propriedades para o mosaico atual.  Configurar as propriedades de mosaico, de acordo com as informações detalhadas a [mosaico referência](log-analytics-view-designer-tiles.md) e clique em **aplicar** ao guardar as alterações.

### <a name="configure-visualization-parts"></a>Configurar partes de visualização
Uma vista pode incluir qualquer número de partes de visualização.  Selecione o **vista** separador e, em seguida, uma parte de visualização para adicionar à vista.  O **propriedades** painel irá apresentar as propriedades para a parte selecionada.  Configurar as propriedades de vista, de acordo com as informações detalhadas a [referência de parte de visualização](log-analytics-view-designer-parts.md) e clique em **aplicar** ao guardar as alterações.

### <a name="delete-a-visualization-part"></a>Eliminar uma peça de visualização
Pode remover uma parte de visualização da vista clicando a **X** botão no canto superior direito da peça.

### <a name="rearrange-visualization-parts"></a>Reorganizar partes de visualização
Vistas tem apenas uma linha de partes de visualização.  Reorganizar partes existentes numa vista, clicando e arrastando-los para uma nova localização.

## <a name="next-steps"></a>Passos seguintes
* Adicionar [mosaicos](log-analytics-view-designer-tiles.md) à vista personalizada.
* Adicionar [partes de visualização](log-analytics-view-designer-parts.md) à vista personalizada.
