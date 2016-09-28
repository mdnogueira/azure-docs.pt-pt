<properties
    pageTitle="O que existe nas imagens de modelo do Azure RemoteApp? | Microsoft Azure"
    description="Saiba mais sobre as imagens de modelo incluídas no Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# O que existe nas imagens de modelo do Azure RemoteApp?

> [AZURE.IMPORTANT]
> O Azure RemoteApp está a ser descontinuado. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

A sua subscrição do Azure RemoteApp inclui três imagens de modelo:


- Windows Server 2012
- Microsoft Office 365 ProPlus (é necessária uma subscrição do Office 365)
- Microsoft Office 2013 Professional Plus (versão de avaliação apenas)

> [AZURE.IMPORTANT]A sua subscrição do Azure RemoteApp concede-lhe o acesso ao software nas imagens, exceto o Office 365 ProPlus, que necessita de uma subscrição separada, e o Office 2013, que não pode ser utilizado em produção. Tal significa que pode partilhar os programas ou as aplicações nas imagens de modelo com os seus utilizadores. Por exemplo, se criar uma coleção que utilize a imagem do Windows Server 2012 R2, pode publicar o System Center Endpoint Protection para que os utilizadores acedam através do RemoteApp.
>
> Para obter mais informações, consulte os [detalhes de licenciamento do RemoteApp](remoteapp-licensing.md). Consulte também [Utilizar o Office com o Azure RemoteApp](remoteapp-o365.md) para obter as informações de licenciamento do Office.

Continue a ler para obter detalhes sobre o conteúdo de cada imagem.

## Windows Server 2012 R2 (“a imagem clássica”)
Esta imagem baseia-se no sistema operativo Microsoft Windows Server 2012 R2 Datacenter e tem as seguintes funções e funcionalidades instaladas para cumprir os requisitos das imagens de modelo do Azure RemoteApp:


- .NET Framework 4.5, 3.5.1, 3.5
- Experiência de Utilização do Computador
- Serviços de Tinta Digital e Escrita Manual
- Media Foundation
- Anfitrião de Sessões de Ambiente de Trabalho Remoto
- Windows PowerShell 4.0
- ISE do Windows PowerShell
- Suporte WoW64

Esta imagem tem também as seguintes aplicações instaladas:

- Adobe Flash Player
- Microsoft Silverlight
- Microsoft System Center 2012 Endpoint Protection
- Microsoft Windows Media Player


## Microsoft Office 365 ProPlus (é necessária uma subscrição)
O Office 365 é a aplicação mais solicitada, pelo que criámos uma imagem “personalizada” para poder trabalhar com ela.

Esta imagem é uma extensão da imagem clássica e tem os seguintes componentes do Microsoft Office 365 ProPlus instalados além dos componentes descritos na imagem do Windows Server 2012 R2:


- Access
- Excel
- Lync
- OneNote
- OneDrive para Empresas (tenha em atenção que o agente de sincronização não é suportado para utilização com o Azure RemoteApp)
- Outlook
- PowerPoint
- Word
- Ferramentas de Verificação do Microsoft Office

A imagem também inclui o Visio Pro e o Project Pro.

E ainda as seguintes aplicações:

- SQL Native Client
- Controlador ODBC
- SQL Server Data Mining cliente
- MasterDataServices cliente
- Microsoft Publisher
- PowerQuery
- Power Map


A funcionalidade completa das aplicações do Office 365 ProPlus está apenas disponível para utilizadores com um plano do Office 365 ProPlus. Para obter mais detalhes dos planos de subscrição do Office 365, consulte [Planos de serviço do Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx). Ainda tem perguntas? Consulte as informações do [Office 365 + RemoteApp](remoteapp-o365.md). Consulte também o novo artigo [Como utilizar a sua subscrição do Office 365 com o Azure RemoteApp](remoteapp-officesubscription.md).

Tenha em atenção de que precisa de uma licença separada para o Office 365 ProPlus, o Visio Pro e o Project Pro, cada um tem a sua própria licença.

## Microsoft Office 2013 Professional Plus (versão de avaliação apenas)
Durante o período de avaliação gratuita, pode testar o serviço com a imagem do Office 2013.

Esta imagem é uma extensão da imagem clássica e tem os seguintes componentes do Microsoft Office 2013 Professional Plus instalados além dos componentes descritos na imagem do Windows Server 2012 R2:


- Access
- Excel
- Lync
- OneNote
- OneDrive para Empresas (tenha em atenção que o agente de sincronização não é suportado para utilização com o Azure RemoteApp)
- Outlook
- PowerPoint
- Project
- Visio
- Word
- Ferramentas de Verificação do Microsoft Office

> [AZURE.IMPORTANT]**Informação legal:** esta imagem não inclui uma licença do Microsoft Office e *não pode ser utilizada para produção*. A imagem do Office 2013 Professional Plus destina-se apenas a uma utilização de avaliação. Caso pretenda utilizar aplicações do Office no Azure RemoteApp para produção, tem de utilizar a imagem do Office 365 ProPlus. Para obter mais detalhes sobre o licenciamento do Office, consulte [Utilizar o Office 365 com o Azure RemoteApp](remoteapp-o365.md)



<!--HONumber=Sep16_HO3-->


