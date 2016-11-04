
---
title: Utilizar o Azure AD Connect Health com sincronização | Microsoft Docs
description: Esta é a página do Azure AD Connect Health que irá discutir como monitorizar a sincronização do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/08/2016
ms.author: billmath

---
# Utilizar o Azure AD Connect Health para sincronização
A seguinte documentação é específica para monitorizar a sincronização do Azure AD Connect (Sync) com o Azure AD Connect Health.  Para informações sobre como monitorizar o AD FS com o Azure AD Connect Health consulte [Utilizar o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md). Adicionalmente, para informações sobre como monitorizar os Serviços de Domínio do Active Directory com o Azure AD Connect Health consulte [Utilizar o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md).

![Azure AD Connect Health para Sincronização](./media/active-directory-aadconnect-health-sync/sync.png)

## Alertas para o Azure AD Connect Health para sincronização
A secção Alertas do Azure AD Connect Health para sincronização apresenta-lhe a lista de alertas ativos. Cada alerta inclui informações relevantes, os passos de resolução e ligações para a documentação relacionada. Ao selecionar um alerta ativo ou resolvido, verá um novo painel com informações adicionais, bem como passos que pode tomar para resolver o alerta e ligações para documentação adicional. Pode também visualizar dados históricos de alertas que foram resolvidos no passado.

Ao selecionar um alerta, receberá informações adicionais, bem como passos que pode tomar para resolver o alerta e ligações para documentação adicional.

![Erro de sincronização do Azure AD Connect](./media/active-directory-aadconnect-health-sync/alert.png)

### Avaliação Limitada de Alertas
Se o Azure AD Connect NÃO está a utilizar a configuração predefinida (por exemplo, se a Filtragem de Atributos é alterado da configuração predefinida para uma configuração personalizada), então o agente do Azure AD Connect Health não irá carregar os eventos de erro relacionados com o Azure AD Connect. 

Isto limita a avaliação de alertas pelo serviço. Verá uma faixa que indica esta condição no Portal do Azure, no seu serviço.

![Azure AD Connect Health para Sincronização](./media/active-directory-aadconnect-health-sync/banner.png)

Pode alterar esta situação clicando em “Definições” e permitindo que o agente do Azure AD Connect Health carregue todos os registos de erros.

![Azure AD Connect Health para Sincronização](./media/active-directory-aadconnect-health-sync/banner2.png)

## Informações de Sincronização
Com a versão mais recente do Azure AD Connect Health para sincronização, foram adicionadas as seguintes novas funcionalidades:

* Latência de operações de sincronização
* Tendência de Alterações de Objetos

### Latência de sincronização
Esta funcionalidade fornece uma tendência gráfica de latência das operações de sincronização (importar, exportar, etc.) para os conectores.  Proporciona uma forma rápida e fácil de entender não apenas a latência das suas operações (ótimo, se tiver um grande conjunto de alterações a ocorrerem), mas também uma forma de detetar anomalias na latência que possam exigir investigação adicional.

![Latência de sincronização](./media/active-directory-aadconnect-health-sync/synclatency.png)

Por predefinição, é apresentada apenas a latência da operação de 'Exportação' para o conector do Azure AD.  Para ver mais operações no conector ou para ver operações a partir de outros conectores, clique com o botão direito do rato no gráfico e escolha a operação e conector específicos.

### Sincronização das Alterações de Objetos
Esta funcionalidade fornece uma tendência gráfica do número de alterações que estão a ser avaliadas e exportadas para o Azure AD.  É difícil, atualmente, tentar recolher estas informações a partir de registos de sincronização.  O gráfico fornece-lhe, não apenas uma forma mais simples de monitorizar, o número de alterações que estão a ocorrer no seu ambiente, mas também um amostra visual das falhas que estão a ocorrer.

![Latência de sincronização](./media/active-directory-aadconnect-health-sync/syncobjectchanges.png)

## Ligações relacionadas
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalação do Agente do Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operações do Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Utilizar o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilizar o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md)
* [FAQ do Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Histórico das Versões do Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)

<!--HONumber=Sep16_HO4-->


