---
title: "Auditoria e registo - ferramenta de modelação de ameaça Microsoft - Azure | Microsoft Docs"
description: "Mitigações ameaças exposta na ferramenta de modelação de ameaça"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 3f1933fc59862eca7ae6ee40bbd5136e449e5cf1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Período de segurança: Auditoria e registo | Mitigações 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Identificar confidenciais entidades na sua solução e implementar a auditoria de alteração](#sensitive-entities)</li></ul> |
| **Aplicação Web** | <ul><li>[Certifique-se de que a auditoria e registo é imposto na aplicação](#auditing)</li><li>[Certifique-se de que a rotação do registo e separação estão no local](#log-rotation)</li><li>[Certifique-se de que a aplicação não regista dados confidenciais do utilizador](#log-sensitive-data)</li><li>[Certifique-se de que os ficheiros de registo de auditoria e têm acesso restrito](#log-restricted-access)</li><li>[Certifique-se de que os eventos de gestão de utilizador são registados](#user-management)</li><li>[Certifique-se de que o sistema tem defesas integradas contra utilização indevida](#inbuilt-defenses)</li><li>[Ativar o registo de diagnóstico para web apps no App Service do Azure](#diagnostics-logging)</li></ul> |
| **Base de Dados** | <ul><li>[Certifique-se de que a auditoria de início de sessão está ativada no SQL Server](#identify-sensitive-entities)</li><li>[Ativar a deteção de ameaças no SQL do Azure](#threat-detection)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Utilize a análise de armazenamento do Azure para auditar o acesso do Storage do Azure](#analytics)</li></ul> |
| **WCF** | <ul><li>[Implementar o registo suficiente](#sufficient-logging)</li><li>[Implementar o processamento da falha de auditoria suficientes](#audit-failure-handling)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que a auditoria e registo é aplicada a Web API](#logging-web-api)</li></ul> |
| **Gateway de campo de IoT** | <ul><li>[Certifique-se de que é aplicada adequada de auditoria e registo no Gateway de campo](#logging-field-gateway)</li></ul> |
| **Gateway de nuvem do IoT** | <ul><li>[Certifique-se de que é aplicada adequada de auditoria e registo no Gateway de nuvem](#logging-cloud-gateway)</li></ul> |

## <a id="sensitive-entities"></a>Identificar confidenciais entidades na sua solução e implementar a auditoria de alteração

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | Identificar as entidades na sua solução com dados confidenciais e implementar a auditoria às alterações dessas entidades e campos |

## <a id="auditing"></a>Certifique-se de que a auditoria e registo é imposto na aplicação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | Ative a auditoria e registo em todos os componentes. Os registos de auditoria devem capturar o contexto de utilizador. Identifique todos os eventos importantes e os eventos de registo. Implementar o registo centralizado |

## <a id="log-rotation"></a>Certifique-se de que a rotação do registo e separação estão no local

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | <p>Rotação do registo é um processo automatizado utilizado na administração do sistema em que os ficheiros de registo com a data de estão arquivados. Servidores que executem aplicações de grandes dimensões, muitas vezes, registar cada pedido: face bulky registos, rotação do registo é uma forma de limitar o tamanho total dos registos permitindo ainda uma análise de eventos recentes. </p><p>Inicie sessão separação basicamente significa que tem de armazenar o início de sessão ficheiros numa partição diferente como onde a SO/aplicação está em execução no modo avert um ataque de recusa de serviço ou a desatualização da sua aplicação, o desempenho dele</p>|

## <a id="log-sensitive-data"></a>Certifique-se de que a aplicação não regista dados confidenciais do utilizador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | <p>Certifique-se de que não iniciar sessão quaisquer dados confidenciais que um utilizador submete para o seu site. Verifique a existência de registo intencional, bem como os efeitos secundários causados por problemas de design. Exemplos de dados confidenciais incluem:</p><ul><li>Credenciais do utilizador</li><li>Número de segurança social ou outras informações de identificação</li><li>Números de cartão de crédito ou outras informações financeiras</li><li>Informações de estado de funcionamento</li><li>As chaves privadas ou outros dados que pode ser utilizados para desencriptar informações encriptadas</li><li>Informações de sistema ou aplicação que podem ser utilizadas para atacar forma mais eficaz a aplicação</li></ul>|

## <a id="log-restricted-access"></a>Certifique-se de que os ficheiros de registo de auditoria e têm acesso restrito

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | <p>Verificar para garantir que os direitos de acesso nos ficheiros de registo estão definidos corretamente. Contas de aplicação devem ter acesso só de escrita e operadores e suporte técnico deve ter acesso só de leitura, conforme necessário.</p><p>Contas de administradores são as únicas contas que devem ter acesso total. Verifique a ACL do Windows nos ficheiros de registo para garantir que estão corretamente restritos:</p><ul><li>Contas de aplicação devem ter acesso só de escrita</li><li>Os operadores e suporte técnico deve ter acesso só de leitura conforme necessário</li><li>Os administradores são as únicas contas que devem ter acesso total</li></ul>|

## <a id="user-management"></a>Certifique-se de que os eventos de gestão de utilizador são registados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | <p>Certifique-se de que a aplicação monitoriza os eventos de gestão de utilizador, tais como inícios de sessão do utilizador com êxito ou falhada, que repõe palavras-passe, as alterações de palavra-passe, o bloqueio da conta, o registo de utilizador. Isto ajuda a detetar e reagir a comportamentos potencialmente suspeitos a fazer. Também permite a recolha de dados de operações; Por exemplo, para controlar quem está a aceder a aplicação</p>|

## <a id="inbuilt-defenses"></a>Certifique-se de que o sistema tem defesas integradas contra utilização indevida

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | <p>Devem ser implementados controlos que emitir exceção de segurança em caso de utilização indevida de aplicação. Por exemplo, se a validação de entrada for no local e um atacante tentar inserir código malicioso que não coincide com regex, uma exceção de segurança pode ser emitida que pode ser um facto de utilização indevida do sistema</p><p>Por exemplo, é recomendado ter exceções de segurança com sessão iniciadas e as ações executadas para os seguintes problemas:</p><ul><li>Entrada de validação</li><li>Violações CSRF</li><li>Força bruta (limite superior para o número de pedidos por utilizador por recurso)</li><li>Violações de carregamento de ficheiros</li><ul>|

## <a id="diagnostics-logging"></a>Ativar o registo de diagnóstico para web apps no App Service do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | EnvironmentType - Azure |
| **Referências**              | N/D  |
| **Passos** | <p>O Azure oferece incorporado diagnóstico para ajudar a depurar um serviço de aplicações web de aplicação. Também se aplica a aplicações API e aplicações móveis. Web apps do App Service fornecem a funcionalidade de diagnóstico para informações de registo do servidor web e a aplicação web.</p><p>Estes são logicamente separados para o diagnóstico do servidor web e o application diagnostics</p>|

## <a id="identify-sensitive-entities"></a>Certifique-se de que a auditoria de início de sessão está ativada no SQL Server

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Configurar a auditoria de início de sessão](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Passos** | <p>A auditoria de início de sessão do servidor de base de dados tem de estar ativada para detetar/Confirmar palavra-passe a deteção de ataques. É importante capturar o início de sessão falhadas. Captura de ambas as tentativas de início de sessão com êxito e oferece o benefício adicional durante as investigações forenses</p>|

## <a id="threat-detection"></a>Ativar a deteção de ameaças no SQL do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure |
| **Atributos**              | Versão do SQL Server - V12 |
| **Referências**              | [Introdução à deteção de ameaças de base de dados do SQL Server](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Passos** |<p>A deteção de ameaças Deteta atividades de base de dados anómalas, indicando potenciais ameaças de segurança para a base de dados. Fornece uma nova camada de segurança, o que permite que os clientes detetar e reagir a potenciais ameaças à medida que ocorrem, fornecendo alertas de segurança em atividades anómalas.</p><p>Os utilizadores podem explorar os suspeitos eventos de auditoria de base de dados SQL do Azure a utilizar para determinar se eles resultam de uma tentativa de aceder, infringir ou exploram os dados na base de dados.</p><p>A deteção de ameaças torna simples para resolver potenciais ameaças à base de dados sem a necessidade de ser um especialista de segurança ou faça a gestão de sistemas de monitorização de segurança avançada</p>|

## <a id="analytics"></a>Utilize a análise de armazenamento do Azure para auditar o acesso do Storage do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D |
| **Referências**              | [Análise de armazenamento a utilizar para monitorizar o tipo de autorização](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Passos** | <p>Para cada conta de armazenamento, um pode ativar a análise de armazenamento do Azure efetuar o registo e armazenar dados de métricas. Os registos de análise de armazenamento fornecem informações importantes como método de autenticação utilizado por outra quando acedem ao armazenamento.</p><p>Isto pode ser realmente útil se totalmente são guarding acesso ao armazenamento. Por exemplo, no Blob Storage pode definir todos os contentores para private e implementar a utilização de um serviço SAS em toda as suas aplicações. Em seguida, pode verificar os registos regularmente para ver se os blobs são acedidos utilizando as chaves de conta de armazenamento, o que podem indicar uma violação de segurança, ou se os blobs são públicos, mas não deve ser.</p>|

## <a id="sufficient-logging"></a>Implementar o registo suficiente

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET framework |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Unido](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Passos** | <p>A falta de um registo de auditoria correta depois de um incidente de segurança pode hamper esforços de análise forenses. Windows Communication Foundation (WCF) oferece a capacidade de registar as tentativas de autenticação com êxito e/ou falhada.</p><p>A registar as tentativas de autenticação falhada, pode avisar os administradores de potenciais ataques de força bruta. Da mesma forma, registo de eventos de autenticação com êxito pode fornecer um registo de auditoria útil quando uma legítima conta for comprometida. Ativar a funcionalidade de auditoria de segurança de serviço do WCF |

### <a name="example"></a>Exemplo
Segue-se um exemplo de configuração com a auditoria ativada
```
<system.serviceModel>
    <behaviors>
        <serviceBehaviors>
            <behavior name=""NewBehavior"">
                <serviceSecurityAudit auditLogLocation=""Default""
                suppressAuditFailure=""false"" 
                serviceAuthorizationAuditLevel=""SuccessAndFailure""
                messageAuthenticationAuditLevel=""SuccessAndFailure"" />
                ...
            </behavior>
        </servicebehaviors>
    </behaviors>
</system.serviceModel>
```

## <a id="audit-failure-handling"></a>Implementar o processamento da falha de auditoria suficientes

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET framework |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Unido](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Passos** | <p>Solução programada está configurada para não gerar uma exceção quando ocorre uma falha ao escrever no registo de auditoria. Se o WCF está configurado para não gerar uma exceção quando for não é possível escrever um registo de auditoria, o programa não será notificado da falha e a auditoria de eventos de segurança críticas poderá não ocorrer.</p>|

### <a name="example"></a>Exemplo
O `<behavior/>` elemento do ficheiro de configuração de WCF abaixo dá instruções ao WCF não notificar a aplicação quando WCF não é possível escrever um registo de auditoria.
````
<behaviors>
    <serviceBehaviors>
        <behavior name="NewBehavior">
            <serviceSecurityAudit auditLogLocation="Application"
            suppressAuditFailure="true"
            serviceAuthorizationAuditLevel="Success"
            messageAuthenticationAuditLevel="Success" />
        </behavior>
    </serviceBehaviors>
</behaviors>
````
Configure WCF para notificar o programa sempre que é não é possível escrever um registo de auditoria. O programa deve ter um esquema de notificação alternativo alerta a organização que os registos de auditoria não são mantidas. 

## <a id="logging-web-api"></a>Certifique-se de que a auditoria e registo é aplicada a Web API

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Ative a auditoria e registo em APIs da Web. Os registos de auditoria devem capturar o contexto de utilizador. Identifique todos os eventos importantes e os eventos de registo. Implementar o registo centralizado |

## <a id="logging-field-gateway"></a>Certifique-se de que é aplicada adequada de auditoria e registo no Gateway de campo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo de IoT | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Quando vários dispositivos se ligam a um Gateway de campo, certifique-se de que as tentativas de ligação e o estado de autenticação (êxito ou falha) para os dispositivos individuais são registados e mantidos no Gateway de campo.</p><p>Além disso, nos casos em que o Gateway de campo é manter as credenciais do IoT Hub para os dispositivos individuais, certifique-se de que a auditoria é executada quando estas credenciais são obtidas. Desenvolva um processo periodicamente carregar os registos para o IoT Hub/armazenamento do Azure para a retenção de prazo longo.</p> |

## <a id="logging-cloud-gateway"></a>Certifique-se de que é aplicada adequada de auditoria e registo no Gateway de nuvem

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem do IoT | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Introdução à monitorização de operações de IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Passos** | <p>Conceção para recolher e armazenar dados de auditoria recolhidos através de monitorização de operações do IoT Hub. Ative monitorização seguintes categorias:</p><ul><li>Operações de identidade de dispositivo</li><li>Comunicações de dispositivo-nuvem</li><li>Comunicações de nuvem para o dispositivo</li><li>Ligações</li><li>Carregamentos de ficheiros</li></ul>|