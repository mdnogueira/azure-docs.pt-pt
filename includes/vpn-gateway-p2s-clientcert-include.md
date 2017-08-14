Cada computador cliente que se ligue uma VNet por Ponto a Site tem de ter um certificado de cliente instalado. O certificado de cliente é gerado a partir do certificado de raiz e instalado em cada computador cliente. Se não estiver instalado um certificado de cliente válido e o cliente tentar estabelecer ligação com a VNet, a autenticação falhará.

Pode optar por gerar um certificado exclusivo para cada cliente ou pode utilizar o mesmo certificado para vários clientes. A vantagem de gerar certificados para cada cliente individual é a capacidade de revogar um único certificado. Caso contrário, se vários clientes estiverem a utilizar o mesmo certificado de cliente e precisar de o revogar, terá de gerar e instalar novos certificados para todos os clientes que utilizam o certificado se poderem autenticar.

Pode gerar certificados de cliente através dos seguintes métodos:

- **Certificado da empresa:**

  - Se estiver a utilizar uma solução de certificado da empresa, gere um certificado de cliente com o formato do valor de nome comum “name@yourdomain.com”, em vez do formato “nome de domínio/nome de utilizador”.
  - Certifique-se de que o certificado de cliente se baseia no modelo de certificado "Utilizador" que tem a "Autenticação de Cliente" como primeiro item na lista de utilização, em vez do Início de Sessão de Smart Card, etc. Pode verificar o certificado ao clicar duas vezes no certificado de cliente e visualizar *Detalhes > Utilização Avançada da Chaves*.

- **Certificado de raiz autoassinado:** É importante seguir os passos de um dos artigos de certificados P2S abaixo. Caso contrário, os certificados de cliente que criar não serão compatíveis com ligações de P2S e os clientes recebem um erro quando tentarem estabelecer ligação. Os passos em qualquer um dos seguintes artigos geram um certificado de cliente compatível: 

  * [Instruções do Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): estas instruções requerem o Windows 10 e o PowerShell para gerar certificados. Os certificados gerados podem ser instalados em qualquer cliente P2S suportado.
  * [Instruções do MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): utilize o MakeCert se não tiver acesso a um computador com o Windows 10 para gerar certificados. O MakeCert foi preterido, mas ainda o pode utilizar para gerar certificados. Os certificados gerados podem ser instalados em qualquer cliente P2S suportado.

  Se gerar um certificado de cliente a partir de um certificado de raiz autoassinado com as instruções do anteriores, este é instalado automaticamente no computador que utilizou para gerá-lo. Se quiser instalar um certificado de cliente noutro computador cliente, tem de exportá-lo como .pfx, juntamente com toda a cadeia de certificados. Esta ação cria um ficheiro .pfx que contém as informações do certificado de raiz necessárias para o cliente ser autenticado com êxito. Para obter os passos para exportar um certificado, veja [Certificados - exportar um certificado de cliente](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).