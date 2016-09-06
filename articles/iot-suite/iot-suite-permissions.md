<properties
  pageTitle="Azure IoT Suite e Azure Active Directory | Microsoft Azure"
  description="Descreve como o Azure IoT Suite utiliza o Azure Active Directory para gerir permissões."
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="get-started-article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="05/16/2016"
  ms.author="araguila"/>
  
# Permissões no site azureiotsuite.com

## O que acontece quando inicia sessão

Quando inicia sessão pela primeira vez em [azureiotsuite.com][lnk-azureiotsuite], o site determina os seus níveis de permissão com base no inquilino do Azure Active Directory (AAD) e na subscrição do Azure atualmente selecionados.

1.  Primeiro, o site localiza os inquilinos do AAD ao quais pertence através do Azure para preencher a lista de inquilinos junto ao seu nome de utilizador com sessão iniciada. Neste momento, o site apenas pode obter os tokens de utilizador para um inquilino de cada vez. Consequentemente, quando muda para um inquilino diferente através da lista pendente no canto superior direito, o site volta a iniciar a sua sessão nesse inquilino para obter os tokens do mesmo.

2.  Em seguida, através do Azure, o site localiza as subscrições às quais o inquilino selecionado está associado. Pode ver as subscrições disponíveis quando cria uma nova solução pré-configurada.

3.  Por último, o site obtém todos os recursos nas subscrições e nos grupos de recursos marcados como soluções pré-configuradas e preenche os mosaicos na home page.

As secções seguintes descrevem as funções que controlam o acesso às soluções pré-configuradas.

## Funções do AAD

As funções do AAD controlam a capacidade de aprovisionar soluções pré-configuradas e gerir utilizadores numa solução pré-configurada.

Pode encontrar mais informações sobre funções de administrador no AAD em [Atribuir funções de administrador no Azure AD][lnk-aad-admin], mas este artigo foca-se principalmente nas funções de **Administrador Global** e de **Utilizador/Membro do Domínio**, conforme utilizadas pelas soluções pré-configuradas.

**Administrador Global:** podem existir vários administradores globais por cada inquilino do AAD. Quando cria um inquilino do AAD, por predefinição, é o administrador global desse inquilino. O administrador global pode aprovisionar uma solução pré-configurada e é-lhe atribuída uma função de **ADMINISTRADOR** para a aplicação no seu inquilino do AAD. No entanto, se outro utilizador no mesmo inquilino do AAD criar uma aplicação, a função predefinida concedida ao administrador global é **SÓ DE LEITURA IMPLÍCITO**. Os administradores globais podem atribuir funções para aplicações através do [portal clássico do Azure][lnk-classic-portal].

**Utilizador/Membro do Domínio:** podem existir vários utilizadores/membros do domínio por inquilino do AAD. Um utilizador do domínio pode aprovisionar uma solução pré-configurada através do site [azureiotsuite.com][lnk-azureiotsuite]. A função predefinida que lhe é concedida para a aplicação que aprovisiona é **ADMINISTRADOR**. Pode criar uma aplicação através do script build.cmd no repositório [azure-iot-remote-monitoring][lnk-rm-github-repo] ou [azure-iot-predictive-maintenance][lnk-pm-github-repo], mas a função predefinida que lhe é concedida é **SÓ DE LEITURA IMPLÍCITO**, uma vez que não tem permissão para atribuir funções. Se outro utilizador no inquilino do AAD criar uma aplicação, por predefinição, é-lhe atribuída a função **SÓ DE LEITURA IMPLÍCITO**. Este não tem a capacidade de atribuir funções para aplicações. Desta forma, não pode adicionar utilizadores ou funções de utilizador para uma aplicação, mesmo que a tenha aprovisionado.

**Utilizador Convidado/Convidado:** podem existir vários utilizadores convidados/convidados por inquilino do AAD. Os utilizadores convidados têm um conjunto limitado de direitos no inquilino do AAD. Como resultado, os utilizadores convidados não podem aprovisionar uma solução pré-configurada no inquilino do AAD.

Para obter mais informações, consulte os seguintes recursos:

- [Criar ou Editar utilizadores no Azure AD][lnk-create-edit-users]
- [Atribuir funções de aplicações no AAD][lnk-assign-app-roles]

## Funções de administrador de subscrição do Azure

As funções de administrador do Azure controlam a capacidade de mapear uma subscrição do Azure para um inquilino do AD.

Pode encontrar mais informações sobre as funções de Coadministrador do Azure, Administrador de Serviço e Administrador de Conta no artigo [How to add or change Azure Co-Administrator, Service Administrator and Account Administrator (Como adicionar ou alterar o Coadministrador do Azure, o Administrador de Serviço e o Administrador de Conta)][lnk-admin-roles].

## Funções da aplicação

As funções de aplicação controlam o acesso a dispositivos na sua solução pré-configurada.

Estão estipuladas duas funções definidas e uma implícita na aplicação criada quando aprovisiona uma solução pré-configurada.

-   **ADMINISTRADOR:** tem controlo total para adicionar, gerir e remover dispositivos

-   **SÓ DE LEITURA:** tem a capacidade de ver os dispositivos

-   **SÓ DE LEITURA IMPLÍCITO:** semelhante a Só de Leitura, mas é concedida a todos os utilizadores do seu inquilino do AAD. Tal acontece por motivos de comodidade durante o desenvolvimento. Pode remover esta função ao modificar o ficheiro de origem [RolePermissions.cs][lnk-resource-cs].

