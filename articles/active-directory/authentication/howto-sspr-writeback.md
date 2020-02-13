---
title: Configurar write-back de senha para SSPR-Azure Active Directory
description: Usar o Azure AD e o Azure AD Connect para o write-back de senhas para um diretório local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1fa447312ad6a1f92eaed1164020cb6ee95606e
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161589"
---
# <a name="how-to-configure-password-writeback"></a>Instruções: Configurar write-back de senha

As etapas a seguir pressupõem que você já tenha configurado o Azure AD Connect em seu ambiente usando as configurações [Expressas](../hybrid/how-to-connect-install-express.md) ou [Personalizadas](../hybrid/how-to-connect-install-custom.md).

1. Para configurar e habilitar o write-back de senha, entre no servidor do Azure AD Connect e inicie o assistente de configuração do **Azure AD Connect**.
2. Na página de **Boas-vindas**, selecione **Configurar**.
3. Na página **Tarefas adicionais**, selecione **Personalizar opções de sincronização** e, em seguida, selecione **Próximo**.
4. Na página **Conectar-se ao Azure AD**, insira uma credencial de administrador global e escolha **Próximo**.
5. Nas páginas de filtragem **Conectar diretórios** e **Domínio/OU**, selecione **Próximo**.
6. Na página **Recursos opcionais**, selecione a caixa ao lado de **Write-back de senha** e selecione **Próximo**.
   ![Habilitar write-back de senha no Azure AD Connect][Writeback]
7. Na página **Pronto para configurar**, clique em **Configurar** e aguarde a conclusão do processo.
8. Quando você vir a configuração terminar, selecione **Sair**.

Para tarefas comuns de solução de problemas relacionadas ao write-back de senha, consulte a seção [Solucionar problemas de write-back de senha](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) em nosso artigo de solução de problemas.

> [!WARNING]
> O write-back de senha deixará de funcionar para clientes que usam as versões 1.0.8641.0 e posteriores do Azure AD Connect quando o [serviço de ACS (Controle de Acesso do Microsoft Azure) for desativado em 7 de novembro de 2018](../azuread-dev/active-directory-acs-migration.md). As versões do Azure AD Connect 1.0.8641.0 e anteriores não permitirão mais o write-back de senha porque dependem do ACS para essa funcionalidade.
>
> Para evitar uma interrupção no serviço, atualize de uma versão anterior do Azure AD Connect para uma versão mais recente, consulte o artigo [Azure AD Connect: atualização de uma versão anterior para a mais recente](../hybrid/how-to-upgrade-previous-version.md)
>

## <a name="licensing-requirements-for-password-writeback"></a>Requisitos de licenciamento do write-back de senha

**Redefinição/alteração/desbloqueio de senha de autoatendimento com write-back local é um recurso Premium do Azure AD**. Para obter mais informações sobre licenciamento, consulte o [site de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Para usar o write-back de senha, você deve ter uma das licenças a seguir atribuídas no locatário:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 ou A3
* Enterprise Mobility + Security E5 ou A5
* Microsoft 365 E3 ou A3
* Microsoft 365 E5 ou A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> Os planos de licenciamento do Office 365 autônomo *não oferecem suporte à/ao "Redefinição/alteração/desbloqueio de senha de autoatendimento com write-back local"* e exigem que você tenha um dos planos anteriores para que esse recurso funcione.
>

## <a name="active-directory-permissions-and-on-premises-password-complexity-policies"></a>Permissões de Active Directory e políticas de complexidade de senha local 

A conta especificada no utilitário do Azure AD Connect deve ter os seguintes itens configurados para estar no escopo para SSPR:

* **Redefinir senha** 
* **Alterar senha** 
* **Permissões de gravação** em `lockoutTime`
* **Permissões de gravação** em `pwdLastSet`
* **Direitos estendidos** em:
   * O objeto raiz de *cada domínio* na floresta
   * As unidades organizacionais (OUs) do usuário que você deseja que estejam no escopo para SSPR

Se não tiver certeza sobre a qual conta a conta descrita acima se refere, abra a interface do usuário da configuração do Azure Active Directory Connect e selecione a opção **Exibir configuração atual**. A conta à qual você precisa adicionar a permissão é listada em **Diretórios Sincronizados**.

Se você definir essas permissões, a conta de serviço de MA de cada floresta poderá gerenciar senhas em nome das contas de usuário nessa floresta. 

> [!IMPORTANT]
> Se você não atribuir essas permissões, mesmo que o write-back pareça estar configurado corretamente, os usuários verão erros ao tentar gerenciar suas senhas locais na nuvem.
>

> [!NOTE]
> Pode levar até uma hora ou mais para que essas permissões sejam replicadas em todos os objetos no diretório.
>

Para configurar as permissões apropriadas para que ocorra o write-back de senha, conclua as etapas a seguir:

1. Abra Usuários e Computadores do Active Directory com uma conta que tem as permissões de administração de domínio apropriadas.
2. No menu **Exibir**, verifique se a opção **Recursos avançados** está ativada.
3. No painel esquerdo, clique com o botão direito do mouse no objeto que representa a raiz do domínio e escolha **Propriedades** > **Segurança** > **Avançado**.
4. Na guia **Permissões**, selecione **Adicionar**.
5. Selecione a conta à qual as permissões estão sendo aplicadas (na configuração do Azure AD Connect).
6. Na lista suspensa **Aplica-se a**, selecione os **objetos de Usuário Descendente**.
7. Em **Permissões**, selecione as caixas para as seguintes opções:
    * **Alterar senha**
    * **Redefinir senha**
8. Em **Permissões**, marque as caixas das seguintes opções:
    * **Gravar lockoutTime**
    * **Gravar pwdLastSet**
9. Selecione **Aplicar/OK** para aplicar as alterações e sair das caixas de diálogo abertas.

Como a origem da autoridade está no local, as políticas de complexidade de senha se aplicam da mesma fonte de dados conectada. Verifique se você alterou as políticas de grupo existentes para "duração mínima da senha". A política de grupo não deve ser definida como 1, o que significa que a senha deve ter pelo menos um dia de idade antes de ser atualizada. Você precisa ter certeza de que ele está definido como 0. Essas configurações podem ser encontradas em `gpmc.msc` em **configuração do computador > políticas > configurações do Windows > configurações de segurança > políticas de conta**. Execute `gpupdate /force` para garantir que a alteração entra em vigor. 

## <a name="next-steps"></a>Próximas etapas

[O que é write-back de senha?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Habilitar write-back de senha no Azure AD Connect"
