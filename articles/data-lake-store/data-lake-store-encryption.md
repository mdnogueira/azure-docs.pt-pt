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
ms.openlocfilehash: 20444d368c568ee716ff242e33323b91ffd198eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="encryption-of-data-in-azure-data-lake-store"></a>Encriptação de dados no Azure Data Lake Store

A encriptação no Azure Data Lake Store ajuda-o a proteger os seus dados, implementar políticas de segurança da empresa e cumprir os requisitos de conformidade normativa. Este artigo apresenta uma descrição geral da estrutura e descreve alguns dos aspetos técnicos da implementação.

O Data Lake Store suporta a encriptação de dados inativos e em trânsito. No caso dos dados inativos, o Data Lake Store suporta a encriptação transparente "ativada por predefinição". Eis o que cada um destes termos significa em maior detalhe:

* **Ativada por predefinição**: quando cria uma nova conta do Data Lake Store, a predefinição ativa a encriptação. Por esse motivo, os dados que são armazenados no Data Lake Store são sempre encriptados antes de serem armazenados em suportes de dados persistentes. Este é o comportamento para todos os dados e não pode ser alterado depois de uma conta ser criada.
* **Transparente**: o Data Lake Store encripta automaticamente os dados antes de persistir e desencripta-os antes da obtenção. A encriptação é configurada e gerida ao nível do Data Lake Store por um administrador. Não são feitas alterações às APIs de acesso aos dados. Assim, não é preciso fazer alterações nas aplicações e serviços que interagem com o Data Lake Store devido à encriptação.

Os dados em trânsito (também denominados dados em movimento) também são sempre encriptados no Data Lake Store. Para além de encriptar os dados antes de serem armazenados em suportes de dados persistentes, os dados também são sempre protegidos em trânsito com HTTPS. O HTTPS é o único protocolo que as interfaces REST do Data Lake Store suportam. O diagrama seguinte mostra como os dados se tornam encriptados no Data Lake Store:

