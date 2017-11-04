Agora que adicionou um acionador, a respetiva hora de fazer algo interessantes com os dados que são gerados pelo acionador. Siga estes passos para adicionar um o **SFTP - extrair pasta** ação. Esta ação irá extraia os conteúdos de um ficheiro, se forem satisfeitas as condições definidas. 

Para configurar esta ação, terá de fornecer as seguintes informações. Vai notar que é fácil de utilizar dados gerados pelo acionador como entrada para algumas das propriedades para o novo ficheiro:

| SFTP - propriedade de pasta extract | Descrição |
| --- | --- |
| Caminho de ficheiro do arquivo de origem |Este é o caminho para o ficheiro que está a ser extraído. Pode selecionar uma dos tokens a partir de uma ação anterior ou procurar o servidor SFTP para localizar o caminho do ficheiro. |
| Caminho da pasta de destino |Este é o caminho onde os ficheiros extraídos irão ser colocados. Pode selecionar uma dos tokens a partir de uma ação anterior, como o caminho de destino ou procurar o servidor SFTP e selecione um caminho. |
| Substituir? |Indica se for encontrado um ficheiro com o mesmo nome que os ficheiros extraídos no caminho da pasta de destino, se o ficheiro existente deve ser substituído ou não. |

Vamos começar a adicionar a ação para extrair os ficheiros, se for avaliada a condição definida anteriormente como *verdadeiro*. 

1. Selecione **adicionar uma ação**.        
   ![Imagem de condição de ação de SFTP 6](./media/connectors-create-api-sftp/condition-6.png)   
2. Selecione o **SFTP - extrair pasta** ação      
   ![Imagem de condição de ação de SFTP 7](./media/connectors-create-api-sftp/condition-7.png)   
3. Selecione **caminho de ficheiro do arquivo de origem**              
   ![Imagem de condição de ação de SFTP 9](./media/connectors-create-api-sftp/condition-9.png)   
4. Selecione o **caminho do ficheiro** token. Isto indica que irá utilizar o caminho do ficheiro do ficheiro que localizar o acionador como o caminho de ficheiro do arquivo de origem.           
   ![Imagem de condição de ação de SFTP 10](./media/connectors-create-api-sftp/condition-10.png)   
5. Selecione **caminho da pasta de destino**           
   ![Imagem de condição de ação de SFTP 11](./media/connectors-create-api-sftp/condition-11.png)   
6. Selecione o **caminho do ficheiro** token. Isto indica que irá utilizar o caminho do ficheiro do ficheiro que localizar o acionador como o caminho de destino para os ficheiros extraídos.   
7. Introduza *\ExtractedFile* no **caminho da pasta de destino** controlo. Fazê-lo imediatamente após o token de caminho de ficheiro no controlo de caminho de pasta de destino.         
   ![Imagem de condição de ação de SFTP 12](./media/connectors-create-api-sftp/condition-12.png)   
8. Introduza *verdadeiro* no **substituir?* controlo para indicar que os ficheiros existentes devem ser substituídos se tiverem o mesmo nome que os ficheiros extraídos.      
   ![Imagem de condição de ação de SFTP 13](./media/connectors-create-api-sftp/condition-13.png)   
9. Guardar as alterações ao seu fluxo de trabalho  

