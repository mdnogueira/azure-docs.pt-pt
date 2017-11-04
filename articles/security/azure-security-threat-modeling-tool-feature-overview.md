---
title: "Ferramenta - Azure de modelação de ameaça de Microsoft | Microsoft Docs"
description: "Saiba mais sobre todas as funcionalidades disponíveis na ferramenta de modelação de ameaça"
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
ms.openlocfilehash: 5c60e13028c3ccdf3269d74ab4724bb34ca10c19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="threat-modeling-tool-feature-overview"></a>Descrição geral da funcionalidade de ferramenta modelação de ameaça

A ferramenta de modelação de ameaça pode ajudá-lo com a necessidades de modelação de ameaça. Para uma introdução básica para a ferramenta, consulte [começar a utilizar a ferramenta de modelação de ameaça](./azure-security-threat-modeling-tool-getting-started.md).

> [!NOTE]
>A ferramenta de modelação de ameaça é atualizada frequentemente, por isso este guia, muitas vezes, para ver a nossa funcionalidades e melhorias mais recentes.

Para abrir uma página em branco, selecione **criar modelo de um**.

![Página em branco](./media/azure-security-threat-modeling-tool/tmtstart.png)

Para ver as funcionalidades atualmente disponíveis na ferramenta, utilize o modelo de ameaça criado pelo nossa equipa no [começar](./azure-security-threat-modeling-tool-getting-started.md) exemplo.

![Modelo de ameaça básico](./media/azure-security-threat-modeling-tool/basictmt.png)

## <a name="navigation"></a>Navegação

Antes de Vamos discutir as funcionalidades integradas, vamos rever os componentes principais encontrados na ferramenta.

### <a name="menu-items"></a>Itens de menu

A experiência é semelhante de outros produtos Microsoft. Vamos rever os itens de menu de nível superior.

![Itens de menu](./media/azure-security-threat-modeling-tool/menuitems.png)

| Etiqueta                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Ficheiro** | <ul><li>Abra o, guardar e fechar ficheiros</li><li>Inicie sessão e terminar sessão no OneDrive contas.</li><li>Partilhe ligações (ver e editar).</li><li>Ver informações de ficheiro.</li><li>Aplica um novo modelo para modelos existentes.</li></ul> |
| **Editar** | Anulação e ações de Refazer, bem como copiar, cola e elimina. |
| **Vista** | <ul><li>Alternar entre **Analysis** e **Design** vistas.</li><li>Abra o windows fechados (por exemplo, stencils, propriedades de elemento e mensagens).</li><li>Esquema de repor as predefinições.</li></ul> |
| **Diagrama** | Adicionar e eliminar diagramas e avance separadores de diagramas. |
| **Relatórios** | Crie relatórios HTML para partilhar com outras pessoas. |
| **Ajuda** | Encontrar guias para ajudar a utilizar a ferramenta. |

Os símbolos são os atalhos para os menus de nível superior:

| Símbolo                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Abrir** | É aberto um novo ficheiro. |
| **Guardar** | Guarda o ficheiro atual. |
| **Design** | Abre o **Design** vista, onde pode criar modelos. |
| **Analisar** | Mostra gerado ameaças e as respetivas propriedades. |
| **Adicionar diagrama** | Adiciona um novo diagrama (semelhante a novos separadores no Excel). |
| **Eliminar diagrama** | Elimina o diagrama atual. |
| **Cortar/copiar/colar** | Copia, cuts e cola sobre elementos. |
| **Anulação/Refazer** | Anular e efectuar novamente ações. |
| **Ampliar / reduzir** | Amplia inteira e para o diagrama para uma melhor vista. |
| **Comentários** | Abre o Fórum do MSDN. |

### <a name="canvas"></a>Tela

A tela é o espaço de onde pode arrastar e largar os elementos. Arrastar e largar é a forma mais rápida e eficiente para criar modelos. Pode também com o botão direito e selecione os itens no menu Adicionar versões genéricas de elementos, conforme mostrado:

