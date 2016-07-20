#### Para criar um dispositivo virtual

1.  No Portal do Azure, aceda ao serviço **StorSimple Manager**.

2. Aceda à página **Dispositivos**. Clique em **Criar dispositivo virtual** na parte inferior da página **Dispositivos**.

3. Na caixa de diálogo **Criar Dispositivo Virtual**, especifique os seguintes detalhes.

     ![Criar dispositivo virtual StorSimple](./media/storsimple-create-virtual-device-u2/CreatePremiumsva1.png)

    1. **Nome** – um nome exclusivo para o seu dispositivo virtual.


    2. **Modelo** – escolha o modelo do dispositivo virtual. Este campo é apresentado apenas se estiver a executar a Atualização 2 ou posterior. Um dispositivo modelo 8010 oferece 30 TB de armazenamento Standard ao passo que o 8020 tem 64 TB de armazenamento Premium. Especifique 8010
    3.  para implementar cenários de obtenção a nível de itens a partir de cópias de segurança. Selecione 8020 para implementar cargas de trabalho de elevado desempenho e de baixa latência ou para utilizar como um dispositivo secundário para recuperação após desastre.
     
    4. **Versão** – escolha a versão do dispositivo virtual. Se tiver selecionado um dispositivo modelo 8020, o campo Versão não será apresentado. Esta opção estará ausente se todos os dispositivos físicos registados neste serviço estiverem a executar a Atualização 1 (ou posterior). Este campo será apresentado apenas se tiver uma mistura de dispositivos físicos pré-Atualização 1 e Atualização 1 registados no mesmo serviço. Uma vez que a versão do dispositivo virtual irá determinar que dispositivo físico poderá utilizar para a ativação pós-falha ou para a clonagem, é importante que crie uma versão adequada do dispositivo virtual. Selecione:

       - Atualização versão 0.3 se pretender efetuar a ativação pós-falha ou DR a partir de um dispositivo físico a executar a Atualização 0.3 ou anterior. 
       - Atualização versão 1 se pretender efetuar a ativação pós-falha ou clonar a partir de um dispositivo físico a executar a Atualização 1 (ou posterior). 
       
    
    5. **Virtual Network** – especifique a rede virtual que pretende utilizar com este dispositivo virtual. Se utilizar o Premium Storage (Atualização 2 ou posterior), tem de selecionar uma rede virtual que seja suportada com a Conta do Premium Storage. As redes virtuais não suportadas estarão indisponíveis na lista pendente. Será avisado se selecionar uma rede virtual não suportada. 

    5. **Conta de Armazenamento para a Criação de Dispositivos Virtuais** – selecione uma conta de armazenamento para armazenar a imagem do dispositivo virtual durante o aprovisionamento. Esta conta de armazenamento deve estar na mesma região que o dispositivo virtual e a rede virtual. Não deverá ser utilizada para armazenamento de dados do dispositivo físicos ou virtual. Por predefinição, será criada uma nova conta de armazenamento para esta finalidade. No entanto, se souber que já tem uma conta de armazenamento adequada para esta utilização, poderá selecioná-la na lista. Se criar um dispositivo virtual Premium, a lista pendente apresentará apenas contas do Premium Storage. 

        >[AZURE.NOTE] O dispositivo virtual só pode ser utilizado com as contas de armazenamento do Azure. Outros fornecedores de serviços em nuvem como Amazon, HP e OpenStack (que são suportados para o dispositivo físico) não são suportados para o dispositivo virtual StorSimple.
    
    1. Clique na marca de verificação para indicar que entende que os dados armazenados no dispositivo virtual serão alojados num Microsoft Datacenter. Se utilizar apenas um dispositivo físico, a sua chave de encriptação é mantida com o dispositivo. Por conseguinte, a Microsoft não a poderá desencriptar. 
     
        Se utilizar um dispositivo virtual, a chave de encriptação e a chave de desencriptação são armazenadas no Microsoft Azure. Para obter mais informações, veja [Considerações de segurança para utilizar um dispositivo virtual](storsimple-security/#storsimple-virtual-device-security).
    2. Clique no ícone de verificação para criar o dispositivo virtual. O dispositivo poderá demorar cerca de 30 minutos a ser aprovisionado.

    ![Fase de criação do dispositivo virtual StorSimple](./media/storsimple-create-virtual-device-u2/StorSimple_VirtualDeviceCreating1M.png)

    



<!--HONumber=Jun16_HO2-->


