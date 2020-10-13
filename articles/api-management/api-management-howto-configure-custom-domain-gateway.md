---
title: Configurar um nome de domínio personalizado para o gateway de gerenciamento de API do Azure auto-hospedado | Microsoft Docs
description: Este tópico descreve as etapas para configurar um nome de domínio personalizado para o gateway de gerenciamento de API do Azure de hospedagem interna.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: 0894203be4867e305c8e15467a2a867b9bfdc727
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86506801"
---
# <a name="configure-a-custom-domain-name"></a>Configurar um nome de domínio personalizado

Quando você provisiona um [Gateway de gerenciamento de API do Azure autohospedado](self-hosted-gateway-overview.md) , ele não recebe o nome do host e precisa ser referenciado por seu endereço IP. Este artigo mostra como mapear um nome DNS personalizado existente (também conhecido como hostname) de um gateway auto-hospedado.

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas neste artigo, você precisa ter:

-   Uma assinatura ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Uma instância de gerenciamento de API. Para obter mais informações, consulte [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
- Um gateway auto-hospedado. Para obter mais informações, consulte [como provisionar o gateway](api-management-howto-provision-self-hosted-gateway.md) de hospedagem interna
-   Um nome de domínio personalizado que pertence a você ou à sua organização. Este tópico não fornece instruções sobre como adquirir um nome de domínio personalizado.
-   Um registro DNS hospedado em um servidor DNS que mapeia o nome de domínio personalizado para o endereço IP do gateway de hospedagem interna. Este tópico não fornece instruções sobre como hospedar um registro DNS.
-   Você deve ter um certificado válido com chaves pública e privada (.PFX). A entidade ou o nome alternativo da entidade (SAN) deve corresponder ao nome de domínio (isso permite que a instância do gerenciamento de API exponha URLs com segurança por TLS).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Adicionar um certificado de domínio personalizado ao serviço de gerenciamento de API

1. Selecione **certificados** em **segurança**.
2. Selecione **+ Adicionar**.
3. Insira um nome de recurso para o certificado no campo **ID** .
4. Selecione o arquivo que contém o certificado (. PFX) selecionando o campo **certificado** ou o ícone de pasta adjacente a ele.
5. Insira a senha do certificado no campo **senha** .
6. Selecione **criar** para adicionar o certificado ao serviço de gerenciamento de API.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Use o portal do Azure para definir um nome de domínio personalizado para o gateway auto-hospedado

1. Selecione os **gateways** em **implantação e infraestrutura**.
2. Selecione o gateway auto-hospedado para o qual você deseja configurar o nome de domínio.
3. Selecione **nomes de host** em **configurações**.
4. Selecione **+ Adicionar**
5. Insira o nome do recurso para o nome do host no campo **Name** .
6. Insira o nome de domínio no campo **hostname** .
7. Selecione um certificado na lista suspensa **certificado** .
8. Marque a caixa de seleção **negociar certificado de cliente** se qualquer uma das APIs expostas por esse gateway usar autenticação de certificado de cliente.
    > [!WARNING]
    > Essa configuração é compartilhada por todos os nomes de domínio configurados para o gateway.
9. Selecione **Adicionar** para atribuir o nome de domínio personalizado para o gateway auto-hospedado selecionado.

## <a name="next-steps"></a>Próximas etapas

[Atualizar e colocar em escala o serviço](upgrade-and-scale.md)
