---
title: Configure um nome de domínio personalizado para o gateway de gerenciamento de API do Azure auto-hospedado | Microsoft Docs
description: Este tópico descreve as etapas para configurar um nome de domínio personalizado para gateway de gerenciamento de API do Azure auto-hospedado.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 1f2184c7c62887a98a76877528b167d173c3d75b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335942"
---
# <a name="configure-a-custom-domain-name"></a>Configurar um nome de domínio personalizado

Quando você fornece um [gateway de gerenciamento de API azure auto-hospedado,](self-hosted-gateway-overview.md) ele não é atribuído nome de host e tem que ser referenciado por seu endereço IP. Este artigo mostra como mapear um nome DNS personalizado existente (também chamado de hostname) um gateway auto-hospedado.

> [!NOTE]
> O recurso gateway auto-hospedado está em visualização. Durante a visualização, o gateway auto-hospedado está disponível apenas nos níveis Desenvolvedor e Premium sem nenhum custo adicional. O nível do desenvolvedor está limitado a uma única implantação de gateway auto-hospedado.

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas neste artigo, você precisa ter:

-   Uma assinatura ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Uma instância de gerenciamento de API. Para obter mais informações, consulte [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
- Um portal auto-hospedado. Para obter mais informações, consulte [Como provisionar gateway auto-hospedado](api-management-howto-provision-self-hosted-gateway.md)
-   Um nome de domínio personalizado que é de sua propriedade ou de sua organização. Este tópico não fornece instruções sobre como obter um nome de domínio personalizado.
-   Um registro De DNS hospedado em um servidor DNS que mapeia o nome de domínio personalizado para o endereço IP do gateway auto-hospedado. Este tópico não fornece instruções sobre como hospedar um registro DNS.
-   Você deve ter um certificado válido com chaves pública e privada (.PFX). O nome alternativo de assunto ou assunto (SAN) tem que corresponder ao nome de domínio (isso permite que a instância de gerenciamento de API exponha URLs com segurança sobre TLS).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Adicionar certificado de domínio personalizado ao seu serviço de gerenciamento de API

1. Selecione **Certificados** em **Segurança**.
2. Selecione **+ Adicionar**.
3. Digite um nome de recurso para o certificado no **campo Ded.**
4. Selecione o arquivo que contém o certificado (. PFX) selecionando o campo **Certificado** ou o ícone da pasta adjacente a ele.
5. Digite a senha do certificado no campo **Senha.**
6. Selecione **Criar** para adicionar o certificado ao seu serviço de gerenciamento de API.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Use o portal Azure para definir um nome de domínio personalizado para o seu gateway auto-hospedado

1. Selecione os **Gateways** em **Configurações**.
2. Selecione o gateway auto-hospedado para o que deseja configurar o nome de domínio.
3. Selecione **Nomes de host em** **Configurações**.
4. Selecione **+ Adicione**
5. Digite o nome do recurso para o nome de host no campo **Nome.**
6. Digite o nome de domínio no campo **Hostname.**
7. Selecione um certificado na retirada do **certificado.**
8. Selecione Negociar caixa de **seleção** de certificado de cliente se alguma das APIs expostas através deste gateway usar autenticação de certificado cliente.
    > [!WARNING]
    > Esta configuração é compartilhada por todos os nomes de domínio configurados para o gateway.
9. Selecione **Adicionar** para atribuir o nome de domínio personalizado ao gateway auto-hospedado selecionado.

## <a name="next-steps"></a>Próximas etapas

[Atualizar e colocar em escala o serviço](upgrade-and-scale.md)
