---
title: Como utilizar Twilio de voz e SMS (Ruby) | Microsoft Docs
description: "Saiba como efetuar uma chamada telefónica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. Exemplos de código escrito em Ruby."
services: 
documentationcenter: ruby
author: devinrader
manager: twilio
editor: 
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: 69e50e7fe0e1f302e96c309878b8dea6869dff4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Como utilizar Twilio de voz e capacidades SMS do Ruby
Este guia demonstra como efetuar tarefas de programação comuns com o serviço de API do Twilio no Azure. Os cenários abrangidos incluem a efetuar uma chamada telefónica e enviar uma mensagem de serviço de mensagens curto (SMS). Para obter mais informações sobre Twilio e a utilização de voz e SMS nas suas aplicações, consulte o [passos](#NextSteps) secção.

## <a id="WhatIs"></a>O que é Twilio?
Twilio é uma API de serviço web de telefonia que lhe permite utilizar os idiomas de web existente e competências para criar aplicações de SMS e voz. Twilio é um serviço de terceiros (não uma funcionalidade do Azure e não um produto Microsoft).

**Voz do Twilio** permite que as aplicações tornar e receber chamadas telefónicas. **Twilio SMS** permite que as aplicações tornar e receber mensagens SMS. **Cliente do Twilio** permite que as aplicações ativar a comunicação de voz através de ligações de Internet existentes, incluindo ligações móveis.

## <a id="Pricing"></a>Preços do Twilio e ofertas especiais
Estão disponíveis informações sobre preços do Twilio no [preços do Twilio][twilio_pricing]. Clientes do Azure recebem um [oferta especial][special_offer]: um crédito livre de textos 1000 ou 1000 minutos de entrada. Para inscrever-se nesta oferta ou obter mais informações, visite [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Conceitos
A API do Twilio é uma API RESTful que fornece uma funcionalidade SMS de voz e de aplicações. Bibliotecas de cliente estão disponíveis em vários idiomas; Para obter uma lista, consulte [Twilio as bibliotecas API][twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções baseado em XML que o informam Twilio como processar uma chamada ou um SMS.

Por exemplo, o seguinte TwiML seria converter o texto **Olá, mundo** para reconhecimento de voz.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Todos os documentos de TwiML têm `<Response>` como o respetivo elemento raiz. A partir daí, pode utilizar Twilio verbos para definir o comportamento da sua aplicação.

### <a id="Verbs"></a>TwiML verbos
Verbos do Twilio são tags XML que dizer Twilio **fazer**. Por exemplo, o  **&lt;indiquem&gt;**  verbo dá instruções ao Twilio audibly entregar uma mensagem sobre uma chamada. 

Segue-se uma lista de verbos do Twilio.

* **&lt;Marcação&gt;**: liga-se o autor da chamada para outro telemóvel.
* **&lt;Recolher&gt;**: recolhe dígitos numéricos introduzidos no teclado do telefone.
* **&lt;Hangup&gt;**: termina uma chamada.
* **&lt;Reproduzir&gt;**: desempenha um ficheiro de áudio.
* **&lt;Colocar em pausa&gt;**: silenciosamente aguarda que um número de segundos especificado.
* **&lt;Registo&gt;**: regista a voz do emissor e devolve um URL de um ficheiro que contém a gravação.
* **&lt;Redirecionar&gt;**: transfere o controlo de uma chamada ou SMS para o TwiML um URL diferente.
* **&lt;Rejeitar&gt;**: rejeita uma chamada para o seu número de Twilio sem a faturação
* **&lt;Diga&gt;**: converte texto para reconhecimento de voz que é efetuado numa chamada.
* **&lt;SMS&gt;**: envia uma mensagem SMS.

Para obter mais informações sobre Twilio verbos, os respetivos atributos e TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API do Twilio, consulte [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Criar uma conta do Twilio
Quando estiver pronto para obter uma conta do Twilio, inscreva-se em [tente Twilio][try_twilio]. Pode começar com uma conta gratuita e atualize a sua conta mais tarde.

Quando se inscreve para uma conta do Twilio, irá obter um número de telefone livre para a sua aplicação. Também irá receber uma SID da conta e um token de autenticação. Ambos serão necessários para efetuar chamadas de API do Twilio. Para impedir o acesso não autorizado à sua conta, mantenha o seu token de autenticação segura. O SID de conta e o token de autenticação estão visíveis no [página de conta do Twilio][twilio_account], nos campos etiquetados **SID da conta** e **AUTH TOKEN**, respetivamente.

### <a id="VerifyPhoneNumbers"></a>Verifique os números de telefone
Além do número é-lhe dada por Twilio, também pode verificar números controlo (ou seja, o número de telefone do telemóvel ou home) para utilizar nas suas aplicações. 

Para obter informações sobre a forma de verificar um número de telefone, consulte [gerir números][verify_phone].

## <a id="create_app"></a>Criar uma aplicação Ruby
Uma aplicação Ruby que utiliza o serviço do Twilio e está em execução no Azure é não diferente de qualquer outra aplicação Ruby, que utiliza o serviço do Twilio. Enquanto os serviços de Twilio são RESTful e podem ser chamados de Ruby de várias formas, este artigo foca-se sobre como utilizar os serviços do Twilio com [biblioteca de programa auxiliar do Twilio para Ruby][twilio_ruby].

Primeiro, [configuração uma nova VM do Linux do Azure] [ azure_vm_setup] para atuar como um anfitrião para a sua nova aplicação Ruby web. Ignore os passos que envolve a criação de uma aplicação de Rails, basta configurar a VM. Certifique-se de que cria um ponto final com uma externa a porta 80 e uma porta interna de 5000.

Nos exemplos abaixo, vamos utilizar [Sinatra][sinatra], uma estrutura web muito simples para Ruby. Mas certamente que pode utilizar a biblioteca de programa auxiliar do Twilio para Ruby com quaisquer outro framework de web, incluindo Ruby no Rails.

SSH para a nova VM e criar um diretório para a nova aplicação. Dentro nesse diretório, crie um ficheiro chamado Gemfile e copie o seguinte código para a mesma:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Na linha de comandos, execute `bundle install`. Esta ação instalará as dependências acima. Em seguida, crie um ficheiro chamado `web.rb`. Este será onde se encontra o código para a sua aplicação web. Cole o seguinte código para a mesma:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

Nesta fase, deve ser capaz de executar o comando `ruby web.rb -p 5000`. Isto irá giratórias-se um servidor web pequeno na porta 5000. Deve ser capaz de procurar a esta aplicação no seu browser, visitando o URL configurar para a VM do Azure. Depois de contactar a sua aplicação web no browser, está pronto para começar a criar uma aplicação do Twilio.

## <a id="configure_app"></a>Configurar a sua aplicação para utilizar Twilio
Pode configurar a sua aplicação web para utilizar a biblioteca do Twilio atualizando o `Gemfile` para incluir esta linha:

    gem 'twilio-ruby'

Na linha de comandos, execute `bundle install`. Agora abra `web.rb` e incluindo esta linha na parte superior:

    require 'twilio-ruby'

Agora está tudo pronto a utilizar a biblioteca de programa auxiliar do Twilio para Ruby na sua aplicação web.

## <a id="howto_make_call"></a>Como: efetuar uma chamada de saída
O seguinte mostra como efetuar uma chamada de saída. Conceitos chave incluem utilizando a biblioteca de programa auxiliar do Twilio para Ruby efetuar chamadas de REST API e compor TwiML. Substitua os valores para o **de** e **para** números de telefone e certifique-se de que verifique a **de** número para a sua conta do Twilio antes de executar o código de telefone.

Adicione esta função para `web.md`:

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";

    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);

      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end

Se a abrir-se `http://yourdomain.cloudapp.net/make_call` num browser, que acionará a chamada à API do Twilio para efetuar a chamada telefónica. Os primeiro dois parâmetros na `client.account.calls.create` bastante dispensam explicações: é o número da chamada `from` e é o número da chamada `to`. 

O terceiro parâmetro (`url`) é o URL que Twilio pedidos para obter instruções sobre o que fazer depois da chamada está ligada. Neste caso estamos a configurar um URL (`http://yourdomain.cloudapp.net`) que devolve um documento TwiML simple e utiliza o `<Say>` verbo algumas text-to-speech e diga "Monkey Olá" para a pessoa que recebe a chamada.

## <a id="howto_recieve_sms"></a>Como: Recieve uma mensagem SMS
No exemplo anterior é iniciado um **saída** chamada telefónica. Este tempo, vamos utilizar o número de telefone Twilio deu nos durante a inscrição para o processo de um **entrada** mensagem SMS.

Primeiro, inicie sessão na sua [Twilio dashboard][twilio_account]. Clique em "Números de" de navegação superior e, em seguida, clique no número Twilio que foram fornecidos. Verá dois URLs que pode configurar. URL do pedido de um URL de pedido de voz e um SMS. Estes são os URLs que Twilio chama sempre que é efetuada uma chamada telefónica ou um SMS é enviado para o seu número. Os URLs são também conhecidos como "web hooks".

Gostaríamos de processar mensagens a receber SMS, por isso, vamos atualizar o URL para `http://yourdomain.cloudapp.net/sms_url`. Avançar e clique em Guardar alterações na parte inferior da página. Agora, fazer uma cópia no `web.rb` vamos programa nossa aplicação para lidar com isto:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Depois de efetuar a alteração, certifique-se voltar a iniciar a aplicação web. Agora, remova o seu telefone e enviar um SMS para o seu número de Twilio. Detetar deve obter uma resposta SMS que diz "Hey, obrigado por ping! Rock Twilio e no Azure! ".

## <a id="additional_services"></a>Como: utilizar os serviços do Twilio adicionais
Para além dos exemplos mostrados aqui, Twilio oferece APIs baseadas na web que pode utilizar para tirar partido das funcionalidades adicionais do Twilio da sua aplicação do Azure. Para mais informações, consulte o [documentação da API do Twilio][twilio_api_documentation].

### <a id="NextSteps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do serviço do Twilio, siga estas ligações para saber mais:

* [Diretrizes de segurança do Twilio][twilio_security_guidelines]
* [HowTos do Twilio e código de exemplo][twilio_howtos]
* [Tutoriais de início rápido do Twilio][twilio_quickstarts] 
* [Twilio no GitHub][twilio_on_github]
* [Comunicar com o suporte do Twilio][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: http://www.windowsazure.com/develop/ruby/tutorials/web-app-with-linux-vm/
