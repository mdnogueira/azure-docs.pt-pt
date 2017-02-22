---
title: Monitorizar a infraestrutura de identidade no local na nuvem.
description: "Esta é a página do Azure AD Connect Health que descreve o que é e porquê utilizá-lo."
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 82798ea6-5cd3-4f30-93ae-d56536f8d8e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: c9ebe21acbe06aa25bcadb3b500c34cf89154388
ms.openlocfilehash: 9abdfa7349bee74b89a23663969904110c730395


---
# <a name="monitor-your-on-premises-identity-infrastructure-and-synchronization-services-in-the-cloud"></a>Monitorizar a infraestrutura de identidade no local e os serviços de sincronização na nuvem
O Azure AD Connect Health ajuda a monitorizar e obter informações sobre a sua infraestrutura de identidade no local, bem como sobre os serviços de sincronização.  Permite-lhe manter uma ligação fiável ao Office 365 e aos Microsoft Online Services ao fornecer capacidades de monitorização para os componentes de identidade chave como Servidores AD FS , servidores do Azure AD Connect (também conhecidos por Motor de Sincronização), Controladores de Domínio do Active Directory e etc. Torna também facilmente acessíveis os pontos de dados chave sobre estes componentes, facilitando a obtenção de informações sobre a utilização e outras informações importantes para tomar decisões informadas.

As informações são apresentadas no [Portal do Azure AD Connect Health](https://aka.ms/aadconnecthealth). Com o Portal do Azure AD Connect Health, pode visualizar alertas, a monitorização do desempenho, a análise de utilização e muito mais. O Azure AD Connect Health ativa a lente única do estado de funcionamento dos componentes de identidade chave, todos num único local.

![O que é o Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnecthealth2.png)

As atualizações futuras do Azure AD Connect Health irão incluir monitorização adicional e informações sobre componentes de identidade adicionais. Fornecer-lhe-ão assim um dashboard único através da lente da identidade, permitindo-lhe ter um ambiente ainda mais robusto, em bom estado de funcionamento e integrado de que os seus utilizadores podem tirar partido para aumentar a sua capacidade para concluir tarefas.

## <a name="why-use-azure-ad-connect-health"></a>Porquê utilizar o Azure AD Connect Health
A integração dos diretórios no local com o Azure AD torna os utilizadores mais produtivos, ao fornecer-lhes uma identidade comum para acederem a recursos na nuvem e no local. No entanto, esta integração acarreta os desafios de garantir que este ambiente está em bom estado, de modo a que os utilizadores possam aceder com fiabilidade aos recursos no local e na nuvem a partir de qualquer dispositivo. O Azure AD Connect Health fornece uma abordagem fácil baseada na nuvem para monitorizar e obter informações acerca da sua infraestrutura de identidade no local que é utilizada para aceder ao Office 365 ou a outras aplicações do Azure AD. É tão simples como instalar um agente em cada um dos servidores de identidade no local.

## <a name="azure-ad-connect-health-for-ad-fsactive-directory-aadconnect-health-adfsmd"></a>[Azure AD Connect Health para AD FS](active-directory-aadconnect-health-adfs.md)
O Azure AD Connect Health para AD FS suporta o AD FS 2.0 no Windows Server 2008 R2 e o AD FS no Windows Server 2012 e Windows Server 2012R2. Suporta também a monitorização de servidores Proxy do AD FS ou de Aplicações Web que fornecem suporte de autenticação para acesso à extranet. Com uma instalação fácil e de baixo custo do agente de estado de funcionamento, o Azure AD Connect Health para AD FS fornece o seguinte conjunto de capacidades principais:

* Monitorização com alertas para saber quando os servidores AD FS e Proxy do AD FS não estão em bom estado
* Notificações por e-mail para alertas críticos
* Ver tendências em dados de desempenho, útil para o planeamento da capacidade do AD FS
* Análise de utilização de inícios de sessão do AD FS com diferentes pivôs (aplicações, utilizadores, localização na rede, etc.), útil para entender como o AD FS está a ser utilizado.
* Relatórios do AD FS, tais como os principais 50 utilizadores com tentativas de Nome de utilizador/Palavra-passe incorretas com o último endereço IP

O vídeo seguinte apresenta uma descrição geral do Azure AD Connect Health para o AD FS

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health--Monitor-you-identity-bridge/player]
>
>

