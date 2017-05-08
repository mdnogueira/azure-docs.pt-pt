---
title: "A Encriptação no Azure Data Lake Store | Microsoft Docs"
description: "Compreender como funciona a encriptação e a rotação de chaves no Azure Data Lake Store"
services: data-lake-store
documentationcenter: 
author: yagupta
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 4/14/2017
ms.author: yagupta
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 9bd9996a4a22b2f57510b6f3b6625e22b3183b1c
ms.contentlocale: pt-pt
ms.lasthandoff: 04/29/2017

---

# <a name="encryption-of-data-in-azure-data-lake-store"></a>Encriptação de Dados no Azure Data Lake Store

## <a name="overview-of-encryption-in-azure-data-lake-store"></a>Descrição Geral da Encriptação no Azure Data Lake Store

A Encriptação no Azure Data Lake Store (ADLS) proporciona-lhe a capacidade de proteger os seus dados, implementar políticas de segurança da empresa e cumprir os requisitos de conformidade normativa. Este artigo disponibiliza-lhe uma descrição geral do design e debate aspetos técnicos de como o Data Lake Store implementa a encriptação de dados.

O ADLS suporta a encriptação de dados inativos ativada por predefinição e transparente. Eis o que cada um destes termos significa em maior detalhe:

* Ativada por predefinição: quando cria uma conta nova do Azure Data Lake Store, a predefinição ativa a encriptação. Por esse motivo, os dados que são armazenados no Data Lake Store são sempre encriptados antes de serem armazenados em suportes de dados persistentes. Este é o comportamento para todos os dados e não pode ser alterado depois de as contas serem criadas.
* Transparente: o ADLS encripta automaticamente os dados antes de persistir e desencripta-os antes da obtenção. A encriptação é configurada e gerida ao nível do Data Lake Store por um administrador. Não são feitas modificações às APIs de acesso aos dados, pelo que não é preciso alterar as aplicações e os serviços que interagem com o Data Lake Store devido à encriptação.

Os dados em trânsito (também denominados dados em movimento) também são sempre encriptados no Data Lake Store. Para além de encriptar os dados antes de serem armazenados em suportes de dados persistentes, os dados também são sempre protegidos em trânsito ou em movimento com HTTPS (HTTP através de Secure Sockets Layer). O HTTPS é o único protocolo que as interfaces REST do Data Lake Store suportam.

![Figura 1](./media/data-lake-store-encryption/fig1.png)


## <a name="setting-up-encryption-with-azure-data-lake-store"></a>Configurar a Encriptação com Azure Data Lake Store

A Encriptação no Azure Data Lake Store é configurada durante a criação da conta e está sempre ativada por predefinição. Os clientes têm a opção de gerir as chaves ou permitir que o Azure Data Lake Store (predefinição) as gira por eles.

Para saber como configurar a Encriptação com o Azure Data Lake Store, veja [Introdução](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)

## <a name="under-the-hood--how-encryption-works-in-azure-data-lake-store"></a>Nos Bastidores - Como funciona a Encriptação no Azure Data Lake Store

### <a name="master-encryption-keys"></a>Chaves de Encriptação Mestras

O Azure Data Lake Store disponibiliza dois modos para gerir as chaves de encriptação mestras (Master Encryption Keys, MEKs). A utilização das chaves de encriptação mestras é explicada com maior detalhe mais adiante. Por agora, vamos supor que a chave de encriptação mestra é a chave de nível superior. O acesso à chave de encriptação mestra é necessário para desencriptar os dados que estejam armazenados no Data Lake Store.

Os dois modos para gerir a chave de encriptação mestra são os seguintes:

1.    Chaves Geridas por Serviços
2.    Chaves Geridas pelo Cliente

