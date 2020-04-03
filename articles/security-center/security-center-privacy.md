---
title: Gerenciar dados de usuário na Central de Segurança do Azure | Microsoft Docs
description: Saiba como gerenciar os dados do usuário no Azure Security Center. O gerenciamento de dados de usuário inclui a capacidade de acessar, excluir ou exportar dados.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: memildin
ms.openlocfilehash: bf715d872fab421de30ebcb146a1981a7d008738
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585981"
---
# <a name="manage-user-data-in-azure-security-center"></a>Gerenciar dados de usuário na Central de Segurança do Azure
Este artigo fornece informações sobre como é possível gerenciar dados de usuário na Central de Segurança do Azure. O gerenciamento de dados de usuário inclui a capacidade de acessar, excluir ou exportar dados.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Um usuário da Central de Segurança com a função de Leitor, Proprietário, Colaborador ou Administrador da Conta pode acessar os dados do cliente dentro da ferramenta. Para saber mais sobre a função administrador de contas, consulte [funções incorporadas para o controle de acesso baseado em funções do Azure](../role-based-access-control/built-in-roles.md) para saber mais sobre as funções Leitor, Proprietário e Contribuinte. Consulte [os administradores de assinatura do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Pesquisar e identificar dados pessoais
Um usuário da Central de Segurança pode exibir os dados pessoais por meio do portal do Azure. A Central de Segurança armazena apenas detalhes de contato de segurança, como endereços de email e números de telefone. Para obter mais informações, consulte [Fornecer detalhes de contato de segurança no Azure Security Center](security-center-provide-security-contact-details.md).

No portal Azure, um usuário pode visualizar configurações IP permitidas usando o recurso de acesso de VM just-in-time do Security Center. Para obter mais informações, consulte [Gerenciar o acesso à máquina virtual usando o just-in-time](security-center-just-in-time.md).

No portal do Azure, um usuário pode exibir alertas de segurança fornecidos pela Central de Segurança, incluindo endereços IP e detalhes do invasor. Para obter mais informações, consulte [Gerenciar e responder a alertas de segurança no Azure Security Center](security-center-managing-and-responding-alerts.md).

## <a name="classifying-personal-data"></a>Classificar dados pessoais
Você não precisa classificar os dados pessoais encontrados no recurso de contato de segurança do Security Center. Os dados salvos são um endereço de email (ou vários endereços de email) e um número de telefone. [Dados de contato](security-center-provide-security-contact-details.md) são validados pela Central de Segurança.

Você não precisa classificar os endereços IP e os números de porta salvos pelo recurso [just-in-time](security-center-just-in-time.md) do Security Center.

Somente um usuário com a função de Administrador pode classificar dados pessoais [exibindo alertas](security-center-managing-and-responding-alerts.md) na Central de Segurança.

## <a name="securing-and-controlling-access-to-personal-data"></a>Protegendo e controlando o acesso a informações pessoais
Um usuário da Central de Segurança com a função Leitor, Proprietário, Colaborador ou Administrador da Conta pode acessar [dados de contato de segurança](security-center-provide-security-contact-details.md).

Um usuário do Security Center atribuído à função de Leitor, Proprietário, Contribuinte ou Administrador de Conta pode acessar suas políticas [just-in-time.](security-center-just-in-time.md)

Um usuário da Central de Segurança com a função Leitor, Proprietário, Colaborador ou Administrador da Conta pode exibir [alertas](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Atualizar dados pessoais
Um usuário da Central de Segurança atribuído à função Proprietário, Colaborador ou Administrador da Conta pode atualizar [dados de contato de segurança](security-center-provide-security-contact-details.md) por meio do portal do Azure.

Um usuário do Security Center atribuído à função de Proprietário, Contribuinte ou Administrador de Conta pode atualizar suas [políticas just-in-time](security-center-just-in-time.md).

Um administrador de conta não pode editar incidentes de alerta. Um [incidente de alerta](security-center-managing-and-responding-alerts.md) é considerado dados de segurança e é somente leitura.

## <a name="deleting-personal-data"></a>Excluir dados pessoais
Um usuário da Central de Segurança atribuído à função Proprietário, Colaborador ou Administrador da Conta pode excluir [dados de contato de segurança](security-center-provide-security-contact-details.md) por meio do portal do Azure.

Um usuário do Security Center atribuído à função de Proprietário, Contribuinte ou Administrador de Conta pode excluir as [políticas just-in-time](security-center-just-in-time.md) através do portal Azure.

Um usuário do Security Center não pode excluir incidentes de alerta. Por razões de segurança, um incidente de [alerta](security-center-managing-and-responding-alerts.md) é considerado apenas dados de leitura.

## <a name="exporting-personal-data"></a>Exportar dados pessoais
Um usuário da Central de Segurança com a função Leitor, Proprietário, Colaborador ou Administrador da Conta pode exportar [dados de contato de segurança](security-center-provide-security-contact-details.md):

- Copiando do portal Azure
- Executando a chamada à API REST do Azure, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

Um usuário do Security Center atribuído à função de Administrador de contas pode exportar as [políticas just-in-time](security-center-just-in-time.md) que contêm os endereços IP por:

- Copiando do portal Azure
- Executando a chamada à API REST do Azure, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

Um Administrador da Conta pode exportar os detalhes do alerta:

- Copiando do portal Azure
- Executando a chamada à API REST do Azure, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Para obter mais informações, consulte [Obter alertas de segurança (GET Collection)](https://msdn.microsoft.com/library/mt704050.aspx).

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Restringir o uso de dados pessoais para criação de perfil ou marketing sem consentimento
Um usuário da Central de Segurança pode optar por excluir seus [dados de contato de segurança](security-center-provide-security-contact-details.md).

[Os dados just-in-time](security-center-just-in-time.md) são considerados dados não identificáveis e são retidos por um período de 30 dias.

[Dados de alerta](security-center-managing-and-responding-alerts.md) são considerados dados de segurança e ficam retidos por um período de dois anos.

## <a name="auditing-and-reporting"></a>Auditoria e relatórios
Os registros de auditoria de contatos de segurança, atualizações de just-in-time e alertas são mantidos nos Registros de [Atividades do Azure](../azure-monitor/platform/platform-logs-overview.md).