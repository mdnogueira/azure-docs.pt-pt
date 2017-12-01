---
title: "Reparametrização de um serviço de web clássico | Microsoft Docs"
description: "Saiba como programaticamente reparametrização de um modelo e atualizar o serviço web para utilizar o modelo treinado recentemente no Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondlaghaeian
editor: 
ms.assetid: e36e1961-9e8b-4801-80ef-46d80b140452
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.openlocfilehash: ab3c0b5776f9a32ab2703f462d58071f7bfd52ff
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="retrain-a-classic-web-service"></a>Voltar a preparar um Serviço Web Clássico
O serviço Web preditiva implementou é a predefinição da classificação de ponto final. Pontos finais predefinidos são mantidos sincronizados com a formação original e a classificação de experimentações, e, por conseguinte, não é possível substituir o modelo treinado para o ponto final predefinido. Para a reparametrização do serviço web, tem de adicionar um novo ponto final para o serviço web. 

## <a name="prerequisites"></a>Pré-requisitos
Tem de ter de configurar uma experimentação de preparação e de uma experimentação preditiva conforme mostrado no [Machine Learning reparametrização dos modelos programáticos](retrain-models-programmatically.md). 

> [!IMPORTANT]
> A experimentação preditiva tem de ser implementada como um clássico de machine learning serviço web. 
> 
> 

Para obter informações adicionais sobre implementar os serviços web, consulte [implementar um serviço web do Azure Machine Learning](publish-a-machine-learning-web-service.md).

## <a name="add-a-new-endpoint"></a>Adicionar um novo ponto final
O serviço Web preditiva que implementou contém uma predefinição a classificação de ponto final que é mantido sincronizado com a formação original e a classificação de modelo treinado experimentações. Para atualizar o seu serviço web com um novo modelo treinado, tem de criar um novo ponto final da classificação. 

Para criar um novo ponto de final classificação, o serviço Web preditiva que pode ser atualizada com o modelo treinado:

> [!NOTE]
> Lembre-se de que está a adicionar o ponto final para o serviço Web preditiva, não o serviço Web de formação. Se tiver implementado corretamente uma formação e um serviço Web preditiva, deverá ver dois serviços de web separado listados. O serviço Web preditiva deve terminar com "[exp preditiva.]".
> 
> 

Existem duas formas na qual pode adicionar um novo ponto final para um serviço web:

1. Através de Programação
2. Utilizar o portal de serviços Web do Microsoft Azure

### <a name="programmatically-add-an-endpoint"></a>Através de programação adicionar um ponto final
Pode adicionar pontos finais de classificação com o código de exemplo fornecido neste [repositório do github](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).

### <a name="use-the-microsoft-azure-web-services-portal-to-add-an-endpoint"></a>Utilizar o portal de serviços Web do Microsoft Azure para adicionar um ponto final
1. No Machine Learning Studio, a coluna de navegação esquerdo, clique em serviços Web.
2. Na parte inferior do dashboard do serviço web, clique em **pré-visualização de pontos finais de gerir**.
3. Clique em **Adicionar**.
4. Escreva um nome e descrição para o novo ponto final. Selecione o nível de registo e se os dados de exemplo estão ativados. Para obter mais informações sobre o registo, consulte [ativar o registo de serviços web do Machine Learning](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Atualizar o modelo treinado do ponto final adicionado
Para concluir o processo reparametrização, tem de atualizar o modelo treinado do novo ponto final que adicionou.

Se tiver adicionado o ponto final com o código de exemplo, isto inclui a localização do URL de ajuda identificado pelo *HelpLocationURL* valor no resultado.

Para obter o URL de caminho:

1. Copie e cole o URL do seu browser.
2. Clique na ligação de recursos de atualização.
3. Copie o URL de publicação do pedido PATCH. Por exemplo:
   
     URL DE PATCH: HTTPS://MANAGEMENT.AZUREML.NET/WORKSPACES/00BF70534500B34REBFA1843D6/WEBSERVICES/AF3ER32AD393852F9B30AC9A35B/ENDPOINTS/NEWENDPOINT2

Agora, pode utilizar o modelo treinado para atualizar o ponto final de classificação que criou anteriormente.

O código de exemplo seguinte mostra como utilizar o *BaseLocation*, *RelativeLocation*, *SasBlobToken*e o URL de aplicar o PATCH para atualizar o ponto final.

    private async Task OverwriteModel()
    {
        var resourceLocations = new
        {
            Resources = new[]
            {
                new
                {
                    Name = "Census Model [trained model]",
                    Location = new AzureBlobDataReference()
                    {
                        BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                        RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
                    }
                }
            }
        };

        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

            using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
            {
                request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
                HttpResponseMessage response = await client.SendAsync(request);

                if (!response.IsSuccessStatusCode)
                {
                    await WriteFailedResponse(response);
                }

                // Do what you want with a successful response here.
            }
        }
    }

O *apiKey* e *endpointUrl* para a chamada pode ser obtida a partir do dashboard de ponto final.

O valor da *nome* parâmetro *recursos* deve corresponder ao nome do recurso do modelo treinado guardado na experimentação preditiva. Para obter o nome do recurso:

1. Iniciar sessão para o [portal clássico do Azure](https://manage.windowsazure.com).
2. No menu à esquerda, clique em **Machine Learning**.
3. No nome, clique em sua área de trabalho e, em seguida, clique em **serviços Web**.
4. No nome, clique em **Census modelo [preditiva exp].** .
5. Clique em novo ponto final que adicionou.
6. No dashboard do ponto final, clique em **atualização recursos**.
7. Na página de documentação de API de recursos de atualização para o serviço web, pode encontrar o **nome do recurso** em **recursos Atualizável**.

Se o seu token SAS expirar antes de terminar a atualizar o ponto final, tem de efetuar uma ação obter com o Id da tarefa para obter um novo token.

Quando o código foi executada com êxito, o novo ponto final deve começar a utilizar o modelo retrained em cerca de 30 segundos.

## <a name="summary"></a>Resumo
Utilizar APIs para a reparametrização, pode atualizar o modelo treinado de um serviço Web preditiva ativar cenários, tais como:

* Modelo periódico reparametrização com novos dados.
* Distribuição de um modelo para clientes com o objetivo de permitir que os reparametrização do modelo utilizando os seus próprios dados.

## <a name="next-steps"></a>Passos seguintes
[Resolução de problemas a reparametrização de um serviço web clássico do Azure Machine Learning](troubleshooting-retraining-models.md)

