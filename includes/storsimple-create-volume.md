<!--author=SharS last changed: 02/04/2016-->

#### <a name="to-create-a-volume"></a>Para criar um volume
1. Na página **Início Rápido** do dispositivo, clique em **Adicionar um volume**. Esta ação inicia o assistente Adicionar um volume.
2. No assistente Adicionar um volume, em **Definições Básicas**, faça o seguinte:
   
   1. Forneça um **Nome** para o volume.
   2. Especifique a **Capacidade Aprovisionada** para o volume em GB ou TB. A capacidade do volume tem de estar entre 1 GB e 64 TB para um dispositivo físico.
   3. Na lista pendente, selecione o **Tipo de Utilização** do volume. 
   4. Se estiver a utilizar este volume de dados de arquivo, selecione a caixa de verificação **Utilizar este volume para dados de arquivo acedidos com menos frequência**. Para todos os outros casos de utilização selecione simplesmente **Volume em Camadas**. (Os volumes em camadas chamavam-se anteriormente volumes principais).
      
        ![Adicionar volume](./media/storsimple-create-volume/ScreenshotUpdate1VolumeFlow.png)
      
      1. Clique no ícone de seta ![ícone de seta](./media/storsimple-create-volume/HCS_ArrowIcon-include.png) para ir para a página seguinte.
3. Na caixa de diálogo **Definições Adicionais**, adicione um novo registo de controlo de acesso (ACR):
   
   1. Forneça um **Nome** para o ACR.
   2. Em **Nome do Iniciador iSCSI**, forneça o Nome Qualificado (IQN) do iSCSI do seu anfitrião do Windows. Se não tiver o IQN, veja [Get the IQN of a Windows Server host (Obter o IQN de um anfitrião do Windows Server)](#get-the-iqn-of-a-windows-server-host).
   3. Recomendamos que ative uma cópia de segurança predefinida selecionando a caixa de verificação **Ativar uma cópia de segurança predefinido para este volume**. A cópia de segurança predefinida criará uma política que é executada às 22:30 todos os dias (hora do dispositivo) e cria um instantâneo na nuvem do volume.
      
      > [!NOTE]
      > Após ativar aqui a cópia de segurança, não será possível reverter a ação. Terá de editar o volume para modificar a definição.
      > 
      > 
      
        ![Adicionar volume](./media/storsimple-create-volume/AddVolume2-include.png)
4. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-create-volume/HCS_CheckIcon-include.png). Será criado um volume com as definições especificadas.

![Vídeo disponível](./media/storsimple-create-volume/Video_icon.png) **Vídeo disponível**

Para ver um vídeo que demonstra como criar um volume do StorSimple, clique [aqui](https://azure.microsoft.com/documentation/videos/create-a-storsimple-volume/).

