


## <a name="attach-an-empty-disk"></a>Anexar um disco vazio
Anexar um disco vazio é uma forma simple para adicionar um disco de dados, porque o Azure cria o ficheiro. vhd para si e armazena-os na conta de armazenamento.

1. Clique em **máquinas virtuais (clássicas)**e, em seguida, selecione a VM adequada.

2. No menu de definições, clique em **discos**.

   ![Anexar um disco novo vazio](./media/howto-attach-disk-windows-linux/menudisksattachnew.png)

3. Na barra de comandos, clique em **anexar novo**.  
    O **anexar novo disco** é apresentada a caixa de diálogo.

    ![Anexe um novo disco](./media/howto-attach-disk-windows-linux/newdiskdetail.png)

    Preencha as seguintes informações:
    - No **nome de ficheiro**, aceite o nome predefinido ou escreva outro para o ficheiro. vhd. O disco de dados utiliza um nome gerado automaticamente, mesmo que escreva outro nome para o ficheiro. vhd.
    - Selecione o **tipo** do disco de dados. Todas as máquinas virtuais suportam discos padrão. Número de máquinas virtuais também suporta discos premium.
    - Selecione o **tamanho (GB)** do disco de dados.
    - Para **alojar a colocação em cache**, escolha none ou só de leitura.
    - Clique em OK para terminar.

4. Depois do disco de dados é criado e ligado, este está listado na secção discos da VM.

   ![Disco de dados nova e vazio anexado com êxito](./media/howto-attach-disk-windows-linux/newdiskemptysuccessful.png)

> [!NOTE]
> Depois de adicionar um disco de dados, terá de iniciar sessão para a VM e inicializar o disco para que possa ser utilizado.

## <a name="how-to-attach-an-existing-disk"></a>Como: anexar um disco existente
Para anexar um disco existente, tem de ter um .vhd disponível numa conta de armazenamento. Utilize o [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) cmdlet para carregar o ficheiro. vhd para a conta de armazenamento. Depois de ter criado e carregado o ficheiro. vhd, poderá anexá-la a uma VM.

1. Clique em **máquinas virtuais (clássicas)**e, em seguida, selecione a máquina virtual adequada.

2. No menu de definições, clique em **discos**.

3. Na barra de comandos, clique em **anexar existente**.

    ![Anexar disco de dados](./media/howto-attach-disk-windows-linux/menudisksattachexisting.png)

4. Clique em **localização**. Apresentam as contas de armazenamento disponível. Em seguida, selecione uma conta de armazenamento adequado aos listados.

    ![Forneça a conta de armazenamento do disco](./media/howto-attach-disk-windows-linux/existdiskstorageaccounts.png)

5. A **conta de armazenamento** contém um ou mais contentores que contêm as unidades de disco (vhds). Selecione o contentor adequado aos listados.

    ![Forneça o contentor do windows de máquinas virtuais](./media/howto-attach-disk-windows-linux/existdiskcontainers.png)

6. O **vhds** painel lista as unidades de disco contidas no contentor. Clique dos discos e, em seguida, clique em selecionar.

    ![Forneça a imagem de disco para o windows de máquinas virtuais](./media/howto-attach-disk-windows-linux/existdiskvhds.png)

7. O **anexar o disco existente** painel mostra novamente, com a localização que contém a conta de armazenamento, contentor e selecionado disco rígido (vhd) para adicionar a máquina virtual.

  Definir **alojar a colocação em cache** como none ou leitura apenas, em seguida, clique em OK.

    ![Disco de dados foi exposto com êxito](./media/howto-attach-disk-windows-linux/exisitingdisksuccessful.png)
