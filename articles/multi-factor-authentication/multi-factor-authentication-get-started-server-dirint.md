---
title: "Integração de diretórios entre o Multi-Factor Authentication do Azure e o Active Directory"
description: "Esta é a página do Multi-Factor Authentication do Azure, que descreve como integrar o Servidor Multi-Factor Authentication do Azure com o Active Directory para que possa sincronizar os diretórios."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: def7a534-cfb2-492a-9124-87fb1148ab1f
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/04/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: da64189de722b5ee3552530bb1276539e5c6c323


---
# <a name="directory-integration-between-azure-mfa-server-and-active-directory"></a>Integração de diretórios entre o Servidor MFA do Azure e o Active Directory
A secção Integração de Diretórios permite-lhe configurar o servidor para integrar com o Active Directory ou outro diretório LDAP.  Permite-lhe configurar atributos para fazer corresponder o esquema de diretório e configurar a sincronização automática de utilizadores.

## <a name="settings"></a>Definições
Por predefinição, o Servidor Multi-Factor Authentication do Azure está configurado para importar ou sincronizar os utilizadores do Active Directory.  O separador permite substituir o comportamento predefinido e vincular a um diretório LDAP diferente, a um diretório de ADAM ou a um controlador de domínio do Active Directory específico.  Também prevê a utilização de Autenticação LDAP para o LDAP do proxy ou para o Enlace de LDAP como um destino RADIUS, pré-autenticação para Autenticação do IIS ou autenticação primária para o Portal de Utilizador.  A tabela seguinte descreve as definições individuais.

![Definições](./media/multi-factor-authentication-get-started-server-dirint/dirint.png)

| Funcionalidade | Descrição |
| --- | --- |
| Utilizar o Active Directory |Selecione a opção Utilizar o Active Directory para utilizar o Active Directory para importação e sincronização.  Esta é a predefinição. <br>Nota: o computador tem de estar associado a um domínio e o utilizador tem de ter sessão iniciada com uma conta de domínio para que a integração do Active Directory funcione corretamente. |
| Incluir domínios fidedignos |Marque a caixa de verificação Incluir domínios fidedignos, para que o agente tente ligar a domínios que o domínio atual considere fidedignos, a outro domínio na floresta ou a domínios envolvidos numa confiança de floresta.  Quando não estiver a importar ou a sincronizar utilizadores de qualquer um dos domínios fidedignos, desmarque a caixa de verificação para melhorar o desempenho.  A predefinição é a caixa marcada. |
| Utilizar configuração de LDAP específica |Selecione a opção Utilizar LDAP para utilizar as definições de LDAP especificadas para a importação e a sincronização. Nota: quando a opção Utilizar LDAP está selecionada, a interface de utilizador altera as referências de Active Directory para LDAP. |
| Botão Editar |O botão Editar permite que as definições de configuração de LDAP atuais sejam modificadas. |
| Utilizar consultas de âmbito de atributos |Indica se devem ser utilizadas consultas de âmbito de atributos.  As consultas de âmbito de atributos permitem registos qualificativos de procuras em diretórios eficientes com base nas entradas no atributo de outro registo.  O Servidor Multi-Factor Authentication do Azure utiliza consultas de âmbito de atributos para consultar de forma eficiente os utilizadores que são membros de um grupo de segurança.   <br>Nota: existem alguns casos em que as consultas de âmbito de atributos são suportadas, mas não devem ser utilizadas.  Por exemplo, o Active Directory pode ter problemas com as consultas de âmbito de atributos quando um grupo de segurança contém membros de mais de um domínio.  Neste caso, a caixa de verificação deve estar desmarcada. |

A tabela seguinte descreve as definições de configuração de LDAP.

