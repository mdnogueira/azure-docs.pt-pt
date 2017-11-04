---
title: "Recomendações de segurança para imagens do Azure Marketplace | Microsoft Docs"
description: "Este artigo fornece recomendações para imagens incluídas no mercado local"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: barclayn
ms.openlocfilehash: 4ae36f87c29975c82bb99f713893a9dc78a249e6
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Recomendações de segurança para imagens do Azure Marketplace

Recomendamos que cada solução de estar em conformidade com as seguintes recomendações de configuração de segurança. Isto irá ajudar a manter um elevado nível de segurança para imagens de solução de parceiros no Azure Marketplace.

Estas recomendações também podem ser úteis para as organizações que não dispõe de imagens no Azure marketplace. Poderá verificar configurações de imagem de Windows e Linux da sua empresa contra as diretrizes encontradas nas tabelas abaixo.

## <a name="open-source-based-images"></a>Abra as imagens com base na origem

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Categoria**                                                 | **Verificação**                                                                                                                                                                                                                                                                              |
| Segurança                                                     | Todos os patches de segurança mais recentes para a distribuição de Linux estão instalados.                                                                                                                                                                                                              |
| Segurança                                                     | As diretrizes da indústria para proteger a imagem VM para a distribuição de Linux específico foram seguidas.                                                                                                                                                                                     |
| Segurança                                                     | Limite a superfície de ataque por manter os requisitos de espaço mínimo em só é necessárias funções de servidor do Windows, funções, serviços e portas de rede.                                                                                                                                               |
| Segurança                                                     | Análise de código fonte e a imagem VM resultante para o software maligno.                                                                                                                                                                                                                                   |
| Segurança                                                     | A imagem VHD inclui apenas necessárias contas bloqueadas, que não dispõe de palavras-passe predefinido que permitem o início de sessão interativo; Não existem portas anterior.                                                                                                                                           |
| Segurança                                                     | Regras de firewall estão desativadas, a menos que a aplicação funcionalmente baseia-se nos mesmos, por exemplo, uma aplicação de firewall.                                                                                                                                                                             |
| Segurança                                                     | Todas as informações confidenciais foi removidas da imagem de VHD, tal como chaves SSH de teste, conhecido certificados desnecessários, ficheiros de registo e ficheiro de anfitriões.                                                                                                                                       |
| Segurança                                                     | Recomenda-se que LVM deve não utilizado.                                                                                                                                                                                                                                            |
| Segurança                                                     | Versões mais recentes das bibliotecas necessárias devem ser incluídas: </br> -OpenSSL v-1.0 ou superior </br> -Python 2.5 ou superior (Python 2.6 + é vivamente recomendável) </br> -Pacote de pyasn1 Python se ainda não estiver instalado </br> -d.OpenSSL v 1.0 ou superior                                                                |
| Segurança                                                     | Entradas do histórico/Shell de deteção tem de ser limpo                                                                                                                                                                                                                                             |
| Redes                                                   | Servidor SSH deve ser incluído por predefinição. Como SSH keep alive sshd configuração com a seguinte opção: ClientAliveInterval 180                                                                                                                                                        |
| Redes                                                   | Imagem não deve conter qualquer configuração de rede personalizada. Elimine o resolv.conf:`rm /etc/resolv.conf`                                                                                                                                                                                |
| Implementação                                                   | Deve ser instalado o agente Linux do Azure mais recente </br> -O agente deve ser instalado utilizando o pacote RPM ou Deb.  </br> -Pode utilizar o processo de instalação manual, mas os pacotes de instalador são recomendados e preferencial. </br> -Se instalar manualmente o agente do repositório do github, primeiro de copiar o `waagent` do ficheiro para `/usr/sbin` e run (root): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>O ficheiro de configuração do agente irão ser colocado no `/etc/waagent.conf`.    |
| Implementação                                                   | Garante que o suporte do Azure pode fornecer nossos parceiros com a consola de série ao necessário e forneça o limite de tempo suficiente para a montagem de disco de SO do armazenamento na nuvem. Imagem tem de ter a adicionado os seguintes parâmetros para a linha de arranque de Kernel:`console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Implementação                                                   | Sem partição de comutação no disco do SO. Comutação pode ser pedida para a criação do disco de recurso local pelo agente Linux.         |
| Implementação                                                   | Recomenda-se que uma partição de raiz única é criada para o disco do SO.      |
| Implementação                                                   | 64-bit apenas sistema de operativo.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Imagens e baseadas no servidor do Windows

|||
|-------------| -------------------------|
| **Categoria**                                                     | **Verificação**                                                                                                                                                                |
| Segurança                                                         | Utilize uma imagem de base do SO segura. O VHD utilizado para a origem de qualquer imagem baseada no Windows Server tem de ter entre as imagens de sistema operativo do Windows Server fornecidas através do Microsoft Azure. |
| Segurança                                                         | Instale todas as atualizações de segurança mais recentes.                                                                                                                                     |
| Segurança                                                         | Aplicações não devem ter uma dependência em nomes de utilizador restrito, como administrador, de raiz e de administrador.                                                                |
| Segurança                                                         | Encriptação de unidade BitLocker não é suportada no disco rígido do sistema operativo. O BitLocker pode ser utilizado em discos de dados.                                                            |
| Segurança                                                         | Limite a superfície de ataque por manter os requisitos de espaço mínimo com funções do Windows Server só é necessárias, funcionalidades, serviços e portas de rede ativadas.                         |
| Segurança                                                         | Análise de código fonte e a imagem VM resultante para o software maligno.                                                                                                                     |
| Segurança                                                         | Definir a atualização de segurança de imagens do Windows Server para a atualização automática.                                                                                                                |
| Segurança                                                         | A imagem VHD inclui apenas necessárias contas bloqueadas, que não dispõe de palavras-passe predefinido que permitem o início de sessão interativo; Não existem portas anterior.                             |
| Segurança                                                         | Regras de firewall estão desativadas, a menos que a aplicação funcionalmente baseia-se nos mesmos, por exemplo, uma aplicação de firewall.                                                               |
| Segurança                                                         | Todas as informações confidenciais foi removidas da imagem de VHD. Por exemplo, ficheiro de anfitriões, ficheiros de registo e certificados desnecessários devem ser removidos.                                              |
| Implementação                                                       | 64-bit apenas sistema de operativo.                            |
