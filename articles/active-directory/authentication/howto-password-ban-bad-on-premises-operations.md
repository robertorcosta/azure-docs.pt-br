---
title: Habilite a proteção por senha ad ad azure no local
description: Saiba como ativar a proteção por senha do Azure AD para um ambiente de serviços de domínio de diretório ativo no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d00a8b84477226b68913f95c5121bbbdfc2eb09d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263810"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>Habilite a proteção de senha do azure active directory no local

Os usuários geralmente criam senhas que usam palavras locais comuns, como uma escola, equipe esportiva ou pessoa famosa. Essas senhas são fáceis de adivinhar e fracas contra ataques baseados em dicionários. Para impor senhas fortes em sua organização, o Azure Active Directory (Azure AD) Password Protection fornece uma lista de senhas banidas global e personalizada. Uma solicitação de alteração de senha falha se houver uma correspondência nesta lista de senhas banidas.

Para proteger o ambiente AD DS (Active Directory Domain Services, serviços de domínio de diretório ativo) no local, você pode instalar e configurar o Azure AD Password Protection para trabalhar com o seu DC on-prem. Este artigo mostra como ativar o Azure AD Password Protection para o seu ambiente local.

Para obter mais informações sobre como o Azure AD Password Protection funciona em um ambiente local, consulte [Como reforçar a proteção por senha do Azure AD para o Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="before-you-begin"></a>Antes de começar

Este artigo mostra como ativar o Azure AD Password Protection para o seu ambiente local. Antes de concluir este artigo, [instale e registre os agentes proxy e os agentes DC do Azure AD Password Protection](howto-password-ban-bad-on-premises-deploy.md) em seu ambiente AD DS no local.

## <a name="enable-on-premises-password-protection"></a>Habilite a proteção por senha no local

1. Faça login no [portal Azure](https://portal.azure.com) e navegue pelos**métodos** > de autenticação de > **segurança** > do **diretório ativo do Azure****.**
1. Defina a opção **Para Ativar proteção por senha no Diretório Ativo do Servidor Windows** como *Sim*.

    Quando esta configuração é definida como *Não,* todos os agentes AD Password Protection DC implantados entram em um modo quiescente onde todas as senhas são aceitas como estão. Nenhuma atividade de validação é realizada e eventos de auditoria não são gerados.

1. Recomenda-se definir inicialmente o **modo de** *auditoria*. Depois de se sentir confortável com o recurso e o impacto sobre os usuários da sua organização, você pode mudar o **modo** para *O Imposto*. Para obter mais informações, consulte a seção a seguir sobre [os modos de operação](#modes-of-operation).
1. Quando estiver pronto, selecione **Salvar**.

    [![](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png "Enable on-premises password protection under Authentication Methods in the Azure portal")](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>Modos de operação

Quando você habilita o Azure AD Password Protection no local, você pode usar o modo *de auditoria* ou o modo *de execução.* Recomendamos que a implantação inicial e os testes sempre comecem no modo de auditoria. As entradas no registro de eventos devem então ser monitoradas para antecipar se algum processo operacional existente será perturbado assim *que o* modo Impor estiver ativado.

### <a name="audit-mode"></a>Modo de auditoria

*O* modo de auditoria destina-se a ser uma maneira de executar o software no modo "e se". Cada serviço de agente DC de proteção por senha do Azure AD avalia uma senha recebida de acordo com a política ativa atualmente.

Se a política atual estiver configurada para estar no modo de auditoria, senhas "ruins" resultarão em mensagens de registro de eventos, mas são processadas e atualizadas. Esse comportamento é a única diferença entre o modo de auditoria e de execução. Todas as outras operações são iguais.

### <a name="enforced-mode"></a>Modo forçado

*O* modo aplicado é destinado como a configuração final. Como quando no modo de auditoria, cada serviço de agente DC do Azure AD Password Protection DC avalia as senhas recebidas de acordo com a política ativa atualmente. No entanto, quando o modo aplicado é ativado, uma senha considerada insegura de acordo com a política é rejeitada.

Quando uma senha é rejeitada no modo forçado pelo agente DC de Proteção por Senha AD do Azure, um usuário final vê um erro semelhante, como se veria se sua senha foi rejeitada pela aplicação tradicional da complexidade da senha no local. Por exemplo, um usuário pode ver a seguinte mensagem de erro tradicional no login do Windows ou alterar a tela de senha:

*"Não é possível atualizar a senha. O valor fornecido para a nova senha não atende aos requisitos de comprimento, complexidade ou histórico do domínio."*

Essa mensagem é apenas um exemplo dos vários resultados possíveis. A mensagem de erro específica pode variar dependendo do software ou cenário real que está tentando definir uma senha insegura.

Os usuários finais afetados podem precisar trabalhar com sua equipe de TI para entender os novos requisitos e escolher senhas seguras.

> [!NOTE]
> O Azure AD Password Protection não tem controle sobre a mensagem de erro específica exibida pela máquina cliente quando uma senha fraca é rejeitada.

## <a name="next-steps"></a>Próximas etapas

Para personalizar a lista de senhas proibidas para sua organização, consulte [Configurar a lista de senhas personalizadas do Azure AD Password Protection](tutorial-configure-custom-password-protection.md).

Para monitorar eventos on-prem, consulte [Monitoramento no prem Azure AD Password Protection](howto-password-ban-bad-on-premises-monitor.md).
