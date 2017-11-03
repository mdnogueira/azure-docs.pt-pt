---
title: "Segurança de série 8000 do StorSimple | Microsoft Docs"
description: "Descreve as funcionalidades de segurança e privacidade que proteger o serviço StorSimple, dispositivos e dados no local e na nuvem."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: e4980cdb77650f011d143ed9e86fbe0af6b29d5d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-security-and-data-protection"></a>Proteção de dados e de segurança do StorSimple

## <a name="overview"></a>Descrição geral

A segurança é uma preocupação principais para qualquer pessoa que está a adotar uma nova tecnologia, especialmente quando a tecnologia é utilizada com dados confidenciais ou proprietários. Como avaliar as diferentes tecnologias, tem de considerar o aumento dos riscos e os custos para proteção de dados. Microsoft Azure StorSimple fornece uma segurança e a solução de privacidade para a proteção de dados, ajudando a garantir:

* **Confidencialidade** – apenas entidades autorizadas podem ver os seus dados.
* **Integridade** – apenas autorizadas entidades podem modificar ou eliminar os dados.

A solução do Microsoft Azure StorSimple consiste em quatro componentes principais que interagem entre si:

* **Serviço de Gestor de dispositivos do StorSimple alojado no Microsoft Azure** – o serviço de gestão que utiliza para configurar e aprovisionar o dispositivo StorSimple.
* **O dispositivo StorSimple** – um dispositivo físico instalado no seu centro de dados. Todos os anfitriões e clientes que geram dados ligar para o dispositivo StorSimple e o dispositivo gere os dados e move-o para a nuvem do Azure conforme adequado.
* **Os clientes/anfitriões ligados ao dispositivo** – os clientes na sua infraestrutura que estabelecer ligação com o dispositivo StorSimple e geram dados tem de ser protegidas.
* **Armazenamento na nuvem** – a localização na nuvem do Azure onde os dados são armazenados.

As secções seguintes descrevem as funcionalidades de segurança do StorSimple que ajudam a proteger a cada um destes componentes e os dados armazenados nos mesmos. Também inclui uma lista de perguntas que possa ter sobre a segurança do Microsoft Azure StorSimple e as respostas correspondentes.

## <a name="storsimple-device-manager-service-protection"></a>Proteção de serviço do Gestor de dispositivos do StorSimple

O serviço do Gestor de dispositivos do StorSimple é um serviço de gestão alojado no Microsoft Azure e utilizado para gerir todos os dispositivos StorSimple que a sua organização tem fornecidas. Pode aceder ao serviço de Gestor de dispositivos do StorSimple, utilizando as credenciais organizacionais para iniciar sessão no portal do Azure através de um browser.

Acesso ao serviço StorSimple Manager de dispositivos requer que a sua organização tiver uma subscrição do Azure que inclua StorSimple. A subscrição é regida pelas funcionalidades que pode aceder no portal do Azure. Se a sua organização não tiver uma subscrição do Azure e pretender saber mais sobre os mesmos, consulte [inscrever-se no Azure como uma organização](../active-directory/sign-up-organization.md).

