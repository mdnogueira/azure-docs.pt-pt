## <a name="transfer-local-files-to-cloud-shell"></a>Transferência de ficheiros locais para a Shell de nuvem
O `clouddrive` diretório sincroniza-se com o painel do portal de armazenamento do Azure. Utilize este painel para transferir os ficheiros de locais de ou para a partilha de ficheiros. Atualizar ficheiros a partir de Shell de nuvem é refletida no armazenamento de ficheiros GUI quando atualizar o painel.

### <a name="download-files"></a>Transferir ficheiros

![Lista de ficheiros locais](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. No portal do Azure, aceda à partilha de ficheiros montada.
2. Selecione o ficheiro de destino.
3. Selecione o **transferir** botão.

### <a name="upload-files"></a>Carregar ficheiros

![Ficheiros de locais para ser carregado](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. Vá para a partilha de ficheiros montada.
2. Selecione o **carregar** botão.
3. Selecione o ficheiro ou ficheiros que pretende carregar.
4. Confirme o carregamento.

Deverá ver agora os ficheiros que estão acessíveis no seu `clouddrive` diretório na Cloud Shell.