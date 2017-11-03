---
title: Atualizar Log Analytics do Azure para a nova pesquisa de registo | Microsoft Docs
description: "O idioma de consulta de análise de registos nova é quase aqui e pode participar na pré-visualização pública.  Este artigo descreve as vantagens do novo idioma e como converter a sua área de trabalho."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 1806b70ba0d34f49abfb954abebff8d29ae61291
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="azure-log-analytics-upgrade-to-new-log-search"></a>Análise de registos do Azure atualizar para a nova pesquisa de registo

O novo idioma de consulta de análise de registos está disponível e a área de trabalho tem de ser atualizados para tirar partido dos mesmos.  Pode atualizar a sua área de trabalho por si ou aguarde pela respetiva ser atualizado automaticamente durante o período de implementação que começa em Outubro enlace tardio e que atravessa o fim do ano.  Este artigo descreve as vantagens do novo idioma e como converter a sua área de trabalho.  

## <a name="why-the-new-language"></a>Por que motivo o novo idioma?
Compreendemos que existe tensão em qualquer transição, e não são alterar apenas o idioma de consulta experimentar do mesmo.  Existem várias razões pelas quais esta alteração irá fornecer valor significativo para os nossos clientes de análise de registos.

- **Simples mas potentes.** O novo idioma é mais fácil de compreender e semelhante ao SQL Server com construções mais como linguagem natural a que a linguagem de legado.
- **Idioma piping completa.**  O novo idioma tem ainda mais extensivos piping capacidades que a linguagem de legado.  Praticamente qualquer saída pode ser direcionada para noutro comando para criar consultas mais complexas que foram anteriormente possíveis.
- **Extrações de campo de hora de pesquisa.**  O novo idioma suporta campos de tempo de execução calculado mais avançados que o idioma de legado.  Pode utilizar cálculos complexos para campos expandidos e, em seguida, utilize os campos calculados para os comandos adicionais, incluindo associações e agregações.
- **Associações avançadas.**  O novo idioma fornece associações mais avançadas que o idioma legado, incluindo a capacidade de associar tabelas em vários campos, utilize associações internas e externas e associar nos campos expandidos.
- **As funções de data/hora.**  O novo idioma tem mais avançadas funções de data/hora que o idioma de legado.
- **Análise de inteligente.**  O novo idioma tem avançadas algoritmos para avaliar padrões nos conjuntos de dados e compare a diferentes conjuntos de dados.
- **Portal de análise avançada.**  O portal de análise avançadas oferece funcionalidades de Analysis Services não está disponíveis no portal do Log Analytics incluindo múltiplas edição de consultas, visualizações adicionais e diagnóstico avançado.
- **Consistência com outras aplicações.**  O novo idioma e o Portal de análise avançadas já são utilizados para análise no Application Insights.  Implementar para análise de registos fornece a consistência entre os serviços do Azure.
- **Melhor integração com o Power BI.** As consultas no novo idioma podem ser exportadas para o Power BI Desktop, pelo que pode utilizar as respetivas capacidades de transformação de dados avançados.
- **Muito mais.** Consulte o [idioma de consulta de análise do Azure registo](https://docs.loganalytics.io) site para obter detalhes completos e tutoriais sobre o novo idioma.


## <a name="when-can-i-upgrade"></a>Quando posso atualizar?
A atualização será revertida em todas as regiões do Azure, de modo poderão estar disponível em algumas regiões antes de outros utilizadores.  Saber quando a sua área de trabalho está disponível para ser actualizado quando for apresentada uma faixa na parte superior da sua área de trabalho inviting a atualizar.

![Atualização 1](media/log-analytics-log-search-upgrade/upgrade-01a.png)

Se a sua área de trabalho é atualizada automaticamente, em seguida, verá uma faixa que indica que é atualizada com um resumo identificar se os problemas encontrados.

 ![Atualização automática](media/log-analytics-log-search-upgrade/auto-upgrade.png)


## <a name="what-happens-after-the-upgrade"></a>O que acontece após a atualização?
As seguintes alterações são efetuadas para a sua área de trabalho quando é convertido:

- Qualquer pesquisas guardadas, regras de alertas e vistas que criou com o estruturador de vistas são automaticamente convertidas para o novo idioma.  Incluído em soluções de procura não é convertida automaticamente, mas está em vez disso, convertidos no momento quando abri-los.  
- [Dashboard](log-analytics-dashboards.md) está a ser preterido na favor de [estruturador de vistas](log-analytics-view-designer.md) e [Azure Dashboards](https://docs.microsoft.com/en-us/azure/azure-portal/azure-portal-dashboards.md).  Os mosaicos que adicionou à minha Dashboard ainda estão disponíveis, mas está a só de leitura.
- [Integração de BI de energia](log-analytics-powerbi.md) é substituída por um processo de novo.  As agendas existentes do Power BI que criou serão desativadas e tem de substituí-los com o processo de novo.
- As respostas de [ações de alerta](log-analytics-alerts-actions.md) utilizar webhooks e runbooks tem um formato de novo e poderá ter de atualizar as regras de alerta em conformidade.
- Veja o [FAQ de pesquisa de registo](log-analytics-log-search-faq.md) para perguntas comuns sobre a atualização.

## <a name="how-do-i-know-if-there-were-any-issues-from-the-upgrade"></a>Como saber se ocorreram quaisquer problemas da atualização?
Uma vez concluída a atualização, haverá um **atualizar resumo** secção as definições para a área de trabalho.  Esta secção para obter informações sobre a atualização e para a vista de verificação a

 ![Resumo da atualização](media/log-analytics-log-search-upgrade/upgrade-summary.png)

## <a name="how-do-i-manually-perform-the-upgrade"></a>Como posso efetuar manualmente a atualização?
Pode atualizar a sua área de trabalho quando vir a faixa na parte superior do portal.  

1.  Iniciar o processo de atualização ao clicar na faixa que indica que **saber mais e atualizar**.

    ![Atualização 2](media/log-analytics-log-search-upgrade/upgrade-01a.png)<br>

2.  Leia as informações adicionais sobre a atualização, na página de informações de atualização.

    ![Atualização 2](media/log-analytics-log-search-upgrade/upgrade-03.png)<br>

3.  Clique em **atualizar agora** para iniciar a atualização.

    ![Atualização 4](media/log-analytics-log-search-upgrade/upgrade-04.png)<br>Uma caixa de notificação no canto superior direito mostra o estado.
    
    ![Atualização 5](media/log-analytics-log-search-upgrade/upgrade-05.png)

4.  Já está!  Passam para a página de pesquisa de registo para ver a área de trabalho atualizada.

    ![Atualização 6](media/log-analytics-log-search-upgrade/upgrade-06.png)

Se ocorrer um problema que faz com que a atualização falhar, pode ir para o [fórum de discussão](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) e publicar a sua pergunta ou [criar um pedido de suporte](../azure-supportability/how-to-create-azure-support-request.md) do portal do Azure.

## <a name="how-do-i-learn-the-new-language"></a>Como posso saber o novo idioma?
Uma vez que é utilizada por vários serviços Criámos uma [site externo para alojar a documentação](https://docs.loganalytics.io/) para o novo idioma.  Isto inclui tutoriais, amostras e uma referência completa para o ajudar a vêm previsto. Pode percorrer o novo idioma ao obter um tutorial [introdução às consultas](https://go.microsoft.com/fwlink/?linkid=856078) e aceder a referência de linguagem em [idioma de consulta de análise de registos](https://go.microsoft.com/fwlink/?linkid=856079).  

Se pretender experimentar o novo idioma num ambiente de demonstração, incluindo um bunch de dados de exemplo, tem uma vista de olhos a [ambiente playground](https://portal.loganalytics.io/demo#/discover/home).

Se já estiver familiarizado com o idioma de consulta de análise de registos legado apesar, em seguida, pode utilizar o conversor de idioma que é adicionado à sua área de trabalho como parte da atualização.  Basta escrevê na sua consulta legada e, em seguida, clique em **converter** para ver a versão traduzida.  Pode, em seguida, um clique no botão de procura para executar a pesquisa ou copiar e colar a consulta convertida para utilizar em qualquer outro como uma regra de alerta local.  Também pode ter um aspeto no nosso [cábula](log-analytics-log-search-transition.md) que compara diretamente consultas comuns do idioma legado.

![Conversor de idioma](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="next-steps"></a>Passos seguintes
- Veja uma [tutorial num novo idioma](https://go.microsoft.com/fwlink/?linkid=856078).
- Percorrer um [tutorial sobre como utilizar o portal de registo de pesquisa](log-analytics-log-search-log-search-portal.md) com o novo idioma de consulta.
- Obtenha uma introdução para o novo [portal da análise avançadas](https://go.microsoft.com/fwlink/?linkid=856587).
