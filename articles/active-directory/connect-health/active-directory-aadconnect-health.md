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
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 881ce13b6e4b10064294e590431434b29da3fb33
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-your-on-premises-identity-infrastructure-and-synchronization-services-in-the-cloud"></a>Monitorizar a infraestrutura de identidade no local e os serviços de sincronização na nuvem
O Azure Active Directory (Azure AD) Connect Health ajuda a monitorizar e a obter informações sobre a sua infraestrutura de identidade no local, bem como sobre os serviços de sincronização. Permite-lhe manter uma ligação fiável ao Office 365 e aos Microsoft Online Services ao fornecer capacidades de monitorização para os componentes de identidade chave, como servidores de Serviços de Federação do Active Directory (AD FS), servidores do Azure AD Connect (também conhecido como Motor de Sincronização), controladores de domínio do Active Directory, etc. Torna também facilmente acessíveis os pontos de dados chave sobre estes componentes, para que possa obter informações sobre a utilização e outras informações importantes para tomar decisões informadas.

As informações são apresentadas no [portal do Azure AD Connect Health](https://aka.ms/aadconnecthealth). No portal do Azure AD Connect Health, pode visualizar alertas, a monitorização do desempenho, a análise de utilização e outras informações. O Azure AD Connect Health ativa a lente única do estado de funcionamento dos componentes de identidade chave num único local.

![O que é o Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnecthealth2.png)

À medida que as funcionalidades do Azure AD Connect Health aumentam, o portal fornece um dashboard único através a lente de identidade. Obtém um ambiente ainda mais robusto, em bom estado de funcionamento e integrado de que os seus utilizadores podem tirar partido para aumentar a sua capacidade para concluir tarefas.

## <a name="why-use-azure-ad-connect-health"></a>Porquê utilizar o Azure AD Connect Health?
A integração dos diretórios no local com o Azure AD torna os utilizadores mais produtivos, porque existe uma identidade comum para acederem a recursos na cloud e no local. No entanto, esta integração acarreta o desafio de garantir que este ambiente está em bom estado de funcionamento, de modo a que os utilizadores possam aceder com fiabilidade aos recursos no local e na cloud a partir de qualquer dispositivo. O Azure AD Connect Health ajuda a monitorizar e a obter informações acerca da sua infraestrutura de identidade no local que é utilizada para aceder ao Office 365 ou a outras aplicações do Azure AD. É tão simples como instalar um agente em cada um dos servidores de identidade no local.

## <a name="azure-ad-connect-health-for-ad-fsactive-directory-aadconnect-health-adfsmd"></a>[Azure AD Connect Health para AD FS](active-directory-aadconnect-health-adfs.md)
O Azure AD Connect Health para AD FS suporta o AD FS 2.0 no Windows Server 2008 R2, no Windows Server 2012 e no Windows Server 2012 R2. Suporta também a monitorização de servidores proxy do AD FS ou de aplicações Web que fornecem suporte de autenticação para acesso à extranet. Com uma instalação fácil e de baixo custo do Agente de Estado de Funcionamento, o Azure AD Connect Health para AD FS fornece o seguinte conjunto de capacidades principais:

* Monitorização com alertas para saber quando os servidores AD FS e proxy do AD FS não estão em bom estado
* Notificações por e-mail para alertas críticos
* Tendências em dados de desempenho, que são úteis para o planeamento da capacidade do AD FS
* Análise de utilização de inícios de sessão do AD FS com pivôs (aplicações, utilizadores, localização na rede, etc.), útil para entender como o AD FS está a ser utilizado
* Relatórios do AD FS, tais como os 50 utilizadores com mais tentativas de nome de utilizador/palavra-passe incorretas e o último endereço IP dos mesmos

O vídeo seguinte apresenta uma descrição geral do Azure AD Connect Health para AD FS.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health--Monitor-you-identity-bridge/player]
>
>

## <a name="azure-ad-connect-health-for-syncactive-directory-aadconnect-health-syncmd"></a>[Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md)
O Azure AD Connect Health para sincronização monitoriza e fornece informações sobre as sincronizações que ocorrem entre o Active Directory no local e o Azure AD. O Azure AD Connect Health para sincronização fornece o seguinte conjunto de capacidades principais:

* Monitorização com alertas para saber quando um servidor do Azure AD Connect, também conhecido como Motor de Sincronização, não está em bom estado de funcionamento
* Notificações por e-mail para alertas críticos
* Sincronizar informações operacionais, que incluem gráficos de latência para operações de sincronização e as tendências em operações diferentes, tal como adições, atualizações e eliminações
* Informações de leitura rápida acerca das propriedades de sincronização e da última exportação com êxito para o Azure AD
* Relatórios sobre erros de sincronização ao nível do objeto \(não requerem o Azure AD Premium\)

O vídeo seguinte apresenta uma descrição geral do Azure AD Connect Health para sincronização.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Health-Monitoring-the-sync-engine/player]
>
>

## <a name="azure-ad-connect-health-for-ad-dsactive-directory-aadconnect-health-addsmd"></a>[Azure AD Connect Health para AD DS](active-directory-aadconnect-health-adds.md)
O Azure AD Connect Health para Active Directory Domain Services (AD DS) fornece monitorização para controladores de domínio instalados no Windows Server 2008 R2, no Windows Server 2012, no Windows Server 2012 R2 e no Windows Server 2016. A instalação do Agente de Estado de Funcionamento permite-lhe monitorizar o ambiente do AD DS no local a partir da cloud. O Azure AD Connect Health para AD DS fornece o seguinte conjunto de capacidades principais:

