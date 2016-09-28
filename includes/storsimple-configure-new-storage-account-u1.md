<!--author=alkohli last changed: 9/17/15-->

#### Para adicionar uma conta de armazenamento no StorSimple Série 8000 Atualização 1.0

1. Na página de destino do serviço StorSimple Manager, selecione o seu serviço e faça duplo clique para aceder à página **Início Rápido**. Selecione a página **Configurar**.

2. Clique em **Adicionar/editar conta de armazenamento**.

3. Na caixa de diálogo **Adicionar/editar conta de armazenamento**, clique em **Adicionar novo**.

4. No campo **Fornecedor**, selecione o fornecedor de serviços em nuvem correto. Os fornecedores suportados são: Azure, Amazon S3, Amazon S3 com RRS, HP e OpenStack. Especifique as credenciais e a localização associadas à conta de armazenamento dos seus fornecedores de serviços em nuvem. Os campos apresentados para as credenciais serão diferentes consoante o fornecedor de serviço em nuvem que especificou. 
  - Se tiver selecionado o Azure como o fornecedor de serviços em nuvem, forneça o **Nome** e a **Chave de Acesso** primária para a conta de armazenamento do Microsoft Azure. Para uma conta do Azure, a localização será automaticamente preenchida.

        ![Add Azure storage account](./media/storsimple-configure-new-storage-account-u1/AddAzureStorageaccount-include.png)

 - Se tiver selecionado Amazon S3 ou Amazon S3 com RRS, forneça um **Nome da Conta de Armazenamento** amigável, **Chave de Acesso** e **Chave Secreta**. Para o Amazon S3 e o Amazon S3 com RRS, são suportadas as seguintes localizações:

        - US Standard
        - US West (Oregon)
        - US West (Northern California)
        - EU (Ireland)
        - Asia Pacific (Singapore)
        - Asia Pacific (Sydney)
        - Asia Pacific (Tokyo)
        - South America (Sao Paulo)

        ![Add Amazon storage account](./media/storsimple-configure-new-storage-account-u1/AddAmazonStorageaccount-include.png)
            
 - Se tiver selecionado HP como o fornecedor de serviços em nuvem, forneça um **Nome da Conta de Armazenamento** amigável, o **ID do Inquilino**, o **Nome de Utilizador** e a **Palavra-passe**. Para HP, são suportadas as seguintes localizações:

        - US East
        - US West
      
        ![Add HP storage account](./media/storsimple-configure-new-storage-account-u1/AddHPStorageaccount-include.png)
            
 - Se tiver selecionado **Openstack** como o fornecedor de serviços em nuvem, forneça um **Nome do Anfitrião**, a **Chave de Acesso** e a **Chave Secreta**.

        > [AZURE.NOTE] Para todos os fornecedores de serviços em nuvem, excluindo o Azure, é permitido um nome amigável. Pode utilizar diferentes nomes amigáveis e criar mais do que uma conta de armazenamento com o mesmo conjunto de credenciais.

        ![Add Openstack storage account](./media/storsimple-configure-new-storage-account-u1/AddOpenstackStorageaccount-include.png)

5. Selecione **Ativar Modo SSL** para criar um canal seguro para a comunicação de rede entre o seu dispositivo e a nuvem. Desmarque a caixa de verificação **Ativar Modo SSL** apenas se estiver a utilizar uma nuvem privada.

      > [AZURE.NOTE] Se estiver a utilizar HP como o fornecedor, o modo SSL estará sempre ativado.
        
6. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png). Será notificado quando a conta de armazenamento for criada com êxito.

7. A conta de armazenamento recentemente criada será apresentada na página **Configurar** em **Contas de armazenamento**. Clique em **Guardar** para guardar a nova conta de armazenamento. Clique em **OK** quando lhe for pedida a confirmação.


<!--HONumber=Sep16_HO3-->


