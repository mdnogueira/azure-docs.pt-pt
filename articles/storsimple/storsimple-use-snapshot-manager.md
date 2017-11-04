---
title: Interface de utilizador do Snapshot Manager do StorSimple | Microsoft Docs
description: "Descreve a interface de utilizador do Snapshot Manager do StorSimple e explica como utilizá-la para gerir as tarefas de cópia de segurança e o catálogo de cópias de segurança."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.custom: 
ms.openlocfilehash: b48c507e38eb7cadff56259f617e336e4efe5708
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Interface de utilizador do Snapshot Manager do StorSimple utilizado para gerir as tarefas de cópia de segurança e o catálogo de cópias de segurança

## <a name="overview"></a>Descrição geral
O Snapshot Manager do StorSimple tem uma interface de utilizador intuitiva que pode utilizar a utilizar e gerir cópias de segurança. Este tutorial fornece uma introdução para a interface de utilizador e, em seguida, explica como utilizar cada uma dos componentes. Para obter uma descrição detalhada do Snapshot Manager do StorSimple, consulte [Novidades Snapshot Manager do StorSimple?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Descrição de consola
Para ver a interface de utilizador, clique no ícone de Snapshot Manager do StorSimple no seu ambiente de trabalho. Aparece a janela de consola, conforme mostrado na ilustração seguinte.

![Painéis do Snapshot Manager do StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

A janela de consola tem cinco elementos principais. Clique na hiperligação adequada para uma descrição completa de cada elemento.

* [Barra de menus](#menu-bar) 
* [Barra de ferramentas](#tool-bar) 
* [Painel de âmbito](#scope-pane) 
* [Painel de resultados](#results-pane) 
* [Painel de ações](#actions-pane) 

Além disso, o Snapshot Manager do StorSimple suporta [navegação e um número de atalhos de teclado](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Acessibilidade de consola
A interface de utilizador do Snapshot Manager do StorSimple suporta as funcionalidades de acessibilidade fornecidas pelo sistema operativo Windows e a consola de gestão da Microsoft (MMC), bem como algumas atalhos de teclado Snapshot Manager do StorSimple específicas. 

* Para obter uma descrição das funcionalidades de acessibilidade do Windows, aceda a [atalhos de teclado para o Windows](https://support.microsoft.com/kb/126449). 
* Para obter uma descrição das funcionalidades de acessibilidade MMC, aceda a [acessibilidade para MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)
* Para obter uma descrição das funcionalidades de acessibilidade Snapshot Manager do StorSimple, aceda a [navegação e atalhos de teclado](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Barra de menus
Barra de menus na parte superior da janela da consola contém [ficheiro](#file-menu), [ação](#action-menu), [vista](#view-menu), [Favoritos](#favorites-menu), [janela](#window-menu), e [ajudar](#help-menu) menus.

Clique em qualquer item na barra de menus para ver uma lista de comandos disponíveis nesse menu. O seguinte exemplo mostra o **vista** menu selecionado na barra de menus.

![Menu Ver selecionado](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Menu Ficheiro
O **ficheiro** menu contém comandos padrão de consola de gestão da Microsoft (MMC).

#### <a name="menu-access"></a>Acesso ao menu
Para ver o **ficheiro** menu, clique em **ficheiro** na barra de menus. É apresentado o menu seguinte.

![Menu ficheiro Snapshot Manager do StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Descrição de menu
A tabela seguinte descreve os itens que aparecem no **ficheiro** menu.

| Item de menu | Descrição |
|:--- |:--- |
| novo |Clique em **novo** para criar uma nova consola com base no Snapshot Manager do StorSimple. |
| Aberto |Clique em **abrir** para abrir uma consola existente. |
| Guardar |Clique em **guardar** para guardar a consola atual. |
| Guardar como |Clique em **guardar como** para criar uma instância nova, cujo nome foi alterada da consola atual. Utilize o **guardar como** opção Personalizar uma vista e guardá-lo para obtenção posterior. Por exemplo, pode criar Snapshot Manager do StorSimple snap-ins que apontam para servidores específicos. |
| Adicionar/Remover Snap-in |Clique em **Adicionar/Remover Snap-in** para adicionar ou remover snap-ins e para organizar os nós no **âmbito** painel. Para obter mais informações, aceda a [adicionar, remover, Organize Snap-ins e as extensões e na MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Opções |Clique em **opções** para alterar o ícone de consola, especifique modos de acesso de utilizador e as permissões ou eliminar ficheiros da consola para aumentar o espaço em disco disponível. |
| Lista de caminhos de ficheiro |Clique num caminho da lista numerado para reabrir um ficheiro que recentemente aberta. |
| Saída |Clique em **saída** para fechar o **ficheiro** menu. |

### <a name="action-menu"></a>Menu ação
Utilize o **ação** menu para selecionar de entre as ações disponíveis. Os itens disponíveis dependem da seleção que tomar no **âmbito** painel ou **resultados** painel.

#### <a name="menu-access"></a>Acesso ao menu
Para ver o **ação** menu, efetue um dos seguintes:

* Faça duplo clique num item de **âmbito** painel ou **resultados** painel.
* Selecione um item no **âmbito** painel ou **resultados** painel e, em seguida, clique em **ação** na barra de menus. 

Por exemplo, se selecionar o nó superior a **âmbito** painel e, em seguida, rato ou clique em **ação** na barra de menus, é apresentado o menu seguinte.

![Menu ação Snapshot Manager do StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

O **ações** painel (no lado direito da consola) contém a mesma lista de ações como o **ação** menu. Além disso, o **ações** painel contém o **vista** opções de menu que permitem-lhe criar uma vista personalizada do **resultados** painel.

![Abra o painel de ações com o menu Ver](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Descrição de menu
A tabela seguinte contém uma lista alfabética de ações de Snapshot Manager do StorSimple. 

* O **ação** coluna apresenta uma lista de ações que pode efetuar em nós e os resultados. 
* O **navegação** coluna explica como apresentar apropriados **ação** menu, de modo a que possa selecionar a ação. Algumas ações aparecem no múltiplo **ação** menus. Para estas ações, selecione um **navegação** opção da lista com marcas. 
* O **Descrição** coluna descreve como utilizar cada ação no **ação** menu ou painel de ações e explica o que faz.

> [!NOTE]
> O **ações** painel e **ação** menus contém opções adicionais, tais como **vista**, **nova janela a partir daqui**,  **Atualizar**, **exportar lista**, e **ajudar**. Estas opções estão disponíveis como parte da MMC e não são específicas para Snapshot Manager do StorSimple. A tabela inclui descrições destas opções.
> 
> 

| Ação | Navegação | Descrição |
|:--- |:--- |:--- |
| Autenticar |Clique em de **dispositivos** nó e faça duplo clique num dispositivo a **resultados** painel. |Clique em **autenticar** para introduzir a palavra-passe que configurou para o dispositivo. |
| Clone |Expanda **catálogo de cópias de segurança**, expanda **instantâneos de nuvem**, clique uma cópia de segurança dated e, em seguida, selecione um volume no **resultados** painel. |Clique em **Clone** para criar uma cópia de um instantâneo de nuvem e armazená-las numa localização que designar. |
| Configurar um dispositivo |Clique com botão direito do **dispositivos** nós. |Clique em **configurar um dispositivo** para configurar um único ou vários dispositivos para ligar para o anfitrião do Windows. |
| Criar política de cópia de segurança |Efetue um dos seguintes:<ul><li>Clique com botão direito **políticas de cópia de segurança**.</li><li>Clique em ou expanda **Volume grupos**e, em seguida, clique com botão direito um grupo de volume.</li><li>Clique em ou expanda **catálogo de cópia de segurança**e, em seguida, clique com botão direito um grupo de volume.</li></ul> |Clique em **criar política de cópia de segurança** para configurar uma cópia de segurança agendada para um grupo de volume. |
| Criar grupo de Volume |Efetue um dos seguintes:<ul><li>Clique em de **Volumes** nó e, em seguida, faça duplo clique num volume no **resultados** painel.</li><li>Clique com botão direito do **Volume grupos** nós.</li></ul> |Clique em **criar grupo de Volume** para atribuir volumes para um grupo de volume. |
| Eliminar |Clique num nó ou resultado (este item é apresentado em muitas **ação** menus e **ações** painéis.) |Clique em **eliminar** para eliminar o nó ou o resultado que selecionou. Quando for apresentada a caixa de diálogo de confirmação, confirmar ou cancelar a eliminação. |
| Detalhes |Clique em de **dispositivos** nó e, em seguida, faça duplo clique num dispositivo a **resultados** painel. |Clique em **detalhes** para ver os detalhes de configuração para um dispositivo. |
| Editar |Clique em **políticas de cópia de segurança**e, em seguida, faça duplo clique uma política no **resultados** painel. |Clique em **editar** para alterar a agenda de cópia de segurança para um grupo de volume. |
| Lista de exportação |Clique em qualquer nó ou resultado (este item é apresentado em todos os **ação** menus e **ações** painéis.) |Clique em **exportar lista** guardar uma lista de um ficheiro de valores separados por vírgulas (CSV). Em seguida, pode importar este ficheiro para uma aplicação de folha de cálculo para análise. |
| Ajuda |Clique em qualquer nó ou o resultado. (Este item é apresentado em todos os **ação** menus e **ações** painéis.) |Clique em **ajudar** para abrir a ajuda online numa janela do browser separados. |
| Nova janela a partir daqui |Clique em qualquer nó ou resultado (este item é apresentado em todos os **ação** menus e **ações** painéis.) |Clique em **nova janela a partir daqui** para abrir uma nova janela do Snapshot Manager do StorSimple. |
| Atualizar |Clique em qualquer nó ou resultado (este item é apresentado em todos os **ação** menus e **ações** painéis.) |Clique em **atualizar** para atualizar a janela de Snapshot Manager do StorSimple atualmente apresentada. |
| Atualizar o dispositivo |Clique em de **dispositivos** nó e faça duplo clique num dispositivo a **resultados** painel. |Clique em **atualizar dispositivo** para sincronizar um dispositivo específico ligado com o Snapshot Manager do StorSimple. |
| Atualizar dispositivos |Clique com botão direito do **dispositivos** nós. |Clique em **atualizar dispositivos** para sincronizar a lista de dispositivos ligados com o Snapshot Manager do StorSimple. |
| Reanalisar volumes |Clique com botão direito do **Volumes** nós. |Clique em **reanalisar volumes** para atualizar a lista de volumes que aparece no **resultados** painel. |
| Restauro |Expanda **catálogo de cópias de segurança**, expandir um grupo de volume, expanda **instantâneos locais** ou **instantâneos de nuvem**e, em seguida, faça duplo clique uma cópia de segurança. |Clique em **restaurar** para substituir os dados de grupo atual do volume com os dados da cópia de segurança selecionado. |
| Efetuar cópia de segurança |Efetue um dos seguintes:<ul><li>Expanda **Volume grupos**e, em seguida, clique com botão direito um grupo de volume.</li><li>Expanda **catálogo de cópia de segurança**e, em seguida, clique com botão direito um grupo de volume.</li></ul> |Clique em **efetuar cópia de segurança** para iniciar uma tarefa de cópia de segurança imediatamente. |
| Apresentação de importações Ativar/desativar |Clique com o botão direito no nó superior no **âmbito** painel (o **Snapshot Manager do StorSimple** nó nos exemplos). |Clique em **alternar importações apresentar** para mostrar ou ocultar os grupos de volume e cópias de segurança associadas que foram importadas a partir do dashboard de serviço do Gestor de dispositivos do StorSimple. |

### <a name="view-menu"></a>Menu Ver
Utilize o **vista** menu para criar uma vista personalizada do **resultados** conteúdo do painel. O **vista** menu contém **Adicionar/remover colunas** e **personalizar** opções.

#### <a name="menu-access"></a>Acesso ao menu
Pode aceder a **vista** menu no menu da barra ou no **ações** painel.

![Menu Ver Snapshot Manager do StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Descrição de menu
A tabela seguinte descreve os itens que aparecem no **vista** menu.

| Item de menu | Descrição |
|:--- |:--- |
| Adicionar/remover colunas |Clique em **Adicionar/remover colunas** para adicionar ou remover colunas no **resultados** painel. |
| Personalizar |Clique em **personalizar** para mostrar ou ocultar itens na janela da consola Snapshot Manager do StorSimple. |

### <a name="favorites-menu"></a>Menu de Favoritos
Utilize o **Favoritos** menu para adicionar, remover e organizar vistas de página e tarefas que utiliza com frequência. 

#### <a name="menu-access"></a>Acesso ao menu
Pode aceder a **Favoritos** menu na barra de menus.

![Menu de Favoritos do Snapshot Manager do StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Descrição de menu
A tabela seguinte descreve os itens que aparecem no **Favoritos** menu.

| Item de menu | Descrição |
|:--- |:--- |
| Adicionar aos Favoritos |Clique em **adicionar aos Favoritos** para adicionar vista atual à sua lista de Favoritos. |
| Organizar Favoritos |Clique em **organizar Favoritos** para organizar os conteúdos da sua pasta de Favoritos. |

### <a name="window-menu"></a>Menu de janela
Utilize o **janela** menu para adicionar e reorganizar janelas de consola Snapshot Manager do StorSimple.

#### <a name="menu-access"></a>Acesso ao menu
Pode aceder a **janela** menu na barra de menus.

![Menu de janela Snapshot Manager do StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

A lista numerada na parte inferior do menu mostra a abrir o windows que estão atualmente. Clique em qualquer janela essa lista para assegurar a janela de primeiro plano. 

#### <a name="menu-description"></a>Descrição de menu
A tabela seguinte descreve os itens que são apresentados no menu de janela.

| Item de menu | Descrição |
|:--- |:--- |
| Nova janela |Clique em **nova janela** para abrir uma nova janela de consola (para além da janela de existente). |
| Em cascata |Clique em **Cascade** para apresentar as janelas de consola aberta num estilo em cascata. |
| Mosaico horizontalmente |Clique em **mosaico horizontalmente** para apresentar as janelas de consola aberta num formato mosaico (ou grelha). |
| Dispor ícones |Se tiver vários consola windows abrir e dispersos por através de ambiente de trabalho, minimizá-los e, em seguida, clique em **dispor ícones** arranjá-los numa linha horizontal na parte inferior do ecrã. |

### <a name="help-menu"></a>Menu Ajuda
Utilize o **ajudar** menu para ver a ajuda online disponível do Snapshot Manager do StorSimple e a MMC. Também pode ver informações sobre as versões de software do MMC e Snapshot Manager do StorSimple que estão actualmente instalados no seu sistema. 

Pode aceder a **ajudar** menu na barra de menus. Também pode aceder a tópicos de ajuda do Snapshot Manager do StorSimple do **ações** painel.

![Menu Ajuda do Snapshot Manager do StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Descrição de menu
A tabela seguinte descreve os itens que são apresentados no menu Ajuda.

| Item de menu | Descrição |
|:--- |:--- |
| Ajuda sobre o instantâneo do StorSimple Manager |Clique em **ajudar no Snapshot Manager do StorSimple** para abrir a ajuda do Snapshot Manager do StorSimple numa janela separada. |
| Tópicos de ajuda |Clique em **tópicos de ajuda** para abrir a ajuda online do MMC numa janela separada. |
| TechCenter Web Site |Clique em **TechCenter do Web Site** para abrir a home page do Microsoft TechNet Tech Center numa janela separada. |
| Sobre a consola de gestão da Microsoft |Clique em **sobre a consola de gestão do Microsoft** para ver qual a versão da consola de gestão Microsoft está instalada no seu sistema. |
| Sobre o Snapshot Manager do StorSimple |Clique em **sobre Snapshot Manager do StorSimple** para ver qual a versão do snap-in é instalada no seu sistema. |

## <a name="tool-bar"></a>Barra de ferramentas
Barra de ferramentas, localizada abaixo da barra de menu contém ícones de navegação e tarefas. Cada ícone é um atalho para uma tarefa específica.

### <a name="icon-descriptions"></a>Descrições de ícone
A tabela seguinte descreve os ícones que aparecem na barra de ferramentas. 

| Ícone | Descrição |
|:--- |:--- |
| ![Seta para a esquerda](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Clique no ícone de seta para a esquerda para regressar à página anterior. |
| ![Seta para a direita](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Clique na seta à direita para ir para a página seguinte (seta está a cinzento, a ação estiver disponível). |
| ![Ícone de cópia de segurança](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Clique no ícone de cima para ir agregar um nível na árvore da consola (o **âmbito** painel). |
| ![Árvore de consola Mostrar/ocultar](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Clique no ícone de árvore de consola Mostrar/ocultar a mostrar ou ocultar o **âmbito** painel. |
| ![Lista de exportação](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Clique no ícone de lista de exportação para exportar uma lista para um ficheiro CSV que especificar. |
| ![Ícone de ajuda](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Clique no ícone de ajuda para abrir um tópico de ajuda online do MMC. |
| ![Mostrar/Ocultar painel de ações](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Clique em Mostrar/ocultar **ações** ícone de painel para mostrar ou ocultar o **ações** painel. |

## <a name="scope-pane"></a>Painel de âmbito
O **âmbito** painel é o painel mais à esquerda na IU do Snapshot Manager do StorSimple. Contém a árvore de consola (ou o nó) e é o mecanismo de navegação primário para o Snapshot Manager do StorSimple. 

### <a name="scope-pane-structure"></a>Estrutura do painel de âmbito
O **âmbito** painel contém uma série de objetos clicáveis (nós) organizada de uma estrutura de árvore. 

![Painel de âmbito](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Para expandir ou fechar um nó, clique no ícone de seta junto ao nome do nó.
* Para ver o estado ou o conteúdo de um nó, clique no nome de nó. As informações aparecem no **resultados** painel. 

O **âmbito** painel contém os seguintes nós: 

* [Nó dispositivos](#devices-node) 
* [Nó de volumes](#volumes-node) 
* [Nó de grupos de volume](#volume-groups-node) 
* [Nó de políticas de cópia de segurança](#backup-policies-node) 
* [Nó catálogo de cópia de segurança](#backup-catalog-node) 
* [Nó de tarefas](#jobs-node) 

### <a name="scope-pane-tasks"></a>Tarefas do painel de âmbito
Pode utilizar o **âmbito** painel para concluir uma ação num nó específico. Para selecionar uma tarefa, efetue um dos seguintes:

* Faça duplo clique no nó e, em seguida, selecione a tarefa de menu que surgir.
* Clique no nó e, em seguida, clique em **ação** na barra de menus. Selecione a tarefa de menu que surgir.
* Clique no nó e, em seguida, selecione a ação no **ações** painel.

Quando seleciona um nó e utilizar qualquer um dos seguintes métodos para ver uma lista de tarefas, são apresentadas apenas as ações que podem ser executadas nesse nó.

### <a name="devices-node"></a>Nó dispositivos
O **dispositivos** nó representa a dispositivos StorSimple e a dispositivos virtuais StorSimple que estão ligados ao Snapshot Manager do StorSimple. Selecione este nó para ligar e configurar um dispositivo e importar os volumes associados, grupos de volumes e cópias de segurança existentes. Vários dispositivos podem ser ligados a um único anfitrião.

* Para expandir o nó, clique no ícone de seta junto a **dispositivos**.
* Para ver um menu de ações disponíveis, clique com botão direito a **dispositivos** nós ou com o botão direito qualquer um de nós que são apresentados na vista expandida.
* Para ver uma lista de dispositivos configurados, clique em **dispositivos** no **âmbito** painel. A lista de dispositivos, juntamente com informações sobre cada dispositivo, é apresentado no **resultados** painel.

### <a name="volumes-node"></a>Nó de volumes
O **Volumes** nó representa as unidades que correspondem aos volumes montados pelo anfitrião, incluindo as que são detetados através de iSCSI e os que são detetados através de um dispositivo. Utilize este nó para ver a lista de volumes disponíveis e atribuir volumes individuais aos grupos de volume.

* Para expandir o nó, clique no ícone de seta junto a **Volumes**.
* Para ver um menu de ações disponíveis, clique com botão direito a **Volumes** nós ou com o botão direito qualquer um de nós que são apresentados na vista expandida.
* Para ver uma lista de volumes, clique em **Volumes** no **âmbito** painel. A lista de volumes, juntamente com informações sobre cada volume, é apresentado no **resultados** painel.

### <a name="volume-groups-node"></a>Nó de grupos de volume
Os grupos de volume são também denominados grupos de consistência. Cada grupo de volume é um conjunto de volumes relacionado com a aplicação que ajuda a garantir a consistência da aplicação durante as operações de cópia de segurança. Utilize o **Volume grupos** nós para configurar estes grupos e para fazer cópias de segurança interativas ou criar agendas de cópia de segurança. 

* Para expandir o nó, clique no ícone de seta junto a **Volume grupos**.
* Para ver um menu de ações disponíveis, clique com botão direito do **Volume grupos** nó ou clique com botão direito qualquer um de nós que são apresentados na vista expandida.
* Para ver uma lista de grupos de volume, clique em **Volume grupos** no **âmbito** painel. A lista de grupos de volume, juntamente com informações sobre cada grupo de volume, é apresentado no **resultados** painel.

### <a name="backup-policies-node"></a>Nó de políticas de cópia de segurança
As políticas de cópia de segurança são as agendas de tarefas para locais e instantâneos de nuvem. Utilize o **políticas de cópia de segurança** deve ser mantido nó para especificar quanto tempo uma cópia de segurança e a frequência é criada uma cópia de segurança. 

* Para expandir o nó, clique no ícone de seta junto a **políticas de cópia de segurança**.
* Para ver um menu de ações disponíveis, clique com botão direito a **políticas de cópia de segurança** nós ou com o botão direito qualquer um de nós que são apresentados na vista expandida.
* Para ver uma lista das políticas de cópia de segurança, clique em **políticas de cópia de segurança** no **âmbito** painel. A lista de políticas de cópia de segurança, juntamente com informações sobre cada política é apresentada no **resultados** painel.

> [!NOTE]
> Pode manter um máximo de 64 cópias de segurança.


### <a name="backup-catalog-node"></a>Nó catálogo de cópia de segurança
O **catálogo de cópia de segurança** nó contêm listas de cópias de segurança no local e fora das instalações de volumes de Azure StorSimple. Este nó está organizado por grupo de volume e cada contentor de volume do grupo contém estruturas separadas para instantâneos locais (o **instantâneo Local**nó s) e instantâneos de nuvem (o **instantâneos de nuvem** nó). Quando expandido, o contentor do grupo de cada volume lista todas bem-sucedida cópias de segurança que foram executadas de forma interativa ou por uma política configurada.

* Para expandir o nó, clique no ícone de seta junto a **catálogo de cópia de segurança**.
* Para ver um menu de ações disponíveis, clique com botão direito do **catálogo de cópia de segurança** nó ou clique com botão direito qualquer um de nós que são apresentados na vista expandida.
* Para ver uma lista de instantâneos de cópia de segurança, clique em **catálogo de cópias de segurança** no **âmbito** painel. É apresentada a lista de instantâneos, juntamente com informações sobre cada instantâneo no **resultados** painel.

### <a name="local-snapshots-node"></a>Nó de instantâneos local
O **instantâneos locais** apresenta uma lista de nó instantâneos locais para um grupo específico de volume. O nó está localizado o **catálogo de cópia de segurança** no nó de **âmbito** painel. Instantâneos locais são cópias de ponto no tempo dos dados de volumes que estão armazenados no dispositivo StorSimple do Azure. Normalmente, este tipo de cópia de segurança pode ser criado e restaurado rapidamente. Pode utilizar um instantâneo local como faria com uma cópia de segurança local.

* Para expandir o nó, clique no ícone de seta junto a **instantâneos locais**.
* Para ver um menu de ações disponíveis, clique com botão direito a **instantâneos locais** nós ou com o botão direito qualquer um de nós que são apresentados na vista expandida.
* Para ver uma lista de instantâneos locais, clique em **instantâneos locais** no **âmbito** painel. É apresentada a lista de instantâneos, juntamente com informações sobre cada instantâneo no **resultados** painel.

### <a name="cloud-snapshots-node"></a>Nó de instantâneos de nuvem
O **instantâneos de nuvem** nó apresenta uma lista de instantâneos de nuvem para um grupo específico de volume. O nó está localizado o **catálogo de cópia de segurança** no nó de **âmbito** painel. Os instantâneos de nuvem são cópias de ponto no tempo dos dados de volume que estão armazenados na nuvem. Um instantâneo na nuvem é equivalente a um instantâneo replicado num sistema de armazenamento diferentes, fora das instalações. Os instantâneos de nuvem são particularmente útil em cenários de recuperação após desastre.

* Para expandir o nó, clique no ícone de seta junto a **instantâneos de nuvem**.
* Para ver um menu de ações disponíveis, clique com botão direito a **instantâneos de nuvem** nós ou com o botão direito qualquer um de nós que são apresentados na vista expandida.
* Para ver uma lista de instantâneos de nuvem, clique em **instantâneos de nuvem** no **âmbito** painel. É apresentada a lista de instantâneos, juntamente com informações sobre cada instantâneo no **resultados** painel.

### <a name="jobs-node"></a>Nó de tarefas
O **tarefas** nós contém informações sobre tarefas de cópia de segurança agendadas, em execução e recentemente completadas. 

* Para expandir o nó, clique no ícone de seta junto a **tarefas**.
* Para ver um menu de ações disponíveis, clique com botão direito do **tarefas** nó ou clique com botão direito qualquer um de nós que são apresentados na vista expandida.
* Para ver uma lista de tarefas agendadas, expanda o **tarefas** nó e, em seguida, clique em **agendada**. É apresentada a lista de tarefas configuradas anteriormente e informações sobre cada tarefa no **resultados** painel. 
* Para ver uma lista de tarefas recentemente completadas, expanda o **tarefas** nó e, em seguida, clique em **últimas 24 horas**. É apresentada uma lista de tarefas que foram concluídas nas últimas 24 horas no **resultados** painel. O **resultados** painel também contém informações sobre cada tarefa concluída.
* Para ver uma lista de tarefas atualmente em execução, expanda o **tarefas** nó e, em seguida, clique em **executar**. É apresentada a lista de atualmente em execução de tarefas e informações sobre cada tarefa no **resultados** painel.

## <a name="results-pane"></a>Painel de resultados
O **resultados** painel é painel central na IU do Snapshot Manager do StorSimple. Contém listas e informações de estado detalhadas para o nó selecionado no **âmbito** painel.

### <a name="example"></a>Exemplo
Para ver o exemplo seguinte, clique o **Volume grupos** no nó de **âmbito** painel. O **resultados** painel apresenta uma lista de grupos de volume com detalhes sobre cada grupo.

![Painel de resultados](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Pode configurar os detalhes apresentados no **resultados** painel: faça duplo clique num nó no **âmbito** painel, clique em **vista**e, em seguida, clique em **Adicionar/remover colunas** .

## <a name="actions-pane"></a>Painel de ações
O **ações** painel é painel à direita na IU do Snapshot Manager do StorSimple. Contém um menu de operações que pode efetuar no nó, vista ou dados que selecionar no **âmbito** painel ou **resultados** painel. O **ações** painel contém os mesmos comandos o **ação** menus que estão disponíveis para os itens no **âmbito** painel e **resultados** painel. Para obter uma descrição de cada ação, consulte a tabela no **ação** secção de menu.

### <a name="examples"></a>Exemplos
Para ver o exemplo seguinte, no **âmbito** painel, expanda o **tarefas** nó e clique em **agendada**. O **ações** painel apresenta as ações disponíveis para o **agendada** nós.

![Exemplo de tarefas agendadas do painel de ações](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Para ver mais opções, no **âmbito** painel, expanda o **tarefas** nó, clique em **agendada**e, em seguida, clique numa tarefa agendada no **resultados** painel. O **ações** painel apresenta as ações disponíveis para a tarefa agendada, conforme mostrado no exemplo seguinte.

![Exemplo de ações de tarefas do painel de ações](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Navegação do teclado e atalhos
Snapshot Manager do StorSimple permite que as funcionalidades de acessibilidade do sistema operativo Windows e a consola de gestão da Microsoft (MMC). Também inclui algumas funcionalidades de navegação do teclado e atalhos que são específicos para o Snapshot Manager do StorSimple, conforme descrito nas secções seguintes.

* [Chaves de navegação do teclado](#keyboard-navigation-keys) 
* [Menu da barra teclas de atalho](#menu-bar-shortcut-keys) 
* [Teclas de atalho do painel de âmbito](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Chaves de navegação do teclado
A tabela seguinte descreve as chaves que pode utilizar para navegar a interface de utilizador do Snapshot Manager do StorSimple. 

| Chave de navegação | Ação |
|:--- |:--- |
| Premida a tecla de seta |Utilize a tecla de seta para baixo para mover verticalmente para o item seguinte num menu ou painel. |
| Introduza |Prima a tecla Enter para concluir uma ação e, em seguida, avance para o passo seguinte. Por exemplo, pode premir a tecla Enter para selecionar **seguinte**, **OK**, ou **criar**e, em seguida, avance para o próximo passo no Assistente de um. |
| ESC |Prima a tecla Esc para fechar um menu ou para cancelar e fechar uma página. |
| F1 |Prima a tecla F1 para ver um tópico de ajuda para a janela atualmente ativo. |
| F5 |Prima a tecla F5 para atualizar um nó. |
| F6 |Prima a tecla de F6 para mover o **âmbito** painel para o **resultados** painel. |
| F10 |Prima a tecla F10 para aceder à barra de menus. |
| Chave de seta para a esquerda |Utilize a tecla de seta para a esquerda para mover horizontalmente de uma opção de barra de menu para a opção anterior. Ao mover o item anterior na barra de menus, é apresentado o menu ação (ou contexto) para o item anterior. |
| Chave de seta para a direita |Utilize a tecla de seta para a direita para mover horizontalmente da opção de barra de um menu para o seguinte. Ao mover o item seguinte na barra de menus, é apresentado o menu ação (ou contexto) para o novo item. |
| Tecla de tabulação |Utilize a tecla de tabulação para mover para o painel seguinte na consola ou para a caixa de texto ou seleção seguinte numa página. |
| Se a tecla de seta |Utilize a tecla de seta para cima para mover verticalmente ao item anterior num menu ou painel. |

### <a name="menu-bar-shortcut-keys"></a>Menu da barra teclas de atalho
A tabela seguinte descreve as combinações de teclas de atalho para a barra de menu. Depois prima as teclas de atalho e abre o menu, pode utilizar as teclas de atalho do menu (as chaves de sublinhado no menu). Para mais informações sobre a barra de menus, aceda a [barra de menus](#menu-bar).

| Atalho | resultado | Tecla de atalho do menu | resultado |
|:--- |:--- |:--- |:--- |
| ALT + F |Abre o **ficheiro** menu. |N |Abre uma nova instância de consola. |
|  |NÃ |Abre o **ferramentas administrativas** página. | |
|  |S |Guarda a consola do Snapshot Manager do StorSimple. | |
|  |A |Abre o **guardar como** página. | |
|  |M |Abre o **Adicionar/Remover Snap-in** página. | |
|  |P |Abre o **opções** página. | |
|  |H |Abre a ajuda online. | |
| ALT + A |Abre o **ação** menu. |I |Ativa a opção de apresentação de importação e desativar. |
|  |W |Abre uma nova consola Snapshot Manager do StorSimple. | |
|  |F |Atualiza a consola do Snapshot Manager do StorSimple. | |
|  |L |Abre o **exportar lista** página. | |
|  |H |Abre a ajuda online. | |
| ALT + V |Abre o **vista** menu. |A |Abre o **Adicionar/remover colunas** página. |
|  |U |Abre o **Personalizar vista** página. | |
| ALT + O |Abre o **Favoritos** menu. |A |Abre o **adicionar aos Favoritos** página. |
|  |NÃ |Abre o **organizar Favoritos** página. | |
| ALT + W |Abre o **janela** menu. |N |Abre-se a outra janela do Snapshot Manager do StorSimple. |
|  |C |Mostra todas as janelas de consola aberta num estilo em cascata. | |
|  |T |Mostra todas as janelas de consola aberta num padrão de grelha. | |
|  |I |Dispõe os ícones numa linha horizontal na parte inferior do ecrã. | |
| ALT + H |Abre o **ajudar** menu. |H |Abre a ajuda online. |
|  |T |Abre a página web do Microsoft TechNet Tech Center. | |
|  |A |Abre o **sobre a consola de gestão do Microsoft** página. | |

### <a name="scope-pane-shortcut-keys"></a>Teclas de atalho do painel de âmbito
As tabelas seguintes mostram o atalho combinações de teclas para cada nó no **âmbito** painel. 

* [Teclas de atalho do nó dispositivos](#devices-node-shortcut-keys)
* [Teclas de atalho do nó de volumes](#volumes-node-shortcut-keys)
* [Teclas de atalho de nó de grupos de volume](#volume-groups-node-shortcut-keys)
* [Teclas de atalho do nó de políticas de cópia de segurança](#backup-policies-node-shortcut-keys)
* [Teclas de atalho do nó de catálogo de cópia de segurança](#backup-catalog-node-shortcut-keys)
* [Teclas de atalho do nó de tarefas](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Teclas de atalho do nó dispositivos
| Menu de atalho | resultado |
|:--- |:--- |
| C |Abre o **configurar um dispositivo** página. |
| D |Atualiza a lista de dispositivos e detalhes do dispositivo. |
| V |Abre o **vista** menu. |
| W |Abre uma nova consola Snapshot Manager do StorSimple concentra-se no **detalhes** nós. |
| F |Atualiza a consola do Snapshot Manager do StorSimple. |
| L |Abre o **exportar lista** página. |
| H |Abre a ajuda online. |

#### <a name="volumes-node-shortcut-keys"></a>Teclas de atalho do nó de volumes
| Menu de atalho | resultado |
|:--- |:--- |
| V |Atualiza a lista de volumes. |
| V (prima duas vezes) |Abre o **vista** menu. |
| W |Abre uma nova consola Snapshot Manager do StorSimple concentra-se no **Volumes** nós. |
| F |Atualiza a consola do Snapshot Manager do StorSimple. |
| L |Abre o **exportar lista** página. |
| H |Abre a ajuda online. |

#### <a name="volume-groups-node-shortcut-keys"></a>Teclas de atalho de nó de grupos de volume
| Menu de atalho | resultado |
|:--- |:--- |
| G |Abre o **criar um grupo de Volume** página. |
| V |Abre o **vista** menu. |
| W |Abre uma nova consola Snapshot Manager do StorSimple concentra-se no **Volume grupos** nós. |
| F |Atualiza a consola do Snapshot Manager do StorSimple. |
| L |Abre o **exportar lista** página. |
| H |Abre a ajuda online. |

#### <a name="backup-policies-node-shortcut-keys"></a>Teclas de atalho do nó de políticas de cópia de segurança
| Menu de atalho | resultado |
|:--- |:--- |
| B |Abre o **criar uma política** página. |
| V |Abre o **vista** menu. |
| W |Abre uma nova consola Snapshot Manager do StorSimple concentra-se no **Volume grupos** nós. |
| F |Atualiza a consola do Snapshot Manager do StorSimple. |
| L |Abre o * * exportar lista * * página. |
| H |Abre a ajuda online. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Teclas de atalho do nó de catálogo de cópia de segurança
| Menu de atalho | resultado |
|:--- |:--- |
| W |Abre uma nova consola Snapshot Manager do StorSimple concentra-se no **Volume grupos** nós. |
| F |Atualiza a consola do Snapshot Manager do StorSimple. |
| H |Abre a ajuda online. |

#### <a name="jobs-node-shortcut-keys"></a>Teclas de atalho do nó de tarefas
| Menu de atalho | resultado |
|:--- |:--- |
| V |Abre o **vista** menu. |
| W |Abre uma nova consola Snapshot Manager do StorSimple concentra-se no **tarefas** nós. |
| F |Atualiza a consola do Snapshot Manager do StorSimple. |
| L |Abre o **exportar lista** página. |
| H |Abre a ajuda online |

## <a name="next-steps"></a>Passos seguintes
* Saiba como [utilizar Snapshot Manager do StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [utilizar Snapshot Manager do StorSimple para ligar e gerir dispositivos](storsimple-snapshot-manager-manage-devices.md).

