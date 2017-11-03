---
title: "Introdução à Azure - ferramenta de modelação de ameaça Microsoft - | Microsoft Docs"
description: "Esta é uma descrição mais aprofundada, realce a ferramenta de modelação de ameaça em ação."
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 2d940b42108948f4cd36a585f1e79def05fe8fd3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Introdução com a ferramenta de modelação de ameaça

A equipa de nuvem e de ferramentas de segurança empresarial lançou a pré-visualização de ferramenta de modelação de ameaça anteriormente este ano como um livre  **[clique para transferência](https://aka.ms/tmtpreview)**. A alteração no mecanismo de entrega permite-nos emitir as melhorias e correções de erros mais recentes para os clientes sempre que abrem a ferramenta, tornando mais fácil de manter e utilizar.
Este artigo orienta-o processo de introdução a ameaça de Microsoft SDL modelação abordagem e mostra-lhe como utilizar a ferramenta para desenvolver modelos de ameaça excelente como uma estrutura principal do processo de segurança.

Este artigo baseia-se dados de conhecimento existente da ameaça SDL modelação abordagem. Para uma análise rápida, consulte  **[aplicações de Web de modelação de ameaça](https://msdn.microsoft.com/library/ms978516.aspx)**  e uma versão arquivada do  **[desvendar segurança falhas de utilizar a abordagem STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)**  MSDN artigo publicado 2006.

Para resumir rapidamente, a abordagem envolve a criação de um diagrama, identificar ameaças, mitigá-los e validar cada mitigação. Eis um diagrama realça este processo:

![Processo do SDL](./media/azure-security-threat-modeling-tool/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>A iniciar a processo de modelação de ameaça

Quando iniciar a ferramenta de modelação de ameaça, irá notar algumas coisas, como mostrado na imagem:

![Página de início em branco](./media/azure-security-threat-modeling-tool/tmtstart.png)

### <a name="threat-model-section"></a>Secção de modelo de ameaça

| Componente                                   | Detalhes                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Comentários, sugestões e botão de problemas** | Leva-o a  **[fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)**  para todos os aspetos do SDL. Proporciona uma oportunidade de leitura que outros utilizadores estão a fazer, juntamente com soluções e recomendações. Se ainda não é possível encontrar aquilo que procura, e-mail tmtextsupport@microsoft.com para a nossa equipa de suporte para o ajudar a                                                                                                                            |
| **Criar um modelo**                          | Abre uma tela em branco para si desenhar o diagrama. Certifique-se de que seleciona o modelo que pretende utilizar para o seu modelo                                                                                                                                                                                                                                                                                                                                                                       |
| **Modelo para modelos de novo**                 | Tem de selecionar o modelo a utilizar antes de criar um modelo. Nosso modelo principal é o modelo de modelo de ameaça do Azure, que contém stencils específicos do Azure, ameaças e atenuações. Para modelos genéricos, selecione a Base de dados de conhecimento do SDL TM no menu pendente. Pretende criar o seu próprio modelo ou submeter um novo para todos os utilizadores? Consulte a nossa  **[modelo repositório](https://github.com/Microsoft/threat-modeling-templates)**  página do GitHub para obter mais informações                              |
| **Abra um modelo**                            | <p>É aberto guardado anteriormente modelos de ameaça. A funcionalidade de abrir modelos recentemente está excelente se precisar de abrir os ficheiros mais recentes. Quando paira o rato sobre a seleção, encontrará 2 formas para abrir modelos:</p><p><ul><li>Abra partir deste computador – clássica forma de abrir um ficheiro com o armazenamento local</li><li>Abra do OneDrive – equipas podem utilizar as pastas no OneDrive para guardar e partilhar todos os respetivos modelos de ameaça numa única localização para o ajudar a aumentar a produtividade e a colaboração</li></ul></p> |
| **Guia de introdução**                   | Abre o  **[ferramenta de modelação de ameaça do Microsoft](./azure-security-threat-modeling-tool.md)**  página principal                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Secção de modelo

| Componente               | Detalhes                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Criar novo modelo** | Abre um modelo em branco a compilação. A menos que tenha conhecimentos aprofundados na criação de modelos a partir do zero, recomendamos-lhe incorporar a partir de fórmulas existentes |
| **Modelo aberto**       | Abre a existente modelos para fazer alterações                                                                                                              |

A equipa da ferramenta de modelação de ameaça está constantemente a trabalhar para melhorar a funcionalidade da ferramenta e experiência. Algumas alterações secundárias poderão ter ocorrido ao longo do ano, mas todas as alterações principais exigem reescritas no guia. Fazem referência ao mesmo, muitas vezes, para garantir que obtém os anúncios mais recentes.

## <a name="building-a-model"></a>Criar um modelo

Nesta secção, iremos seguir:

- Cristina (um programador)
- Ricardo (um Gestor de programa) e
- Ashish (um recurso de teste)

Vai durante o processo de desenvolver o seu primeiro modelo de ameaça.

> Ricardo: Olá Cristina, posso trabalhados o diagrama de modelo de ameaça e Queríamos ter a certeza obtivemos os detalhes da direita. Pode ajudar-me analise-lo?
> Cristina: absolutamente. Vamos ver.
> Ricardo abre-se a ferramenta e partilhe o seu ecrã com Cristina.

![Modelo de ameaça básico](./media/azure-security-threat-modeling-tool/basictmt.png)

> Cristina: Ok, procura simples, mas pode a guiá-me-lo?
> Ricardo: se! Segue-se a repartição:
> - Utilizador nossa humano é desenhado como uma entidade externa — um quadrado
> - Se está a enviar comandos para o nosso servidor Web — círculo
> - O servidor Web está a consultar uma base de dados (duas linhas paralelas)

O que Ricardo apenas mostrou Cristina é DFD, abreviado para  **[diagrama de fluxo de dados](https://en.wikipedia.org/wiki/Data_flow_diagram)**. A ferramenta de modelação de ameaça permite aos utilizadores especificarem limites de fidedignidade, indicados por linhas de ponteada vermelhos, para mostrar onde diferentes entidades estão no controlo. Por exemplo, os administradores de TI necessitam de um sistema do Active Directory para efeitos de autenticação, pelo que o Active Directory está fora do seu controlo.

> Cristina: Procura direita-me. O que sobre as ameaças?
> Ricardo: Deixar-me mostram-lhe.

## <a name="analyzing-threats"></a>Analisar as ameaças

Quando clica na vista de análise da seleção de menu de ícone (ficheiro com Lupa), ele é direcionado para uma lista de ameaças geradas a ferramenta de modelação de ameaças encontradas baseada num modelo predefinido, que utiliza a abordagem SDL chamada  **[ STRIDE (Spoofing, adulteração, divulgação de informações, recusa de serviço e elevação de privilégios)](https://en.wikipedia.org/wiki/STRIDE_(security))**. A ideia é que o software vem sob um conjunto previsível de ameaças, que pode ser encontrado utilizar estas 6 categorias.

Esta abordagem é como a proteger a sua próxima, assegurando que cada porta e a janela tem um mecanismo de bloqueio antes de adicionar um sistema de alarme ou chasing após o ladrão.

![Ameaças básicas](./media/azure-security-threat-modeling-tool/basicthreats.png)

Ricardo começa selecionando o primeiro item na lista. Eis o que acontece:

Em primeiro lugar, a interação entre os dois stencils é melhorada

![Interação](./media/azure-security-threat-modeling-tool/interaction.png)

Segundo informações adicionais sobre a ameaça, é apresentado na janela de propriedades de ameaça

![Informações de interação](./media/azure-security-threat-modeling-tool/interactioninfo.png)

A ameaça gerada ajuda-lo a compreender potenciais falhas de design. A categorização STRIDE dá-lhe uma ideia no vetores de ataque potencial, enquanto a descrição adicional informa-lo exatamente o que é o problema, juntamente com potenciais formas de resolver. Ele pode utilizar campos editáveis para escrever notas nos detalhes do justificação ou alterar as classificações de prioridade, dependendo da barra de erros da sua organização.

Os modelos do Azure tem detalhes adicionais para ajudar os utilizadores a compreender não apenas o que é incorreto, mas também como o corrigir, adicionando as descrições, exemplos e hiperligações para documentação específicos do Azure.

A descrição efetuadas caso tenha em atenção a importância da adição de um mecanismo de autenticação para impedir que os utilizadores a ser falsificado, revelando a ameaça para ser trabalhados primeiro. Alguns minutos para o debate com Cristina, estes compreendido a importância de implementação de controlo de acesso e funções. Ricardo preenchidos algumas notas rápidas para se certificar de que estes foram implementadas.

Como correu Ricardo para as ameaças em divulgação de informações, ele realizados que o plano de controlo de acesso necessárias algumas contas de só de leitura para a geração do relatório e auditoria. Ele wondered se deve ser uma ameaça de novo, mas as mitigações foram os mesmos, pelo que ele indicados a ameaça em conformidade.
Ele também considerar sobre divulgação de informações pouco mais e realizados que as bandas de cópia de segurança foram vai necessitam de encriptação, uma tarefa para a equipa de operações.

Ameaças não aplicáveis para o design devido a mitigações existentes ou segurança garante que pode ser alterada para "Não é aplicável" da lista pendente de estado. Existem três opções de outras: não foi iniciado – seleção predefinida, tem de investigação – utilizado a seguir cópias de segurança em itens e Mitigated – uma vez que é completamente trabalhado.

## <a name="reports--sharing"></a>Relatórios & partilha

Depois de Ricardo atravessa a lista com Cristina e adiciona notas importantes, mitigações/justifications, prioridade e alterações de estado, seleciona putador relatórios -> Criar relatório completo -> guardar relatórios, imprime os um relatório nice para ele percorrer com colegas para garantir que o trabalho de segurança adequada é implementado.

![Informações de interação](./media/azure-security-threat-modeling-tool/report.png)

Se quiser Ricardo partilhar o ficheiro em vez disso, ele pode facilmente fazê por guardar na conta do OneDrive da sua organização. Assim que ele que, ele pode copiar a ligação do documento e partilhá-lo com as seus colegas. 

## <a name="threat-modeling-meetings"></a>Reuniões de modelação de ameaça

Quando Ricardo enviados o modelo de ameaça à sua colega utilizando OneDrive, Ashish, o recurso de teste, foi underwhelmed. Seemed como Ricardo e Cristina omitido bastante alguns importantes nos casos extremos, que pode ficar facilmente comprometido. A skepticism é um complemento para modelos de ameaça.

Neste cenário, depois de Ashish demorou através do modelo de ameaça, ele chamado para reuniões de modelação de ameaça duas: uma reunião sincronizar do processo e percorrer os diagramas e, em seguida, uma segunda reunião para ameaça rever e início de sessão desativado.

No reunião primeiro, Ashish despendido walking todas as pessoas através a ameaça SDL processo de modelação de 10 minutos. Em seguida, ele solicitados a configurar o diagrama de modelo de ameaça e iniciado explicá-lo em detalhe. Dentro de cinco minutos, um componente em falta importante tinha sido identificado.

Alguns minutos posterior, Ashish e Ricardo obteve para um debate expandido de como o servidor Web foi criado. Não foi da forma ideal para uma reunião continuar, mas todas as pessoas, eventualmente, compreendo que detetar a discrepância numa fase inicial foi vai guardá-los hora no futuro.

Reunião segunda, a equipa walked através de ameaças, abordados algumas formas de resolvê-los e assinado no modelo de ameaça. Estes verificado o documento no controlo de origem e continuaram com o desenvolvimento.

## <a name="thinking-about-assets"></a>Ter em consideração ativos

Alguns leitores que tenham ameaça modelada poderão reparar que ainda não falamos sobre recursos de todo. Iremos já descobriu que número engenheiros de software compreendem de mais do que eles compreender o conceito de recursos e recursos de que um atacante poderá estar interessado no seu software.

Se ao modelo de ameaça uma próxima, poderá começar por ter em consideração a família, fotografias insubstituíveis ou elementos artísticos importantes. Talvez pode começar por ter em consideração que poderá quebrar e o sistema de segurança atual. Ou poderá começar por considerar as funcionalidades físicas, como o conjunto ou o front-porch. Estes são análogos a ter em consideração ativos, os atacantes ou estrutura de software. Qualquer uma destas três abordagens funcionar.

A abordagem de ameaça modelação que iremos tiver aqui apresentada é substancialmente mais simples do que o Microsoft efetuou no passado. Detetámos que a abordagem de design de software funciona bem para várias equipas. Esperamos que incluam mesmo seu.

## <a name="next-steps"></a>Passos Seguintes

Envie as perguntas, comentários e preocupações para tmtextsupport@microsoft.com. **[Transferir](https://aka.ms/tmtpreview)**  a ferramenta de modelação de ameaça para começar a utilizar.