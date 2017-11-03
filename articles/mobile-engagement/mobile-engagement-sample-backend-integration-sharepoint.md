---
title: "Azure Mobile Engagement – integração de back-end"
description: Ligar o Azure Mobile Engagement com um back-end do SharePoint para criar campanhas do SharePoint
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 06297b43-579f-46e6-8a58-961a68f9aa09
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: d49f1094f4c3f170f3618f3e19e42266f9ae8858
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement---api-integration"></a>Azure Mobile Engagement – integração de API
Num sistema marketing automatizado, criar e ativar as campanhas de marketing também ocorrem automaticamente. Para esta finalidade - Azure Mobile Engagement permite criar essas campanhas de marketing automatizadas com APIs bem. 

Normalmente os clientes utilizar a interface de front-end do Mobile Engagement para criar anúncios/inquérito etc como parte do respetivas campanhas de marketing. No entanto às campanhas de marketing ficam maduros, for necessário para tirar partido dos dados bloqueados nos sistemas de back-end (como qualquer sistema CRM ou sistema CMS, como o SharePoint), de modo a que é possível criar um pipeline totalmente automatizado que cria as campanhas no dinamicamente com base nos dados fluir em dos sistemas de back-end do Mobile Engagement. 

![][5]

Este tutorial passa através de um cenário em que um utilizador de negócio do SharePoint preenche uma lista do SharePoint com dados de marketing e um processo automatizado escolherá itens na lista e estabelece ligação com o sistema de Mobile Engagement utilizando as APIs REST disponível para criar uma campanha de marketing a partir dos dados do SharePoint. 

> [!IMPORTANT]
> Em geral, pode utilizar este exemplo como ponto de partida para compreender como chamar qualquer API de REST do Mobile Engagement como fornece detalhes sobre os dois aspetos fundamentais de chamar as APIs - parâmetros de autenticação e passando. 
> 
> 

## <a name="sharepoint-integration"></a>Integração do SharePoint
1. Eis o aspeto lista do SharePoint de exemplo. **Título**, **categoria**, **NotificationTitle**, **mensagem** e **URL** são utilizados para criar o anúncio. Há uma coluna chamada **IsProcessed** que é utilizada pelo processo de automatização de exemplo sob a forma de um programa de consola. Pode a executar esta consola do programa como um trabalho Web do Azure para que pode agendá-la ou pode utilizar diretamente o fluxo de trabalho do SharePoint para o programa de criar e ativar o anúncio, quando um item é inserido na lista do SharePoint. Neste exemplo, vamos utilizar o programa de consola que passa através de itens no SharePoint listar e criar o anúncio no Azure Mobile Engagement para cada um deles e, finalmente, marca a **IsProcessed** sinalizador VERDADEIRO na criação de anúncio com êxito.
   
    ![][1]
2. Estamos a utilizar o código do exemplo *autenticação remota no SharePoint Online utilizando o modelo de objeto de cliente* [aqui](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c) para autenticar com a lista do SharePoint.
3. Uma vez autenticado, vamos percorrer os itens de lista para saber de qualquer itens criados recentemente (que terão **IsProcessed** = false). 
   
         static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);
   
                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();
   
                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;
   
                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;
   
                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";
   
                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## <a name="mobile-engagement-integration"></a>Integração do Mobile Engagement
1. Depois de encontrámos um item que requer processamento --extrair as informações necessárias para criar um anúncio do item da lista e chamada `CreateAzMECampaign` criá-la e, subsequentemente, `ActivateAzMECampaign` para o ativar. Estes são essencialmente chamadas da REST API chamar para o back-end do Mobile Engagement. 
2. As APIs REST do Mobile Engagement requerem um **cabeçalho de autorização HTTP de esquema de autenticação básica** que é composto pelo `ApplicationId` e `ApiKey` que obtém a partir do portal do Azure. Certifique-se de que está a utilizar a chave a partir de **chaves de api** secção e *não* do **sdk chaves** secção. 
   
   ![][2]
   
       static string CreateAuthZHeader()
       {
           string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
           return BasicAuthzHeaderString;
       }
   
       static public string EncodeTo64(string toEncode)
       {
           byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
           string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
           return returnValue;
       }  
3. Para criar o anúncio escreva campanha - consulte o [documentação](https://msdn.microsoft.com/library/azure/mt683750.aspx). Tem de certificar-se de que especifica a campanha `kind` como *anúncio* e [payload](https://msdn.microsoft.com/library/azure/mt683751.aspx) e passou-a como FormUrlEncodedContent. 
   
        static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";
   
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }
4. Assim que tiver o anúncio criado, verá algo semelhante ao seguinte no portal do Mobile Engagement (tenha em atenção que o estado = Rascunho e ativado = n/d)
   
    ![][3]
5. `CreateAzMECampaign`cria uma campanha de anúncio e devolve o respetivo Id para o autor da chamada. `ActivateAzMECampaign`requer este Id como parâmetro para ativar a campanha. 
   
        static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }
6. Assim que tiver o anúncio ativado, verá algo semelhante ao seguinte no portal do Mobile Engagement:
   
    ![][4]
7. Assim que a campanha obtém ativada, quaisquer dispositivos que satisfazem o critério na campanha irão começar a ver as notificações. 
8. Também irá reparar que o item da lista marcado com IsProcessed = false foi definido como verdadeiro quando a campanha de anúncio é criada.  

Este exemplo criar uma campanha de anúncio simples especificando principalmente as propriedades necessárias. Pode personalizá-la como pode partir do portal através das informações [aqui](https://msdn.microsoft.com/library/azure/mt683751.aspx). 

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png



