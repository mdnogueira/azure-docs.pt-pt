
---
title: Utilizar o Azure AD Connect Health com o AD FS | Microsoft Docs
description: Esta é a página do Azure AD Connect Health acerca de como monitorizar a infraestrutura do AD FS no local.
services: active-directory
documentationcenter: ''
author: karavar
manager: femila
editor: karavar

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/14/2016
ms.author: vakarand

---
# Utilizar o Azure AD Connect Health com o AD FS
A seguinte documentação é específica para monitorizar a infraestrutura do AD FS com o Azure AD Connect Health. Para obter informações sobre a monitorização do Azure AD Connect (Sincronização) com o Azure AD Connect Health, consulte [Utilizar Azure AD Connect Health para Sincronização](active-directory-aadconnect-health-sync.md). Além disso, para obter informações sobre a monitorização dos Serviços de Domínio do Active Directory com o Azure AD Connect Health, consulte [Utilizar Azure AD Connect Health com AD DS](active-directory-aadconnect-health-adds.md). 

## Alertas do AD FS
A secção Alertas do Azure AD Connect Health apresenta a lista de alertas ativos. Cada alerta inclui informações relevantes, os passos de resolução e ligações para a documentação relacionada. Ao selecionar um alerta ativo ou resolvido, verá um novo painel com informações adicionais, bem como passos que pode tomar para resolver o alerta e ligações para documentação adicional. Pode também visualizar dados históricos de alertas que foram resolvidos no passado.

Ao selecionar um alerta, receberá informações adicionais, bem como passos que pode tomar para resolver o alerta e ligações para documentação adicional.

![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health/alert2.png)

## Análise de Utilização do AD FS
A Análise de Utilização do Azure AD Connect Health analisa o tráfego de autenticação dos servidores de federação. Selecionar a caixa de análise de utilização abrirá o painel de análise de utilização, que mostrará as métricas e os agrupamentos.

> [!NOTE]
> Para que possa utilizar a análise de utilização com o AD FS, tem de se certificar de que a auditoria do AD FS está ativada. Para obter mais informações, consulte [Ativar Auditoria do AD FS](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs).
> 
> 

![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health/report1.png)

Para selecionar métricas adicionais, especifique um intervalo de tempo, ou, para alterar o agrupamento, clique simplesmente com o botão direito do rato no gráfico de análise de utilização e selecione Editar Gráfico. Em seguida, pode especificar o intervalo de tempo, alterar ou selecionar métricas e alterar o agrupamento. Pode ver a distribuição do tráfego de autenticação com base em diferentes “métricas” e agrupar cada métrica utilizando os parâmetros “agrupar por” relevantes descritos abaixo.

| Métrica | Agrupar por | O que significa agrupamento e por que motivo é útil? |
| --- | --- | --- |
| Total de Pedidos: o número total de pedidos processados pelo serviço de federação |Todos |Mostrará a contagem do número total de pedidos sem agrupamento. |
| Aplicação |Esta opção agrupará o total de pedidos com base na entidade confiadora visada. Este agrupamento é útil para entender que aplicação está a receber que percentagem do tráfego total. | |
| Servidor |Esta opção agrupará o total de pedidos com base no servidor que processou o pedido. Este agrupamento é útil para entender a distribuição de carga do tráfego total. | |
| Associação à área de trabalho |Esta opção agrupará o total de pedidos com base em pedidos provenientes de dispositivos que estão associados à área de trabalho (conhecidos). Este agrupamento é útil para entender se os recursos são acedidos através de dispositivos que são desconhecidos da infraestrutura de identidade. | |
| Método de autenticação |Esta opção agrupará o total de pedidos com base no método de autenticação utilizado para a autenticação. Este agrupamento é útil para entender o método de autenticação comum que é utilizado para autenticação. Seguem-se os métodos de autenticação possíveis <ol> <li>Autenticação Integrada do Windows (Windows)</li> <li>Autenticação Baseada em Formulários (Formulários)</li> <li>SSO (Início de Sessão Único)</li> <li>Autenticação de Certificados X509 (Certificado)</li> <br>Tenha em atenção que um pedido é contabilizado como SSO (Início de Sessão Único) se os servidores de federação receberem o pedido com um Cookie de SSO. Nestes casos, se o cookie for válido, o utilizador não é solicitado a fornecer credenciais e obtém acesso totalmente integrado à aplicação. Isto é comum se tiver várias entidades confiadoras protegidas pelos servidores de federação. | |
| Localização na Rede |Esta opção agrupará o total de pedidos com base na localização na rede do utilizador. Pode ser intranet ou extranet. Este agrupamento é útil para saber que percentagem do tráfego provêm da intranet versus extranet. | |
| Pedidos Falhados Totais: o número total de pedidos falhados processados pelo serviço de federação <br> (Esta métrica só está disponível no AD FS para o Windows Server 2012 R2) |Tipo de Erro |Mostrará o número de erros com base em tipos de erro predefinidos. Este agrupamento é útil para entender os tipos comuns de erros. <ul><li>Nome de Utilizador ou Palavra-passe Incorretos: erros devido a nome de utilizador ou palavra-passe incorretos.</li> <li>“Bloqueio de Extranet”: falhas devido a pedidos recebidos de um utilizador que foi bloqueado da extranet </li><li> “Palavra-passe Expirada”: falhas devido a utilizadores que iniciam sessão com uma palavra-passe expirada.</li><li>“Conta Desativada”: falhas devido a utilizadores que iniciam sessão com uma conta desativada.</li><li>“Autenticação do Dispositivo”: falhas devido a utilizadores que não se autenticam utilizando a Autenticação do Dispositivo.</li><li>“Autenticação do Certificado de Utilizador”: falhas devido a utilizadores que não se autenticam devido a um certificado inválido.</li><li>“MFA”: falhas devido utilizadores que não se autenticam utilizando o Multi Factor Authentication.</li><li>“Outras Credenciais”: “Autorização de Emissão”: falhas devido a falhas de autorização.</li><li>“Delegação de Emissão”: falhas devido a erros de delegação de emissão.</li><li>“Aceitação de Token”: falhas devido ao ADFS rejeitar o token de um Fornecedor de Identidade de terceiros.</li><li>“Protocolo”: falha devido a erros de protocolo.</li><li>“Desconhecido”: cobre tudo. Todas as outras falhas que não se encaixem nas categorias definidas.</li> |
| Servidor |Esta ação agrupará os erros com base no servidor. É útil para entender a distribuição de erros pelos servidores. Uma distribuição desigual pode indicar um servidor num estado defeituoso. | |
| Localização na Rede |Esta ação agrupará os erros com base na localização na rede dos pedidos (intranet vs. extranet). É útil para entender o tipo de pedidos que estão a falhar. | |
| Aplicação |Esta opção agrupará as falhas com base na aplicação de destino (entidade confiadora). É útil para entender que aplicação de destino está a ter o maior número de erros. | |
| Contagem de Utilizadores: o número médio de utilizadores exclusivos ativos no sistema |Todos |Fornece uma contagem do número médio de utilizadores que utilizam o serviço de federação no intervalo de tempo selecionado. Os utilizadores não são agrupados. <br>A média dependerá do intervalo de tempo selecionado. |
| Aplicação |Esta opção agrupará o número médio de utilizadores com base na aplicação de destino (entidade confiadora). É útil para entender quantos utilizadores estão a utilizar que aplicação. | |

