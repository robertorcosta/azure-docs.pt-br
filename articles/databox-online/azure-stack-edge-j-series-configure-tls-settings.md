---
title: Configurar o TLS 1,2 em clientes Windows que acessam Azure Stack dispositivo de borda
description: Descreve como configurar o TLS 1,2 em clientes Windows que acessam Azure Stack dispositivo de borda.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 01/06/2020
ms.author: alkohli
ms.openlocfilehash: 552e44e4d3d1b9e237d6ee99d82b946d34fa5e3d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89082839"
---
# <a name="configure-tls-12-on-windows-clients-accessing-azure-stack-edge-device"></a>Configurar o TLS 1,2 em clientes Windows que acessam Azure Stack dispositivo de borda

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Se você estiver usando um cliente Windows para acessar seu dispositivo Azure Stack Edge, será necessário configurar o TLS 1,2 em seu cliente. Este artigo fornece recursos e diretrizes para configurar o TLS 1,2 em seu cliente Windows. 

As diretrizes fornecidas aqui se baseiam no teste realizado em um cliente que executa o Windows Server 2016.

## <a name="configure-tls-12-for-current-powershell-session"></a>Configurar o TLS 1,2 para a sessão atual do PowerShell

Execute as etapas a seguir para configurar o TLS 1,2 em seu cliente.

1. Execute o PowerShell como administrador.
2. Para definir o TLS 1,2 para a sessão atual do PowerShell, digite:
  
    ```azurepowershell
    $TLS12Protocol = [System.Net.SecurityProtocolType] 'Ssl3 , Tls12'
    [System.Net.ServicePointManager]::SecurityProtocol = $TLS12Protocol
    ```
## <a name="configure-tls-12-on-client"></a>Configurar o TLS 1,2 no cliente

Se você quiser definir o TLS 1,2 de todo o sistema para o seu ambiente, siga as diretrizes nestes documentos:

- [Geral-como habilitar o TLS 1,2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)
- [Como habilitar o TLS 1.2 em clientes](https://docs.microsoft.com/configmgr/core/plan-design/security/enable-tls-1-2-client)
- [Como habilitar o TLS 1.2 nos servidores do site e nos sistemas de sites remotos](https://docs.microsoft.com/configmgr/core/plan-design/security/enable-tls-1-2-server)
- [Protocolos em TLS/SSL (Schannel SSP)](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-ecc-curve-order)
- [Conjuntos de codificação](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12): [Configurando especificamente a ordem do conjunto de criptografia TLS](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order) , certifique-se de listar seus conjuntos de codificação atuais e preceder qualquer falta da seguinte lista:

    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

    Você também pode adicionar esses conjuntos de codificação editando diretamente as configurações do registro.

    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "Functions"  -PropertyType String -Value ("TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384")
    ```

- Como definir curvas elípticas

    Certifique-se de listar suas curvas elíptico atuais e preceder qualquer falta da seguinte lista:

    - P-256 
    - P-384

    Você também pode adicionar essas curvas elípticos editando diretamente as configurações do registro.
    
    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "EccCurves" -PropertyType MultiString -Value @("NistP256", "NistP384")
    ```
    
    - [Defina o tamanho mínimo de troca de chave RSA como 2048](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#keyexchangealgorithm---client-rsa-key-sizes).



## <a name="next-steps"></a>Próximas etapas

[Conectar-se ao Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md)