<properties
   pageTitle="Executar qualquer aplicação do Windows em qualquer dispositivo com o Azure RemoteApp | Microsoft Azure"
   description="Saiba como partilhar qualquer aplicação do Windows com os seus utilizadores através do Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="07/02/2016"
   ms.author="elizapo"/>

# Executar qualquer aplicação do Windows em qualquer dispositivo com o Azure RemoteApp

Pode executar uma aplicação do Windows em qualquer lugar em qualquer dispositivo, neste momento, a sério – utilizando simplesmente o Azure RemoteApp. Seja uma aplicação personalizada escrita há 10 anos ou uma aplicação do Office, os utilizadores já não precisam de ficar vinculados a um sistema operativo específico (por exemplo, o Windows XP) para essas poucas aplicações.

Com o Azure RemoteApp, os utilizadores também podem utilizar os seus próprios dispositivos Android ou Apple e obter a mesma experiência que têm no Windows (ou em Windows Phone). Isto consegue-se ao alojar a sua aplicação de Windows numa coleção de máquinas virtuais de Windows via Azure – às quais os utilizadores podem aceder não importa de onde desde que tenham uma ligação à Internet. 

Continue a ler para obter um exemplo de como fazê-lo exatamente.

Neste artigo, vamos partilhar o Access com todos os nossos utilizadores. No entanto, pode utilizar QUALQUER aplicação. Desde que possa instalar a aplicação num computador com Windows Server 2012 R2, poderá partilhá-la seguindo os passos abaixo. Pode rever os [requisitos da aplicação](remoteapp-appreqs.md) para certificar-se de que a sua aplicação funcionará.

Como o Access é uma base de dados e queremos que a base de dados seja útil, vamos seguir alguns passos adicionais para permitir que os utilizadores acedam à partilha de dados do Access. Se a aplicação não é uma base de dados ou não precisa que os utilizadores acedam a uma partilha de ficheiros, pode ignorar esses passos neste tutorial

> [AZURE.NOTE] <a name="note"></a>Para concluir este tutorial, precisa de uma conta do Azure:
> - Pode [abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F): obterá créditos que pode utilizar para experimentar serviços pagos do Azure e, mesmo depois de os ter gasto, pode manter a conta e utilizar os serviços gratuitos do Azure, como Web Sites. O seu cartão de crédito não será cobrado, a menos que altere explicitamente as suas definições e peça para ser cobrado.
> - Pode [ativar os benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): a sua subscrição do MSDN dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure.


## Criar uma coleção no RemoteApp

Comece por criar uma coleção. A coleção funciona como um contentor para as aplicações e os utilizadores. Cada coleção baseia-se numa imagem – pode criar as suas ou utilizar uma fornecida com a sua subscrição. Para este tutorial, estamos a utilizar a imagem de avaliação do Office 2013 – ela contém a aplicação que pretendemos partilhar.

1. No Portal do Azure, desloque-se para baixo na árvore de navegação da esquerda até ver RemoteApp. Abra essa página.
2. Clique em **Criar uma coleção de RemoteApp**.
3. Clique em **Criação rápida** e introduza um nome para a coleção.
4. Selecione a região que pretende utilizar para criar a sua coleção. Para uma experiência otimizada, selecione a região mais próxima geograficamente da localização onde os utilizadores acederão à aplicação. Por exemplo, neste tutorial, os utilizadores estarão localizados em Redmond, Washington. A região do Azure mais próxima é **EUA Oeste**.
5. Selecione o plano de faturação que pretende utilizar. O plano de faturação básico coloca 16 utilizadores numa VM do Azure grande, enquanto que o plano de faturação padrão tem 10 utilizadores numa máquina virtual grande de Azure. Como um exemplo geral, o plano básico funciona muito bem para um fluxo de trabalho de tipo de entrada de dados. Para uma aplicação de produtividade, como o Office, seria aconselhável o plano padrão.
6. Por fim, selecione a imagem do Office 2013 Professional. Esta imagem contém aplicações do Office 2013. Apenas um lembrete – esta imagem só é boa para coleções de avaliação e provas de conceito. Não é possível utilizar esta imagem numa coleção de produção.
7. Agora, clique em **Criar uma coleção de RemoteApp**.

![Criar uma coleção em nuvem no RemoteApp](./media/remoteapp-anyapp/ra-anyappcreatecollection.png)

Esta ação inicia a criação da coleção, mas pode levar uma hora.

Agora está pronto para adicionar os seus utilizadores.

## Partilhar a aplicação com os utilizadores

Assim que a coleção foi criada com êxito, está na altura de publicar Access aos utilizadores e adicionar os utilizadores devem ter acesso a esta aplicação.

Se se tiver afastado do nó de Azure RemoteApp enquanto estava a criar a coleção, comece por regressar ao mesmo a partir da home page do Azure.

2. Clique na coleção que criou anteriormente para aceder às opções adicionais e configurar a coleção.
![Uma nova coleção de nuvem em RemoteApp](./media/remoteapp-anyapp/ra-anyappcollection.png)
3. No separador **Publicação**, clique em **Publicar** na parte inferior do ecrã e, em seguida, clique em **Publicar programas do menu de Início**.
![Publicar um programa RemoteApp](./media/remoteapp-anyapp/ra-anyapppublish.png)
4. Selecione as aplicações que pretende publicar a partir da lista. Para o nosso objetivo, escolhemos Access. Clique em **Concluído**. Aguarde que as aplicações concluam a publicação.
![Publicação de Access no RemoteApp](./media/remoteapp-anyapp/ra-anyapppublishaccess.png)


