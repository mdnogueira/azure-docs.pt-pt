
<properties
    pageTitle="Utilizar o Azure AD Connect Health com o AD DS | Microsoft Azure"
    description="Esta é a página do Azure AD Connect Health que irá discutir como monitorizar o AD DS."
    services="active-directory"
    documentationCenter=""
    authors="arluca"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/14/2016"
    ms.author="arluca"/>


# Utilizar o Azure AD Connect Health com o AD DS
A seguinte documentação é específica para monitorizar os Serviços de Domínio do Active Directory com o Azure AD Connect Health. Isto inclui o AD DS instalado no Windows Server 2008 R2, Windows Server 2012 e no Windows Server 2012 R2.

Para informações sobre como monitorizar o AD FS com o Azure AD Connect Health consulte [Utilizar o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md). Além disso, para obter informações sobre como monitorizar o Azure AD Connect (Sincronização) com o Azure AD Connect Health, veja [Utilizar o Azure AD Connect Health para Sincronização](active-directory-aadconnect-health-sync.md).

![Azure AD Connect Health para AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-entry.png)

## Alertas para o Azure AD Connect Health para AD DS
A secção Alertas no Azure AD Connect Health para AD DS fornece uma lista de alertas ativos e resolvidos, relacionados com os seus Controladores de Domínio. Selecionar um alerta ativa ou resolvida irá abrir um novo painel com informações adicionais, em conjunto com passos de resolução e ligações para a documentação de suporte. Cada tipo de alerta pode ter uma ou mais instâncias, que correspondem a cada um dos controladores de domínio afetados por esse alerta específico. Perto da parte inferior do painel de alertas, pode selecionar um controlador de domínio afetado e um novo painel será aberto com detalhes adicionais sobre essa instância de alerta específica.

Ativar notificações de e-mail para alertas está disponível neste painel, bem como alterar o intervalo de tempo pretendido. Expandir o intervalo de tempo permite-lhe ver os alertas resolvidos anteriores.

![Erro de sincronização do Azure AD Connect](./media/active-directory-aadconnect-health/aadconnect-health-adds-alerts.png)

## Controladores de Domínio
Este dashboard fornece uma vista topológica do seu ambiente, em conjunto com as principais métricas operacionais e estado de funcionamento de cada um dos controladores de domínio monitorizado. As métricas apresentadas ajudam a identificar rapidamente quaisquer controladores de domínio que possam exigir uma investigação adicional. Por predefinição, é apresentado apenas um subconjunto de colunas. No entanto, ao clicar no comando das colunas irá encontrar o conjunto completo de colunas que estão disponíveis. Ao selecionar as colunas mais importantes para si faz com que este dashboard seja um local único e fácil de utilizar para ver o estado de funcionamento do seu ambiente de AD DS. 

![Controladores de Domínio](./media/active-directory-aadconnect-health/aadconnect-health-adds-domainsandsites-dashboard.png)

Os controladores de domínio podem ser agrupados pelo respetivo domínio ou site, o que é útil para compreender a topologia do ambiente. Por último, se fizer duplo clique no cabeçalho do painel, o dashboard será maximizado para utilizar os imóveis do ecrã disponível. Isto pode ser especialmente útil quando apresenta várias colunas. 

## Estado de Replicação
Este dashboard fornece uma vista do estado de replicação e a topologia de replicação dos controladores de domínio monitorizados. O estado da mais recente tentativa de replicação está listado, em conjunto com a documentação útil para qualquer erro encontrado. Se selecionar um controlador de domínio com um erro irá abrir um novo painel com informações adicionais, em conjunto com passos de resolução e ligações para a documentação de suporte. 

![Estado de Replicação](./media/active-directory-aadconnect-health/aadconnect-health-adds-replication.png)

## Monitorização
Esta funcionalidade fornece as tendências gráficas de diferentes contadores de desempenho, que são continuamente recolhidas de cada um dos controladores de domínio monitorizados. O desempenho de um controlador de domínio pode facilmente ser comparado em todos os outros controladores de domínio monitorizados na sua floresta. Além disso, pode ver vários contadores de desempenho lado a lado, que é útil quando resolver problemas no seu ambiente. 

![Monitorização](./media/active-directory-aadconnect-health/aadconnect-health-adds-monitoring.png)

Por predefinição, podemos pré-selecionar quatro contadores de desempenho. No entanto, pode incluir outros clicando no comando de filtro e selecionando ou desmarcando contadores de desempenho pretendidos. Além disso, se clicar num gráfico de contador de desempenho específico, será aberto um novo painel, que inclui os pontos de dados correspondentes para cada um dos controladores de domínio monitorizados.

## Ligações relacionadas

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalação do Agente do Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operações do Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Utilizar o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilizar o Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md)
* [FAQ do Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Histórico das Versões do Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)



<!--HONumber=Sep16_HO3-->


