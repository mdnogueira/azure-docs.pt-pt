<!--author=alkohli last changed: 08/16/2016-->

#### <a name="to-create-a-volume"></a>Para criar um volume
1. Na página **Guia de Introdução** do dispositivo, clique em **Adicionar um volume** para iniciar o assistente Adicionar um volume.
2. No assistente Adicionar um volume, em **Definições Básicas**:
   
   1. Escreva um **Nome** para o volume.
   2. Na lista pendente, selecione o **Tipo de Utilização** do volume. Para cargas de trabalho que necessitem de garantias locais, latências baixas e desempenho superior, selecione um volume **Afixado localmente**. Para todos os outros dados, selecione um volume **Em camadas**. Se estiver a utilizar este volume para os dados de arquivo, marque **Utilizar este volume para dados de arquivo acedidos com menos frequência**. 
      
       Um volume localmente afixado é fortemente aprovisionado e assegura que os dados primários no volume permanecem locais no dispositivo e não transbordam para a nuvem.  Se criar um volume localmente afixado, o dispositivo verifica o espaço disponível nas camadas locais para aprovisionar o volume do tamanho necessário. A operação de criação de um volume localmente afixado pode envolver transbordar dados existentes do dispositivo para a nuvem e o tempo necessário para criar o volume poderá ser longo. O tempo total depende do tamanho do volume aprovisionado, da largura de banda de rede disponível e dos dados no dispositivo. 
      
       Um volume em camadas é fracamente aprovisionado e pode ser criado rapidamente. Selecionar **Utilizar este volume para dados de arquivo acedidos com menos frequência** para o volume em camadas direcionado para dados de arquivo altera o tamanho dos segmentos de eliminação de duplicados do volume para 512 KB. Se este campo não estiver selecionado, o volume em camadas correspondente utiliza um tamanho do segmento de 64 KB. Um tamanho de segmentos de eliminação de duplicados maior permite ao dispositivo agilizar a transferência de dados de arquivo de grandes dimensões para a nuvem.
   3. Especifique a **Capacidade Aprovisionada** para o volume. Tome nota da capacidade que está disponível, consoante o tipo de volume selecionado. O tamanho do volume especificado não pode exceder o espaço disponível.
      
       Pode aprovisionar volumes localmente afixados até 8,5 TB ou volumes em camadas até 200 TB no dispositivo 8100. No dispositivo 8600 maior, pode aprovisionar volumes localmente afixados até 22,5 TB ou volumes em camadas até 500 TB. Como é preciso espaço local no dispositivo para alojar o conjunto de trabalho de volumes em camadas, a criação de volumes localmente afixados tem impacto no espaço disponível para o aprovisionamento de volumes em camadas. Por conseguinte, se criar um volume afixado localmente, será reduzido o espaço disponível para criação de volumes em camadas. Do mesmo modo, se for criado um volume em camadas, o espaço disponível para a criação de volumes localmente afixados é reduzido.
      
       Se aprovisionar um volume localmente afixado de 8,5 TB (tamanho máximo admissível) no dispositivo 8100, terá esgotado todo o espaço local disponível no dispositivo. Não poderá criar nenhum volume em camadas a partir desse ponto, uma vez que não há espaço local no dispositivo para alojar o conjunto de trabalho do volume em camadas. Os volumes em camadas existentes também afetam o espaço disponível. Por exemplo, se tiver um dispositivo 8100 que já tem volumes em camadas de, aproximadamente, 106 TB, está disponível apenas um espaço de 4 TB para volumes afixados localmente.
      
       A imagem seguinte mostra a caixa de diálogo **Definições Básicas** para um volume afixado localmente.
      
        ![Adicionar volume local](./media/storsimple-create-volume-u2/add-local-volume-include.png)
      
       A imagem seguinte mostra a caixa de diálogo **Definições Básicas** para um volume em camadas.
      
        ![Adicionar volume local](./media/storsimple-create-volume-u2/add-tiered-volume-include.png)
   
   1. Clique no ícone de seta ![ícone de seta](./media/storsimple-create-volume-u2/HCS_ArrowIcon-include.png) para ir para a página seguinte.
3. Na caixa de diálogo **Definições Adicionais**, adicione um novo registo de controlo de acesso (ACR):
   
   1. Forneça um **Nome** para o ACR.
   2. Em **Nome do Iniciador iSCSI**, forneça o Nome Qualificado (IQN) do iSCSI do seu anfitrião do Windows. Se não tiver o IQN, veja [Get the IQN of a Windows Server host (Obter o IQN de um anfitrião do Windows Server)](#get-the-iqn-of-a-windows-server-host).
   3. Em **Cópia de segurança predefinida para este volume?**, selecione a caixa de verificação **Ativar**. A cópia de segurança predefinida cria uma política que é executada às 22:30 todos os dias (hora do dispositivo) e cria um instantâneo de nuvem do volume.
      
      > [!NOTE]
      > Após ativar aqui a cópia de segurança, não será possível reverter a ação. Tem de editar o volume para modificar esta definição.
      > 
      > 
      
      ![Adicionar volume](./media/storsimple-create-volume-u2/AddVolumeAdditionalSettings1.png)
4. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-create-volume-u2/HCS_CheckIcon-include.png). É criado um volume com as definições especificadas.

