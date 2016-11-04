### Configurar uma Etiqueta de DNS para o endereço IP público
Para ligar ao Motor de Base de Dados do SQL Server a partir da Internet, configure primeiro uma Etiqueta de DNS para o seu endereço IP público.

> [!NOTE]
> As Etiquetas de DNS não são necessárias se planear ligar apenas a instância do SQL Server dentro da mesma Virtual Network ou apenas localmente.
> 
> 

Para criar uma Etiqueta de DNS, comece por selecionar **Máquinas virtuais** no portal. Selecione a sua VM do SQL Server para ver as respetivas propriedades.

1. No painel da máquina virtual, selecione o **Endereço IP público**.
   
    ![endereço IP público.](./media/virtual-machines-sql-server-connection-steps/rm-public-ip-address.png)
2. Nas propriedades do Endereço IP público, expanda **Configuração**.
3. Introduza um nome para a Etiqueta de DNS. Este nome é um Registo A que pode ser utilizado para ligar à sua VM do SQL Server diretamente pelo nome em vez de ser pelo Endereço IP.
4. Clique no botão **Guardar**.
   
    ![etiqueta de DNS](./media/virtual-machines-sql-server-connection-steps/rm-dns-label.png)

### Ligar ao Motor da Base de Dados a partir de outro computador
1. Num computador ligado à Internet, abra o SQL Server Management Studio (SSMS).
2. Na caixa de diálogo **Ligar ao Servidor** ou **Ligar ao Motor de Base de Dados**, edite o valor **Nome do servidor**. Introduza o nome DNS completo da máquina virtual (determinado na tarefa anterior).
3. Na caixa **Autenticação**, selecione **Autenticação do SQL Server**.
4. Na caixa **Início de sessão**, escreva o nome de um início de sessão SQL válido.
5. Na caixa **Palavra-passe**, escreva a palavra-passe do início de sessão.
6. Clique em **Ligar**.
   
    ![ligação SSMS](./media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png)

<!--HONumber=Sep16_HO3-->