![Diagrama de encriptação de dados no Data Lake Store](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-store"></a>Configurar a encriptação com o Data Lake Store

A encriptação no Data Lake Store é configurada durante a criação da conta e está sempre ativada por predefinição. Pode gerir as chaves ou permitir que o Data Lake Store as gira si (esta é a predefinição).

Para obter mais informações, veja o artigo [Introdução](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="how-encryption-works-in-data-lake-store"></a>Como funciona a encriptação no Data Lake Store

As informações seguintes explicam como gerir as chaves de encriptação mestra e explicam os três tipos diferentes de chaves que poderá utilizar na encriptação de dados no Data Lake Store.

### <a name="master-encryption-keys"></a>Chaves de encriptação mestras

O Data Lake Store disponibiliza dois modos para gerir as chaves de encriptação mestras (Master Encryption Keys, MEKs). Por agora, vamos supor que a chave de encriptação mestra é a chave de nível superior. O acesso à chave de encriptação mestra é necessário para desencriptar os dados armazenados no Data Lake Store.

Os dois modos para gerir a chave de encriptação mestra são os seguintes:

*   Chaves geridas por serviços
*   Chaves geridas pelo cliente

Em ambos os modos, a chave de encriptação mestra é protegida mediante armazenamento no Azure Key Vault. O Key Vault é um serviço totalmente gerido e altamente seguro do Azure, que pode ser utilizado para salvaguardar chaves criptográficas. Para obter mais informações, veja [Key Vault](https://azure.microsoft.com/services/key-vault).

Segue-se uma breve comparação das capacidades proporcionadas pelos dois modos de gestão de MEKs.

|  | Chaves geridas por serviços | Chaves geridas pelo cliente |
| --- | --- | --- |
|Como são armazenados os dados?|São sempre encriptados antes de serem armazenados.|São sempre encriptados antes de serem armazenados.|
|Onde é armazenada a Chave de Encriptação Mestra?|Cofre de Chaves|Cofre de Chaves|
|As chaves de encriptação são armazenadas de forma desprotegida, fora do Key Vault? |Não|Não|
|É possível obter a MEK a partir do Key Vault?|Não. Depois de a MEK ser armazenada no Key Vault, só pode ser utilizada para encriptação e desencriptação.|Não. Depois de a MEK ser armazenada no Key Vault, só pode ser utilizada para encriptação e desencriptação.|
|Quem é o proprietário da instância do Key Vault e da MEK?|O serviço Data Lake Store|O utilizador é o proprietário da instância do Key Vault, que pertence à sua própria subscrição do Azure. A MEK no Key Vault pode ser gerida por software ou hardware.|
|Pode revogar o acesso à MEK do serviço Data Lake Store?|Não|Sim. Pode gerir listas de controlo de acesso no Key Vault e remover entradas do controlo de acesso para a identidade de serviço no âmbito do serviço Data Lake Store.|
|Pode eliminar permanentemente a MEK?|Não|Sim. Se eliminar a MEK do Key Vault, os dados na conta do Data Lake Store não podem ser desencriptados por ninguém, incluindo o serviço Data Lake Store. <br><br> Se tiver criado explicitamente uma cópia de segurança da MEK antes de a eliminar do Key Vault, a MEK pode ser restaurada e os dados recuperados. No entanto, se não tiver feito uma cópia de segurança antes de eliminar a MEK do Key Vault, não mais será possível desencriptar os dados na conta do Data Lake Store depois disso.|


Para além desta diferença, ou seja, de quem gere a MEK e a instância do Key Vault onde esta reside, o resto do design é igual em ambos os modos.

É importante não esquecer o seguinte quando escolher o modo para as chaves de encriptação mestras:

*   Pode optar por utilizar chaves geridas pelo cliente ou chaves geridas pelo serviço quando aprovisiona uma conta do Data Lake Store.
*   Depois de uma conta do Data Lake Store ser aprovisionada, o modo não pode ser alterado.

### <a name="encryption-and-decryption-of-data"></a>Encriptação e desencriptação de dados

São utilizados três tipos de chaves no design da encriptação de dados. A tabela seguinte fornece um resumo:

| Chave                   | Abreviatura | Associada a | Localização do armazenamento                             | Tipo       | Notas                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Chave de Encriptação Mestra | MEK          | Uma conta do Data Lake Store | Cofre de Chaves                              | Assimétrica | Pode ser gerida pelo Data Lake Store ou por si.                                                              |
| Chave de Encriptação de Dados   | DEK          | Uma conta do Data Lake Store | Armazenamento persistente, gerido pelo serviço Data Lake Store | Simétrica  | O DEK é encriptado pela MEK. O DEK encriptado é o que é armazenado no suporte de dados persistente. |
| Chave de Encriptação de Blocos  | BEK          | Um bloco de dados | Nenhuma                                         | Simétrica  | A BEK é obtida a partir da DEK e do bloco de dados.                                                      |

O diagrama seguinte ilustra estes conceitos:

![Chaves na encriptação de dados](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudo-algoritmo quando um ficheiro vai ser desencriptado:
1.  Verificar se a DEK da conta do Data Lake Store está em cache e pronta para ser utilizada.
    - Se não for esse o caso, leia a DEK encriptada a partir do armazenamento persistente e envie-a para o Key Vault, para ser desencriptada. Coloque a DEK desencriptada em cache na memória. Está agora pronta para ser utilizada.
2.  Relativamente a cada bloco de dados no ficheiro:
    - Leia o bloco de dados encriptado no armazenamento persistente.
    - Gere a BEK a partir da DEK e do bloco de dados encriptado.
    - Utilize a BEK para desencriptar os dados.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudo-algoritmo quando vai ser encriptado um bloco de dados:
1.  Verificar se a DEK da conta do Data Lake Store está em cache e pronta para ser utilizada.
    - Se não for esse o caso, leia a DEK encriptada a partir do armazenamento persistente e envie-a para o Key Vault, para ser desencriptada. Coloque a DEK desencriptada em cache na memória. Está agora pronta para ser utilizada.
2.  Gere uma BEK exclusivo para o bloco de dados a partir da DEK.
3.  Utilize a encriptação AES-256 para encriptar o bloco de dados com a BEK.
4.  Armazene o bloco de dados encriptado no armazenamento persistente.

> [!NOTE] 
> Por motivos de desempenho, a DEK armazenada de forma desprotegida é colocada em cache na memória durante um breve período de tempo e imediatamente apagada depois de decorrido esse período. No suporte de dados persistente, é sempre armazenada encriptada pela MEK.

## <a name="key-rotation"></a>Rotação de chaves

Quando estiver a utilizar chaves geridas pelo cliente, pode rodar a MEK. Para saber como configurar uma conta do Data Lake Store com chaves geridas pelo cliente, veja o artigo [Introdução](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="prerequisites"></a>Pré-requisitos

Quando configurou a conta do Data Lake Store, optou por utilizar as suas próprias chaves. Esta opção não pode ser alterada depois de a conta ter sido criada Os passos seguintes partem do princípio de que está a utilizar chaves geridas pelo cliente (ou seja, que escolheu as suas próprias chaves a partir do Key Vault).

Tenha em atenção que, se utilizar as opções predefinidas para a encriptação, os dados são sempre encriptados utilizando chaves geridas pelo Data Lake Store. Com esta opção, não tem a capacidade de rodar chaves, uma vez que são geridas pelo Data Lake Store.

### <a name="how-to-rotate-the-mek-in-data-lake-store"></a>Como rodar a MEK no Data Lake Store

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Navegue para a instância do Key Vault que armazena as chaves associadas à sua conta do Data Lake Store. Selecione **Chaves**.

    ![Captura de ecrã do Key Vault](./media/data-lake-store-encryption/keyvault.png)

3.  Selecione a chave associada à sua conta do Data Lake Store e crie uma versão nova desta chave. Tenha em atenção que, atualmente, o Data Lake Store só suporta a rotação de chaves para uma nova versão de uma chave. Não suporta a rotação para uma chave diferente.

   ![Captura de ecrã da janela Chaves, com a opção Nova Versão realçada](./media/data-lake-store-encryption/keynewversion.png)

4.  Navegue para a conta de armazenamento do Data Lake Store e selecione **Encriptação**.

    ![Captura de ecrã da janela da conta de armazenamento do Data Lake Store, com a opção Encriptação realçada](./media/data-lake-store-encryption/select-encryption.png)

5.  Verá uma mensagem a informar de que está disponível uma versão nova da chave. Clique em **Rodar Chave** para atualizar a chave para a versão nova.

    ![Captura de ecrã da janela do Data Lake Store com a mensagem e a opção Rodar Chave realçadas](./media/data-lake-store-encryption/rotatekey.png)

Esta operação deve demorar menos de dois minutos e não se prevê qualquer período de indisponibilidade durante a rotação de chaves. Depois de a operação estar concluída, a versão nova da chave estará em uso.
