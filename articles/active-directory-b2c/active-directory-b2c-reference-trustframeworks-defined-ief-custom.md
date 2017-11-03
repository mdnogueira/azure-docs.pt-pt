---
title: "Referência do Azure Active Directory B2C: - Confiar estruturas | Microsoft Docs"
description: "Um tópico sobre as políticas personalizadas do Azure Active Directory B2C e o Framework de experiência de identidade"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: joroja
ms.openlocfilehash: 4e2de9c4d1c0f92970911e132fffaacbd01d9ad0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definir as estruturas de confiança com o Framework de experiência de identidade do Azure AD B2C

O Azure Active Directory B2C políticas personalizadas (Azure AD B2C) que utilizam a arquitetura de experiência de identidade fornecem a sua organização com um serviço centralizado. Este serviço reduz a complexidade da Federação de identidade numa grande Comunidade de interesse. A complexidade é reduzida para uma relação de confiança único e uma troca de metadados único.

Azure AD B2C políticas personalizadas que utilizam o Framework de experiência de identidade para que possa responder às seguintes questões:

- Quais são as informações legais, segurança, privacidade e políticas de proteção de dados tem de ser aderiu a?
- Quem são os contactos e quais são os processos para se tornar um participante autorizado?
- Quem são os fornecedores de informações de identidade autorizada (também conhecido como "fornecedores de afirmações") e o que fazer oferecem?
- Quem são as entidades confiadoras autorizadas (e, opcionalmente, o que é necessário)?
- Quais são o technical "em risco" requisitos de interoperabilidade para participantes?
- Quais são as regras de operacional "tempo de execução" que tem de ser impostas para trocar informações de identidade digital?

Para responder a todos os estas perguntas, construir as políticas personalizadas do Azure AD B2C que utilizam a utilização de estrutura de experiência de identidade de confiança Framework (TF). Vejamos este construção e que fornece.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Compreender a base de gestão de fidedignidade Framework e a Federação

A arquitetura de confiança é uma especificação escrita da identidade, segurança, privacidade e para os quais participantes numa Comunidade de interesse devem estar em conformidade com políticas de proteção de dados.

Identidade federada fornece uma base para alcançar a garantia de identidade do utilizador final na escala da Internet. Por delegar a gestão de identidades a terceiros, uma identidade digital única para um utilizador final pode ser reutilizada com várias entidades confiadoras.  

Garantia de identidade requer que os fornecedores de identidade (IdPs) e os fornecedores de atributo (AtPs) aderem segurança específicos, privacidade e políticas operacionais e práticas.  Se não podem efetuar inspections diretas, confiadoras (RPs) tem de desenvolver as relações de confiança com o IdPs e AtPs que escolherem para trabalhar com.  

À medida que cresce o número de fornecedores de informações de identidade digital e os consumidores, é difícil continuar gestão pairwise estas relações de fidedignidade, ou mesmo a troca pairwise dos metadados técnica necessária para conetividade de rede.  Hubs de federação tem atingido apenas limitado êxito a resolução desses problemas.

### <a name="what-a-trust-framework-specification-defines"></a>Define que uma especificação de confiança Framework
TFs são linchpins do modelo de abrir identidade Exchange (OIX) de confiança Framework onde cada Comunidade de interesse é regida por uma especificação de TF específica. Define uma especificação de TF:

- **As métricas de segurança e privacidade para a Comunidade de interesse com a definição de:**
    - Os níveis de garantia (LOA) que são oferecidos/exigido pela participantes; Por exemplo, um conjunto ordenado de classificações de confiança da autenticidade das informações de identidade digital.
    - Os níveis de proteção (LOP) que são oferecidos/exigido pela participantes; Por exemplo, um conjunto ordenado de classificações de confiança para a proteção de informações de identidade digital que são processadas pelo participantes na Comunidade de interesse.

- **A descrição das informações de identidade digital que tem oferecidas/necessário por participantes**.

