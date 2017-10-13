<!--author=SharS last changed: 9/17/15-->

#### <a name="to-create-a-volume-container"></a>Para criar um contentor de volume
1. Na página **Início Rápido** do dispositivo, clique em **Adicionar um contentor do volume**. É apresentada a caixa de diálogo **Criar Contentor de Volume**.
   
    ![Criar Contentor de Volume](./media/storsimple-create-volume-container/HCS_CreateVolumeContainerM-include.png)
2. Na caixa de diálogo **Criar Contentor de Volume**:
   
   1. Forneça um **Nome** para o contentor do volume. O nome tem de ter entre 3 e 32 carateres.
   2. Selecione uma **Conta de Armazenamento** associada a este contentor de volume Pode escolher a conta predefinida que é gerada no momento da criação do serviço. Também pode utilizar a opção **Adicionar nova** para especificar uma conta de armazenamento que não esteja associada a esta subscrição do serviço.
   3. Selecione **Ativar Encriptação do Armazenamento na Nuvem** para ativar a encriptação dos dados enviados do dispositivo para a nuvem.
   4. Forneça e confirme uma **Chave de Encriptação do Armazenamento na Nuvem** que tenha entre 8 e 32 carateres. Esta chave é utilizada pelo dispositivo para aceder aos dados encriptados.
   5. Selecione **Sem limite** na lista pendente **Especificar largura de banda** se desejar consumir toda a largura de banda disponível. Também pode configurar esta opção para **Personalizar** para utilizar controlos de largura de banda e especificar um valor entre 1 e 1000 Mbps. 
      Se tiver disponíveis as informações de utilização da largura de banda, poderá conseguir alocar a largura de banda com base no agendamento, especificando **Selecionar um modelo de largura de banda**. Para um procedimento passo-a-passo, veja [Adicionar um modelo de largura de banda](../articles/storsimple/storsimple-manage-bandwidth-templates.md#add-a-bandwidth-template).
   6. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-create-volume-container/HCS_CheckIcon-include.png) para guardar este contentor de volume e sair do assistente. 
   
   O contentor de volume criado recentemente será apresentado na página **Contentores de volume**.

![Vídeo disponível](./media/storsimple-create-volume-container/Video_icon.png) **Vídeo disponível**

Para ver um vídeo que demonstra como criar um contentor de volume na sua solução StorSimple, clique [aqui](https://azure.microsoft.com/documentation/videos/create-a-volume-container-in-your-storsimple-solution/).

