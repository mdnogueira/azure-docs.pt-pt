### <a name="prepare-for-a-push-installation-on-a-linux-server"></a>Preparar uma instalação por push num servidor Linux

1. Certifique-se de que existe conectividade de rede entre o computador Linux e o servidor de processos.
2. Crie uma conta que possa ser utilizada pelo servidor de processos para aceder ao computador. A conta deve ser um utilizador **raiz** no servidor Linux de origem. (Utilize esta conta apenas para a instalação por push e para atualizações.)
3. Verifique se o ficheiro /etc/hosts no servidor Linux de origem tem entradas que mapeiam o nome de anfitrião local para endereços IP associados a todos os adaptadores de rede.
4. Instale os pacotes openssh, openssh-server e openssl mais recentes no computador que quer replicar.
5. Certifique-se de que o Secure Shell (SSH) está ativado e em execução na porta 22.
6. Ative a autenticação de subsistema e palavra-passe SFTP no ficheiro sshd_config:
  1.  Inicie sessão como **raiz**.
  2.  No ficheiro /etc/ssh/sshd_config, encontre a linha que começa por **PasswordAuthentication**.
  3.  Remova os comentários da linha e altere o valor para **sim**.
  4.  Encontre a linha que começa por **Subsystem** e remova os comentários da mesma.

     ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)
  5. Reinicie o serviço **sshd**.

7. Adicione a conta que criou no CSPSConfigtool.
    1.  Inicie sessão no servidor de configuração.
    2.  Abra **cspsconfigtool.exe**. (Está disponível como um atalho no ambiente de trabalho e na pasta %ProgramData%\home\svsystems\bin.)
    3.  No separador **Gerir Contas**, clique em **Adicionar Conta**.
    4.  Adicione a conta que criou. 
    5.  Introduza as credenciais que utiliza para ativar a replicação para um computador.
