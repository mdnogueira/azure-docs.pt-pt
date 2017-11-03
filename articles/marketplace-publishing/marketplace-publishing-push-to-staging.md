---
title: "Preparar e testar a sua oferta para a implementação no Azure Marketplace | Microsoft Docs"
description: "Instruções detalhadas sobre fornecer conteúdo de marketing, configurar planos de preços e testar a sua oferta antes de implementar no Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 3ccd2448-895b-477e-adf6-ab655a21d2fa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/17/2016
ms.author: hascipio
ms.openlocfilehash: 7db86716cdf8f9eb921c3c1813970acae7a3016b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="complete-the-offer-creation-with-marketing-content"></a>Conclua a criação de oferta com conteúdo de marketing
Neste passo do processo de publicação, terá de fornecer determinados conteúdo de marketing e detalhes sobre a sua oferta e/ou SKUs no Azure Marketplace. Por exemplo, irá fornecer uma descrição do seu produto, logótipos da empresa, planos de preços, os detalhes dos planos e outras informações necessárias para enviar a sua oferta e/ou SKU para teste. Estas informações são utilizadas como conteúdo marketing no portal do Azure. Irá iniciar este processo no [portal publicação][link-pubportal].

## <a name="step-1-provide-marketplace-marketing-content"></a>Passo 1: Fornecer conteúdo de marketing do Marketplace
**O inglês é o predefinido e idioma suportado apenas.** Certifique-se de que todas as informações fornecidas nos campos estão em inglês. Todas as informações podem ser editadas em qualquer altura até emitir para a fase de testes.

