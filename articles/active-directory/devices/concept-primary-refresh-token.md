---
title: Token de atualização primária (PRT) e Azure AD - Azure Active Directory
description: Qual é o papel e como gerenciamos o PrT (Primary Refresh Token) no Azure Active Directory?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a237ad35d9d5d8abee784926563d972d0ee95f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672644"
---
# <a name="what-is-a-primary-refresh-token"></a>O que é um Token de atualização primária?

Um Primary Refresh Token (PRT) é um artefato-chave da autenticação Azure AD em dispositivos Windows 10, iOS e Android. É um JSON Web Token (JWT) especialmente emitido para corretores de token sinuosos da Microsoft para habilitar o soo de login único (SSO) através dos aplicativos usados nesses dispositivos. Neste artigo, forneceremos detalhes sobre como um PRT é emitido, usado e protegido em dispositivos Windows 10.

Este artigo pressupõe que você já entende os diferentes estados do dispositivo disponíveis no Azure AD e como o login único funciona no Windows 10. Para obter mais informações sobre dispositivos no Azure AD, consulte o artigo O que é o [gerenciamento de dispositivos no Azure Active Directory?](overview.md)

## <a name="key-terminology-and-components"></a>Terminologia-chave e componentes

Os seguintes componentes do Windows desempenham um papel fundamental na solicitação e utilização de um PRT:

