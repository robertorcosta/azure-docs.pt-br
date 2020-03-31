---
title: 'Azure AD Connect: Autenticação em nuvem via rollout encenado | Microsoft Docs'
description: Este artigo explica como migrar da autenticação federada para a autenticação em nuvem usando um rollout em etapas.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3044ebdd716eb85dc63d3a77089912d0d51d8b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74915224"
---
# <a name="migrate-to-cloud-authentication-by-using-staged-rollout-preview"></a>Migrar para autenticação em nuvem usando o rollout em estágio (visualização)

Usando uma abordagem de rollout em etapas, você pode migrar da autenticação federada para a autenticação em nuvem. Este artigo discute como fazer a troca. Antes de começar a implantação em etapas, no entanto, você deve considerar as implicações se uma ou mais das seguintes condições forem verdadeiras:
    
-  No momento, você está usando um servidor de autenticação multifatorial no local. 
-  Você está usando cartões inteligentes para autenticação. 
-  Seu servidor atual oferece certos recursos somente para federação.

Antes de experimentar este recurso, sugerimos que você revise nosso guia sobre a escolha do método de autenticação certo. Para obter mais informações, consulte a tabela "Comparando métodos" em [Escolher o método de autenticação certo para a solução de identidade híbrida do Azure Active Directory](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods).

Para obter uma visão geral do recurso, consulte este "Azure Active Directory: O que é rollout encenado?" Vídeo:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Pré-requisitos

-   Você tem um inquilino do Azure Active Directory (Azure AD) com domínios federados.

-   Você decidiu mudar para qualquer uma das duas opções:
    - **Opção Uma** - sincronização de hash de*senha (sincronização)* + *sem sinal único (SSO)*
    - **Opção B** - *de autenticação* + de passagem*perfeita SSO*
    
    Embora *o SSO perfeito* seja opcional, recomendamos permitir que ele obtenha uma experiência de login silenciosa para usuários que estão executando máquinas de domínio de dentro de uma rede corporativa.

-   Você configurou todas as políticas de marca de inquilino e acesso condicional que você precisa para usuários que estão sendo migrados para autenticação em nuvem.

-   Se você planeja usar a Autenticação Multifatorial do Azure, recomendamos que você use [o registro convergente para redefinição de senha de autoatendimento (SSPR) e autenticação multifatorial](../authentication/concept-registration-mfa-sspr-combined.md) para que seus usuários registrem seus métodos de autenticação uma vez.

-   Para usar o recurso de rollout encenado, você precisa ser um administrador global em seu inquilino.

-   Para habilitar *o SSO perfeito* em uma floresta específica de Diretório Ativo, você precisa ser um administrador de domínio.

## <a name="supported-scenarios"></a>Cenários com suporte

Os seguintes cenários são suportados para a implantação em etapas. O recurso funciona apenas para:

- Usuários que são provisionados para o Azure AD usando o Azure AD Connect. Não se aplica a usuários somente em nuvem.

- Tráfego de login do usuário em navegadores e clientes *de autenticação modernos.* Aplicativos ou serviços em nuvem que usam autenticação legado voltarão aos fluxos de autenticação federados. Um exemplo pode ser o Exchange on-line com autenticação moderna desligada, ou o Outlook 2010, que não suporta autenticação moderna.

## <a name="unsupported-scenarios"></a>Cenários sem suporte

Os seguintes cenários não são suportados para a implantação em etapas:

- Certos aplicativos enviam o parâmetro de consulta "domain_hint" para o Azure AD durante a autenticação. Esses fluxos continuarão, e os usuários habilitados para a implantação em etapas continuarão a usar a federação para autenticação.

<!-- -->