1. Aceda ao portal de publicação, [https://publish.windowsazure.com](https://publish.windowsazure.com).
2. No menu da esquerda, clique em de **Marketing** separador.
3. No painel principal, clique em de **inglês (EUA)** botão.
   
   > [!IMPORTANT]
   > Todos os campos têm de ter entradas, incluindo as imagens, para que possa enviar para o teste.
   > 
   > 

### <a name="details-and-plans"></a>Os detalhes e planos
1. Introduza o título da oferta (máximo de 50 carateres), oferecem resumo (máximo de 100 carateres), oferecem resumo longo (máximo, 256 carateres), oferecem descrição (máximo 1300 carateres), logótipos sob o **detalhes** separador
2. Introduza o título do plano (máximos 50 carateres), o resumo de plano (máximo de 100 carateres), planeie descrição (máximo de 2000 de carateres) sob o **planos** separador.
   
   > [!NOTE]
   > Pode utilizar as seguintes tags de HTML para formatar o resumo; longa resumo e descrição da oferta e planos. As etiquetas HTML permitidas são h1 h2, h3, h4, h5, p, ol, ul, li, uma [destino | href], segura, IT, o b, posso.
   > 
   > 
3. Não introduza texto duplicado em oferta e planear a descrição.
4. Não introduza texto duplicado sob o título do plano e a oferta longa resumo.
5. Não introduza texto duplicado sob o título de plano e a oferta de resumo.
6. Não introduza títulos de plano idênticas para uma oferta com vários esquemas.
7. Carregue as imagens das especificações necessárias (mencionadas no Portal de publicação) no formato PNG, um para cada tamanho.
8. Certifique-se de que o logótipos seguem as diretrizes de logótipo do Azure Marketplace mencionadas abaixo.
   
   ![desenho](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-02.png)

**Diretrizes de logótipo do Azure Marketplace**

Todos os logótipos carregados no Portal de publicação devem seguir o abaixo diretrizes:

* A estrutura do Azure tem uma paleta de cores simples. Mantenha o número de site primário e secundário cores o logótipo baixa.
* As cores de tema do portal do Azure estão em brancos e marcar a negrito. Por conseguinte, evite utilizar estes cores como a cor de fundo da sua logótipos. Utilize algumas cor que iria tornar o seu logótipos prominent no portal do Azure. Recomendamos simples de principal de cores. **Se estiver a utilizar o fundo transparente, em seguida, certifique-se de que o texto/logótipos não são branco ou preto ou azul.**
* Não utilize um fundo gradação o logótipo.
* Evite a colocação de texto, mesmo sua empresa ou o nome da marca, no logótipo. O aspeto e funcionalidade do logótipo deve ser 'simples' e deve evitar gradações.
* Não deve ser esticado o logótipo.
* Logótipo pequeno deve ser de tamanho 40 X 40 px
* Logótipo médio deve ser de tamanho 90 X 90 px
* Logótipo grande deve ser de tamanho 115 X 115 px
* Logótipo Wide deve ser de tamanho 255 X 115 px
* Logótipo de heroína deve ser de tamanho 815 X 290 px

> [!NOTE]
> O logótipo heroína é opcional. O fabricante pode optar por não carregar um logótipo de heroína. No entanto uma vez carregado o ícone de heroína não pode ser eliminado da publicação portal. Nessa altura, o parceiro tem de seguir as diretrizes de Azure Marketplace para ícones heroína.
> 
> 

  ![desenho](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-03.png)

**Diretrizes adicionais para o ícone de logótipo heroína (opcional)**

* O logótipo heroína é opcional. O fabricante pode optar por não carregar um logótipo de heroína. **No entanto uma vez carregado o ícone de heroína não pode ser eliminado da publicação portal. Nessa altura, o parceiro tem de seguir as diretrizes de Azure Marketplace para os ícones heroína pessoa que não irá ser aprovada a oferta para produção.**
* O nome de apresentação do publicador, o título do plano e a oferta longa resumo são apresentados na cor branco do tipo de letra. Por conseguinte, deve evitar manter quaisquer cor leve em segundo plano do ícone heroína. Negra, fundo branco e transparente não é permitido para ícones heroína.
* O nome de apresentação do publicador, plano título, a oferta longa resumo e no botão Criar estão incorporados programaticamente dentro o logótipo heroína assim que a oferta fica listados. Por isso, não deve introduzir qualquer texto enquanto está a conceber o logótipo heroína. Basta deixe espaço em branco à direita, porque o texto (ou seja, publicador nome a apresentar, plano título, a oferta longa resumo) será incluído programaticamente por-nos através do mesmo. O espaço em branco para o texto deve ser 415 x 100 à direita (e é de deslocamento por 370px da esquerda).
  
  ![desenho](media/marketplace-publishing-push-to-staging/pubportal-herobanner.png)

### <a name="links"></a>Ligações
No **ligações** separador na barra da esquerda, introduza quaisquer ligações com as informações que podem ajudar os clientes. Introduza um nome e um URL para cada ligação.

![desenho](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-link-01.png)

### <a name="sample-images-optional"></a>Imagens de exemplo (opcionais)
> [!NOTE]
> Incluindo uma imagem de exemplo passo é opcional.
> Apesar de pode carregar várias imagens de exemplo a publicação portal, apenas uma imagem (selecionada aleatoriamente pelo sistema) obtém apresentada no portal do Azure. Por este motivo, recomendamos que carregar no máximo, uma imagem de exemplo.
> 
> 

No **imagens de exemplo** separador no menu da esquerda, carregue uma nova imagem clicando **carregue uma nova imagem**. Se tiver uma imagem existente e pretende substituí-lo, clique em **Substituir imagem**.

![desenho](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-sampleimg-01.png)

### <a name="legal"></a>Informações Legais
No **legais** separador, forneça uma ligação para as políticas de termos de utilização. Introduza ou cole os termos de grande **os termos de utilização** caixa. O limite de carateres para os termos legais de utilização é 1.000.000 carateres.

![desenho](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-legal-01.png)

**Nota:** para ofertas de Máquina Virtual, quando uma oferta/SKU é testado no Portal do Azure, uma vez que não é possível alterar os campos indicados abaixo:

* **Identificador de oferta:** [portal de publicação -> máquinas virtuais -> a sua oferta -> imagens da VM do separador -> oferecem identificador]
* **Identificador SKU:** [portal de publicação -> máquinas virtuais -> selecione a sua oferta -> SKUs separador -> Adicionar um SKU]
* **Espaço de nomes do publicador:** [portal de publicação -> máquinas virtuais -> instruções separador -> informe-nos sobre a sua empresa (localizado em "Passo 2 registar a sua empresa") -> espaço de nomes do publicador-espaço de nomes >]

Para ofertas de Máquina Virtual, assim que a oferta/SKU está listado no Azure Marketplace, não é possível alterar os campos indicados abaixo:

* **Identificador de oferta:** [portal de publicação -> máquinas virtuais -> selecione a sua oferta -> imagens de VM -> oferecem identificador]
* **Identificador SKU:** [portal de publicação -> máquinas virtuais -> selecione a sua oferta -> SKUs separador -> Adicionar um SKU]
* **Espaço de nomes do publicador:** [portal de publicação -> máquinas virtuais -> instruções separador -> espaço de nomes de publicador de informe-nos sobre sua empresa (localizado em passo 2 registar) -> espaço de nomes]
* **Portas:** [portal de publicação -> máquinas virtuais -> a sua oferta -> imagens da VM do separador -> abra portas]
* **Alteração de preço de SKU(s) listadas**
* **Alteração de modelo de SKU(s) listadas de faturação**
* **Remoção de faturação regiões de SKU(s) listadas**
* **Alterar a contagem de discos de dados de SKU(s) listadas**

## <a name="step-2-set-your-prices"></a>Passo 2: Definir os seus preços
### <a name="pricing-models"></a>Modelos de preços
| Modelo de determinação de preço | Descrição |
| --- | --- |
| Base |Taxa mensal flat paga momento da compra; Por exemplo, $10/ mês. |
| Consumo (a.k.a. medição de utilização) |Paga por utilização, que é definida pelo fabricante da oferta. Excedido não pode ser definido por posto de trabalho, por utilizador, etc., porque não há nenhum conceito de uma fração de um utilizador ou a capacidade de fazer proration. Utilização é comunicada pelo parceiro de cada hora. Cliente pays no do ciclo de faturação mensal, por oposição adiantado like planos mensais. |
| Avaliação gratuita |Cliente pode utilizar gratuitamente, por um período de tempo limitado e, em seguida, após a pagar taxas normais. |
| Escalão gratuito |Plano é sempre livre. |
| Migração (a.k.a. conversão ou atualização/mudança para versão anterior) de plano |Conceito de um utilizador mover do seu plano atual para outro plano aceitável; definido pelo parceiro. |

**Os modelos disponíveis por tipo de oferta de preços**

> [!IMPORTANT]
> Disponibilidade de determinados modelos de preços variam consoante o tipo de oferta. Consulte a tabela abaixo.
> 
> 

|  | Apenas base | Consumo apenas | Base + consumo |
| --- | --- | --- | --- |
| Imagem de máquina virtual |Não |Sim |Não |
| Serviço do Programador |Sim |Sim |Sim |

### <a name="21-set-your-vm-prices"></a>2.1. Definir os preços VM
Atualmente para máquinas virtuais, temos o seguinte **3 tipos de modelos de faturação:**

* **Hora a hora:** aos clientes obterem cobrados numa base por hora com base nas tarifas definidas por editores nos tamanhos VM. Em case de **hora a hora de faturação** modelo SKUs, o preço total estará summation o custo de software-lhe cobrados pelo fabricante e o custo da infraestrutura cobradas pela Microsoft. Este custo total será apresentado ao cliente como um preço por hora e mensal quando estiver a considerar a compra (consulte a captura de ecrã abaixo). **Publicador recebe 80% do custo de software cobrado por-los.** Por conseguinte, volte a disponibilizar o cálculo em conformidade antes definição preços para os SKUs.
  
    ![desenho](media/marketplace-publishing-push-to-staging/img2.1-01.png)
* **Avaliação gratuita:** é outra versão do modelo de hora a hora. Aqui, o cliente não obter cobrado custo de software para os 30 primeiros days(Free) depois de implementar a VM. Depois de 30days obter cobrados numa base por hora com base nas tarifas definidas por editores no modelo de hora a hora.
* **Bring-Your-proprietário-licença (BYOL):** os editores gerir o licenciamento do software em execução na VM.

**Importante:** assim que a oferta/SKU está listado no Azure Marketplace, não é possível alterar os campos indicados abaixo.

* **Alteração de preço de SKU(s) listadas**
* **Alteração de modelo de SKU(s) listadas de faturação**
* **Remoção de faturação regiões de SKU(s) listadas**
* **Alterar a contagem de discos de dados de SKU(s) listadas**
* **Identificador de oferta:** [portal de publicação -> máquinas virtuais -> selecione a sua oferta -> imagens de VM -> oferecem identificador]
* **Identificador SKU:** [portal de publicação -> máquinas virtuais -> selecione a sua oferta -> SKUs separador -> Adicionar um SKU]
* **Espaço de nomes do publicador:** [portal de publicação -> máquinas virtuais -> instruções separador -> espaço de nomes de publicador de informe-nos sobre sua empresa (localizado em passo 2 registar) -> espaço de nomes]
* **Portas:** [portal de publicação -> máquinas virtuais -> a sua oferta -> imagens da VM do separador -> abra portas]

### <a name="sell-to-countries-of-the-sku"></a>Países "Vende para" do SKU
Precisa de considerar atentamente onde efetuar os SKUs disponíveis. Alguns países/regiões estão classificados como "Microsoft remit" e outros estão classificados como "Remit de ISV."

* Países "Microsoft remit", a Microsoft recolhe taxas de clientes e pays taxas (remits) para o government.
* "ISV remit" países, parceiros são responsáveis por clientes de taxas de recolher e pagar taxas para o government. Se optar por propor países "ISV remit", tem de ter a capacidade de calcular e pagar taxas países que selecionar.

> [!NOTE]
> O SKU não estarão disponível na países/regiões, a menos que definiu os seus preços [publicação portal](https://publish.windowsazure.com). Orientações para obter, definir o preço de hora a hora e BYOL SKUs são indicadas abaixo.
> 
> 

### <a name="211-how-to-setup-hourly-pricing-model-for-a-sku"></a>2.1.1 como configurar o modelo de preços por hora para um SKU
Siga os passos indicados abaixo para o modelo de preços hora a hora para um SKU do programa de configuração:

1. Início de sessão para o [publicação portal](https://publish.windowsazure.com).
2. Navegue para o **máquinas virtuais** separador e selecione a sua oferta.
3. No menu do lado esquerdo, clique em de **SKUS** separador.
4. Certifique-se de que o SKU não está marcada como "Modelo de faturação por hora". Se não, em seguida, clique em de **editar** botão para reverter o modelo de faturação. Irá abrir uma janela. Desmarque a caixa de verificação 'faturação e licenciamento é feito externamente a partir do Azure (também conhecido como traga a sua própria licença)' e guarde as alterações.
5. Se pretender ativar a avaliação gratuita para a primeira 30days da implementação do SKU, em seguida, selecione a opção "Um mês" para a pergunta "É uma versão de avaliação gratuita disponível?" Caso contrário, selecione a opção "Não avaliação". Agora, siga os passos indicados abaixo.
6. No menu do lado esquerdo, clique em de **preços** separador.
7. Selecione a região de base.
   
   ![desenho](media/marketplace-publishing-push-to-staging/img2.1.1_07.png)
8. Defina os preços para todos os núcleos. **Tem de fornecer preços para todos os núcleos de um SKU, mesmo que o SKU não o suporta.**
   
    ![desenho](media/marketplace-publishing-push-to-staging/img2.1.1_08.png)
9. Defina os preços para as outras regiões manualmente ou pode utilizar o assistente AUTOPRICE para definir os preços de outras regiões, com base na região de base. Para utilizar o AUTOPRICE assistente, clique no botão **AUTOPRICE outras mercados com base nos preços em Unidas Estados.** **Nota:** etiqueta do botão poderão ser diferente consoante a região que selecionou. Uma vez que vamos selecionou dos Estados Unidos ao criar este documento, por isso, o botão assinalada como como "Automática de preços outros mercados com base nos preços nos Estados Unidos" na captura de ecrã abaixo.
   
   ![desenho](media/marketplace-publishing-push-to-staging/img2.1.1_09.png)
10. O Assistente de preços automática será aberto. A primeira página exibe a seleção de mercado base. Se a secção e mover para a página seguinte, clicando no botão "->".
    
    ![desenho](media/marketplace-publishing-push-to-staging/img2.1.1_10.png)
11. Opção para selecionar os núcleos e planos será apresentada na página de 2. Selecione os planos pretendidos e clique em "-a >" do botão. Clique em de **todos os ativar/desativar** botão para selecionar todos os o **planos de serviço** e **medidores** ou possa verificar manualmente as caixas de verificação. **Tem de fornecer preços para todos os núcleos de um SKU, mesmo que o SKU não o suporta.** Por conseguinte, certifique-se de que todos os tamanhos de núcleos estão selecionados.
    
    ![desenho](media/marketplace-publishing-push-to-staging/img2.1.1_11.png)
12. Página 3 apresenta mercados/regiões. Clique em de **todos os ativar/desativar** botão para selecionar todas as regiões ou manualmente, marque as caixas de região. Clique no botão "->" mover para a página seguinte. **Nota:** Microsoft dedução dos impostos Remitted países estão em falta por que um próxima como símbolo. Para obter mais detalhes, consulte a secção "Vende para" países/regiões do SKU desta página.
    
    ![desenho](media/marketplace-publishing-push-to-staging/img2.1.1_12.png)
13. Página 4 apresenta as taxas de exchange. Clique no botão Concluir para concluir os passos.

### <a name="212-how-to-setup-byol-pricing-model-for-a-sku"></a>2.1.2 como modelo de preços BYOL do programa de configuração para um SKU
Siga os passos indicados abaixo para configurar o modelo de preços para um SKU de BYOL:

1. Início de sessão para o [publicação portal](https://publish.windowsazure.com).
2. Navegue para o **máquinas virtuais** separador e selecione a sua oferta.
3. No menu do lado esquerdo, clique em de **SKUS** separador.
4. Certifique-se de que o SKU não está marcada como "Traga a sua própria licença SKU". Caso contrário, em seguida, clique no botão Editar para reverter o modelo de faturação. Irá abrir uma janela. Selecione a caixa de verificação 'faturação e licenciamento é feito externamente a partir do Azure (também conhecido como traga a sua própria licença)' e guarde as alterações.
   
   ![desenho](media/marketplace-publishing-push-to-staging/img2.1.2_04.png)
5. No menu do lado esquerdo, clique em de **preços** separador.
6. Selecione a região de base e disponibilizá-SKU na região marcando a caixa de verificação contra o SKU na secção de disponibilidade de SKU EXTERNALLY-LICENSED (BYOL) (consulte a captura de ecrã abaixo).
   
   ![desenho](media/marketplace-publishing-push-to-staging/img2.1.2_06.png)
7. Disponibilizar o SKU de noutras regiões manualmente ou pode utilizar o assistente AUTOPRICE para esta finalidade. Consulte os pontos de #9 a #13 (o qual explica a utilização do assistente AUTOPRICE) na secção **"2.1.1 como configurar a hora a hora modelo de preços para um SKU"** desta página.

### <a name="22-set-your-developer-service-prices"></a>2.2. Definir os preços de serviço do Programador
Planos podem ser qualquer combinação de base + consumo, onde a base é o preço mensal e excedidas é os preços de pagamento por utilização. (Consulte abaixo para obter mais detalhes).

**Exemplo:** oferta de serviço do Programador de Contoso

| Planear | Preço | Inclui | Caminho de migração |
| --- | --- | --- | --- |
| Gratuito |$0/ mês |Funcionalidade básica. |Pode migrar para qualquer outro plano |
| Bronze |$10/ mês |Funcionalidades básicas e uma quota de 1.000 da funcionalidade X. |Pode migrar para planos de Bronze Plus, prata e Gold |
| Bronze Plus |Período de avaliação gratuita: $0/ mês + $0/meter01 |Funcionalidades básicas e uma quota de 10.000 de funcionalidade X.  Assim que a quota de funcionalidade X é utilizada, o cliente pode paga por utilização através de meter01. |Pode migrar para planos Silver Plus e Gold |
| Bronze Plus |Paga período (a.k.a. avaliação gratuita expirou): $10/mês + $ 0,05/meter01 |Funcionalidades básicas e uma quota de 10.000 de funcionalidade X.  Assim que a quota de funcionalidade X é utilizada, o cliente pode paga por utilização através de meter01. |Pode migrar para planos Silver Plus e Gold |
| Silver |$0.15/meter01 |O cliente pode paga por utilização através de meter01, que se destine a funcionalidade de X. |Pode migrar para planos de Bronze e Gold |
| Prata Plus |$20/ mês + $0.15/meter01 + $0.01/meter02 |Funcionalidades básicas e uma quota de 10 000 da funcionalidade X e 100 da funcionalidade Y.  Assim que a quota de funcionalidade X é utilizada, o cliente pode paga por utilização através de meter01.  Assim que a quota de funcionalidade Y é utilizada, o cliente pode paga por utilização através de meter02. |Pode migrar para planos de Bronze Plus e Gold |
| Gold |$ 1000/mês |Quota de 10.000 de funcionalidade X, 1000 da funcionalidade Y e ilimitado da funcionalidade Z. |Pode migrar para todos os planos exceto livre |

## <a name="step-3-provide-support-information"></a>Passo 3: Fornecer suporte de informações
Os detalhes de contactos são utilizados para internas as comunicações entre o parceiro e a Microsoft apenas. O URL de suporte estará disponível para clientes finais.

1. Vá para o **suporte** cabeçalho no lado esquerdo do portal de publicação.
2. Introduza informações em **contacte engenharia**.
3. Introduza informações em **suporte ao cliente**. Se fornecer apenas o suporte de e-mail, introduza um número de telefone fictício e o endereço de e-mail será utilizada.
4. Introduza o URL de suporte.

## <a name="step-4-choose-azure-marketplace-categories"></a>Passo 4: Escolher categorias do Azure Marketplace
O **categorias** separador fornece uma matriz de seleções. A oferta pode abrangidos por estes e pode selecionar até cinco categorias.

## <a name="how-your-marketing-will-appear"></a>Como será apresentado o marketing
Segue-se uma vista detalhada de como é utilizada a oferta de marketing informações no [Web site do Azure Marketplace](https://azure.microsoft.com/marketplace/) e no [portal do Azure](https://portal.azure.com).

### <a name="azure-marketplace-website"></a>Site do Azure Marketplace
![desenho](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![desenho](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Lista de ofertas no Web site do Azure Marketplace*

![desenho](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Detalhes de descrição de oferta no Web site do Azure Marketplace*

![desenho](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Oferecer descrição detalhes de preços no Web site do Azure Marketplace*

### <a name="azure-portal"></a>Portal do Azure
![desenho](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-01.png)

*Lista de ofertas no Portal do Azure*

![desenho](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-02.png)

*Detalhes de descrição de oferta no portal do Azure*

## <a name="next-steps"></a>Passos seguintes
Agora que o conteúdo de Marketplace estiver carregado, vamos avançar com a testar a sua oferta no modo de transição. No entanto, tem de selecionar o tipo de oferta adequado na lista abaixo, como os passos variam consoante o tipo de oferta.

* [Testar a sua oferta do VM no modo de transição](marketplace-publishing-vm-image-test-in-staging.md)
* [Testar a sua oferta do modelo de solução no modo de transição](marketplace-publishing-solution-template-test-in-staging.md)

## <a name="see-also"></a>Consultar também
* [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]:media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]:media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]:media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]:media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]:media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]:https://publish.windowsazure.com
[link-push-to-production]:marketplace-publishing-push-to-production.md
