### <a name="prerequisites"></a>Pré-requisitos
* Um [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol) conta  

Antes de poder utilizar a sua conta SFTP numa aplicação lógica, tem de autorizar a aplicação lógica para ligar à sua conta SFTP. Felizmente, pode fazê-facilmente na sua aplicação lógica no Portal do Azure.  

Eis os passos para autorizar a aplicação lógica para ligar à sua conta SFTP:  

1. Para criar uma ligação ao SFTP, no designer de aplicação lógica, selecione **Mostrar Microsoft APIs geridas** na lista pendente, em seguida, introduza *SFTP* na caixa de pesquisa. Selecione o **SFTP - quando um ficheiro é adicionado ou modificado** acionador:  
   ![Imagem de ligação online SFTP 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. Se ainda não criou quaisquer ligações a SFTP antes, irá obter lhe fornecer as credenciais SFTP. Estas credenciais serão utilizadas para autorizar a aplicação lógica para ligar a e aceder aos dados da sua conta SFTP:  
   ![Imagem de ligação online SFTP 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. Repare a ligação foi criada e está agora livre para continuar com os outros passos da sua aplicação lógica:   
   ![Imagem de ligação online SFTP 3](./media/connectors-create-api-sftp/sftp-3.png) 

