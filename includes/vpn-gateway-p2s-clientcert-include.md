Cada computador cliente que se ligue uma VNet por Ponto a Site tem de ter um certificado de cliente instalado. O certificado de cliente é gerado a partir do certificado de raiz e instalado em cada computador cliente. Se não estiver instalado um certificado de cliente válido e o cliente tentar estabelecer ligação com a VNet, a autenticação falhará.

Pode optar por gerar um certificado exclusivo para cada cliente ou pode utilizar o mesmo certificado para vários clientes. A vantagem de gerar certificados para cada cliente individual é a capacidade de revogar um único certificado. Caso contrário, se vários clientes estiverem a utilizar o mesmo certificado de cliente e precisar de o revogar, terá de gerar e instalar novos certificados para todos os clientes que utilizam o certificado se poderem autenticar.

Pode gerar certificados de cliente através dos seguintes métodos:

- **Certificado da empresa:**

  - Se estiver a utilizar uma solução de certificado da empresa, gere um certificado de cliente com o formato do valor de nome comum “name@yourdomain.com”, em vez do formato “nome de domínio/nome de utilizador”.
  - Certifique-se de que o certificado de cliente se baseia no modelo de certificado "Utilizador" que tem a "Autenticação de Cliente" como primeiro item na lista de utilização, em vez do Início de Sessão de Smart Card, etc. Pode verificar o certificado ao clicar duas vezes no certificado de cliente e visualizar *Detalhes > Utilização Avançada da Chaves*.

- **Certificado de raiz autoassinado:** se gerar um certificado de cliente a partir de um certificado de raiz autoassinado com as instruções do artigo [Create a self-signed root certificate for Point-to-Site connections](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert)(Criar um certificado de raiz autoassinado para ligações de Ponto a Site), este é instalado automaticamente no computador que utilizou para gerá-lo. Se quiser instalar um certificado de cliente noutro computador cliente, tem de exportá-lo. Siga as instruções no artigo para [exportar o certificado](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).