| Funcionalidade | Descrição |
| --- | --- |
| Servidor |Introduza o nome do anfitrião ou endereço IP do servidor que executa o diretório LDAP.  Também pode ser especificado um servidor de cópia de segurança, separado por ponto e vírgula. <br>Nota: quando o Tipo de enlace é SSL, geralmente é preciso um nome de anfitrião totalmente qualificado. |
| DN Base |Introduza o nome único do objeto de diretório base a partir do qual serão iniciadas todas as consultas de diretório.  Por exemplo, dc=abc,dc=com. |
| Tipo de enlace - Consultas |Selecione o tipo de enlace adequado para utilizar quando se vincula a procurar no diretório LDAP.  Este serve para importações, sincronização e resolução de nomes de utilizador. <br><br>  Anónimo - será feito um enlace anónimo.  DN do Enlace e Palavra-passe do Enlace não serão utilizados.  Isto irá funcionar apenas se o diretório LDAP permitir o enlace anónimo e as permissões permitirem a consulta dos registos e atributos adequados.  <br><br> Simples - o DN do Enlace e a Palavra-passe do Enlace serão transmitidos como texto simples para vincular ao diretório LDAP.  Esta opção só deve ser utilizada para fins de teste, para verificar se é possível aceder ao servidor e se a conta de enlace possui o acesso adequado.  Recomenda-se, em alternativa, a utilização de SSL depois de ter sido instalado o certificado adequado.  <br><br> SSL - o DN do Enlace e a Palavra-passe do Enlace serão encriptados com SSL para se vincularem ao diretório LDAP.  Isto requer que um certificado no qual o diretório confia seja instalado localmente.  <br><br> Windows - o DN do Enlace e a Palavra-passe do Enlace serão utilizados para ligar em segurança a um controlador de domínio do Active Directory ou ao diretório de ADAM.  Se o campo Nome de utilizador do enlace for deixado em branco, a conta do utilizador com sessão iniciada será utilizada para o enlace. |
| Tipo de enlace - Autenticações |Selecione o tipo de enlace adequado para utilizar quando efetuar a autenticação do enlace de LDAP.  Veja as descrições dos tipos de enlace em Tipo de enlace - Consultas.  Por exemplo, esta ação permite a utilização do enlace Anónimo para consultas enquanto o enlace de SSL serve para proteger as autenticações de enlace de LDAP. |
| DN do Enlace ou Nome de utilizador do enlace |Introduza o nome único do registo de utilizador para a conta a utilizar ao fazer o enlace ao diretório LDAP.<br><br>O nome único do enlace só é utilizado quando o Tipo de enlace for Simples ou SSL.  <br><br>Introduza o nome de utilizador da conta Windows a utilizar quando faz o enlace ao diretório LDAP quando o Tipo de enlace é Windows.  Se o campo for deixado em branco, a conta do utilizador com sessão iniciada será utilizada para o enlace. |
| Palavra-passe do Enlace |Introduza a palavra-passe do enlace para o nome de utilizador ou DN do Enlace que está a ser utilizado para fazer o enlace ao diretório LDAP.  Para configurar a palavra-passe para o Serviço AdSync do Servidor Multi-Factor Auth, a sincronização tem de estar ativada e o serviço tem de estar a ser executado no computador local.  A palavra-passe será guardada nos Nomes de utilizador e palavras-passe armazenadas do Windows, sob a conta como a qual o Serviço AdSync do Servidor Multi-Factor Auth está a ser executado.  A palavra-passe também será guardada sob a conta como a qual a interface de utilizador do Servidor Multi-Factor Auth está a ser executada e sob a conta como a qual o Serviço do Servidor Multi-Factor Auth está a ser executado.  <br><br> Nota: uma vez que a palavra-passe apenas é armazenada nos Nomes de utilizador e palavras-passe armazenadas do Windows do servidor local, este passo tem de ser executado em cada Servidor Multi-Factor Auth que precisa de acesso à palavra-passe. |
| Tamanho limite da consulta |Especifique o tamanho limite para o número máximo de utilizadores que uma pesquisa de diretório irá devolver.  Este limite deve corresponder à configuração no diretório LDAP.  Para grandes pesquisas onde a paginação não é suportada, a importação e a sincronização tentarão obter utilizadores em lotes.  Se o tamanho limite aqui especificado for maior do que o limite configurado no diretório LDAP, alguns utilizadores podem ser omitidos. |
| Botão Testar |Clique no botão Testar para testar o enlace ao servidor LDAP.  <br><br> Nota: a opção Utilizar LDAP não precisa de ser selecionada para testar o enlace.  Isto permite que o enlace seja testado antes de utilizar a configuração de LDAP. |

## <a name="filters"></a>Filtros
Os filtros permitem definir critérios para qualificar registos ao realizar uma pesquisa de diretório.  Ao definir o filtro, pode determinar o âmbito dos objetos que quer sincronizar.  

