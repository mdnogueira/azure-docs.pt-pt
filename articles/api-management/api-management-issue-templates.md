---
title: Emitir modelos na API Management do Azure | Microsoft Docs
description: "Saiba como personalizar o conteúdo das páginas problema no portal do programador na API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 47da4bb2-426e-4e53-8fa7-214ee2e3ab37
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 9d13a146e94328b8ac57dc1036676328a4bea9d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="issue-templates-in-azure-api-management"></a>Emitir modelos na API Management do Azure
Gestão de API do Azure fornece a capacidade para personalizar os conteúdos de páginas de portal de programador utilizando um conjunto de modelos que configurar o respetivo conteúdo. Utilizando [DotLiquid](http://dotliquidmarkup.org/) sintaxe e o editor à sua escolha, tal como [DotLiquid para Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), e um conjunto de fornecido localizado [recursos de cadeia](api-management-template-resources.md#strings), [recursos de glifo](api-management-template-resources.md#glyphs), e [controlos de página](api-management-page-controls.md), tem uma enorme flexibilidade para configurar o conteúdo das páginas como julgar utilizando estes modelos.  
  
 Os modelos nesta secção permitem-lhe personalizar os conteúdos das páginas problema no portal do programador.  
  
-   [Lista de problema](#IssueList)  
  
> [!NOTE]
>  Modelos predefinidos de exemplo estão incluídos na documentação do seguinte, mas estão sujeitos a alterações devido a melhoramentos contínuos. Pode ver os modelos predefinidos em direto no portal do programador, navegando para os modelos de individuais pretendidos. Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do Programador de API Management utilizando modelos](api-management-developer-portal-templates.md).  
  
##  <a name="IssueList"></a>Lista de problema  
 O **lista problema** modelo permite-lhe personalizar o corpo da página lista problema no portal do programador.  
  
 ![Portal de programador da lista de emitir](./media/api-management-issue-templates/APIM-Issue-List-Developer-Portal.png "APIM Portal do Programador de lista de problema")  
  
### <a name="default-template"></a>Modelo predefinido  
  
```xml  
<div class="row">  
  <div class="col-md-9">  
    <h2>{% localized "IssuesStrings|WebIssuesIndexTitle" %}</h2>  
  </div>  
</div>  
<div class="row">  
  <div class="col-md-12">  
    {% if issues.size > 0 %}  
    <ul class="list-unstyled">  
      {% capture reportedBy %}{% localized "IssuesStrings|WebIssuesStatusReportedBy" %}{% endcapture %}  
      {% assign replaceString0 = '{0}' %}  
      {% assign replaceString1 = '{1}' %}  
      {% for issue in issues %}  
      <li>  
        <h3>  
          <a href="/issues/{{issue.id}}">{{issue.title}}</a>  
        </h3>  
        <p>{{issue.description}}</p>  
        <em>  
          {% capture state %}{{issue.issueState}}{% endcapture %}  
          {% capture devName %}{{issue.subscriptionDeveloperName}}{% endcapture %}  
          {% capture str1 %}{{ reportedBy | replace : replaceString0, state }}{% endcapture %}  
          {{ str1 | replace : replaceString1, devName }}  
          <span class="UtcDateElement">{{ issue.reportedOn | date: "r" }}</span>  
        </em>  
      </li>  
      {% endfor %}  
    </ul>  
    <paging-control></paging-control>  
    {% else %}  
    {% localized "CommonResources|NoItemsToDisplay" %}  
    {% endif %}  
    {% if canReportIssue %}  
    <a class="btn btn-primary" id="createIssue" href="/Issues/Create">{% localized "IssuesStrings|WebIssuesReportIssueButton" %}</a>  
    {% elsif isAuthenticated %}  
    <hr />  
    <p>{% localized "IssuesStrings|WebIssuesNoActiveSubscriptions" %}</p>  
    {% else %}  
    <hr />  
    <p>  
      {% capture signIntext %}{% localized "IssuesStrings|WebIssuesNotSignin" %}{% endcapture %}  
      {% capture link %}<a href="/signin">{% localized "IssuesStrings|WebIssuesSignIn" %}</a>{% endcapture %}  
      {{ signIntext | replace : replaceString0, link }}  
    </p>  
    {% endif %}  
  </div>  
</div>  
```  
  
### <a name="controls"></a>Controlos  
 O `Issue list` modelo pode utilizar o seguinte [página controlos](api-management-page-controls.md).  
  
-   [controlo de paginação](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Modelo de dados  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|Problemas|Coleção de [problema](api-management-template-data-model-reference.md#Issue) entidades.|Os problemas visíveis ao utilizador atual.|  
|Paginação|[Paginação](api-management-template-data-model-reference.md#Paging) entidade.|As informações de paginação para a coleção de aplicações.|  
|IsAuthenticated|Valor booleano|Se o utilizador atual está assinado no portal do programador.|  
|CanReportIssues|Valor booleano|Indica se o utilizador atual tem permissões para um problema de ficheiros.|  
|Pesquisa|Cadeia|Esta propriedade foi preterida e não deve ser utilizada.|  
  
### <a name="sample-template-data"></a>Dados de exemplo do modelo  
  
```json  
{  
    "Issues": [  
        {  
            "Id": "5702b68bb16653124c8f9ba7",  
            "ApiId": "570275f1b16653124c8f9ba3",  
            "Title": "I couldn't figure out how to connect my application to the API",  
            "Description": "I'm having trouble connecting my application to the backend API.",  
            "SubscriptionDeveloperName": "Clayton",  
            "IssueState": "Proposed",  
            "ReportedOn": "2016-04-04T18:46:35.64",  
            "Comments": null,  
            "Attachments": null,  
            "Services": null  
        }  
    ],  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 1,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "IsAuthenticated": true,  
    "CanReportIssue": true,  
    "Search": null  
}  
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do Programador de API Management utilizando modelos](api-management-developer-portal-templates.md).