### Alterar funções da aplicação para um utilizador

Pode utilizar o procedimento seguinte para tornar um utilizador do seu Active Directory administrador da solução pré-configurada.

Tem de ser um administrador global do AAD para alterar as funções de um utilizador:

1. Aceda ao [portal clássico do Azure][lnk-classic-portal].

2. Selecione **Active Directory**.

3. Clique no nome do seu inquilino do AAD (este é o diretório que escolheu em azureiotsuite.com quando aprovisionou a sua solução).

4. Clique em **Aplicações**.

5. Clique no nome da aplicação que corresponde ao nome da sua solução pré-configurada. Se não vir a sua aplicação na lista, mude a lista pendente **Mostrar** para **Aplicações que a minha empresa detém** e clique na marca de verificação.

7. Clique em **Utilizadores**.

8. Selecione o utilizador cuja função pretende alterar.

9. Clique em **Atribuir**, selecione a função que pretende atribuir ao utilizador (tal como **Administrador**) e clique na marca de verificação.

## FAQ

### Sou administrador de serviço e gostaria de alterar o mapeamento de diretório entre a minha subscrição e um inquilino do AAD específico. Como posso fazê-lo?

1. Aceda ao [portal clássico do Azure][lnk-classic-portal] e clique em **Definições** na lista de serviços no lado esquerdo.

2. Selecione a subscrição para a qual pretende alterar o mapeamento de diretório.

3. Clique em **Editar Diretório**.

4. Selecione o **Diretório** que pretende utilizar na lista pendente. Clique na seta de avanço.

5. Confirme o mapeamento de diretório e os coadministradores afetados. Tenha em atenção que, se estiver a efetuar uma mudança a partir de outro diretório, todos os coadministradores do diretório original são removidos.

### Sou utilizador/membro do domínio no inquilino do AAD e criei uma solução pré-configurada. Como posso obter a atribuição de uma função para a minha aplicação?

Peça a um administrador global para o definir como administrador global no inquilino do AAD, de modo a obter permissões para atribuir funções a utilizadores pessoalmente. Em alternativa, peça a um administrador global para lhe atribuir uma função. Se pretender alterar o inquilino do AAD no qual a sua solução pré-configurada foi implementada, consulte a questão seguinte.

### Como posso alternar o inquilino do AAD atribuído à minha aplicação e solução pré-configurada de monitorização remota?

Pode executar uma implementação de nuvem a partir de <https://github.com/Azure/azure-iot-remote-monitoring> e voltar a implementar com um inquilino do AAD recém-criado. Uma vez que, por predefinição, é um administrador global quando cria um novo inquilino do AAD, obtém acesso para adicionar utilizadores e atribuir funções aos mesmos.

1. Crie um novo diretório do AAD no [Portal de Gestão do Azure][lnk-classic-portal].

2. Aceda a <https://github.com/Azure/azure-iot-remote-monitoring>.

3. Execute `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (Por exemplo, `build.cmd cloud debug myRMSolution`)

4. Quando solicitado, defina o **tenantid** para ser o inquilino recém-criado em vez do inquilino anterior.


### Quero alterar um Administrador de Serviço ou Coadministrador depois de iniciar sessão com uma conta institucional.

Consulte o artigo de suporte [Changing Service Administrator and Co-Administrator when logged in with an organisational account (Alterar o Administrador de Serviço e Coadministrador depois de iniciar sessão com uma conta institucional)][lnk-service-admins].

### Porque estou a ver este erro? "A sua conta não tem as permissões adequadas para criar uma solução. Consulte o seu administrador de conta ou tente com uma conta diferente."

Observe o diagrama abaixo:

![][img-flowchart]

> [AZURE.NOTE] Se continuar a ver o erro depois de confirmar que é administrador global no inquilino do AAD e coadministrador na subscrição, solicite ao administrador de conta que remova o utilizador e atribua novamente as permissões necessárias pela seguinte ordem: adicionar o utilizador como administrador global e, em seguida, adicionar o utilizador como coadministrador na subscrição do Azure. Se os problemas persistirem, contacte [Ajuda e Suporte][lnk-help-support].

**Porque estou a ver este erro quando tenho uma subscrição do Azure?** *É necessária uma subscrição do Azure para criar soluções pré-configuradas. Pode criar uma conta de avaliação gratuita em apenas alguns minutos.*

Se tem a certeza de que tem uma subscrição do Azure, valide o mapeamento do inquilino da sua subscrição e certifique-se de que o inquilino correto está selecionado na lista pendente. Se confirmou que o inquilino pretendido está correto, siga o diagrama acima e valide o mapeamento da sua subscrição e este inquilino do AAD.

## Passos seguintes

Para saber mais sobre o IoT Suite, veja como pode [personalizar uma solução pré-configurada][lnk-customize].

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-aad-admin]: https://azure.microsoft.com/documentation/articles/active-directory-assign-admin-roles/
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-create-edit-users]: https://azure.microsoft.com/documentation/articles/active-directory-create-users/
[lnk-assign-app-roles]: https://azure.microsoft.com/documentation/articles/active-directory-application-manifest/
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md


<!--HONumber=Aug16_HO1-->


