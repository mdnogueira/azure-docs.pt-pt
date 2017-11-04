
1. Para aumentar os privilégios, escreva:
   
        sudo -s
   
    Introduza a sua palavra-passe.
2. Para instalar a edição de servidor de Comunidade do MySQL, escreva:
   
        zypper install mysql-community-server
   
    Aguarde enquanto o MySQL transfere e instala.
3. Para definir o MySQL para iniciar quando efetua o arranque do sistema, escreva:
   
        insserv mysql
4. Inicie manualmente o daemon de MySQL (mysqld) com este comando:
   
        rcmysql start
   
    Para verificar o estado de que o daemon de MySQL, escreva:
   
        rcmysql status
   
    Para parar o daemon de MySQL, escreva:
   
        rcmysql stop
   
   > [!IMPORTANT]
   > Após a instalação, a palavra-passe de raiz do MySQL está vazia por predefinição. Recomendamos que execute **mysql\_segura\_instalação**, um script que ajuda a proteger MySQL. O script pede-lhe para alterar a palavra-passe de raiz do MySQL, remova as contas de utilizador anónimo, desativar inícios de sessão remoto raiz, remover bases de dados de teste e recarregar a tabela de privilégios. Recomendamos que responder Sim para todas estas opções e altere a palavra-passe de raiz.
   > 
   > 
5. Escreva este para executar o script de instalação de MySQL do script:
   
        mysql_secure_installation
6. Inicie sessão no MySQL:
   
        mysql -u root -p
   
    Introduza a palavra-passe de raiz MySQL (que é alterado no passo anterior) e irá ser apresentado numa linha de onde pode emitir instruções SQL para interagir com a base de dados.
7. Para criar um novo utilizador do MySQL, execute o seguinte no **mysql >** linha:
   
        CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
   
    Tenha em atenção, a ponto e vírgula (;) no fim das linhas são cruciais para terminar os comandos.
8. Para criar uma base de dados e conceder a `mysqluser` permissões de utilizador no mesmo, emita os seguintes comandos:
   
        CREATE DATABASE testdatabase;
        GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
   
    Tenha em atenção que os nomes de utilizador de base de dados e as palavras-passe só são utilizadas pelos scripts ligar à base de dados.  Os nomes das contas de utilizador de base de dados não representa necessariamente contas de utilizador real no sistema.
9. Para iniciar sessão a partir de outro computador, escreva:
   
        GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';
   
    onde `ip-address` é o endereço IP do computador a partir do qual irá ligar ao MySQL.
10. Para sair do utilitário de administração de base de dados MySQL, escreva:
    
        quit

## <a name="add-an-endpoint"></a>Adicionar um ponto final
1. Depois de instalado o MySQL, terá de configurar um ponto final para aceder remotamente MySQL. Inicie sessão no [portal clássico do Azure][AzurePortal]. Clique em **máquinas virtuais**, clique no nome da nova máquina virtual e, em seguida, clique em **pontos finais**.
2. Clique em **adicionar** na parte inferior da página.
3. Adicionar um ponto final com o nome "MySQL" com o protocolo **TCP**, e **pública** e **privada** portas configuradas para "3306".
4. Para ligar remotamente à máquina virtual do seu computador, escreva:
   
        mysql -u mysqluser -p -h <yourservicename>.cloudapp.net
   
    Por exemplo, se utilizar a máquina virual que foi criada neste tutorial, escreva este comando:
   
        mysql -u mysqluser -p -h testlinuxvm.cloudapp.net

[MySQLDocs]: http://dev.mysql.com/doc/
[AzurePortal]: http://manage.windowsazure.com

[Image9]: ./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpointMySQL.png
