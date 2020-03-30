---
title: Configure o nome de domínio personalizado para a instância de gerenciamento de API do Azure
titleSuffix: Azure API Management
description: Este tópico descreve como configurar um nome de domínio personalizado para sua instância de Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 4587909ad6fca6cdf21d54d11d89f797bbb29833
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335845"
---
# <a name="configure-a-custom-domain-name"></a>Configurar um nome de domínio personalizado

Quando você cria uma instância de serviço de gerenciamento de API `azure-api.net` do Azure, o Azure atribui-lhe um subdomínio de (por exemplo, `apim-service-name.azure-api.net`). No entanto, você pode expor seus pontos finais de gerenciamento de API usando seu próprio nome de domínio personalizado, como **contoso.com**. Este tutorial mostra como mapear um nome de DNS personalizado existente para pontos finais expostos por uma instância de gerenciamento de API.

> [!IMPORTANT]
> O Gerenciamento de API aceita apenas solicitações com valores [de cabeçalho do host](https://tools.ietf.org/html/rfc2616#section-14.23) que correspondem ao nome de domínio padrão ou a qualquer um dos nomes de domínio personalizados configurados.

> [!WARNING]
> Os clientes que desejam usar a fixação de certificados para melhorar a segurança de seus aplicativos devem usar um nome de domínio personalizado e um certificado que eles gerenciam, não o certificado padrão. Os clientes que fixarem o certificado padrão, em vez disso, estarão tendo uma forte dependência das propriedades do certificado que não controlam, o que não é uma prática recomendada.

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas neste artigo, você precisa ter:

-   Uma assinatura ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Uma instância de gerenciamento de API. Para obter mais informações, consulte [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
-   Um nome de domínio personalizado que é de sua propriedade ou de sua organização. Este tópico não fornece instruções sobre como obter um nome de domínio personalizado.
-   Um registro CNAME hospedado em um servidor DNS que mapeia o nome de domínio personalizado para o nome de domínio padrão da instância de gerenciamento de API. Este tópico não fornece instruções sobre como hospedar um registro CNAME.
-   Você deve ter um certificado válido com chaves pública e privada (.PFX). O nome alternativo de assunto ou assunto (SAN) tem que corresponder ao nome de domínio (isso permite que a instância de gerenciamento de API exponha URLs com segurança sobre TLS).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Usar o portal do Azure para definir um nome de domínio personalizado

1. Navegue até a instância de gerenciamento de API no [portal Azure](https://portal.azure.com/).
1. Selecione **domínios personalizados**.

    Há uma série de pontos finais aos quais você pode atribuir um nome de domínio personalizado. No momento, os seguintes pontos de extremidade estão disponíveis:

    - **Gateway** (padrão `<apim-service-name>.azure-api.net`é: ),
    - **Portal** (o padrão é: `<apim-service-name>.portal.azure-api.net`),
    - **Gerenciamento** (o padrão é: `<apim-service-name>.management.azure-api.net`),
    - **SCM** (padrão `<apim-service-name>.scm.azure-api.net`é: ),
    - **NewPortal** (padrão `<apim-service-name>.developer.azure-api.net`é: ).

    > [!NOTE]
    > Apenas o ponto final **do Gateway** está disponível para configuração na camada Consumo.
    > Você pode atualizar todos os pontos de extremidade ou alguns deles. Comumente, os clientes atualizam **o Gateway** (essa URL é usada para chamar a API exposta através do Gerenciamento de API) e **o Portal** (o URL do portal do desenvolvedor).
    > **Os** pontos finais de gerenciamento e **SCM** são usados internamente apenas pelos proprietários de instâncias de gerenciamento de API e, portanto, são menos freqüentemente atribuídos a um nome de domínio personalizado.
    > O **nível Premium** suporta a definição de vários nomes de host para o ponto final do **Gateway.**

1. Selecione o ponto de extremidade que você deseja atualizar.
1. Na janela à direita, clique em **Personalizado**.

    - Em **Nome de domínio personalizado**, especifique o nome que você deseja usar. Por exemplo, `api.contoso.com`.
    - No **Certificado,** selecione um certificado do Key Vault. Você também pode carregar um válido . Arquivo PFX e fornecer sua **Senha,** se o certificado estiver protegido com uma senha.

    > [!NOTE]
    > Os nomes de domínio curinga, por exemplo, `*.contoso.com` são suportados em todos os níveis, exceto no nível Consumo.

    > [!TIP]
    > Recomendamos o uso do Azure Key Vault para gerenciar certificados e configurá-los para rodar automaticamente.
    > Se você usar o Azure Key Vault para gerenciar o certificado TLS/SSL de domínio personalizado, certifique-se de que o certificado está inserido no Key Vault [como um _certificado,_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate)não um _segredo_.
    >
    > Para obter um certificado TLS/SSL, o Gerenciamento de API deve ter a lista e obter permissões de segredos no Azure Key Vault contendo o certificado. Ao usar o portal Azure, todas as etapas de configuração necessárias serão concluídas automaticamente. Ao usar ferramentas de linha de comando ou API de gerenciamento, essas permissões devem ser concedidas manualmente. Isso é feito em duas etapas. Primeiro, use a página Identidades Gerenciadas na instância de gerenciamento de API para garantir que a Identidade Gerenciada esteja ativada e anote a identificação principal mostrada nessa página. Em segundo lugar, dê permissão à lista de permissões e obtenha permissões secretas para esta id principal no Cofre de Chaves do Azure contendo o certificado.
    >
    > Se o certificado estiver configurado para rodar automaticamente, o Gerenciamento de API pegará a versão mais recente automaticamente sem qualquer tempo de inatividade para o serviço (se o nível de Gerenciamento de API tiver SLA - ou seja, em todos os níveis, exceto no nível Desenvolvedor).

1. Clique em Aplicar.

    > [!NOTE]
    > O processo de atribuição do certificado pode levar aproximadamente 15 minutos dependendo do tamanho da implantação. O Desenvolvedor SKU tem tempo de inatividade, SKUs básicos e superiores não têm tempo de inatividade.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>Configuração de DNS

Ao configurar o DNS para o seu nome de domínio personalizado, você tem duas opções:

-   Configure um registro CNAME que aponte para o ponto final do nome de domínio personalizado configurado.
-   Configure um registro A que aponte para o endereço IP do gateway de gerenciamento de API.

> [!NOTE]
> Embora o endereço IP de ocorrência de Managment da API seja estático, ele pode mudar em alguns cenários. Por causa disso, recomenda-se usar CNAME ao configurar domínio personalizado. Leve isso em consideração ao escolher o método de configuração do DNS. Leia mais no [artigo de documentação IP](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses) e no [FAQ de Gerenciamento de API](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services).

## <a name="next-steps"></a>Próximas etapas

[Atualizar e colocar em escala o serviço](upgrade-and-scale.md)
