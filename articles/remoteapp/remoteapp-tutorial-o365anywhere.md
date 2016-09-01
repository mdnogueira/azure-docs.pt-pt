<properties
   pageTitle="Obter a mesma experiência do Office 365 em qualquer dispositivo com o Azure RemoteApp | Microsoft Azure"
   description="Saiba como partilhar qualquer aplicação do Office 365 com os utilizadores através do Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="guscatal;elizapo"/>


# Obter a mesma experiência do Office 365 em qualquer dispositivo com o Azure RemoteApp

> [AZURE.IMPORTANT]
> O Azure RemoteApp está a ser descontinuado. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Este artigo abordará como implementar o Office 365 em qualquer dispositivo na sua empresa. Os utilizadores podem obter as mesmas capacidades e experiência da IU no Android, Apple e Windows.

Tal será possível através do Azure RemoteApp ao alojar o Office 365 em máquinas virtuais dimensionáveis no Azure às quais os utilizadores podem estabelecer uma ligação. Este conjunto de máquinas virtuais tem a designação de "coleção na nuvem".

## Criar uma coleção na nuvem

Imediatamente após criar uma conta do Azure, navegue para o **RemoteApp** clicando na ligação do lado esquerdo.
![Mostrar o Azure RemoteApp no Portal do Azure](./media/remoteapp-tutorial-o365anywhere/1-menu.png)

Em seguida, continue clicando em **nova** na parte inferior e "criando rapidamente" uma coleção. Indique um nome, a região, a subscrição, o plano e a imagem "Office Professional 2013" fornecida por nós.
![Criar diálogo](./media/remoteapp-tutorial-o365anywhere/2-quickcreate.png)

Após preencher o formulário, o processo de criação da coleção deverá iniciar. Este poderá demorar até, aproximadamente, uma hora.

![A aguardar](./media/remoteapp-tutorial-o365anywhere/3-waiting.png)

Após a conclusão do processo, o aspeto será semelhante a este. Se clicarmos em **Publicação**, podemos ver que muitas aplicações do Office já foram publicadas por nós.
![Coleção criada](./media/remoteapp-tutorial-o365anywhere/4-done.png)

![Aplicações publicadas](./media/remoteapp-tutorial-o365anywhere/5-publish.png)

Neste momento também pode adicionar mais utilizadores com acesso a esta coleção clicando em **Acesso de Utilizador**.
![Configurar o acesso de utilizador](./media/remoteapp-tutorial-o365anywhere/6-user.png)

Vamos agora tentar estabelecer ligação ao Office 365!

## Ligar ao Office 365

Aceda a [https://www.remoteapp.windowsazure.com/](https://www.remoteapp.windowsazure.com/), desloque-se para baixo e clique em **Transferir clientes** para instalar o cliente do Azure RemoteApp no seu dispositivo atual. As capturas de ecrã a seguir dizem respeito ao Windows.

Após a aplicação iniciar, ser-lhe-á pedido para iniciar sessão com a sua conta Microsoft (anteriormente designada "Live ID"). Utilize, por agora, a mesma para a sua conta do Azure. Quando tiver sessão iniciada, deverá ver uma notificação de novos convites. Clique na mesma e deverá ser apresentada uma lista semelhante à lista a seguir. Aceite o convite correspondente ao e-mail do proprietário da conta do Azure.

![Novo convite](./media/remoteapp-tutorial-o365anywhere/7-araclient.png)

Este é o aspeto quando existem novos convites.

![Aceitar uma aplicação](./media/remoteapp-tutorial-o365anywhere/8-invitation.png)

Após aceitar o convite, deverá ver todas as aplicações do Office no cliente do Azure RemoteApp.

![Lista de aplicações](./media/remoteapp-tutorial-o365anywhere/9-work.png)

Ao clicar em qualquer uma destas, a aplicação deverá iniciar na máquina virtual do Azure e deverá estar tudo a funcionar! Divirta-se!

![a iniciar](./media/remoteapp-tutorial-o365anywhere/10-arastart.png)

![powerpoint](./media/remoteapp-tutorial-o365anywhere/11-pp.png)



<!--HONumber=ago16_HO4-->