- Os admins podem implantar autenticação em nuvem usando grupos de segurança. Para evitar latência de sincronização quando estiver usando grupos de segurança do Active Directory no local, recomendamos que você use grupos de segurança na nuvem. Aplicam-se as seguintes condições:

    - Você pode usar um máximo de 10 grupos por recurso. Ou seja, você pode usar 10 grupos cada para *sincronização de hash*de senha, *autenticação de passagem*e *SSO sem emendas*.
    - Grupos aninhados não são *suportados*. Este escopo também se aplica à visualização pública.
    - Grupos dinâmicos não são *suportados* para a implantação em etapas.
    - Objetos de contato dentro do grupo impedirão que o grupo seja adicionado.

- Você ainda precisa fazer o corte final de federado para autenticação em nuvem usando o Azure AD Connect ou powerShell. A implantação em etapas não alterna domínios de federados para gerenciados.

- Quando você adiciona pela primeira vez um grupo de segurança para o lançamento em etapas, você está limitado a 200 usuários para evitar um intervalo de UX. Depois de adicionar o grupo, você pode adicionar mais usuários diretamente a ele, conforme necessário.

## <a name="get-started-with-staged-rollout"></a>Comece com o lançamento encenado

Para testar o login de sincronização de *hash* de senha usando a implantação em etapas, siga as instruções de pré-trabalho na próxima seção.

