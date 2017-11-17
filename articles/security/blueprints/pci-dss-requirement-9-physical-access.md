---
title: "Azure Blueprint de processamento de pagamento - requisitos de acesso físico"
description: Requisito de PCI DSS 9
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 91595a69-e9ce-4f9c-8388-10224165d9c0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 89f7b20a130e988bfe4964d50ae97de788ca4623
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="physical-access-requirements-for-pci-dss-compliant-environments"></a>Requisitos de acesso físico para ambientes em conformidade de PCI DSS 
## <a name="pci-dss-requirement-9"></a>Requisito de PCI DSS 9

**Restringir o acesso físico aos dados de cardholder**

> [!NOTE]
> Estes requisitos são definidos pelo [Council de normas de segurança da indústria de cartão de pagamento (PCI)](https://www.pcisecuritystandards.org/pci_security/) como parte do [PCI dados segurança Standard (DSS) versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre testes de procedimentos e as diretrizes para cada requisito.

Qualquer físico aceder a sistemas ou dados que cardholder próxima dados proporciona a oportunidade para indivíduos para aceder a dispositivos ou os dados e para remover os sistemas ou hardcopies e devem ser restrito adequadamente. Para efeitos de requisito 9, "trazidos técnico" refere-se os funcionários a tempo inteiro e part-time, funcionários temporários, subcontratados e consultores que estão fisicamente presentes no local da entidade. "Visitante" refere-se para um fornecedor, o convidado de qualquer técnico no local, serviço ou de trabalho qualquer pessoa que tem de introduzir a instalações durante um período pequeno, normalmente, não mais do que um dia. "Suporte de dados" refere-se a todos os documento e suportes de dados Eletrónicos que contêm dados cardholder.

## <a name="pci-dss-requirement-91"></a>Requisito de PCI DSS 9.1

**9.1** utilizar controlos de entrada de instalações adequado para o limite e o monitor de acesso físico aos sistemas no ambiente de dados de cardholder.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure é responsável por implementar, imposição e monitorização de segurança de acesso físico para os centros de dados. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Não aplicável.|



### <a name="pci-dss-requirement-911"></a>Requisito de PCI DSS 9.1.1

**9.1.1** utilizar câmaras de vídeo ou acesso controlo mecanismos (ou ambos) para monitorizar o individuais acesso físico a áreas confidenciais. Reveja os dados recolhidos e correlacionar com outras entradas. Armazenar para, pelo menos, três meses, a menos que caso contrário é restringido por lei.

> [!NOTE]
> "Áreas confidenciais" refere-se a qualquer centro de dados, sala de servidor ou qualquer área que aloja os sistemas que armazenarem, processam ou transmitem dados cardholder. Isto exclui áreas destinado ao público, onde apenas de ponto de venda terminals não estiverem presentes, tais como as áreas de cashier num arquivo de revenda.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure é responsável por implementar, imposição e CCTV e mecanismos de controlo de acesso de Biometria de centros de dados de monitorização. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Não aplicável.|



### <a name="pci-dss-requirement-912"></a>Requisito de PCI DSS 9.1.2

**9.1.2** implemente controlos de e/ou físicos lógicos para restringir o acesso ao jacks de rede acessível publicamente. 

Por exemplo, rede jacks localizadas no áreas públicas e áreas acessíveis para os visitantes podem ser desativadas e ativadas apenas quando o acesso à rede explicitamente está autorizado. Em alternativa, os processos ser implementados para garantir que visitantes são escorted em todas as horas nas áreas com jacks de rede do Active Directory.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não existem nenhum jacks de rede acessível publicamente dentro da plataforma Microsoft Azure. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Não aplicável.|



### <a name="pci-dss-requirement-913"></a>Requisito de PCI DSS 9.1.3

**9.1.3** restringir o acesso físico aos pontos de acesso sem fios, gateways, handheld dispositivos, o hardware de rede/comunicações e linhas de telecommunication.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Acesso físico ao Microsoft Azure hardware de rede é controlado rigorosamente por listas de acesso, várias formas de autenticação, físicas barreiras as eficazes para entrada e o requisito de negócio têm de ser aprovadas para aceder ao equipamento da. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Não aplicável.|



## <a name="pci-dss-requirement-92"></a>Requisito de PCI DSS 9.2

**9.2** desenvolver procedimentos facilmente distinguir entre técnico no local e os visitantes, para incluir:
- Identificar técnico no local e visitantes (por exemplo, atribuição destaques)
- Alterações para aceder aos requisitos
- Revogar ou terminar técnico no local e identificação de visitantes expirada (como ID destaques).

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure é responsável por implementar, imposição e monitorização identificação de segurança e dos funcionários ou contratantes acesso físico ao visitando centros de dados. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Não aplicável.|



## <a name="pci-dss-requirement-93"></a>Requisito de PCI DSS 9.3

**9.3** controle o acesso físico para o técnico no local para as áreas confidenciais da seguinte forma:
- Acesso tem de ser autorizado e com base na função de trabalho individuais.
- O acesso é revogado imediatamente após a terminação e todos os mecanismos de acesso físico, tais como chaves, cartões de acesso, etc., são devolvidos ou desativados.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Autorizações de acesso aos datacenters da Microsoft é controlado através de uma lista de autorizados acesso aprovada pela equipa do Centro de dados com base no princípio do menor privilégio. A lista de controlo de acesso é revista, verificar e atualizada trimestralmente.<br /><br />Centros de dados do Microsoft Azure utilizam dispositivos de acesso físico como portas de perímetro, leitores de distintivo acesso eletrónicas, leitores de Biometria, ataques man-traps/portais e passagem anti fazer uma cópia de dispositivos. Dispositivos de distintivo de acesso são monitorizados continuamente. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Não aplicável.|



## <a name="pci-dss-requirement-94"></a>Requisito de PCI DSS 9.4

**9.4** implementar procedimentos para identificar e autorizar visitantes. Procedimentos devem incluir o seguinte.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure é responsável para a imposição de entregas previamente aprovadas são recebidas na bay carregamento segura que esteja fisicamente isolada de instalações de processamento de informações e é monitorizado pelo empregados autorizados. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Não aplicável.|



### <a name="pci-dss-requirement-941"></a>Requisito de PCI DSS 9.4.1

**9.4.1** visitantes estiverem autorizados antes de entrar e escorted em todas as vezes em áreas onde os dados de cardholder são processados ou mantidos.


**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure é responsável para a imposição de entregas previamente aprovadas são recebidas na bay carregamento segura que esteja fisicamente isolada de instalações de processamento de informações e é monitorizado pelo empregados autorizados. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Não aplicável.|



### <a name="pci-dss-requirement-942"></a>Requisito de PCI DSS 9.4.2

**9.4.2** visitantes são identificados e dado um destaque ou outra identificação que expira e que distingue visibly os visitantes do técnico no local.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Acesso a dados do Microsoft System center tem de ser previamente aprovado e visitantes autorizados são necessários para dar entrada com a segurança física at the point of chegada e fornecer uma prova válida de ID antes de entrada. Destaques indicam claramente empregados. Subcontratados e visitantes recebem destaques temporárias que tem de ser surrendered após partidas da instalações. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Não aplicável.|



### <a name="pci-dss-requirement-943"></a>Requisito de PCI DSS 9.4.3

**9.4.3** visitantes-lhe pedidos para surrender o distintivo ou identificação antes de deixarem a instalações ou a data de expiração.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Visitantes são necessários para surrender destaques após partidas das qualquer instalação da Microsoft. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Não aplicável.|



### <a name="pci-dss-requirement-944"></a>Requisito de PCI DSS 9.4.4

**9.4.4** um registo de visitantes é utilizado para manter um registo de auditoria físico da atividade de visitantes para a instalação, bem como gabinetes de computador e centros de dados onde os dados de cardholder forem armazenados ou transmitidos.
Documente nome o visitante, firm representado e empregados trazidos autorizar o acesso físico no registo.
Manter este registo por um mínimo de três meses, a menos que caso contrário é restringido por lei.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure é responsável por manter um registo de visitantes como um registo de auditoria físico da atividade de visitantes para a instalação, bem como gabinetes de computador e centros de dados onde os dados de cardholder forem armazenados ou transmitidos. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Não aplicável.|



## <a name="pci-dss-requirement-95"></a>Requisito de PCI DSS 9.5

**9.5** Proteja fisicamente todos os suportes de dados.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore armazena todos os dados na base de dados do Azure SQL. Uma instância de base de dados de SQL de PaaS é utilizada para demonstramos medidas de segurança da base de dados. Para obter mais informações, consulte [orientações de PCI - SQL Database do Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-951"></a>Requisito de PCI DSS 9.5.1

**9.5.1** armazenar cópias de segurança do suporte de dados numa localização segura, preferencialmente, uma instalação fora das instalações, como um site alternativo ou cópia de segurança ou um local de armazenamento comercial. Reveja a segurança a localização, pelo menos, anual.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore armazena todos os dados na base de dados do Azure SQL. Uma instância de base de dados de SQL de PaaS é utilizada para demonstramos medidas de segurança da base de dados. Para obter mais informações, consulte [orientações de PCI - SQL Database do Azure](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-96"></a>Requisito de PCI DSS 9.6

**9.6** manter o controlo strict sobre a distribuição interno ou externo de qualquer tipo de suporte de dados, incluindo o seguinte.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore armazena todos os dados na base de dados do Azure SQL. Uma instância de base de dados de SQL de PaaS é utilizada para demonstramos medidas de segurança da base de dados. Para obter mais informações, consulte [orientações de PCI - SQL Database do Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-961"></a>Requisito de PCI DSS 9.6.1

**9.6.1** classificar o suporte de dados para a sensibilidade dos dados pode ser determinada.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore armazena todos os dados na base de dados do Azure SQL. Uma instância de base de dados de SQL de PaaS é utilizada para demonstramos medidas de segurança da base de dados. Para obter mais informações, consulte [orientações de PCI - SQL Database do Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-962"></a>Requisito de PCI DSS 9.6.2

**9.6.2** envie o suporte de dados por courier segura ou outro método de entrega que pode ser controlado com exatidão.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore armazena todos os dados na base de dados do Azure SQL. Uma instância de base de dados de SQL de PaaS é utilizada para demonstramos medidas de segurança da base de dados. Para obter mais informações, consulte [orientações de PCI - SQL Database do Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-963"></a>Requisito de PCI DSS 9.6.3

**9.6.3** Certifique-se de gestão aprova todos os suportes de dados, que é movido de uma área protegida (incluindo quando o suporte de dados é distribuído para utilizadores autónomos).

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore armazena todos os dados na base de dados do Azure SQL. Uma instância de base de dados de SQL de PaaS é utilizada para demonstramos medidas de segurança da base de dados. Para obter mais informações, consulte [orientações de PCI - SQL Database do Azure](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-97"></a>Requisito de PCI DSS 9.7

**9.7** manter strict controlo sobre o armazenamento e a acessibilidade do suporte de dados.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore armazena todos os dados na base de dados do Azure SQL. Uma instância de base de dados de SQL de PaaS é utilizada para demonstramos medidas de segurança da base de dados. Para obter mais informações, consulte [orientações de PCI - SQL Database do Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-971"></a>Requisito de PCI DSS 9.7.1

**9.7.1** corretamente manter registos de inventário de todos os suportes de dados e realize inventários de suporte de dados, pelo menos, anual.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore armazena todos os dados na base de dados do Azure SQL. Uma instância de base de dados de SQL de PaaS é utilizada para demonstramos medidas de segurança da base de dados. Para obter mais informações, consulte [orientações de PCI - SQL Database do Azure](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-98"></a>Requisito de PCI DSS 9.8

**9.8** destruir o suporte de dados quando este já não é necessário para a empresa ou por motivos legais da seguinte forma.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore armazena todos os dados na base de dados do Azure SQL. Uma instância de base de dados de SQL de PaaS é utilizada para demonstramos medidas de segurança da base de dados. Para obter mais informações, consulte [orientações de PCI - SQL Database do Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-981"></a>Requisito de PCI DSS 9.8.1

**9.8.1** shred, incinerate ou pulp materiais de cópia de disco rígido para que não é possível ter é possível reconstruir dados cardholder. Proteja os contentores de armazenamento utilizados para os materiais que estão a ser destruída.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore armazena todos os dados na base de dados do Azure SQL. Uma instância de base de dados de SQL de PaaS é utilizada para demonstramos medidas de segurança da base de dados. Para obter mais informações, consulte [orientações de PCI - SQL Database do Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-982"></a>Requisito de PCI DSS 9.8.2

**9.8.2** compor dados cardholder no suporte de dados Eletrónicos irrecuperável para que não é possível ter é possível reconstruir dados cardholder.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | As técnicas de destruição de dados variam consoante o tipo do objeto de dados que está a ser destruído, se, ser subscrições, armazenamento, máquinas virtuais ou bases de dados. No ambiente de multi-inquilino do Microsoft Azure, cuidado atenção é direcionada para Certifique-se de dados do que um cliente não tem permissão para "fuga" para dados de outro cliente ou quando um cliente elimina dados, nenhum outro cliente (incluindo, na maioria dos casos, o cliente uma vez que os dados pertencentes) podem ter acesso a dados eliminados.<br /><br />Microsoft Azure segue NIST 800-88 diretrizes no Sanitização de suporte de dados, a principal preocupação de garantir que os dados de endereço não é libertado inadvertidamente. Estas diretrizes abranger sanitização eletrónica e física. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore podem ser eliminado inteiramente ao eliminar o grupo de recursos utilizados durante a implementação.|



## <a name="pci-dss-requirement-99"></a>Requisito de PCI DSS 9.9

**9.9** proteger os dispositivos que captura de dados do cartão de pagamento através de interação físico direta com o cartão contra adulteração e de substituição.

> [!NOTE]
> Estes requisitos se aplicam aos dispositivos de leitura do cartão utilizados em transações de cartão presente (ou seja, percorra a cartão ou dip) at the point of venda. Este requisito não se destina a aplicar aos componentes de entrada de chave manuais como teclados de computador e POS keypads. 

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza OMS para registar todas as alterações de sistema.<br /><br />[Operations Management Suite (OMS)](/azure/operations-management-suite/) fornece registos um vasto conjunto de alterações. As alterações podem ser revistas e podem ser verificadas em termos de exatidão. Para obter instruções mais específicas, consulte [orientações de PCI - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-991"></a>Requisito de PCI DSS 9.9.1

**9.9.1** manter uma lista atualizada de dispositivos. A lista deve incluir o seguinte:
- Marca, o modelo do dispositivo
- Localização do dispositivo (por exemplo, o endereço do site ou das instalações onde está localizado o dispositivo)
- Número de série do dispositivo ou outro método de identificação exclusivo

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore fornece uma arquitetura de referência e uma lista de todos os serviços utilizados na respetiva documentação de implementação.|



### <a name="pci-dss-requirement-992"></a>Requisito de PCI DSS 9.9.2

**9.9.2** periodicamente inspecionar analisa de dispositivo para detetar a adulteração (por exemplo, a adição de skimmers cartão dispositivos) ou substituição (por exemplo, ao verificar o número de série ou outro dispositivo características para verificar tem não foram alternados com um dispositivo fraudulento).

> [!NOTE]
> Sinais que um dispositivo pode foram adulterado ou substituído exemplos de anexos inesperados ou os cabos ligados para o dispositivo, etiquetas de segurança em falta ou foi alterada, interrompida ou de forma diferente coloridas tem maiúsculas e minúsculas ou alterações ao número de série ou outros visuais externos.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Não aplicável.|



### <a name="pci-dss-requirement-993"></a>Requisito de PCI DSS 9.9.3

**9.9.3** fornecer formação para técnico a ter em consideração a adulteração tentada ou substituição de dispositivos. Formação deve incluir o seguinte:
- Verificar a identidade de pessoas quaisquer terceiros reclamação seja técnico reparação ou manutenção, antes de conceder-lhes acesso para modificar ou resolver problemas de dispositivos.
- Não instalar, substituir ou devolver dispositivos sem verificação.
- Tenha em atenção de comportamento suspeito à volta de dispositivos (por exemplo, as tentativas de pessoas que desconhecido para desligue ou abra dispositivos).
- Relatório de comportamento suspeito e indicações de adulteração de dispositivo ou substituição ao pessoal adequado (por exemplo, para um responsável manager ou segurança).

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Não aplicável.|



## <a name="pci-dss-requirement-910"></a>Requisito de PCI DSS 9.10

**9.10** Certifique-se de que as políticas de segurança e de procedimentos operacionais para restringir o acesso físico aos dados cardholder estão documentados em utilização e conhecidos para todas as partes afetadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Não aplicável.|




