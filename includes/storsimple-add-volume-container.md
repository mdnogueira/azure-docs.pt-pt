<!--author=SharS last changed: 1/7/2016-->

#### <a name="to-add-a-volume-container"></a>Para adicionar um contentor de volume
1. No **dispositivos** página, selecione o dispositivo, faça duplo clique e, em seguida, clique em de **contentores de Volume** separador.
2. Clique em **adicionar** na parte inferior da página. No **criar contentor de volume** diálogo caixa, efetue o seguinte:
   
   1. Forneça um exclusivo **nome** para o contentor de volume. Este nome pode conter um máximo de 32 carateres.
   2. Selecione um **conta de armazenamento** estar associado este contentor de volume. Pode escolher uma conta de armazenamento existente na mesma subscrição ou selecione **adicionar mais** para selecionar uma conta de armazenamento a partir de outra subscrição. Também pode escolher a conta de armazenamento que foi gerada pela primeira vez quando o serviço foi criado.
   3. Especificar a largura de banda como **ilimitada** se pretender consumir largura de banda disponível, ou **personalizada** aplicar controlos de largura de banda. Para uma largura de banda personalizada, forneça um valor entre 1 e 1000 Mbps. Alocar largura de banda com base numa agenda, pode **selecionar um modelo de largura de banda**.
   4. Recomendamos que mantenha **ativar a encriptação de armazenamento de nuvem** selecionado para encriptar os dados que vai para a nuvem. Desative a encriptação apenas se a utilização de outros meios para encriptar os dados. Não é possível modificar a definição de encriptação depois de criar o contentor de volume.
   5. Forneça um **chave de encriptação de armazenamento na nuvem** que contenha entre 8 e 32 carateres. O dispositivo utiliza esta chave para aceder aos dados encriptados. No **Confirmar chave de encriptação de armazenamento na nuvem** campo, introduza a chave de encriptação de armazenamento na nuvem novamente para confirmá-la. 
   6. Clique na seta para continuar para a página seguinte.
      
      ![Criar contentor de volume com o modelo de largura de banda 1](./media/storsimple-add-volume-container/HCS_CreateVCBT1-include.png) 
3. Se tiver especificado **selecionar um modelo de largura de banda**, escolha na lista pendente de modelos existentes de largura de banda. Reveja as definições de agendamento e clique no ícone de verificação ![ícone de verificação](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png).
   
    ![Criar contentor de volume com o modelo de largura de banda 2](./media/storsimple-add-volume-container/HCS_CreateVCBT2-include.png) 

O contentor de volume será guardado e o contentor de volume criado recentemente será apresentado no **contentor de Volume** página.