Em ambos os modos, a chave de encriptação é protegida mediante armazenamento no Azure Key Vault. O Azure Key Vault é um serviço totalmente gerido e altamente seguro do Azure, que pode ser utilizado para salvaguardar chaves criptográficas. Pode ler mais sobre o Azure Key Vault [aqui](https://azure.microsoft.com/services/key-vault)

Segue-se uma breve comparação das capacidades proporcionadas pelos dois modos de gestão de MEKs.

|  | Chaves Geridas por Serviços | Chaves Geridas pelo Cliente |
| --- | --- | --- |
|Como são armazenados os dados?|São sempre encriptados antes de serem armazenados|São sempre encriptados antes de serem armazenados|
|Onde É Armazenada a Chave de Encriptação Mestra?|Azure Key Vault|Azure Key Vault|
|As chaves de encriptação são armazenadas de forma desprotegida, fora do Azure Key Vault?|Não|Não|
|É possível obter a MEK a partir do Azure Key Vault?|Não. Uma vez armazenada no cofre de chaves, só pode ser utilizada para encriptação e desencriptação.|Não. Uma vez armazenada no cofre de chaves, só pode ser utilizada para encriptação e desencriptação.|
|Quem é o proprietário do Azure Key Vault e da MEK?|Serviço Azure Data Lake Store.|O cliente é o proprietário do Azure Key Vault, que pertence à respetiva subscrição do Azure. A MEK no cofre de chaves pode ser gerida por hardware (HSM) ou software.|
|O cliente pode revogar o acesso à MEK ao serviço Azure Data Lake Store?|Não|Sim. Podem gerir listas de controlo de acesso no Azure Key Vault e remover entradas do controlo de acesso para a identidade de serviço no âmbito do serviço Azure Data Lake Store.|
|O cliente pode eliminar permanentemente a MEK?|Não|Sim. Se o cliente eliminar a MEK no Azure Key Vault, os dados na conta do ADLS não podem ser desencriptados por ninguém, incluindo pelo serviço Azure Data Lake Store. <br><br> Se o cliente tiver criado explicitamente uma cópia de segurança da MEK antes de a eliminar no Azure Key Vault, esta pode ser restaurada e os dados recuperados. No entanto, se o cliente não tiver feito uma cópia de segurança antes de eliminar a MEK no Azure Key Vault, não será nunca possível desencriptar posteriormente os dados na conta do ADLS.|


Para além da principal diferença, ou seja quem gere a MEK e o Key Vault onde esta reside, o resto do design é igual em ambos os modos.

Existem alguns aspetos importantes a ter em conta relacionados com a escolha do modo para as chaves de encriptação mestra.

1.    Pode optar por utilizar chaves geridas pelo cliente ou pelo ADLS, quando aprovisiona uma conta do ADLS.
2.    O modo não pode ser alterado depois de aprovisionada a conta do ADLS.

### <a name="encryption-and-decryption-of-data"></a>Encriptação e Desencriptação de Dados

São utilizados três tipos de chaves no design da encriptação de dados do Azure Data Lake. A tabela seguinte resume a forma como estes desempenham os seus papéis:

| Chave                   | Abreviatura | Associada a | Localização do Armazenamento                             | Tipo       | Notas                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Chave de Encriptação Mestra | MEK          | Uma conta do ADLS | Azure Key Vault                              | Assimétrica | Pode ser gerida pelo ADLS ou pelo Cliente                                                              |
| Chave de Encriptação de Dados   | DEK          | Uma conta do ADLS | Armazenamento persistente - gerido pelo serviço ADLS | Simétrica  | Encriptada pela MEK, é a DEK encriptada que é armazenada no suporte de dados persistente |
| Chave de Encriptação de Blocos  | BEK          | Um bloco de dados | Nenhuma                                         | Simétrica  | A BEK é obtida a partir da DEK e do bloco de dados                                                      |

O diagrama seguinte ilustra estes conceitos:

![Figura 2](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudo-algoritmo quando um ficheiro vai ser desencriptado:
1.    Verificar se a DEK da conta do ADLS está em cache e pronta para ser utilizada.
    * Se não for esse o caso, leia a DEK encriptada a partir do armazenamento persistente e envie-a para o Azure Key Vault, para ser desencriptada. Coloque a DEK desencriptada em cache na memória, que está agora pronta para ser utilizada.
2.    Relativamente a cada bloco de dados no ficheiro
    * Leia o bloco de dados encriptado no armazenamento persistente
    * Gere a BEK a partir da DEK e do bloco de dados encriptado
    * Utilize a BEK para desencriptar os dados
#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudo-algoritmo quando vai ser encriptado um bloco de dados:
1.    Verificar se a DEK da conta do ADLS está em cache e pronta para ser utilizada.
    * Se não for esse o caso, leia a DEK encriptada a partir do armazenamento persistente e envie-a para o Azure Key Vault, para ser desencriptada. Coloque a DEK desencriptada em cache na memória, que está agora pronta para ser utilizada.
2.    Gere uma BEK exclusivo para o bloco de dados a partir da DEK.
3.    Utilize a encriptação AES-256 para encriptar o bloco de dados com a BEK.
4.    Armazene o bloco de dados encriptado no armazenamento persistente

> [!NOTE] 
> Por motivos de desempenho, a Chave de Encriptação de Dados (Data Encryption Key, DEK) armazenada de forma desprotegida é colocada em cache na memória durante um breve período de tempo e imediatamente apagada depois de decorrido esse período. No suporte de dados persistente, é sempre armazenada encriptada pela Chave de Encriptação Mestra (MEK).

## <a name="key-rotation"></a>Rotação de Chaves

O Azure Data Lake Store permite a rotação da Chave de Encriptação Mestra (MEK) quando são utilizadas chaves geridas pelo cliente. Para saber como configurar uma conta do ADLS com chaves geridas pelo cliente, veja a página [Introdução](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="pre-requisites"></a>Pré-requisitos

Durante a configuração da conta do Azure Data Lake Store, os clientes podem optar por utilizar as suas próprias chaves. Esta opção não pode ser alterada depois de a conta ter sido criada Se utilizar as opções predefinidas para a encriptação, os dados serão sempre encriptados com as chaves geridas pelo Azure Data Lake. Nesta opção, o cliente não tem a capacidade de fazer a rotação de chaves, pois são geridas por este serviço. Os passos abaixo partem do princípio de que está a utilizar chaves geridas pelo cliente (ou seja, que escolheu as suas próprias chaves a partir do seu cofre de chaves).

### <a name="how-to-rotate-the-key-mek-in-azure-data-lake-store"></a>Como rodar a chave (MEK) no Azure Data Lake Store

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/) novo
2. Navegue para o Key Vault que armazena as chaves associadas à sua conta do Azure Data Lake Store e selecione Chaves.

    ![Chaves](./media/data-lake-store-encryption/keyvault.png)

3.    Selecione a chave associada à sua conta do Azure Data Lake Store e crie uma versão nova da mesma.
  
   Neste momento, o Azure Data Lake só suporta a rotação de chaves para uma nova versão de uma chave; não suportamos a rotação para chaves diferentes.

   ![newversion](./media/data-lake-store-encryption/keynewversion.png)

4.    Navegue para a conta do Azure Data Lake Storage e selecione Encriptação

    ![newversion](./media/data-lake-store-encryption/select-encryption.png)

5.    Verá uma nota a informar de que está disponível uma versão nova da chave, bem como um botão para fazer a rotação da chave para esta versão nova. Clique em Rodar Chave para atualizar a chave para a versão nova.

    ![concluído](./media/data-lake-store-encryption/rotatekey.png)

6. Esta operação deve demorar menos de dois segundos e não se prevê qualquer período de indisponibilidade durante a rotação de chaves. Quando a operação estiver concluída, a versão nova da chave estará em uso.

