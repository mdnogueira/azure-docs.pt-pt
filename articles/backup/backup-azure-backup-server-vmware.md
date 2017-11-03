---
title: "Cópia de segurança servidores do VMware com o servidor de cópia de segurança do Azure | Microsoft Docs"
description: "Utilize o servidor de cópia de segurança do Azure para cópia de segurança de um VMware vCenter/ESXi os servidores para o Azure ou o disco. Este artigo fornece passo = instruções passo a passo para a cópia de segurança (ou da proteção) as cargas de trabalho do VMware."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
ms.assetid: 6b131caf-de85-4eba-b8e6-d8a04545cd9d
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/24/2017
ms.author: markgal;
ms.openlocfilehash: ad331dffb7c31d12290f4223967c568e4535fe3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-a-vmware-server-to-azure"></a>Cópia de segurança de um servidor VMware para Azure

Este artigo explica como configurar o servidor de cópia de segurança do Azure para ajudar a proteger cargas de trabalho de servidor de VMware. Este artigo pressupõe que já tiver instalado de servidor do Backup do Azure. Se não tiver instalado de servidor do Backup do Azure, consulte [preparar a cópia de segurança de cargas de trabalho utilizando o servidor de cópia de segurança do Azure](backup-azure-microsoft-azure-backup.md).

Servidor de cópia de segurança do Azure pode criar cópias de segurança ou ajudar a proteger, versão do servidor vCenter 6.5, 6.0 e 5.5 do VMware.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Criar uma ligação segura ao vCenter Server

Por predefinição, o servidor de cópia de segurança do Azure comunica com cada servidor vCenter através de um canal HTTPS. Para ativar a comunicação segura, recomendamos que instale o certificado de autoridade de certificação de VMware (CA) no servidor de cópia de segurança do Azure. Se não necessitam de uma comunicação segura e prefere desativar o requisito de HTTPS, consulte [protocolo de comunicação segura desativar](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol). Para criar uma ligação segura entre o servidor de cópia de segurança do Azure e o vCenter Server, importe o certificado fidedigno no servidor de cópia de segurança do Azure.

Normalmente, utilizar um browser no computador servidor de cópia de segurança do Azure para ligar ao vCenter Server através do vSphere cliente Web. Na primeira vez que utiliza o browser do servidor de cópia de segurança do Azure para ligar ao vCenter Server, a ligação não segura. A imagem seguinte mostra a ligação não segura.

![Exemplo de uma ligação não segura para o servidor VMware](./media/backup-azure-backup-server-vmware/unsecure-url.png)

Para corrigir este problema e criar uma ligação segura, transferir os certificados de AC de raiz fidedigna.