Para obter informações sobre quais cmdlets powershell usar, consulte [a pré-visualização do Azure AD 2.0](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="pre-work-for-password-hash-sync"></a>Pré-trabalho para sincronização de hash de senha

1. Habilite a *sincronização* de hash de senha na página [Recursos](how-to-connect-install-custom.md#optional-features) opcionais no Azure AD Connect. 

   ![Captura de tela da página "Recursos opcionais" no Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Certifique-se de que um ciclo completo de *sincronização de hash* de senha tenha sido executado para que todos os hashes de senha dos usuários tenham sido sincronizados com o Azure AD. Para verificar o status da sincronização de hash de *senha,* você pode usar os diagnósticos do PowerShell em [Solucionar problemas de sincronização de hash com sincronização do Azure AD Connect](tshoot-connect-password-hash-synchronization.md).

   ![Captura de tela do registro de solução de problemas do AADConnect](./media/how-to-connect-staged-rollout/sr2.png)

Se você quiser testar o login *de autenticação pass-through* usando a distribuição em etapas, habilite-o seguindo as instruções de pré-trabalho na próxima seção.

## <a name="pre-work-for-pass-through-authentication"></a>Pré-trabalho para autenticação de passagem

1. Identifique um servidor que está executando o Windows Server 2012 R2 ou posterior onde você deseja que o agente *de autenticação de passagem* seja executado. 

   *Não* escolha o servidor Azure AD Connect.Certifique-se de que o servidor está unido por domínio, pode autenticar usuários selecionados com o Active Directory e pode se comunicar com o Azure AD em portas de saída e URLs. Para obter mais informações, consulte a seção "Passo 1: Verifique os pré-requisitos" do [Quickstart: Azure AD sem sinal único](how-to-connect-sso-quick-start.md).

1. [Baixe o agente de autenticação Azure AD Connect](https://aka.ms/getauthagent)e instale-o no servidor. 

1. Para habilitar [alta disponibilidade,](how-to-connect-sso-quick-start.md)instale agentes de autenticação adicionais em outros servidores.

1. Certifique-se de que configurou as configurações do [Smart Lockout](../authentication/howto-password-smart-lockout.md) adequadamente. Fazer isso ajuda a garantir que as contas de diretório ativo no local dos usuários não seja bloqueada por maus atores.

Recomendamos ativar *o SSO perfeito* independentemente do método de login *(sincronização* de hash de senha ou *autenticação de passagem)* selecionado para a implantação em etapas. Para habilitar *o SSO perfeito,* siga as instruções de pré-trabalho na próxima seção.

## <a name="pre-work-for-seamless-sso"></a>Pré-trabalho para SSO perfeito

 *Habilite o SSO* perfeito nas florestas do Active Directory usando o PowerShell. Se você tiver mais de uma floresta de Diretório Ativo, habilite-a para cada floresta individualmente.  *O SSO sem emendas* é acionado apenas para usuários selecionados para a implantação em etapas. Não afeta sua configuração de federação existente.

*Habilite o SSO perfeito* fazendo o seguinte:

1. Faça login no Azure AD Connect Server.

2. Vá para a pasta  *%programfiles%\\Microsoft Azure Active Directory Connect.*

3. Importe o *módulo SSO* PowerShell perfeito executando o seguinte comando: 

   `Import-Module .\AzureADSSO.psd1`

4. Execute o PowerShell como administrador. No PowerShell, `New-AzureADSSOAuthenticationContext`ligue. Este comando abre um painel onde você pode inserir as credenciais de administrador global do seu inquilino.

5. Ligue `Get-AzureADSSOStatus | ConvertFrom-Json`para. Este comando exibe uma lista de florestas do Active Directory (veja a lista "Domínios") na qual esse recurso foi ativado. Por padrão, ele é definido como falso no nível do inquilino.

   ![Exemplo da saída do Windows PowerShell](./media/how-to-connect-staged-rollout/sr3.png)

6. Ligue `$creds = Get-Credential`para. No prompt, digite as credenciais de administrador de domínio para a floresta de Diretório Ativo pretendida.

7. Chame `Enable-AzureADSSOForest -OnPremCredentials $creds`. Este comando cria a conta de computador AZUREADSSOACC a partir do controlador de domínio local para a floresta Active Directory que é necessária para *sso so .*

8. *O SSO sem costura* requer urls na zona intranet. Para implantar esses URLs usando políticas de grupo, consulte [Quickstart: Azure AD sem sinal único](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature).

9. Para um passo a passo completo, você também pode baixar nossos [planos de implantação](https://aka.ms/SeamlessSSODPDownload) para *SSO sem emendas*.

## <a name="enable-staged-rollout"></a>Habilitar a implantação em etapas

Para implementar um recurso específico *(autenticação de passagem,* *sincronização de hash de senha*ou *SSO perfeita)* para um conjunto seleto de usuários em um grupo, siga as instruções nas próximas seções.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Habilite um lançamento em etapas de um recurso específico em seu inquilino

Você pode lançar uma dessas opções:

- **Opção Uma** - *senha hash sync* + *SSO sem emenda*
- **Opção B** - *de autenticação* + de passagem*perfeita SSO*
- **SSO**de autenticação sem soa*de autenticação* + sem soo de sincronização de*sincronia* + de*sincronia de sincronia* de senha - 

Faça o seguinte:

1. Para acessar a visualização UX, faça login no [portal Azure AD](https://aka.ms/stagedrolloutux).

2. Selecione o link Ativar a implantação em estágio para o link **de entrada de usuário gerenciado (Preview).**

   Por exemplo, se você quiser ativar a *Opção A,* deslize os controles **de sincronização** de hash e **sem emenda** para **on,** conforme mostrado nas imagens a seguir.

   ![A página Azure AD Connect](./media/how-to-connect-staged-rollout/sr4.png)

   ![A página "Ativar recursos de distribuição em estágio (Preview)"](./media/how-to-connect-staged-rollout/sr5.png)

3. Adicione os grupos ao recurso para habilitar a *autenticação de passagem* e *sso sem emendas*. Para evitar um intervalo de UX, certifique-se de que os grupos de segurança não contenham mais de 200 membros inicialmente.

   ![A página "Gerenciar grupos para password Hash Sync (Visualização)"](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >Os membros de um grupo são automaticamente habilitados para a implantação em etapas. Grupos aninhados e dinâmicos não são suportados para a implantação em etapas.

## <a name="auditing"></a>Auditoria

Habilitamos eventos de auditoria para as várias ações que executamos para implantação em etapas:

- Evento de auditoria quando você habilita uma implantação em etapas para sincronização de *hash de senha,* *autenticação de passagem*ou *SSO sem emenda*.

  >[!NOTE]
  >Um evento de auditoria é registrado quando *o SSO perfeito* é ligado usando a distribuição em etapas.

  ![O painel "Criar política de distribuição para recurso" - Guia de atividades](./media/how-to-connect-staged-rollout/sr7.png)

  ![O painel "Criar política de distribuição para recurso" - Guia Propriedades Modificadas](./media/how-to-connect-staged-rollout/sr8.png)

- Evento de auditoria quando um grupo é adicionado à *sincronização de hash por senha,* *autenticação de passagem*ou *SSO sem emendas*.

  >[!NOTE]
  >Um evento de auditoria é registrado quando um grupo é adicionado à *sincronização de hash de senha* para implantação em etapas.

  ![O painel "Adicionar um grupo para recurso de lançamento" - Guia de atividades](./media/how-to-connect-staged-rollout/sr9.png)

  ![O painel "Adicionar um grupo para recurso de implantação" - Guia Propriedades Modificadas](./media/how-to-connect-staged-rollout/sr10.png)

- Evento de auditoria quando um usuário que foi adicionado ao grupo está habilitado para a implantação em etapas.

  ![O painel "Adicionar usuário ao recurso de implantação" - Guia de atividade](media/how-to-connect-staged-rollout/sr11.png)

  ![O painel "Adicionar usuário ao recurso de implantação" - Target(s)](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Validação

Para testar o login com *sincronização* de hash de senha ou *autenticação de passagem* (nome de usuário e login de senha), faça o seguinte:

1. Na extranet, vá para a [página Aplicativos](https://myapps.microsoft.com) em uma sessão privada do navegador e, em seguida, digite o UserPrincipalName (UPN) da conta de usuário selecionada para a implantação em etapas.

   Os usuários que foram direcionados para a implantação em etapas não são redirecionados para sua página de login federada. Em vez disso, eles são solicitados a entrar na página de login da marca Azure AD.

1. Certifique-se de que o login seja exibido com sucesso no [relatório de atividade de login do Azure AD](../reports-monitoring/concept-sign-ins.md) filtrando com o UserPrincipalName.

Para testar o login com *SSO perfeito:*

1. Na intranet, vá para a [página Aplicativos](https://myapps.microsoft.com) em uma sessão privada do navegador e, em seguida, digite o UserPrincipalName (UPN) da conta de usuário selecionada para a implantação em etapas.

   Os usuários que foram direcionados para a implantação encenada de *SSO sem emendas* são apresentados com um "Tentando inscrevê-lo..." mensagem antes que eles estão silenciosamente assinados dentro

1. Certifique-se de que o login seja exibido com sucesso no [relatório de atividade de login do Azure AD](../reports-monitoring/concept-sign-ins.md) filtrando com o UserPrincipalName.

   Para rastrear os logins de usuários que ainda ocorrem no Active Directory Federation Services (AD FS) para usuários selecionados de rollout em etapas, siga as instruções na solução de [problemas do AD FS: Eventos e registro](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Verifique a documentação do fornecedor sobre como verificar isso em provedores de federação de terceiros.

## <a name="remove-a-user-from-staged-rollout"></a>Remover um usuário do lançamento encenado

A remoção de um usuário do grupo desativa a implantação encenada para esse usuário. Para desativar o recurso de rollout encenado, deslize o controle de volta para **Desligado**.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: Posso usar esse recurso na produção?**

R: Sim, você pode usar este recurso em seu inquilino de produção, mas recomendamos que você primeiro experimentá-lo em seu inquilino de teste.

**P: Esse recurso pode ser usado para manter uma "coexistência" permanente, onde alguns usuários usam autenticação federada e outros usam autenticação em nuvem?**

R: Não, este recurso foi projetado para migrar de autenticação federada para nuvem em estágios e, em seguida, para eventualmente cortar para autenticação em nuvem. Não recomendamos o uso de um estado misto permanente, pois essa abordagem pode levar a fluxos de autenticação inesperados.

**P: Posso usar o PowerShell para realizar o lançamento encenado?**

R: Sim. Para saber como usar o PowerShell para realizar o lançamento em etapas, consulte [Azure AD Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="next-steps"></a>Próximas etapas
- [Pré-visualização do Azure AD 2.0](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
