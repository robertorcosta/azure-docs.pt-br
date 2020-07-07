---
title: Segurança do Azure Stack Edge | Microsoft Docs
description: Descreve os recursos de segurança e privacidade que protegem o dispositivo, o serviço e os dados do Azure Stack Edge no local e na nuvem.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 3d567ec4d760be24fdbb79ff85bd6db0eb4a66c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82570064"
---
# <a name="azure-stack-edge-security-and-data-protection"></a>Segurança de borda Azure Stack e proteção de dados

A segurança é uma preocupação importante quando você está adotando uma nova tecnologia, especialmente se a tecnologia for usada com dados confidenciais ou proprietários. Azure Stack Edge ajuda a garantir que apenas entidades autorizadas possam exibir, modificar ou excluir seus dados.

Este artigo descreve os Azure Stack recursos de segurança de borda que ajudam a proteger cada um dos componentes da solução e os dados armazenados neles.

O Azure Stack Edge consiste em quatro componentes principais que interagem entre si:

- **Azure Stack serviço do Edge, hospedado no Azure**. O recurso de gerenciamento que você usa para criar a ordem do dispositivo, configurar o dispositivo e acompanhar o pedido até a conclusão.
- **Azure Stack dispositivo do Edge**. O dispositivo de transferência enviado para você para que você possa importar seus dados locais para o Azure.
- **Clientes/hosts conectados ao dispositivo**. Os clientes em sua infraestrutura que se conectam ao dispositivo Azure Stack Edge e contêm dados que precisam ser protegidos.
- **Armazenamento em nuvem**. O local na plataforma de nuvem do Azure onde os dados são armazenados. Normalmente, esse local é a conta de armazenamento vinculada ao recurso de borda Azure Stack que você cria.

## <a name="azure-stack-edge-service-protection"></a>Proteção de serviço do Azure Stack Edge

O serviço de borda do Azure Stack é um serviço de gerenciamento hospedado no Azure. O serviço é usado para configurar e gerenciar o dispositivo.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="azure-stack-edge-device-protection"></a>Proteção de dispositivo do Azure Stack Edge

O dispositivo Azure Stack Edge é um dispositivo local que ajuda a transformar seus dados processando-os localmente e, em seguida, enviando-os para o Azure. Seu dispositivo:

- Precisa de uma chave de ativação para acessar o serviço do Azure Stack Edge.
- O é protegido sempre por uma senha de dispositivo.
- É um dispositivo bloqueado. O dispositivo BMC e BIOS são protegidos por senha. O BIOS é protegido por acesso limitado ao usuário.
- Tem inicialização segura habilitada.
- Executa o Windows Defender Device Guard. A proteção de dispositivo permite que você execute somente aplicativos confiáveis que você define em suas políticas de integridade de código.

### <a name="protect-the-device-via-activation-key"></a>Proteger o dispositivo por meio da chave de ativação

Somente um dispositivo de borda Azure Stack autorizado tem permissão para ingressar no serviço de borda do Azure Stack que você cria em sua assinatura do Azure. Para autorizar um dispositivo, você precisa usar uma chave de ativação para ativar o dispositivo com o serviço do Azure Stack Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Para obter mais informações, consulte [obter uma chave de ativação](azure-stack-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteger o dispositivo por meio de senha

As senhas garantem que somente usuários autorizados possam acessar seus dados. Os dispositivos Azure Stack Edge são inicializados em um estado bloqueado.

Você pode:

- Conecte-se à interface do usuário da Web local do dispositivo por meio de um navegador e forneça uma senha para entrar no dispositivo.
- Conecte-se remotamente à interface do dispositivo do PowerShell por HTTP. O gerenciamento remoto é ativado por padrão. Em seguida, você pode fornecer a senha do dispositivo para entrar no dispositivo. Para obter mais informações, consulte [conectar-se remotamente ao dispositivo do Azure Stack Edge](azure-stack-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Use a interface do usuário da Web local para [alterar a senha](azure-stack-edge-manage-access-power-connectivity-mode.md#manage-device-access). Se você alterar a senha, lembre-se de notificar todos os usuários de acesso remoto para que eles não tenham problemas de entrada.

## <a name="protect-your-data"></a>Proteger seus dados

Esta seção descreve os recursos de segurança do Azure Stack Edge que protegem dados armazenados e em trânsito.

### <a name="protect-data-at-rest"></a>Proteger dados em repouso

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- O BitLocker XTS-criptografia de 256 bits é usado para proteger dados locais.


### <a name="protect-data-in-flight"></a>Proteger dados em trânsito

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteger dados por meio de contas de armazenamento

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Gire e [sincronize suas chaves de conta de armazenamento](azure-stack-edge-manage-shares.md#sync-storage-keys) regularmente para ajudar a proteger sua conta de armazenamento de usuários não autorizados.

## <a name="manage-personal-information"></a>Gerenciar informações pessoais

O serviço do Azure Stack Edge coleta informações pessoais nos seguintes cenários:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Para exibir a lista de usuários que podem acessar ou excluir um compartilhamento, siga as etapas em [gerenciar compartilhamentos na borda de Azure Stack](azure-stack-edge-manage-shares.md).

Para obter mais informações, examine a política de privacidade da Microsoft na [central de confiabilidade](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Próximas etapas

[Implantar o dispositivo do Azure Stack Edge](azure-stack-edge-deploy-prep.md)
