---
title: "Azure de criptografia - ferramenta de modelação de ameaça Microsoft - | Microsoft Docs"
description: "Mitigações ameaças exposta na ferramenta de modelação de ameaça"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 96e74371fe51a8050a91c86215e3eefab07bbed8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="security-frame-cryptography--mitigations"></a>Moldura de segurança: Criptografia | Mitigações 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Aplicação Web** | <ul><li>[Utilize apenas aprovados bloco simétrica cifras e comprimentos de chave](#cipher-length)</li><li>[Utilize aprovados vetores de inicialização do cifras simétricos e modos de cifra do bloco](#vector-ciphers)</li><li>[Utilizar aprovados assimétricos algoritmos, comprimentos de chave e o preenchimento](#padding)</li><li>[Utilizar aprovados generators número aleatórios](#numgen)</li><li>[Não utilize cifras fluxo simétrica](#stream-ciphers)</li><li>[Utilizar algoritmos hash de MAC/HMAC/codificada aprovados](#mac-hash)</li><li>[Utilizar apenas as funções de hash criptográficos aprovados](#hash-functions)</li></ul> |
| **Base de Dados** | <ul><li>[Utilizar algoritmos de encriptação forte para encriptar dados na base de dados](#strong-db)</li><li>[Devem ser encriptados e assinados digitalmente pacotes SSIS](#ssis-signed)</li><li>[Adicionar a assinatura digital para a base de dados críticos securables](#securables-db)</li><li>[Utilizar o SQL server EKM para proteger chaves de encriptação](#ekm-keys)</li><li>[Utilize a funcionalidade de AlwaysEncrypted se as chaves de encriptação não devem ser reveladas ao motor de base de dados](#keys-engine)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Armazenar as chaves criptográficas em segurança no dispositivo IoT](#keys-iot)</li></ul> | 
| **Gateway de nuvem do IoT** | <ul><li>[Gerar uma chave simétrica aleatória de comprimento suficiente para a autenticação ao IoT Hub](#random-hub)</li></ul> | 
| **Cliente para dispositivos móveis Dynamics CRM** | <ul><li>[Certifique-se de que é uma política de gestão de dispositivos no local que requer um PIN de utilização e permite remoto limpeza](#pin-remote)</li></ul> | 
| **Cliente do Outlook do Dynamics CRM** | <ul><li>[Certifique-se uma política de gestão de dispositivos no local que necessita de um bloqueio PIN/palavra-passe/automático e encripta todos os dados (por exemplo, o Bitlocker)](#bitlocker)</li></ul> | 
| **Servidor de identidades** | <ul><li>[Certifique-se de que as chaves de assinatura são revertidas quando utilizar o servidor de identidades](#rolled-server)</li><li>[Certifique-se de que o ID de cliente criptograficamente forte, segredo do cliente são utilizadas no servidor de identidades](#client-server)</li></ul> | 

## <a id="cipher-length"></a>Utilize apenas aprovados bloco simétrica cifras e comprimentos de chave

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Produtos tem de utilizar apenas as cifras bloco simétrico e comprimentos de chave associados que foram aprovados explicitamente pelo Advisor Crypto na sua organização. Algoritmos simétricos aprovados na Microsoft incluem as cifras bloco seguinte:</p><ul><li>Para o novo código AES-128, AES 192 e AES 256 são aceitáveis</li><li>Para retrocompatibilidade com o código existente, três chave 3DES é aceitável</li><li>Para os produtos cifras simétrica blocos a utilizar:<ul><li>O Advanced Encryption Standard (AES) é necessário para o novo código</li><li>Triple três a chave de encriptação e a Data (3DES) é permitido no código existente para compatibilidade com versões anteriores</li><li>Todos os outras blocos as cifras, incluindo RC2, DES, 2 chave 3DES, DESX e Skipjack, só podem ser utilizadas para desencriptar os dados antigos e têm de ser substituídas se utilizada para encriptação</li></ul></li><li>Para algoritmos de encriptação simétrica bloco, é necessário um comprimento de chave mínimo de 128 bits. O algoritmo de encriptação apenas do bloco recomendado para o novo código é AES (AES-128, AES 192 e AES 256 são aceitáveis tudo)</li><li>Três chave 3DES é atualmente aceitável se já estiver a ser utilizado no código existente; Recomenda-se a transição para AES. DES, DESX, RC2 e Skipjack já não são considerados seguros. Estes algoritmos só podem ser utilizados para desencriptar os dados existentes com vista à, compatibilidade com versões anteriores e dados devem ser encriptados de voltar a utilizar uma cifra de bloco recomendada</li></ul><p>Tenha em atenção que todas as cifras de bloco simétrica tem de ser utilizadas com um modo de cifra aprovados, o que requer a utilização de um vetor de inicialização adequado (IV). Um IV adequado, normalmente, é um número aleatório e nunca um valor constante</p><p>A utilização de legado ou, caso contrário não aprovados algoritmos criptográficos e comprimentos de chave inferior para a leitura de dados existentes (por oposição a escrita de dados novos) pode ser permitida depois de rever quadro de criptografia da sua organização. No entanto, tem de ficheiros para uma exceção contra este requisito. Além disso, em implementações em empresas, produtos devem considerar os administradores de aviso quando a criptografia fraca é utilizada para ler os dados. Esses avisos devem ser explicativo e passíveis de ação. Em alguns casos, poderá ser apropriado controlar a utilização de criptografia fraca de política de grupo</p><p>Permitido algoritmos de .NET para agilidade criptografia gerido (por ordem de preferência)</p><ul><li>AesCng (compatível com FIPS)</li><li>AuthenticatedAesCng (compatível com FIPS)</li><li>AESCryptoServiceProvider (compatível com FIPS)</li><li>AESManaged (não-compatíveis com FIPS)</li></ul><p>Tenha em atenção que nenhuma destes algoritmos podem ser especificadas através do `SymmetricAlgorithm.Create` ou `CryptoConfig.CreateFromName` métodos sem efetuar alterações ao ficheiro Machine. config. Além disso, tenha em atenção que é denominado AES em versões do .NET antes de .NET 3.5 `RijndaelManaged`, e `AesCng` e `AuthenticatedAesCng` são > disponível através de CodePlex e exigir CNG no SO subjacente</p>

## <a id="vector-ciphers"></a>Utilize aprovados vetores de inicialização do cifras simétricos e modos de cifra do bloco

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Todas as cifras de bloco simétrica tem de ser utilizadas com um modo de cifra simétrica aprovados. Os modos aprovados apenas são CBC e CTS. Em particular, o modo de livro (ECB) código eletrónicas da operação deve ser evitado; a utilização de ECB requer revisão de placa de criptografia da sua organização. Todos os utilização de OFB, CFB, CTR, CCM e GCM ou qualquer outro modo de encriptação deve ser revisto por placa de criptografia da sua organização. Reutilizar o mesmo vetor de inicialização (IV) com cifras bloco em "transmissão em fluxo modos de cifras," como CTR, pode fazer com que os dados encriptados ser revelado. Todas as cifras de bloco simétrica também tem de ser utilizadas por um vetor de inicialização adequado (IV). Um IV adequado é criptograficamente forte, número aleatório e nunca um valor constante. |

## <a id="padding"></a>Utilizar aprovados assimétricos algoritmos, comprimentos de chave e o preenchimento

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>A utilização de algoritmos criptográficos banned apresenta risco significativo para segurança do produto e deve ser evitada. Produtos tem de utilizar apenas os algoritmos criptográficos e comprimentos de chave e o preenchimento que foram aprovadas explicitamente pelo Conselho de criptografia da sua organização associadas.</p><ul><li>**RSA -** pode ser utilizado para encriptação, assinatura e troca de chaves. Encriptação RSA tem de utilizar apenas os modos de preenchimento OAEP ou RSA KEM. Código existente, pode utilizar v 1.5 PKCS n. º 1 preenchimento modo apenas para compatibilidade. Utilização de preenchimento nulo é banned explicitamente. As chaves de > = 2048 bits é necessário para o novo código. Código existente pode suportar chaves < 2048 bits apenas para efeitos de compatibilidade após uma revisão por placa de criptografia da sua organização. Chaves < 1024 bits só pode ser utilizados para desencriptação/verificar dados antigos e tem de ser substituído se utilizada para encriptação ou operações de assinatura</li><li>**ECDSA -** podem ser utilizadas para assinatura apenas. ECDSA com > = chaves de 256 bits é necessário para o novo código. Com base em ECDSA assinaturas tem de utilizar uma das três curvas NIST aprovada (p-256, p-384, ou P521). Curvas que foi analisadas exaustivamente podem ser utilizadas apenas depois de ver com o painel de criptografia da sua organização.</li><li>**ECDH -** podem ser utilizadas para apenas a troca de chaves. ECDH com > = chaves de 256 bits é necessário para o novo código. Troca de chaves baseado em ECDH tem de utilizar uma das três curvas NIST aprovada (p-256, p-384, ou P521). Curvas que foi analisadas exaustivamente podem ser utilizadas apenas depois de ver com o painel de criptografia da sua organização.</li><li>**DSA -** poderá ser aceitável depois de revisão e aprovação da placa de criptografia da sua organização. Contacte o Assistente de segurança para agendar a revisão do quadro de criptografia da sua organização. Se a utilização do DSA for aprovada, tenha em atenção que, terá de proíbem a utilização de chaves de menos de 2048 bits de comprimento. CNG suporta 2048 bits e maiores comprimentos de chave a partir do Windows 8.</li><li>**Diffie-Hellman -** podem ser utilizadas para a sessão gestão de chaves apenas. Comprimento da chave > = 2048 bits é necessário para o novo código. Código existente pode suportar comprimentos de chave < 2048 bits apenas para efeitos de compatibilidade após uma revisão por placa de criptografia da sua organização. As chaves não podem ser utilizados < 1024 bits.</li><ul>

## <a id="numgen"></a>Utilizar aprovados generators número aleatórios

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Produtos tem de utilizar aprovados generators de números aleatórios. Funções pseudorandom como rand de função de tempo de execução C, a classe do .NET Framework System.Random ou funções de sistema, tais como GetTickCount por conseguinte, tem, nunca ser utilizadas em desse código. Utilização do dupla de curva elíptica aleatório número gerador (DUAL_EC_DRBG) algoritmo é proibida</p><ul><li>**CNG -** BCryptGenRandom (utilização do sinalizador BCRYPT_USE_SYSTEM_PREFERRED_RNG recomendado, a menos que o autor da chamada poderá ser executado em qualquer IRQL maior que 0 [ou seja, PASSIVE_LEVEL])</li><li>**CAPI -** cryptGenRandom</li><li>**O Win32/64-** RtlGenRandom (implementações novas devem utilizar BCryptGenRandom ou CryptGenRandom) * rand_s * SystemPrng (para o modo de kernel)</li><li>**. NET -** RNGCryptoServiceProvider ou RNGCng</li><li>**Aplicações da loja Windows -** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom ou. GenerateRandomNumber</li><li>**Apple OS X (10.7+)/iOS(2.0+) -** int SecRandomCopyBytes (SecRandomRef aleatória, contagem de size_t, uint8_t *bytes)</li><li>**Apple OS X (< 10.7)-** utilização / Dev/aleatório obter números aleatórios</li><li>**Java(including Google Android Java Code) -** java.security.SecureRandom classe. Tenha em atenção que para Android 4.3 (Jelly Bean), os programadores devem seguir o Android recomendado solução e atualizar as aplicações para inicializar explicitamente PRNG com entropia /dev/urandom ou /dev/random</li></ul>|

## <a id="stream-ciphers"></a>Não utilize cifras fluxo simétrica

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Cifras fluxo simétrica, tais como RC4, não podem ser utilizadas. Em vez de cifras simétrica fluxo, os produtos devem utilizar uma cifra de blocos, especificamente AES com um comprimento de chave de pelo menos de 128 bits. |

## <a id="mac-hash"></a>Utilizar algoritmos hash de MAC/HMAC/codificada aprovados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Produtos tem de utilizar apenas aprovados o código de autenticação de mensagem (MAC) ou os algoritmos de code (HMAC) com base em hash mensagem autenticação.</p><p>Um código de autenticação de mensagem (MAC) é um elemento de informação ligado a uma mensagem que permite que o destinatário verificar a autenticidade do remetente de ambos e a integridade da mensagem com uma chave secreta. A utilização de qualquer um MAC com base em hash ([HMAC](http://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) ou [bloco de cifras baseada em MAC](http://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) é permitido como tempo como hash todos os subjacente ou de encriptação simétrica algoritmos também estão aprovados para utilização; Isto inclui atualmente as funções de HMAC SHA2 (HMAC SHA256, HMAC SHA384 e HMAC SHA512) e CMAC/OMAC1 e OMAC2 bloqueiam baseado em cifras MACs (estas baseiam-se em AES).</p><p>Utilização de HMAC SHA1 pode ser permitida para compatibilidade de plataforma, mas será necessária uma exceção a este procedimento de ficheiro e são submetidos a revisão criptográfica da sua organização. Não é permitida a truncagem de HMACs para menos de 128 bits. Utilizar métodos de hash uma chave e os dados não foi aprovada e tem são submetidos a placa de criptografia da sua organização rever antes da utilização do cliente.</p>|

## <a id="hash-functions"></a>Utilizar apenas as funções de hash criptográficos aprovados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Produtos tem de utilizar a família SHA-2 de algoritmos de hash (SHA256, SHA384 e SHA512). Se for necessário um hash mais curto, tais como um comprimento de saída de 128 bits para caber numa estrutura de dados concebida com o hash MD5 mais curto em mente, as equipas de produto podem truncar uma dos hashes SHA2 (normalmente SHA256). Tenha em atenção que SHA384 é uma versão truncada de SHA512. Não é permitida a truncagem de hashes criptográficos por motivos de segurança para menos de 128 bits. Novo código não tem de utilizar o MD2, MD4, MD5, SHA-0, SHA-1 ou algoritmos de hash RIPEMD. Colisões de hashes são viáveis exequível para estes algoritmos que quebra de forma eficaz-los.</p><p>Permitido algoritmos de hash de .NET para agilidade criptografia gerido (por ordem de preferência):</p><ul><li>SHA512Cng (compatível com FIPS)</li><li>SHA384Cng (compatível com FIPS)</li><li>SHA256Cng (compatível com FIPS)</li><li>SHA512Managed (não-compatíveis com FIPS) (utilize SHA512 como nome do algoritmo em chamadas a HashAlgorithm.Create ou CryptoConfig.CreateFromName)</li><li>SHA384Managed (não-compatíveis com FIPS) (utilize SHA384 como o nome do algoritmo em chamadas a HashAlgorithm.Create ou CryptoConfig.CreateFromName)</li><li>SHA256Managed (não-compatíveis com FIPS) (utilize SHA256 como nome do algoritmo em chamadas a HashAlgorithm.Create ou CryptoConfig.CreateFromName)</li><li>SHA512CryptoServiceProvider (compatível com FIPS)</li><li>SHA256CryptoServiceProvider (compatível com FIPS)</li><li>SHA384CryptoServiceProvider (compatível com FIPS)</li></ul>| 

## <a id="strong-db"></a>Utilizar algoritmos de encriptação forte para encriptar dados na base de dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Escolher um algoritmo de encriptação](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Passos** | Algoritmos de encriptação definem transformações de dados que não é possível ser invertidas facilmente por utilizadores não autorizados. SQL Server permite que os administradores e programadores escolher entre vários algoritmos, incluindo DES, Triple DES, TRIPLE_DES_3KEY, RC2, RC4, RC4 de 128 bits, DESX, AES de 128 bits, 192 bits AES e AES 256 bits |

## <a id="ssis-signed"></a>Devem ser encriptados e assinados digitalmente pacotes SSIS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Identificar a origem dos pacotes com assinaturas digitais](https://msdn.microsoft.com/library/ms141174.aspx), [ameaças e vulnerabilidades mitigação (Serviços de integração)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **Passos** | A origem de um pacote é a pessoa ou organização que criou o pacote. Pode ser arriscado executar um pacote a partir de uma origem desconhecida ou não fidedigna. Para impedir a adulteração não autorizados de pacotes SSIS, assinaturas digitais devem ser utilizadas. Além disso, para garantir a confidencialidade dos pacotes durante o armazenamento/trânsito, pacotes SSIS tem de ser encriptados |

## <a id="securables-db"></a>Adicionar a assinatura digital para a base de dados críticos securables

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [ADICIONAR a assinatura (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **Passos** | Nos casos em que a integridade da base de dados crítico com capacidade de segurança tem de ser verificado, assinaturas digitais devem ser utilizadas. Podem ser assinados digitalmente securables de base de dados, tais como um procedimento armazenado, a função, a assemblagem ou acionador. Segue-se um exemplo de quando isto pode ser útil: diga-num ISV e estiver (independentes de Software de fornecedor) forneceu suporte para um software entregue um dos seus clientes. Antes de a fornecer o suporte, seria aconselhável o ISV para se certificar de que uma base de dados com capacidade de segurança no software não foi adulterado por engano ou por uma tentativa de maliciosa. Se o com capacidade de segurança estiver assinada digitalmente, a ISV pode verificar a assinatura digital e validar a integridade.| 

## <a id="ekm-keys"></a>Utilizar o SQL server EKM para proteger chaves de encriptação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [SQL Server Extensible Key Management (EKM)](https://msdn.microsoft.com/library/bb895340), [Extensible Key Management com o Cofre de chave do Azure (SQL Server)](https://msdn.microsoft.com/library/dn198405) |
| **Passos** | SQL Server Extensible Key Management permite que as chaves de encriptação que protegem os ficheiros de base de dados para ser armazenada num dispositivo de-box, como um smart card, um dispositivo USB ou um módulo EKM/HSM. Isto também permite a proteção de dados aos administradores de base de dados (exceto os membros do grupo sysadmin). Dados podem ser encriptados através da utilização de chaves de encriptação que apenas o utilizador de base de dados tem acesso no módulo externo EKM/HSM. |

## <a id="keys-engine"></a>Utilize a funcionalidade de AlwaysEncrypted se as chaves de encriptação não devem ser reveladas ao motor de base de dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | SQL Server versão - V12, MsSQL2016 |
| **Referências**              | [Sempre encriptado (motor de base de dados)](https://msdn.microsoft.com/library/mt163865) |
| **Passos** | Sempre encriptado é uma funcionalidade concebida para proteger os dados confidenciais, como números de cartão de crédito ou números de identificação national (por exemplo, E.U.A. números de segurança social), armazenados nas bases de dados SQL Database do Azure ou SQL Server. Sempre encriptado permite aos clientes encriptar dados confidenciais dentro de aplicações de cliente e nunca revelar as chaves de encriptação para o motor de base de dados (base de dados SQL ou SQL Server). Como resultado, sempre encriptado proporciona uma separação entre os utilizadores que possuem os dados (e pode vê-la) e aqueles que gerir os dados (mas deve ter sem acesso) |

## <a id="keys-iot"></a>Armazenar as chaves criptográficas em segurança no dispositivo IoT

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | SO do dispositivo - Windows IoT Core, conectividade do dispositivo - SDKs do dispositivo IoT do Azure |
| **Referências**              | [TPM no Windows IoT Core](https://developer.microsoft.com/windows/iot/docs/tpm), [configurar TPM no Windows IoT Core](https://developer.microsoft.com/windows/iot/win10/setuptpm), [TPM de SDK de dispositivos de IoT do Azure](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Passos** | Chaves Symmetric ou privada do certificado com segurança num hardware protegido armazenamento, como de chips TPM ou Smart Card. Windows 10 IoT Core suporta o utilizador de um TPM e não existirem vários TPMs compatíveis que podem ser utilizados: https://developer.microsoft.com/windows/iot/win10/tpm. Recomenda-se para utilizar um Firmware ou discreto do TPM. Só deve ser utilizado um TPM de Software para desenvolvimento e testes. Depois de um TPM está disponível e as chaves são aprovisionadas no mesmo, o código que gera o token deve ser escrito sem rígido codificação quaisquer informações sensíveis no mesmo. | 

### <a name="example"></a>Exemplo
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Como podem ser vistos, a chave primária do dispositivo não está presente no código. Em vez disso, é armazenada no TPM na ranhura 0. Dispositivo TPM gera um token SAS curta duração, em seguida, utilizado para estabelecer ligação ao IoT Hub. 

## <a id="random-hub"></a>Gerar uma chave simétrica aleatória de comprimento suficiente para a autenticação ao IoT Hub

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem do IoT | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Opção de gateway - IoT Hub do Azure |
| **Referências**              | N/D  |
| **Passos** | IoT Hub contém um registo de identidade de dispositivo e ao aprovisionamento de um dispositivo, gera automaticamente uma chave simétrica aleatória. Recomenda-se para utilizar esta funcionalidade de registo de identidade do Azure IoT Hub para gerar a chave utilizada para autenticação. IoT Hub, também permite uma chave de ser especificado ao criar o dispositivo. Se uma chave é gerada fora do IoT Hub durante o aprovisionamento de dispositivos, recomenda-se para criar uma chave simétrica aleatória ou pelo menos de 256 bits. |

## <a id="pin-remote"></a>Certifique-se de que é uma política de gestão de dispositivos no local que requer um PIN de utilização e permite remoto limpeza

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente para dispositivos móveis Dynamics CRM | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que é uma política de gestão de dispositivos no local que requer um PIN de utilização e permite remoto limpeza |

## <a id="bitlocker"></a>Certifique-se uma política de gestão de dispositivos no local que necessita de um bloqueio PIN/palavra-passe/automático e encripta todos os dados (por exemplo, o Bitlocker)

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente do Outlook do Dynamics CRM | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se uma política de gestão de dispositivos no local que necessita de um bloqueio PIN/palavra-passe/automático e encripta todos os dados (por exemplo, o Bitlocker) |

## <a id="rolled-server"></a>Certifique-se de que as chaves de assinatura são revertidas quando utilizar o servidor de identidades

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de identidades | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Servidor de identidades - chaves, assinaturas e criptografia](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Passos** | Certifique-se de que as chaves de assinatura são revertidas quando utilizar o servidor de identidades. A hiperligação na secção referências explica como este deve ser planeada sem causar falhas para as aplicações no servidor de identidades. |

## <a id="client-server"></a>Certifique-se de que o ID de cliente criptograficamente forte, segredo do cliente são utilizadas no servidor de identidades

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de identidades | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Certifique-se de que o ID de cliente criptograficamente forte, segredo do cliente são utilizadas no servidor de identidades. As diretrizes seguintes devem ser utilizadas ao gerar um ID de cliente e o segredo:</p><ul><li>Gerar um GUID aleatório como o ID de cliente</li><li>Gerar uma chave de 256 bits criptograficamente aleatória como o segredo</li></ul>|