## <a name="azure-ad-connect-health-for-syncactive-directory-aadconnect-health-syncmd"></a>[Azure AD Connect Health para Sincronização](active-directory-aadconnect-health-sync.md)
O Azure AD Connect Health para Sincronização monitoriza e fornece informações sobre as sincronizações que ocorrem entre o Active Directory no local e o Azure Active Directory. O Azure AD Connect Health para Sincronização fornece o seguinte conjunto de capacidades principais:

* Monitorização com alertas para saber quando os servidores do Azure AD Connect, também conhecidos como Motor de Sincronização, não estão em bom estado de funcionamento
* Notificações por e-mail para alertas críticos
* Sincronizar informações operacionais, incluindo gráficos de latência para Operações de Sincronização e as tendências em operações diferentes, tal como adições, atualizações e eliminações.
* Informações de leitura rápida acerca das propriedades de sincronização e da última exportação com êxito para o Azure AD
* Os relatórios sobre erros de sincronização ao nível do objeto \(não requerem o Azure AD Premium\)

O vídeo seguinte apresenta uma descrição geral do Azure AD Connect Health para sincronização

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Health-Monitoring-the-sync-engine/player]
>
>

## <a name="azure-ad-connect-health-for-ad-ds-previewactive-directory-aadconnect-health-addsmd"></a>[Azure AD Connect Health para AD DS (pré-visualização)](active-directory-aadconnect-health-adds.md)
O Azure AD Connect Health para AD DS proporciona monitorização para Controladores de Domínio instalados no Windows Server 2008 R2, no Windows Server 2012, no Windows Server 2012 R2 e no Windows Server 2016. Uma instalação do agente de estado de funcionamento de fácil e de baixo custo permite-lhe monitorizar o ambiente AD DS diretamente a partir da nuvem. O Azure AD Connect Health para AD DS fornece o seguinte conjunto de capacidades principais:

* Alertas de monitorização para detetar quando os controladores de domínio estão em mau estado de funcionamento, em conjunto com as notificações de correio eletrónico para alertas críticos.
* O dashboard de Controladores de Domínio que fornece uma vista rápida do estado de funcionamento e operacional de controladores de domínio.
* O dashboard de Estado de Replicação com informações de replicação mais recentes, em conjunto com ligações para guias de resolução de problemas quando forem detetados erros.
* Aceda rapidamente a quaisquer gráficos de dados de desempenho dos contadores de desempenho populares, necessários para resolução de problemas e efeitos de monitorização.

O vídeo seguinte apresenta uma descrição geral do Azure AD Connect Health para o AD DS

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health-monitors-on-premises-AD-Domain-Services/player]
>
>

## <a name="get-started-with-azure-ad-connect-health"></a>Introdução ao Azure AD Connect Health
É muito fácil começar a utilizar o Azure AD Connect Health. Siga os passos abaixo:

1. [Obtenha o Azure AD Premium](../active-directory-get-started-premium.md) ou [inicie uma versão de avaliação](https://azure.microsoft.com/trial/get-started-active-directory/)
2. [Transfira e instale os agentes do Azure AD Connect Health](#download-and-install-azure-ad-connect-health-agent) nos servidores de identidade.
3. Veja o dashboard do Azure AD Connect Health em [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth).

> [!NOTE]
> Lembre-se de que só poderá ver dados no dashboard do Azure AD Connect Health, depois de instalar os agentes nos servidores de destino.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Transferir e Instalar o Agentes do Azure AD Connect Health
* Certifique-se de que cumpre os requisitos para o Azure AD Connect Health
* Para começar a utilizar o Azure AD Connect Health para o AD FS, pode transferir a versão mais recente do agente aqui: [Transferir o Agente do Azure AD Connect Health para o AD FS.](http://go.microsoft.com/fwlink/?LinkID=518973)
  [](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)
* Para começar a utilizar o Azure AD Connect Health para sincronização, transfira e instale a [versão mais recente do Azure AD Connect](http://go.microsoft.com/fwlink/?linkid=615771).  O agente de estado de funcionamento será instalado como parte da instalação do Azure AD Connect (versão 1.0.9125.0 ou superior).  O Azure AD Connect suporta uma atualização no local de versões anteriores.
* Para começar a utilizar o Azure AD Connect Health para o AD FS, pode transferir a versão mais recente do agente aqui: [Transferir o Agente do Azure AD Connect Health para o AD FS.](http://go.microsoft.com/fwlink/?LinkID=820540)
  [](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)

## <a name="azure-ad-connect-health-portal"></a>Portal do Azure AD Connect Health
O Portal do Azure AD Connect Health permite a visualização de alertas, a monitorização do desempenho e a análise da utilização. https://aka.ms/aadconnecthealth leva-o para o painel principal do Azure AD Connect Health.  Pode considerar um painel como uma janela. No painel principal, verá Início Rápido, Serviços no Azure AD Connect Health e opções adicionais de configuração. Por baixo da captura de ecrã há uma breve explicação de cada um deles.  Após implementar os agentes, o serviço de estado de funcionamento identifica automaticamente os serviços monitorizados pelo Azure AD Connect Health.

![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health/portal4.png)

* **Início Rápido** – ao selecionar esta opção abre painel Início Rápido. Aqui poderá transferir o agente do Azure AD Connect Health ao escolher Obter Ferramentas, aceder a documentação e fornecer comentários.
* **Serviços de Federação do Active Directory** – representa todos os serviços do AD FS que o Azure AD Connect Health está atualmente a monitorizar. Ao selecionar uma das instâncias, será aberto um painel com informações sobre essa instância de serviços.  Estas informações incluem uma descrição geral, as propriedades, os alertas, a monitorização e a análise da utilização. Saiba mais acerca das capacidades [aqui.](active-directory-aadconnect-health-adfs.md)
* **Azure Active Directory Connect (Sincronização)** – representa os servidores do Azure AD Connect que o Azure AD Connect Health está atualmente a monitorizar. Ao selecionar a entrada, será aberto um painel com informações sobre os seus servidores do Azure AD Connect. Saiba mais acerca das capacidades [aqui.](active-directory-aadconnect-health-sync.md)
* **Serviços de Domínio do Active Directory** – representa todas as florestas do AD FS que o Azure AD Connect Health está a monitorizar. Ao selecionar uma das florestas, será aberto um painel com informações sobre essa floresta.  Estas informações incluem uma descrição geral de informações essenciais, dashboard de Controladores de Domínio, dashboard do Estado de Replicação, alertas e monitorização. Saiba mais acerca das capacidades [aqui.](active-directory-aadconnect-health-adds.md)
* **Configurar** – permite ativar ou desativar o seguinte:

  1. Atualização automática, para atualizar automaticamente o agente do Azure AD Connect Health para a versão mais recente. Isto significa que será atualizado automaticamente para a versão mais recente do agente do Azure AD Connect Health mal fique disponível. Opção ativada por predefinição.
  2. Permitir o acesso da Microsoft aos dados de estado de funcionamento do diretório do Azure AD apenas para a resolução de problemas. Isto significa que se esta opção estiver ativada, a Microsoft será capaz de ver os mesmos dados que está a ver. Isto pode ajudar a resolver problemas e na assistência com problemas. Opção desativada por predefinição.

## <a name="related-links"></a>Ligações relacionadas
* [Instalação do Agente do Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operações do Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Utilizar o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilizar o Azure AD Connect Health para Sincronização](active-directory-aadconnect-health-sync.md)
* [Utilizar o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md)
* [FAQ do Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Histórico das Versões do Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)



<!--HONumber=Jan17_HO3-->


