---
title: Acesso Condicional - Acesso ao bloco - Diretório Ativo do Azure
description: Crie uma política de acesso condicional personalizado para
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb,
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9ea88da0a575647eecacbfd8a476202f814b821
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631804"
---
# <a name="conditional-access-block-access"></a>Acesso Condicional: Acesso ao bloco

Para organizações com uma abordagem conservadora de migração em nuvem, a política do bloco é uma opção que pode ser usada. 

> [!CAUTION]
> A configuração errada de uma política de blocos pode levar as organizações a serem bloqueadas do portal Azure.

Políticas como essas podem ter efeitos colaterais não intencionais. Testes e validação adequados são vitais antes de habilitar. Os administradores devem utilizar ferramentas como o [modo conditional access somente e](concept-conditional-access-report-only.md) a ferramenta E se no Acesso [Condicional](what-if-tool.md) ao fazer alterações.

## <a name="user-exclusions"></a>Exclusões de usuários

As políticas de acesso condicional são ferramentas poderosas, recomendamos excluir as seguintes contas da sua política:

* **Acesso de emergência** ou **contas de vidro de quebra** para evitar o bloqueio de contas em todo o inquilino. No cenário improvável em que todos os administradores estão bloqueados do seu inquilino, sua conta administrativa de acesso de emergência pode ser usada para entrar no inquilino para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo, [Gerenciar contas de acesso de emergência no Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Contas de** serviço e **princípios de serviço,** como a Conta Sincronizada do Azure AD Connect. Contas de serviço são contas não interativas que não estão vinculadas a nenhum usuário em particular. Eles são normalmente usados por serviços back-end que permitem acesso programático aos aplicativos, mas também são usados para fazer login em sistemas para fins administrativos. Contas de serviço como essas devem ser excluídas, uma vez que o MFA não pode ser concluído programáticamente.
   * Se sua empresa tiver essas contas em uso em scripts ou código, considere substituí-las [por identidades gerenciadas](../managed-identities-azure-resources/overview.md). Como uma solução temporária, você pode excluir essas contas específicas da política de linha de base.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de Acesso Condicional

As etapas a seguir ajudarão a criar políticas de acesso condicional para bloquear o acesso a todos os aplicativos, exceto [o Office 365,](concept-conditional-access-cloud-apps.md#office-365-preview) se os usuários não estiverem em uma rede confiável. Essas políticas são colocadas no [modo somente relatório](howto-conditional-access-report-only.md) para iniciar para que os administradores possam determinar o impacto que terão nos usuários existentes. Quando os administradores estão confortáveis de que as políticas se aplicam como pretendem, eles podem trocá-las para **On**.

A primeira política bloqueia o acesso a todos os aplicativos, exceto os aplicativos do Office 365, se não em um local confiável.

1. Faça login no **portal Azure** como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. Navegue até o Acesso**Condicional de****Segurança** > do Diretório >  **Ativo do Azure**.
1. Selecione **Nova política**.
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições**, selecione **Usuários e grupos**.
   1. Em **Incluir,** selecione **Todos os usuários**.
   1. Em **Exclusão,** selecione **Usuários e grupos** e escolha o acesso de emergência ou contas de vidro de quebra de sua organização. 
   1. Selecione **Feito**.
1. Em **aplicativos ou ações na Nuvem,** selecione as seguintes opções:
   1. Em **Incluir,** selecione **Todos os aplicativos em nuvem**.
   1. Em **Exclusão,** selecione **Office 365 (visualização)** e **selecione Selecionar**e selecione **'Feito 'Feito '''''''**
1. Em **Condições:**
   1. Em **Condições** > **de Localização**.
      1. Definir **configurar** como **Sim**
      1. Em **Incluir,** selecione **Qualquer local**.
      1. Em **Exclusão,** selecione **Todos os locais confiáveis**.
      1. Selecione **Feito**.
   1. Em **Aplicativos clientes (Preview),** **configure Configure** para **Sim**e selecione **Feito,** em **seguida, Feito**.
1. Em **Controles de** > **acesso, selecione** **Bloquear acesso**e selecione Selecionar **Selecionar**.
1. Confirme suas configurações e **configure Ativar** a diretiva para **somente relatório**.
1. Selecione **Criar** para criar para ativar sua política.

Uma segunda política é criada abaixo para exigir autenticação multifatorial ou um dispositivo compatível para usuários do Office 365.

1. Selecione **Nova política**.
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições**, selecione **Usuários e grupos**.
   1. Em **Incluir,** selecione **Todos os usuários**.
   1. Em **Exclusão,** selecione **Usuários e grupos** e escolha o acesso de emergência ou contas de vidro de quebra de sua organização. 
   1. Selecione **Feito**.
1. Em **Aplicativos ou ações** > na**Nuvem, selecione** **Selecionar aplicativos,** escolha **o Office 365 (visualização)** e **selecione Selecionar**, em seguida, **Feito**.
1. Em **Controles de** > **acesso, grant**, selecione Acesso de **Subvenção**.
   1. Selecione **Exigir autenticação de vários fatores** e **exigir que o dispositivo seja marcado como compatível** selecionar **selecionar**.
   1. **Certifique-se de exigir que todos os controles selecionados estão selecionados.**
   1. Selecione **Selecionar**.
1. Confirme suas configurações e **configure Ativar** a diretiva para **somente relatório**.
1. Selecione **Criar** para criar para ativar sua política.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

[Determine o impacto usando o modo somente de relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simule o comportamento do sinal usando a ferramenta Acesso Condicional E se](troubleshoot-conditional-access-what-if.md)
