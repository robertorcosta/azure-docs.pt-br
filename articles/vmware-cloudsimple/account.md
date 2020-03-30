---
title: Gerenciamento de contas - Solução Azure VMware pelo portal CloudSimple
description: Descreve como gerenciar contas na solução Azure VMware pelo portal CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00348d36c38fdbfdf69a2e4e80a4c720268b40c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025360"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>Gerenciar contas na solução Azure VMware pelo portal CloudSimple

Quando você cria seu serviço CloudSimple, ele cria uma conta no CloudSimple. A conta está associada à sua assinatura do Azure onde o serviço está localizado. Todos os usuários com funções de proprietário e contribuinte na assinatura têm acesso ao portal CloudSimple. O ID de assinatura do Azure e o ID do inquilino associados ao serviço CloudSimple são encontrados na página Contas.

Para gerenciar contas no portal CloudSimple, [acesse o portal](access-cloudsimple-portal.md) e selecione **Conta** no menu lateral.

Selecione **Resumo** para visualizar informações sobre a configuração CloudSimple da sua empresa. A capacidade atual da configuração em nuvem é mostrada, incluindo número de Nuvens Privadas, armazenamento total, configuração de cluster vSphere, número de números de nós e número de núcleos de computação. Um link é incluído para comprar nós adicionais se a configuração atual não atender a todas as suas necessidades.

## <a name="email-alerts"></a>Alertas por email

Você pode adicionar endereços de e-mail de qualquer pessoa que você gostaria de notificar sobre alterações na configuração da Nuvem Privada.

1. Na área **de alertas de e-mail adicional,** clique **em Adicionar novo**.
2. Digite o endereço de e-mail.
3. Retorno à imprensa.  

Para remover uma entrada, clique em **X**.

## <a name="cloudsimple-operator-access"></a>Acesso ao operador CloudSimple

A configuração de acesso do operador permite que o CloudSimple o ajude com a solução de problemas, permitindo que um engenheiro de suporte faça login no seu portal CloudSimple.  A configuração é ativada por padrão. Todas as ações executadas pelo engenheiro de suporte quando conectados à sua conta do cliente são registradas e disponíveis para sua revisão na página**de Auditoria** **de Atividades.** > 

Clique no acesso ao **operador CloudSimple habilitado** a alternar para ativar ou desativar o acesso.
