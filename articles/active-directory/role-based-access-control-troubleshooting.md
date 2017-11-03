---
title: Resolver problemas do RBAC do Azure | Microsoft Docs
description: "Obter ajuda com problemas ou questões sobre os recursos de controlo de acesso baseado em funções."
services: azure-portal
documentationcenter: na
author: andredm7
manager: femila
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.openlocfilehash: 407c030ea159915d4d7ac21760a3d17ec2204372
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="role-based-access-control-troubleshooting"></a>Resolução de problemas de controlo de acesso baseado em funções

Este artigo de documento responde a questões recorrentes sobre os direitos de acesso específicas que são concedidos com as funções, para que saiba o que esperar quando utiliza as funções no portal do Azure e pode resolver problemas de acesso. Estas três funções abrangem todos os tipos de recursos:

* Proprietário  
* Contribuinte  
* Leitor  

Os proprietários e contribuintes têm acesso total à experiência de gestão, mas um contribuinte não é possível conceder acesso a outros utilizadores ou grupos. Coisas obter um pouco mais interessantes com a função de leitor, pelo que é onde irá gastamos algum tempo. Consulte o [artigo get-started do controlo de acesso baseado em funções](role-based-access-control-configure.md) para obter detalhes sobre como conceder acesso.

## <a name="app-service-workloads"></a>Cargas de trabalho de serviço de aplicações
### <a name="write-access-capabilities"></a>Capacidades de acesso de escrita
Se conceder um acesso de só de leitura de utilizador para uma aplicação web única, algumas funcionalidades estão desativadas que não seria de esperar. As seguintes capacidades de gestão requerem **escrever** aceder a uma aplicação web (contribuinte ou proprietário) e não estão disponíveis em qualquer cenário de só de leitura.

* Comandos (como iniciar, parar, etc.)
* Alterar definições como a configuração geral, as definições de dimensionamento, as definições de cópia de segurança e as definições de monitorização
* Aceder a credenciais de publicação e outros segredos, como as definições de aplicação e as cadeias de ligação
* Registos de transmissão em fluxo
* Configuração de registos de diagnóstico
* Consola (linha de comandos)
* Implementações de Active Directory e recentes (para a implementação contínua de local git)
* Estimado gastam
* Testes Web
* Rede virtual (visível apenas para um leitor se anteriormente tiver sido configurada uma rede virtual por um utilizador com acesso de escrita).

Se não é possível aceder a qualquer um destes mosaicos, terá de peça ao seu administrador de acesso de Contribuidor na aplicação web.

### <a name="dealing-with-related-resources"></a>Lidar com recursos relacionados
As Web apps são complicou pela presença de alguns diferentes recursos interplay. Segue-se um grupo de recursos típico com alguns sites:

![Grupo de recursos de aplicação Web](./media/role-based-access-control-troubleshooting/website-resource-model.png)

Como resultado, se conceder alguém acesso apenas na aplicação web, muitas das funcionalidades no painel do Web site no portal do Azure está desativado.

Estes itens requerem **escrever** acesso a **plano do App Service** que corresponde ao seu Web site:  

* Visualizar a aplicação web do escalão de preço (gratuita ou Standard)  
* Configuração de dimensionamento (número de instâncias, tamanho da máquina virtual, definições de dimensionamento automático)  
* Quotas (storage, largura de banda, CPU)  

Estes itens requerem **escrever** acesso a totalidade **grupo de recursos** que contém o seu Web site:  

* Certificados SSL e os enlaces (certificados SSL podem ser partilhados entre sites no mesmo grupo de recursos e geolocalização)  
* Regras de alertas  
* Definições de dimensionamento automático  
* Componentes do Application insights  
* Testes Web  

## <a name="virtual-machine-workloads"></a>Cargas de trabalho de máquina virtual
Muito como web Apps, algumas funcionalidades no painel da máquina virtual requerem acesso de escrita para a máquina virtual ou a outros recursos no grupo de recursos.

Máquinas virtuais relacionadas com os nomes de domínio, redes virtuais, contas de armazenamento e regras de alertas.

Estes itens requerem **escrever** acesso a **Máquina Virtual**:

* Pontos Finais  
* Endereços IP  
* Discos  
* Extensões  

Esses requisitos requerem **escrever** acesso a ambos os **Máquina Virtual**e o **grupo de recursos** (juntamente com o nome de domínio) que está a ser:  

* Conjunto de disponibilidade  
* Conjunto com balanceamento de carga  
* Regras de alertas  

Se não é possível aceder a qualquer um destes mosaicos, peça ao seu administrador de acesso de contribuinte ao grupo de recursos.

## <a name="see-more"></a>Ver mais
* [Controlo de acesso baseado em funções](role-based-access-control-configure.md): começar a utilizar o RBAC no portal do Azure.
* [Funções incorporadas](role-based-access-built-in-roles.md): obter detalhes sobre as funções que vêm padrão no RBAC.
* [Funções personalizadas no Azure RBAC](role-based-access-control-custom-roles.md): Saiba como criar funções personalizadas para se ajustarem às suas necessidades de acesso.
* [Criar um relatório de histórico de alterações de acesso](role-based-access-control-access-change-history-report.md): manter um registo dos alteração atribuições de funções no RBAC.

