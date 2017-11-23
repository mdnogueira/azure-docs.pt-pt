---
title: "Do Azure Active Directory Connect: FAQ – | Microsoft Docs"
description: "Esta página tem perguntas mais frequentes sobre o Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2017
ms.author: billmath
ms.openlocfilehash: 9d11795aa8b768a8574c3f8bc375b74402825ba3
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="frequently-asked-questions-for-azure-active-directory-connect"></a>Perguntas mais frequentes sobre Azure Active Directory Connect

## <a name="general-installation"></a>Instalação geral
**P: instalação funcionará se o Administrador Global do AD do Azure tiver 2FA ativado?**  
Com as compilações de Fevereiro de 2016, isto é suportado.

**P: existe uma forma de instalar o Azure AD Connect automático?**  
Só é suportada para instalar o Azure AD Connect, utilizando o Assistente de instalação. Não é suportada uma instalação automática e silenciosa.

**P: Tenho uma floresta em que um domínio não pode ser contactado. Como instalar o Azure AD Connect?**  
Com as compilações de Fevereiro de 2016, isto é suportado.

**P: o agente de estado de funcionamento de AD DS funciona no server core?**  
Sim. Depois de instalar o agente, pode concluir o processo de registo utilizando o cmdlet PowerShell seguinte: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

## <a name="network"></a>Rede
**P: Posso tem uma firewall, o dispositivo de rede ou outra coisa que limita as ligações de tempo máximo pode permanecer aberta na minha rede. Quanto tempo os meus limiar de tempo limite de lado do cliente estará ao utilizar o Azure AD Connect?**  
Todo o software de rede, dispositivos físicos ou há mais alguma coisa que limita o tempo máximo de ligações podem permanecer abertas deve utilizar um limiar de, pelo menos, 5 minutos (300 segundos) para a conectividade entre o servidor onde está instalado o cliente do Azure AD Connect e Azure Active Directory. Isto também se aplica a todas as ferramentas de sincronização do Microsoft Identity lançadas anteriormente.

**P: são SLDs (domínios de etiqueta única) suportada?**  
Não, do Azure AD Connect não suporta local florestas/domínios SLDs a utilizar.

**P: são florestas com domínios de AD não contíguo suportados?**  
Não, do Azure AD Connect não suporta florestas no local que contém os espaços de nomes não contíguo.

**P: são "separada por pontos" com o nome de NetBios suportado?**  
Não, do Azure AD Connect não suporta florestas/domínios de no local onde o nome NetBios contenha um ponto final "." no nome.

**P: puro IPv6 ambiente é suportado?**  
Não, do Azure AD Connect não suporta puro ambiente IPv6.

## <a name="federation"></a>Federação
**P: o que posso fazer se receber uma mensagem de e-mail que perguntar-me para renovar certificado meu do Office 365**  
Utilize as orientações que está destacada no [renovar certificados](active-directory-aadconnect-o365-certs.md) tópico sobre como renovar o certificado.

**P: Posso ter "Atualizar automaticamente da entidade confiadora" definido para da entidade confiadora do Office 365. É necessário efetuar qualquer ação, quando faz o meu automaticamente o certificado de assinatura de token através de?**  
Utilize as orientações que é descrita no artigo [renovar certificados](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Ambiente
**P: é suportado para mudar o nome do servidor após a instalação do Azure AD Connect?**  
Não. Alterar o nome do servidor fará com que o motor de sincronização para não conseguir ligar à base de dados SQL e não será possível iniciar o serviço.

## <a name="identity-data"></a>Dados de identidade
**P: o atributo UPN (userPrincipalName) no Azure AD não corresponde ao UPN no local - por que motivo?**  
Consulte estes artigos:

* [Os nomes de utilizador no Office 365, Azure ou Intune não correspondem a UPN no local ou o ID de início de sessão alternativo](https://support.microsoft.com/en-us/kb/2523192)
* [As alterações não são sincronizadas pela ferramenta de sincronização de diretórios do Azure Active Directory depois de alterar o UPN de uma conta de utilizador para utilizar um diferente domínio federado](https://support.microsoft.com/en-us/kb/2669550)

Também pode configurar o Azure AD para permitir que o motor de sincronização atualizar o userPrincipalName conforme descrito em [funcionalidades do serviço de sincronização do Azure AD Connect](active-directory-aadconnectsyncservice-features.md).

**P: é, suportado para correspondência de forma recuperável AD grupo/contacte objetos com objetos existentes do grupo/contacte do Azure AD no local?**  
Não, isto não é atualmente suportado.

**P: é, suportado para definir manualmente ImmutableId atributo em objetos existentes do Azure AD grupo/contacte rígido corresponder ao que no local objetos de AD grupo/contacto?**  
Não, isto não é atualmente suportado.



## <a name="custom-configuration"></a>Configuração personalizada
**P: onde estão documentados os cmdlets do PowerShell do Azure AD Connect?**  
Com exceção dos cmdlets documentados neste site, os outros cmdlets do PowerShell encontrados no Azure AD Connect não são suportados para utilização do cliente.

**P: Posso utilizar "Servidor de exportação/importação do servidor" encontrada no *Synchronization Service Manager* para mover a configuração entre servidores?**  
Não. Esta opção não irá obter todas as definições de configuração e não deve ser utilizada. Em vez disso, deve utilizar o Assistente para criar a configuração base no segundo servidor e utilize o editor de regra de sincronização para gerar scripts do PowerShell para mover qualquer regra personalizada entre servidores. Consulte [Swing migração](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**P: as palavras-passe ser colocadas em cache para a página de início de sessão do Azure e pode isto impedida porque contém um elemento de entrada de palavra-passe com a conclusão automática = "false" atributo?**</br>
Atualmente não suportamos modificar os atributos HTML do campo entrada de palavra-passe, incluindo a tag de conclusão automática. Estamos atualmente uma funcionalidade que vai permitir Javascript personalizado que irá permitir-lhe adicionar qualquer atributo para o campo de palavra-passe. Deve ser posterior disponível parte de 2017.

**P: na página início de sessão do Azure, são apresentados os nomes de utilizador para os utilizadores que tenham anteriormente sessão iniciada com êxito.  Este comportamento pode ser ativado?**</br>
Atualmente não suportamos modificar os atributos HTML da página de início de sessão. Estamos atualmente uma funcionalidade que vai permitir Javascript personalizado que irá permitir-lhe adicionar qualquer atributo para o campo de palavra-passe. Deve ser posterior disponível parte de 2017.

**P: existe uma forma de impedir sessões simultâneas?**</br>
Não.



## <a name="troubleshooting"></a>Resolução de problemas
**P: como posso obter ajuda com o Azure AD Connect?**

[Procurar na Base de dados de Conhecimento Microsoft (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Procure a Base de dados de conhecimento (BDC da Microsoft) para técnicas soluções para problemas de break-fix comuns sobre o suporte do Azure AD Connect.

[Fóruns do Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Pode pesquisar e navegue para a technical questões e respostas da Comunidade ou peça ao seu próprio pergunta clicando [aqui](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Como obter suporte para o Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

* Utilize esta hiperligação para obter suporte através do portal do Azure.

