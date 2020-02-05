---
title: Gerenciamento de contas – portal da AVS (soluções VMware do Azure)
description: Descreve como gerenciar contas no portal da AVS (soluções VMware do Azure)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1fd7cb1c55fb664828448cef0b67ea9b16323bdf
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025360"
---
# <a name="manage-accounts-on-the-azure-vmware-solutions-avs-portal"></a>Gerenciar contas no portal da AVS (soluções VMware do Azure)

Quando você cria o serviço AVS, ele cria uma conta no AVS. A conta está associada à sua assinatura do Azure onde o serviço está localizado. Todos os usuários com funções de proprietário e colaborador na assinatura têm acesso ao portal da AVS. A ID de assinatura do Azure e a ID de locatário associada ao serviço AVS são encontradas na página contas.

Para gerenciar contas no portal da AVS, [acesse o portal](access-cloudsimple-portal.md) e selecione **conta** no menu lateral.

Selecione **Resumo** para exibir informações sobre a configuração da AVS da sua empresa. A capacidade atual de sua configuração de nuvem é mostrada, incluindo o número de nuvens privadas de AVS, o armazenamento total, a configuração de cluster vSphere, o número de nós e o número de núcleos de computação. Um link é incluído para comprar nós adicionais se a configuração atual não atender a todas as suas necessidades.

## <a name="email-alerts"></a>Alertas de email

Você pode adicionar endereços de email de qualquer pessoa que você gostaria de notificar sobre as alterações na configuração de nuvem privada da AVS.

1. Na área **alertas de email adicionais** , clique em **Adicionar novo**.
2. Insira o endereço de email.
3. Pressione retornar.  

Para remover uma entrada, clique em **X**.

## <a name="avs-operator-access"></a>Acesso do operador AVS

A configuração de acesso do operador permite a sincronização automática para ajudá-lo a solucionar problemas ao permitir que um engenheiro de suporte entre no portal da AVS. A configuração é habilitada por padrão. Todas as ações executadas pelo engenheiro de suporte quando conectado à sua conta de cliente são registradas e disponíveis para sua análise na página de **auditoria** de > de **atividades** .

Clique na opção **acesso do operador AVS habilitado** para ativar ou desativar o acesso.
