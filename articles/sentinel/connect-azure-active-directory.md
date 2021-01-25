---
title: Conectar dados de Azure Active Directory ao Azure Sentinel | Microsoft Docs
description: Saiba como coletar dados de Azure Active Directory e transmitir logs de entrada e logs de auditoria do Azure AD para o Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: 409a316bd9c4222dd9b8ff30e42e37d23805c38b
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757755"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Conectar dados de Azure Active Directory (Azure AD)

Você pode usar o conector interno do Sentinela do Azure para coletar dados de [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) e transmiti-los para o Azure Sentinel. O conector permite que você transmita [logs de entrada](../active-directory/reports-monitoring/concept-sign-ins.md) e [logs de auditoria](../active-directory/reports-monitoring/concept-audit-logs.md).

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer licença do Azure AD (gratuito/O365/P1/P2) é suficiente para ingerir os logs de entrada no Sentinela do Azure. Encargos adicionais por gigabyte podem ser aplicados por Azure Monitor (Log Analytics) e pelo Azure Sentinel.

- O usuário deve receber a função colaborador do Azure Sentinel no espaço de trabalho.

- O usuário deve receber as funções de administrador global ou de administrador de segurança no locatário do qual você deseja transmitir os logs.

- Seu usuário deve ter permissões de leitura e gravação para as configurações de diagnóstico do Azure AD a fim de poder ver o status da conexão. 

## <a name="connect-to-azure-active-directory"></a>Conectar ao Active Directory do Azure

1. No Azure Sentinel, selecione **conectores de dados** no menu de navegação.

1. Na Galeria de conectores de dados, selecione **Azure Active Directory** e, em seguida, selecione a **página abrir conector**.

1. Marque as caixas de seleção ao lado dos tipos de log que você deseja transmitir para o Azure Sentinel e clique em **conectar**. Estes são os tipos de log dos quais você pode escolher:

    - **Logs de entrada**: informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
    - **Logs de auditoria**: informações de atividade do sistema sobre gerenciamento de usuários e grupos, aplicativos gerenciados e atividades de diretório.
    - **Logs de entrada de usuário não interativo**: informações sobre entradas executadas por um cliente em nome de um usuário, que não exigem nenhuma interação ou fator de autenticação do usuário.
    - **Logs de entrada da entidade de serviço**: informações sobre entradas por aplicativos e entidades de serviço que não envolvem nenhum usuário. Nessas entradas, o aplicativo ou serviço fornece uma credencial em seu próprio nome para autenticar ou acessar recursos.
    - **Logs de entrada de identidade gerenciada**: entradas por recursos do Azure que têm segredos gerenciados pelo Azure.
    - **Logs de provisionamento**: informações sobre a atividade do sistema sobre usuários, grupos e funções provisionadas pelo serviço de provisionamento do Azure AD.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs**, na seção **LogManagement** , nas tabelas a seguir:

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Para consultar os logs do Azure AD, insira o nome da tabela relevante na parte superior da janela de consulta.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar Azure Active Directory ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
