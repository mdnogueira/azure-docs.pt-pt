---
title: "Como utilizar o Pacote de Conteúdos do Power BI para o Azure Active Directory | Microsoft Docs"
description: "Saiba como utilizar o Pacote de Conteúdos do Power BI Para o Azure Active Directory"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 5c642bb814a279756e8391f12fdc86b6ec0b4a8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Como utilizar o Pacote de Conteúdos do Power BI para o Azure Active Directory

Para os administradores de TI, é fundamental compreender como é que os utilizadores adotam e utilizam as funcionalidades do Azure Active Directory. Desta forma, podem planear a comunicação e a infraestrutura de TI, de modo a aumentar a utilização e tirar o máximo proveito das funcionalidades do AAD. O Pacote de Conteúdos do Power BI para o Azure Active Directory dá-lhe a capacidade de analisar mais aprofundadamente os seus dados, para saber como pode utilizá-los e obter informações mais ricas sobre o que os utilizadores estão a fazer com o Azure Active Directory e as várias capacidades em que confia plenamente.  Com a integração das APIs do Azure Active Directory no Power BI, pode transferir facilmente os pacotes de conteúdos pré-criadas e obter informações relativas a todas as atividades no seu Azure Active Directory com a avançada experiência de visualização que o Power BI oferece. Pode criar o seu próprio dashboard e partilhá-lo facilmente com qualquer pessoa da sua organização. 

Este tópico disponibiliza-lhe instruções passo a passo para instalar e utilizar o pacote de conteúdos no seu ambiente.

## <a name="installation"></a>Instalação  

**Para instalar o Pacote de Conteúdos do Power BI:**

