---
title: "Como efetuar uma chamada telefónica do Twilio (.NET) | Microsoft Docs"
description: "Saiba como efetuar uma chamada telefónica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. Exemplos de código escrito no .NET."
services: 
documentationcenter: .net
author: devinrader
manager: timlt
editor: 
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 0899a49cbfda775017dab7fc6d8963bbeb86d74c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Como efetuar uma chamada telefónica utilizando Twilio numa função da web no Azure
Este guia demonstra como utilizar Twilio para efetuar uma chamada de uma página web alojado no Azure. A aplicação resultante pede ao utilizador para efetuar uma chamada com o número especificado e a mensagem, conforme mostrado na captura de ecrã seguinte.

![Formato de chamada do Azure utilizando Twilio e ASP.NET][twilio_dotnet_basic_form]

## <a name="twilio-prereqs"></a>Pré-requisitos
Terá de fazer o seguinte para utilizar o código neste tópico:

1. Adquirir uma conta do Twilio e a autenticação de token do [Twilio consola][twilio_console]. Para obter uma introdução Twilio, inscreva-se em [https://www.twilio.com/try-twilio][try_twilio]. Pode avaliar preços em [http://www.twilio.com/pricing][twilio_pricing]. Para obter informações sobre a API fornecida pelo Twilio, consulte [http://www.twilio.com/voice/api][twilio_api].
2. Adicionar o *biblioteca .NET do Twilio* para a função da web. Consulte **para adicionar as bibliotecas do Twilio ao seu projeto de função web**, mais adiante neste tópico.

Deve estar familiarizado com a criação num nível básico [função da Web no Azure][azure_webroles_get_started].

## <a name="howtocreateform"></a>Como: criar um formulário web para efetuar uma chamada
<a id="use_nuget"></a>Para adicionar as bibliotecas do Twilio ao seu projeto de função da web:

1. Abra a solução no Visual Studio.
2. Clique com botão direito **referências**.
3. Clique em **gerir pacotes NuGet**.
4. Clique em **Online**.
5. Na caixa de pesquisa online, escreva *twilio*.
6. Clique em **instalar** no pacote Twilio.

O código seguinte mostra como criar um formulário web para obter dados de utilizador para efetuar uma chamada. Neste exemplo, uma função da Web de ASP.NET com o nome **TwilioCloud** é criado.

```aspx
<%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
    AutoEventWireup="true" CodeBehind="Default.aspx.cs"
    Inherits="WebRole1._Default" %>

<asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
</asp:Content>
<asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
    <div>
        <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
        </asp:BulletedList>
    </div>
    <div>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <div>
            To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
            Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
            <asp:Button ID="callpage" runat="server" Text="Make this call"
                onclick="callpage_Click" />
        </div>
    </div>
</asp:Content>
```

## <a id="howtocreatecode"></a>Como: criar o código para efetuar a chamada
O código seguinte, o que é chamado quando o utilizador conclui o formulário, cria a mensagem de chamada e gera a chamada. Neste exemplo, o código é executado o processador de eventos de onclick do botão no formulário. (Utilize a conta do Twilio e a autenticação de token em vez dos valores de marcador de posição atribuídos a `accountSID` e `authToken` no código abaixo.)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Twilio;
using Twilio.Http;
using Twilio.Types;
using Twilio.Rest.Api.V2010;

namespace WebRole1
{
    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }

        protected void callpage_Click(object sender, EventArgs e)
        {
            // Call porcessing happens here.

            // Use your account SID and authentication token instead of
            // the placeholders shown here.
            var accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
            var authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

            // Instantiate an instance of the Twilio client.
            TwilioClient.Init(accountSID, authToken);

            // Retrieve the account, used later to retrieve the
            var account = AccountResource.Fetch(accountSID);

            this.varDisplay.Items.Clear();

            if (this.toNumber.Text == "" || this.message.Text == "")
            {
                this.varDisplay.Items.Add(
                        "You must enter a phone number and a message.");
            }
            else
            {
                // Retrieve the values entered by the user.
                var to = PhoneNumber(this.toNumber.Text);
                var from = new PhoneNumber("+14155992671");
                var myMessage = this.message.Text;

                // Create a URL using the Twilio message and the user-entered
                // text. You must replace spaces in the user's text with '%20'
                // to make the text suitable for a URL.
                var url = $"http://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
                var twimlUri = new Uri(url);

                // Display the endpoint, API version, and the URL for the message.
                this.varDisplay.Items.Add($"Using Twilio endpoint {
                }");
                this.varDisplay.Items.Add($"Twilioclient API Version is {apiVersion}");
                this.varDisplay.Items.Add($"The URL is {url}");

                // Place the call.
                var call = CallResource.create(to, from, url: twimlUri);
                this.varDisplay.Items.Add("Call status: " + call.Status);
            }
        }
    }
}
```

A chamada é efetuada e o ponto final do Twilio, versão de API e o estado de chamada são apresentados. A seguinte captura de ecrã mostra a saída de uma execução de exemplo.

![Resposta de chamada do Azure utilizando Twilio e ASP.NET][twilio_dotnet_basic_form_output]

Obter mais informações sobre TwiML podem ser encontradas em [http://www.twilio.com/docs/api/twiml][twiml]. Obter mais informações sobre &lt;indiquem&gt; e outros verbos Twilio podem ser encontrados em [http://www.twilio.com/docs/api/twiml/say][twilio_say].

## <a id="nextsteps"></a>Passos seguintes
Este código foi fornecido para lhe mostrar funcionalidades básicas utilizando Twilio numa função web ASP.NET no Azure. Antes de implementar no Azure em produção, poderá querer adicionar mais processamento de erros ou outras funcionalidades. Por exemplo:

* Em vez de utilizar um formulário web, pode utilizar o armazenamento de Blobs do Azure ou uma instância de SQL Database do Azure para armazenar os números de telefone e chamar texto. Para obter informações sobre como utilizar os Blobs no Azure, consulte [como utilizar o serviço de armazenamento de Blobs do Azure no .NET][howto_blob_storage_dotnet]. Para obter informações sobre como utilizar a base de dados SQL, consulte [como utilizar a SQL Database do Azure em aplicações de .NET][howto_sql_azure_dotnet].
* Pode utilizar `RoleEnvironment.getConfigurationSettings` para obter o Twilio ID da conta e a autenticação de token de definições de configuração da implementação, em vez de pré-programar os valores existentes na sua forma. Para obter informações sobre o `RoleEnvironment` classe, consulte [Microsoft.WindowsAzure.ServiceRuntime espaço de nomes][azure_runtime_ref_dotnet].
* Leia as diretrizes de segurança do Twilio em [https://www.twilio.com/docs/security][twilio_docs_security].
* Saiba mais sobre Twilio em [https://www.twilio.com/docs][twilio_docs].

## <a name="seealso"></a>Ver também
* [Como utilizar Twilio para SMS de voz e capacidades do Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: http://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
[azure_webroles_get_started]: https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-dotnet-get-started
