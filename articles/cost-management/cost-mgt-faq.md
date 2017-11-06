---
title: "Perguntas mais frequentes sobre gestão de custo do Azure | Microsoft Docs"
description: "Fornece respostas a algumas das perguntas comuns sobre a gestão de custo do Azure."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/23/2017
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: a01d8d1ed0f5234f4950d448b54087767353c8ef
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="frequently-asked-questions-for-azure-cost-management"></a>Perguntas mais frequentes sobre gestão de custo do Azure


Este artigo aborda algumas perguntas comuns sobre a gestão de custo do Azure (também conhecido como Cloudyn). Se tiver dúvidas sobre a gestão de custo, pode pedir ao-las em [perguntas mais frequentes para o Azure custo Management Cloudyn](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-azure-cost-management-by-cloudyn?forum=Cloudyn).

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Como posso resolver problemas comuns de configuração de empresa indireta?

Ao utilizar o portal de Cloudyn pela primeira vez, poderá ver as seguintes mensagens de se for um utilizador Enterprise Agreement ou o fornecedor de solução em nuvem (CSP):

- "A chave de API especificada não é uma chave de registo de nível superior" apresentado no **conjunto de cópias de segurança do Azure custo gestão** assistente.
- "Inscrição direta – não" apresentado no portal do contrato Enterprise.
- "Não existem dados de utilização encontrados para os últimos 30 dias. Entre em contacto com o distribuidor para se certificar de marcação foi ativada para a sua conta do Azure"apresentado no portal do Cloudyn.

As mensagens anteriores indicam que comprou um Enterprise Agreement do Azure através de um revendedor ou CSP. Seu revendedor ou CSP tem de ativar _markup_ para o Azure da conta para que possa visualizar os dados no Cloudyn.

Eis como corrigir os problemas:

