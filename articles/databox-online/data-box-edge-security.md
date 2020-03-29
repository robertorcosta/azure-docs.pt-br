---
title: Segurança da azure Data Box Edge | Microsoft Docs
description: Descreve os recursos de segurança e privacidade que protegem o dispositivo, serviço e dados do Azure Data Box Edge no local e na nuvem.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 375576dd4a7897c48474fd2af00a99084292d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "69970895"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Segurança e proteção de dados do Azure Data Box Edge

A segurança é uma grande preocupação quando você está adotando uma nova tecnologia, especialmente se a tecnologia é usada com dados confidenciais ou proprietários. O Azure Data Box Edge ajuda você a garantir que apenas entidades autorizadas possam visualizar, modificar ou excluir seus dados.

Este artigo descreve os recursos de segurança data box edge que ajudam a proteger cada um dos componentes da solução e os dados armazenados neles.

O Azure Data Box Edge consiste em quatro componentes principais que interagem entre si:

- **Serviço Data Box Edge, hospedado no Azure**. O recurso de gerenciamento que você usa para criar a ordem do dispositivo, configurar o dispositivo e, em seguida, rastrear o pedido até a conclusão.
- **Dispositivo Data Box Edge .** O dispositivo de transferência enviado para você para que você possa importar seus dados no local para o Azure.
- **Clientes/hosts conectados ao dispositivo**. Os clientes em sua infra-estrutura que se conectam ao dispositivo Data Box Edge e contêm dados que precisam ser protegidos.
- **Armazenamento em nuvem**. A localização na plataforma de nuvem do Azure onde os dados são armazenados. Esse local é tipicamente a conta de armazenamento vinculada ao recurso Data Box Edge que você cria.

## <a name="data-box-edge-service-protection"></a>Proteção de serviço data box edge

O serviço Data Box Edge é um serviço de gerenciamento hospedado no Azure. O serviço é usado para configurar e gerenciar o dispositivo.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Proteção do dispositivo Data Box Edge

O dispositivo Data Box Edge é um dispositivo no local que ajuda a transformar seus dados processando-os localmente e, em seguida, enviando-os para o Azure. Seu dispositivo:

- Precisa de uma chave de ativação para acessar o serviço Data Box Edge.
- É protegido o tempo todo por uma senha do dispositivo.
- É um dispositivo bloqueado. O dispositivo BMC e BIOS são protegidos por senha. O BIOS é protegido por acesso limitado ao usuário.
- Tem inicialização segura ativada.
- Executa o Windows Defender Device Guard. O Device Guard permite que você execute apenas aplicativos confiáveis que você define em suas políticas de integridade de código.

### <a name="protect-the-device-via-activation-key"></a>Proteja o dispositivo através da chave de ativação

Apenas um dispositivo Data Box Edge autorizado é permitido aderir ao serviço Data Box Edge que você cria em sua assinatura do Azure. Para autorizar um dispositivo, você precisa usar uma chave de ativação para ativar o dispositivo com o serviço Data Box Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Para obter mais informações, consulte [Obter uma chave de ativação](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteja o dispositivo via senha

As senhas garantem que apenas usuários autorizados possam acessar seus dados. Os dispositivos Data Box Edge são inicializados em um estado bloqueado.

Você pode:

- Conecte-se à interface web local do dispositivo através de um navegador e, em seguida, forneça uma senha para fazer login no dispositivo.
- Conecte-se remotamente à interface PowerShell do dispositivo em HTTP. O gerenciamento remoto é ligado por padrão. Em seguida, você pode fornecer a senha do dispositivo para fazer login no dispositivo. Para obter mais informações, consulte [Conecte-se remotamente ao seu dispositivo Data Box Edge](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Use a ui web local para [alterar a senha](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Se você alterar a senha, certifique-se de notificar todos os usuários de acesso remoto para que eles não tenham problemas para fazer login.

## <a name="protect-your-data"></a>Proteger seus dados

Esta seção descreve os recursos de segurança data box edge que protegem dados em trânsito e armazenados.

### <a name="protect-data-at-rest"></a>Proteger dados em repouso

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- A criptografia de 256 bits do BitLocker XTS-AES é usada para proteger os dados locais.


### <a name="protect-data-in-flight"></a>Proteger dados em vôo

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteger dados por meio de contas de armazenamento

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Gire e, em seguida, [sincronize as chaves da conta](data-box-edge-manage-shares.md#sync-storage-keys) de armazenamento regularmente para ajudar a proteger sua conta de armazenamento contra usuários não autorizados.

## <a name="manage-personal-information"></a>Gerenciar informações pessoais

O serviço Data Box Edge coleta informações pessoais nos seguintes cenários:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Para visualizar a lista de usuários que podem acessar ou excluir uma ação, siga as etapas em [Gerenciar compartilhamentos no Data Box Edge](data-box-edge-manage-shares.md).

Para obter mais informações, revise a política de privacidade da Microsoft no [Trust Center](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Próximas etapas

[Implante seu dispositivo Data Box Edge](data-box-edge-deploy-prep.md)
