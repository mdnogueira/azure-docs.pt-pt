---
title: Como utilizar o PowerShell para criar certificados x. 509 | Microsoft Docs
description: "Como utilizar o PowerShell para criar certificados x. 509 localmente e ativar o x. 509 com a base de segurança do seu hub IoT do Azure num ambiente simulado."
services: iot-hub
documentationcenter: 
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2017
ms.author: dkshir
ms.openlocfilehash: 31f94686fed376fbeda2ccdcbc5ed001bcda8126
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="powershell-scripts-to-manage-ca-signed-x509-certificates"></a>Scripts do PowerShell para gerir os certificados x. 509 assinado para AC

A segurança baseada em certificados de x. 509 no IoT Hub requer começar com uma [cadeia de certificados x. 509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), que inclui o certificado de raiz, bem como quaisquer certificados intermediários até o certificado de folha. Isto *como* guia explica como scripts do PowerShell de exemplo que utilizam [OpenSSL](https://www.openssl.org/) para criar e assinar certificados x. 509. Recomendamos que utilize este guia para experimentação apenas, uma vez que muitos destes passos acontecerá durante o processo no mundo real de fabrico. Pode utilizar estes certificados para simular a segurança na sua hub IoT do Azure ao utilizar o *autenticação de certificado x. 509*. Os passos neste guia criam certificados localmente no seu computador Windows. 

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial parte do princípio de que os binários de OpenSSL tiver adquirido. O utilizador pode:
    - Transferir o código de origem do OpenSSL e os binários de compilação no seu computador, ou 
    - transferir e instalar qualquer [binários de OpenSSL de terceiros](https://wiki.openssl.org/index.php/Binaries), por exemplo, do [este projeto no SourceForge](https://sourceforge.net/projects/openssl/).

<a id="createcerts"></a>

## <a name="create-x509-certificates"></a>Criar certificados x. 509
Os passos seguintes mostram um exemplo de como criar os certificados de raiz de x. 509 localmente. 

1. Abra uma janela do PowerShell como um *administrador*. 
2. Navegue para o diretório de trabalho. Execute o script seguinte para definir as variáveis globais. 
    ```PowerShell
    $openSSLBinSource = "<full_path_to_the_binaries>\OpenSSL\bin"
    $errorActionPreference    = "stop"

    # Note that these values are for test purpose only
    $_rootCertSubject         = "CN=Azure IoT Root CA"
    $_intermediateCertSubject = "CN=Azure IoT Intermediate {0} CA"
    $_privateKeyPassword      = "123"

    $rootCACerFileName          = "./RootCA.cer"
    $rootCAPemFileName          = "./RootCA.pem"
    $intermediate1CAPemFileName = "./Intermediate1.pem"
    $intermediate2CAPemFileName = "./Intermediate2.pem"
    $intermediate3CAPemFileName = "./Intermediate3.pem"

    $openSSLBinDir              = Join-Path $ENV:TEMP "openssl-bin"

    # Whether to use ECC or RSA.
    $useEcc                     = $true
    ```
3. Execute o seguinte script que copia os binários de OpenSSL para o diretório de trabalho e define variáveis de ambiente:

    ```PowerShell
    function Initialize-CAOpenSSL()
    {
        Write-Host ("Beginning copy of openssl binaries to {0} (and setting up env variables...)" -f $openSSLBinDir)
        if (-not (Test-Path $openSSLBinDir))
        {
            mkdir $openSSLBinDir | Out-Null
        }

        robocopy $openSSLBinSource $openSSLBinDir * /s 
        robocopy $openSSLBinSource . * /s 

        Write-Host "Setting up PATH and other environment variables."
        $ENV:PATH += "; $openSSLBinDir"
        $ENV:OPENSSL_CONF = Join-Path $openSSLBinDir "openssl.cnf"

        Write-Host "Success"
    }
    Initialize-CAOpenSSL
    ```
4. Em seguida, execute o script seguinte que procura se um certificado especificado pelo *nome do requerente* já estiver instalado, e se OpenSSL está corretamente configurado no seu computador:
    ```PowerShell
    function Get-CACertBySubjectName([string]$subjectName)
    {
        $certificates = gci -Recurse Cert:\LocalMachine\ |? { $_.gettype().name -eq "X509Certificate2" }
        $cert = $certificates |? { $_.subject -eq $subjectName -and $_.PSParentPath -eq "Microsoft.PowerShell.Security\Certificate::LocalMachine\My" }
        if ($NULL -eq $cert)
        {
            throw ("Unable to find certificate with subjectName {0}" -f $subjectName)
        }
    
        write $cert
    }
    function Test-CAPrerequisites()
    {
        $certInstalled = $null
        try
        {
            $certInstalled = Get-CACertBySubjectName $_rootCertSubject
        }
        catch {}

        if ($NULL -ne $certInstalled)
        {
            throw ("Certificate {0} already installed.  Cleanup CA certs 1st" -f $_rootCertSubject)
        }

        if ($NULL -eq $ENV:OPENSSL_CONF)
        {
            throw ("OpenSSL not configured on this system.  Run 'Initialize-CAOpenSSL' (even if you've already done so) to set everything up.")
        }
        Write-Host "Success"
    }
    Test-CAPrerequisites
    ```
    Se está tudo configurado corretamente, deverá ver "Êxito" mensagem. 

<a id="createcertchain"></a>

## <a name="create-x509-certificate-chain"></a>Criar cadeia de certificados x. 509
Criar uma cadeia de certificados com uma raiz de AC, por exemplo, "CN = AC de raiz de IoT do Azure" que utiliza este exemplo, executando o seguinte script do PowerShell. Este script também atualiza o arquivo de certificados do sistema operativo Windows, também cria ficheiros de certificado no seu diretório de trabalho. 
    1. O script seguinte cria uma função do PowerShell para criar um certificado autoassinado, para um determinado *nome do requerente* e a autoridade de assinatura. 
    ```PowerShell
    function New-CASelfsignedCertificate([string]$subjectName, [object]$signingCert, [bool]$isASigner=$true)
    {
        # Build up argument list
        $selfSignedArgs =@{"-DnsName"=$subjectName; 
                           "-CertStoreLocation"="cert:\LocalMachine\My";
                           "-NotAfter"=(get-date).AddDays(30); 
                          }

        if ($isASigner -eq $true)
        {
            $selfSignedArgs += @{"-KeyUsage"="CertSign"; }
            $selfSignedArgs += @{"-TextExtension"= @(("2.5.29.19={text}ca=TRUE&pathlength=12")); }
        }
        else
        {
            $selfSignedArgs += @{"-TextExtension"= @("2.5.29.37={text}1.3.6.1.5.5.7.3.2,1.3.6.1.5.5.7.3.1", "2.5.29.19={text}ca=FALSE&pathlength=0")  }
        }

        if ($signingCert -ne $null)
        {
            $selfSignedArgs += @{"-Signer"=$signingCert }
        }

        if ($useEcc -eq $true)
        {
            $selfSignedArgs += @{"-KeyAlgorithm"="ECDSA_nistP256";
                             "-CurveExport"="CurveName" }
        }

        # Now use splatting to process this
        Write-Host ("Generating certificate {0} which is for prototyping, NOT PRODUCTION.  It will expire in 30 days." -f $subjectName)
        write (New-SelfSignedCertificate @selfSignedArgs)
    }
    ``` 
    2. A seguinte função do PowerShell cria intermédios certificados x. 509 utilizando a função anterior, bem como os binários de OpenSSL. 
    ```PowerShell
    function New-CAIntermediateCert([string]$subjectName, [Microsoft.CertificateServices.Commands.Certificate]$signingCert, [string]$pemFileName)
    {
        $certFileName = ($subjectName + ".cer")
        $newCert = New-CASelfsignedCertificate $subjectName $signingCert
        Export-Certificate -Cert $newCert -FilePath $certFileName -Type CERT | Out-Null
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\CA" -FilePath $certFileName | Out-Null

        # Store public PEM for later chaining
        openssl x509 -inform deer -in $certFileName -out $pemFileName

        del $certFileName
   
        write $newCert
    }  
    ```
    3. A função do PowerShell seguinte cria a cadeia de certificados x. 509. Leitura [certificado encadeie](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification) para obter mais informações.
    ```PowerShell
    function New-CACertChain()
    {
        Write-Host "Beginning to install certificate chain to your LocalMachine\My store"
        $rootCACert =  New-CASelfsignedCertificate $_rootCertSubject $null
    
        Export-Certificate -Cert $rootCACert -FilePath $rootCACerFileName  -Type CERT
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\Root" -FilePath $rootCACerFileName

        openssl x509 -inform der -in $rootCACerFileName -out $rootCAPemFileName

        $intermediateCert1 = New-CAIntermediateCert ($_intermediateCertSubject -f "1") $rootCACert $intermediate1CAPemFileName
        $intermediateCert2 = New-CAIntermediateCert ($_intermediateCertSubject -f "2") $intermediateCert1 $intermediate2CAPemFileName
        $intermediateCert3 = New-CAIntermediateCert ($_intermediateCertSubject -f "3") $intermediateCert2 $intermediate3CAPemFileName
        Write-Host "Success"
    }    
    ```
    Este script cria um ficheiro denominado *RootCA.cer* no seu diretório de trabalho. 
    4. Por fim, utilize as funções de PowerShell acima para criar a cadeia de certificados x. 509, executando o comando `New-CACertChain` na janela do PowerShell. 


<a id="signverificationcode"></a>

## <a name="proof-of-possession-of-your-x509-ca-certificate"></a>Prova de posse do seu certificado de AC de x. 509

Este script executa o *prova de posse* fluxo para o certificado x. 509. 

Na janela do PowerShell no seu ambiente de trabalho, execute o seguinte código:
    ```PowerShell
    function New-CAVerificationCert([string]$requestedSubjectName)
    {
        $cnRequestedSubjectName = ("CN={0}" -f $requestedSubjectName)
        $verifyRequestedFileName = ".\verifyCert4.cer"
        $rootCACert = Get-CACertBySubjectName $_rootCertSubject
        Write-Host "Using Signing Cert:::" 
        Write-Host $rootCACert
    
        $verifyCert = New-CASelfsignedCertificate $cnRequestedSubjectName $rootCACert $false

        Export-Certificate -cert $verifyCert -filePath $verifyRequestedFileName -Type Cert
        if (-not (Test-Path $verifyRequestedFileName))
        {
            throw ("Error: CERT file {0} doesn't exist" -f $verifyRequestedFileName)
        }
    
        Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnRequestedSubjectName, (Join-Path (get-location).path $verifyRequestedFileName)) 
    }
    New-CAVerificationCert "<your verification code>"
    ```
   Esta ação cria um certificado com o nome de requerente determinado assinado pela AC, como um ficheiro denominado *VerifyCert4.cer* no seu diretório de trabalho. Este ficheiro de certificado irão ajudá-lo a validar o seu IoT hub que tem a permissão de assinatura (ou seja, a chave privada) desta AC.


<a id="createx509device"></a>

## <a name="create-leaf-x509-certificate-for-your-device"></a>Criar certificado x. 509 de folha para o seu dispositivo

Esta secção mostra que pode utilizar um script do PowerShell que cria um certificado de dispositivo de folha e a cadeia de certificados correspondentes. 

Na janela do PowerShell no seu computador local, execute o script seguinte para criar um certificado assinado para AC x. 509 para este dispositivo:
    ```PowerShell
    function New-CADevice([string]$deviceName, [string]$signingCertSubject=$_rootCertSubject)
    {
        $cnNewDeviceSubjectName = ("CN={0}" -f $deviceName)
        $newDevicePfxFileName = ("./{0}.pfx" -f $deviceName)
        $newDevicePemAllFileName      = ("./{0}-all.pem" -f $deviceName)
        $newDevicePemPrivateFileName  = ("./{0}-private.pem" -f $deviceName)
        $newDevicePemPublicFileName   = ("./{0}-public.pem" -f $deviceName)
    
        $signingCert = Get-CACertBySubjectName $signingCertSubject ## "CN=Azure IoT CA Intermediate 1 CA"

        $newDeviceCertPfx = New-CASelfSignedCertificate $cnNewDeviceSubjectName $signingCert $false
    
        $certSecureStringPwd = ConvertTo-SecureString -String $_privateKeyPassword -Force -AsPlainText

        # Export the PFX of the cert we've just created.  The PFX is a format that contains both public and private keys.
        Export-PFXCertificate -cert $newDeviceCertPfx -filePath $newDevicePfxFileName -password $certSecureStringPwd
        if (-not (Test-Path $newDevicePfxFileName))
        {
            throw ("Error: CERT file {0} doesn't exist" -f $newDevicePfxFileName)
        }

        # Begin the massaging.  First, turn the PFX into a PEM file which contains public key, private key, and other attributes.
        Write-Host ("When prompted for password by openssl, enter the password as {0}" -f $_privateKeyPassword)
        openssl pkcs12 -in $newDevicePfxFileName -out $newDevicePemAllFileName -nodes

        # Convert the PEM to get formats we can process
        if ($useEcc -eq $true)
        {
            openssl ec -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
        }
        else
        {
            openssl rsa -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
        }
        openssl x509 -in $newDevicePemAllFileName -out $newDevicePemPublicFileName
 
        Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnNewDeviceSubjectName, (Join-Path (get-location).path $newDevicePemPublicFileName)) 
    }
    ```
   Em seguida, execute `New-CADevice "<yourTestDevice>"` na janela do PowerShell, utilizando o nome amigável que utilizou para criar o seu dispositivo. Quando lhe for pedido para a palavra-passe para a chave privada da AC, introduza "123". Esta ação cria um  _<yourTestDevice>. pfx_ ficheiro no seu diretório de trabalho.