![Filtros](./media/multi-factor-authentication-get-started-server-dirint/dirint2.png)

O Multi-Factor Authentication do Azure tem as três opções seguintes.

* **Filtro de contentores** -Especifique os critérios de filtro utilizados para qualificar registos de contentores ao realizar uma pesquisa de diretório.  Para o Active Directory e ADAM, é geralmente utilizado (|(objectClass=organizationalUnit)(objectClass=container)).  Para outros diretórios LDAP, devem ser utilizados critérios de filtro que qualifiquem cada tipo de objeto de contentor, consoante o esquema do diretório.  <br>Nota: se for deixado em branco, será utilizado ((objectClass=organizationalUnit)(objectClass=container)) por predefinição.
* **Filtro de grupos de segurança** - Especifique os critérios de filtro utilizados para qualificar registos de grupos de segurança ao realizar uma pesquisa de diretório.  Para o Active Directory e ADAM, é geralmente utilizado (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)).  Para outros diretórios LDAP, devem ser utilizados critérios de filtro que qualifiquem cada tipo de objeto de grupo de segurança, consoante o esquema do diretório.  <br>Nota: se for deixado em branco, será utilizado (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)) por predefinição.
* **Filtro de utilizador** - Especifique os critérios de filtro utilizados para qualificar registos de utilizadores ao realizar uma pesquisa de diretório.  Para o Active Directory e ADAM, é geralmente utilizado (&(objectClass=user)(objectCategory=person)).  Para outros diretórios LDAP, deve ser utilizado (objectClass=inetOrgPerson) ou algo semelhante, consoante o esquema do diretório. <br>Nota: se for deixado em branco, será utilizado (&(objectCategory=person)(objectClass=user)) por predefinição.

## <a name="attributes"></a>Atributos
Os atributos podem ser personalizados conforme necessário para um diretório específico.  Isto permite-lhe adicionar atributos personalizados e ajustar a sincronização para apenas os atributos de que precisa.  O valor para cada campo de atributo deve corresponder ao nome do atributo, tal como definido no esquema do diretório.  Utilize a tabela abaixo para obter informações adicionais.

![Atributos](./media/multi-factor-authentication-get-started-server-dirint/dirint3.png)

