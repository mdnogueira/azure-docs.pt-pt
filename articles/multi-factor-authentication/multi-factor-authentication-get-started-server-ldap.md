<properties 
    pageTitle="Autenticação LDAP e Servidor Multi-Factor Authentication do Azure" 
    description="Esta é a página do Multi-Factor Authentication do Azure que irá ajudar a implementar a Autenticação LDAP e o Servidor Multi-Factor Authentication do Azure." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtand"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/12/2016" 
    ms.author="billmath"/>

# Autenticação LDAP e Servidor Multi-Factor Authentication do Azure 


Por predefinição, o Servidor Multi-Factor Authentication do Azure está configurado para importar ou sincronizar os utilizadores do Active Directory. No entanto, pode ser configurado para ser vinculado a diferentes diretórios LDAP, como um diretório do ADAM ou controlador de domínio específico do Active Directory. Quando configurado para ligar a um diretório através de LDAP, o Servidor Multi-Factor Authentication do Azure pode ser configurado para atuar como um proxy LDAP para efetuar as autenticações. Também permite a utilização do enlace de LDAP como um destino RADIUS para pré-autenticação de utilizadores ao utilizar a Autenticação do IIS ou para autenticação primária no Portal de Utilizador do Multi-Factor Authentication do Azure.

Quando utilizar o Multi-Factor Authentication do Azure como um proxy LDAP, o Servidor Multi-Factor Authentication do Azure é inserido entre o cliente LDAP (por exemplo, a aplicação VPN) e o servidor de diretório LDAP para poder adicionar a autenticação multifator. Para o Multi-Factor Authentication do Azure funcionar, o Servidor Multi-Factor Authentication do Azure tem de ser configurado para comunicar com os servidores cliente e o diretório LDAP. Nesta configuração, o Servidor Multi-Factor Authentication do Azure aceita os pedidos LDAP das aplicações e dos servidores cliente e reencaminha-os para o servidor de diretório LDAP de destino para validar as credenciais principais. Se a resposta do diretório LDAP mostrar que as credenciais principais são válidas, o Multi-Factor Authentication do Azure efetua a autenticação de segundo fator e envia uma resposta de volta para o cliente LDAP. A autenticação completa apenas terá êxito se a autenticação no servidor LDAP e a autenticação multifator forem bem-sucedidas. 





## Configuração da Autenticação LDAP


Para configurar a autenticação LDAP, instale o Servidor Multi-Factor Authentication do Azure num servidor do Windows. Utilize o seguinte procedimento: 

