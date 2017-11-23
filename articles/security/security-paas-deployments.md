---
title: "Proteger implementações PaaS | Microsoft Docs"
description: " Compreenda as vantagens de segurança de PaaS versus outros modelos de serviço em nuvem e mais práticas recomendadas para proteger a sua implementação PaaS do Azure. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: techlake
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 4629e0ab6bbc9554128a923e92b269df79446b18
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="securing-paas-deployments"></a>Proteger implementações PaaS

Este artigo fornece informações que o ajuda a:

- Compreender as vantagens de segurança de alojar aplicações na nuvem
- Avaliar as vantagens de segurança de plataforma como serviço (PaaS) versus outros modelos de serviço em nuvem
- Altere o foco de segurança de uma rede de centrada para uma abordagem de segurança de perímetro centrada em identidade
- Implementar gerais PaaS segurança recomendações das melhores práticas

## <a name="cloud-security-advantages"></a>Vantagens de segurança de nuvem
Existem vantagens de segurança para a ser na nuvem. Num ambiente no local, as organizações prováveis têm unmet responsabilidades e limitados recursos disponíveis para investir em segurança, o que cria um ambiente em que os atacantes conseguem explora vulnerabilidades em todas as camadas.

![Vantagens de segurança de era de nuvem][1]

As organizações conseguem melhorar os seus a deteção de ameaças e tempos de resposta utilizando as capacidades de segurança baseado na nuvem de um fornecedor e intelligence de nuvem.  Por mudanças responsabilidades para o fornecedor de nuvem, as organizações podem obter mais cobertura de segurança, que permite-lhes reatribuir os recursos de segurança e a atribuição de prioridades de outras empresas.

## <a name="division-of-responsibility"></a>Divisão de responsabilidade
É importante compreender a divisão de responsabilidade entre si e a Microsoft. No local, que é proprietário da pilha de toda, mas como mover para a nuvem algumas responsabilidades transferir para o Microsoft. A matriz de responsabilidade seguinte mostra as áreas da pilha de uma implementação de SaaS, IaaS e PaaS, que é responsável por e Microsoft é responsável.

![Zonas de responsabilidade][2]

Para todos os tipos de implementação de nuvem, que possui os dados e identidades. É responsável por proteger a segurança dos seus dados e identidades, recursos no local e os componentes de nuvem controlo (que varia consoante o tipo de serviço).

Responsabilidades são sempre mantidas por si, independentemente do tipo de implementação, são:

- Dados
- Pontos Finais
- Conta
- Gestão de acesso

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Vantagens de segurança de uma PaaS modelo de serviço de nuvem
Vamos utilizar a mesma matriz de responsabilidade, observe as vantagens de segurança de uma implementação Azure PaaS versus no local.

![Vantagens de segurança de PaaS][3]

Iniciar na parte inferior da pilha, a infraestrutura física, a Microsoft Mitigam os riscos comuns e responsabilidades Uma vez que a nuvem da Microsoft é monitorizada continuamente pela Microsoft, é difícil de ataques. -Não sentido para um atacante pursue a nuvem da Microsoft como um destino. A menos que o atacante tenha muitos dinheiro e recursos, o atacante é provável que avançar para o destino de outro.  

No meio da pilha, não há qualquer diferença entre uma implementação PaaS e no local. Na camada da aplicação e a camada de gestão de acesso e de conta, terá de riscos semelhantes. Na secção seguinte passos deste artigo, iremos irá ajudá-lo para as melhores práticas para eliminando ou minimizar estes riscos.

No topo da pilha, governação de dados e gestão de direitos, que tomar um risco que pode ser mitigado ao gestão de chaves. (Gestão de chaves é abordada melhores práticas.) Enquanto a gestão de chaves é uma responsabilidade adicional, tem áreas numa implementação PaaS que já não tem de gerir, pelo que pode deslocar recursos para gestão de chaves.

A plataforma do Azure também fornece proteção forte por DDoS utilizando várias tecnologias baseadas na rede. No entanto, todos os tipos de métodos de proteção DDoS baseados na rede tem os respetivos limites numa base por ligação e por centro de dados. Para ajudar a evitar o impacto de ataques de DDoS grandes, pode tirar partido da capacidade de nuvem de núcleo do Azure de ativação rapidamente e automaticamente horizontal para se Defender contra ataques de DDoS. Iremos irá entrar em mais detalhe na forma como pode fazer isto nos artigos práticas recomendadas.

## <a name="modernizing-the-defenders-mindset"></a>Modernizing mente a defender
PaaS implementações dá origem a um bit na sua abordagem geral de segurança. Deslocar a necessidade de controlar tudo por si para a partilha de responsabilidade com a Microsoft.

Outra diferença significativa entre PaaS e as implementações tradicionais no local, é uma nova vista de que define o perímetro principal de segurança. Historicamente, o perímetro de segurança no local primário era sua rede e a maioria das estruturas de segurança no local, utilizar a rede como respetivo pivot principal de segurança. Para implementações PaaS, melhor são servidos pelo considerar identidade ser perímetro principal de segurança.

## <a name="identity-as-the-primary-security-perimeter"></a>Identidade como perímetro principal de segurança
Um dos cinco características essenciais da nuvem informática é amplo acesso à rede, que torna a pensar centrada em rede menos relevantes. O objetivo de grande parte é permitir que os utilizadores acedam aos recursos, independentemente da localização de informática na cloud. Para a maioria dos utilizadores, a respetiva localização vai ser algures na Internet.