| Funcionalidade | Descrição |
| --- | --- |
| Identificador exclusivo |Introduza o nome de atributo do atributo que funciona como o identificador exclusivo do contentor, do grupo de segurança e de registos de utilizador.  No Active Directory, normalmente é objectGUID.  Em outras implementações de LDAP, poderá ser entryUUID ou algo semelhante.  A predefinição é objectGUID. |
| ... Botões (Selecionar Atributo) |Cada campo de atributo tem um botão "..." ao lado do mesmo, que apresentará a caixa de diálogo Selecionar Atributo que permite a seleção de um atributo numa lista. <br><br>Caixa de diálogo Selecionar Atributo.<br><br>Nota: os atributos podem ser introduzidos manualmente e não precisam de corresponder a um atributo da lista de atributos. |
| Tipo de identificador exclusivo |Selecione o tipo do atributo de identificador exclusivo.  No Active Directory, o atributo objectGUID é do tipo GUID.  Em outras implementações de LDAP, poderá ser do tipo Matriz de Bytes ASCII ou Cadeia.  A predefinição é GUID. <br><br>Nota: é importante definir esta opção corretamente, uma vez que os Itens de sincronização são mencionados pelo respetivo Identificador exclusivo e o Tipo de identificador exclusivo serve para encontrar diretamente o objeto no diretório.  Definir este tipo como Cadeia quando, na verdade, o diretório armazena o valor como uma matriz de bytes de carateres ASCII irá impedir que a sincronização funcione corretamente. |
| Nome único |Introduza o nome de atributo do atributo que contém o nome único para cada registo.  No Active Directory, normalmente é distinguishedName.  Em outras implementações de LDAP, poderá ser entryDN ou algo semelhante.  A predefinição é distinguishedName. <br><br>Nota: se não existir um atributo que contenha apenas o nome único, pode ser utilizado o atributo adspath.  A parte "LDAP://<server>/" do caminho será removida automaticamente, deixando apenas o nome único do objeto. |
| Nome do contentor |Introduza o nome de atributo do atributo que contém o nome num registo de contentor.  O valor deste atributo será apresentado na Hierarquia de Contentores ao importar a partir do Active Directory ou ao adicionar itens de sincronização.  A predefinição é name. <br><br>Nota: se contentores diferentes utilizarem atributos diferentes para os respetivos nomes, podem ser especificados vários atributos de nome de contentor, separados por ponto e vírgula.  O primeiro atributo de nome de contentor encontrado num objeto de contentor será utilizado para apresentar o respetivo nome. |
| Nome do grupo de segurança |Introduza o nome de atributo do atributo que contém o nome num registo de grupo de segurança.  O valor deste atributo será apresentado na lista Grupos de Segurança ao importar a partir do Active Directory ou ao adicionar itens de sincronização.  A predefinição é name. |
| Utilizadores |Os atributos seguintes servem para procurar, apresentar, importar e sincronizar informações de utilizador a partir do diretório. |
| Nome de utilizador |Introduza o nome de atributo do atributo que contém o nome de utilizador num registo de utilizador.  O valor deste atributo será utilizado como o nome de utilizador do Servidor Multi-Factor Auth.  Um segundo atributo pode ser especificado como cópia de segurança do primeiro.  O segundo atributo só será utilizado se o primeiro atributo não incluir um valor para o utilizador.  As predefinições são userPrincipalName e sAMAccountName. |
| Nome próprio |Introduza o nome de atributo do atributo que contém o nome próprio num registo de utilizador.  A predefinição é givenName. |
| Apelido |Introduza o nome de atributo do atributo que contém o apelido num registo de utilizador.  A predefinição é sn. |
| Endereço de e-mail |Introduza o nome de atributo do atributo que contém o endereço de e-mail num registo de utilizador.  O endereço de e-mail servirá para enviar mensagens de e-mail de boas-vindas e de atualização ao utilizador.  A predefinição é mail. |
| Grupo de utilizadores |Introduza o nome de atributo do atributo que contém o grupo de utilizadores num registo de utilizador.  O grupo de utilizadores pode ser utilizado para filtrar os utilizadores no agente e em relatórios no Portal de Gestão do Servidor Multi-Factor Auth. |
| Descrição |Introduza o nome de atributo do atributo que contém a descrição num registo de utilizador.  A descrição só é utilizada para procurar.  A predefinição é description. |
| Idioma da chamada telefónica |Introduza o nome de atributo do atributo que contém o nome abreviado do idioma a utilizar para chamadas de voz do utilizador. |
| Idioma da mensagem de texto SMS |Introduza o nome de atributo do atributo que contém o nome abreviado do idioma a utilizar para mensagens de texto SMS do utilizador. |
| Idioma da aplicação móvel |Introduza o nome de atributo do atributo que contém o nome abreviado do idioma a utilizar para mensagens de texto da aplicação para telemóvel do utilizador. |
| Idioma do token OATH |Introduza o nome de atributo do atributo que contém o nome abreviado do idioma a utilizar para mensagens de texto do token OATH do utilizador. |
| Telefones |Os atributos seguintes servem para importar ou sincronizar números de telefone do utilizador.  Se não for especificado um nome de atributo para o tipo de telefone, o tipo de telefone não estará disponível quando importar do Active Directory ou adicionar itens de sincronização. |
| Empresa |Introduza o nome de atributo do atributo que contém o número de telefone da empresa num registo de utilizador.  A predefinição é telephoneNumber. |
| Casa |Introduza o nome de atributo do atributo que contém o número de telefone de casa num registo de utilizador.  A predefinição é homePhone. |
| Pager |Introduza o nome de atributo do atributo que contém o número de pager num registo de utilizador.  A predefinição é pager. |
| Telemóvel |Introduza o nome de atributo do atributo que contém o número de telemóvel num registo de utilizador.  A predefinição é mobile. |
| Fax |Introduza o nome de atributo do atributo que contém o número de fax num registo de utilizador.  A predefinição é facsimileTelephoneNumber. |
| Número de IP |Introduza o nome de atributo do atributo que contém o número de telefone de IP num registo de utilizador.  A predefinição é ipPhone. |
| Personalizado |Introduza o nome de atributo do atributo que contém um número de telefone personalizado num |
| registo de utilizador.  A predefinição é blank. | |
| Extensão |Introduza o nome de atributo do atributo que contém a extensão de número de telemóvel num registo de utilizador.  O valor do campo de extensão será utilizado como a extensão apenas do número de telefone principal.  A predefinição é blank. <br><br>Nota: se o atributo Extensão não for especificado, as extensões podem ser incluídas como parte do atributo de telefone.  A extensão deve ser precedida de «x», de modo a que possa ser analisada.  Por exemplo, 555-123-4567 x890 resultaria em 555-123-4567 como o número de telefone e 890 como a extensão. |
| Botão Restaurar Predefinições |Clique no botão Restaurar Predefinições para voltar a colocar todos os atributos no respetivo valor predefinido.  As predefinições devem funcionar corretamente com o esquema normal do Active Directory ou do ADAM. |

