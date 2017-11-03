---
title: "Galleries módulos e Runbooks de automatização do Azure | Microsoft Docs"
description: "Os Runbooks e módulos da Microsoft e a Comunidade estão disponíveis para instalar e utilizar no seu ambiente de automatização do Azure.  Este artigo descreve como pode aceder a estes recursos e para contribuir com os runbooks para a galeria."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: d3fee7b4-630a-4c10-8425-9bf51d7c9e58
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte;bwren
ms.openlocfilehash: e46c09a0cfecf07514f85b5801129fbd8162510f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galleries módulos e Runbooks de automatização do Azure
Em vez de criar os seus próprios runbooks e módulos na automatização do Azure, pode aceder a uma variedade de cenários que já ter sido criadas pela Microsoft e a Comunidade.  Pode utilizar estes cenários sem modificações ou pode utilizá-los como um ponto de partida e editá-los para os seus requisitos específicos.

Pode obter runbooks a [Galeria de Runbooks](#runbooks-in-runbook-gallery) e módulos do [galeria do PowerShell](#modules-in-powerShell-gallery).  Também pode contribuir para a Comunidade através da partilha de cenários que desenvolver.

## <a name="runbooks-in-runbook-gallery"></a>Runbooks na Galeria de Runbooks
O [Galeria de Runbooks](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=RootCategory&f\[0\].Value=WindowsAzure&f\[1\].Type=SubCategory&f\[1\].Value=WindowsAzure_automation&f\[1\].Text=Automation) proporciona uma variedade de runbooks da Microsoft e da Comunidade que pode importar para a automatização do Azure. Pode optar por transferir um runbook da galeria do que está alojada no [Centro de scripts do TechNet](https://gallery.technet.microsoft.com/scriptcenter/site/upload), ou diretamente pode importar runbooks a partir da Galeria a partir do portal clássico do Azure ou o portal do Azure.

Só é possível importar diretamente a partir da galeria do Runbook utilizando o portal clássico do Azure ou o portal do Azure. Não é possível executar esta função com o Windows PowerShell.

> [!NOTE]
> Deve validar os conteúdos de todos os runbooks que aproveitar na Galeria de Runbooks e tenha muito cuidado na instalação e executá-los num ambiente de produção. |
> 
> 

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-classic-portal"></a>Para importar um runbook da galeria do Runbook com o portal clássico do Azure
1. No portal do Azure, clique em, **novo**, **serviços aplicacionais**, **automatização**, **Runbook**, **da galeria**.
2. Selecione uma categoria para ver os runbooks relacionados e selecione um runbook para ver os detalhes. Quando seleciona o runbook que pretende, clique no botão de seta para a direita.
   
    ![Galeria de runbooks](media/automation-runbook-gallery/runbook-gallery.png)
3. Reveja o conteúdo do runbook e tenha em atenção quaisquer requisitos na descrição. Quando tiver terminado, clique no botão de seta para a direita.
4. Introduza os detalhes de runbook e, em seguida, clique no botão de marca de verificação. O nome do runbook é já preenchido.
5. O runbook é apresentado no **Runbooks** separador para a conta de automatização.

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Para importar um runbook da galeria do Runbook com o portal do Azure
1. No portal do Azure, abra a sua conta da Automatização.
2. Clique no mosaico **Runbooks** para abrir a lista de runbooks.
3. Clique em **procurar galeria** botão.
   
    ![Galeria botão Procurar](media/automation-runbook-gallery/browse-gallery-button.png)
4. Localize o item de galeria que pretende e selecione-o para ver os respectivos detalhes.
   
    ![Procurar da Galeria](media/automation-runbook-gallery/browse-gallery.png)
5. Clique em **projeto de fonte de vista** para ver o item no [Centro de scripts do TechNet](http://gallery.technet.microsoft.com/).
6. Para importar um item, clique na mesma para ver os respectivos detalhes e, em seguida, clique em de **importar** botão.
   
    ![Botão Importar](media/automation-runbook-gallery/gallery-item-detail.png)
7. Opcionalmente, altere o nome do runbook e, em seguida, clique em **OK** para importar o runbook.
8. O runbook é apresentado no **Runbooks** separador para a conta de automatização.

### <a name="adding-a-runbook-to-the-runbook-gallery"></a>A adição de um runbook na Galeria de runbooks
Microsoft encoraja adicionar runbooks na Galeria de Runbooks que pensa que seria útil para outros clientes.  Pode adicionar um runbook pelo [carregá-lo para o Centro de scripts](http://gallery.technet.microsoft.com/site/upload) tendo em conta os seguintes detalhes.

* Tem de especificar *do Windows Azure* para o **categoria** e *automatização* para o **subcategoria** para o runbook que será apresentado no assistente.  
* O carregamento tem de ser um único ficheiro. ps1 ou. graphrunbook.  Se o runbook necessitar de todos os módulos, os runbooks subordinados ou ativos, em seguida, deve listar os na descrição da submissão e na secção de comentários do runbook.  Se tiver um cenário que necessitam de vários runbooks, em seguida, carregar cada separadamente e lista os nomes dos runbooks relacionados em cada um das respetivas descrições. Certifique-se de que utiliza as mesmas etiquetas para que apareçam na mesma categoria. Um utilizador tem de ler a descrição para saber que outros runbooks não são necessários o cenário de trabalho.
* Adicione a etiqueta "GraphicalPS" se está a publicar um **runbook gráfico** (não um fluxo de trabalho gráfico). 
* Inserir uma PowerShell ou o fluxo de trabalho do PowerShell fragmento de código para utilizar a descrição **inserir a secção de código** ícone.
* O resumo para o carregamento é apresentado nos resultados da Galeria de Runbooks, pelo que deve fornecer informações detalhadas que o ajuda a um utilizador identificar a funcionalidade do runbook.
* Deve atribuir a uma a três das etiquetas seguintes para o carregamento.  O runbook está listado no assistente em categorias que corresponde ao respetivas etiquetas.  As etiquetas não nesta lista são ignoradas pelo assistente. Se não especificar as etiquetas correspondentes, o runbook está listado em outra categoria.
  
  * Cópia de segurança
  * Gestão de Capacidade
  * Controlo de alterações
  * Conformidade
  * Dev / teste ambientes
  * Recuperação Após Desastre
  * Monitorização
  * Aplicação de patches
  * Aprovisionamento
  * Remediação
  * Gestão de ciclo de vida VM
* Automatização de atualizações de Galeria de uma vez uma hora, pelo que não verão as suas contribuições imediatamente.

## <a name="modules-in-powershell-gallery"></a>Módulos na galeria do PowerShell
Módulos do PowerShell contêm cmdlets que pode utilizar nos runbooks e módulos existentes que pode instalar na automatização do Azure estão disponíveis no [galeria do PowerShell](http://www.powershellgallery.com).  Pode iniciar esta galeria a partir do portal do Azure e instalá-los diretamente na automatização do Azure ou pode transferi-las e instalá-los manualmente.  Não é possível instalar os módulos diretamente a partir do portal clássico do Azure, mas pode transferi-los instalá-los tal como faria com qualquer outro módulo.

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Para importar um módulo a partir da galeria do módulo de automatização com o portal do Azure
1. No portal do Azure, abra a sua conta da Automatização.
2. Selecione **módulos** em **recursos partilhados** para abrir a lista de módulos.
4. Clique em **procurar galeria** da parte superior da página.
   
    ![Galeria de módulo](media/automation-runbook-gallery/modules-blade.png) <br>
5. No **procurar galeria** página, pode procurar pelos seguintes campos:
   
   * Nome do módulo
   * Etiquetas
   * Autor
   * Nome do cmdlet/DSC de recursos
6. Localize um módulo que está interessado em e selecione-o para ver os respectivos detalhes.  
   Quando Pormenorize um módulo específico, pode ver mais informações sobre o módulo, incluindo uma ligação para a galeria do PowerShell, necessárias dependências e todos os cmdlets e/ou recursos de DSC que contém o módulo.
   
    ![Detalhes de módulos do PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png) <br>
7. Para instalar o módulo diretamente na automatização do Azure, clique o **importação** botão.
   
    ![Botão de módulo de importar](media/automation-runbook-gallery/module-import-button.png)
8. Quando clica no botão Importar, no **importar** painel, pode ver o nome do módulo que está prestes a importar. Se a todas as dependências se encontram instaladas, o **OK** botão está ativado. Se tiver dependências em falta, terá de importar os antes de importar este módulo.
9. Clique em **OK** para importar o módulo. Enquanto a automatização do Azure importa um módulo à sua conta, extrai metadados sobre o módulo e os cmdlets.
   
    ![Página de módulos de importação](media/automation-runbook-gallery/module-import-blade.png)
   
    Esta operação pode demorar alguns minutos, uma vez que cada atividade tem de ser extraído.
10. Receberá uma notificação inicial que o módulo está a ser implementado e a notificação de outra quando foi concluída.
11. Depois do módulo é importado, pode ver as atividades disponíveis e pode utilizar os respetivos recursos nos seus runbooks e a configuração de estado pretendido.

## <a name="requesting-a-runbook-or-module"></a>Pedir um runbook ou o módulo
Pode enviar pedidos para [voz do utilizador](https://feedback.azure.com/forums/246290-azure-automation/).  Se precisar de ajudar a escrever um runbook ou tem alguma pergunta sobre o PowerShell, publique uma pergunta a nossa [fórum](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Passos Seguintes
* Para começar com runbooks, consulte o artigo [criar ou importar um runbook na automatização do Azure](automation-creating-importing-runbook.md)
* Para compreender as diferenças entre o PowerShell e o fluxo de trabalho do PowerShell com runbooks, consulte [fluxo de trabalho do PowerShell de aprendizagem](automation-powershell-workflow.md)