A figura seguinte mostra como o perímetro de segurança tem evoluiu e deu lugar do perímetro rede para um perímetro de identidade. Segurança torna-se mais e menos sobre defende a sua rede sobre defende os seus dados, bem como gerir a segurança das suas aplicações e utilizadores. A principal diferença é que pretender emitir segurança próximo para o que é importante para a sua empresa.

![Identidade como novo perímetro de segurança][4]

Inicialmente, os serviços do Azure PaaS (por exemplo, funções da web e SQL do Azure) fornecido pouca ou nenhuma rede tradicional defesas de perímetro. -Foi entendido que objetivo do elemento de ser expostos à Internet (função da web) e que a autenticação fornece o perímetro novo (por exemplo, BLOB ou SQL do Azure).

Práticas de segurança moderna partem do princípio de que o adversário tenha sido infringido de perímetro da rede. Por conseguinte, práticas de defesa moderna movido para a identidade. As organizações tem de estabelecer um perímetro baseado na identidade de segurança com forte hygiene de autenticação e autorização (melhores práticas).

## <a name="recommendations-for-managing-the-identity-perimeter"></a>Recomendações para a gestão de perímetro de identidade

Princípios e padrões para o perímetro de rede foram disponíveis para decades. Em contrapartida, o setor tem relativamente menos experiência com a utilização de identidade como perímetro principal de segurança. Com que consiga aceder tal, podemos ter acumulados suficiente experiência para fornecer algumas recomendações gerais que são comprovadas no campo e aplicam quase todos os serviços de PaaS.

O seguinte resume uma geral abordagem das melhores práticas para gerir o perímetro de identidade.

- **Não perca o seu chaves ou credenciais** proteger chaves e as credenciais é essencial para proteger implementações PaaS. Perda de chaves e as credenciais é um problema comum. É uma boa solução utilizar uma solução centralizada onde podem ser armazenados chaves e segredos nos módulos de segurança de hardware (HSM). O Azure oferece um HSM na nuvem com o [Cofre de chaves do Azure](../key-vault/key-vault-whatis.md).
- **Não colocar as credenciais e outros segredos no código fonte ou GitHub** a única coisa que worse que perder as chaves e as credenciais está a ter uma entidade não autorizada obter acesso aos mesmos. Os atacantes são capazes de tirar partido das bot tecnologias de encontrar chaves e segredos armazenados em repositórios de código como o GitHub. Não coloque chaves e segredos nestes repositórios de código fonte públicos.
- **Proteger as interfaces de gestão de VM nos serviços de PaaS e IaaS híbrida** serviços IaaS e PaaS são executados em máquinas virtuais (VMs). Dependendo do tipo de serviço, estão disponíveis várias interfaces de gestão que ativar remotamente gerir estas VMs diretamente. Gestão remota protocolos, tal como [protocolo Secure de Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell), [protocolo RDP (Remote Desktop Protocol)](https://support.microsoft.com/kb/186607), e [PowerShell remoto](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) pode ser utilizado. Em geral, recomendamos que não ativar acesso remoto direto às VMs através da Internet. Se estiver disponível, deve utilizar abordagens alternativas, tais como utilizar redes privadas virtuais numa rede virtual do Azure. Se abordagens alternativas não estão disponíveis, em seguida, certifique-se de que utiliza as frases de acesso complexos e, se estiver disponível, autenticação de dois fatores (tais como [Azure multi-factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)).
- **Utilizar plataformas seguras de autenticação e autorização**

  - Utilize identidades federadas no Azure AD em vez de arquivos de utilizador personalizadas. Quando utilizar identidades federadas, tirar partido de uma abordagem baseada na plataforma e delegar a gestão de identidades autorizadas para os parceiros. Uma abordagem de identidade federada é especialmente importante em cenários de quando os funcionários estão terminados e que as informações que precisam para serem refletidas através de vários sistemas de identidade e autorização.
  - Utilize mecanismos de autenticação e autorização da plataforma fornecido em vez de código personalizado. O motivo é que a desenvolver o código de autenticação personalizados pode ser propensas ao erro. Na maioria das suas programadores não é especialistas de segurança e pouco a ter em consideração os subtleties e os desenvolvimentos mais recentes na autenticação e autorização. Código comercial (por exemplo, da Microsoft) é, muitas vezes, extensivamente revista de segurança.
  - Utilize a autenticação multifator. Autenticação multifator é padrão atual para autenticação e autorização, porque evita as fragilidades de segurança inerentes em tipos de nome de utilizador e palavra-passe de autenticação. Acesso para ambas as interfaces de gestão do Azure (portal/remota do PowerShell) e para o cliente serviços deve ser concebido e configurado para utilizar [Azure multi-factor Authentication (MFA)](../multi-factor-authentication/multi-factor-authentication.md).
  - Utilize protocolos de autenticação padrão, tais como o OAuth2 e Kerberos. Estes protocolos foram extensivamente ponto a ponto revista e provavelmente são implementados como parte das suas bibliotecas de plataforma para autenticação e autorização.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, vamos concentra-se em vantagens de segurança de uma implementação PaaS do Azure. Em seguida, saiba práticas recomendadas para proteger o seu PaaS web e móveis soluções. Iremos começar com o App Service do Azure, SQL Database do Azure e Azure SQL Data Warehouse. Como artigos sobre práticas recomendadas para outros serviços do Azure ficam disponíveis, serão fornecidas ligações na lista seguinte:

- [Serviço de Aplicações do Azure](security-paas-applications-using-app-services.md)
- [Base de dados SQL do Azure e do armazém de dados SQL do Azure](security-paas-applications-using-sql.md)
- Storage do Azure
- Cache REDIS do Azure
- Service Bus do Azure
- Firewalls de aplicação Web

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png