## Monitorização de Desempenho do AD FS
A Monitorização de Desempenho do Azure AD Connect Health fornece informações de monitorização sobre métricas. Ao marcar a caixa Monitorização, é apresentado um painel que fornece informações detalhadas sobre as métricas.

![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health/perf1.png)

Ao selecionar a opção Filtrar no topo do painel, pode filtrar por servidor para ver as métricas de um servidor específico. Para alterar as métricas, clique simplesmente com o botão direito do rato no gráfico de monitorização no painel de monitorização e selecione Editar Gráfico. Em seguida, no novo painel apresentado, pode selecionar métricas adicionais na lista pendente e especificar um intervalo de tempo para ver os dados de desempenho.

## Relatórios do AD FS
O Azure AD Connect Health fornece relatórios sobre a atividade e o desempenho do AD FS. Estes relatórios ajudam os administradores a obter conhecimentos aprofundados sobre as atividades nos respetivos servidores do AD FS.

### Principais 50 Utilizadores com inícios de sessão falhados por Nome de Utilizador/Palavra-passe
Uma das razões comuns para um pedido de autenticação falhado num servidor do AD FS é um pedido com credenciais inválidas, ou seja, um nome de utilizador ou palavra-passe incorreto. Esta situação ocorre normalmente devido a palavras-passe complexas, palavras-passe esquecidas ou erros de digitação.

Existem, no entanto, outras razões que podem resultar num montante inesperado de pedidos desses a serem processados pelos servidores do AD FS, por exemplo, uma aplicação que coloca em cache as credenciais de utilizador e estas expiram ou um utilizador mal intencionado a tentar iniciar sessão na conta de um utilizador com uma série de palavras-passe bem conhecidas.

O Azure AD Connect Health para ADFS fornece um relatório sobre os principais 50 utilizadores com tentativas de início de sessão falhadas devido a palavra-passe ou nome de utilizador inválido. Consegue-se isto processando os eventos de auditoria gerados por todos os servidores do AD FS nos farms

![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health-adfs/report1a.png)

Neste relatório, terá acesso fácil às seguintes informações:

* N.º total de pedidos falhados com nome de utilizador/palavra-passe incorretos nos últimos 30 dias.
* N.º médio de utilizadores que falhou um início de sessão com nome de utilizador/palavra-passe incorretos, numa base diária.

Clicar nesta parte leva-o para o painel do relatório principal que fornece detalhes adicionais, que incluem um gráfico que fornece informações de tendências para estabelecer uma linha de base sobre pedidos com o nome de utilizador ou palavra-passe errados e a lista dos principais 50 utilizadores com o número de tentativas falhadas.

O gráfico fornece as seguintes informações:

* O n.º total de inícios de sessão falhados devido a um nome de utilizador/palavra-passe incorreto numa base diária.
* O n.º total de utilizadores exclusivos que falharam inícios de sessão numa base diária.

![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health-adfs/report2a.png)

O relatório fornece o seguinte:

| Item de Relatório | Descrição |
| --- | --- |
| ID de Utilizador |Mostra o ID de utilizador que foi utilizado. Tenha em atenção que o valor é o que o utilizador escreveu e, em alguns casos, verá também o ID de utilizador errado que está ser utilizado. |
| Tentativas Falhadas |Mostra o n.º total de tentativas falhadas para esse ID de utilizador específico. A tabela está ordenada por ordem descendente do maior número de tentativas falhadas. |
| Última Falha |Mostra o carimbo de data/hora em que ocorreu a última falha. |

> [!NOTE]
> Este relatório é atualizado automaticamente a cada 2 horas com novas informações recolhidas nesse período de tempo. Sendo assim, as tentativas de início de sessão nas últimas 2 horas poderão não estar incluídas no relatório.
> 
> 

## Ligações relacionadas
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalação do Agente do Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operações do Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Utilizar o Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md)
* [Utilizar o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md)
* [FAQ do Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Histórico das Versões do Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)

<!--HONumber=Sep16_HO4-->


