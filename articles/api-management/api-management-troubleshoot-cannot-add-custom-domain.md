---
title: Não é possível adicionar domínio personalizado usando o certificado Key Vault
titleSuffix: Azure API Management
description: Aprenda a solucionar problemas no qual você não pode adicionar um domínio personalizado no Gerenciamento de API do Azure usando um certificado de cofre chave.
services: api-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/19/2019
ms.author: tehnoonr
ms.openlocfilehash: a09c15466a4a9f62b2696b087cb7ab23cc767379
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430586"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>Falha ao atualizar nomes de host de serviço de gerenciamento de API

Este artigo descreve o erro "Falha ao atualizar nomes de hostnames de serviço de gerenciamento de API" que você pode experimentar ao adicionar um domínio personalizado para o serviço de gerenciamento de API do Azure. Este artigo fornece etapas de solução de problemas para ajudá-lo a resolver o problema.

## <a name="symptoms"></a>Sintomas

Quando você tenta adicionar um domínio personalizado para o serviço de gerenciamento de API usando um certificado do Azure Key Vault, você recebe a seguinte mensagem de erro:

- Falha ao atualizar nomes de host do serviço de gerenciamento de API. Solicitação dehttps://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0recurso ' ' falhou com StatusCode: Proibido para RequestId: . Mensagem de exceção: A operação retornou um código de status inválido 'Proibido'.

## <a name="cause"></a>Causa

O serviço de gerenciamento de API não tem permissão para acessar o cofre de chaves que você está tentando usar para o domínio personalizado.

## <a name="solution"></a>Solução

Para resolver esse problema, siga estas etapas:

1. Vá para o [portal Azure,](Https://portal.azure.com)selecione a instância de gerenciamento de API e, em seguida, **selecione Identidades gerenciadas**. Certifique-se de que a opção **Registrar com diretório ativo do Azure** está definida como **Sim**. 
    ![Registrando-se com o Diretor Ativo do Azure](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png)
1. No portal Azure, abra o serviço **de cofres Key** e selecione o cofre-chave que você está tentando usar para o domínio personalizado.
1. Selecione **políticas de acesso**e verifique se há um princípio de serviço que corresponda ao nome da instância de serviço de gerenciamento de API. Se houver, selecione o diretor de serviço e certifique-se de que ele tenha a permissão **Obter** listada **permissões secretas**.  
    ![Adicionando política de acesso para o diretor de serviços](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png)
1. Se o serviço de gerenciamento de API não estiver na lista, selecione **Adicionar política**de acesso e crie a seguinte política de acesso:
    - **Configurar a partir do modelo:** Nenhum
    - **Selecione principal**: Pesquise o nome do serviço de gerenciamento de API e selecione-o na lista
    - **Permissões-chave**: Nenhuma
    - **Permissões secretas**: Obter
    - **Permissões de certificado**: Nenhuma
1. Selecione **OK** para criar a política de acesso.
1. Selecione **Salvar** para salvar as alterações.

Verifique se o problema foi resolvido. Para fazer isso, tente criar o domínio personalizado no serviço de gerenciamento de API usando o certificado Key Vault.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o serviço de gerenciamento de API:

- Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre o Gerenciamento de API.
* Para outras maneiras de proteger seu serviço de back-end, confira [Autenticação de certificado mútuo](api-management-howto-mutual-certificates.md).

* [Crie uma instância de serviço de gerenciamento de API](get-started-create-service-instance.md).
* [Gerencie sua primeira API](import-and-publish.md).