<properties
    pageTitle="Como migrar Logic Apps para o esquema versão 2015-08-01-pré-visualização | App Service do Microsoft Azure"
    description="Pode migrar facilmente as Logic Apps para a versão mais recente do esquema. Basta seguir estes passos."
    services="app-service\logic"
    documentationCenter=""
    authors="MSFTMAN"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
    ms.service="app-service-logic"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/20/2016"
    ms.author="deonhe"/>

# Como migrar Logic Apps para o esquema versão 2015-08-01-pré-visualização

Para mover as Logic Apps existentes para o novo esquema, efetue o seguinte:  
1. Abra a aplicação lógica no Portal do Azure  
2. Clique em Atualizar Esquema:

 ![API Icon][step1]   
A página Atualizar Esquema apresenta e fornece uma ligação para um documento que fornece detalhes sobre as melhorias efetuadas no novo esquema:
 ![API Icon][step2]

>[AZURE.NOTE] Ao selecionar **Atualizar Esquema**, os passos de migração são automaticamente executados e o código de saída é fornecido. Pode utilizar este código para atualizar a definição. No entanto, certifique-se de que segue as melhores práticas de codificação, tal como as descritas na secção **Melhores práticas** abaixo.

## Melhores práticas para migrar as Logic Apps para a versão mais recente do esquema:  

- Copie o script migrado para uma nova Aplicação Lógica, não substitua o antigo até ter concluído os testes e ter confirmado que a aplicação migrada funciona conforme esperado.
- Teste a aplicação lógica **antes** de passar para a produção
- Após concluir a migração, comece a atualizar as Logic Apps para utilizar as [APIs geridas](./apis-list.md) sempre que possível. Por exemplo, pode começar a utilizar o Dropbox v2, sempre que estiver a utilizar o DropBox v1.


## Passos seguintes
-  [Saiba como migrar manualmente as Logic Apps](../app-service-logic/app-service-logic-schema-2015-08-01.md)


<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png









<!---HONumber=Jun16_HO2-->