Uma vez que o serviço do Gestor de dispositivos do StorSimple está alojado no Azure, está protegido pelas funcionalidades de segurança do Azure. Para obter mais informações sobre as funcionalidades de segurança fornecida pelo Microsoft Azure, visite o [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Proteção de dispositivos do StorSimple

O dispositivo StorSimple é um dispositivo de armazenamento híbrida no local que contenha unidades de estado sólido (SSDs) e unidades de disco rígido (HDDs), juntamente com os controladores de redundantes e capacidades de ativação pós-falha automática. Os controladores de gerir o armazenamento em camadas, colocando atualmente utilizados (ou quentes) dados no armazenamento local (no StorSimple no local ou de dispositivo servidores), ao mover dados menos utilizados com frequência para a nuvem.

Apenas autorizados StorSimple dispositivos estão autorizados a aderir ao serviço StorSimple Manager de dispositivo que criou na sua subscrição do Azure. Para autorizar um dispositivo, tem de registá-lo com o serviço do Gestor de dispositivos do StorSimple, fornecendo a chave de registo do serviço. A chave de registo do serviço é uma chave aleatória de 128 bits gerada no portal do Azure.

![Chave de registo do serviço](./media/storsimple-security/ServiceRegistrationKey.png)

Para saber como obter uma chave de registo do serviço, aceda a [passo 2: obter a chave de registo do serviço](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

A chave de registo do serviço é uma chave de tempo que contém 100 + carateres. Pode copiar a chave e guarde-o num ficheiro de texto numa localização segura, de modo a que pode utilizá-lo para autorizar dispositivos adicionais conforme necessário. Se a chave de registo do serviço se perder depois de registar o seu primeiro dispositivo, pode gerar uma nova chave do serviço do Gestor de dispositivos do StorSimple. Isto não irá afetar o funcionamento dos dispositivos existentes.

Depois de um dispositivo é registado, utiliza os tokens para comunicar com o Microsoft Azure. A chave de registo do serviço não será utilizada após o registo do dispositivo.

> [!NOTE]
> Recomendamos que voltar a gerar a chave de registo do serviço após cada utilização.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Proteger a sua solução StorSimple através de palavras-passe

As palavras-passe são um aspeto importante de segurança do computador e são utilizadas extensivamente na solução StorSimple, para ajudar a garantir que os seus dados estão acessíveis para apenas os utilizadores autorizados. StorSimple permite-lhe configurar as palavras-passe seguintes:

* Palavra-passe do administrador de dispositivo do StorSimple
* Challenge Handshake Authentication Protocol (CHAP) e o destino do iniciador as palavras-passe
* Palavra-passe do Snapshot Manager StorSimple

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell para StorSimple e a palavra-passe de administrador do dispositivo de StorSimple

O Windows PowerShell para StorSimple é uma interface de linha de comandos que pode utilizar para gerir o dispositivo StorSimple. O Windows PowerShell para StorSimple tem funcionalidades que lhe permitem registar o seu dispositivo, configure a interface de rede no seu dispositivo, instalar determinados tipos de atualizações, resolver problemas relacionados com o seu dispositivo acedendo a sessão de suporte e alteram o estado do dispositivo. Pode aceder a do Windows PowerShell para StorSimple estabelecendo ligação à consola de série do dispositivo ou ao utilizar a comunicação remota do Windows PowerShell.

Comunicação remota do PowerShell pode ser feita através de HTTPS ou HTTP. Se estiver ativada a gestão remota através de HTTPS, terá de transferir o certificado de gestão remota do dispositivo e instalá-la no cliente remoto. Para obter mais informações sobre a comunicação remota do PowerShell, aceda a [ligar remotamente ao dispositivo StorSimple](storsimple-8000-remote-connect.md).

Depois de utilizar o Windows PowerShell para StorSimple para ligar ao dispositivo, terá de fornecer a palavra-passe de administrador do dispositivo para iniciar sessão no dispositivo.

![Palavra-passe do administrador do dispositivo](./media/storsimple-security/DeviceAdminPW.png)

Mantenha as seguintes melhores práticas em mente:

* Gestão remota está desativada por predefinição. Pode utilizar o serviço do Gestor de dispositivos do StorSimple para ativá-la. Como melhor prática de segurança, acesso remoto deve ser ativado apenas durante o período de tempo que é realmente necessária.
* Se alterar a palavra-passe, lembre-se de que notificar todos os utilizadores de acesso remoto para que não registam uma perda de conectividade inesperado.
* O serviço do Gestor de dispositivos do StorSimple não é possível obter palavras-passe existentes: só pode redefini-los. Recomendamos que armazene todas as palavras-passe num local seguro para que não é necessário repor uma palavra-passe se for esquecido. Se precisar de repor uma palavra-passe, lembre-se de que notificar todos os utilizadores antes de que a repuser.

Pode aceder a interface do Windows PowerShell utilizando uma ligação de série ao dispositivo. Também pode aceder-lhe remotamente utilizando HTTP ou HTTPS, o que fornecer segurança adicional. HTTPS fornece um nível superior de segurança de uma série ou de uma ligação HTTP. No entanto, para utilizar HTTPS, tem primeiro de instalar um certificado no computador cliente que irá aceder ao dispositivo. Pode transferir o certificado de acesso remoto da página de configuração de dispositivo no serviço StorSimple Manager do dispositivo. Se o certificado para acesso remoto, tem de transferir um novo certificado e propagá-lo a todos os clientes que estão autorizados a utilizar a gestão remota.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Challenge Handshake Authentication Protocol (CHAP) e o destino do iniciador as palavras-passe

CHAP é um esquema de autenticação utilizado pelo dispositivo StorSimple para validar a identidade de clientes remotos. A verificação baseiam-se uma palavra-passe partilhada. CHAP pode ser unidirecional (unidirecionais) ou mútua (bidirecional). Com unidirecional CHAP, o destino (o dispositivo StorSimple) autentica um iniciador (anfitrião). CHAP inverso ou mútua requer que o destino autenticar o iniciador e, em seguida, o iniciador autenticar o destino. O StorSimple pode ser configurado para utilizar qualquer um dos métodos.

Tenha em atenção o seguinte quando configurar o CHAP:

* O nome de utilizador tem de conter menos de 233 carateres.
* A palavra-passe do CHAP tem de ter entre 12 e 16 carateres. A tentar utilizar um nome de utilizador mais ou palavra-passe ocorrerá uma falha de autenticação no anfitrião do Windows.
* Não é possível utilizar a mesma palavra-passe para o Iniciador do CHAP e o destino do CHAP.
* Depois de definir a palavra-passe, pode ser alterado, mas não é possível obter. Se a palavra-passe é alterada, lembre-se de que notificar todos os utilizadores de acesso remoto para que estes possam ligar com êxito para o dispositivo StorSimple.

Para obter mais informações sobre CHAP e como configurá-lo para a sua solução StorSimple, aceda a [configurar o CHAP para o dispositivo StorSimple](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Palavra-passe do Snapshot Manager StorSimple

Snapshot Manager do StorSimple é um snap-in da consola de gestão da Microsoft (MMC), que utiliza grupos de volume e o serviço de cópia de sombra de Volume do Windows para gerar as cópias de segurança consistentes com aplicações. Além disso, pode utilizar o Snapshot Manager do StorSimple para criar as agendas de cópia de segurança e clone ou restauro de volumes.

Quando configurar um dispositivo para utilizar o Snapshot Manager do StorSimple, será necessário para fornecer a palavra-passe do Snapshot Manager do StorSimple. Esta palavra-passe primeiro está definido no Windows PowerShell para StorSimple durante o registo. A palavra-passe também pode ser definida e alterada do serviço do Gestor de dispositivos do StorSimple. Esta palavra-passe autentica o dispositivo com o Snapshot Manager do StorSimple.

![Palavra-passe do Snapshot Manager StorSimple](./media/storsimple-security/SnapshotMgrPassword.png)

A palavra-passe do Snapshot Manager do StorSimple tem de ser 14 a 15 carateres e tem de conter 3 ou mais de uma combinação de carateres em maiúsculas, minúsculas, numérico e especiais. Depois de definir a palavra-passe do Snapshot Manager do StorSimple, pode ser alterado, mas não é possível obter. Se alterar a palavra-passe, lembre-se de que notificar todos os utilizadores remotos.

Para obter mais informações sobre o Snapshot Manager do StorSimple, aceda a [Novidades Snapshot Manager do StorSimple?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Melhores práticas de palavra-passe

Recomendamos que utilize as diretrizes seguintes para ajudar a garantir que as palavras-passe do StorSimple estão seguras e bem protegido:

* Altere as palavras-passe a cada três meses. Alterar as palavras-passe é imposta anual.
* Utilize palavras-passe seguras. Para obter mais informações, aceda a [criar palavras-passe mais fortes e Proteja-os](http://blogs.microsoft.com/cybertrust/2014/08/25/create-stronger-passwords-and-protect-them/).
* Utilize sempre palavras-passe diferentes mecanismos de acesso diferentes; cada um das palavras-passe que especificar deve ser exclusiva.
* Não partilhe as palavras-passe com qualquer pessoa que não está autorizado a aceder ao dispositivo StorSimple.
* Não enunciar sobre uma palavra-passe à frente outras pessoas ou sugestão em formato de uma palavra-passe.
* Se suspeitar que uma conta ou palavra-passe tiver sido comprometido, relatar o incidente para o departamento de segurança de informações.
* Processe todas as palavras-passe como informações confidenciais, confidenciais. 

## <a name="storsimple-data-protection"></a>Proteção de dados do StorSimple

Esta secção descreve as funcionalidades de segurança do StorSimple que proteger dados em trânsito e dados armazenados.

Conforme descrito noutras secções, palavras-passe são utilizadas para autorizar e autenticar os utilizadores antes de terem acesso à sua solução StorSimple. Outra consideração de segurança está a proteger dados contra utilizadores não autorizados enquanto está a ser transferido entre sistemas de armazenamento e enquanto esta está a ser armazenada. As secções seguintes descrevem as funcionalidades de proteção de dados fornecidas com StorSimple.

> [!NOTE]
> A eliminação de duplicados proporciona proteção adicional para dados armazenados no dispositivo StorSimple e no armazenamento do Microsoft Azure. Quando tem eliminação de dados duplicados, os objetos de dados são armazenados separadamente dos metadados utilizado para mapear e aceder aos mesmos: não há nenhum contexto de nível de armazenamento disponível para reconstrua os dados com base na estrutura de volume, o sistema de ficheiros ou o nome de ficheiro.


## <a name="protect-data-flowing-through-the-service"></a>Proteger dados que fluem através do serviço

O objetivo principal do serviço Gestor de dispositivos do StorSimple é gerir e configurar o dispositivo StorSimple. O serviço do Gestor de dispositivos do StorSimple é executado no Microsoft Azure. Utilize o portal do Azure para introduzir dados de configuração do dispositivo e, em seguida, o Microsoft Azure utiliza o serviço StorSimple Manager de dispositivo para enviar dados para o dispositivo. StorSimple utiliza um sistema de pares de chaves assimétricas para ajudar a garantir que um comprometimento de serviço do Azure não irá resultar de um compromisso da informação armazenada.

![Encriptação de dados em trânsito](./media/storsimple-security/DataEncryption.png)

O sistema de chave assimétrico ajuda a proteger os dados que flui através do serviço da seguinte forma:

1. Um certificado de encriptação de dados que utiliza uma chave pública e privada assimétrica par é gerado no dispositivo e é utilizado para proteger os dados. As chaves são geradas quando o primeiro dispositivo está registado.
2. As chaves de certificado de encriptação de dados são exportadas num ficheiro Personal Information Exchange (. pfx) que está protegido pela chave de encriptação de dados do serviço, que é uma chave de 128 bits forte que é gerada aleatoriamente pelo primeiro dispositivo durante o registo.
3. A chave pública do certificado é segura disponibilizada para o serviço do Gestor de dispositivos do StorSimple e a chave privada permanece com o dispositivo.
4. Introduzir o serviço de dados são encriptados utilizando o público chave e desencriptado utilizando a chave privada armazenada no dispositivo, garantindo que o serviço do Azure não é possível desencriptar os dados que fluem para o dispositivo.

A chave de encriptação de dados do serviço é gerada no apenas o primeiro dispositivo registado com o serviço. Todos os dispositivos posteriores que são registados com o serviço tem de utilizar a mesma chave de encriptação de dados do serviço.

> [!IMPORTANT]
> É muito importante efetuar uma cópia da chave de encriptação de dados do serviço e guardá-lo numa localização segura. Uma cópia da chave de encriptação de dados do serviço deve ser armazenada de forma a que possa ser acedida por uma pessoa autorizada e pode ser facilmente comunicado ao administrador do dispositivo.
> 
> Se se perder a chave de encriptação de dados do serviço, uma pessoa de suporte da Microsoft pode ajudá-lo para obtê-lo, desde que tenha, pelo menos, um dispositivo num estado online. Recomendamos que altere a chave de encriptação de dados do serviço após é obtido. Para obter instruções, aceda a [alterar a chave de encriptação de dados do serviço](storsimple-service-dashboard.md#change-the-service-data-encryption-key).

Pode alterar a chave de encriptação de dados do serviço e o certificado de encriptação de dados correspondente ao selecionar o **chave de encriptação de dados de serviço de alteração** opção no dashboard do serviço. Para garantir que a segurança dos dados não for comprometida, tem de utilizar um dispositivo físico StorSimple para alterar a chave de encriptação de dados do serviço. Alterar as chaves de encriptação requer que todos os dispositivos ser atualizado com a nova chave. Por conseguinte, recomendamos que altere a chave quando todos os dispositivos estão online. Se os dispositivos estiverem offline, as respetivas chaves podem ser alteradas noutra altura. Os dispositivos com chaves Desatualizadas ainda será capazes de executar cópias de segurança, mas não poderão restaurar dados até que a chave é atualizada. Para obter mais informações, aceda a [utilize o dashboard de serviço do Gestor de dispositivos do StorSimple](storsimple-8000-service-dashboard.md).

A chave de encriptação de dados do serviço e o certificado de encriptação de dados não expirar. No entanto, recomendamos que altere a chave de encriptação de dados do serviço anual para ajudar a evitar o comprometimento de chave.

## <a name="protect-data-at-rest"></a>Proteger dados Inativos

O dispositivo StorSimple gere dados através do armazenamento em camadas, localmente e na nuvem, dependendo da frequência de utilização. Todos os computadores anfitriões que estão ligados ao dispositivo enviam dados para o dispositivo, o que, em seguida, move os dados na nuvem, conforme apropriado. Dados são transferidos do dispositivo para a nuvem em segurança através da Internet. Cada dispositivo tem um destino de iSCSI que analisa todos os volumes partilhados nesse dispositivo. Todos os dados são encriptados antes de ser enviada para o armazenamento na nuvem. 

![Chave de encriptação de armazenamento na nuvem](./media/storsimple-security/CloudStorageEncryption.png)

Para ajudar a garantir a segurança e integridade dos dados movidas para a nuvem, o StorSimple permite-lhe definir as chaves de encriptação de armazenamento em nuvem da seguinte forma:

* Especifique a chave de encriptação de armazenamento na nuvem quando cria um contentor de volume. A chave não pode ser modificada ou adicionada mais tarde.
* Todos os volumes num contentor de volume partilham a mesma chave de encriptação. Se pretende uma forma diferente de encriptação para um volume específico, recomendamos que crie um novo contentor de volume para alojar esse volume.
* Ao introduzir a chave de encriptação de armazenamento na nuvem no serviço do Gestor de dispositivos do StorSimple, a chave for encriptada utilizando a parte pública da chave de encriptação de dados do serviço e, em seguida, é enviada para o dispositivo.
* A chave de encriptação de armazenamento na nuvem não é armazenada em qualquer lugar no serviço e for conhecida apenas para o dispositivo.
* Especificar uma chave de encriptação de armazenamento na nuvem é opcional. Pode enviar dados que foi encriptados no anfitrião para o dispositivo.

### <a name="additional-security-best-practices"></a>Melhores práticas de segurança adicionais

* Divisão de tráfego: isolar o SAN iSCSI de tráfego de utilizador numa LAN empresa ao implementar uma rede completamente separada e utilizar VLANs onde isolamento físico não é uma opção. Uma rede dedicada para armazenamento de iSCSI irá garantir a segurança e o desempenho dos seus dados críticos. A combinação de tráfego de armazenamento e de utilizador através de uma LAN empresarial não é recomendada e pode aumentar a latência e provocar falhas de rede.
* Para segurança de rede do lado do anfitrião, utilize interfaces de rede que suportam o motor de descarga de TCP/IP (TOE). TOE reduz a carga da CPU pelo processamento TCP no adaptador de rede.

## <a name="protect-data-via-storage-accounts"></a>Proteger dados através de contas de armazenamento

Cada subscrição do Microsoft Azure, pode criar uma ou mais contas de armazenamento. (Uma conta do storage fornece um espaço de nomes exclusivo para trabalhar com dados armazenados na nuvem do Azure.) Acesso a uma conta de armazenamento é controlado pelas chaves de acesso e de subscrição associadas com essa conta de armazenamento.

Quando cria uma conta de armazenamento, o Microsoft Azure gera duas chaves de acesso de armazenamento de 512 bits, uma das quais é utilizada para autenticação quando o dispositivo StorSimple acede a conta de armazenamento. Tenha em atenção que apenas um destas chaves está a ser utilizado. A outra chave está retida na reserva, permitindo-lhe rodar as chaves periodicamente. Para rodar chaves, tornar a chave secundária ativa e, em seguida, elimine a chave primária. Em seguida, pode criar uma nova chave para utilização durante a próxima rotação. (Por motivos de segurança, muitos dos centros de dados exigem rotação da chave.)

Recomendamos que siga estas práticas recomendadas para rotação da chave:

* Deve rodar chaves de conta de armazenamento regularmente para ajudar a garantir que a sua conta de armazenamento não é acedida por utilizadores não autorizados.
* Periodicamente, o administrador do Azure deve alterar ou voltar a gerar a chave primária ou secundária utilizando a secção de armazenamento do portal do Azure para aceder diretamente ao mesmo a conta de armazenamento.

## <a name="protect-data-via-encryption"></a>Proteger dados através de encriptação

StorSimple utiliza os algoritmos de encriptação seguintes para proteger os dados armazenados na ou estiverem em deslocação entre os componentes da solução StorSimple.

| Algoritmo | Comprimento de chave | Aplicações/protocolos/comentários |
| --- | --- | --- |
| RSA |2048 |RSA PKCS 1 v 1.5, tal é utilizada pelo portal do Azure para encriptar os dados de configuração que são enviados para o dispositivo: por exemplo, o armazenamento de credenciais, configuração do dispositivo StorSimple, de contas e chaves de encriptação de armazenamento da nuvem. |
| AES |256 |AES com CBC é utilizado para encriptar a parte pública da chave de encriptação de dados do serviço antes de ser enviada para o portal do Azure do dispositivo StorSimple. Também é utilizado pelo dispositivo StorSimple para encriptar dados antes dos dados são enviados para a conta de armazenamento na nuvem. |

## <a name="storsimple-cloud-appliance-security"></a>Segurança de dispositivo de nuvem do StorSimple

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="frequently-asked-questions-faq"></a>Perguntas mais frequentes (FAQ)

Seguem-se algumas perguntas e respostas sobre segurança e o Microsoft Azure StorSimple.

**P:** meu serviço seja comprometido. O que deve ser os meus passos seguintes?

**R:** imediatamente, deve alterar a chave de encriptação de dados do serviço e as chaves de conta de armazenamento para a conta de armazenamento que está a ser utilizado para dados de camadas. Para obter instruções, aceda a:

* [Alterar a chave de encriptação de dados do serviço](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
* [Rotação da chave de contas do storage](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**P:** tenho um novo dispositivo StorSimple que é solicitar a chave de registo do serviço. Como obtê-lo?

**R:** esta chave foi criada quando criou o serviço do Gestor de dispositivos do StorSimple. Quando utilizar o serviço do Gestor de dispositivos do StorSimple para ligar ao dispositivo, pode utilizar a página de início rápido do serviço para ver ou voltar a gerar a chave de registo do serviço. Gerar uma nova chave de registo do serviço não irá afetar os dispositivos registados existentes. Para obter instruções, aceda a:

* [Ver ou voltar a gerar a chave de registo do serviço](storsimple-8000-manage-service.md##regenerate-the-service-registration-key)

**P:** perdeu a minha chave de encriptação de dados do serviço. O que posso fazer?

**R:** contacte o suporte da Microsoft. Pode iniciar sessão a uma sessão de suporte no seu dispositivo e ajuda a obter a chave (desde que, pelo menos, um dispositivo está online). Imediatamente depois de obter a chave de encriptação de dados do serviço, só deve ser alterado para se certificar de que a nova chave é conhecida apenas para si. Para obter instruções, aceda a:

* [Alterar a chave de encriptação de dados do serviço](storsimple-service-dashboard.md#change-the-service-data-encryption-key)

**P:** autorizados de um dispositivo para uma alteração de chave de encriptação de dados de serviço, mas não foi possível iniciar o processo de alteração de chave. O que devo fazer?

**R:** se o período de tempo limite expirou, terá de voltar a autorizar o dispositivo para que a alteração de chave de encriptação do serviço dados e iniciar o processo de novo.

**P:** posso alterar a chave de encriptação de dados do serviço, mas como posso não conseguiu atualizar os outros dispositivos no prazo de 4 horas. É necessário iniciar novamente?

**R:** o período de tempo de 4 horas é apenas para iniciar a alteração. Depois de iniciar o processo de atualização no dispositivo StorSimple autorizado, a autorização é válida até que todos os dispositivos são atualizados.

**P:** StorSimple nossa administrador tiver saído da empresa. O que devo fazer?

**R:** alterar e repor as palavras-passe que permitem o acesso ao dispositivo StorSimple e alterar a encriptação de dados do serviço de chave para se certificar de que as novas informações não são conhecidas não autorizado técnico. Para obter instruções, aceda a:

* [Utilizar o serviço do Gestor de dispositivos do StorSimple para alterar as palavras-passe do storsimple](storsimple-8000-change-passwords.md)
* [Alterar a chave de encriptação de dados do serviço](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
* [Configurar o CHAP para o dispositivo StorSimple](storsimple-8000-configure-chap.md)

**P:** pretender fornecer a palavra-passe do Snapshot Manager do StorSimple num anfitrião que está a ligar para o dispositivo StorSimple, mas a palavra-passe não está disponível. O que devo fazer?

**R:** se tenha esquecido a palavra-passe, deve criar um novo. Em seguida, lembre-se de que todos os utilizadores existentes de informar que a palavra-passe foi alterada e que deve a atualizar os seus clientes para utilizar a nova palavra-passe. Para obter instruções, aceda a:

* [Alterar a palavra-passe do Snapshot Manager do StorSimple](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Autenticar um dispositivo](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**P:** o certificado para acesso remoto para o Windows PowerShell para StorSimple foi alterado no dispositivo. Como posso atualizar os meus clientes de acesso remoto?

**R:** pode transferir o novo certificado do serviço do Gestor de dispositivos do StorSimple e, em seguida, forneça-lo para ser instalado no arquivo de certificados de clientes de acesso remoto. Para obter instruções, aceda a:

* [Cmdlet de importar certificado](https://technet.microsoft.com/library/hh848630.aspx)

**P:** os meus dados protegidos se o serviço do Gestor de dispositivos do StorSimple é comprometido?

**R:** dados de configuração de serviço são sempre encriptados com a chave pública quando vê-la num web browser. Porque o serviço não tem acesso à chave privada, o serviço não será capaz de ver quaisquer dados. Se o serviço StorSimple Manager do dispositivo for comprometido, não há nenhum impacto sobre, porque existem não existem chaves armazenados no serviço do Gestor de dispositivos do StorSimple.

**P:** se alguém obtém acesso para o certificado de encriptação de dados, serão os meus dados comprometidos?

**R:** Microsoft Azure armazena a chave de encriptação de dados do cliente (ficheiro. pfx) num formato encriptado. Porque o ficheiro. pfx é encriptado e o serviço do StorSimple não tem a chave de encriptação de dados do serviço para desencriptar o ficheiro. pfx, simplesmente obter acesso ao ficheiro. pfx não irá expor qualquer segredos.

**P:** o que acontece se uma entidade governamental pede-lhe Microsoft os meus dados?

**R:** porque todos os dados são encriptados no serviço e a chave privada é mantida com o dispositivo, a entidade governamental tem de pedir ao cliente para os dados.

## <a name="next-steps"></a>Passos seguintes

[Implementar o dispositivo StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