- **As políticas técnicas para produção e o consumo de informações de identidade digital e, consequentemente, para medir LOA e LOP. Estas políticas escritas incluem, geralmente, as seguintes categorias de políticas:**
    - Identidade de verificação do políticas, por exemplo: *são vivamente como informações de identidade de uma pessoa vetted?*
    - As políticas de segurança, por exemplo: *como vivamente são integridade informações e confidencialidade protegida?*
    - Políticas de privacidade, por exemplo: *que controlo a um utilizador tem sobre informações de identificação pessoais (PII)*?
    - Políticas de Survivability, por exemplo: *se um fornecedor ceases operações, como funciona continuidade e a proteção da função PII?*

- **Os perfis técnicos para produção e o consumo de informações de identidade digital. Estes perfis incluem:**
    - Interfaces de âmbito para o qual as informações de identidade digital estão disponíveis num LOA especificado.
    - Requisitos técnicos para interoperabilidade de na transmissão.

- **As descrições de várias funções que podem realizar participantes na Comunidade e as qualificações que são necessários para realizar estas funções.**

Assim uma especificação de TF é regida pelas como informações de identidade são trocadas entre os participantes da Comunidade de interesse: das entidades confiadoras, identidade e fornecedores de atributo e verificadores de atributo.

Uma especificação de TF é um ou vários documentos que funcionam como uma referência para a governação de Comunidade de interesse regulates a asserção e consumo de informações de identidade digitais dentro da Comunidade. É um conjunto de políticas e procedimentos concebidos para estabelecer a confiança nas identidades digitais que são utilizados para transações online entre membros de uma Comunidade de interesse documentado.  

Por outras palavras, uma especificação de TF define as regras para criar um ecossistema de identidade federada viável para uma Comunidade.

Atualmente não há contrato ampla na vantagem essas uma abordagem. Não há doubt que confiança especificações de estrutura facilitam o desenvolvimento de ecossistemas de identidade digital com verificável características de segurança, segurança e privacidade, que significa que podem ser reutilizadas em várias comunidades de interesse.

Para que o motivo, as políticas personalizadas do Azure AD B2C que utilizam a arquitetura de experiência de identidade utiliza a especificação como base da respetiva representação de dados para um TF para facilitar a interoperabilidade.  

Azure AD B2C as políticas personalizadas que tiram partido da estrutura de experiência de identidade representam uma especificação de TF como uma combinação de dados humanos e machine-readable. Alguns secções deste modelo (normalmente, as secções são mais dedicadas a governação) são representadas como referência para documentação publicada de política de segurança e privacidade juntamente com os procedimentos relacionados (se aplicável). Noutras secções descrevem pormenorizadamente as regras de metadados e o tempo de execução de configuração que facilitam a automatização operacional.

## <a name="understand-trust-framework-policies"></a>Compreender as políticas de fidedignidade Framework

Em termos de implementação, a especificação de TF é composta por um conjunto de políticas que lhe permitem o controlo total sobre experiências e comportamentos de identidade.  Azure AD B2C as políticas personalizadas que tiram partido da estrutura de experiência de identidade permitem-lhe criar e criar a suas próprias TF através deste tipo declarativas políticas que pode definir e configurar:

- A referência do documento ou referências que definem a ecossistema de identidade federada da Comunidade que está relacionada com a TF. São ligações a documentação do TF. As regras de (predefinida) operacional "tempo de execução" ou os percursos de utilizador que automatizarem e/ou a controlam a utilização de afirmações e o exchange. Estes percursos de utilizador associados a um LOA (e um LOP). Uma política, por conseguinte, pode ter percursos de utilizador com diversos LOAs (e LOPs).

- Os fornecedores de identidade e de atributo ou os fornecedores de afirmações na Comunidade de interesse e os perfis de técnicos suportam juntamente com o accreditation LOA/LOP (fora de banda) que está relacionada com aos mesmos.

- A integração com verificadores de atributo ou fornecedores de afirmações.

- As entidades confiadoras na Comunidade (por inferência).

- Os metadados para estabelecer comunicações de rede entre os participantes. Estes metadados, juntamente com os perfis técnicos, são utilizados durante uma transacção para ligar "em risco" interoperabilidade entre a entidade confiadora e outros participantes da Comunidade.

- A conversão de protocolo se qualquer (por exemplo, SAML, OAuth2, WS-Federation e o OpenID Connect).

- Os requisitos de autenticação.

- A orquestração multifator se aplicável.

- Um esquema partilhado para todas as afirmações que estão disponíveis e mapeamentos aos participantes de uma Comunidade de interesse.

