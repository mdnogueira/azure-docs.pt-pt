---
title: "Notas de versão do catálogo de dados do Azure | Microsoft Docs"
description: "Notas de versão do catálogo de dados do Azure."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 3aca9c49-45a4-4352-92e6-bd25ee3eacf7
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 10/01/2017
ms.author: maroche
ms.openlocfilehash: ba79bf465fc7775c44b370de287bfca5e29beccd
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="azure-data-catalog-release-notes"></a>Notas de versão do catálogo de dados do Azure
## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>Notas para as 20 de Novembro de 2015 da versão do catálogo de dados do Azure
### <a name="opening-data-sources-in-power-bi-desktop"></a>Abrir as origens de dados no Power BI Desktop
Ao utilizar a opção "Abrir no Power BI Desktop" do **catálogo de dados do Azure** portal, os utilizadores poderão encontrar um dos dois problemas na aplicação Power BI Desktop:

* É apresentada uma caixa de diálogo com o título "Não é possível para abrir documentos"
* Abre a aplicação Power BI Desktop, mas o ficheiro parece estar em branco

Para cada situação, o problema pode ser resolvido pela transferir e instalar a versão mais recente do Power BI Desktop de [PowerBI.com](https://powerbi.com).

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>Notas para a 13 de Novembro de 2015 da versão do catálogo de dados do Azure
### <a name="registering-and-connecting-to-teradata"></a>Registar e ligar para Teradata
Ao ligar a origens de dados de Teradata utilizadores tem de ter instalado o controlador Teradata ODBC correto que corresponde ao número de bits (32 bits ou 64 bits) do software que está a ser utilizado.

A partir deste data de lançamento ADC, o mais recente [o controlador ODBC do Teradata para windows (versão 15.10)](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) é compatível com o Office 2013, mas não com o Office 2016.

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>Notas para de 13 de Julho de 2015 da versão do catálogo de dados do Azure
### <a name="registering-and-connecting-to-oracle-database"></a>Registar e ligar à base de dados Oracle
Ao ligar a origens de dados de base de dados Oracle utilizadores tem de ter instalado os controladores de Oracle corretos que corresponde ao número de bits (32 bits ou 64 bits) do software que está a ser utilizado.

* Ao registar origens de dados Oracle num computador com o Windows de 32 bits, serão utilizados os controladores de Oracle de 32 bits
* Ao registar origens de dados Oracle num computador com o Windows de 64 bits, serão utilizados os controladores de Oracle de 64 bits
* Ao ligar a origens de dados Oracle com o Excel num computador com a versão de 32 bits do Microsoft Office, incluindo no Windows de 64 bits, os controladores de Oracle de 32 bits serão utilizados
* Ao ligar a origens de dados Oracle com o Excel num computador com a versão de 64 bits do Microsoft Office, serão utilizados os controladores de Oracle de 64 bits

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>Registar e ligar ao SQL Server Reporting Services
Suporte para as origens de dados do SQL Server Reporting Services (SSRS) está actualmente limitado para apenas os servidores de modo nativo. Suporte para SSRS no modo SharePoint será adicionado a uma versão posterior.

### <a name="opening-data-assets-in-excel"></a>Recursos de dados de abrir no Excel
Ao abrir os recursos de dados no Microsoft Excel do **catálogo de dados do Azure** portal, os utilizadores poderão ser-lhe solicitados com um **aviso de segurança do Microsoft Excel** caixa de diálogo. Este é padrão, podem selecionar o comportamento esperado e os utilizadores **ativar** para continuar.

Para obter mais informações, consulte [ativar ou desativar alertas de segurança sobre ligações de ficheiros e de Web sites suspeitos](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>Registo da origem de dados e configuração de proxy e de política
Os utilizadores podem encontrar uma situação em que podem iniciar sessão no portal do catálogo de dados do Azure, mas quando tentam iniciar sessão para a ferramenta de registo da origem de dados encontrarem uma mensagem de erro que impede que iniciar sessão.

Existem duas causas possíveis para este comportamento do problema:

**Causa 1: Configuração de serviços de Federação do Active Directory** a ferramenta de registo da origem de dados utiliza a autenticação de formulários para validar os inícios de sessão do utilizador no Active Directory. Início de sessão bem-sucedidos, a autenticação de formulários deve ser ativada na política de autenticação Global por um administrador do Active Directory.

Em algumas situações, este comportamento de erro pode ocorrer apenas quando o utilizador está na rede da empresa, ou apenas quando o utilizador está a ligar de fora da rede da empresa. A política de autenticação Global permite que os métodos de autenticação para ser ativada em separado para intranet e extranet ligações. Se a autenticação de formulários não está ativada para a rede a partir do qual o utilizador está a ligar, poderão ocorrer erros de início de sessão.

Para obter mais informações, consulte [configurar políticas de autenticação](https://technet.microsoft.com/library/dn486781.aspx).

**Causa 2: Configuração de proxy de rede** se a rede empresarial utiliza um servidor proxy, a ferramenta de registo poderá não conseguir ligar ao Azure Active Directory através do proxy. Os utilizadores podem Certifique-se de que a ferramenta de registo editando o ficheiro de configuração a ferramenta, adicionar esta secção para o ficheiro:

      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


Para localizar o ficheiro de RegistrationTool.exe.config, inicie a ferramenta de registo e, em seguida, abrir o utilitário do Gestor de tarefas do Windows. No separador Detalhes do Gestor de tarefas, clique com o botão direito no RegistrationTool.exe e escolha a localização do ficheiro aberto no menu de pop-up.