1. Tem de ativar o revendedor _markup_ para a sua conta. Para obter instruções, consulte o [Indireta guia de integração do cliente](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. Gerar a chave de Enterprise Agreement do Azure para utilização com Cloudyn. Para obter instruções, consulte [EA para adicionar o Azure](https://support.cloudyn.com/hc/en-us/articles/210429585-Adding-Your-AZURE-EA) ou [como encontrar o ID de inscrição EA e a chave de API](https://youtu.be/u_phLs_udig).

Apenas um administrador de serviço do Azure pode ativar a gestão de custo. Permissões de coadministrador são insuficientes.

Pode gerar a chave de API de contrato Enterprise do Azure para configurar Cloudyn, tem de ativar a API de faturação do Azure ao seguir as instruções em:

- [Descrição geral de APIs de relatórios para os clientes empresariais](../billing/billing-enterprise-api.md)
- [Portal de empresa do Microsoft Azure API de relatórios](https://ea.azure.com/helpdocs/reportingAPI) em **ativar acesso a dados para a API**


Também poderá ter de conceder os administradores de departamento, proprietários de conta e permissões de administradores da empresa para _ver encargos_ com a API de faturação.

## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Como ativar a suspensos ou Escalamento bloqueado utilizadores?

Se receber um alerta com um pedido para permitir o acesso de um utilizador, terá de ativar a conta de utilizador.

Para ativar a conta de utilizador:

1. Inicie sessão no Cloudyn utilizando a conta de utilizador administrativo do Azure que utilizou para configurar Cloudyn. Em alternativa, inicie sessão com uma conta de utilizador que foi concedida acesso de administrador.

2. Selecione o símbolo de equipamento no canto superior direito e selecione **gestão de utilizadores**.

3. Localize o utilizador, selecione o símbolo de lápis e, em seguida, edite o utilizador.

4. Em **estado do utilizador**, alterar o estado de **suspenso** para **Active Directory**.

Contas de utilizador Cloudyn ligam utilizando o início de sessão a partir do Azure. Se um utilizador mistypes a palavra-passe, poderá obter bloqueados de Cloudyn, mesmo que ainda possam aceder ao Azure.

Se alterar o seu endereço de correio electrónico no Cloudyn do endereço predefinido no Azure, a sua conta pode obter bloqueada. Poderá mostrar "Estado initiallySuspended." Se a sua conta de utilizador está bloqueada, contacte um administrador alternativo para repor a sua conta.

Recomendamos que crie, pelo menos, duas contas de administrador de Cloudyn no caso de uma das contas obtém bloqueada.

Se não é possível iniciar sessão no portal de Cloudyn, certifique-se de que está a utilizar o URL de gestão de custo correto do Azure para iniciar sessão no Cloudyn. Utilize um dos seguintes URLs:

- https://Azure.cloudyn.com
- https://MS.Portal.Azure.com/#Blade/Microsoft_Azure_CostManagement/CloudynMainBlade

Evite utilizar o https://app.cloudyn.com URL direta Cloudyn.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Como ativar o não ativados tentarem contas com credenciais do Azure?

Assim que as contas do Azure são detetadas pelo Cloudyn, dados de custo imediatamente são fornecidos nos relatórios baseados em custos. No entanto, para Cloudyn fornecer dados de utilização e desempenho, tem de registar as suas credenciais do Azure para as contas. Para obter instruções, consulte [adicionar o Azure Resource Manager](https://support.cloudyn.com/hc/en-us/articles/212784085-Adding-Azure-Resource-Manager).

Para adicionar as credenciais do Azure para uma conta no portal do Cloudyn, selecione o símbolo de edição à direita do nome da conta, não a subscrição.

Até que as suas credenciais do Azure são adicionados ao Cloudyn, a conta é apresentado como _não ativado_.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Como adicionar múltiplas contas e as entidades a uma subscrição existente?

Entidades adicionais são utilizadas para adicionar contratos de Enterprise adicionais a uma subscrição de Cloudyn. As hiperligações seguintes descrevem como adicionar entidades adicionais:

- [Adicionar uma entidade](https://support.cloudyn.com/hc/en-us/articles/212016145-Adding-an-Entity) artigo
- [Definir a hierarquia de entidades de custo](https://support.cloudyn.com/hc/en-us/articles/115005142529-Video-Defining-your-hierarchy-with-Cost-Entities) vídeo

Para os CSPs:

Para adicionar contas CSP adicionais para uma entidade, selecione **\\<ServerName>\SMS_<SITECODE>\HOTFIX\<KB acesso** em vez de **Enterprise** ao criar a entidade de novo. Se a sua conta está registada como um Enterprise Agreement e pretender adicionar credenciais CSP, Cloudyn suporte técnico poderá ter de modificar as definições de conta. Se tiver um pago subscritor do Azure, pode criar um novo pedido de suporte no portal do Azure. Selecione **ajuda + suporte**e, em seguida, selecione **novo pedido de suporte**.

## <a name="how-do-i-change-the-currency-symbol-used-in-cloudyn"></a>Como posso alterar o símbolo de moeda utilizada no Cloudyn?

Quando todas as contas do Azure numa única entidade utilizam a mesma moeda, a moeda utilizada é detetada automaticamente. No entanto, o símbolo de moeda indica é mostrado como  **$**  para qualquer uma das moedas seguintes:

- GBP = Reino Unido cardinal sterling
- EUR = euro alfabetos
- INR = Indian rupee
- NOK = krone norueguês

Embora o símbolo de moeda poderá mostrar  **$**  para E.U.A. não utilizados no compromisso, os valores de custo são apresentados na moeda correta. Por exemplo, se todas as suas contas utilizam euros na mesma entidade, o _valores_ mostrado na Cloudyn são euros, embora o  **$**  símbolo aparece indica.

Se tiver um cliente do Enterprise Agreement do Azure, os técnicos de suporte de Cloudyn podem alterar o símbolo de moeda apresentado nos relatórios de custo de $. Pode criar um novo pedido de suporte no portal do Azure. Selecione **ajuda + suporte**e, em seguida, selecione **novo pedido de suporte**.

Se tiver um cliente CSP, não é possível alterar o símbolo de moeda. Cloudyn só suporta cartões que utilizam utilizados no compromisso E.U.A. Cloudyn está a explorar a opção para suportar cartões de taxas de moedas diferentes.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Quais são os dados de Cloudyn atualizar linhas cronológicas?

Cloudyn tem as linhas cronológicas atualização de dados seguintes:

- **Inicial**: depois de configurar, pode demorar até 24 horas para ver os dados de custo em Cloudyn. Também pode demorar até 10 dias para Cloudyn recolher dados suficientes para apresentar as recomendações de dimensionamento.
- **Diária**: A contar do dia tenth ao fim de cada mês, Cloudyn deve mostrar os seus dados atualizados do dia anterior após sobre UTC + 3 o dia seguinte.
- **Mensal**: do primeiro dia a dia tenth de cada mês, Cloudyn poderá mostrar os seus dados apenas até ao fim do mês anterior.

Cloudyn processa dados para o dia anterior, quando estão disponíveis dados completos do dia anterior. Dados do dia anterior estão normalmente disponíveis em Cloudyn por sobre UTC + 3 cada dia. Alguns dados, tais como etiquetas, podem demorar um adicional de 24 horas para processar.

Dados para o mês atual não estão disponíveis para a coleção no início de cada mês. Durante o período, a fornecedores de serviços de finalizar os respetivos faturação para o mês anterior. Dados do mês anterior é apresentado no Cloudyn 5 a 10 dias após o início de cada mês. Durante este período, poderá ver apenas amortized custos do mês anterior. Não poderá ver dados de faturação ou utilização diários. Quando os dados ficarem disponíveis, Cloudyn os processos que retroactively. Após o processamento, todos os dados mensais é mostrada entre o quinto dia e tenth dia de cada mês.

Se existir um atraso de envio de dados do Azure para Cloudyn, dados ainda são registados no Azure. Os dados são transferidos para Cloudyn quando a ligação é restaurada.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Como pode um CSP direto configurar Cloudyn acesso para indiretos CSP clientes ou parceiros?

Para obter instruções, consulte [configurar o acesso CSP indireto no Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).
