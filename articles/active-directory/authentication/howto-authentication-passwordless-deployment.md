---
title: Planeje uma implantação de autenticação sem senha com o Azure AD
description: Saiba como planejar e implantar uma implementação de autenticação sem senha do Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: baselden
author: iainfoulds
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 145439ebedd2ddf7c081339146010c66f37fe1af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136527"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Planeje uma implantação de autenticação sem senha no Azure Active Directory

> [!NOTE]
> Para criar uma versão offline deste plano de implantação, use a funcionalidade Print to PDF do seu navegador.

A maioria dos ataques cibernéticos começa com um nome de usuário e senha comprometidos. As organizações tentam combater a ameaça exigindo que os usuários usem uma das seguintes abordagens:

- Senhas longas
- Senhas complexas
- Alterações frequentes de senha
- Autenticação Multifator (MFA)

A pesquisa da Microsoft [mostra](https://aka.ms/passwordguidance) que esses esforços irritam os usuários e elevam os custos de suporte. Para obter mais informações, consulte [Seu Pa$$word não importa](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

### <a name="benefits-of-passwordless-authentication"></a>Benefícios da autenticação sem senha

- **Maior segurança**. Reduza o risco de ataques de phishing e spray de senha removendo senhas como uma superfície de ataque.
-  **Melhor experiência do usuário.** Dê aos usuários uma maneira conveniente de acessar dados de qualquer lugar. Fornecer fácil acesso a aplicativos e serviços como Outlook, OneDrive ou Office enquanto estiver móvel.
-  **Insights robustos.** Obtenha insights sobre a atividade sem senha dos usuários com registro e auditoria robustos.

Com sem senha, a senha é substituída por algo que você tem mais algo que você é ou algo que você sabe. Por exemplo, o Windows Hello for Business pode usar um gesto biométrico como um rosto ou impressão digital, ou um PIN específico do dispositivo que não é transmitido por uma rede.

## <a name="passwordless-authentication-methods"></a>Métodos de autenticação sem senha
A Microsoft oferece três opções de autenticação sem senha que cobrem muitos cenários. Esses métodos podem ser usados em conjunto:

- [O Windows Hello for Business](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) é o melhor para os usuários em seus computadores Windows dedicados.
- O login da chave de segurança com [as chaves de segurança FIDO2](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) é especialmente útil para usuários que fazem login em máquinas compartilhadas como quiosques, em situações onde o uso de telefones é restrito e para identidades altamente privilegiadas.
- O login telefônico com o [aplicativo Microsoft Authenticator](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) é útil para fornecer uma opção sem senha aos usuários com dispositivos móveis. O aplicativo Authenticator transforma qualquer telefone iOS ou Android em uma credencial forte e sem senha, permitindo que os usuários entrem em qualquer plataforma ou navegador. Os usuários fazem login recebendo uma notificação em seu telefone, correspondendo um número exibido na tela ao do telefone e, em seguida, usando seus dados biométricos ou PIN para confirmar.

### <a name="passwordless-authentication-scenarios"></a>Cenários de autenticação sem senha

Os métodos de autenticação sem senha da Microsoft permitem diferentes cenários. Considere suas necessidades organizacionais, pré-requisitos e os recursos de cada método de autenticação para selecionar sua estratégia de autenticação sem senha. Recomendamos que todas as organizações que usam dispositivos Windows 10 usem o Windows Hello for Business. Em seguida, adicione o login do telefone (com o aplicativo Microsoft Authenticator) ou as chaves de segurança para cenários adicionais.

| Cenário | Autenticação telefônica | Chaves de segurança | Windows Hello for Business |
| --- | --- | --- | --- |
| **Login do computador:** <br> Do dispositivo com Windows 10 atribuído | **Não** | **Sim** <br> Com biometria, PIN | **Sim**<br>com reconhecimento biométrico e ou PIN |
| **Login do computador:** <br> Do dispositivo compartilhado windows 10 | **Não** | **Sim** <br> Com biometria, PIN  | **Não** |
| **Login do aplicativo web:** <br>a partir de um computador dedicado ao usuário | **Sim** | **Sim** <br> Desde que o login único nos aplicativos seja ativado pelo login do computador | **Sim**<br> Desde que o login único nos aplicativos seja ativado pelo login do computador |
| **Login do aplicativo web:** <br> de um dispositivo móvel ou não-windows | **Sim** | **Não** | **Não** |
| **Login do computador:** <br> Computador não Windows | **Não** | **Não** | **Não** |

Para obter informações sobre a seleção do melhor método para sua organização, consulte [Decidir um método sem senha](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless#deciding-a-passwordless-method).

## <a name="prerequisites"></a>Pré-requisitos

As organizações devem atender aos seguintes pré-requisitos antes de iniciar uma implantação sem senha:

| Pré-requisito | Aplicativo autenticador | Chaves de segurança FIDO2 |
| --- | --- | --- |
| [O registro combinado para autenticação multifatorial do Azure e redefinição de senha de autoatendimento (SSPR)](howto-registration-mfa-sspr-combined.md) está ativado (recurso de visualização) | √ | √ |
| [Os usuários podem executar a autenticação multifatorial do Azure](howto-mfa-getstarted.md) | √ | √ |
| [Os usuários se registraram para autenticação multifatorial do Azure e SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Os usuários registraram seus dispositivos móveis no Azure Active Directory](../devices/overview.md) | √ |   |
| Windows 10 versão 1809 ou superior usando um navegador suportado como Microsoft Edge ou Mozilla Firefox <br> (versão 67 ou superior). <br> *A Microsoft recomenda a versão 1903 ou superior para suporte nativo.* |   | √ |
| Chaves de segurança FIDO2 compatíveis. Certifique-se de que você está usando um dispositivo de segurança FIDO2 [testado pela Microsoft e verificado,](howto-authentication-passwordless-enable.md) ou outro dispositivo de segurança FIDO2 compatível. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Pré-requisitos para Windows Hello for Business

Os pré-requisitos para o Windows Hello dependem muito se você está implantando em uma configuração no local, híbrida ou somente em nuvem. Para obter mais informações, consulte a [lista completa de pré-requisitos para Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-multi-factor-authentication"></a>Autenticação Multifator do Azure

Os usuários registram seu método sem senha como parte do fluxo de registro de autenticação multifatorial do Azure. A autenticação multifatorial com um nome de usuário e senha, juntamente com outro método registrado, pode ser usada como um recuo no caso de não poderem usar o telefone ou a chave de segurança em alguns cenários.

### <a name="licensing"></a>Licenciamento 
Não há custo adicional para autenticação sem senha, embora alguns pré-requisitos possam exigir uma assinatura premium. Para obter informações detalhadas sobre recursos e licenciamento na [página de licenciamento do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). 

## <a name="develop-a-plan"></a>Desenvolva um plano

Considere as necessidades do seu negócio e os casos de uso para cada método de autenticação. Em seguida, selecione o método que melhor se adequa às suas necessidades.

### <a name="use-cases"></a>Casos de uso

A tabela a seguir descreve os casos de uso a serem implementados durante este projeto.

| Área | Descrição |
| --- | --- |
| **Acesso** | O login sem senha está disponível em um dispositivo corporativo ou pessoal dentro ou fora da rede corporativa. |
| **Auditoria** | Os dados de uso estão disponíveis para os administradores auditar em tempo quase real. <br> Os dados de uso são baixados em sistemas corporativos pelo menos a cada 29 dias, ou a ferramenta SIEM é usada. |
| **Governança** | O ciclo de vida das atribuições do usuário para o método de autenticação apropriado e os grupos associados é definido e monitorado. |
| **Segurança** | O acesso ao método de autenticação apropriado é controlado através de atribuições de usuário e grupo. <br> Apenas usuários autorizados podem usar o login sem senha. |
| **Desempenho** | Os cronogramas de propagação de atribuição de acesso são documentados e monitorados. <br> O sinal é medido para facilitar o uso. |
| **Experiência do Usuário** | Os usuários estão cientes da compatibilidade móvel. <br> Os usuários podem configurar o login sem senha do aplicativo Authenticator. |
| **Suporte** | Os usuários estão cientes de como encontrar suporte para problemas de login sem senha. |

### <a name="engage-the-right-stakeholders"></a>Envolva as partes interessadas certas

Quando os projetos de tecnologia falham, é normalmente devido a expectativas incompatíveis sobre impacto, resultados e responsabilidades. Para evitar essas armadilhas, [certifique-se de que você está engajando os stakeholders certos](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) e que os papéis das partes interessadas no projeto são bem compreendidos.

### <a name="plan-communications"></a>Planejar comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunique proativamente como a experiência dos usuários mudará, quando ela mudará e como ganhar suporte se eles tiverem problemas.

Suas comunicações com usuários finais devem incluir as seguintes informações:

- [Habilitando a experiência combinada de registro de segurança](howto-authentication-passwordless-phone.md)
- [Baixando o aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md)
- [Registrando-se no aplicativo Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Fazendo login com seu telefone](../user-help/user-help-auth-app-sign-in.md)

A Microsoft fornece [modelos](https://aka.ms/mfatemplates)de comunicação de autenticação multifatorial, [modelos de comunicação](https://www.microsoft.com/download/details.aspx?id=56768)de redefinição de senha de autoatendimento (SSPR) e [documentação do usuário final](../user-help/security-info-setup-signin.md) para ajudar a elaborar suas comunicações. Você pode enviar [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) os usuários para se registrar diretamente selecionando os links **de Informações de Segurança** nessa página.

### <a name="plan-to-pilot"></a>Planeje pilotar

Quando você implanta autenticação sem senha, você deve primeiro ativar um ou mais grupos piloto. Você pode [criar grupos](../fundamentals/active-directory-groups-create-azure-portal.md) especificamente para este fim. Adicione os usuários que participarão do piloto aos grupos. Em seguida, habilite novos métodos de autenticação sem senha para os grupos selecionados.

Os grupos podem ser sincronizados a partir de um diretório local ou do Azure AD. Uma vez que você esteja feliz com os resultados do seu piloto, você pode ligar a autenticação sem senha para todos os usuários.

Consulte [as melhores práticas para um piloto](https://aka.ms/deploymentplans) na página de planos de implantação.

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>Planeje autenticação sem senha com o aplicativo Microsoft Authenticator

O aplicativo Microsoft Authenticator é um download gratuito do Google Play ou da Apple App Store. [Saiba mais sobre como baixar o aplicativo Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Faça com que os usuários baixem o aplicativo Microsoft Authenticator. e siga as instruções para ativar o login do telefone. 

Ele transforma qualquer telefone iOS ou Android em uma credencial forte e sem senha. Os usuários fazem login em qualquer plataforma ou navegador recebendo uma notificação em seu telefone, correspondendo um número exibido na tela ao do telefone e, em seguida, usando a biometria ou um PIN para confirmar. [Veja detalhes sobre como funciona o aplicativo Microsoft Authenticator](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless#user-using-microsoft-authenticator-for-passwordless-sign-in). 

![faça login com o aplicativo Authenticator](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Considerações técnicas para o aplicativo Microsoft Authenticator

**Integração AD FS** - Quando um usuário habilita a credencial sem senha do Microsoft Authenticator, a autenticação para esse usuário é padrão ao enviar uma notificação para aprovação. Os usuários de um inquilino híbrido são impedidos de serem direcionados ao ADFS para fazer login, a menos que selecionem "Use sua senha em vez disso". Esse processo também ignora quaisquer políticas de acesso condicional no local e fluxos de autenticação de passagem. No entanto, se um *login_hint* for especificado, o usuário é encaminhado para o ADFS e contorna a opção de usar a credencial sem senha.

**Servidor de autenticação multifatorial do Azure** - Os usuários finais habilitados para autenticação multifatorial através do servidor MFA azure no local da organização podem criar e usar uma única credencial de login de telefone sem senha. Se o usuário tentar atualizar várias instalações (5 ou mais) do Autenticador Microsoft com a credencial, essa alteração pode resultar em um erro.

**Registro do dispositivo** - Para usar o aplicativo Authenticator para autenticação sem senha, o dispositivo deve ser registrado no inquilino Azure AD e não pode ser um dispositivo compartilhado. Um dispositivo só pode ser registrado em um único inquilino. Esse limite significa que apenas uma conta de trabalho ou escola é suportada para login telefônico usando o aplicativo Authenticator.

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>Planeje autenticação sem senha com chaves de segurança FIDO2
Existem três tipos de implantações de login sem senha disponíveis com chaves de segurança:

-    Aplicativos web do Azure Active Directory em um navegador suportado
-    Azure Active Directory juntou-se aos dispositivos Windows 10
-    Diretório ativo do Azure híbrido juntou-se aos dispositivos Windows 10 (visualização)
     -    Fornece acesso tanto aos recursos baseados em nuvem quanto aos locais. Para obter mais informações sobre o acesso aos recursos locais, consulte [o SSO para obter recursos no local usando chaves FIDOP2](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)

Você deve habilitar **chaves de segurança FIDO2 compatíveis**. A Microsoft anunciou [parcerias importantes com os principais fornecedores fido2](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493).

**Para aplicativos web Azure AD e Azure AD Windows aderiram aos dispositivos**:

-    Windows 10 versão 1809 ou superior usando um navegador suportado como Microsoft Edge ou Mozilla Firefox (versão 67 ou superior). 
-    A versão 1809 do Windows 10 suporta o login FIDO2 e pode exigir que o software do fabricante de chaves FIDO2 seja implantado. Recomendamos que você use a versão 1903 ou posterior. 

**Para dispositivos de domínio de diretório ativo do Azure híbridos:** 
-    Windows 10 Insider build 18945 ou posterior
-    Servidores de domínio totalmente corrigidos executando o Windows Server 2016 ou 2019.
-    Última versão do Azure AD Connect

Para obter uma lista completa de requisitos, consulte [Habilitar o login da chave de segurança sem senha para dispositivos Windows 10 com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-windows#requirements).


### <a name="security-key-life-cycle"></a>Ciclo de vida chave da segurança

As chaves de segurança permitem o acesso aos seus recursos, e você deve planejar o gerenciamento desses dispositivos físicos.

1. **Distribuição de**chaves : Planeje como prover chaves para sua organização. Você pode ter um processo centralizado de provisionamento ou permitir que os usuários finais comprem chaves compatíveis com FIDO 2.0.
1. **Ativação da chave**: Os usuários finais devem ativar a chave de segurança. Os usuários finais registram suas chaves de [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) segurança e habilitam o segundo fator (PIN ou biométrico) no primeiro uso.
1. **Desabilitando uma chave**: Embora a funcionalidade da chave de segurança esteja no estágio de visualização, não há como um administrador remover uma chave de uma conta de usuário. O usuário deve removê-lo. Se uma chave for perdida ou roubada:
   1. Remova o usuário de qualquer grupo habilitado para autenticação sem senha.
   1. Verifique se eles removeram a chave como um método de autenticação.
   1. Emita uma nova chave. **Substituição da chave**: Os usuários podem habilitar duas chaves de segurança ao mesmo tempo. Ao substituir uma chave de segurança, certifique-se de que o usuário também removeu a chave que está sendo substituída.

### <a name="enable-windows-10-support"></a>Habilite o suporte ao Windows 10

Habilitar o login do Windows 10 usando chaves de segurança FIDO2 requer a habilitação da funcionalidade do provedor de credenciais no Windows 10. Escolha uma destas opções:

- [Habilitar provedor de credencial com Intune](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - A implantação do Intune é a opção recomendada.
- [Habilitar provedor de credencial com um pacote de provisionamento](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Se a implantação do Intune não for possível, os administradores devem implantar um pacote em cada máquina para habilitar a funcionalidade do provedor de credenciais. A instalação do pacote pode ser realizada por uma das seguintes opções:
      - Gerente de diretiva ou configuração de grupo
      - Instalação local em uma máquina Windows 10
- [Habilitar provedor de credencial com política de grupo](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - Apenas suportado para dispositivos híbridos Azure AD juntou-se.

#### <a name="enable-on-premises-integration"></a>Habilite a integração no local

Para habilitar o acesso aos recursos locais, siga as etapas para habilitar o [login de chave de segurança sem senha aos recursos locais (visualização)](howto-authentication-passwordless-security-key-on-premises.md).

> [!IMPORTANT]
> Essas etapas também devem ser concluídas para qualquer dispositivo híbrido Azure AD para utilizar as chaves de segurança FIDO2 para o login do Windows 10.

### <a name="register-security-keys"></a>Registre as chaves de segurança

Os usuários devem registrar sua chave de segurança em cada um de seus Azure Active Directory junto às máquinas windows 10.

Para obter mais informações, consulte [Registro e gerenciamento de chaves de segurança FIDO2](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys).


## <a name="plan-auditing-security-and-testing"></a>Planejar auditoria, segurança e testes
O planejamento de auditoria que atenda às suas estruturas organizacionais e de conformidade é uma parte essencial da sua implantação.

### <a name="auditing-passwordless"></a>Auditoria sem senha

O Azure AD tem relatórios que fornecem insights técnicos e de negócios. Que seus proprietários de aplicativos comerciais e técnicos assumam a propriedade e consumam esses relatórios com base nas necessidades da sua organização.

A seção **métodos de autenticação** dentro do portal Azure Active Directory é onde os administradores podem habilitar e gerenciar configurações para credenciais sem senha.

O Azure AD adiciona entradas aos logs de auditoria quando:

- Um admin faz alterações na seção Métodos de Autenticação.
- Um usuário faz qualquer tipo de alteração em suas credenciais dentro do Azure Active Directory.

A tabela a seguir fornece alguns exemplos de cenários típicos de relatórios:

|   | gerenciar riscos | Aumentar a produtividade | Governança e conformidade |
| --- | --- | --- | --- |
| **Tipos de relatório** | Métodos de autenticação- usuários cadastrados para registro de segurança combinado | Métodos de autenticação – usuários cadastrados para notificação de aplicativos | Logins: veja quem está acessando o inquilino e como |
| **Ações potenciais** | Usuários-alvo ainda não registrados | Impulsionar a adoção do aplicativo Microsoft Authenticator ou chaves de segurança | Revogar o acesso ou impor políticas adicionais de segurança para os admins |

**O Azure AD mantém** a maioria dos dados de auditoria por 30 dias e disponibiliza os dados através do portal Azure Admin ou API para você baixar em seus sistemas de análise. Se você precisar de retenção mais longa, exporte e consuma logs em uma ferramenta SIEM, como [Azure Sentinel,](../../sentinel/connect-azure-active-directory.md)Splunk ou Sumo Logic. [Saiba mais sobre como visualizar seus relatórios de acesso e uso.](../reports-monitoring/overview-reports.md)

Os usuários podem registrar e gerenciar [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)suas credenciais navegando para . Este link direciona os usuários para a experiência de gerenciamento de credenciais do usuário final que foi habilitada através da experiência combinada de registro de autenticação SSPR/Multifatorial. O Azure AD registra o registro de dispositivos de segurança FIDO2 e altera os métodos de autenticação por um usuário.

### <a name="plan-security"></a>Planejar segurança
Como parte deste plano de implantação, a Microsoft recomenda que a autenticação sem senha seja habilitada para todas as contas de admin privilegiadas.

Quando os usuários ativam ou desativam a conta em uma chave de segurança ou redefinem o segundo fator para a chave de segurança em suas máquinas Windows 10, uma entrada é adicionada ao registro de segurança e estão os seguintes IDs de evento: *4670* e *5382*.

### <a name="plan-testing"></a>Teste de plano

Em cada etapa de sua implantação, enquanto você testa cenários e adoção, certifique-se de que os resultados são como esperado.

#### <a name="testing-the-microsoft-authenticator-app"></a>Testando o aplicativo Microsoft Authenticator

A seguir estão casos de teste de exemplo para autenticação sem senha com o aplicativo Microsoft Authenticator:

| Cenário | Resultados esperados |
| --- | --- |
| O usuário pode registrar o aplicativo Microsoft Authenticator | O usuário pode cadastrar o aplicativo a partir de aka.ms/mysecurityinfo |
| O usuário pode ativar o login do telefone | Login telefônico configurado para conta de trabalho |
| O usuário pode acessar um aplicativo com login de telefone | O usuário passa pelo fluxo de login do telefone e chega ao aplicativo. |
| Teste reverter o registro de login do telefone desativando o login sem senha do Microsoft Authenticator na tela de métodos de autenticação no portal do Azure Active Directory | Anteriormente, os usuários não podiam usar o login sem senha do Microsoft Authenticator. |
| Removendo o login do telefone do aplicativo Microsoft Authenticator | Conta de trabalho não está mais disponível no Microsoft Authenticator |

#### <a name="testing-security-keys"></a>Testando chaves de segurança

A seguir estão casos de teste de exemplo para autenticação sem senha com chaves de segurança.

**Login FIDO sem senha para o Azure Active Directory Juntou-se aos dispositivos Windows 10**

| Cenário | Resultados esperados |
| --- | --- |
| O usuário pode registrar o dispositivo FIDO2 (1809) | O usuário pode registrar o dispositivo FIDO2 usando em Configurações > Contas > fazer login em opções > Chave de Segurança |
| O usuário pode redefinir o dispositivo FIDO2 (1809) | O usuário pode redefinir o dispositivo FIDO2 usando o software do fabricante |
| O usuário pode fazer login com o dispositivo FIDO2 (1809) | O usuário pode selecionar a chave de segurança na janela de login e fazer login com sucesso. |
| O usuário pode registrar o dispositivo FIDO2 (1903) | O usuário pode registrar o dispositivo FIDO2 em Configurações > Contas > fazer login > chave de segurança |
| O usuário pode redefinir o dispositivo FIDO2 (1903) | O usuário pode redefinir o dispositivo FIDO2 em Configurações > Contas > fazer login em opções > Chave de Segurança |
| O usuário pode fazer login com o dispositivo FIDO2 (1903) | O usuário pode selecionar a chave de segurança na janela de login e fazer login com sucesso. |

**Login FIDO sem senha para aplicativos web Azure AD**

| Cenário | Resultados esperados |
| --- | --- |
| O usuário pode registrar o dispositivo FIDO2 em aka.ms/mysecurityinfo usando o Microsoft Edge | As inscrições devem ter sucesso |
| O usuário pode registrar o dispositivo FIDO2 em aka.ms/mysecurityinfo usando o Firefox | As inscrições devem ter sucesso |
| O usuário pode fazer login no OneDrive on-line usando o dispositivo FIDO2 usando o Microsoft Edge | O login deve ter sucesso |
| O usuário pode fazer login no OneDrive on-line usando o dispositivo FIDO2 usando o Firefox | O login deve ter sucesso |
| Teste reverter o registro do dispositivo FIDO2 desligando as chaves de segurança FIDO2 na janela do método de autenticação no portal do Diretório Ativo do Azure | Os usuários serão solicitados a fazer login usando sua chave de segurança. Os usuários farão login com sucesso e um erro será exibido: "A política da sua empresa exige que você use um método diferente para fazer login". Os usuários devem então ser capazes de selecionar um método diferente e fazer login com sucesso. Feche a janela e faça login novamente para verificar se eles não vêem a mesma mensagem de erro. |

### <a name="plan-for-rollback"></a>Planejar a reversão

Embora a autenticação sem senha seja um recurso leve com impacto mínimo nos usuários finais, pode ser necessário reverter.

Reverter o retorno requer que o administrador faça login no portal do Diretório Ativo do Azure, selecione os métodos de autenticação fortes desejados e altere a opção ativar para **No**. Esse processo desativa a funcionalidade sem senha para todos os usuários.

Os usuários que já registraram dispositivos de segurança FIDO2 são solicitados a usar o dispositivo de segurança no próximo login e, em seguida, ver o seguinte erro:

![escolher uma maneira diferente de assinar](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>Implantar e solucionar problemas de autenticação sem senha

Siga as etapas alinhadas ao método escolhido abaixo.

### <a name="required-administrative-roles"></a>Funções administrativas obrigatórias

| Papel Azure AD | Descrição |
| --- | --- |
| Administrador global|Função menos privilegiada capaz de implementar experiência de registro combinada. |
| Administrador de Autenticação | Função menos privilegiada capaz de implementar e gerenciar métodos de autenticação. |
| Usuário | Função menos privilegiada para configurar o aplicativo Authenticator no dispositivo ou para inscrever dispositivo-chave de segurança para login web ou Windows 10. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Implantar login de telefone com o aplicativo Microsoft Authenticator

Siga as etapas do artigo, [Habilite o login sem senha com o aplicativo Microsoft Authenticator](howto-authentication-passwordless-phone.md) para habilitar o aplicativo Microsoft Authenticator como um método de autenticação sem senha em sua organização.

### <a name="deploy-fido2-security-key-sign-in"></a>Implantar o login da chave de segurança FIDO2

Siga as etapas do artigo, [Habilite o login de chave de segurança sem senha para o Azure AD](howto-authentication-passwordless-security-key.md) para habilitar as chaves de segurança FIDO2 como métodos de autenticação sem senha.

### <a name="troubleshoot-phone-sign-in"></a>Solucionar problemas no login do telefone

| Cenário | Solução |
| --- | --- |
| O usuário não pode realizar o registro combinado. | Certifique-se [de que o registro combinado](concept-registration-mfa-sspr-combined.md) está habilitado. |
| O usuário não pode ativar o aplicativo autenticador de login de telefone. | Certifique-se de que o usuário está no escopo de implantação. |
| O usuário NÃO está no escopo para autenticação sem senha, mas é apresentado com opção de login sem senha, que não pode concluir. | Esse cenário ocorre quando o usuário habilita o login telefônico no aplicativo antes da política ser criada. <br> *Para ativar o login*: Adicione o usuário ao escopo dos usuários habilitados para login sem senha. <br> *Para bloquear o login*: faça com que o usuário remova sua credencial desse aplicativo. |

### <a name="troubleshoot-security-key-sign-in"></a>Solucionar problemas na entrada da chave de segurança

| Cenário | Solução |
| --- | --- |
| O usuário não pode realizar o registro combinado. | Certifique-se [de que o registro combinado](concept-registration-mfa-sspr-combined.md) está habilitado. |
| O usuário não pode adicionar uma chave de segurança em suas [configurações de segurança](https://aka.ms/mysecurityinfo). | Certifique-se de que [as chaves de segurança](howto-authentication-passwordless-security-key.md) estão habilitadas. |
| O usuário não pode adicionar a chave de segurança nas opções de login do Windows 10. | [Certifique-se de que as chaves de segurança para o Windows entrem](howto-authentication-passwordless-enable.md) |
| **Mensagem de erro**: Detectamos que este navegador ou sistema operacional não suporta chaves de segurança FIDO2. | Os dispositivos de segurança FIDO2 sem senha só podem ser registrados em navegadores suportados (Microsoft Edge, Firefox versão 67) no Windows 10 versão 1809 ou superior. |
| **Mensagem de erro**: A política da empresa exige que você use um método diferente para fazer login. | As chaves de segurança inseguras estão habilitadas no inquilino. |
| Usuário incapaz de gerenciar minha chave de segurança no Windows 10 versão 1809 | A versão 1809 exige que você use o software de gerenciamento de chaves de segurança fornecido pelo fornecedor-chave FIDO2. Entre em contato com o fornecedor para obter suporte. |
| Acho que minha chave de segurança FIDO2 pode estar com defeito — como posso testá-la. | Navegue [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/)para , digite credenciais para uma conta **+** de teste, conecte a chave de segurança suspeita, selecione o botão no canto superior direito da tela, clique em criar e passe pelo processo de criação. Se este cenário falhar, seu dispositivo pode estar com defeito. |

## <a name="next-steps"></a>Próximas etapas

- [Habilite chaves de segurança sem senha para fazer login no Azure AD](howto-authentication-passwordless-security-key.md)
- [Habilite o login sem senha com o aplicativo Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Saiba mais sobre métodos de autenticação uso & insights](howto-authentication-methods-usage-insights.md)

