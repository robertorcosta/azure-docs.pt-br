---
title: Simulando detecções de risco no Azure AD Identity Protection
description: Saiba como simular detecções de risco na Proteção de Identidade
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e50e406e74c0b78f41830c4ea7c5b10830002ed3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72886929"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Simulando detecções de risco na Proteção de Identidade

Os administradores podem querer simular o risco em seu ambiente para realizar os seguintes itens:

- Preencha dados no ambiente de Proteção de Identidade simulando detecções e vulnerabilidades de risco.
- Configure políticas de acesso condicional baseadas em riscos e teste o impacto dessas políticas.

Este artigo fornece etapas para simular os seguintes tipos de detecção de risco:

- Endereço IP anônimo (fácil)
- Propriedades de login desconhecidas (moderadas)
- Viagem atípica (difícil)

Outras detecções de risco não podem ser simuladas de forma segura.

Mais informações sobre cada detecção de risco podem ser encontradas no artigo, [O que é risco](concept-identity-protection-risks.md).

## <a name="anonymous-ip-address"></a>Endereço IP anônimo

A conclusão do procedimento a seguir requer que você use:

- O [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) para simular endereços IP anônimos. Talvez você precise usar uma máquina virtual, caso sua organização restrinja o uso do navegador Tor.
- Uma conta de teste que ainda não está registrada para autenticação multifatorial do Azure.

**Para simular uma entrada de um IP anônimo, realize as seguintes etapas**:

1. Usando o [Navegador Tor](https://www.torproject.org/projects/torbrowser.html.en), navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com).   
2. Insira as credenciais da conta que deseja exibir no relatório **Entradas de endereços IP anônimos** .

A entrada aparece no painel de segurança Identity Protection dentro de 10 a 15 minutos. 

## <a name="unfamiliar-sign-in-properties"></a>Propriedades de entrada desconhecidas

Para simular locais desconhecidos, você deve entrar a partir de um local e do dispositivo em que a sua conta de teste não tenha entrado anteriormente.

O procedimento abaixo usa um criado recentemente:

- Conexão VPN, para simular novo local.
- Máquina virtual, para simular um novo dispositivo.

A conclusão do procedimento a seguir requer que você use uma conta de usuário que tenha:

- Pelo menos um histórico entrada de 30 dias.
- Autenticação multifatorial do Azure ativada.

**Para simular uma entrada de um local desconhecido, realize as seguintes etapas**:

1. Ao fazer login com sua conta de teste, falhe no desafio de autenticação multifatorial (MFA) ao não passar no desafio MFA.
2. Usando sua nova VPN, navegue [https://myapps.microsoft.com](https://myapps.microsoft.com) e insira as credenciais de sua conta de teste.

A entrada aparece no painel de segurança Identity Protection dentro de 10 a 15 minutos.

## <a name="atypical-travel"></a>Viagem atípica

Simular a condição de viagem atípica é difícil porque o algoritmo usa aprendizado de máquina para eliminar falsos positivos, como viagens atípicas de dispositivos familiares ou logins de VPNs que são usados por outros usuários no diretório. Além disso, o algoritmo requer um histórico de login de 14 dias e 10 logins do usuário antes de começar a gerar detecções de risco. Devido aos modelos complexos de aprendizagem de máquina e às regras acima, há uma chance de que as etapas a seguir não levem a uma detecção de risco. Você pode querer replicar essas etapas para várias contas Azure AD para simular essa detecção.

**Para simular uma detecção atípica de risco de viagem, execute as seguintes etapas:**

1. Usando seu navegador padrão, navegue para [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Digite as credenciais da conta para a sua detecção de risco de viagem.
3. Altere o agente do usuário. Você pode alterar o agente de usuário no Microsoft Edge a partir de Ferramentas de Desenvolvedor (F12).
4. Altere seu endereço IP. Você pode alterar seu endereço IP usando uma VPN, um complemento do Tor ou criando uma nova máquina virtual no Azure em um data center diferente.
5. Faça login [https://myapps.microsoft.com](https://myapps.microsoft.com) para usar as mesmas credenciais de antes e dentro de poucos minutos após o login anterior.

A entrada aparece no painel de controle Identity Protection dentro de 2 a 4 horas.

## <a name="testing-risk-policies"></a>Testando políticas de risco

Esta seção fornece etapas para testar o usuário e as políticas de risco de login criadas no artigo, [Como: Configurar e ativar políticas de risco](howto-identity-protection-configure-risk-policies.md).

### <a name="user-risk-policy"></a>Política de risco do usuário

Para testar uma política de segurança de risco de usuário, execute as seguintes etapas:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
1. Navegue até a visão geral da > **segurança** > do diretório**ativo do** **Azure**.
1. Selecione **Configurar a política de risco do usuário**.
   1. Em **Atribuições**
      1. **Usuários** - Escolha **todos os usuários** ou **Selecione indivíduos e grupos** se limitar sua distribuição.
         1. Opcionalmente, você pode optar por excluir os usuários da apólice.
      1. **Condições** - **Risco do usuário** A recomendação da Microsoft é definir essa opção como **Alta**.
   1. **Controles**
      1. **Acesso** - A recomendação da Microsoft é **permitir o acesso** e exigir alteração de **senha.**
   1. **Impor política** - **off**
   1. **Salvar** - Esta ação o devolverá à página **Visão Geral.**
1. Eleve o risco de usuário de uma conta de teste simulando uma das detecções de risco algumas vezes.
1. Aguarde alguns minutos e verifique se o risco aumentou para o usuário. Caso assim, simule mais detecções de risco para o usuário.
1. Retorne à sua política de risco e defina **a política de execução** para **on** e **salve** sua mudança de política.
1. Agora você pode testar o Acesso Condicional baseado em risco do usuário, fazendo login usando um usuário com um nível de risco elevado.

### <a name="sign-in-risk-security-policy"></a>Política de segurança de risco de entrada

Para testar uma política de segurança de entrada, execute as seguintes etapas:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
1. Navegue até a visão geral da > **segurança** > do diretório**ativo do** **Azure**.
1. Selecione **Configurar a política de risco de login**.
   1. Em **Atribuições**
      1. **Usuários** - Escolha **todos os usuários** ou **Selecione indivíduos e grupos** se limitar sua distribuição.
         1. Opcionalmente, você pode optar por excluir os usuários da apólice.
      1. **Condições** - **Risco de login** A recomendação da Microsoft é definir essa opção como Média e **superior**.
   1. **Controles**
      1. **Acesso** - A recomendação da Microsoft é **permitir o acesso** e exigir **autenticação multifatorial.**
   1. **Impor política** - **sobre**
   1. **Salvar** - Esta ação o devolverá à página **Visão Geral.**
1. Agora você pode testar o Acesso Condicional baseado em Risco de Login, fazendo login usando uma sessão arriscada (por exemplo, usando o navegador Tor). 

## <a name="next-steps"></a>Próximas etapas

- [O que é risco?](concept-identity-protection-risks.md)

- [Como: Configurar e habilitar políticas de risco](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory Identity Protection](overview-identity-protection.md)
