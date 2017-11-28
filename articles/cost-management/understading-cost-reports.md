---
title: "Noções sobre o custo de relatórios na gestão de custo do Azure | Microsoft Docs"
description: "Este artigo ajuda-o a compreender a estrutura básica do Cloudyn relatórios e as funções."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/27/2017
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: df2108a6e2a01195340a09eacf1c56f9d738c923
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="understanding-cost-reports"></a>Relatórios de compreensão de custos

Este artigo ajuda-o a compreender a estrutura básica do Cloudyn relatórios e as funções. A maioria dos relatórios de Cloudyn são intuitivas e tem um aspeto e funcionalidade uniforme. Depois de ler este artigo, estará pronto a utilizar todos os relatórios. Muitas funcionalidades padrão estão disponíveis ao longo de vários relatórios, permitindo-lhe navegar os relatórios com facilidade. Os relatórios são personalizáveis e pode selecionar a partir de várias opções para calcular e apresentar resultados.

## <a name="report-fields-and-options"></a>Opções e campos do relatório

Eis ver um exemplo do relatório custo ao longo do tempo. A maioria dos relatórios de Cloudyn têm um esquema semelhante.

![relatório de exemplo](./media/understanding-cost-reports/sample-report.png)

Cada área numerada na imagem anterior está descrita em detalhe nas seguintes informações:

1. **Intervalo de datas**

    Utilize a lista de intervalo de datas para definir um intervalo de tempo de relatório utilizando uma predefinição ou personalizado.
2. **Filtros guardado**

    Utilize a lista de filtros guardado para guardar os grupos atuais e os filtros que são aplicados ao relatório. Filtros guardados estão disponíveis em relatórios de custos e o desempenho, incluindo:

      - Análise de custo
      - Alocação
      - Gestão de Ativos
      - Otimização

  Escreva um nome de filtro e clique em **guardar**.

3. **Etiquetas**

    Utilize a área de etiquetas ao grupo por categorias de etiqueta. Etiquetas listadas no menu departamento do Azure ou as etiquetas de centro de custos ou tags de entidade e subscrição do custo do Cloudyn. Selecione etiquetas para filtrar os resultados. Também pode escrever um nome de etiqueta (palavra-chave) para filtrar os resultados.

    ![Selecione opções](./media/understanding-cost-reports/select-options.png)

    Clique em **adicionar** para adicionar um novo filtro.

    ![Adicionar um filtro](./media/understanding-cost-reports/add-filter.png)

    Etiqueta de agrupamento ou filtragem não estar relacionado com recursos do Azure ou as etiquetas de grupo de recursos.

    Agrupamento de etiqueta de custo de alocação e filtragem estão disponíveis no **grupos** opção do menu.

4. **Grupos em relatórios**

    Utilizar grupos na análise de custos relatórios para mostrar padrão, descritos categorias de dados do relatório de faturação.  No entanto, os grupos no custo alocação relatórios Mostrar categorias de vistas baseada na etiqueta. Categorias de tag são definidas no modelo de alocação de custos e categorias de descritos padrão de dados de faturação.

    ![etiquetas de grupos](./media/understanding-cost-reports/groups-tags01.png)

    ![etiquetas de grupos](./media/understanding-cost-reports/groups-tags02.png)

    Em relatórios de alocação de custo, os grupos de categorias de grupo baseada na tag poderão incluir:
      - Etiquetas
      - etiquetas de grupo de recursos
      - Cloudyn etiquetas de entidade de custos
      - Categorias de etiqueta de subscrição para fins de alocação de custos

  Exemplos podem incluir:
     - Centro de custos
     - Departamento
     - Aplicação
     - Ambiente
     - Custo de código

5. **Filtros**

    Utilize único ou selecionar vários filtros para definir os intervalos para valores selecionados. Para definir um filtro, clique em **adicionar** e, em seguida, selecione as categorias de filtro e valores.

6. **Modelo de custo**

    Utilize o modelo de custos para selecionar um modelo de custo que criou anteriormente com 360 de alocação de custo. Pode ter vários modelos de custo de Cloudyn, dependendo dos requisitos de atribuição de custo. Alguns dos seus agrupamentos organizacionais poderão ter de custo requisitos de atribuição que sejam diferentes das outras pessoas. Cada equipa pode ter o seu próprio modelo custo dedicado.

    Para obter informações sobre como criar uma definição de modelo de alocação de custo, consulte [Utilize etiquetas personalizadas para alocar os custos](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Amortization**

    Utilize Amortization nos relatórios de alocação de custos para ver a utilização não baseia taxas de serviço ou os custos de pagar única e distribuídos os custos ao longo do tempo uniformemente durante o respetivo tempo de vida. Exemplos de taxas Monouso poderão incluir:
    - Anual taxas de suporte
    - Taxas de componentes de segurança anual
    - Taxas de compra de instâncias reservadas
    - Alguns itens do Azure Marketplace.

  Em Amortization, selecione **Amortized custo** ou **custo real**.

8. **Resolução**

    Utilize a resolução para selecionar a resolução de tempo dentro do intervalo de datas selecionadas. Determina a resolução de tempo como unidades são apresentadas no relatório e podem ser:
    - Diariamente
    - Semanalmente
    - Custo
    - Trimestralmente
    - Anual

9. **Regras de atribuição**

    Utilize regras de atribuição para aplicar ou desativar o novo cálculo do custo alocação custo. Pode ativar ou desativar o novo de cálculo de atribuição de custos para dados de faturação. O novo cálculo aplica-se para as categorias selecionadas no relatório. Permite-lhe avaliar o impacto de novo cálculo de atribuição de custo em relação a dados não processados de faturação.

10. **Não categorizado**

    Utilize não categorizado para incluir ou excluir os custos não categorizados no relatório.

11. **Mostrar/ocultar campos**

    A opção de Mostrar/ocultar não tem qualquer efeito nos relatórios.

12.   **Formatos de apresentação**

    Utilize os formatos de apresentação para selecionar várias vistas de gráfico ou tabela.

    ![formatos de apresentação](./media/understanding-cost-reports/display-formats.png)

13. **Cor multi**

    Utilize multi cores para definir a cor dos gráficos no relatório.

14. **Ações**

    Utilize ações para guardar, exportar ou agendamento do relatório.

## <a name="next-steps"></a>Passos seguintes

- Se ainda não tiver concluído o primeiro tutorial para a gestão de custo, leia-lo no [rever os custos de utilização e](tutorial-review-usage.md).