1. No browser no servidor de cópia de segurança do Azure, introduza o URL para o vSphere cliente Web. É apresentada a página de início de sessão de cliente Web vSphere.

    ![vSphere Web cliente](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

    Na parte inferior das informações para os administradores e programadores, localize o **transferência os certificados de AC de raiz fidedigna** ligação.

    ![Ligação para transferir os certificados de AC de raiz fidedigna](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  Se não vir a página de início de sessão de cliente Web vSphere, verifique as definições de proxy do seu browser.

2. Clique em **transferência os certificados de AC de raiz fidedigna**.

    O vCenter Server transfere um ficheiro para o seu computador local. Nome do ficheiro é denominado **transferir**. Dependendo do seu browser, receberá uma mensagem a perguntar se pretende abrir ou guardar o ficheiro.

    ![Transferir a mensagem quando os certificados são transferidos](./media/backup-azure-backup-server-vmware/download-certs.png)

3. Guarde o ficheiro para uma localização no servidor de cópia de segurança do Azure. Quando guardar o ficheiro, adicione a extensão de nome de ficheiro. zip.

    O ficheiro é um ficheiro. zip que contém as informações sobre certificados. Com a extensão. zip, pode utilizar as ferramentas de extração.

4. Clique com botão direito **download.zip**e, em seguida, selecione **extrair todos os** para extrair os conteúdos.

    O ficheiro. zip extrai os respetivos conteúdos para uma pasta denominada **certificados**. São apresentados dois tipos de ficheiros na pasta de certificados. O ficheiro de certificado de raiz tem uma extensão que comece com uma sequência como.0 e.1 numerada.
    
    O ficheiro CRL tem uma extensão que comece com uma sequência como .r0 ou .r1. O ficheiro CRL está associado um certificado.

    ![Transferir os ficheiros extraídos localmente ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. No **certificados** pasta, faça duplo clique no ficheiro de certificado de raiz e, em seguida, clique em **mudar o nome**.

    ![Mudar o nome do certificado de raiz ](./media/backup-azure-backup-server-vmware/rename-cert.png)

    Altere a extensão do certificado de raiz para. crt. Quando estiver a pedido se tiver a certeza de que pretende alterar a extensão, clique em **Sim** ou **OK**. Caso contrário, alterar função pretendida do ficheiro. O ícone para o ficheiro é alterado para um ícone que representa um certificado de raiz.

6. Faça duplo clique o certificado de raiz e a partir do menu de pop-up, selecione **instalar certificado**.

    O **Assistente para importar certificados** é apresentada a caixa de diálogo.

7. No **Assistente para importar certificados** caixa de diálogo, selecione **máquina Local** como destino para o certificado e, em seguida, clique em **seguinte** para continuar.

    ![Opções de destino de armazenamento de certificados ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

    Se estiver a pedido se pretender permitir que as alterações ao computador, clique em **Sim** ou **OK**, para todas as alterações.

8. No **arquivo de certificados** página, selecione **colocar todos os certificados no seguinte arquivo**e, em seguida, clique em **procurar** para escolher o arquivo de certificados.

    ![Colocar os certificados num lugar para cima de armazenamento específico](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

    O **selecionar arquivo de certificados** é apresentada a caixa de diálogo.

    ![Hierarquia de pastas de armazenamento de certificados](./media/backup-azure-backup-server-vmware/cert-store.png)

9. Selecione **autoridades de certificação de raiz fidedigna** como a pasta de destino para os certificados e, em seguida, clique em **OK**.

    ![Pasta de destino de certificado](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

    O **autoridades de certificação de raiz fidedigna** pasta foi confirmada como como o arquivo de certificados. Clique em **Seguinte**.

    ![Pasta de arquivo de certificados](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. No **a concluir o Assistente de importação do certificado** página, certifique-se de que o certificado é na pasta pretendida e, em seguida, clique em **concluir**.

    ![Verificar o certificado é a pasta adequada](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

    É apresentada uma caixa de diálogo, a importação do certificado com êxito é confirmada.

11. Inicie sessão no vCenter Server para confirmar que a ligação é segura.

  Se a importação do certificado não for concluída com êxito e não é possível estabelecer uma ligação segura, consulte a documentação do VMware vSphere no [obter certificados de servidor](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html).

  Se tiver limites segurados na sua organização e não pretender ativar o protocolo HTTPS, utilize o procedimento seguinte para desativar as comunicações seguras.

### <a name="disable-secure-communication-protocol"></a>Desativar o protocolo de comunicação segura

Se a sua organização não requer o protocolo HTTPS, utilize os seguintes passos para desativar o HTTPS. Para desativar o comportamento predefinido, crie uma chave de registo que ignora o comportamento predefinido.

1. Copie e cole o seguinte texto no ficheiro. txt.

  ```
  Windows Registry Editor Version 5.00
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
  "IgnoreCertificateValidation"=dword:00000001
  ```

2. Guarde o ficheiro para o seu computador de servidor de cópia de segurança do Azure. Para o nome de ficheiro, utilize DisableSecureAuthentication.reg.

3. Faça duplo clique no ficheiro para ativar a entrada de registo.


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>Criar uma conta de utilizador e função no vCenter Server

No vCenter Server, uma função é um conjunto predefinido de privilégios. Um administrador do servidor vCenter cria as funções. Para atribuir permissões, o administrador de pares de contas de utilizador com uma função. Para estabelecer as credenciais do utilizador necessárias para criar cópias de segurança do computador do servidor vCenter, criar uma função com privilégios específicos e, em seguida, associar a conta de utilizador com a função.

Servidor de cópia de segurança do Azure utiliza um nome de utilizador e palavra-passe para autenticar com o vCenter Server. Servidor do Backup do Azure utiliza estas credenciais como autenticação para todas as operações de cópia de segurança.

Para adicionar uma função de servidor vCenter e os respetivos privilégios para um administrador de cópias de segurança:

1. A iniciar sessão para o vCenter Server e, em seguida, no vCenter Server **navegador** painel, clique em **administração**.

    ![Opção de administração no painel do vCenter Server navegador](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. No **administração** selecione **funções**e, em seguida, no **funções** painel clique no ícone de função de adicionar (o símbolo +).

    ![Adicionar função](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

    O **criar função** é apresentada a caixa de diálogo.

    ![Criar função](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. No **criar função** caixa de diálogo a **nome da função** box, introduza *BackupAdminRole*. O nome de função pode ser que quiser, mas deve ser reconhecível para fim da função.

4. Selecione os privilégios para a versão adequada do vCenter e, em seguida, clique em **OK**. A tabela seguinte identifica os privilégios necessários para o vCenter 6.0 e vCenter 5.5.

  Quando seleciona os privilégios, clique no ícone junto a etiqueta principal para expandir o principal e ver os privilégios subordinado. Para selecionar os privilégios de máquina virtual, terá de tecnológica de vários níveis da hierarquia de subordinado do principal. Não precisa de selecionar todos os privilégios subordinado dentro de um privilégio principal.

  ![Hierarquia de privilégio principal subordinado](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  Depois de clicar em **OK**, a nova função aparece na lista no painel funções.

|Privilégios para o vCenter 6.0| Privilégios para o vCenter 5.5|
|--------------------------|---------------------------|
|Datastore.AllocateSpace   | Datastore.AllocateSpace|
|Global.ManageCustomFields | Global.ManageCustomerFields|
|Global.SetCustomFields    |   |
|Host.Local.CreateVM       | Network.Assign |
|Network.Assign            |  |
|Resource.AssignVMToPool   |  |
|VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   |
|VirtualMachine.Config.AdvanceConfig| VirtualMachine.Config.AdvancedConfig|
|VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking |
|VirtualMachine.Config.HostUSBDevice||
|VirtualMachine.Config.QueryUnownedFiles|    |
|VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement |
|VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff |
|VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create |
|VirtualMachine.Provisioning.DiskRandomAccess| |
|VirtualMachine.Provisioning.DiskRandomRead|VirtualMachine.Provisioning.DiskRandomRead |
|VirtualMachine.State.CreateSnapshot| VirtualMachine.State.CreateSnapshot|
|VirtualMachine.State.RemoveSnapshot|VirtualMachine.State.RemoveSnapshot |
</br>



## <a name="create-a-vcenter-server-user-account-and-permissions"></a>Criar uma conta de utilizador do servidor vCenter e permissões

Depois de configurar a função com privilégios, crie uma conta de utilizador. A conta de utilizador tem um nome e uma palavra-passe, que fornece as credenciais que são utilizadas para autenticação.

1. Para criar uma conta de utilizador, no vCenter Server **navegador** painel, clique em **utilizadores e grupos**.

    ![Opção de utilizadores e grupos](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    O **vCenter utilizadores e grupos** painel aparece.

    ![Painel de utilizadores e grupos do vCenter](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. No **vCenter utilizadores e grupos** painel, selecione o **utilizadores** separador e, em seguida, clique no ícone de utilizadores de adicionar (o símbolo +).

    O **novo utilizador** é apresentada a caixa de diálogo.

3. No **novo utilizador** diálogo caixa, adicionar as informações do utilizador e, em seguida, clique em **OK**. Neste procedimento, o nome de utilizador é BackupAdmin.

    ![Caixa de diálogo do novo utilizador](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

    A nova conta de utilizador é apresentado na lista.

4. Para associar a conta de utilizador com a função no **navegador** painel, clique em **permissões Global**. No **permissões Global** painel, selecione o **gerir** separador e, em seguida, clique no ícone de adicionar (o símbolo +).

    ![Painel de permissões global](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

    O **raiz de permissões de Global - adicionar permissão** é apresentada a caixa de diálogo.

5. No **raiz de permissão de Global - adicionar permissão** caixa de diálogo, clique em **adicionar** para escolher o utilizador ou grupo.

    ![Escolha o utilizador ou grupo](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

    O **selecionar utilizadores/grupos** é apresentada a caixa de diálogo.

6. No **selecionar utilizadores/grupos** diálogo caixa, escolha **BackupAdmin** e, em seguida, clique em **adicionar**.

    No **utilizadores**, a *domínio ome de utilizador* formato é utilizado para a conta de utilizador. Se pretender utilizar um domínio diferente, selecione-à partir de **domínio** lista.

    ![Adicionar utilizador BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

    Clique em **OK** para adicionar os utilizadores selecionados para o **adicionar permissão** caixa de diálogo.

7. Agora que identificou o utilizador, atribua o utilizador à função. No **função atribuída**, na lista pendente, selecione **BackupAdminRole**e, em seguida, clique em **OK**.

    ![Atribua o utilizador à função](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  No **gerir** separador o **permissões Global** painel, a nova conta de utilizador e a função associada são apresentadas na lista.


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>Estabelecer as credenciais do servidor vCenter no servidor de cópia de segurança do Azure

Antes de adicionar o servidor VMware para o servidor de cópia de segurança do Azure, instalar [atualização 1 para o servidor de cópia de segurança do Azure](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Para abrir o servidor de cópia de segurança do Azure, faça duplo clique no ícone no ambiente de trabalho do servidor de cópia de segurança do Azure.

    ![Ícone de servidor do Backup do Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

    Se não é possível localizar o ícone no ambiente de trabalho, abra o servidor de cópia de segurança do Azure da lista de aplicações instaladas. O nome da aplicação de servidor de cópia de segurança do Azure denomina-se a cópia de segurança do Microsoft Azure.

2. Na consola do servidor de cópia de segurança do Azure, clique em **gestão**, clique em **servidores de produção**e, em seguida, no Friso, clique em **gerir VMware**.

    ![Consola do servidor do Backup do Azure](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

    O **gerir credenciais** é apresentada a caixa de diálogo.

    ![Caixa de diálogo Gerir credenciais do servidor de cópia de segurança do Azure](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. No **gerir credenciais** caixa de diálogo, clique em **adicionar** para abrir o **adicionar credencial** caixa de diálogo.

4. No **adicionar credencial** caixa de diálogo, introduza um nome e uma descrição para a nova credencial. Em seguida, especifique o nome de utilizador e palavra-passe. O nome, *Contoso Vcenter credencial* é utilizado para identificar a credencial no procedimento seguinte. Utilize o mesmo nome de utilizador e palavra-passe que é utilizado para o vCenter Server. Se o servidor vCenter e o servidor de cópia de segurança do Azure não estão no mesmo domínio, no **nome de utilizador**, especifique o domínio.

    ![Caixa de diálogo Adicionar credencial do servidor de cópia de segurança do Azure](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

    Clique em **adicionar** para adicionar a nova credencial do servidor de cópia de segurança do Azure. A nova credencial aparece na lista no **gerir credenciais** caixa de diálogo.
    
    ![Caixa de diálogo Gerir credenciais do servidor de cópia de segurança do Azure](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Para fechar o **gerir credenciais** caixa de diálogo, clique em de **X** no canto superior direito.


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Adicionar o vCenter Server para o servidor de cópia de segurança do Azure

Assistente de adição do servidor de produção é utilizado para adicionar o vCenter Server para o servidor de cópia de segurança do Azure.

Para abrir o Assistente de adição do servidor de produção, execute o seguinte procedimento:

1. Na consola do servidor de cópia de segurança do Azure, clique em **gestão**, clique em **servidores de produção**e, em seguida, clique em **adicionar**.

    ![Assistente de adição do servidor de produção aberta](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

    O **Assistente de adição do servidor de produção** é apresentada a caixa de diálogo.

    ![Assistente de adição do servidor de produção](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. No **tipo selecionar servidor de produção** página, selecione **servidores VMware**e, em seguida, clique em **seguinte**.

3. No **servidor nome/endereço IP**, especifique o nome de domínio completamente qualificado (FQDN) ou o endereço IP do servidor do VMware. Se todos os servidores do ESXi são geridos pelo mesmo vCenter, pode utilizar o nome do vCenter.

    ![Especifique o endereço IP ou FQDN do servidor de VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. No **porta SSL**, introduza a porta que é utilizada para comunicar com o servidor do VMware. Utilize a porta 443, que é a porta predefinida, a menos que saiba que é necessária uma porta diferente.

5. No **especificar credenciais**, selecione as credenciais que criou anteriormente.

    ![Especificar credenciais](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Clique em **adicionar** para adicionar o servidor do VMware para a lista de **adicionados servidores do VMware**e, em seguida, clique em **seguinte** mover para a página seguinte do assistente.

    ![Adicionar servidor VMWare e credenciais](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. No **resumo** página, clique em **adicionar** para adicionar o servidor do VMware especificado para o servidor de cópia de segurança do Azure.

    ![Adicionar o servidor VMware para o servidor de cópia de segurança do Azure](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  A cópia de segurança do servidor do VMware é uma cópia de segurança sem agente e o novo servidor é adicionado imediatamente. O **concluir** página mostra os resultados.

  ![Página de conclusão](./media/backup-azure-backup-server-vmware/summary-screen.png)

  Para adicionar várias instâncias do servidor vCenter para o servidor de cópia de segurança do Azure, repita os passos anteriores nesta secção.

Depois de adicionar o vCenter Server para o servidor de cópia de segurança do Azure, o passo seguinte consiste em criar um grupo de proteção. O grupo de proteção Especifica os vários detalhes para a retenção de curto ou longo prazo e é onde definir e aplicar a política de cópia de segurança. A política de cópia de segurança é a agenda para quando ocorrerem a cópias de segurança e o que é uma cópia de segurança.


## <a name="configure-a-protection-group"></a>Configurar um grupo de proteção

Se não tiver utilizado o System Center Data Protection Manager ou o servidor de cópia de segurança do Azure antes, consulte o artigo [planear para cópias de segurança de disco](https://technet.microsoft.com/library/hh758026.aspx) para preparar o ambiente de hardware. Depois, verifique se tem o armazenamento adequado, utilize o Assistente Criar novo grupo de proteção para adicionar máquinas virtuais VMware.

1. Na consola do servidor de cópia de segurança do Azure, clique em **proteção**e no Friso de ferramentas, clique em **novo** para abrir o Assistente Criar novo grupo de proteção.

    ![Abrir o Assistente Criar novo grupo de proteção](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

    O **criar novo grupo de proteção** é apresentada a caixa de diálogo do assistente.

    ![Caixa de diálogo Assistente Criar novo grupo de proteção](./media/backup-azure-backup-server-vmware/protection-wizard.png)

    Clique em **seguinte** para avançar para o **selecionar tipo de grupo de proteção** página.

2. No **tipo de grupo de proteção selecione** página, selecione **servidores** e, em seguida, clique em **seguinte**. O **selecionar membros do grupo** é apresentada a página.

3. No **selecionar membros do grupo** página, os membros disponíveis e os membros selecionados aparecem. Selecione os membros que pretende proteger e, em seguida, clique em **seguinte**.

    ![Selecionar Membros do grupo](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

    Quando seleciona um membro, se selecionar uma pasta que contém outros pastas ou VMs, essas pastas e VMs também são selecionadas. A inclusão das pastas e VMs na pasta principal é denominada proteção ao nível da pasta. Para remover uma pasta ou a VM, desmarque a caixa de verificação.

    Se uma VM ou uma pasta que contenha uma VM, já está protegida para o Azure, não é possível selecionar essa VM novamente. Ou seja, depois de uma VM é protegida para o Azure, não pode ser protegido, que impede que o pontos de recuperação duplicados a ser criada para uma VM. Se pretender ver a que instância de servidor de cópia de segurança do Azure já protege um membro, aponte para o membro para ver o nome do servidor de proteção.

4. No **selecionar método de proteção de dados** página, introduza um nome para o grupo de proteção. Proteção de curta duração (para o disco) e a proteção online são selecionados. Se quiser utilizar proteção online (para o Azure), tem de utilizar proteção de curto prazo para disco. Clique em **seguinte** prossiga para o intervalo de proteção de curta duração.

    ![Selecione o método de proteção de dados](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. No **especificar objetivos a curto prazo** página, para **período de retenção**, especifique o número de dias que pretende manter pontos de recuperação que estão *armazenados no disco*. Se pretender alterar a hora e dias quando são tirados pontos de recuperação, clique em **modificar**. Os pontos de recuperação de curta duração são cópias de segurança completas. Não são cópias de segurança incrementais. Quando estiver satisfeito com os objetivos de curto prazo, clique em **seguinte**.

    ![Especificar objetivos a curto prazo](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. No **rever alocação do disco** página, reveja e se necessário, modifique o espaço em disco para as VMs. As alocações de disco recomendado baseiam-se no período de retenção especificado no **especificar objetivos a curto prazo** página, o tipo de carga de trabalho e o tamanho dos dados protegidos (identificados no passo 3).  

  - **Tamanho dos dados:** tamanho dos dados do grupo de proteção.
  - **Espaço em disco:** a quantidade recomendada de espaço em disco para o grupo de proteção. Se pretender modificar esta definição, deve alocar espaço total ligeiramente superior à quantidade que estimar o que crescimentos de cada origem de dados.
  - **Colocalizar dados:** se ativar a colocalização, do ponto de dados de várias origens na proteção podem mapear para uma única réplica e a recuperação de volume. A colocalização não é suportada para todas as cargas de trabalho.
  - **Aumente automaticamente:** se ativar esta definição, se os dados no grupo de proteção superem a alocação inicial, o System Center Data Protection Manager tenta aumentar o tamanho de disco por 25 por cento.
  - **Detalhes do agrupamento de armazenamento:** mostra o estado de agrupamento de armazenamento, incluindo total e o tamanho de disco restante.

    ![Rever alocação do disco](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

    Quando estiver satisfeito com a alocação de espaço, clique em **seguinte**.

7. No **escolher método de criação de réplica** página, especifique como pretende gerar a cópia inicial ou a réplica, dos dados protegidos no servidor de cópia de segurança do Azure.

    A predefinição é **automaticamente através da rede** e **agora**. Se utilizar a predefinição, é recomendado que especifique uma hora de ponta. Escolha **mais tarde** e especifique um dia e hora.

    Para grandes quantidades de dados ou menos-a-ideal nas condições da rede, considere replicar os dados offline utilizando suportes de dados amovível.

    Depois de efetuar as suas opções, clique em **seguinte**.

    ![Escolher método de criação de réplica](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. No **opções de verificação de consistência** página, selecione como e quando automatizar as verificações de consistência. Pode executar verificações de consistência quando os dados de réplica se tornar inconsistentes ou numa agenda definida.

    Se não pretender configurar verificações de consistência automática, pode executar uma verificação manual. Na área de proteção da consola do servidor de cópia de segurança do Azure, clique no grupo de proteção e, em seguida, selecione **efetuar verificação de consistência**.

    Clique em **seguinte** mover para a página seguinte.

9. No **especificar dados da proteção Online** página, selecione um ou mais origens de dados que pretende proteger. Pode selecionar os membros individualmente ou clique em **Selecionar tudo** escolher todos os membros. Após escolher os membros, clique em **seguinte**.

    ![Especificar dados da proteção online](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. No **Especificar agenda de cópia de segurança Online** página, especifique a agenda para gerar pontos de recuperação a partir da cópia de segurança do disco. Depois do ponto de recuperação é gerado, é transferido para o Cofre dos serviços de recuperação no Azure. Quando estiver satisfeito com a agenda de cópia de segurança online, clique em **seguinte**.

    ![Especifique a agenda de cópia de segurança online](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. No **especificar política de retenção Online** página, indique quanto pretende manter os dados de cópia de segurança no Azure. Depois da política estiver definida, clique em **seguinte**.

    ![Especificar a política de retenção online](./media/backup-azure-backup-server-vmware/retention-policy.png)

    Não há nenhum limite de tempo para o período de tempo pode manter os dados no Azure. Quando armazena os dados de ponto de recuperação no Azure, o limite apenas é que não pode ter mais do que 9999 pontos de recuperação por instância protegido. Neste exemplo, a instância protegida é o servidor de VMware.

12. No **resumo** página, consulte os detalhes para os membros do grupo de proteção e as definições e, em seguida, clique em **criar grupo**.

    ![Membro do grupo de proteção e o resumo da definição](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>Passos seguintes
Se utilizar o servidor de cópia de segurança do Azure para proteger cargas de trabalho de VMware, poderá estar interessado no servidor de cópia de segurança do Azure a utilizar para ajudar a proteger um [do Microsoft Exchange server](./backup-azure-exchange-mabs.md), um [farm do SharePoint do Microsoft](./backup-azure-backup-sharepoint-mabs.md), ou um [Base de dados do SQL Server](./backup-azure-sql-mabs.md).

Para obter informações sobre problemas com a registar o agente, consulte Configurar o grupo de proteção ou cópia de segurança de trabalhos, [resolver problemas do servidor de cópia de segurança do Azure](./backup-azure-mabs-troubleshoot.md).