1. Inicie sessão no [Power BI](https://app.powerbi.com/groups/me/getdata/services) com a sua Conta do Power BI (é a mesma conta que a sua conta do O365 ou do Azure AD).

2. Na parte inferior do painel de navegação esquerdo, selecione **Obter Dados**.

    ![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/01.png)
 
3. Na caixa de diálogo **Serviços**, clique em **Obter**.
   
    ![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/02.png)

4.  Procure **Azure Active Directory**.

    ![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/03.png)
 
5.  Quando lhe for pedido, escreva o seu ID de Inquilino do Azure AD e clique em **Seguinte**.

    > [!TIP] 
    > Uma forma rápida de obter o ID de Inquilino do seu inquilino do Office 365 / Azure AD é iniciar sessão no Portal do Azure AD, ir ao diretório e copiar o ID do URL https://manage.windowsazure.com/woodgroveonline.com#Workspaces/ActiveDirectoryExtension/Directory/<tenantid>/directoryQuickStart.

    ![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/04.png) 

6.  Clique em **entrar**. 
 
    ![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/05.png) 



7.  Introduza o nome de utilizador e a palavra-passe e clique em **Iniciar sessão**.
 
    ![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/06.png) 

8.  Na caixa de diálogo de consentimento da aplicação, clique em **Aceitar**.
 
9.  Quando o seu dashboard de registos de Atividades do Azure Active Directory estiver criado, clique no mesmo.
 
    ![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/08.png) 

## <a name="what-can-i-do-with-this-content-pack"></a>O que posso fazer com este pacote de conteúdos?

Antes de avançarmos para aquilo que pode fazer com este pacote de conteúdos, segue-se uma pré-visualização rápida dos diversos relatórios que o mesmo contém. Os dados dos relatórios remontam aos **últimos 30 dias**.

### <a name="reports-included-in-this-version-of-azure-active-directory-logs-content-pack"></a>Relatórios incluídos nesta versão do Pacote de Conteúdos de registos do Azure Active Directory

**Relatório de Utilização de Aplicações e Tendências**: obtenha informações sobre as aplicações utilizadas na sua organização e quais as que o são mais vezes e quando. Pode utilizar este relatório para recolher dados sobre como é que uma aplicação que tenha implementado recentemente na sua organização está a ser utilizada ou para descobrir quais são as aplicações mais populares. Com estas informações, pode melhorar a utilização, se vir que uma determinada aplicação não está a ser utilizada.

**Inícios de sessão por localização e utilizadores**: recolha informações sobre todos os inícios de sessão feitos com o Azure Identity, ao mesmo tempo que revela informações sobre a identidade dos utilizadores. Com estas informações, pode analisar mais aprofundadamente inícios de sessão individuais e responder a perguntas como:

- De onde é que este utilizador iniciou sessão?
- Que utilizador iniciou sessão mais vezes e de onde é que o fez? 
- O início de sessão foi bem-sucedido?  
 
Pode clicar numa data ou localização específica para ver informações mais detalhadas.

**Utilizadores exclusivos por aplicação**: veja todos os utilizadores exclusivos que estão a utilizar uma determinada aplicação. Inclui apenas os utilizadores que iniciaram sessão “*com êxito*” numa aplicação.

**Inícios de sessão de dispositivo**: veja que tipo de SO e de browsers os utilizadores da sua organização estão a utilizar, com informações detalhadas sobre eles, incluindo:

- Nome de Utilizador
- Endereço IP
- Localização 
- Estado de início de sessão 

Com este relatório, pode ficar a saber quais são os diversos perfis de dispositivos utilizados na sua organização e determinar políticas de dispositivos com base no que é utilizado.

**Funil de SSPR**: saiba como é que as reposições de palavras-passe são feitas na sua organização. Veja quantas tentativas de repor palavras-passe foram feitas com a ferramenta SSPR e quantas foram bem-sucedidas. Utilize o funil de SSPR (reposição personalizada de palavras-passe) para analisar os detalhes das falhas de Reposições de palavras-passe e ver por que motivo ocorreram determinadas falhas. Este relatório proporciona informações mais completas sobre a forma como a ferramenta SSPR é utilizada na sua organização, para que possa tomar as decisões certas.

## <a name="customizing-azure-ad-activity-content-pack"></a>Personalizar o Pacote de Conteúdos de Atividades do Azure AD

**Alterar a Visualização**:pode clicar em **Editar Relatório** para alterar a visualização de um relatório e selecionar a visualização que pretende.
 
![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/09.png) 
 
![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/10.png) 

**Incluir campos adicionais**: pode adicionar um campo ao relatório ou removê-lo ao selecionar o elemento visual ao qual quer adicionar/remover o campo. No exemplo a seguir, vamos adicionar o campo “sign-in status” à vista de tabela. 
 
![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/11.png) 

**Afixar visualizações ao dashboard**: pode personalizar o seu dashboard e incluir as suas próprias visualizações no relatório e afixá-las ao dashboard. No exemplo a seguir, adicionámos um filtro novo, com o nome “Sign-in Status”, e incluímo-lo no relatório. Também alterámos a visualização de gráfico de barras para gráfico de linhas e posso afixar este elemento visual novo ao dashboard.

![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/12.png) 

![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/13.png) 
 

 


**Partilhar o seu dashboard**: depois de criar o conteúdo que queria, pode partilhar o dashboard com os utilizadores da sua organização. Não se esqueça de que, depois de partilhar, eles podem ver os campos que selecionou no relatório.
 
![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/14.png) 



## <a name="scheduling-a-daily-refresh-of-your-power-bi-report"></a>Agendar uma atualização diária do seu relatório do Power BI

Para agendar uma atualização diária do seu relatório do Power BI, aceda a **Conjuntos de dados > Definições > Agendar Atualização** e defina-a conforme mostrado abaixo.
 
![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/15.png) 

## <a name="updating-to-newer-version-of-content-pack"></a>Atualizar para uma versão mais recente do pacote de conteúdos

Se quiser atualizar um pacote de conteúdos para obter uma versão mais recente:

- Transfira o pacote novo e configure-e de acordo com as instruções apresentadas neste artigo.

- Depois de concluída a configuração, aceda a **Origem de Dados > Definições > Credenciais da origem de dados**

    ![Pacote de Conteúdos do Power BI para o Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/16.png) 

Assim que a versão nova do pacote de conteúdos estiver em funcionamento, pode remover a antiga, se necessário, eliminando os relatórios subjacentes e os conjuntos de dados associados a esse pacote de conteúdos.

## <a name="still-having-issues"></a>Ainda está com problemas? 

Veja o nosso [guia de resolução de problemas](active-directory-reporting-troubleshoot-content-pack.md). Para obter ajuda geral com o Power BI, veja estes [artigos de ajuda](https://powerbi.microsoft.com/en-us/documentation/powerbi-service-get-started/).
 

## <a name="next-steps"></a>Passos seguintes

Para obter uma descrição geral dos relatórios, veja [Relatórios do Azure Active Directory](active-directory-reporting-azure-portal.md).
