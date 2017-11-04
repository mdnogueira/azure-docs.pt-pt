## <a name="download-install-and-register-the-azure-backup-agent"></a>Transferir, instalar e registar o agente de cópia de segurança do Azure
Depois de criar o Cofre de cópia de segurança do Azure, deve ser instalado um agente em cada uma das suas máquinas do Windows (Windows Server, cliente Windows, o servidor do System Center Data Protection Manager ou máquina do servidor de cópia de segurança do Azure) que permite fazer cópias de segurança de dados e aplicações para o Azure .

1. Iniciar sessão para o [Portal de gestão](https://manage.windowsazure.com/)
2. Clique em **dos serviços de recuperação**, em seguida, selecione o Cofre de cópia de segurança que pretende registar um servidor. É apresentada a página de início rápido de mensagens em fila para esse cofre de cópias de segurança.
   
    ![Início rápido](./media/backup-install-agent/quickstart.png)
3. Na página início rápido, clique o **cliente para o Windows Server ou o System Center Data Protection Manager ou o Windows** opção em **Transferir agente**. Clique em **guardar** para copiá-lo para o computador local.
   
    ![Guardar o agente](./media/backup-install-agent/agent.png)
4. Assim que o agente estiver instalado, faça duplo clique MARSAgentInstaller.exe para iniciar a instalação do agente do Backup do Azure. Escolha a pasta de instalação e a pasta scratch necessário para o agente. A localização da cache especificada tem de ter espaço livre que é, pelo menos, 5% dos dados de cópia de segurança.
5. Se utilizar um servidor proxy para ligar à internet, o **configuração de Proxy** ecrã, introduza os detalhes do servidor proxy. Se utilizar um proxy autenticado, introduza os detalhes de nome e palavra-passe do utilizador neste ecrã.
6. O agente do Backup do Azure instala .NET Framework 4.5 e o Windows PowerShell (se ainda não estiver disponível) para concluir a instalação.
7. Assim que o agente estiver instalado, clique em de **avançar para o registo** botão para continuar com o fluxo de trabalho.
   
   ![Registar](./media/backup-install-agent/register.png)
8. No ecrã de credenciais de cofre, procure e selecione o ficheiro de credenciais do cofre que foi anteriormente transferido.
   
    ![Credenciais do Cofre](./media/backup-install-agent/vc.png)
   
    O ficheiro de credenciais do cofre só é válido para 48 horas (após a transferência do portal). Se encontrar qualquer erro este ecrã (por exemplo "ficheiro de credenciais de cofre fornecido expirou"), início de sessão no portal do Azure e transfira o ficheiro de credenciais do cofre novamente.
   
    Certifique-se de que o ficheiro de credenciais do Cofre está disponível numa localização que pode ser acedida pela aplicação de configuração. Se ocorrerem erros relacionados com de acesso, copie o ficheiro de credenciais do cofre para uma localização temporária nesta máquina e repita a operação.
   
    Se ocorrer um erro de credenciais de cofre inválida (por exemplo, "credenciais de cofre inválidas fornecidas"), o ficheiro está danificado ou não tiver as credenciais mais recentes associado o serviço de recuperação. Repita a operação depois de transferir um novo ficheiro de credenciais do cofre a partir do portal. Este erro normalmente é utilizado se o utilizador clica no **credenciais do Cofre de transferência** opção no portal do Azure, sucessivamente rápida. Neste caso, apenas o ficheiro de credenciais de cofre segundo é válido.
9. No **definição de encriptação** ecrã, pode gerar uma frase de acesso ou fornecer uma frase de acesso (mínimo de 16 carateres). Lembre-se guardar o frase de acesso numa localização segura.
   
    ![Encriptação](./media/backup-install-agent/encryption.png)
   
   > [!WARNING]
   > Se o frase de acesso de perda ou esquecimento; Não pode ajudar a Microsoft recuperar os dados de cópia de segurança. O utilizador final possui a frase de acesso de encriptação e a Microsoft não têm visibilidade para a frase de acesso utilizada pelo utilizador final. Guarde o ficheiro numa localização segura conforme necessário durante uma operação de recuperação.
   > 
   > 
10. Assim que clicar no **concluir** botão, a máquina está registada com êxito para o Cofre e está agora pronto para iniciar a cópia de segurança em Microsoft Azure.
11. Ao utilizar o Microsoft Azure Backup autónomo pode modificar as definições especificadas durante o fluxo de trabalho de registo clicando no **alterar propriedades** opção no snap de mmc da cópia de segurança do Azure no.
    
    ![Alterar propriedades](./media/backup-install-agent/change.png)
    
    Em alternativa, ao utilizar o Data Protection Manager, pode modificar as definições especificadas durante o fluxo de trabalho de registo clicando a **configurar** opção selecionando **Online** sob o **Gestão** separador.
    
    ![Configurar o Azure Backup](./media/backup-install-agent/configure.png)

