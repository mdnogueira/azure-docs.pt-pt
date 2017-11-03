---
title: Implementar a sua oferta no Azure Marketplace | Microsoft Docs
description: "Saiba mais sobre e percorrer as instruções para implementar a sua oferta – imagem de máquina virtual, o serviço de programador, serviço de dados, etc. - para o Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 8f79b891-84e2-4f41-ba0d-66420e2c6b2e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2016
ms.author: hascipio
ms.openlocfilehash: 12dc81642905cd9449a1032c7ab57298e6b69ba8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Implementar a sua oferta no Azure Marketplace
Quando estiver satisfeito com a sua oferta (ou seja, tiver testado cenários de cliente, de marketing conteúdo, etc.) e está pronto para iniciar, pedir **Push para produção** no **publicar** separador.  

1. Os quatro passos sob as instruções página na publicação portal deve ser concluída e verde. Para ofertas de Máquina Virtual, certifique-se de que as seguintes diretrizes são seguidas.
   
    ![desenho][img-pubportal-walkthru-checked]
2. Selecione o **publicar** separador da lista no lado esquerdo.
   
    ![desenho][img-pubportal-menu-publish]
3. Clique no botão **solicitar aprovação para push para produção**. Depois do pedido é efetuado, a equipa de aprovação executa uma revisão final e, em seguida, a oferta estarão disponível no Azure Marketplace.
   
    ![desenho][img-pubportal-publish-pushproduction]

> [!IMPORTANT]
> Em caso de máquinas virtuais, ao clicar no botão de pedido de aprovação para enviar para produção, os seguintes passos são efetuados atrás cena. Poderá ver o progresso de cada passo no separador Publicar na publicação portal. Tem de verificar esta página num intervalo regular (até que o estado mostra "Apresentados") para qualquer informação de falha que precisam de correção da sua end.
> 
> * Em primeiro lugar, o pedido de produção passa para a equipa de certificação que validar o vhd. No entanto, se atualizar a sua oferta já listada e o pedido tem obteve apenas alterações de marketing, em seguida, o passo de certificação é ignorado.
> * No próximo passo, o pedido são fornecidos para a equipa de validação de conteúdos que confirme os conteúdos de marketing da oferta.
> * Se forem efetuadas com êxito os passos acima, a oferta é aprovada na produção. Neste momento, o estado ficar "listado" no portal de publicação. No entanto, este estado de "Apresentados" não implica que o processo estiver concluído. Os seguintes passos têm de ser concluída antes da oferta está disponível no Azure Marketplace.
> * Quando a oferta é aprovada na produção no passo acima, da oferta de início da replicação em todos os centros de dados do Azure. Geralmente, demora 24-48hours a conclusão da replicação mas poderá demorar até uma semana, consoante o tamanho do vhd. No entanto, se atualizar a sua oferta já listada e tem obteve a alteração de marketing apenas, em seguida, a replicação é mais rápida.
> * Quando a replicação estiver concluída, em seguida, a oferta estarão disponível no Azure Marketplace.
> 
> Pode sempre eliminar a oferta enquanto está a ser um **rascunho** Estado (ou seja, nunca **Push transição** ou **Push para produção**). No **histórico** separador, clique em de **rejeitar rascunho** na parte inferior da página para eliminar um rascunho.
> 
> 

## <a name="production-checklist-for-all-virtual-machine-offers"></a>Lista de verificação de produção para todas as ofertas de Máquina Virtual
* Certifique-se de que um parceiro de certificados do Microsoft Azure
* No separador SKUs, a opção "Ocultar esta SKU do Marketplace porque deve sempre ser comprou através de um modelo de solução" deverá ser marcado como Sim, apenas se o SKU não faz parte de um modelo de solução. Em todos os outros casos, esta opção sempre deverá ser marcada como não.
* Lembre-se:, Não deve alterar a definição de visibilidade SKU depois do SKU não está listada. Não é proporcionado suporte esta funcionalidade.
* Certifique-se de que o logótipos cumprem as diretrizes de logótipo do Azure Marketplace indicadas abaixo.
* Descrição de oferta e SKU não deve ser igual.
* Do SKU título e oferecem longo resumo não devem ser igual.
* Título de SKU e o resumo de oferta não devem ser igual.
* Não deve ser idênticos para uma oferta a SKUs de vários títulos de SKU.

**Diretrizes de logótipo do Azure Marketplace**

* A estrutura do Azure tem uma paleta de cores simples. Mantenha o número de site primário e secundário cores o logótipo baixa.
* As cores de tema do portal do Azure estão em brancos e marcar a negrito. Por conseguinte, evite utilizar estes cores como a cor de fundo da sua logótipos. Utilize algumas cor que iria tornar o seu logótipos prominent no portal do Azure. Recomendamos simples de principal de cores. Se estiver a utilizar o fundo transparente, em seguida, certifique-se de que o texto/logótipo não é branco ou marcar a negrito.
* Não utilize um fundo gradação o logótipo.
* Evite a colocação de texto, mesmo sua empresa ou o nome da marca, no logótipo.
* O aspeto e funcionalidade do logótipo deve ser 'simples' e deve evitar gradações.
* Não deve ser esticado o logótipo.

**Diretrizes adicionais para o logótipo heroína:**

* O logótipo heroína é opcional. O fabricante pode optar por não carregar um logótipo de heroína. **No entanto uma vez carregado o ícone de heroína não pode ser eliminado da publicação portal. Nessa altura, o parceiro tem de seguir as diretrizes de Azure Marketplace para os ícones heroína pessoa que não irá ser aprovada a oferta para produção.**
* O nome de apresentação do publicador, o título SKU e a oferta longa resumo são apresentados na cor branco do tipo de letra. Por conseguinte, deve evitar manter quaisquer cor leve em segundo plano do ícone heroína. Negra, fundo branco e transparente não é permitido para ícones heroína.
* O publicador apresentar o nome, o título do SKU, a oferta longa resumo e no botão Criar estão incorporados programaticamente dentro o logótipo heroína assim que a oferta fica listada. Por isso, não deve introduzir qualquer texto enquanto está a conceber o logótipo heroína. Basta deixe espaço em branco à direita, porque o texto (ou seja, nome a apresentar do publicador, título do SKU, a oferta longa resumo) será incluído programaticamente por-nos através do mesmo. O espaço em branco para o texto deve ser 415 x 100 à direita (e é de deslocamento por 370px da esquerda).

## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>Lista de verificação de produção adicionais para a máquina de Virtual já listadas oferece
* Verifique se existe já uma oferta com o mesmo nome de oferta da sua empresa. Se Sim, em seguida, deve adicionar uma nova versão de SKU no oferta existente em vez de criar uma nova oferta duplicada.
* Disco de dados não deve ser alterado entre duas versões do mesmo SKU.
* No Azure Marketplace não suporta a alteração de preço dos SKUS listadas como tem impacto sobre faturação dos clientes existentes. Certifique-se de que não altere os preços dos SKUs listados regiões onde o SKU não está disponível. No entanto, pode adicionar novos SKUs ou adicionar nova regiões para um SKU existente.

## <a name="next-steps"></a>Passos seguintes
Assim que a oferta fica em direto, teste os cenários de cliente para validar que todos os contratos e a funcionalidade funcionem corretamente no ambiente de produção como testar e validar no ambiente de teste.

## <a name="see-also"></a>Consultar também
* [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png
