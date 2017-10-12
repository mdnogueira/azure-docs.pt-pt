<!--author=alkohli last changed: 9/17/15-->

#### <a name="to-add-a-storage-account-in-storsimple-8000-series-update-10"></a>Para adicionar uma conta de armazenamento no StorSimple Série 8000 Atualização 1.0
1. Na página de destino do serviço StorSimple Manager, selecione o seu serviço e faça duplo clique para aceder à página **Início Rápido**. Selecione a página **Configurar**.
2. Clique em **Adicionar/editar conta de armazenamento**.
3. Na caixa de diálogo **Adicionar/editar conta de armazenamento**, clique em **Adicionar novo**.
4. No campo **Fornecedor**, selecione o fornecedor de serviços em nuvem correto. Os fornecedores suportados são: Azure, Amazon S3, Amazon S3 com RRS, HP e OpenStack. Especifique as credenciais e a localização associadas à conta de armazenamento dos seus fornecedores de serviços em nuvem. Os campos apresentados para as credenciais serão diferentes consoante o fornecedor de serviço em nuvem que especificou. 
   
   * Se tiver selecionado o Azure como o fornecedor de serviços em nuvem, forneça o **Nome** e a **Chave de Acesso** primária para a conta de armazenamento do Microsoft Azure. Para uma conta do Azure, a localização será automaticamente preenchida.
     
        ![Adicionar conta de armazenamento do Azure](./media/storsimple-configure-new-storage-account-u1/AddAzureStorageaccount-include.png)
   * Se tiver selecionado Amazon S3 ou Amazon S3 com RRS, forneça um **Nome da Conta de Armazenamento** amigável, **Chave de Acesso** e **Chave Secreta**. Para o Amazon S3 e o Amazon S3 com RRS, são suportadas as seguintes localizações:
     
     * E.U.A. Standard
     * E.U.A. Oeste (Oregon)
     * E.U.A. Oeste (Norte da Califórnia)
     * UE (Irlanda)
     * Ásia-Pacífico (Singapura)
     * Ásia-Pacífico (Sydney)
     * Ásia-Pacífico (Tóquio)
     * América do Sul (São Paulo)
       
       ![Adicionar conta de armazenamento do Amazon](./media/storsimple-configure-new-storage-account-u1/AddAmazonStorageaccount-include.png)
   * Se tiver selecionado HP como o fornecedor de serviços em nuvem, forneça um **Nome da Conta de Armazenamento** amigável, o **ID do Inquilino**, o **Nome de Utilizador** e a **Palavra-passe**. Para HP, são suportadas as seguintes localizações:
     
     * E.U.A Leste
     * E.U.A. Oeste
       
       ![Adicionar conta de armazenamento de HP](./media/storsimple-configure-new-storage-account-u1/AddHPStorageaccount-include.png)
   * Se tiver selecionado **Openstack** como o fornecedor de serviços em nuvem, forneça um **Nome do Anfitrião**, a **Chave de Acesso** e a **Chave Secreta**.
     
     > [!NOTE]
     > Para todos os fornecedores de serviços em nuvem, excluindo o Azure, é permitido um nome amigável. Pode utilizar diferentes nomes amigáveis e criar mais do que uma conta de armazenamento com o mesmo conjunto de credenciais.
     > 
     > 
     
        ![Adicionar conta de armazenamento do Openstack](./media/storsimple-configure-new-storage-account-u1/AddOpenstackStorageaccount-include.png)
5. Selecione **Ativar Modo SSL** para criar um canal seguro para a comunicação de rede entre o seu dispositivo e a nuvem. Desmarque a caixa de verificação **Ativar Modo SSL** apenas se estiver a utilizar uma nuvem privada.
   
   > [!NOTE]
   > Se estiver a utilizar HP como o fornecedor, o modo SSL estará sempre ativado.
   > 
   > 
6. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png). Será notificado quando a conta de armazenamento for criada com êxito.
7. A conta de armazenamento recentemente criada será apresentada na página **Configurar** em **Contas de armazenamento**. Clique em **Guardar** para guardar a nova conta de armazenamento. Clique em **OK** quando lhe for pedida a confirmação.