* Alertas de monitorização para detetar quando os controladores de domínio estão em mau estado de funcionamento e notificações por e-mail para alertas críticos
* O dashboard de Controladores de Domínio, que fornece uma vista rápida do estado de funcionamento e operacional dos controladores de domínio
* O dashboard de Estado de Replicação que inclui as informações de replicação mais recentes e hiperligações para guias de resolução de problemas quando forem detetados erros
* Acesso rápido em qualquer local a gráficos de dados de desempenho de contadores de desempenho populares, que são necessários para efeitos de resolução de problemas e de monitorização

O vídeo seguinte apresenta uma descrição geral do Azure AD Connect Health para AD DS.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health-monitors-on-premises-AD-Domain-Services/player]
>
>

## <a name="get-started-with-azure-ad-connect-health"></a>Introdução ao Azure AD Connect Health
Para começar a utilizar o Azure AD Connect Health, utilize os passos seguintes:

1. [Obtenha o Azure AD Premium](../active-directory-get-started-premium.md) ou [inicie uma versão de avaliação](https://azure.microsoft.com/trial/get-started-active-directory/).
2. [Transfira e instale os Agentes do Azure AD Connect Health](#download-and-install-azure-ad-connect-health-agent) nos servidores de identidade.
3. Veja o dashboard do Azure AD Connect Health em [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth).

> [!NOTE]
> Lembre-se de que só poderá ver dados no dashboard do Azure AD Connect Health depois de instalar os Agentes do Azure AD Connect Health nos servidores de destino.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Transferir e instalar o Agente do Azure AD Connect Health
* Certifique-se de que [cumpre os requisitos](active-directory-aadconnect-health-agent-install.md#requirements) para o Azure AD Connect Health.
* Introdução ao Azure AD Connect Health para AD FS
    * [Transferir o Agente do Azure AD Connect Health para o AD FS.](http://go.microsoft.com/fwlink/?LinkID=518973)
    * [Veja a instruções de instalação](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Introdução ao Azure AD Connect Health para sincronização
    * [Transferir e instalar a versão mais recente do Azure AD Connect](http://go.microsoft.com/fwlink/?linkid=615771). O Agente de Estado de Funcionamento para sincronização será instalado como parte da instalação do Azure AD Connect (versão 1.0.9125.0 ou superior).
* Introdução ao Azure AD Connect Health para AD DS
    * [Transfira o Agente do Azure AD Connect Health para AD DS](http://go.microsoft.com/fwlink/?LinkID=820540).
    * [Veja a instruções de instalação](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds).

## <a name="azure-ad-connect-health-portal"></a>Portal do Azure AD Connect Health
O portal do Azure AD Connect Health mostra vistas de alertas, monitorização de desempenho e análise de utilização. O URL https://aka.ms/aadconnecthealth leva-o para o painel principal do Azure AD Connect Health. Pode considerar um painel como uma janela. No painel principal, verá **Início Rápido**, serviços no Azure AD Connect Health e opções adicionais de configuração. Veja a captura de ecrã seguinte e as breves explicações a seguir à captura de ecrã. Após implementar os agentes, o serviço de estado de funcionamento identifica automaticamente os serviços monitorizados pelo Azure AD Connect Health.

> [!NOTE]
> Para obter informações de licenciamento, veja [FAQ do Azure AD Connect](active-directory-aadconnect-health-faq.md) ou a [página de Preços do Azure AD](https://aka.ms/aadpricing).
    
![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health/portal4.png)

* **Início Rápido**: ao selecionar esta opção, o painel **Início Rápido** abre. Pode transferir o Agente do Azure AD Connect Health, selecionando **Obter ferramentas**. Também pode aceder à documentação e fornecer comentários.
* **Serviços de Federação do Active Directory**: esta opção mostra todos os serviços do AD FS que o Azure AD Connect Health está atualmente a monitorizar. Quando seleciona uma instância, o painel que aparece mostra informações sobre essa instância de serviço. Estas informações incluem uma descrição geral, as propriedades, os alertas, a monitorização e a análise da utilização. Saiba mais acerca das capacidades em [Utilizar o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md).
* **Azure Active Directory Connect (sincronização)**: esta opção mostra os servidores do Azure AD Connect que o Azure AD Connect Health está atualmente a monitorizar. Quando seleciona a entrada, o painel que aparece mostra informações sobre os servidores do Azure AD Connect. Saiba mais acerca das capacidades em [Utilizar o Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md).
* **Active Directory Domain Services**: esta opção mostra todas as florestas do AD FS que o Azure AD Connect Health está atualmente a monitorizar. Quando seleciona uma floresta, o painel que aparece mostra informações sobre essa floresta. Estas informações incluem uma descrição geral de informações essenciais, o dashboard de Controladores de Domínio, o dashboard de Estado de Replicação, alertas e monitorização. Saiba mais acerca das capacidades em [Utilizar o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md).
* **Configurar**: esta secção inclui opções para ativar ou desativar o seguinte:

  - Atualização automática, para atualizar automaticamente o agente do Azure AD Connect Health para a versão mais recente: quando ficarem disponíveis versões mais recentes do Agente do Azure AD Connect Health, a atualização para as mesmas será feita automaticamente. Opção ativada por predefinição.
  - Permitir o acesso da Microsoft aos dados de estado de funcionamento do diretório do Azure AD apenas para a resolução de problemas: se esta opção estiver ativada, a Microsoft poderá ver os mesmos dados que o utilizador vê. Estas informações podem ajudar a resolver problemas e na assistência com problemas. Opção desativada por predefinição.

## <a name="related-links"></a>Ligações relacionadas
* [Instalação do Agente do Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operações do Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Utilizar o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilizar o Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md)
* [Utilizar o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md)
* [FAQ do Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Histórico de versões do Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