Para editar atributos, basta clicar no botão Editar no separador Atributos.  Será apresentada uma janela que lhe permite editar os atributos.

![Editar Atributos](./media/multi-factor-authentication-get-started-server-dirint/dirint4.png)

## <a name="synchronization"></a>Sincronização
A sincronização mantém a base de dados de utilizador do Multi-Factor do Azure sincronizada com os utilizadores do Active Directory ou de outro diretório de protocolo LDAP (Lightweight Directory Access Protocol).  O processo é semelhante à importação manual de utilizadores do Active Directory, mas, periodicamente, consulta alterações ao grupo de segurança e ao utilizador do Active Directory para processar.  Também prevê a desativação ou a remoção de utilizadores removidos de um contentor ou grupo de segurança ou a remoção de utilizadores eliminados do Active Directory.

O serviço ADSync do Multi-Factor Auth é um serviço Windows que executa a consulta periódica do Active Directory.  Não deve ser confundido com o Azure AD Sync ou o Azure AD Connect.  O ADSync do Multi-Factor Auth, embora incorporado numa base de código semelhante, é específico do Servidor Multi-Factor Authentication do Azure.  É instalado num estado parado e é iniciado pelo serviço do Servidor Multi-Factor Auth quando configurado para execução.  Se tiver uma configuração de Servidor Multi-Factor Auth multisservidor, o ADSync do Multi-Factor Auth só pode ser executado num único servidor.

O serviço ADSync do Multi-Factor Auth utiliza a extensão de servidor LDAP DirSync fornecida pela Microsoft para consultar alterações de forma eficiente.  Este chamador de controlo DirSync tem de ter o direito "directory get changes" e o direito de acesso de controlo expandido DS-Replication-Get-Changes.  Por predefinição, estes direitos são atribuídos às contas de Administrador e LocalSystem nos controladores de domínio.  O serviço AdSync do Multi-Factor Auth está configurado para ser executado como LocalSystem, por predefinição.  Por isso, é mais simples executar o serviço num controlador de domínio.  O serviço pode ser executado como uma conta com permissões menores, caso a configure para fazer sempre uma sincronização completa.  É menos eficiente, mas requer menos privilégios de conta.

Se for configurado para utilizar LDAP e o diretório LDAP suportar o controlo DirSync, a consulta de alterações a grupos de segurança e utilizadores funcionará da mesma forma que funciona com o Active Directory.  Se o diretório LDAP não suportar o controlo DirSync, será executada uma sincronização completa durante cada ciclo.

![Sincronização](./media/multi-factor-authentication-get-started-server-dirint/dirint5.png)

Utilize a tabela abaixo para obter informações adicionais sobre cada uma das definições individuais no separador Sincronização.

