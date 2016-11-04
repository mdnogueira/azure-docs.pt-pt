---
title: Autenticação RADIUS e Servidor Multi-Factor Authentication do Azure
description: Esta é a página do Multi-Factor Authentication do Azure que irá ajudar a implementar a Autenticação RADIUS e o Servidor Multi-Factor Authentication do Azure.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtand

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: kgremban

---
# Autenticação RADIUS e Servidor Multi-Factor Authentication do Azure
A secção Autenticação RADIUS permite-lhe ativar e configurar a autenticação RADIUS para o Servidor Multi-Factor Authentication do Azure. RADIUS é um protocolo padrão para aceitar pedidos de autenticação e processar esses pedidos. O Servidor Multi-Factor Authentication do Azure funciona como um servidor RADIUS e é inserido entre o cliente RADIUS (por exemplo, a aplicação VPN) e o destino de autenticação, que pode ser o Active Directory (AD), um diretório LDAP ou outro servidor RADIUS, para adicionar o Multi-Factor Authentication do Azure. Para o Multi-Factor Authentication do Azure funcionar, tem de configurar o Servidor Multi-Factor Authentication do Azure para poder comunicar com os servidores cliente e o destino de autenticação. O Servidor Multi-Factor Authentication do Azure aceita os pedidos de um cliente RADIUS, valida as credenciais relativamente ao destino de autenticação, adiciona o Multi-Factor Authentication do Azure e envia uma resposta novamente para o cliente RADIUS. A autenticação completa apenas terá êxito se a autenticação primária e o Multi-Factor Authentication do Azure forem bem-sucedidos.

> [!NOTE]
> O Servidor MFA apenas suporta os protocolos RADIUS PAP (protocolo de autenticação de palavras-passe) e MSCHAPv2 (Challenge Handshake Authentication Protocol da Microsoft) ao atuar como um servidor RADIUS.  Podem ser utilizados outros protocolos, como o EAP (protocolo de autenticação extensível), quando o servidor MFA funciona como um proxy RADIUS para outro servidor RADIUS que suporte esse protocolo, como o Microsoft NPS.
> </br>
> Quando utilizar outros protocolos nesta configuração, os tokens SMS e OATH unidirecionais não funcionarão, uma vez que o Servidor MFA não consegue iniciar uma resposta Challenge RADIUS com êxito utilizando esse protocolo.
> 
> 

![Autenticação Radius](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## Configuração da Autenticação RADIUS
Para configurar a autenticação RADIUS, instale o Servidor Multi-Factor Authentication do Azure num servidor do Windows. Se tiver um ambiente do Active Directory, o servidor deve ser associado ao domínio dentro da rede. Utilize o seguinte procedimento para configurar o Servidor Multi-Factor Authentication do Azure:

1. No Servidor Multi-Factor Authentication do Azure, clique no ícone Autenticação RADIUS no menu esquerdo.
2. Selecione a caixa de verificação Ativar autenticação RADIUS.
3. No separador Clientes, altere a(s) porta(s) de Autenticação e a(s) porta(s) de Gestão de Contas se pretender vincular o serviço RADIUS do Multi-Factor Authentication do Azure a portas não padrão para escutar os pedidos RADIUS dos clientes que serão configurados.
4. Clique no botão Adicionar...
5. Na caixa de diálogo Adicionar Cliente RADIUS, introduza o endereço IP da aplicação/servidor que irá autenticar no Servidor Multi-Factor Authentication do Azure, um nome de Aplicação (opcional) e um segredo Partilhado. O segredo partilhado terá de ser o mesmo no Servidor Multi-Factor Authentication do Azure e na aplicação/servidor. O nome da Aplicação aparece nos relatórios do Multi-Factor Authentication do Azure e poderá ser apresentado nas mensagens de autenticação SMS ou da Aplicação Móvel.
6. Selecione a caixa de correspondência de utilizador Exigir autenticação Multi-Factor Authentication se todos os utilizadores tiverem sido ou forem importados para o Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de utilizadores ainda não tiverem sido importados para o Servidor de e/ou estiverem excluídos da autenticação multifator, deixe a caixa desmarcada. Consulte o ficheiro de ajuda para obter informações adicionais sobre esta funcionalidade.
7. Selecione a caixa Ativar token OATH de contingência se os utilizadores forem utilizar a autenticação de aplicações móveis Multi-Factor Authentication do Azure e pretender utilizar códigos de acesso OATH como uma autenticação de contingência para a chamada telefónica fora da banda, SMS ou notificação push.
8. Clique no botão OK.
9. Pode repetir os passos 4 a 8 para adicionar clientes RADIUS adicionais.
10. Clique no separador Destino.
11. Se o Servidor Multi-Factor Authentication do Azure estiver instalado num servidor associado a um domínio no ambiente do Active Directory, selecione o domínio do Windows.
12. Se os utilizadores tiverem de ser autenticados relativamente a um diretório LDAP, selecione o enlace de LDAP. Quando utilizar o enlace de LDAP, tem de clicar no ícone de Integração de Diretórios e editar a configuração LDAP no separador Definições, para que o Servidor possa vincular ao seu diretório. Pode encontrar instruções para a configuração de LDAP no guia de configuração do Proxy LDAP.
13. Se os utilizadores tiverem de ser autenticados relativamente a outro servidor RADIUS, selecione o(s) servidor(es) RADIUS.
14. Configure o servidor em que será utilizado o proxy nos pedidos RADIUS clicando no botão Adicionar...
15. Na caixa de diálogo Adicionar Servidor RADIUS, introduza o endereço IP do servidor RADIUS e um segredo Partilhado. O segredo partilhado terá de ser o mesmo no Servidor Multi-Factor Authentication do Azure e no servidor RADIUS. Altere a porta de Autenticação e a porta de Gestão de Contas se forem utilizadas portas diferentes pelo servidor RADIUS.
16. Clique no botão OK.
17. É necessário adicionar o Servidor Multi-Factor Authentication do Azure como um cliente RADIUS no outro servidor RADIUS para que processe os pedidos de acesso enviados do Servidor Multi-Factor Authentication do Azure. Tem de utilizar o mesmo segredo partilhado configurado no Servidor Multi-Factor Authentication do Azure.
18. Pode repetir este passo para adicionar mais servidores RADIUS e configurar a ordem pela qual o Servidor os deve chamar com os botões Mover Para Cima e Mover Para Baixo. Isto conclui a configuração do Servidor Multi-Factor Authentication do Azure. O Servidor está agora a escutar nas portas configuradas para pedidos de acesso RADIUS dos clientes configurados.   

## Configuração do Cliente RADIUS
Para configurar o cliente RADIUS, utilize as diretrizes:

* Configure a sua aplicação/servidor para autenticar através de RADIUS no endereço IP do Servidor Multi-Factor Authentication do Azure, que funcionará como servidor RADIUS.
* Utilize o mesmo segredo partilhado configurado acima.
* Configure o tempo limite de RADIUS para 30 a 60 segundos, de modo a existir tempo suficiente para validar as credenciais do utilizador, efetuar a autenticação multifator, receber a respetiva resposta e, em seguida, responder ao pedido de acesso RADIUS.

<!--HONumber=Sep16_HO3-->