- Todas as transformações de afirmações, juntamente com o minimization de dados neste contexto, para suportar a utilização de afirmações e o exchange.

- O enlace e a encriptação.

- O armazenamento de afirmações.

### <a name="understand-claims"></a>Compreender as afirmações

> [!NOTE]
> Iremos coletivamente referir-se a todos os tipos possíveis de informações de identidade que podem ser trocadas como "afirmações": afirmações sobre a credencial de autenticação de um utilizador final vetting de identidade, dispositivo de comunicação, a localização física, atributos, de identificação pessoal e assim sucessivamente.  
>
> Utilizamos as termo "afirmações" – em vez de "atributos" – porque transações online, destes artefactos de dados não estão factos que podem ser verificados diretamente pela entidade confiadora. Em vez disso está asserções ou afirmações, sobre factos para o qual a entidade confiadora tem de desenvolver confiança suficiente para conceder a transação de pedido do utilizador final.  
>
> Também utilizamos o termo "afirmações" porque o Azure AD B2C políticas personalizadas que utilizam a arquitetura de experiência de identidade foram concebidas para simplificar a troca de todos os tipos de informações de identidade digital de uma forma consistente, independentemente se o protocolo subjacente é definido para obtenção de atributo ou autenticação de utilizador.  Da mesma forma, utilizamos o termo "fornecedores de afirmações" coletivamente referir-se a fornecedores de identidade, a fornecedores de atributo e verificadores de atributo quando não queremos distinguir entre as respetivas funções específicas.   

Assim, governem como informações de identidade são trocadas entre uma entidade confiadora, identidade e fornecedores de atributo e verificadores de atributo. Controlarem que identity e fornecedores de atributo são necessárias para a autenticação da entidade confiadora. Estes devem ser considerados como linguagem específicas do domínio (DSL), ou seja, um idioma do computador que tenha especializada para um domínio de aplicação em particular com herança, *se* instruções, de que o polimorfismo.

Estas políticas constituem a parte machine-readable a construção TF nas políticas personalizadas do Azure AD B2C tirar partido da estrutura de experiência de identidade. Incluem todos os detalhes operacionais, incluindo metadados e perfis técnicas, definições de esquema de afirmações, as funções de transformação de afirmações e percursos de utilizador que são preenchidos para facilitar a operacional orquestração e automatização dos fornecedores de afirmações.  

Estes são considerados como para ser *documentos de maior duração* porque não há a possibilidade de boa os respetivos conteúdos serão alteram ao longo do tempo relativo à declarado participantes ativas nas políticas. Também é o potencial que possam alterar os termos e condições para a ser um participante.  

Configuração de Federação e de manutenção são muito simplificados das entidades confiadoras de Reconfigurações de confiança e a conectividade contínuas de proteção como fornecedores de afirmações diferentes/verificadores aderir ou sair (Comunidade representada pelo) do conjunto de políticas.

Interoperabilidade é outro significativo desafio. Fornecedores de afirmações adicionais/verificadores deve ser integrados porque das entidades confiadoras são pouco provável suportar todos os protocolos necessários. As políticas personalizadas do Azure AD B2C resolverem este problema ao suporte de protocolos de norma da indústria e aplicando os percursos de utilizador específico para transpor pedidos quando das entidades confiadoras e fornecedores de atributo não suportam o mesmo protocolo.  

Percursos de utilizador incluem perfis de protocolo e metadados que são utilizadas para ligar "em risco" interoperabilidade entre a entidade confiadora e outros participantes. Também existem regras de tempo de execução operacional que são aplicadas a identidade exchange pedido/resposta as mensagens de informações para impor a compatibilidade com políticas publicadas como parte da especificação de TF. A ideia dos percursos de utilizador é importante para a personalização da experiência do cliente. É também sheds leve sobre o funcionamento do sistema ao nível do protocolo.

Dessa forma, portais e as aplicações de terceiros de entidade confiadora podem, consoante os respetivos contexto, invocar políticas personalizadas do Azure AD B2C que tiram partido da estrutura de experiência de identidade transmitir o nome de uma política específica e obter precisamente a troca de informações e comportamento pretendem sem qualquer muss, fuss ou risco.