1. Depois da aplicação concluir a publicação, aceda ao separador **Acesso do Utilizador** para adicionar todos os utilizadores que precisam de aceder às suas aplicações. Introduza os nomes de utilizador (endereço de email) para os seus utilizadores e depois clique em **Guardar**.

![Adicionar utilizadores à RemoteApp](./media/remoteapp-anyapp/ra-anyappaddusers.png)


1. Agora, está na altura de informar os utilizadores sobre estas novas aplicações e indicar-lhes como aceder às mesmas. Para fazer isto, envie aos utilizadores uma mensagem de email que os aponte para o URL de transferência do cliente do Ambiente de Trabalho Remoto.
![O URL de transferência de cliente para RemoteApp](./media/remoteapp-anyapp/ra-anyappurl.png)

## Configurar o acesso ao Access

Algumas aplicações necessitam de uma configuração adicional depois implementá-las através do RemoteApp. Em particular, para o Access, vamos criar uma partilha de ficheiros no Azure à qual pode aceder qualquer utilizador. (Se não quiser fazê-lo, pode criar uma [coleção híbrida](remoteapp-create-hybrid-deployment.md) [em vez da nossa coleção na nuvem] que permita aos utilizadores aceder a ficheiros e informações na sua rede local.) Em seguida, temos de pedir aos nossos utilizadores para mapear uma unidade local nos respetivos computadores para o sistema de ficheiros de Azure.

Na primeira parte tem que fazer como administrador. Em seguida, temos alguns passos que os utilizadores devem seguir.

1. Comece por publicar a interface de linha de comandos (cmd.exe). No separador **Publicação** , selecione **cmd**, e, em seguida, clique em **Publicar > Publicar programa utilizando caminho**.
2. Introduza o nome da aplicação e o caminho. Para o nosso objetivo, utilize "Explorador de Ficheiros" como nome e "% SYSTEMDRIVE%\windows\explorer.exe" como caminho.
![Publique o ficheiro cmd.exe.](./media/remoteapp-anyapp/ra-publishcmd.png)
3. Agora tem de criar uma [Conta do Storage](../storage/storage-create-storage-account.md) de Azure. Chamámos à nossa "accessstorage", por isso, escolha um nome que faça sentido. (Citando incorretamente Highlander, só pode haver uma "accessstorage.") ![Nossa conta do Storage de Azure](./media/remoteapp-anyapp/ra-anyappazurestorage.png)
4. Agora, regresse ao seu dashboard para obter o caminho para o armazenamento (localização de ponto final). Voltará a utilizá-lo dentro de pouco, por isso, certifique-se de que o copie para algum lado.
![O caminho da conta do Storage](./media/remoteapp-anyapp/ra-anyappstoragelocation.png)
5. Em seguida, uma vez criada a conta do Storage, precisa da chave de acesso primária. Clique em **Gerir teclas de acesso** e depois copie a chave de acesso primária.
6. Agora, configure o contexto da conta do Storage e crie uma nova partilha de ficheiros para Access. Execute os cmdlets seguintes numa janela elevada do Windows PowerShell:

        $ctx=New-AzureStorageContext <account name> <account key>
        $s = New-AzureStorageShare <share name> -Context $ctx

    Por isso, para a nossa partilha, são estes os cmdlets que iremos executar:

        $ctx=New-AzureStorageContext accessstorage <key>
        $s = New-AzureStorageShare <share name> -Context $ctx


Agora, é a vez do utilizador. Primeiro, faça com que os utilizadores instalem um [cliente RemoteApp](remoteapp-clients.md). Em seguida, os utilizadores necessitam de mapear uma unidade a partir da respetiva conta para que a partilha de ficheiro Azure criado e adicionar os seus ficheiros do Access. É assim que devem fazê-lo:

1. No cliente do RemoteApp, aceda às aplicações publicadas. Inicie o programa cmd.exe.
2. Execute o comando seguinte para mapear uma unidade do seu computador para partilha de ficheiros:

        net use z: \\<accountname>.file.core.windows.net\<share name> /u:<user name> <account key>

    Se definir o parâmetro **/persistent** como Sim, a unidade mapeada persistirá em todas as sessões.
1. Agora, inicie a aplicação do Explorador de ficheiros a partir do RemoteApp. Copie os ficheiros de Access que pretende utilizar na aplicação partilhada à partilha de ficheiros.
![Colocação de ficheiros Access numa partilha Azure](./media/remoteapp-anyapp/ra-anyappuseraccess.png)
1. Por fim, abra o Access e, em seguida, abra a base de dados que acabou de partilhar. Deverá ver os seus dados no Access em execução a partir da nuvem.
![Uma base de dados real de Access em execução a partir da nuvem](./media/remoteapp-anyapp/ra-anyapprunningaccess.png)

Agora pode utilizar o Access em qualquer um dos seus dispositivos – basta certificar-se de que instala um cliente RemoteApp.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passos seguintes

Agora que já aprendeu a criar uma coleção, experimente criar uma [coleção que utiliza o Office 365](remoteapp-tutorial-o365anywhere.md). Ou antes, pode criar uma [coleção híbrida ](remoteapp-create-hybrid-deployment.md)que pode aceder à sua rede local.

<!--Image references-->
 



<!--HONumber=Aug16_HO1-->


