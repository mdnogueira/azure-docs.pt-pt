---
title: "Azure Blueprint de processamento de pagamento - requisitos da política"
description: Requisito de PCI DSS 12
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: a79d59d8-20e3-4efe-8686-c8f4ed80e220
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 05e9ed7c886d37a024db1eedbc541705b7d8a9a9
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="policy-requirements-for-pci-dss-compliant-environments"></a>Requisitos da política para ambientes de PCI DSS compatíveis  
## <a name="pci-dss-requirement-12"></a>Requisito de PCI DSS 12

**Manter uma política de segurança de informações de endereços para todo o pessoal**

> [!NOTE]
> Estes requisitos são definidos pelo [Council de normas de segurança da indústria de cartão de pagamento (PCI)](https://www.pcisecuritystandards.org/pci_security/) como parte do [PCI dados segurança Standard (DSS) versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre testes de procedimentos e as diretrizes para cada requisito.

Uma política de segurança forte define o sinal de segurança para a entidade completa e informa o técnico de que é esperado um deles. Todo o pessoal deve ter conhecimento da sensibilidade dos dados e as responsabilidades para protegê-lo. Para efeitos de requisito 12, "pessoal" refere-se os funcionários a tempo inteiro e part-time, funcionários temporários, subcontratados e consultores que são "residente" no site da entidade ou caso contrário, tem acesso ao ambiente de dados de cardholder.

## <a name="pci-dss-requirement-121"></a>Requisito de PCI DSS 12.1

**12.1** estabelecer, publicar, manter e disseminar uma política de segurança.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por estabelecer e manutenção de uma política de segurança de informações.|



### <a name="pci-dss-requirement-1211"></a>Requisito de PCI DSS 12.1.1

**12.1.1** rever a política de segurança, pelo menos, anual e atualizar a política quando o ambiente é alterado.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis para atualizar a respetiva política de segurança de informações anual, pelo menos, ou quando existem alterações ao seu respetivo ambiente de dados de cardholder (CDE).|



## <a name="pci-dss-requirement-122"></a>Requisito de PCI DSS 12.2

**12.2** implementar uma avaliação de risco processar que:
- É efetuada pelo menos anual e após alterações significativas no ambiente (por exemplo, aquisição, fusão, reposicionamento, etc.)
- Identifica elementos críticos, ameaças e vulnerabilidades
- Resultados numa análise formal, documentado de risco.
- > Exemplos de metodologias de avaliação de risco incluem, mas não estarem limitados aos OCTAVE, ISO 27005 e SP do NIST 800-30.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis pela implementação de um processo de avaliação de risco que abrange todas as ameaças listadas no 12.2 requisito.|



## <a name="pci-dss-requirement-123"></a>Requisito de PCI DSS 12.3

**12.3** desenvolver políticas de utilização para tecnologias críticas e definir a correta utilização destas tecnologias.

> [!NOTE]
> Exemplos de tecnologias críticos incluem, mas não são limitados para acesso remoto e tecnologias sem fios, computadores portáteis, tablets, suporte de dados amovível eletrónicas, utilização de correio eletrónico e a utilização de Internet.
Certifique-se de que estas políticas de utilização requerem o seguinte.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes serão responsáveis pela criação e manutenção das políticas dictating utilização adequada, implementação e autenticação para tecnologias críticas dentro do respetivo CDE.|



### <a name="pci-dss-requirement-1231"></a>Requisito de PCI DSS 12.3.1

**12.3.1** explícita aprovação por entidades confiadoras autorizadas

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes serão responsáveis pela criação e manutenção das políticas dictating utilização adequada, implementação e autenticação para tecnologias críticas dentro do respetivo CDE.|



### <a name="pci-dss-requirement-1232"></a>Requisito de PCI DSS 12.3.2

**12.3.2** autenticação para utilizar a tecnologia

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes serão responsáveis pela criação e manutenção das políticas dictating utilização adequada, implementação e autenticação para tecnologias críticas dentro do respetivo CDE.|



### <a name="pci-dss-requirement-1233"></a>Requisito de PCI DSS 12.3.3

**12.3.3** uma lista de todos os dispositivos e empregados com acesso

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes serão responsáveis pela criação e manutenção das políticas dictating utilização adequada, implementação e autenticação para tecnologias críticas dentro do respetivo CDE.|



### <a name="pci-dss-requirement-1234"></a>Requisito de PCI DSS 12.3.4

**12.3.4** um método com precisão e prontamente determinar proprietário, as informações de contacto e objetivo (por exemplo, etiquetando, codificação, e/ou inventário de dispositivos)

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes serão responsáveis pela criação e manutenção das políticas dictating utilização adequada, implementação e autenticação para tecnologias críticas dentro do respetivo CDE.|



### <a name="pci-dss-requirement-1235"></a>Requisito de PCI DSS 12.3.5

**12.3.5** aceitável utiliza a tecnologia

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes serão responsáveis pela criação e manutenção das políticas dictating utilização adequada, implementação e autenticação para tecnologias críticas dentro do respetivo CDE.|



### <a name="pci-dss-requirement-1236"></a>Requisito de PCI DSS 12.3.6

**12.3.6** localizações de rede aceitável para as tecnologias

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por determinar as localizações de rede aceitável para as VMs de baseados na cloud, armazenamento e serviços de suporte.|



### <a name="pci-dss-requirement-1237"></a>Requisito de PCI DSS 12.3.7

**12.3.7** lista dos produtos aprovado da empresa

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por determinar as localizações de rede aceitável para as VMs de baseados na cloud, armazenamento e serviços de suporte.|



### <a name="pci-dss-requirement-1238"></a>Requisito de PCI DSS 12.3.8

**12.3.8** desligar automática de sessões para tecnologias de acesso remoto após um período de inatividade

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure utiliza a funcionalidade de bloqueio do Microsoft AD empresarial sessão, que impõe outs de bloqueio de sessão após um período de inatividade. Ligações de rede estão terminadas após 30 minutos de inatividade. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes serão responsáveis pela criação e manutenção das políticas dictating utilização adequada, implementação e autenticação para tecnologias críticas dentro do respetivo CDE.|



### <a name="pci-dss-requirement-1239"></a>Requisito de PCI DSS 12.3.9

**12.3.9** ativação das tecnologias de acesso remoto para os fornecedores e parceiros de negócios apenas quando necessário para os fornecedores e parceiros de negócios, com a desativação de imediato após a utilização

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes serão responsáveis pela criação e manutenção das políticas dictating utilização adequada, implementação e autenticação para tecnologias críticas dentro do respetivo CDE.|



### <a name="pci-dss-requirement-12310"></a>Requisito de PCI DSS 12.3.10

**12.3.10** para proibir técnico aceder aos dados cardholder através de tecnologias de acesso remoto, a copiar, mover e armazenamento de dados de cardholder para unidades de disco rígido locais e o suporte de dados Eletrónicos amovível, a menos que autorizado explicitamente para um definido necessidade empresarial.
Em que exista uma necessidade de negócio autorizados, as políticas de utilização devem necessitar dos dados ser protegidos em conformidade com todas as aplicável PCI DSS requisitos.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por garantir que técnico aceder aos dados cardholder através de tecnologias de acesso remoto está proibidos de as copiar, mover e armazenar dados cardholder unidades de disco rígido locais e o suporte de dados Eletrónicos amovível, a menos que explicitamente autorizado para uma necessidade de negócio definido.|



## <a name="pci-dss-requirement-124"></a>Requisito de PCI DSS 12.4

**12.4** Certifique-se de que a política de segurança e procedimentos definem claramente responsabilidades de segurança de informações de todo o pessoal.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes serão responsáveis pela criação e manutenção das políticas dictating utilização adequada, implementação e autenticação para tecnologias críticas dentro do respetivo CDE.|



### <a name="pci-dss-requirement-1241"></a>Requisito de PCI DSS 12.4.1

**12.4.1** requisitos adicionais para fornecedores de serviços só: gestão de executivo estabelecer a responsabilidade para a proteção de dados de cardholder e um programa de conformidade de PCI DSS para incluir:
- Accountability geral para manter a conformidade de PCI DSS
- Definir um charter para um programa de conformidade de PCI DSS e comunicação para a gestão executiva 

> [!NOTE]
> Este requisito é uma melhor prática até 31 de Janeiro de 2018, após o qual torna-se um requisito.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes que estão a fornecedores de serviços são responsáveis por documentar as respetivas programa de conformidade de PCI.|



## <a name="pci-dss-requirement-125"></a>Requisito de PCI DSS 12,5

**12,5** atribuir a uma pessoa ou equipa as seguintes responsabilidades de gestão de segurança de informações.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por definir e atribuir informações responsabilidades de segurança para os seus empregados.|



### <a name="pci-dss-requirement-1251"></a>Requisito de PCI DSS 12.5.1

**12.5.1** estabelecer, documentar e distribuir as políticas de segurança e procedimentos.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por definir e atribuir informações responsabilidades de segurança para os seus empregados.|



### <a name="pci-dss-requirement-1252"></a>Requisito de PCI DSS 12.5.2

**12.5.2** monitor analisar alertas de segurança e as informações e distribuir ao pessoal adequado.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por definir e atribuir informações responsabilidades de segurança para os seus empregados.|



### <a name="pci-dss-requirement-1253"></a>Requisito de PCI DSS 12.5.3

**12.5.3** estabelecer, documentos e distribuir incidentes resposta e Escalamento os procedimentos de segurança para garantir o processamento atempadamente e eficaz de todas as situações.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes serão responsáveis pela criação e manutenção das políticas dictating utilização adequada, implementação e autenticação para tecnologias críticas dentro do respetivo CDE.|



### <a name="pci-dss-requirement-1254"></a>Requisito de PCI DSS 12.5.4

**12.5.4** administrar as contas de utilizador, incluindo adições, eliminações e modificações.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes serão responsáveis pela criação e manutenção das políticas dictating utilização adequada, implementação e autenticação para tecnologias críticas dentro do respetivo CDE.|



### <a name="pci-dss-requirement-1255"></a>Requisito de PCI DSS 12.5.5

**12.5.5** monitorize e controle todas aceder aos dados.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes serão responsáveis pela criação e manutenção das políticas dictating utilização adequada, implementação e autenticação para tecnologias críticas dentro do respetivo CDE.|



## <a name="pci-dss-requirement-126"></a>Requisito de PCI DSS 12.6

**12.6** implementa um programa de deteção de segurança formal para fazer com que todo o pessoal em atenção a importância de política de segurança de dados de cardholder e procedimentos.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes serão responsáveis pela criação e manutenção das políticas envolvente deteção de segurança para os funcionários com acesso para o CDE.|



### <a name="pci-dss-requirement-1261"></a>Requisito de PCI DSS 12.6.1

**12.6.1** informe técnico após contratação e pelo menos anual. 

> [!NOTE]
> Métodos podem variar consoante a função dos empregados e o respetivo nível de acesso aos dados cardholder.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por assegurar que os funcionários receber e confirmar a deteção de PCI-DSS anual, pelo menos, de preparação e de segurança de informações.|



### <a name="pci-dss-requirement-1262"></a>Requisito de PCI DSS 12.6.2

**12.6.2** requerem técnico, pelo menos, anual reconhecer que estes li e compreendi a política de segurança e procedimentos.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por assegurar que os funcionários receber e confirmar a deteção de PCI-DSS anual, pelo menos, de preparação e de segurança de informações.|



## <a name="pci-dss-requirement-127"></a>Requisito de PCI DSS 12.7

**12.7** ecrã potenciais técnico antes da contratamos para minimizar o risco de ataques de origens internas. (Verificações exemplos de histórico de sua admissão anterior, registo criminal, histórico de crédito e referência verifica.) 

> [!NOTE]
> Para essas pessoal potencial para ser recrutados para determinados posições como cashiers de arquivo que apenas têm acesso para o número do cartão de um cada vez quando o facilitar o início uma transação, este requisito é apenas uma recomendação.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por assegurar que os funcionários com acesso para o CDE sofrer verificações de detalhado em segundo plano.|



## <a name="pci-dss-requirement-128"></a>Requisito de PCI DSS 12.8

**12.8** manter e implementar políticas e procedimentos para gerir fornecedores de serviços com quem cardholder dados são partilhados ou que podem afetar a segurança dos dados cardholder, da seguinte forma.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por monitorizar a conformidade de PCI para os fornecedores de serviços com quem cardholder dados são partilhados ou pode afetar a segurança do CDE. Os clientes têm de manter uma lista de todos os serviço fornece utilizadas dentro do respetivo CDE.|



### <a name="pci-dss-requirement-1281"></a>Requisito de PCI DSS 12.8.1

**12.8.1** manter uma lista de fornecedores de serviços, incluindo uma descrição de serviço fornecido.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por monitorizar a conformidade de PCI para os fornecedores de serviços com quem cardholder dados são partilhados ou pode afetar a segurança do CDE. Os clientes têm de manter uma lista de todos os serviço fornece utilizadas dentro do respetivo CDE.|



### <a name="pci-dss-requirement-1282"></a>Requisito de PCI DSS 12.8.2

**12.8.2** manter um contrato de escrita que inclui uma confirmação que os fornecedores de serviços são responsáveis pela segurança dos dados cardholder os fornecedores de serviços possuem caso contrário, armazenam, processam ou transmitem em nome do cliente, ou mesmo que estes pode afetar a segurança do ambiente de dados de cardholder do cliente. 

> [!NOTE]
> As palavras utilizadas exata de uma confirmação dependerá do contrato entre as duas partes, os detalhes do serviço que está a ser fornecido e as responsabilidades atribuídas a cada entidade. A confirmação não tem de incluir a utilização de expressões exata fornecida este requisito.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis pela manutenção dos contratos escritos com fornecedores de serviços de confirmar a responsabilidade para manter a segurança dos dados cardholder.|



### <a name="pci-dss-requirement-1283"></a>Requisito de PCI DSS 12.8.3

**12.8.3** Certifique-se existe um processo estabelecido para o envolvimento dos fornecedores de serviços, incluindo adequado devido antes de diligence para o engagement.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por assegurar que não há um processo estabelecido para o envolvimento dos fornecedores de serviços, incluindo adequado devido antes de diligence para o engagement.|



### <a name="pci-dss-requirement-1284"></a>Requisito de PCI DSS 12.8.4

**12.8.4** manter um programa para monitorizar o estado de conformidade de PCI DSS de fornecedores de serviços, pelo menos, anual.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes é responsável pela manutenção um programa para monitorizar o estado de conformidade de PCI DSS de fornecedores de serviços, pelo menos, anual.|



### <a name="pci-dss-requirement-1285"></a>Requisito de PCI DSS 12.8.5

**12.8.5** mantêm as informações sobre os requisitos de PCI DSS são geridos por cada fornecedor de serviços e que são geridos pela entidade.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por manter uma cópia do [matriz de resumo de responsabilidade](https://aka.ms/pciblueprintcrm32), que descreve os requisitos de PCI DSS são da responsabilidade do cliente e os que são da responsabilidade do Microsoft Azure.|



## <a name="pci-dss-requirement-129"></a>Requisito de PCI DSS 12.9

**12.9** requisitos adicionais para fornecedores de serviços só: fornecedores de serviços reconhecer escrever aos clientes que são responsáveis pela segurança dos dados cardholder o fornecedor de serviços tiver ou caso contrário, armazena, processos, ou transmite em nome do cliente ou na extensão que que pode afetar a segurança do ambiente de dados de cardholder do cliente. 

> [!NOTE]
> As palavras utilizadas exata de uma confirmação dependerá do contrato entre as duas partes, os detalhes do serviço que está a ser fornecido e as responsabilidades atribuídas a cada entidade. A confirmação não tem de incluir a utilização de expressões exata fornecida este requisito.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes que são fornecedores de serviços são responsáveis por confirmar as responsabilidades para manter a conformidade de PCI. |



## <a name="pci-dss-requirement-1210"></a>Requisito de PCI DSS 12.10

**12.10** implementar um plano de resposta a incidentes. Esteja preparado para responder imediatamente a uma violação de sistema.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por planos de resposta a incidentes de desenvolvimento e testes que considera quaisquer controlos de cliente relacionadas com pontos de touch partilhado e quaisquer aplicações de cliente tirar partido da infraestrutura do Azure. É responsabilidade do cliente, para fornecer informações de contacto precisos para o Azure, no caso de um incidente tem de ser comunicadas aos mesmos, que poderá afetar os respetivos dados ou a aplicação.|



### <a name="pci-dss-requirement-12101"></a>Requisito de PCI DSS 12.10.1

**12.10.1** criar o plano de resposta a incidentes para ser implementada em caso de violação de sistema. Certifique-se os endereços de plano seguinte, no mínimo:
- Estratégias de comunicação e contacte em caso de comprometimento, incluindo notificações de marcas de pagamento, no mínimo, responsabilidades e funções
- Procedimentos de resposta a incidentes específicos
- Procedimentos de recuperação e de continuidade de negócio
- Processos de cópia de segurança de dados
- Análise dos requisitos legais para os compromissos de relatórios
- Cobertura e respostas de todos os componentes de sistema críticos
- Referência ou de inclusão dos procedimentos de resposta a incidentes de marcas de pagamento

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por planos de resposta a incidentes de desenvolvimento e testes que considera quaisquer controlos de cliente relacionadas com pontos de touch partilhado e quaisquer aplicações de cliente tirar partido da infraestrutura do Azure. É responsabilidade do cliente, para fornecer informações de contacto precisos para o Azure, no caso de um incidente tem de ser comunicadas aos mesmos, que poderá afetar os respetivos dados ou a aplicação.|



### <a name="pci-dss-requirement-12102"></a>Requisito de PCI DSS 12.10.2

**12.10.2** rever e o plano de teste, incluindo todos os elementos listado no requisito 12.10.1, pelo menos anual.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por planos de resposta a incidentes de desenvolvimento e testes que considera quaisquer controlos de cliente relacionadas com pontos de touch partilhado e quaisquer aplicações de cliente tirar partido da infraestrutura do Azure. É responsabilidade do cliente, para fornecer informações de contacto precisos para o Azure, no caso de um incidente tem de ser comunicadas aos mesmos, que poderá afetar os respetivos dados ou a aplicação.|



### <a name="pci-dss-requirement-12103"></a>Requisito de PCI DSS 12.10.3

**12.10.3** designar pessoal específico para estar disponível de 24x7 forma para responder a alertas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por planos de resposta a incidentes de desenvolvimento e testes que considera quaisquer controlos de cliente relacionadas com pontos de touch partilhado e quaisquer aplicações de cliente tirar partido da infraestrutura do Azure. É responsabilidade do cliente, para fornecer informações de contacto precisos para o Azure, no caso de um incidente tem de ser comunicadas aos mesmos, que poderá afetar os respetivos dados ou a aplicação.|



### <a name="pci-dss-requirement-12104"></a>Requisito de PCI DSS 12.10.4

**12.10.4** fornecer formação apropriada para a equipa de violação de segurança responsabilidades de resposta.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por planos de resposta a incidentes de desenvolvimento e testes que considera quaisquer controlos de cliente relacionadas com pontos de touch partilhado e quaisquer aplicações de cliente tirar partido da infraestrutura do Azure. É responsabilidade do cliente, para fornecer informações de contacto precisos para o Azure, no caso de um incidente tem de ser comunicadas aos mesmos, que poderá afetar os respetivos dados ou a aplicação.|



### <a name="pci-dss-requirement-12105"></a>Requisito de PCI DSS 12.10.5

**12.10.5** incluem alertas a partir de sistemas, incluindo mas não limitado a deteção de intrusão, prevenção de intrusões, firewalls e sistemas de monitorização de integridade do ficheiro de monitorização de segurança.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por planos de resposta a incidentes de desenvolvimento e testes que considera quaisquer controlos de cliente relacionadas com pontos de touch partilhado e quaisquer aplicações de cliente tirar partido da infraestrutura do Azure. É responsabilidade do cliente, para fornecer informações de contacto precisos para o Azure, no caso de um incidente tem de ser comunicadas aos mesmos, que poderá afetar os respetivos dados ou a aplicação.|



### <a name="pci-dss-requirement-12106"></a>Requisito de PCI DSS 12.10.6

**12.10.6** desenvolver um processo para modificar e evoluir o plano de resposta a incidentes, de acordo com lições aprendidas e incorporar desenvolvimentos da indústria.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por planos de resposta a incidentes de desenvolvimento e testes que considera quaisquer controlos de cliente relacionadas com pontos de touch partilhado e quaisquer aplicações de cliente tirar partido da infraestrutura do Azure. É responsabilidade do cliente, para fornecer informações de contacto precisos para o Azure, no caso de um incidente tem de ser comunicadas aos mesmos, que poderá afetar os respetivos dados ou a aplicação.|



## <a name="pci-dss-requirement-1211"></a>Requisito de PCI DSS 12.11

**12.11** **requisitos adicionais para fornecedores de serviços só:** efetuar revisões, pelo menos, trimestral para confirmar a técnico é os seguintes procedimentos operacionais e de políticas de segurança.
Revisões devem abranger os seguintes processos:
- Revê registos diário
- Revisões de conjunto de regras de firewall
- Aplicar as normas de configuração para novos sistemas
- Responder a alertas de segurança
- Processos de gestão de alteração 

> [!NOTE]
> Este requisito é uma melhor prática até 31 de Janeiro de 2018, após o qual torna-se um requisito.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes que estão a fornecedores de serviços são responsáveis por documentar as revisões de processos para confirmar o desempenho do controlo de conformidade de PCI.|



### <a name="pci-dss-requirement-12111"></a>Requisito de PCI DSS 12.11.1

**12.11.1** requisitos adicionais para fornecedores de serviços só: manter a documentação do processo de revisão trimestral para incluir:
- Documentar resultados das revisões
- Reveja e início de sessão convocaremos de resultados por parte do pessoal atribuída responsabilidade para o programa de conformidade de PCI DSS 

> [!NOTE]
> Este requisito é uma melhor prática até 31 de Janeiro de 2018, após o qual torna-se um requisito.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes que estão a fornecedores de serviços são responsáveis por documentar as revisões de processos para confirmar o desempenho do controlo de conformidade de PCI.|




