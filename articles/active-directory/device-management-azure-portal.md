---
title: "Gerir dispositivos através do portal do Azure | Microsoft Docs"
description: Saiba como utilizar o portal do Azure para gerir dispositivos.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: cd13ecf50cf03c362f8da2da83ab698c4e8ba0cc
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="managing-devices-using-the-azure-portal"></a>Gestão de dispositivos no portal do Azure


Gestão de dispositivos no Azure Active Directory (Azure AD), pode certificar-se de que os utilizadores acedem aos seus recursos dos dispositivos que cumprem as normas de segurança e conformidade. 

Neste tópico:

- Parte do princípio de que está familiarizado com o [introdução à gestão de dispositivos no Azure Active Directory](device-management-introduction.md)

- Fornece informações sobre como gerir os seus dispositivos no portal do Azure



## <a name="configure-device-settings"></a>Configurar definições do dispositivo

Para gerir os seus dispositivos no portal do Azure, os seus dispositivos tem de ser [registado ou associado](device-management-introduction.md#getting-devices-under-the-control-of-azure-ad) para o Azure AD. Como administrador, pode ajustar o processo de registar e associação de dispositivos ao configurar as definições do dispositivo. 

![Configurar definições do dispositivo](./media/device-management-azure-portal/22.png)

A página de definições do dispositivo permite-lhe configurar:

![Gerir um dispositivo do Intune](./media/device-management-azure-portal/21.png)


- **Os utilizadores podem associar dispositivos ao Azure AD** -esta definição permite-lhe selecionar os utilizadores podem [associar dispositivos](device-management-introduction.md#azure-ad-joined-devices) para o Azure AD. A predefinição é **todos os**.

- **Os administradores locais adicionais sobre o Azure AD dispositivos associados ao** -pode selecionar os utilizadores que são concedidos direitos de administrador local num dispositivo. São adicionados utilizadores adicionados aqui para o *administradores de dispositivos* função no Azure AD. Os administradores globais no Azure AD e aos proprietários do dispositivo são concedidos direitos de administrador local por predefinição. Esta opção é uma capacidade de edição premium disponível através de produtos como o Azure AD Premium ou Enterprise Mobility Suite (EMS). 

- **Os utilizadores podem registar os seus dispositivos com o Azure AD** -terá de configurar esta definição para permitir que os dispositivos sejam [registado](device-management-introduction.md#azure-ad-registered-devices) com o Azure AD. Se selecionar **nenhum**, dispositivos não são permitidos para registar quando não estão do Azure AD associado ou híbrida do Azure AD associado. Inscrição com o Microsoft Intune ou a gestão de dispositivos móveis (MDM) para o Office 365 requer o registo. Se tiver configurado qualquer um destes serviços, **todos os** está selecionado e **NONE** não está disponível...

- **Requer multi-Factor Auth associar dispositivos** -pode escolher se os utilizadores são obrigados a fornecer um segundo fator de autenticação para [associação](device-management-introduction.md#azure-ad-joined-devices) os respetivos dispositivos para o Azure AD. A predefinição é **não**. Recomendamos que requerem autenticação multifator ao registar um dispositivo. Antes de ativar a multi-factor authentication para este serviço, certifique-se de que a autenticação multifator está configurada para os utilizadores que registar os respetivos dispositivos. Para obter mais informações sobre os serviços de autenticação multifator Azure diferentes, consulte [introdução à Azure multi-factor authentication](../multi-factor-authentication/multi-factor-authentication-get-started.md). 

- **Número máximo de dispositivos** -esta definição permite-lhe selecionar o número máximo de dispositivos que um utilizador pode ter no Azure AD. Se um utilizador atingir esta quota, estes não são poderá adicionar dispositivos até um ou mais dos dispositivos existentes são removidas. As aspas de dispositivo são contabilizadas para todos os dispositivos do Azure AD associada ao ou do Azure AD atualmente registado. O valor predefinido é **20**.

- **Os utilizadores poderão sincronizar as definições e dados da aplicação em todos os dispositivos** -por predefinição, esta definição está definida como **NONE**. A seleção de utilizadores específicos ou grupos ou todos os permite as ao utilizador definições e dados da aplicação a sincronização entre os dispositivos Windows 10. Saiba mais sobre o funcionamento da sincronização no Windows 10.
Esta opção é uma capacidade de premium disponível através de produtos como o Azure AD Premium ou Enterprise Mobility Suite (EMS).
 




## <a name="locate-devices"></a>Localizar os dispositivos

Tem duas opções para localizar os dispositivos registados e associado ao:

- **Todos os dispositivos** no **gerir** secção o **dispositivos** página  

    ![Todos os dispositivos](./media/device-management-azure-portal/41.png)


- **Dispositivos** no **gerir** secção um **utilizador** página
 
    ![Todos os dispositivos](./media/device-management-azure-portal/43.png)



Com ambas as opções, pode obter uma vista que:


- Permite-lhe pesquisar dispositivos com o nome a apresentar como filtro.

- Fornece uma descrição detalhada dos dispositivos registados e associados

- Permite-lhe realizar tarefas comuns de gestão do dispositivo
   

![Todos os dispositivos](./media/device-management-azure-portal/51.png)


## <a name="device-management-tasks"></a>Tarefas de gestão de dispositivos

Como administrador, pode gerir os dispositivos registados ou associados. Esta secção fornece informações sobre tarefas comuns de gestão de dispositivos.


### <a name="manage-an-intune-device"></a>Gerir um dispositivo do Intune

Se for um administrador do Intune, pode gerir dispositivos marcados como **Microsoft Intune**. Um administrador pode ver adicionais do dispositivo 

![Gerir um dispositivo do Intune](./media/device-management-azure-portal/31.png)


### <a name="enable--disable-an-azure-ad-device"></a>Ativar / desativar um dispositivo do Azure AD

Para ativar / desativar um dispositivo, tem duas opções:

- O menu de tarefas ("...") no **todos os dispositivos** página

    ![Gerir um dispositivo do Intune](./media/device-management-azure-portal/71.png)

- Barra de ferramentas no **dispositivos** página

    ![Gerir um dispositivo do Intune](./media/device-management-azure-portal/32.png)


**Comentários:**

- Tem de ser um administrador global no Azure AD para ativar / desativar um dispositivo. 
- Desativar um dispositivo impede que um dispositivo aceder aos seus recursos do Azure AD. 



### <a name="delete-an-azure-ad-device"></a>Eliminar um dispositivo do Azure AD

Para eliminar um dispositivo, tem duas opções:

- O menu de tarefas ("...") no **todos os dispositivos** página

    ![Gerir um dispositivo do Intune](./media/device-management-azure-portal/72.png)

- Barra de ferramentas no **dispositivos** página

    ![Eliminar um dispositivo](./media/device-management-azure-portal/34.png)


**Comentários:**

- Tem de ser um administrador global no Azure AD para eliminar um dispositivo.  

- Eliminar um dispositivo:
 
    - Impede que um dispositivo de aceder aos seus recursos do Azure AD. 

    - Remove todos os detalhes que estão ligados ao dispositivo, por exemplo, as chaves do BitLocker para dispositivos Windows.  

    - Representa uma atividade não recuperável e não é recomendado, exceto se for necessário.

Se um dispositivo é gerido por outra autoridade de gestão (por exemplo, o Microsoft Intune), certifique-se de que o dispositivo foi limpo / extinto antes de eliminar o dispositivo no Azure AD.

 


### <a name="view-or-copy-device-id"></a>Ver ou copie o ID de dispositivo

Pode utilizar um ID de dispositivo para verificar os detalhes de ID de dispositivo no dispositivo ou através do PowerShell durante a resolução de problemas. Para aceder a opção de cópia, clique no dispositivo.

![Ver um ID de dispositivo](./media/device-management-azure-portal/35.png)
  

### <a name="view-or-copy-bitlocker-keys"></a>Ver ou copie as chaves do BitLocker

Se for um administrador, pode ver e copiar as chaves do BitLocker para ajudar os utilizadores recuperem a respetiva unidade encriptada. Estas chaves só estão disponíveis para dispositivos Windows que são encriptados e tem as suas chaves armazenados no Azure AD. Pode copiar estas chaves quando aceder aos detalhes do dispositivo.
 
![Ver as chaves do BitLocker](./media/device-management-azure-portal/36.png)



## <a name="audit-logs"></a>Registos de auditoria


Atividades de dispositivo estão disponíveis através de registos de atividade. Isto inclui atividades acionadas pelo serviço de registo de dispositivos e utilizadores:

- Criação do dispositivo e adicionar os proprietários / utilizadores do dispositivo

- Alterações às definições do dispositivo

- Operações de dispositivo como eliminar ou atualizar um dispositivo
 
O ponto de entrada para os dados de auditorias é **registos de auditoria** no **atividade** secção o **dispositivos** página.

![Registos de auditoria](./media/device-management-azure-portal/61.png)


Um registo de auditoria tem uma vista de lista predefinida que mostra:

- A data e hora da ocorrência

- os destinos

- o iniciador / ator (quem) de uma atividade

- a atividade (o que)

![Registos de auditoria](./media/device-management-azure-portal/63.png)

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.
 
![Registos de auditoria](./media/device-management-azure-portal/64.png)


Para limitar os dados comunicados a um nível que funcione para si, pode filtrar os dados de auditoria através dos seguintes campos:

- Categoria
- Tipo de recurso de atividade
- Atividade
- Intervalo de datas
- Alvo
- Iniciado pelo (Ator)

Para além dos filtros, pode procurar entradas específicas.

![Registos de auditoria](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Passos seguintes

* [Introdução à gestão de dispositivos no Azure Active Directory](device-management-introduction.md)



