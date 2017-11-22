[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Posso utilizar AC de raiz de PKI interna para a conetividade Ponto a Site?

Sim. Anteriormente, só podiam ser utilizados os certificados de raiz autoassinados. Ainda pode carregar 20 certificados de raiz.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Ferramentas de que pode utilizar para criar certificados?

Pode utilizar a sua solução de PKI empresarial (a PKI interna), do Azure PowerShell, MakeCert e OpenSSL.

### <a name="certsettings"></a>Existem instruções para os parâmetros e definições de certificado?

* **Solução PKI/Enterprise PKI interna:** consulte os passos para [gerar certificados](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **O Azure PowerShell:** consulte o [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) artigo para obter os passos.

* **MakeCert:** consulte o [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) artigo para obter os passos.

* **OpenSSL:** 

    * Ao exportar certificados, lembre-se de que converter o certificado de raiz para Base64.

    * Para o certificado de cliente:

      * Ao criar a chave privada, especifique o comprimento como 4096.
      * Ao criar o certificado para o *-extensões* parâmetro, especifique *usr_cert*.