* **Cloud Authentication Provider** (CloudAP): CloudAP é o provedor de autenticação moderno para login do Windows, que verifica o login dos usuários em um dispositivo Windows 10. O CloudAP fornece uma estrutura de plugin que os provedores de identidade podem construir para permitir a autenticação no Windows usando as credenciais desse provedor de identidade.
* **Gerenciador de contas** web (WAM): O WAM é a corretora de tokens padrão em dispositivos Windows 10. O WAM também fornece uma estrutura de plugin que os provedores de identidade podem construir e habilitar o SSO para seus aplicativos que dependem desse provedor de identidade.
* **Plugin Azure AD CloudAP**: Um plugin específico do Azure AD construído na estrutura cloudap, que verifica as credenciais do usuário com o Azure AD durante o login do Windows.
* **Plugin Azure AD WAM**: Um plugin específico do Azure AD construído na estrutura WAM, que permite que o SSO aaplicações que dependem do Azure AD para autenticação.
* **Dsreg**: Um componente específico do Azure AD no Windows 10, que lida com o processo de registro do dispositivo para todos os estados do dispositivo.
* **Trusted Platform Module** (TPM): Um TPM é um componente de hardware incorporado em um dispositivo, que fornece funções de segurança baseadas em hardware para segredos de usuários e dispositivos. Mais detalhes podem ser encontrados no artigo [Trusted Platform Module Technology Overview](/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>O que o PRT contém?

Um PRT contém reivindicações geralmente contidas em qualquer token de atualização do Azure AD. Além disso, existem algumas reivindicações específicas do dispositivo incluídas no PRT. Elas são as seguintes:

* **ID do dispositivo**: Um PRT é emitido para um usuário em um dispositivo específico. A reivindicação `deviceID` de id do dispositivo determina o dispositivo em que o PRT foi emitido ao usuário. Esta reivindicação é posteriormente emitida para tokens obtidos através do PRT. A reivindicação de id do dispositivo é usada para determinar a autorização para acesso condicional com base no estado do dispositivo ou conformidade.
* **Chave de sessão**: A chave de sessão é uma chave simétrica criptografada, gerada pelo serviço de autenticação Azure AD, emitido como parte do PRT. A chave da sessão funciona como a prova de posse quando um PRT é usado para obter tokens para outras aplicações.

### <a name="can-i-see-whats-in-a-prt"></a>Posso ver o que tem em um PRT?

Um PRT é um blob opaco enviado do Azure AD cujo conteúdo não é conhecido por nenhum componente cliente. Você não pode ver o que está dentro de um PRT.

## <a name="how-is-a-prt-issued"></a>Como é emitido um PRT?

O registro do dispositivo é um pré-requisito para autenticação baseada em dispositivos no Azure AD. Um PRT é emitido aos usuários apenas em dispositivos registrados. Para obter mais detalhes sobre o registro do dispositivo, consulte o artigo [Windows Hello for Business and Device Registration](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Durante o registro do dispositivo, o componente dsreg gera dois conjuntos de pares de chaves criptográficas:

* Chave do dispositivo (dkpub/dkpriv)
* Chave de transporte (tkpub/tkpriv)

As chaves privadas são vinculadas ao TPM do dispositivo se o dispositivo tiver um TPM válido e funcional, enquanto as chaves públicas são enviadas ao Azure AD durante o processo de registro do dispositivo. Essas chaves são usadas para validar o estado do dispositivo durante as solicitações PRT.

O PRT é emitido durante a autenticação do usuário em um dispositivo Windows 10 em dois cenários:

* **Azure AD juntou-se** ou **o Hybrid Azure AD juntou-se**: Um PRT é emitido durante o login do Windows quando um usuário faz login com suas credenciais de organização. Um PRT é emitido com todas as credenciais suportadas pelo Windows 10, por exemplo, senha e Windows Hello for Business. Neste cenário, o plugin Azure AD CloudAP é a principal autoridade para o PRT.
* **Dispositivo registrado no Azure AD**: Um PRT é emitido quando um usuário adiciona uma conta de trabalho secundária ao seu dispositivo Windows 10. Os usuários podem adicionar uma conta ao Windows 10 de duas maneiras diferentes -  
   * Adicionar uma conta através do **Use esta conta em todos os lugares neste prompt de dispositivo** após fazer login em um aplicativo (por exemplo, Outlook)
   * Adicionando uma conta de **Configurações** > **Contas** > **Acessam o Trabalho ou o Conexão Escolar** > **Connect**

Nos cenários de dispositivos registrados pelo Azure AD, o plugin Azure AD WAM é a principal autoridade para o PRT, uma vez que o logon do Windows não está acontecendo com esta conta Azure AD.

> [!NOTE]
> Os provedores de identidade de terceiros precisam oferecer suporte ao protocolo WS-Trust para permitir a emissão de PRT em dispositivos Windows 10. Sem o WS-Trust, o PRT não pode ser emitido aos usuários no Hybrid Azure AD ou no Azure AD

## <a name="what-is-the-lifetime-of-a-prt"></a>Qual é a vida de um PRT?

Uma vez emitido, um PRT é válido por 14 dias e é continuamente renovado enquanto o usuário usar ativamente o dispositivo.  

## <a name="how-is-a-prt-used"></a>Como é usado um PRT?

Um PRT é usado por dois componentes-chave no Windows:

* **Plugin Azure AD CloudAP**: Durante o login do Windows, o plugin Ad CloudAP do Azure solicita um PRT do Azure AD usando as credenciais fornecidas pelo usuário. Ele também armazena o PRT para ativar o login em cache quando o usuário não tem acesso a uma conexão à internet.
* **Plugin Azure AD WAM**: Quando os usuários tentam acessar aplicativos, o plugin AD WAM do Azure usa o PRT para ativar o SSO no Windows 10. O plugin Azure AD WAM usa o PRT para solicitar tokens de atualização e acesso para aplicativos que dependem do WAM para solicitações de tokens. Ele também permite sSO em navegadores injetando o PRT em solicitações de navegador. O Navegador SSO no Windows 10 é suportado no Microsoft Edge (nativamente) e no Chrome (através da extensão Windows 10 Accounts ou Office Online).

## <a name="how-is-a-prt-renewed"></a>Como um PRT é renovado?

Um PRT é renovado em dois métodos diferentes:

* **Plugin Azure AD CloudAP a cada 4 horas**: O plugin CloudAP renova o PRT a cada 4 horas durante o login do Windows. Se o usuário não tiver conexão com a internet durante esse tempo, o plugin CloudAP renovará o PRT depois que o dispositivo estiver conectado à internet.
* **Plugin Azure AD WAM durante solicitações de token de aplicativo**: O plugin WAM permite o SSO em dispositivos Windows 10, permitindo solicitações silenciosas de token para aplicativos. O plugin WAM pode renovar o PRT durante essas solicitações de token de duas maneiras diferentes:
   * Um aplicativo solicita ao WAM um token de acesso silenciosamente, mas não há nenhum token de atualização disponível para esse aplicativo. Neste caso, o WAM usa o PRT para solicitar um token para o aplicativo e recebe de volta um novo PRT na resposta.
   * Um aplicativo solicita ao WAM um token de acesso, mas o PRT é inválido ou o Azure AD requer autorização adicional (por exemplo, Autenticação Multifatorial do Azure). Neste cenário, o WAM inicia um logon interativo exigindo que o usuário reautenticaou ou forneça verificação adicional e um novo PRT é emitido em autenticação bem-sucedida.

### <a name="key-considerations"></a>Considerações-chave

* Um PRT só é emitido e renovado durante a autenticação nativa do aplicativo. Um PRT não é renovado ou emitido durante uma sessão de navegador.
* No Azure AD juntou-se e híbrido Azure AD juntou dispositivos, o plugin CloudAP é a autoridade principal para um PRT. Se um PRT for renovado durante uma solicitação de token baseada em WAM, o PRT será enviado de volta ao plugin CloudAP, que verifica a validade do PRT com o Azure AD antes de aceitá-lo.

## <a name="how-is-the-prt-protected"></a>Como o PRT é protegido?

Um PRT é protegido vinculando-o ao dispositivo em que o usuário fez o seu assinatura. O Azure AD e o Windows 10 permitem a proteção PRT através dos seguintes métodos:

* **Durante o primeiro login**: Durante o primeiro login, um PRT é emitido assinando solicitações usando a chave do dispositivo gerada criptograficamente durante o registro do dispositivo. Em um dispositivo com um TPM válido e funcional, a chave do dispositivo é protegida pelo TPM impedindo qualquer acesso malicioso. Um PRT não é emitido se a assinatura da chave do dispositivo correspondente não puder ser validada.
* **Durante as solicitações de token e renovação**: Quando um PRT é emitido, o Azure AD também emite uma chave de sessão criptografada para o dispositivo. Ele é criptografado com a chave de transporte público (tkpub) gerada e enviada ao Azure AD como parte do registro do dispositivo. Esta chave de sessão só pode ser descriptografada pela chave de transporte privado (tkpriv) protegida pelo TPM. A chave da sessão é a chave Proof-of-Possession (POP) para quaisquer solicitações enviadas ao Azure AD.  A chave de sessão também é protegida pelo TPM e nenhum outro componente do sistema operacional pode acessá-la. As solicitações de token ou solicitações de renovação PRT são assinadas com segurança por esta chave de sessão através do TPM e, portanto, não podem ser adulteradas. O Azure AD invalidará quaisquer solicitações do dispositivo que não sejam assinadas pela chave de sessão correspondente.

Ao proteger essas chaves com o TPM, atores mal-intencionados não podem roubar as chaves nem reproduzir o PRT em outro lugar, pois o TPM é inacessível mesmo que um invasor tenha posse física do dispositivo.  Assim, o uso de um TPM aumenta muito a segurança do Azure AD Joined, do Hybrid Azure AD e dos dispositivos registrados pelo Azure AD contra roubo de credenciais. Para desempenho e confiabilidade, o TPM 2.0 é a versão recomendada para todos os cenários de registro de dispositivos Azure AD no Windows 10.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Como os tokens de aplicativos e cookies do navegador são protegidos?

**Tokens de aplicativos**: Quando um aplicativo solicita token através do WAM, o Azure AD emite um token de atualização e um token de acesso. No entanto, o WAM só retorna o token de acesso ao aplicativo e protege o token de atualização em seu cache, criptografando-o com a chave DPAPI (Data Protection Application Programming Interface, interface de proteção de dados) do usuário. O WAM usa com segurança o token de atualização assinando solicitações com a chave de sessão para emitir mais tokens de acesso. A tecla DPAPI é protegida por uma chave simétrica baseada no Azure AD. Quando o dispositivo precisa descriptografar o perfil do usuário com a tecla DPAPI, o Azure AD fornece a chave DPAPI criptografada pela chave de sessão, que o plugin CloudAP solicita ao TPM para descriptografar. Essa funcionalidade garante a consistência na proteção de tokens de atualização e evita que os aplicativos implementem seus próprios mecanismos de proteção.  

**Cookies do navegador**: No Windows 10, o Azure AD suporta sso do navegador no Internet Explorer e no Microsoft Edge nativamente ou no Google Chrome através da extensão de contas do Windows 10. A segurança é construída não apenas para proteger os cookies, mas também os pontos finais para os quais os cookies são enviados. Os cookies do navegador são protegidos da mesma forma que um PRT, utilizando a chave de sessão para assinar e proteger os cookies.

Quando um usuário inicia uma interação com o navegador, o navegador (ou extensão) invoca um host cliente nativo com. O host cliente nativo garante que a página seja de um dos domínios permitidos. O navegador poderia enviar outros parâmetros para o host cliente nativo, incluindo um nonce, no entanto, o host cliente nativo garante a validação do nome do host. O host cliente nativo solicita um cookie PRT do plugin CloudAP, que cria e assina com a chave de sessão protegida pelo TPM. Como o prt-cookie é assinado pela chave de sessão, ele não pode ser adulterado. Este biscoito PRT está incluído no cabeçalho de solicitação do Azure AD para validar o dispositivo do que ele está originando. Se usar o navegador Chrome, apenas a extensão explicitamente definida no manifesto do host cliente nativo pode invocá-lo impedindo que extensões arbitrárias faça essas solicitações. Uma vez que o Azure AD valida o cookie PRT, ele emite um cookie de sessão para o navegador. Este cookie de sessão também contém a mesma chave de sessão emitida com um PRT. Durante as solicitações subseqüentes, a chave de sessão é validada efetivamente vinculando o cookie ao dispositivo e impedindo replays de outros lugares.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Quando é que um PRT recebe uma reivindicação do MFA?

Um PRT pode obter uma reivindicação de autenticação multifatorial (MFA) em cenários específicos. Quando um PRT baseado em MFA é usado para solicitar tokens para aplicativos, a reivindicação do MFA é transferida para esses tokens de aplicativo. Essa funcionalidade proporciona uma experiência perfeita aos usuários, impedindo o desafio do MFA para cada aplicativo que o exija. Um PRT pode obter uma reivindicação MFA das seguintes maneiras:

* **Faça login com o Windows Hello for Business**: O Windows Hello for Business substitui senhas e usa chaves criptográficas para fornecer uma autenticação forte de dois fatores. O Windows Hello for Business é específico para um usuário em um dispositivo, e ele próprio requer que o MFA provite. Quando um usuário faz login com o Windows Hello for Business, o PRT do usuário recebe uma reclamação do MFA. Esse cenário também se aplica aos usuários que fizerem login com smartcards se a autenticação do smartcard produzir uma reivindicação MFA da ADFS.
   * Como o Windows Hello for Business é considerado autenticação multifatorial, a reivindicação do MFA é atualizada quando o próprio PRT é atualizado, de modo que a duração do MFA se estenderá continuamente quando os usuários entrarem com o WIndows Hello for Business
* **MFA durante o login interativo wam**: Durante uma solicitação de token através do WAM, se um usuário for obrigado a fazer MFA para acessar o aplicativo, o PRT que é renovado durante essa interação é impresso com uma reclamação MFA.
   * Neste caso, a reivindicação do MFA não é atualizada continuamente, de modo que a duração do MFA é baseada na vida útil definida no diretório.
   * Quando um PRT e RT existentes anteriores são usados para acesso a um aplicativo, o PRT e o RT serão considerados como a primeira prova de autenticação. Uma nova AT será necessária com uma segunda prova e uma reivindicação mfa impressa. Isso também emitirá um novo PRT e RT.
* **MFA durante o registro do dispositivo**: Se um administrador configurou as configurações de seus dispositivos no Azure AD para exigir que o [MFA registre dispositivos,](device-management-azure-portal.md#configure-device-settings)o usuário precisa fazer O MFA para concluir o registro. Durante esse processo, o PRT que é emitido ao usuário tem a reivindicação mfa obtida durante o registro. Esse recurso só se aplica ao usuário que fez a operação de adesão, não a outros usuários que se login em que o dispositivo.
   * Semelhante ao login interativo WAM, a reivindicação mfa não é atualizada continuamente, de modo que a duração do MFA é baseada na vida útil definida no diretório.

O Windows 10 mantém uma lista particionada de PRTs para cada credencial. Então, há um PRT para cada um do Windows Hello for Business, senha ou smartcard. Esse particionamento garante que as reivindicações de MFA sejam isoladas com base na credencial usada e não misturadas durante as solicitações de token.

## <a name="how-is-a-prt-invalidated"></a>Como um PRT é invalidado?

Um PRT é invalidado nos seguintes cenários:

* **Usuário inválido**: Se um usuário for excluído ou desativado no Azure AD, seu PRT será invalidado e não poderá ser usado para obter tokens para aplicativos. Se um usuário excluído ou desativado já fez login em um dispositivo antes, o login armazenado em cache os registraria até que o CloudAP esteja ciente de seu estado inválido. Uma vez que o CloudAP determina que o usuário é inválido, ele bloqueia logons subseqüentes. Um usuário inválido é automaticamente bloqueado do login para novos dispositivos que não têm suas credenciais armazenadas em cache.
* **Dispositivo inválido**: Se um dispositivo for excluído ou desativado no Azure AD, o PRT obtido nesse dispositivo será invalidado e não poderá ser usado para obter tokens para outros aplicativos. Se um usuário já estiver conectado a um dispositivo inválido, ele pode continuar a fazê-lo. Mas todos os tokens no dispositivo são invalidados e o usuário não tem SSO para nenhum recurso desse dispositivo.
* **Mudança de senha**: Após um usuário alterar sua senha, o PRT obtido com a senha anterior é invalidado pelo Azure AD. A mudança de senha resulta na obtenção de um novo PRT. Essa invalidação pode acontecer de duas maneiras diferentes:
   * Se o usuário entrar no Windows com sua nova senha, o CloudAP descartará o PRT antigo e solicitará ao Azure AD para emitir um novo PRT com sua nova senha. Se o usuário não tiver uma conexão com a Internet, a nova senha não poderá ser validada, o Windows pode exigir que o usuário digite sua senha antiga.
   * Se um usuário fez login com sua senha antiga ou alterou sua senha após fazer login no Windows, o PRT antigo será usado para quaisquer solicitações de token baseadas em WAM. Neste cenário, o usuário é solicitado a reautenticar durante a solicitação de token WAM e um novo PRT é emitido.
* **Problemas de TPM**: Às vezes, o TPM de um dispositivo pode vacilar ou falhar, levando à inacessibilidade das chaves protegidas pelo TPM. Neste caso, o dispositivo é incapaz de obter um PRT ou solicitar tokens usando um PRT existente, pois não pode provar a posse das chaves criptográficas. Como resultado, qualquer PRT existente é invalidado pelo Azure AD. Quando o Windows 10 detecta uma falha, ele inicia um fluxo de recuperação para reregistrar o dispositivo com novas chaves criptográficas. Com o Azure Ad híbrido, assim como o registro inicial, a recuperação acontece silenciosamente sem a entrada do usuário. Para dispositivos registrados no Azure AD ou no Azure AD, a recuperação precisa ser realizada por um usuário que tenha privilégios de administrador no dispositivo. Neste cenário, o fluxo de recuperação é iniciado por um prompt do Windows que orienta o usuário a recuperar o dispositivo com sucesso.

## <a name="detailed-flows"></a>Fluxos detalhados

Os diagramas a seguir ilustram os detalhes subjacentes na emissão, renovação e uso de um PRT para solicitar um token de acesso a um aplicativo. Além disso, essas etapas também descrevem como os mecanismos de segurança acima mencionados são aplicados durante essas interações.

### <a name="prt-issuance-during-first-sign-in"></a>Emissão de PRT durante o primeiro sinal

![Emissão de PRT durante primeiro sinal em fluxo detalhado](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> Nos dispositivos azure AD unidos, essa troca acontece de forma sincronizada para emitir um PRT antes que o usuário possa fazer logon no Windows. Nos dispositivos híbridos do Azure AD, o Active Directory no local é a principal autoridade. Assim, o usuário só está esperando até que possa adquirir um TGT para fazer login, enquanto a emissão de PRT acontece de forma assíncrona. Este cenário não se aplica aos dispositivos registrados no Azure AD, pois o logon não usa credenciais Azure AD.

| Etapa | Descrição |
| :---: | --- |
| Um | O usuário insere sua senha no login na UI. O LogonUI passa as credenciais em um buffer auth para lSA, que por sua vez passa internamente para o CloudAP. O CloudAP encaminha essa solicitação para o plugin CloudAP. |
| B | O plugin CloudAP inicia uma solicitação de descoberta de reino para identificar o provedor de identidade para o usuário. Se o inquilino do usuário tiver uma configuração de provedor de federação, o Azure AD retorna o ponto final do Metadata Exchange (MEX) do provedor da federação. Se não, o Azure AD retorna que o usuário é gerenciado indicando que o usuário pode autenticar com o Azure AD. |
| C | Se o usuário for gerenciado, o CloudAP receberá o nonce do Azure AD. Se o usuário estiver federado, o plugin CloudAP solicitará um token SAML do provedor da federação com as credenciais do usuário. Uma vez que ele recebe, o token SAML, ele solicita um nonce do Azure AD. |
| D | O plugin CloudAP constrói a solicitação de autenticação com as credenciais do usuário, nonce e um escopo de corretor, assina a solicitação com a chave do dispositivo (dkpriv) e envia-a para o Azure AD. Em um ambiente federado, o plugin CloudAP usa o token SAML devolvido pelo provedor da federação em vez das credenciais do usuário. |
| E | O Azure AD valida as credenciais do usuário, o nonce e a assinatura do dispositivo, verifica se o dispositivo é válido no inquilino e emite o PRT criptografado. Junto com o PRT, o Azure AD também emite uma chave simétrica, chamada de chave Session criptografada pelo Azure AD usando a chave Transporte (tkpub). Além disso, a tecla Session também está incorporada no PRT. Esta chave de Sessão atua como a chave De prova de posse (PoP) para solicitações subseqüentes com o PRT. |
| F | O plugin CloudAP passa a chave PRT e Session criptografada para o CloudAP. O CloudAP solicita ao TPM para descriptografar a chave Sessão usando a tecla Transporte (tkpriv) e recriptografá-la usando a própria chave do TPM. O CloudAP armazena a chave Session criptografada em seu cache junto com o PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>Renovação prt em logons subseqüentes

![Renovação prt em logons subseqüentes](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Etapa | Descrição |
| :---: | --- |
| Um | O usuário insere sua senha no login na UI. O LogonUI passa as credenciais em um buffer auth para lSA, que por sua vez passa internamente para o CloudAP. O CloudAP encaminha essa solicitação para o plugin CloudAP. |
| B | Se o usuário já fez logon ao usuário anteriormente, o Windows inicia o login em cache e valida credenciais para fazer login no usuário. A cada 4 horas, o plugin CloudAP inicia a renovação prt de forma assíncrona. |
| C | O plugin CloudAP inicia uma solicitação de descoberta de reino para identificar o provedor de identidade para o usuário. Se o inquilino do usuário tiver uma configuração de provedor de federação, o Azure AD retorna o ponto final do Metadata Exchange (MEX) do provedor da federação. Se não, o Azure AD retorna que o usuário é gerenciado indicando que o usuário pode autenticar com o Azure AD. |
| D | Se o usuário estiver federado, o plugin CloudAP solicitará um token SAML do provedor da federação com as credenciais do usuário. Uma vez que ele recebe, o token SAML, ele solicita um nonce do Azure AD. Se o usuário for gerenciado, o CloudAP receberá diretamente o nonce do Azure AD. |
| E | O plugin CloudAP constrói a solicitação de autenticação com as credenciais do usuário, nonce e prt existente, assina a solicitação com a chave Sessão e envia-a para o Azure AD. Em um ambiente federado, o plugin CloudAP usa o token SAML devolvido pelo provedor da federação em vez das credenciais do usuário. |
| F | O Azure AD valida a assinatura da chave da Sessão comparando-a com a chave Sessão incorporada no PRT, valida o nonce e verifica se o dispositivo é válido no inquilino e emite um novo PRT. Como visto anteriormente, o PRT é novamente acompanhado com a chave Session criptografada pela chave Transporte (tkpub). |
| G | O plugin CloudAP passa a chave PRT e Session criptografada para o CloudAP. O CloudAP solicita ao TPM para descriptografar a chave Sessão usando a tecla Transporte (tkpriv) e recriptografá-la usando a própria chave do TPM. O CloudAP armazena a chave Session criptografada em seu cache junto com o PRT. |

### <a name="prt-usage-during-app-token-requests"></a>Uso de PRT durante solicitações de token de aplicativo

![Uso de PRT durante solicitações de token de aplicativo](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Etapa | Descrição |
| :---: | --- |
| Um | Um aplicativo (por exemplo, Outlook, OneNote etc.) inicia uma solicitação de token ao WAM. A WAM, por sua vez, pede ao plugin Azure AD WAM para atender à solicitação de token. |
| B | Se um token de atualização para o aplicativo já estiver disponível, o plugin AD WAM do Azure o usará para solicitar um token de acesso. Para fornecer a prova de vinculação do dispositivo, o plugin WAM assina a solicitação com a chave Sessão. O Azure AD valida a chave Session e emite um token de acesso e um novo token de atualização para o aplicativo, criptografado pela chave Sessão. O plugin WAM solicita ao plugin Cloud AP para descriptografar os tokens, o que, por sua vez, solicita ao TPM para descriptografar usando a tecla Session, resultando no plugin WAM recebendo ambos os tokens. Em seguida, o plugin WAM fornece apenas o token de acesso ao aplicativo, enquanto ele recriptografa o token de atualização com DPAPI e armazena-o em seu próprio cache  |
| C |  Se um token de atualização para o aplicativo não estiver disponível, o plugin AD WAM do Azure usará o PRT para solicitar um token de acesso. Para fornecer o comprovante de posse, o plugin WAM assina a solicitação contendo o PRT com a chave Sessão. O Azure AD valida a assinatura da chave da Sessão comparando-a com a chave Sessão incorporada no PRT, verifica se o dispositivo é válido e emite um token de acesso e um token de atualização para o aplicativo. além disso, o Azure AD pode emitir um novo PRT (baseado no ciclo de atualização), todos eles criptografados pela tecla Session. |
| D | O plugin WAM solicita ao plugin Cloud AP para descriptografar os tokens, o que, por sua vez, solicita ao TPM para descriptografar usando a tecla Session, resultando no plugin WAM recebendo ambos os tokens. Em seguida, o plugin WAM fornece apenas o token de acesso ao aplicativo, enquanto ele recriptografa o token de atualização com DPAPI e armazena-o em seu próprio cache. O plugin WAM usará o token de atualização daqui para frente para este aplicativo. O plugin WAM também devolve o novo plugin PRT para Cloud AP, que valida o PRT com o Azure AD antes de atualizá-lo em seu próprio cache. O plugin Cloud AP usará o novo PRT daqui para frente. |
| E | O WAM fornece o token de acesso recém-emitido ao WAM, que, por sua vez, fornece de volta ao aplicativo de chamada|

### <a name="browser-sso-using-prt"></a>Navegador SSO usando PRT

![Navegador SSO usando PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Etapa | Descrição |
| :---: | --- |
| Um | O usuário faz login no Windows com suas credenciais para obter um PRT. Uma vez que o usuário abre o navegador, o navegador (ou extensão) carrega as URLs do registro. |
| B | Quando um usuário abre uma URL de login azure AD, o navegador ou extensão valida a URL com as obtidas no registro. Se eles coincidirem, o navegador invoca o host cliente nativo para obter um token. |
| C | O host cliente nativo valida que os URLs pertencem aos provedores de identidade da Microsoft (conta Microsoft ou Azure AD), extrai um nonce enviado da URL e faz uma chamada para o plugin CloudAP para obter um cookie PRT. |
| D | O plugin CloudAP criará o cookie PRT, entrará com a chave de sessão vinculada ao TPM e enviará de volta para o host cliente nativo. Como o cookie é assinado pela chave de sessão, ele não pode ser adulterado. |
| E | O host cliente nativo retornará este cookie PRT para o navegador, o que o incluirá como parte do cabeçalho de solicitação chamado x-ms-RefreshTokenCredential e solicitará tokens do Azure AD. |
| F | O Azure AD valida a assinatura da chave da Sessão no cookie PRT, valida o nonce, verifica se o dispositivo é válido no inquilino e emite um token de ID para a página da Web e um cookie de sessão criptografado para o navegador. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a solução de problemas relacionados ao PRT, consulte o artigo [Solução de problemas híbrido suscetido ao Windows 10 e ao Windows Server 2016 dispositivos](troubleshoot-hybrid-join-windows-current.md).
