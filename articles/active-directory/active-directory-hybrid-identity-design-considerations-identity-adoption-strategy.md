---
title: "Azure Active Directory híbrida identidade considerações de design - definir uma estratégia de adoção de identidade híbrida | Microsoft Docs"
description: "Com o controlo de acesso condicional, o Azure Active Directory verifica as condições específicas, que escolha ao autenticar o utilizador e antes de permitir o acesso à aplicação. Depois destas condições são cumpridas, o utilizador é autenticado e permissão de acesso à aplicação."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 2c57b394beb6382807a4c8c83de975a0ae68d726
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definir uma estratégia de adoção de identidade híbrida
Nesta tarefa, vai definir a estratégia de adoção de identidade híbrida para a sua solução de identidade híbrida cumprir os requisitos de negócio que foram abordados:

* [Determinar as necessidades de negócio](active-directory-hybrid-identity-design-considerations-business-needs.md)
* [Determinar os requisitos de sincronização de diretórios](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Determinar os requisitos de autenticação multifator](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definir a estratégia de necessidades de negócio
Tem dos endereços de tarefa primeiro determinar as empresas e organizações.  Isto pode ser muito abrangente e creep âmbito pode ocorrer se não tiver cuidado.  No início manter tudo simples mas lembre-se sempre ao planear uma estrutura que irá suportar e facilitar a alteração no futuro.  Independentemente de ser uma estrutura simple ou a um extremamente complexo, o Azure Active Directory é a plataforma do Microsoft Identity que suporte aplicações com suporte para Office 365, Microsoft Online Services e em nuvem.

## <a name="define-an-integration-strategy"></a>Definir uma estratégia de integração
A Microsoft tem três cenários de integração principais que são as identidades de nuvem, identidades sincronizadas e identidades federadas.  Deve planear a adoção de uma destes estratégias de integração.  A estratégia de que escolha podem variar e podem incluir as decisões de escolher um, o tipo de experiência de utilizador que pretenda fornecer, tem algumas da infraestrutura existente já no local e o que é o mais económico.  

![](./media/hybrid-id-design-considerations/integration-scenarios.png)

Os cenários definidos na figura acima são:

* **Identidades de nuvem**: estes são identidades que existe apenas na nuvem.  No caso do Azure AD, podem residir especificamente no diretório do Azure AD.
* **Sincronizadas**: estes são identidades que existem no local e na nuvem.  Utilizando o Azure AD Connect, esses utilizadores são ser criados ou associados a contas do Azure AD existentes.  Hash de palavra-passe do utilizador é sincronizado entre o ambiente no local à nuvem em que é chamado um hash de palavra-passe.  Quando utilizar sincronizado a uma advertência é que, se um utilizador é desativado no ambiente no local, pode demorar até 3 horas para esse Estado da conta a aparecer no Azure AD.  Isto acontece porque o intervalo de tempo de sincronização.
* **Federado**: existem destas identidades no local e na nuvem.  Utilizando o Azure AD Connect, esses utilizadores são ser criados ou associados a contas do Azure AD existentes.  

> [!NOTE]
> Para obter mais informações sobre as opções de sincronização ler [integrar as identidades no local ao Azure Active Directory](connect/active-directory-aadconnect.md).
> 
> 

A tabela seguinte irá ajudar a determinar as vantagens e desvantagens de cada uma das seguintes estratégias:

| Estratégia de | Vantagens | Desvantagens |
| --- | --- | --- |
| **Identidades de nuvem** |Mais fácil de gerir para a organização pequena. <br> Nada para instalar hardware adicional no-local-não necessária<br>Facilmente desativada se o utilizador sai da empresa |Os utilizadores terão de início de sessão ao aceder a cargas de trabalho na nuvem <br> As palavras-passe podem ou não podem ser o mesmo para as identidades de nuvem e no local |
| **Sincronizados** |Palavra-passe de no local irá autenticar no local e na nuvem diretórios <br>Mais fácil de gerir para pequenas, médias ou grandes organizações <br>Os utilizadores podem ter início de sessão único (SSO) para alguns dos recursos <br> Método de Microsoft preferido para sincronização <br> Mais fácil de gerir |Alguns clientes podem estar relutantes sincronizar os seus diretórios com a nuvem devido a política da empresa específica |
| **Federado** |Os utilizadores podem ter início de sessão único (SSO) <br>Se um utilizador é terminado ou deixa, a conta pode ser desativada imediatamente e acesso revogado,<br> Suporta avançadas cenários que não pode ser conseguido com sincronizados |Mais passos para instalar e configurar o <br> Manutenção superior <br> Pode necessitar de hardware adicional para a infraestrutura de STS <br> Pode necessitar de hardware adicional para instalar o servidor de Federação. Software adicional é necessário se for utilizado o AD FS <br> Requer configuração extensa para SSO <br> Ponto de crítico de falha se o servidor de Federação está inativo, os utilizadores não conseguirá autenticar |

### <a name="client-experience"></a>Experiência do cliente
A estratégia de que utiliza determinarão a experiência de início de sessão do utilizador.  As tabelas seguintes lhe fornecem informações sobre os utilizadores que devem esperar sua experiência de início de sessão ser.  Tenha em atenção que nem todos os fornecedores de identidade federada suportam o SSO em todos os cenários.

**Aplicações de rede associados a um domínio e privadas**:

|  | Identidade sincronizada | Identidade federada |
| --- | --- | --- |
| Browsers da Web |Autenticação baseada em formulários |início de sessão único no, por vezes, necessário para fornecer o ID de organização |
| Outlook |Pedido de credenciais |Pedido de credenciais |
| Skype para empresas (Lync) |Pedido de credenciais |início de sessão único para o Lync, solicitado que as credenciais para o Exchange |
| SkyDrive Pro |Pedido de credenciais |início de sessão único |
| Office Pro Plus subscrição |Pedido de credenciais |início de sessão único |

**Origens externas ou não fidedignas**:

|  | Identidade sincronizada | Identidade federada |
| --- | --- | --- |
| Browsers da Web |Autenticação baseada em formulários |Autenticação baseada em formulários |
| Outlook, o Skype para empresas (Lync), Skydrive Pro, subscrição do Office |Pedido de credenciais |Pedido de credenciais |
| Exchange ActiveSync |Pedido de credenciais |início de sessão único para o Lync, solicitado que as credenciais para o Exchange |
| Aplicações Móveis |Pedido de credenciais |Pedido de credenciais |

Se tiver determinado na tarefa 1, que tem um 3rd terceiros IdP ou são curso para utilizar uma para fornecer a Federação com o Azure AD, terá de ter em consideração as seguintes capacidades suportadas:

* Nenhum fornecedor de SAML 2.0 que está em conformidade para o perfil SP Lite pode suportar autenticação para o Azure AD e associados a aplicações
* Suporta autenticação passiva, o que facilita a autenticação OWA, SPO, etc.
* Clientes do Exchange Online podem ser suportados através de SAML 2.0 avançada cliente Perfil (ECP)

Deve também estar ciente de que capacidades não estarão disponíveis:

* Sem o suporte de WS-confiança/Federação, irão interromper a todos os outros clientes ativos
  * Isto significa que nenhum cliente Lync, o cliente do OneDrive, subscrição do Office, Office Mobile antes do Office 2016
* Transição do Office para autenticação passiva irão permitir-lhe suportar puro IdPs de 2.0 SAML, mas suporte continuarão a estar numa base do cliente pelo cliente

> [!NOTE]
> Para obter a lista mais atualizada ler o artigo http://aka.ms/ssoproviders.
> 
> 

## <a name="define-synchronization-strategy"></a>Definir a estratégia de sincronização
Nesta tarefa, vai definir as ferramentas que serão utilizadas para sincronizar a organização dados no local para a nuvem e o que deve utilizar de topologia.  Uma vez, a maioria das organizações utilizam o Active Directory, são fornecidas informações sobre como utilizar o Azure AD Connect para abordar as perguntas acima algum detalhe.  Para ambientes que não tenham do Active Directory, não há informações sobre como utilizar o FIM 2010 R2 ou o MIM 2016 para ajudar a planear esta estratégia.  No entanto, as versões futuras do Azure AD Connect irão suportar diretórios LDAP, por isso, consoante a linha cronológica, esta informação poderá ajudá-lo.

### <a name="synchronization-tools"></a>Ferramentas de sincronização
Ao longo dos anos, várias ferramentas de sincronização tem existia e utilizado para vários cenários.  O Azure AD Connect está atualmente a ir para a ferramenta de eleição para todos os cenários suportados.  AAD Sync e DirSync ainda estão em torno e poderão ainda estar presentes no seu ambiente agora. 

> [!NOTE]
> Para informações mais recentes sobre as capacidades suportadas de cada ferramenta, leia o artigo [comparação das ferramentas de integração de diretórios](active-directory-hybrid-identity-design-considerations-tools-comparison.md) artigo.  
> 
> 

### <a name="supported-topologies"></a>Topologias suportadas
Ao definir uma estratégia de sincronização, a topologia utilizada têm de ser determinada. Consoante as informações que foi determinou no passo 2 pode determinar que topologia é o correto para utilizar. A única floresta, única topologia do Azure AD é mais comuns e é composta por uma única floresta do Active Directory e uma única instância do Azure AD.  Isto vai ser utilizada numa maioria dos cenários e é a topologia de esperado ao utilizar a instalação do Azure AD Connect Express conforme mostrado na imagem abaixo.

![](./media/hybrid-id-design-considerations/single-forest.png)Cenário de floresta única é muito comum para mesmo pequenas e grandes organizações com várias florestas, conforme mostrado na figura 5.

> [!NOTE]
> Para obter mais informações sobre as diferentes no local e topologias do Azure AD com o Azure AD Connect sincronização leia o artigo [topologias do Azure AD Connect](connect/active-directory-aadconnect-topologies.md).
> 
> 

![](./media/hybrid-id-design-considerations/multi-forest.png) 

Cenário de várias floresta

Se este o caso, em seguida, a topologia de vários-forest-único do Azure AD deve ser considerada se os itens seguintes são verdadeiras:

* Os utilizadores têm apenas 1 identidade em todas as florestas – a seção de utilizadores exclusivamente identificação abaixo descreve esta mais detalhadamente.
* O utilizador efetua a autenticação para a floresta em que a sua identidade se encontra
* UPN e a âncora de origem (id imutável) serão provenientes nesta floresta
* Todas as florestas estão acessíveis pelo Azure AD Connect – Isto significa que não têm de ser domínio associado e pode ser colocado numa rede de Perímetro se Isto facilita isto.
* Os utilizadores tenham apenas uma caixa de correio
* A floresta que aloja as caixas de correio de um utilizador tem a melhor qualidade dos dados de atributos visíveis na lista de endereços Global Exchange (GAL)
* Se não existir nenhuma caixa de correio no utilizador, pode ser utilizada qualquer floresta, em seguida, estes valores de contribuir
* Se tiver uma caixa de correio ligada, em seguida, há também outra conta numa floresta diferente costumava iniciar sessão.

> [!NOTE]
> Objetos que existem no local e na nuvem "ligados" através de um identificador exclusivo. No contexto de sincronização de diretórios, este identificador exclusivo é referido como o SourceAnchor. No contexto de Single Sign-On, isto é referido como o ImmutableId. [Conceitos de design para o Azure AD Connect](connect/active-directory-aadconnect-design-concepts.md#sourceanchor) para mais considerações sobre a utilização de SourceAnchor.
> 
> 

Se o procedimento acima não forem VERDADEIRO e tiver mais do que uma conta Active Directory ou mais do que uma caixa de correio, o Azure AD Connect escolha um e ignorar o outro.  Se tiver ligado caixas de correio, mas nenhuma outra conta, estas contas não serão exportadas para o Azure AD e esse utilizador não será um membro de quaisquer grupos.  Isto é diferente da forma como foi no passado com o DirSync e é intencional para melhor suporte estes cenários de várias florestas. Um cenário de várias floresta é mostrado na imagem abaixo.

![](./media/hybrid-id-design-considerations/multiforest-multipleAzureAD.png) 

**Várias florestas vários cenário do Azure AD**

Recomenda-se ter apenas um único diretório no Azure AD para uma organização, mas é suportada, uma relação de 1:1 é mantida entre um servidor de sincronização do Azure AD Connect e um diretório do Azure AD.  Para cada instância do Azure AD, terá de uma instalação do Azure AD Connect.  Além disso, do Azure AD, por predefinição está isolado e os utilizadores numa instância do Azure AD não será capazes de ver os utilizadores na outra instância.

É possível e suportados para ligar uma instância no local do Active Directory para vários diretórios do Azure AD, conforme mostrado na imagem abaixo:

![](./media/hybrid-id-design-considerations/single-forest-flitering.png) 

**Cenário de filtragem de floresta única**

Para fazê-lo a seguintes deve ser verdadeiras:

* Servidores de sincronização do Azure AD Connect tem de ser configurados para efeitos de filtragem para cada têm um conjunto de objetos mutuamente exclusivo.  Isto feito, por exemplo, ao controlo de âmbito de cada servidor para um determinado domínio ou UO.
* Só pode ser registado um domínio DNS num único diretório do Azure AD, por isso, o UPNs AD tem de utilizar espaços de nomes separados dos utilizadores no no local
* Uma instância do Azure AD os utilizadores só conseguirão ver utilizadores da sua instância.  Este não poderá ver utilizadores em de outras instâncias
* Apenas um dos diretórios do Azure AD pode ativar híbrida do Exchange com o local AD
* Exclusivity mútua também se aplica a repetição de escrita.  Isto faz com que algumas funcionalidades de repetição de escrita não são suportadas com esta topologia, uma vez que estes partem do princípio de uma única configuração no local.  Isto inclui:
  * Grupo de write-back com a configuração predefinida
  * Write-back do dispositivo

Lembre-se de que o seguinte não é suportado e não deve ser selecionado como uma implementação:

* Não é suportado ter múltiplos servidores de sincronização do Azure AD Connect ligar para o mesmo diretório do Azure AD, mesmo se estiverem configurados para sincronizar conjunto mutuamente exclusivo de objeto
* -Não é suportado para sincronizar o mesmo utilizador para vários diretórios do Azure AD. 
* É também não suportada para efetuar uma configuração alterar para que os utilizadores num Azure AD a aparecer como contactos noutro diretório do Azure AD. 
* Também é suportada para modificar a sincronização do Azure AD Connect para ligar a vários diretórios do Azure AD.
* Diretórios do Azure AD são propositado isolada. -Não é suportado para alterar a configuração da sincronização do Azure AD Connect para ler dados a partir de outro diretório do Azure AD numa tentativa de construir uma GAL unificada e comuns entre os diretórios. Também não suportado para exportar utilizadores como contactos para outro AD no local utilizando a sincronização do Azure AD Connect.

> [!NOTE]
> Se a sua organização impede que os computadores na sua rede a ligar à Internet, este artigo apresenta uma lista de pontos finais (FQDN, IPv4 e IPv6 intervalos de endereços) que deve incluir na sua saída permite listas e zona de Sites do Internet Explorer fidedignos do cliente de computadores garantir que os computadores com êxito podem utilizar o Office 365. Para obter mais informações, leia [intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Definir a estratégia de autenticação multifator
Nesta tarefa, vai definir a estratégia de autenticação multifator para utilizar.  Multi-factor Authentication do Azure tem dois versão diferente.  Um é uma conta baseada na nuvem e o outro é baseado utilizando o servidor MFA do Azure no local.  Com base na comparação acima, pode determinar que solução é o correto para a sua estratégia.  Utilize a tabela abaixo para determinar que opção de estrutura melhor satisfazer o requisito de segurança da sua empresa:

Opções de estrutura de multifator:

| Recursos para proteger | MFA na nuvem | MFA no local |
| --- | --- | --- |
| Aplicações da Microsoft |sim |sim |
| Aplicações SaaS na galeria de aplicações |sim |sim |
| Aplicações IIS publicadas através do Proxy de Aplicação do Azure AD |sim |sim |
| Aplicações de IIS não publicadas através do Proxy de aplicações do Azure AD |não |sim |
| Acesso remoto como VPN, RDG |não |sim |

Embora poderá ter settled numa solução para a sua estratégia, ainda tem de utilizar a avaliação da acima local onde os utilizadores estiverem localizados.  Isto pode fazer com que a solução alterar.  Utilize a tabela abaixo para ajudar a determinar isto:

| Localização do utilizador | Opção de conceção preferida |
| --- | --- |
| Azure Active Directory |Várias-FactorAuthentication na nuvem |
| Azure AD e AD no local utilizando federação com o AD FS |Ambos |
| Azure AD e AD através do Azure AD Connect sem sincronização de palavra-passe no local |Ambos |
| Azure AD e no local utilizando o Azure AD Connect com sincronização de palavra-passe |Ambos |
| AD no local |Servidor Multi-Factor Authentication |

> [!NOTE]
> Deve também garantir que a opção de estrutura de autenticação multifator que selecionou suporta as funcionalidades que são necessárias para o design.  Para obter mais informações, leia [escolher a solução de segurança multifator para si](../multi-factor-authentication/multi-factor-authentication-get-started.md#what-am-i-trying-to-secure).
> 
> 

## <a name="multi-factor-auth-provider"></a>Fornecedor do multi-factor Auth
Autenticação multifator está disponível por predefinição para os administradores globais que possuam um inquilino do Azure Active Directory. No entanto, se pretender expandir a autenticação multifator para todos os utilizadores e/ou pretende para os administradores globais ser capazes de tirar partido funcionalidades como o portal de gestão, saudações personalizadas e relatórios, em seguida, deve adquirir e configurar o fornecedor de autenticação Multifator.

> [!NOTE]
> Deve também garantir que a opção de estrutura de autenticação multifator que selecionou suporta as funcionalidades que são necessárias para o design. 
> 
> 

## <a name="next-steps"></a>Passos seguintes
[Determinar os requisitos de proteção de dados](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Consultar também
[Descrição geral das considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)