| Funcionalidade | Descrição |
| --- | --- |
| Ativar a sincronização com o Active Directory |Quando selecionada, o serviço Servidor Multi-Factor Auth será iniciado para consultar periodicamente alterações ao Active Directory. <br><br>Nota: tem de ser adicionado, pelo menos, um Item de Sincronização e uma tarefa Sincronizar Agora tem de ser realizada antes de o serviço Servidor Multi-Factor Auth começar a processar alterações. |
| Sincronizar a cada |Especifique o intervalo de tempo que o serviço Servidor Multi-Factor Auth irá esperar entre consultas e processamento de alterações. <br><br> Nota: o intervalo especificado corresponde ao tempo entre o início de cada ciclo.  Se o tempo de processamento de alterações exceder o intervalo, o serviço voltará a consultar de imediato. |
| Remover utilizadores que já não constem no Active Directory |Quando selecionada, o serviço Servidor Multi-Factor Auth irá processar tombstones de utilizadores eliminados do Active Directory e remover o utilizador do Servidor Multi-Factor Auth relacionado. |
| Efetuar sempre uma sincronização completa |Quando selecionada, o serviço Servidor Multi-Factor Auth irá efetuar sempre uma sincronização completa.  Quando não estiver selecionada, o serviço Servidor Multi-Factor Auth irá efetuar uma sincronização incremental, consultando apenas os utilizadores que tenham sido alterados.  A predefinição é a caixa desmarcada. <br><br> Nota: quando desmarcada, uma sincronização incremental só pode ser executada quando o diretório suportar o controlo DirSync e a conta que está a ser utilizada para o enlace ao diretório tiver as permissões adequadas para fazer consultas incrementais DirSync.  Se a conta não tiver as permissões adequadas ou vários domínios estiverem envolvidos na sincronização, recomenda-se a execução de uma sincronização completa. |
| Exigir aprovação do administrador quando o número de utilizadores desativados ou removidos exceder o limiar |Os itens de sincronização podem ser configurados para desativar ou remover utilizadores que já não são membros do grupo de segurança ou do contentor do item.  Como salvaguarda, a aprovação do administrador pode ser exigida quando o número de utilizadores a desativar ou a remover exceder um limiar.  Quando estiver marcada, será exigida aprovação para o limiar especificado.  A predefinição é 5 e o intervalo é de 1 a 999. <br><br> A aprovação é facilitada ao enviar primeiro uma notificação por e-mail aos administradores. A notificação por e-mail dá instruções para rever e aprovar a desativação e remoção de utilizadores.  Quando a interface de utilizador do Servidor Multi-Factor Auth for iniciada, pedirá aprovação. |

O botão **Sincronizar Agora** permite executar uma sincronização completa para os itens de sincronização especificados.  Uma sincronização completa é precisa sempre que forem adicionados, modificados, removidos ou reordenados itens de sincronização.  Também é precisa para que o serviço AdSync do Multi-Factor Auth fique operacional, uma vez que define o ponto de partida a partir do qual o serviço irá consultar as alterações incrementais.  Se tiverem sido feitas alterações a itens de sincronização e não tiver sido feita uma sincronização completa, ser-lhe-á pedido para Sincronizar Agora quando navegar para outra secção ou quando fechar a interface de utilizador.

O botão **Remover** permite ao administrador eliminar um ou mais itens de sincronização da lista de itens de sincronização do Servidor Multi-Factor Auth.

> [!WARNING]
> Assim que um registo de item de sincronização tiver sido removido, não pode ser recuperado. Terá de voltar a adicionar o registo do item de sincronização, caso o tenha eliminado por engano.
> 
> 

O item de sincronização ou os itens de sincronização foram removidos do Servidor Multi-Factor Auth.  O serviço Servidor Multi-Factor Auth já não irá processar os itens de sincronização.

Os botões Mover para cima e Mover para baixo permitem ao administrador alterar a ordem dos itens de sincronização.  A ordem é importante, uma vez que o mesmo utilizador poderá ser membro de mais do que um item de sincronização (por exemplo, um contentor e um grupo de segurança).  As definições aplicadas ao utilizador durante a sincronização serão provenientes do primeiro item de sincronização na lista ao qual o utilizador está associado.  Por isso, os itens de sincronização devem ser colocados por ordem de prioridade.

> [!TIP]
> Depois de remover itens de sincronização, deve fazer uma sincronização completa.  Depois de ordenar itens de sincronização, deve fazer uma sincronização completa.  Clique no botão Sincronizar Agora para fazer uma sincronização completa.
> 
> 

## <a name="multifactor-auth-servers"></a>Servidores Multi-Factor Auth
Podem ser configurados Servidores Multi-Factor Auth adicionais para funcionarem como cópia de segurança do proxy RADIUS, do proxy LDAP ou para Autenticação do IIS. A configuração da Sincronização será partilhada entre todos os agentes. No entanto, apenas um destes agentes poderá ter o serviço Servidor Multi-Factor Auth em execução. Este separador permite selecionar o Servidor Multi-Factor Auth que deve estar ativado para sincronização.

![Servidores Multi-Factor-Auth](./media/multi-factor-authentication-get-started-server-dirint/dirint6.png)




<!--HONumber=Nov16_HO2-->


