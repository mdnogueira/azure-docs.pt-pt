---
title: "A extensão do painel de acesso do Azure de resolução de problemas do IE | Microsoft Docs"
description: "Como utilizar a política de grupo para implementar o suplemento do Internet Explorer para o portal de aplicações My."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: f56b3230-26fd-42ec-9e3d-2c12daf15479
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2a4c3b69a9ab31e6382cb5cf863101861fa2a78e
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>A extensão do painel de acesso de resolução de problemas para o Internet Explorer
Este artigo ajuda-o a resolver os problemas seguintes:

* É possível aceder às suas aplicações através do portal do meu aplicações ao utilizar o Internet Explorer.
* Consulte a mensagem "Instalação de Software", apesar de já tiver instalado o software.

Se for um administrador, consulte também: [como implementar a extensão do painel de acesso para o Internet Explorer utilizando a política de grupo](active-directory-saas-ie-group-policy.md)

## <a name="run-the-diagnostic-tool"></a>Execute a ferramenta de diagnóstico
Pode diagnosticar problemas de instalação com a extensão do painel de acesso ao descarregar e executar a ferramenta de diagnóstico do painel de acesso:

1. [Clique aqui para transferir a ferramenta de diagnóstico.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
2. Abra o ficheiro e prima **extrair todos os** botão.
   
    ![Prima extrair todos](./media/active-directory-saas-ie-troubleshooting/extract1.png)
3. Em seguida, prima a **extrair** botão para continuar.
   
    ![Prima Extract](./media/active-directory-saas-ie-troubleshooting/extract2.png)
4. Para executar a ferramenta, clique no ficheiro denominado **AccessPanelExtensionDiagnosticTool**, em seguida, selecione **abrir com > Microsoft Windows baseado em Script Host**.
   
    ![Abrir com > Microsoft Windows baseado em anfitrião de Script](./media/active-directory-saas-ie-troubleshooting/open_tool.png)
5. Em seguida, verá a janela de diagnóstico seguinte, que descreve o que poderá estar errado com a instalação.
   
    ![Um exemplo da janela de diagnóstico](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)
6. Clique em "**Sim**" para permitir que o programa de corrigir os problemas que foram encontrados.
7. Para guardar estas alterações, feche cada janela do Internet Explorer e, em seguida, abra o Internet Explorer novamente.<br />Se ainda não é possível aceder as aplicações, tente os passos abaixo.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Verifique que a extensão do painel de acesso está ativada
Para verificar que a extensão do painel de acesso está ativada no Internet Explorer:

1. No Internet Explorer, clique em de **ícone engrenagem** no canto superior direito da janela. Em seguida, selecione **opções da Internet**.<br />(Em versões mais antigas do Internet Explorer, pode encontrá-lo na **ferramentas > Opções da Internet**.
   
    ![Aceda a ferramentas > Opções da Internet](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)
2. Clique em de **programas** separador, em seguida, clique em de **gerir suplementos** botão.
   
    ![Clique em Gerir suplementos](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)
3. Nesta caixa de diálogo Selecionar **extensão do painel de acesso** e, em seguida, clique em de **ativar** botão.
   
    ![Clique em ativar](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)
4. Para guardar estas alterações, feche cada janela do Internet Explorer e, em seguida, abra o Internet Explorer novamente.

## <a name="enable-extensions-for-inprivate-browsing"></a>Ativar extensões para navegação InPrivate
Se estiver a utilizar o modo de Navegação InPrivate:

1. No Internet Explorer, clique em de **ícone engrenagem** no canto superior direito da janela. Em seguida, selecione **opções da Internet**.<br />(Em versões mais antigas do Internet Explorer, pode encontrá-lo na **ferramentas > Opções da Internet**.
   
    ![Um exemplo da janela de diagnóstico](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)
2. Vá para o **privacidade** separador, em seguida, **desmarque** a caixa de verificação com etiqueta **desativar barras de ferramentas e extensões quando inicia a Navegação InPrivate**</p>
   
    ![Desmarque desativar barras de ferramentas e extensões quando inicia a Navegação InPrivate](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)
3. Para guardar estas alterações, feche cada janela do Internet Explorer e, em seguida, abra o Internet Explorer novamente.

## <a name="uninstall-the-access-panel-extension"></a>Desinstalar a extensão do painel de acesso
Para desinstalar a extensão do painel de acesso a partir do seu computador:

1. No seu teclado, prima a **tecla Windows** para abrir o menu Iniciar. Quando o menu está aberto, pode escrever qualquer coisa a fazer uma pesquisa. Escreva "Painel de controlo" e, em seguida, abra o **painel de controlo** quando for apresentada nos resultados da pesquisa.
   
    ![Pesquisa para o painel de controlo](./media/active-directory-saas-ie-troubleshooting/search_sm.png)
2. No canto superior direito do painel de controlo, altere o **ver** opção para **ícones grandes**. Em seguida, localize e clique o **programas e funcionalidades** botão.
   
    ![Controlo de vista para mostrar ícones grandes](./media/active-directory-saas-ie-troubleshooting/control_panel.png)
3. Na lista, selecione **extensão do painel de acesso**e clique em de **desinstalação** botão.
   
    ![Clique em desinstalar](./media/active-directory-saas-ie-troubleshooting/uninstall.png)
4. Pode, em seguida, tentar instalar a extensão novamente para ver se o problema foi resolvido.

Se ocorrerem problemas desinstalar a extensão, pode também removê-la utilizando o [Microsoft corrigir-](https://go.microsoft.com/?linkid=9779673) ferramenta.

## <a name="related-articles"></a>Artigos relacionados
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
* [Acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Como implementar a extensão do painel de acesso para o Internet Explorer utilizando a política de grupo](active-directory-saas-ie-group-policy.md)

