---
title: "Criar os Serviços BizTalk do Azure no Portal do Azure | Microsoft Docs"
description: Saiba como aprovisionar ou criar os BizTalk Services do Azure no Portal do Azure; MABS, WABS
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 3ad18876-a649-40d6-9aa0-1509c1d62c43
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: eca77b4a82eb67e1755717bb4429f8d450a64dc5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-biztalk-services-using-the-azure-portal"></a>Criar os BizTalk Services com o Portal do Azure

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]


> [!TIP]
> Para iniciar sessão no Portal do Azure, deve ter uma conta e uma subscrição do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Veja [Avaliação Gratuita do Azure](http://go.microsoft.com/fwlink/p/?LinkID=239738).


## <a name="CreateService"></a>Criar um Serviço BizTalk
Nem todas as definições do BizTalk Service estarão disponíveis, consoante a Edição que escolher.

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação da parte inferior, selecione **NOVO**:  
   ![Selecionar o botão Novo][NEWButton]
3. Selecione **SERVIÇOS APLICACIONAIS** > **BIZTALK SERVICE** > **CRIAÇÃO PERSONALIZADA**:  
   ![Selecionar Serviço BizTalk e Criação Personalizada][NewBizTalkService]
4. Introduza as definições do BizTalk Service:
   
    <table border="1">
    <tr>
    <td><strong>Nome do Serviço BizTalk</strong></td>
    <td>Pode introduzir qualquer nome específico. Alguns exemplos incluem:<br/><br/>
    <em>minhaempresa</em>.biztalk.windows.net<br/>
    <em>minhaaplicaçãominhaempresa</em>.biztalk.windows.net<br/>
    <em>minhaaplicação</em>.biztalk.windows.net<br/><br/>“.biztalk.windows.net” é automaticamente adicionado ao nome que introduzir. Esta ação cria um URL utilizado para aceder ao BizTalk Service, como <strong>https://<em>minhaaplicação</em>. biztalk.windows.net</strong>.
    </td>
    </tr>
    <tr>
    <td><strong>Edição</strong></td>
    <td>Se estiver na fase de teste/desenvolvimento, escolha <strong>Programador</strong>. Se estiver na fase de produção, veja <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">BizTalk Services: Gráfico de Edições</a> para determinar se <strong>Premium</strong>, <strong>Standard</strong> ou <strong>Básico</strong> é a escolha certa para o seu cenário de negócio.
    </td>
    </tr>
    <tr>
    <td><strong>Região</strong></td>
    <td>Selecione a região geográfica para alojar o BizTalk Service.</td>
    </tr>
    <tr>
    <td><strong>URL do domínio</strong></td>
    <td><strong>Opcional</strong>. Por predefinição, o URL do domínio é <em>NomeDoBizTalkService</em>.biztalk.windows.net. Também pode introduzir um domínio personalizado. Por exemplo, se o seu domínio for <em>contoso</em>, pode introduzir: <br/><br/>
    <em>MinhaEmpresa</em>.contoso.com<br/>
    <em>MinhaAplicaçãoMinhaEmpresa</em>.contoso.com<br/>
    <em>MinhaAplicação</em>.contoso.com<br/>
    <em>NomeDoBizTalkService</em>.contoso.com<br/>
    </td>
    </tr>
    </table>
Selecione a seta SEGUINTE.
5. Introduza as definições do Armazenamento e da Base de Dados:  <table border="1">
    <tr>
    <td><strong>Conta de armazenamento de monitorização/arquivo</strong></td>
    <td>Selecione uma conta de armazenamento existente ou crie uma nova. <br/><br/>Se criar uma nova conta de armazenamento, introduza o <strong>Nome da Conta de Armazenamento</strong>.</td>
    </tr>
    <tr>
    <td><strong>Base de dados de controlo</strong></td>
    <td>Se utilizar uma SQL Database do Azure existente, esta não poderá ser utilizada por mais nenhum BizTalk Service. Introduza o mesmo nome de início de sessão e palavra-passe que utilizou aquando da criação da SQL Database do Azure.<br/><br/><strong>DICA</strong> Crie a Base de dados de controlo e a Conta de armazenamento de monitorização/arquivo na mesma região que o Serviço BizTalk.</td>
    </tr>
    </table>
Selecione a seta SEGUINTE.
6. Introduza as definições da Base de dados:  <table border="1">
    <tr>
    <td><strong>Nome</strong></td>
    <td>Disponível quando selecionar <strong>Criar uma nova instância da Base de dados SQL</strong> no ecrã anterior.
    <br/><br/>
Introduza um nome para a SQL Database que será utilizada pelo BizTalk Service.</td>
    </tr>
    <tr>
    <td><strong>Servidor</strong></td>
    <td>Disponível quando selecionar <strong>Criar uma nova instância da Base de dados SQL</strong> no ecrã anterior.
    <br/><br/>
Selecione um servidor da SQL Database existente ou crie um novo.</td>
    </tr>
    <tr>
    <td><strong>Nome de início de sessão do servidor</strong></td>
    <td>Introduza o nome de utilizador de início de sessão.</td>
    </tr>
    <tr>
    <td><strong>Palavra-passe de início de sessão do servidor</strong></td>
    <td>Introduza a palavra-passe de início de sessão.</td>
    </tr>
    <tr>
    <td><strong>Região</strong></td>
    <td>Disponível quando selecionar <strong>Criar uma nova instância da Base de dados SQL</strong>. Selecione a região geográfica para alojar a SQL Database.</td>
    </tr>
    </table>

Selecione a marca de verificação para concluir o assistente. É exibido o ícone de progresso:  
![O ícone de progresso é apresentado aquando da conclusão][ProgressComplete]

Quando terminar, o BizTalk Service do Azure é criado e estará pronto para as suas aplicações. As predefinições são suficientes. Se pretender alterar as predefinições, selecione **BIZTALK SERVICES** no painel de navegação esquerdo e, em seguida, selecione o seu BizTalk Service. São apresentadas definições adicionais nos separadores [Dashboard, Monitorização e Dimensionamento](biztalk-dashboard-monitor-scale-tabs.md), na parte superior.

Consoante o estado do BizTalk Service, há determinadas operações que não poderão ser concluídas. Para obter uma lista dessas operações, veja o [Gráfico de Estado dos BizTalk Services](biztalk-service-state-chart.md).

## <a name="post-provisioning-steps"></a>Passos pós-aprovisionamento
* [Instalar o certificado num computador local](#InstallCert)
* [Adicionar um certificado pronto para produção](#AddCert)
* [Obter o espaço de nomes do Controlo de Acesso](#ACS)

#### <a name="InstallCert"></a>Instalar o certificado num computador local
Como parte do aprovisionamento do BizTalk Service, é criado um certificado autoassinado e associado à sua subscrição do BizTalk Service. Tem de transferir este certificado e instalá-lo nos computadores a partir dos quais pretende implementar aplicações do BizTalk Service ou enviar mensagens para um ponto final do BizTalk Service.

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecione **BIZTALK SERVICES** no painel de navegação esquerdo e, em seguida, selecione a sua subscrição do BizTalk Service.
3. Selecione o separador **Dashboard**.
4. Selecione **Transferir Certificado SSL**:  
   ![Modificar o Certificado SSL][QuickGlance]
5. Faça duplo clique no certificado e execute o assistente para instalar o certificado. Confira se instala o certificado no arquivo **Autoridades de Certificados de Raiz Fidedigna**.

#### <a name="AddCert"></a>Adicionar um certificado pronto para produção
O certificado autoassinado criado automaticamente aquando da criação dos BizTalk Services destina-se apenas a ser utilizado em ambientes de desenvolvimento. Para cenários de produção, substitua-o pelo certificado pronto para produção.

1. No separador **Dashboard**, selecione **Atualizar Certificado SSL**.
2. Navegue até ao certificado SSL privado (*NomeDoCertificado*.pfx) que inclui o nome do seu BizTalk Service, introduza a palavra-passe e, em seguida, clique na marca de verificação.

#### <a name="ACS"></a>Obter o espaço de nomes do Controlo de Acesso
1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecione **BIZTALK SERVICES** no painel de navegação esquerdo e, em seguida, selecione o seu BizTalk Service.
3. Na barra de tarefas, selecione **Informações de Ligação**:  
   ![Selecionar Informações de Ligação][ACSConnectInfo]
4. Copie os valores do Controlo de Acesso.

Quando implementa um projeto dos BizTalk Services a partir do Visual Studio, introduza este espaço de nomes do Controlo de Acesso. O espaço de nomes do Controlo de Acesso é criado automaticamente para o seu BizTalk Service.

Pode utilizar os valores do Controlo de Acesso com qualquer aplicação. Após criar os BizTalk Services do Azure, este espaço de nomes do Controlo de Acesso controla a autenticação com a implementação do seu BizTalk Service. Se pretender alterar a subscrição ou gerir o espaço de nomes, selecione **ACTIVE DIRECTORY** no painel de navegação esquerdo e, em seguida, selecione o seu espaço de nomes. A barra de tarefas lista as suas opções.

Clique em **Gerir** para abrir o Portal de Gestão do Controlo de Acesso. No Portal de Gestão do Controlo de Acesso, o BizTalk Service utiliza as **Identidades do serviço**:  
![Identidades do Serviço ACS no Portal de Gestão do Controlo de Acesso][ACSServiceIdentities]

A identidade do Serviço do Controlo de Acesso é um conjunto de credenciais que permite a autenticação direta das aplicações ou dos clientes com o Controlo de Acesso e a receção de um token.

> [!IMPORTANT]
> O BizTalk Service utiliza **Proprietário** para a identidade predefinida do serviço e o valor **Palavra-passe**. Se utilizar o valor Chave Simétrica em vez do valor Palavra-passe, poderá ocorrer o seguinte erro.<br/><br/>*Não foi possível ligar à conta do Serviço de Gestão do Controlo de Acesso com as credenciais especificadas*
> 
> 

Em [Gerir o Espaço de Nomes do ACS](https://msdn.microsoft.com/library/azure/hh674478.aspx), pode ver uma lista de algumas diretrizes e recomendações.

## <a name="requirements-explained"></a>Requisitos explicados
Estes requisitos não se aplicam à Edição Gratuita.

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Do que precisa</strong></td>
        <td><strong>Porque precisa</strong></td>
</tr>
<tr>
<td>Subscrição do Azure</td>
<td>A subscrição determina quem pode iniciar sessão no Portal do Azure. O Titular da conta cria a subscrição em <a HREF="https://account.windowsazure.com/Subscriptions">Subscrições do Azure</a>.
<br/><br/>
A conta do Azure pode ter várias subscrições e pode ser gerida por qualquer pessoa autorizada. Por exemplo, o titular da conta do Azure cria uma subscrição com o nome <em>BizTalkServiceSubscription</em> e permite aos Administradores do BizTalk da sua empresa (por exemplo, ContosoBTSAdmins@live.com) acederem a esta subscrição. Neste cenário, os Administradores do BizTalk iniciam sessão no Portal do Azure e têm todos os direitos de Administrador para todos os serviços alojados na subscrição, incluindo os BizTalk Services do Azure. Os Administradores do BizTalk não são titulares da conta do Azure e, por conseguinte, não têm acesso a quaisquer informações de faturação.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577">Gerir Subscrições e Contas de Armazenamento no Portal do Azure</a> fornece mais informações.
</td>
</tr>
<tr>
<td>Base de Dados SQL do Azure</td>
<td>Armazena as tabelas, as vistas e os procedimentos armazenados utilizados pelo BizTalk Service, incluindo os Dados de controlo.
<br/><br/>
Quando cria um BizTalk Service, pode utilizar um Servidor SQL do Azure existente, uma SQL Database do Azure ou criar automaticamente um novo servidor ou base de dados.
<br/><br/>
O dimensionamento da SQL Database é configurado automaticamente. Normalmente, o dimensionamento predefinido é suficiente para um BizTalk Service. Alterar o dimensionamento terá impactos nos preços. Veja <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930">Contas e Faturação na Base de Dados SQL do Azure</a>
<br/><br/>
<strong>Notas</strong>
<br/>
<ul>
<li> Quando cria uma nova Base de Dados e um novo Servidor SQL do Azure, os Serviços do Azure são automaticamente ativados. Os BizTalk Services requerem a ativação dos Serviços do Azure.</li>
<li>Se criar uma nova SQL Database do Azure num Servidor SQL do Azure existente, as regras de firewall do servidor irão manter-se inalteradas. Por conseguinte, é possível que outros Serviços do Azure não tenham acesso às bases de dados do servidor.</li>
</ul>
</td>
</tr>
<tr>
<td>Espaço de nomes do Controlo de Acesso do Azure</td>
<td>É autenticado com o BizTalk Services do Azure. Quando implementa um projeto dos BizTalk Services a partir do Visual Studio, introduza este espaço de nomes do Controlo de Acesso. Quando cria um BizTalk Service, o espaço de nomes do Controlo de Acesso é criado automaticamente.</td>
</tr>

<tr>
<td>Conta de armazenamento do Azure</td>
<td>Permite aceder às tabelas, blobs e filas utilizados pelo BizTalk Service para guardar o seguinte:

<ul>
<li>Ficheiros de registo que monitorizam o BizTalk Service. O resultado da monitorização também é apresentado no separador **Monitorização** do Portal do Azure.</li>
<li>Ao criar um contrato X12 ou AS2 entre parceiros, pode ativar a funcionalidade Arquivo para armazenar as propriedades das mensagens. Estes dados são guardados na Conta de armazenamento.</li>
</ul>
<br/>
Quando cria um BizTalk Service, pode utilizar uma Conta de armazenamento existente ou criar automaticamente uma nova.
<br/><br/>
As predefinições do Armazenamento são suficientes para um BizTalk Service.
<br/><br/>
Quando cria uma Conta de armazenamento, são criadas automaticamente uma Chave Primária e uma Chave Secundária. Estas Chaves controlam o acesso à Conta de armazenamento. O BizTalk Service utiliza automaticamente a Chave Primária.
<br/><br/>
Para obter mais informações, veja <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">Armazenamento</a>.
</td>
</tr>

<tr>
<td>Certificado SSL privado</td>
<td>
Quando cria um BizTalk Service do Azure, também é criado um URL HTTPS com o nome do seu BizTalk Service. Este URL é configurado automaticamente para utilizar um certificado autoassinado apenas de desenvolvimento. Para a produção, precisa de um certificado SSL privado.
<br/><br/>
<strong>Informações importantes sobre o Certificado SSL</strong>

<ul>
<li>A data de expiração do certificado tem de ser inferior a cinco anos.</li>
<li>Todos os certificados privados requerem uma palavra-passe. Lembre-se desta palavra-passe e, como uma melhor prática, partilhe-a com os seus administradores.</li>
<li>Os certificados autoassinados são utilizados num ambiente de teste/desenvolvimento. Quando utilizar certificados autoassinados, importe o certificado para o seu arquivo de Certificados Pessoais e para o arquivo de Certificados de Autoridades de Certificação de Raiz Fidedigna.</li>
</ul>
<br/>Quando enviar o pedido de certificado de produção à autoridade de certificação, faculte as seguintes propriedades do certificado:
<br/>

<ul>
<li><strong>Utilização de Chave Avançada</strong>: no mínimo, os Serviços BizTalk do Azure requerem a Autenticação do Servidor.</li>
<li><strong>Nome Comum</strong>: introduza o nome de domínio completamente qualificado (FQDN) do URL do seu Serviço BizTalk do Azure. Veja <a HREF="#CreateService">Criar um BizTalk Service</a> neste artigo.</li>
</ul>
<br/>
Após criar o BizTalk Service, pode adicionar um novo certificado ou outro diferente.
</td>
</tr>
</table>
<!---Loc Comment: Please, check link [Create a BizTalk Service] since it is not redirecting to any location.--->



## <a name="hybrid-connections"></a>Ligações Híbridas
Quando cria um BizTalk Service do Azure, o separador **Ligações Híbridas** fica disponível:

![Separador Ligações Híbridas][HybridConnectionTab]

As Ligações Híbridas são utilizadas para ligar um site do Azure ou um serviço móvel do Azure a qualquer recurso no local que utilize uma porta TCP estática, como o SQL Server, o MySQL, as APIs Web de HTTP, os Mobile Services e a maioria dos Serviços Web personalizados.  As Ligações Híbridas e o BizTalk Adapter Service são diferentes. O BizTalk Adapter Service é utilizado para ligar os BizTalk Services do Azure a um sistema de Linha de Negócio (LOB) no local.

 Veja [Ligações Híbridas](integration-hybrid-connection-overview.md) para saber mais, incluindo como criar e gerir as Ligações Híbridas.

## <a name="next-steps"></a>Passos seguintes
Agora que criou um BizTalk Service, familiarize-se com os diferentes separadores em [BizTalk Services: separadores Dashboard, Monitorização e Dimensionamento](biztalk-dashboard-monitor-scale-tabs.md). O BizTalk Service está pronto para as suas aplicações. Para começar a criar aplicações, veja [BizTalk Services do Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Consultar também
* [Serviços BizTalk: Gráfico de Edições](biztalk-editions-feature-chart.md)<br/>
* [Serviços BizTalk: Gráfico de Estado](biztalk-service-state-chart.md)<br/>
* [Serviços BizTalk: Cópia de segurança e Restauro](biztalk-backup-restore.md)<br/>
* [Serviços BizTalk: limitação](biztalk-throttling-thresholds.md)<br/>
* [Serviços BizTalk: Nome e Chave do Emissor](biztalk-issuer-name-issuer-key.md)<br/>
* [Como posso começar a utilizar o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Ligações Híbridas](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
