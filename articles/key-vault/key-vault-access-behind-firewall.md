<properties
    pageTitle="Aceder ao Cofre de Chaves protegido por firewall | Microsoft Azure"
    description="Saiba como aceder ao Cofre de Chaves de acesso a partir de uma aplicação protegida por uma firewall"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/13/2016"
    ms.author="ambapat"/>


# Aceder ao Cofre de Chaves protegido por firewall
### P: A minha aplicação cliente do cofre de chaves tem de estar protegida por uma firewall, que portas/anfitriões/endereços IP devo abrir para ativar o acesso ao cofre de chaves?

Para aceder a um cofre de chaves, a sua aplicação cliente do cofre de chaves tem de poder aceder a vários pontos finais para várias funcionalidades.

- Autenticação (através do Azure Active Directory)
- Gestão do Cofre de Chaves (que inclui criar/ler/atualizar/eliminar e também definir as políticas de acesso) através do Azure Resource Manager
- Aceder e gerir objetos (chaves e segredos) armazenados no próprio cofre de chaves, vai através do ponto final específico do cofre de chaves (por exemplo, [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net)).  

Dependendo da configuração e do ambiente, existem algumas variações.   

## Portas

Todo o tráfego do cofre de chaves para as três funções (autenticação, gestão e acesso ao plano de dados) é feito por HTTPS: Porta 443. No entanto, para CRL, existirá ocasionalmente tráfego HTTP (porta 80). Os clientes que suportem OCSP, não devem atingir o CRL, mas, ocasionalmente, poderão alcançar [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).  

## Autenticação

A aplicação cliente do Cofre de Chaves terá de aceder a pontos finais do Azure Active Directory para autenticação. O ponto final utilizado depende da configuração do inquilino do AAD e o tipo de principal - principal de utilizador, principal de serviço e o tipo de conta, por exemplo, Microsoft Account ou com o ID da Organização.  

| Tipo de Principal | Ponto final:porta |
|----------------|---------------|
| Utilizador com Conta Microsoft<br> (por exemplo, utilizador@hotmail.com) | **Global:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure US Government:**<br> login-us.microsoftonline.com:443<br><br>**Azure Alemanha:**<br> login.microsoftonline.de:443<br><br> e <br>login.live.com:443   |
| Principal de Utilizador/Serviço com o ID da Organização com o AAD (por exemplo, utilizador@contoso.com) | **Global:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure US Government:**<br> login-us.microsoftonline.com:443<br><br>**Azure Alemanha:**<br> login.microsoftonline.de:443 |
| Principal de Utilizador/Serviço com o ID da Organização + ADFS ou outro ponto final federado (por exemplo, utilizador@contoso.com) | Todos os pontos finais acima para ID da Organização mais ADFS ou outros pontos finais federados |

Existem outros cenários possíveis complexos. Consulte o [Fluxo de Autenticação do Azure Active Directory](/documentation/articles/active-directory-authentication-scenarios/), [Integrar Aplicações com o Azure Active Directory](/documentation/articles/active-directory-integrating-applications/) e [Protocolos de Autenticação do Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx) para obter informações adicionais.  

## Gestão do Cofre de Chaves

Para a Gestão do Cofre de Chaves (CRUD e definição da política de acesso), a aplicação cliente do cofre de chaves tem de aceder ao ponto final do Azure Resource Manager.  

| Tipo de operação | Ponto final:porta |
|----------------|---------------|
| Operações do Painel de Controlo do Cofre de Chaves<br> através do Azure Resource Manager | **Global:**<br> management.azure.com:443<br><br> **Azure China:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> management.microsoftazure.de:443 |
| Graph API do Azure Active Directory | **Global:**<br> graph.windows.net:443<br><br> **Azure China:**<br> graph.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> graph.windows.net:443<br><br> **Azure Alemanha:**<br> graph.cloudapi.de:443 |

## Operações do Cofre de Chaves

Para todas as operações criptográficas e de gestão do objeto do cofre de chaves (chaves e segredos), o cliente do cofre de chaves precisa de aceder ao ponto final do cofre de chaves. Dependendo da localização do seu Cofre de Chaves, o sufixo DNS do ponto final é diferente. O ponto final do Cofre de chaves é o formato: <nome-do-cofre>.<sufixo-dns-específico-da-região>, tal como descrito na tabela abaixo.  

| Tipo de operação | Ponto final:porta |
|----------------|---------------|
| As operações do Cofre de Chaves, como operações de criptografia em chaves, criar/ler/atualizar/eliminar chaves e segredos, definir/obter etiquetas e outros atributos de objetos de cofre de chaves (chaves/segredos)     | **Global:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |

## Intervalos de Endereços IP

O serviço do Cofre de Chaves, por sua vez, utiliza outros recursos do Azure como infraestrutura PaaS e, por conseguinte, não é possível fornecer um intervalo de endereços IP específico que os pontos finais do serviço do cofre de chaves terão num determinado momento. No entanto, se a sua firewall só suporta intervalos do endereço IP, então consulte o documento [Intervalos IP do Centro de Dados do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653) .   Para autenticação e identidade (Azure Active Directory), a aplicação tem de poder estabelecer ligação com os pontos finais descritos na [Autenticação e Endereços de identidade](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## Passos Seguintes

- Se tiver perguntas sobre o Cofre de Chaves, visite os [Fóruns do Cofre de Chaves do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)



<!--HONumber=Sep16_HO3-->