1. No Servidor Multi-Factor Authentication do Azure, clique no ícone Autenticação LDAP no menu esquerdo.
2. Selecione a caixa de verificação Ativar Autenticação LDAP.![Autenticação LDAP](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png) 
3. No separador Clientes, altere a porta TCP e a porta SSL se pretender vincular o serviço LDAP do Multi-Factor Authentication do Azure a portas não padrão para escutar os pedidos LDAP dos clientes que serão configurados.
4. Se planear utilizar o LDAPS do cliente para o Servidor Multi-Factor Authentication do Azure, é necessário instalar um certificado SSL no servidor no qual o Servidor está a ser executado. Clique no botão Procurar... junto à caixa Certificado SSL e selecione o certificado instalado que será utilizado para a ligação segura. 
5. Clique no botão Adicionar...
6. Na caixa de diálogo Adicionar Cliente LDAP, introduza o endereço IP da aplicação ou servidor que irá autenticar no Servidor e um nome de Aplicação (opcional). O nome da Aplicação aparece nos relatórios do Multi-Factor Authentication do Azure e poderá ser apresentado nas mensagens de autenticação SMS ou da Aplicação Móvel.
7. Selecione a caixa de correspondência de utilizador Exigir autenticação Azure Multi-Factor Authentication se todos os utilizadores tiverem sido ou forem importados para o Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de utilizadores ainda não tiverem sido importados para o Servidor de e/ou estiverem excluídos da autenticação multifator, deixe a caixa desmarcada. Consulte o ficheiro de ajuda para obter informações adicionais sobre esta funcionalidade. 
8. Pode repetir os passos 5 a 7 para adicionar clientes LDAP adicionais.
9. Quando o Multi-Factor Authentication do Azure está configurado para receber autenticações LDAP, é necessário utilizar o proxy nessas autenticações para o diretório LDAP. Assim, o separador Destino apenas apresenta uma única opção desativada para utilizar um destino LDAP. Para configurar a ligação do diretório LDAP, clique no ícone Integração de Diretórios. 
10. No separador Definições, selecione o botão de opção Utilizar configuração de LDAP específica.
11. Clique no botão Editar...
12. Na caixa de diálogo Editar Configuração de LDAP, preencha os campos com as informações necessárias para ligar ao diretório LDAP. As descrições dos campos estão incluídas na tabela abaixo. Nota: estas informações também estão incluídas no ficheiro de ajuda do Servidor Multi-Factor Authentication do Azure.![Integração de Diretórios](./media/multi-factor-authentication-get-started-server-ldap/ldap.png) 
13. Teste a ligação LDAP ao clicar no botão Testar.
14. Se o teste de ligação LDAP for concluído com êxito, clique no botão OK. 
15. Clique no separador Filtros. O Servidor está pré-configurada para carregar contentores, grupos de segurança e utilizadores do Active Directory. Se vincular a outro diretório LDAP, provavelmente terá de editar os filtros apresentados. Clique na ligação Ajuda para obter mais informações sobre filtros.
16. Clique no separador Atributos. O Servidor está pré-configurada para mapear os atributos do Active Directory.
17. Se vincular a outro diretório LDAP ou para alterar os mapeamentos de atributos pré-configurados, clique no botão Editar...
18. Na caixa de diálogo Editar Atributos, modifique os mapeamentos de atributos LDAP para o seu diretório. Os nomes de atributos podem ser introduzidos ou selecionados clicando no botão ... junto a cada campo.
19. Clique na ligação Ajuda para obter mais informações sobre atributos.
20. Clique no botão OK.
21. Clique no ícone Definições da Empresa e selecione o separador Resolução de Nomes de Utilizador.
22. Se ligar ao Active Directory a partir de um servidor associado a um domínio, deverá deixar o botão de opção Utilizar identificadores de segurança (SIDs) do Windows para nomes de utilizador correspondentes selecionado. Caso contrário, selecione o botão de opção Utilizar o atributo de identificador exclusivo de LDAP para nomes de utilizador correspondentes. Quando selecionado, o Servidor Multi-Factor Authentication do Azure tentará resolver cada nome de utilizador para um identificador exclusivo no diretório LDAP. Será efetuada uma pesquisa LDAP nos atributos de Nome de Utilizador definidos no separador Integração de Diretórios -> Atributos. Quando um utilizador efetua a autenticação, o nome de utilizador será resolvido para o identificador exclusivo no diretório LDAP e o identificador exclusivo será utilizado para corresponder o utilizador no ficheiro de dados do Multi-Factor Authentication do Azure. Esta ação permite comparações não sensíveis a maiúsculas e minúsculas, bem como formatos de nomes de utilizador longos e curtos. Isto conclui a configuração do Servidor Multi-Factor Authentication do Azure. O Servidor está agora a escutar nas portas configuradas para pedidos de acesso LDAP dos clientes configurados e definido para utilizar o proxy nesses pedidos para o diretório LDAP para autenticação.


## Configuração do Cliente LDAP

Para configurar o cliente LDAP, utilize as diretrizes:

- Configure a sua aplicação ou servidor para efetuar a autenticação através de LDAP no Servidor Multi-Factor Authentication do Azure como se fosse o seu diretório LDAP. Deve utilizar as mesmas definições que normalmente utilizaria para ligar diretamente ao seu diretório LDAP, exceto para o nome do servidor ou o endereço IP, que será o do Servidor Multi-Factor Authentication do Azure. 
- Configure o tempo limite de LDAP para 30 a 60 segundos, de modo a existir tempo suficiente para validar as credenciais do utilizador no diretório LDAP, efetuar a autenticação de segundo fator, receber a respetiva resposta e, em seguida, responder ao pedido de acesso LDAP. 
- Se utilizar o LDAPS, a aplicação ou o servidor que cria as consultas LDAP tem de confiar no certificado SSL instalado no Servidor Multi-Factor Authentication do Azure.




<!--HONumber=Jun16_HO2-->


