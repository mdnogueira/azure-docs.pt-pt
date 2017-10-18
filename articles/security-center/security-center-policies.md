---
title: "Definir políticas de segurança no Centro de Segurança do Azure | Microsoft Docs"
description: "Este documento ajuda-o a configurar as políticas de segurança no Centro de Segurança do Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: yurid
ms.openlocfilehash: 67564e930310433bf4d51f7642bdd7ebf7e8e600
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-security-policies-in-azure-security-center"></a>Definir políticas de segurança no Centro de Segurança do Azure
Este documento ajuda-o a configurar as políticas de segurança no Centro de Segurança, ao servir de orientação pelos passos necessários para realizar esta tarefa.


## <a name="how-security-policies-work"></a>Como funcionam as políticas de segurança?
O Centro de Segurança cria automaticamente uma política de segurança predefinida para cada uma das suas subscrições do Azure. Pode editá-la no Centro de Segurança ou utilizar a [Política do Azure](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) para criar definições novas, definir políticas adicionais e atribuir políticas a vários Grupos de Gestão (que podem representar toda a organização, uma unidade de negócio, etc.) e monitorizar a conformidade com estas políticas nestes âmbitos.

> [!NOTE]
> O Azure Policy está em pré-visualização limitada. Clique [aqui](https://aka.ms/getpolicy) para aderir. Para obter mais informações sobre as Políticas do Azure, leia [Criar e gerir políticas para impor a conformidade](http://docs.microsoft.com/en-us/azure/azure-policy/create-manage-policy).

## <a name="how-to-change-security-policies-in-security-center"></a>Como alterar políticas de segurança no Centro de Segurança?
Pode editar a política de segurança predefinida para cada uma das suas subscrições do Azure no Centro de Segurança. Para modificar uma política de segurança, tem de ser proprietário, contribuidor ou Administrador de Segurança dessa subscrição ou do Grupo de Gestão que a contém. Inicie sessão no portal do Azure e siga os passos subsequentes para ver as suas políticas de segurança no Centro de Segurança:

1. No dashboard **Centro de Segurança**, em **Geral**, clique em **Política de Segurança**.
2. Selecione a subscrição na qual quer ativar a política de segurança.

    ![Gestão de Políticas](./media/security-center-policies/security-center-policies-fig10.png)

3. Na secção **COMPONENTES DA POLÍTICA**, clique em **Política de segurança**.

    ![Componentes da política](./media/security-center-policies/security-center-policies-fig12.png)

4. Esta é a política predefinida atribuída ao Centro de Segurança através do Azure Policy. Pode eliminar os itens que estão em **POLÍTICAS E PARÂMETROS** ou adicionar outras definições de política que estão em **OPÇÕES DISPONÍVEIS**. Para tal, basta clicar no sinal de adição junto ao nome da definição.

    ![Definições de política](./media/security-center-policies/security-center-policies-fig11.png)

5. Se quiser uma explicação mais detalhada sobre a política, clique na mesma e será aberta outra página com os detalhes e o código JSON com a estrutura [definição de política (https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy#policy-definition-structure):

    ![Json](./media/security-center-policies/security-center-policies-fig13.png)

6. Quando concluir a edição, clique em **Guardar**.

## <a name="see-also"></a>Consultar também
Neste documento, aprendeu a configurar as políticas de segurança no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Guia de operações e planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md). Saiba como planear e compreender as considerações de conceção para adoção do Centro de Segurança do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md). Saiba como gerir e responder a alertas de segurança.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md). Saiba como monitorizar o estado de funcionamento das soluções dos seus parceiros.
* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