#### <a name="drop-the-stencil-on-the-canvas"></a>Remova o stencil na tela

![Largar tela](./media/azure-security-threat-modeling-tool/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Selecione o stencil

![Propriedades de elemento](./media/azure-security-threat-modeling-tool/canvasdrop2.png)

### <a name="stencils"></a>Stencils

Com base no modelo de que selecionar, pode encontrar todos os stencils disponíveis para utilização. Se não conseguir localizar os elementos à direita, utilize outro modelo. Ou pode modificar um modelo para se ajustarem às suas necessidades. Geralmente, pode encontrar uma combinação de categorias nestes:

| Nome de stencil                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Processo** | Aplicações, plug-ins do browser, threads, máquinas virtuais |
| **Interactor externo** | Fornecedores de autenticação, dos navegadores, os utilizadores, aplicações web |
| **Arquivo de dados** | Cache, armazenamento, ficheiros de configuração, bases de dados, registo |
| **Fluxo de dados** | Binário, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, com o nome de pipe, RPC/DCOM, SMB, UDP |
| **Limites de linha/limite de fidedignidade** | Redes empresariais, internet, máquina, sandbox, de modo kernel/utilizador |

### <a name="notesmessages"></a>Notas/mensagens

| Componente                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Mensagens** | Lógica de ferramenta interno que os utilizadores de alertas sempre que há um erro, como não existem fluxos de dados entre elementos. |
| **Notas** | Notas manuais são adicionadas ao ficheiro por equipas de engenharia em toda a estrutura e rever o processo. |

### <a name="element-properties"></a>Propriedades de elemento

Propriedades de elemento variam consoante os elementos que selecionar. Para além dos limites de fidedignidade, todos os outros elementos contenham três seleções gerais:

| Propriedade de elemento                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Nome** | Útil para os processos, arquivos, interactors e fluxos de atribuição de nomes, de modo a que está a facilmente reconhecidas. |
| **Fora do âmbito** | Se selecionado, o elemento é retirado da matriz de geração de ameaça (não recomendado). |
| **Razão para fora do âmbito** | Foi selecionado o campo de justificação para permitir que os utilizadores saber porquê fora do âmbito. |

As propriedades são alteradas em cada categoria de elemento. Selecione cada elemento inspecionar as opções disponíveis. Ou pode abrir o modelo para obter mais informações. Vamos rever as funcionalidades.

## <a name="welcome-screen"></a>Ecrã de boas-vindas

Quando abre a aplicação, consulte o **boas-vindas** ecrã.

### <a name="open-a-model"></a>Abra um modelo

Coloque o cursor sobre **abra um modelo** duas opções de revela: **aberta de neste computador** e **aberta do OneDrive**. O primeiro abre-se de opção de **ficheiro aberto** ecrã. A segunda opção orienta-o processo de início de sessão para o OneDrive. Após a autenticação com êxito, pode selecionar ficheiros e pastas.

![Modelo aberto](./media/azure-security-threat-modeling-tool/openmodel.png)

![Abra do computador ou do OneDrive](./media/azure-security-threat-modeling-tool/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Comentários, problemas e sugestões

Quando seleciona **comentários, problemas e sugestões**, vá para o Fórum do MSDN para SDL ferramentas. Pode ler que outras pessoas são indicar sobre a ferramenta, incluindo soluções e ideias de novo.

![Comentários](./media/azure-security-threat-modeling-tool/feedback.png)

## <a name="design-view"></a>Vista de estrutura

Quando abrir ou criar um novo modelo, o **Design** ver abre.

### <a name="add-elements"></a>Adicionar elementos

Pode adicionar elementos na grelha de duas formas:

- **Arrastar e largar**: arraste o elemento pretendido à grelha. Em seguida, utilize as propriedades de elemento para fornecer informações adicionais.
- **Clique com botão direito**: com o botão direito em qualquer lugar na grelha e selecione os itens no menu pendente. Será apresentada uma representação genérica do elemento que selecionar no ecrã.

### <a name="connect-elements"></a>Ligar elementos

Pode ligar-se de elementos de duas formas:

- **Arrastar e largar**: arraste o fluxo de dados pretendido para a grelha e ligue-se ambas as extremidades para os elementos adequados.
- **Clique em + deslocar**: clique o primeiro elemento (enviar dados), prima e mantenha premida a tecla Shift e, em seguida, selecione o segundo elemento (receber dados). Clique com botão direito e selecione **Connect**. Se utilizar um fluxo de dados bidirecional, a ordem não é tão importante.

### <a name="properties"></a>Propriedades

 Para ver as propriedades que podem ser modificadas no stencils, selecionadas o stencil e as informações preenche em conformidade. O exemplo seguinte mostra antes e após um **base de dados** stencil é de arrastar o diagrama:

#### <a name="before"></a>Antes de

![Antes de](./media/azure-security-threat-modeling-tool/properties1.png)

#### <a name="after"></a>Após

![Após](./media/azure-security-threat-modeling-tool/properties2.png)

### <a name="messages"></a>Mensagens

Se criar um modelo de ameaça e se esqueça de ligar os fluxos de dados para elementos, receberá uma notificação. Pode ignorar a mensagem ou, pode seguir as instruções para corrigir o problema. 

![Mensagens](./media/azure-security-threat-modeling-tool/messages.png)

### <a name="notes"></a>Notas

Para adicionar notas para o diagrama, mudar do **mensagens** separador para o **notas** separador.

## <a name="analysis-view"></a>Vista de análise

Depois de criar o diagrama, selecione o **Analysis** símbolo (Lupa) na barra de ferramentas atalhos para mudar para o **Analysis** vista.

![Vista de análise](./media/azure-security-threat-modeling-tool/analysisview.png)

### <a name="generated-threat-selection"></a>Seleção de ameaça gerado

Quando seleciona uma ameaça, pode utilizar três funções distintas:

| Funcionalidade                               | Informações      |
| --------------------------------------- | ------------ |
| **Indicador de leitura** | <p>A ameaça está marcada como leitura, o que ajuda a manter um registo dos itens que revisto.</p><p>![Indicador unread/leitura](./media/azure-security-threat-modeling-tool/readmode.png)</p> |
| **Foco de interação** | <p>Interação no diagrama que pertence a uma ameaça é realçada.</p><p>![Foco de interação](./media/azure-security-threat-modeling-tool/interactionfocus.png)</p> |
| **Propriedades de ameaça** | <p>Informações adicionais sobre a ameaça aparecem no **ameaça propriedades** janela.</p><p>![Propriedades de ameaça](./media/azure-security-threat-modeling-tool/threatproperties.png)</p> |

### <a name="priority-change"></a>Alteração da prioridade

Pode alterar o nível de prioridade de cada ameaça gerado. Cores diferentes tornam mais fácil identificar alta, média-e ameaças de prioridade baixa.

![Alteração da prioridade](./media/azure-security-threat-modeling-tool/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Campos de editáveis de propriedades de ameaça

Como mostrado na imagem anterior, pode alterar as informações geradas pela ferramenta. Também pode adicionar informações a determinados campos, tais como justificação. Estes campos são gerados pelo modelo. Se precisar de mais informações para cada ameaça, pode efetuar as modificações.

![Propriedades de ameaça](./media/azure-security-threat-modeling-tool/threatproperties.png)

## <a name="reports"></a>Relatórios

Depois de concluir a alteração prioridades e atualizar o estado de cada ameaça gerado, pode guardar o ficheiro e/ou imprimir um relatório. Aceda a **relatório** > **criar relatório completo**. Nome do relatório e deverá ver algo semelhante a imagem seguinte:

![Relatório](./media/azure-security-threat-modeling-tool/report.png)

## <a name="next-steps"></a>Passos seguintes

* Para contribuir com um modelo para a Comunidade, aceda ao nosso [GitHub](https://github.com/Microsoft/threat-modeling-templates) página. 
* Para começar a utilizar com a ferramenta, vá para o [transferir](https://aka.ms/tmtpreview) página.
