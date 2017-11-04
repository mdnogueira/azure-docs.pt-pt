## <a name="create-a-storage-account-using-the-azure-portal"></a>Criar uma conta de armazenamento utilizando o portal do Azure

Em primeiro lugar, crie uma nova conta de armazenamento para fins gerais para utilizar este guia de introdução. 

1. Vá para o [portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure. 
2. No Hub menu, selecione **novo** > **armazenamento** > **conta de armazenamento - BLOBs, ficheiro, tabela, fila**. 
3. Introduza um nome exclusivo para a sua conta de armazenamento. Mantenha estas regras em mente para atribuir nomes a sua conta de armazenamento:
    - O nome deve ter entre 3 e 24 carateres de comprimento.
    - O nome pode conter apenas letras minúsculas e números.
4. Certifique-se de que estão definidos os valores predefinidos seguintes: 
    - **Modelo de implementação** está definido como **do Resource manager**.
    - **Tipo de conta** está definido como **fins gerais**.
    - **Desempenho** está definido como **padrão**.
    - **Replicação** está definido como **armazenamento localmente redundante (LRS)**.
5. Selecione a sua subscrição. 
6. Para **grupo de recursos**, crie um novo e atribua um nome exclusivo. 
7. Selecione o **localização** a utilizar para a sua conta de armazenamento.
8. Verifique **afixar ao dashboard** e clique em **criar** para criar a conta de armazenamento. 

Depois de criar a sua conta do storage, está afixada ao dashboard. Clique na mesma para abri-lo. Em **definições**, clique em **chaves de acesso**. Selecione a chave primária e copie associada **cadeia de ligação** para a área de transferência, em seguida, cole-o num editor de texto para utilização posterior.
