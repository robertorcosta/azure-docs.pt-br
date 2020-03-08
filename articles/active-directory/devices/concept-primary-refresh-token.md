---
title: Token de atualização primário (PRT) e Azure AD-Azure Active Directory
description: Qual é a função do e como podemos gerenciar o PRT (token de atualização principal) no Azure Active Directory?
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
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672644"
---
# <a name="what-is-a-primary-refresh-token"></a>O que é um token de atualização primário?

Um PRT (token de atualização principal) é um artefato importante da autenticação do Azure AD em dispositivos Windows 10, iOS e Android. É um JWT (token Web JSON) emitido especialmente para agentes de token de terceiros da Microsoft para habilitar o SSO (logon único) nos aplicativos usados nesses dispositivos. Neste artigo, forneceremos detalhes sobre como um PRT é emitido, usado e protegido em dispositivos Windows 10.

Este artigo pressupõe que você já entendeu os diferentes Estados de dispositivo disponíveis no Azure AD e como o logon único funciona no Windows 10. Para obter mais informações sobre dispositivos no Azure AD, consulte o artigo [o que é gerenciamento de dispositivos no Azure Active Directory?](overview.md)

## <a name="key-terminology-and-components"></a>Principais terminologias e componentes

Os componentes do Windows a seguir desempenham um papel fundamental na solicitação e uso de um PRT:

* CloudAP ( **provedor de autenticação de nuvem** ): CloudAP é o provedor de autenticação moderno para entrar no Windows, que verifica os usuários que estão fazendo logon em um dispositivo Windows 10. O CloudAP fornece uma estrutura de plug-in que os provedores de identidade podem desenvolver para habilitar a autenticação no Windows usando as credenciais do provedor de identidade.
* **Gerenciador de contas da Web** (WAM): WAM é o agente de token padrão em dispositivos Windows 10. O WAM também fornece uma estrutura de plug-in que os provedores de identidade podem criar e habilitar o SSO para seus aplicativos que dependem desse provedor de identidade.
* **Plug-in CloudAP do Azure ad**: um plug-in específico do Azure ad criado na estrutura CloudAP, que verifica as credenciais do usuário com o Azure ad durante a entrada do Windows.
* **Plug-in do Azure ad WAM**: um plug-in específico do Azure ad criado na estrutura WAM, que HABILITA o SSO para aplicativos que dependem do Azure ad para autenticação.
* **Dsreg**: um componente específico do Azure AD no Windows 10, que manipula o processo de registro de dispositivo para todos os Estados do dispositivo.
* **Trusted Platform Module** (TPM): um TPM é um componente de hardware incorporado a um dispositivo, que fornece funções de segurança baseadas em hardware para segredos de usuário e dispositivo. Mais detalhes podem ser encontrados no artigo [Trusted Platform Module visão geral da tecnologia](/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>O que o PRT contém?

Um PRT contém declarações geralmente contidas em qualquer token de atualização do Azure AD. Além disso, há algumas declarações específicas do dispositivo incluídas no PRT. Elas são as seguintes:

* **ID do dispositivo**: um PRT é emitido para um usuário em um dispositivo específico. A declaração de ID do dispositivo `deviceID` determina o dispositivo no qual o PRT foi emitido para o usuário. Essa declaração é emitida posteriormente para tokens obtidos por meio do PRT. A declaração de ID do dispositivo é usada para determinar a autorização de acesso condicional com base no estado ou na conformidade do dispositivo.
* **Chave de sessão**: a chave de sessão é uma chave simétrica criptografada, gerada pelo serviço de autenticação do Azure AD, emitida como parte do PRT. A chave de sessão atua como prova de posse quando um PRT é usado para obter tokens para outros aplicativos.

### <a name="can-i-see-whats-in-a-prt"></a>Posso ver o que há em um PRT?

Um PRT é um blob opaco enviado do Azure AD cujo conteúdo não é conhecido por nenhum componente cliente. Você não pode ver o que está dentro de um PRT.

## <a name="how-is-a-prt-issued"></a>Como um PRT é emitido?

O registro de dispositivo é um pré-requisito para a autenticação baseada em dispositivo no Azure AD. Um PRT é emitido para usuários somente em dispositivos registrados. Para obter detalhes mais detalhados sobre o registro de dispositivos, consulte o artigo [Windows Hello para empresas e registro de dispositivos](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Durante o registro do dispositivo, o componente dsreg gera dois conjuntos de pares de chaves de criptografia:

* Chave do dispositivo (dkpub/dkpriv)
* Chave de transporte (tkpub/tkpriv)

As chaves privadas serão associadas ao TPM do dispositivo se o dispositivo tiver um TPM válido e em funcionamento, enquanto as chaves públicas são enviadas ao Azure AD durante o processo de registro do dispositivo. Essas chaves são usadas para validar o estado do dispositivo durante solicitações de PRT.

O PRT é emitido durante a autenticação do usuário em um dispositivo Windows 10 em dois cenários:

* Ingresso no Azure ad **ingressado** ou **ad híbrido**: um PRT é emitido durante o logon do Windows quando um usuário entra com suas credenciais da organização. Um PRT é emitido com todas as credenciais com suporte do Windows 10, por exemplo, senha e Windows Hello para empresas. Nesse cenário, o plug-in CloudAP do Azure AD é a autoridade principal para o PRT.
* **Dispositivo registrado do Azure ad**: um PRT é emitido quando um usuário adiciona uma conta de trabalho secundária ao seu dispositivo Windows 10. Os usuários podem adicionar uma conta ao Windows 10 de duas maneiras diferentes:  
   * Adicionar uma conta por meio da solicitação **usar esta conta em qualquer lugar neste dispositivo** depois de entrar em um aplicativo (por exemplo, Outlook)
   * Adicionar uma conta de **configurações** > **contas** > **acessar o trabalho ou a escola** > **conectar**

Nos cenários de dispositivo registrado do Azure AD, o plug-in do Azure AD WAM é a autoridade primária para o PRT, pois o logon do Windows não está ocorrendo com esta conta do Azure AD.

> [!NOTE]
> provedores de identidade de terceiros precisam dar suporte ao protocolo WS-Trust para habilitar a emissão de PRT em dispositivos Windows 10. Sem o WS-Trust, o PRT não pode ser emitido para usuários em dispositivos ingressados no Azure AD híbrido ou no Azure AD

## <a name="what-is-the-lifetime-of-a-prt"></a>Qual é o tempo de vida de um PRT?

Depois de emitido, um PRT é válido por 14 dias e é renovado continuamente, contanto que o usuário use o dispositivo ativamente.  

## <a name="how-is-a-prt-used"></a>Como é usado um PRT?

Um PRT é usado por dois componentes principais no Windows:

* **Plug-in CloudAP do Azure ad**: durante a entrada do Windows, o plug-in CloudAP do Azure ad solicita um PRT do Azure ad usando as credenciais fornecidas pelo usuário. Ele também armazena em cache o PRT para habilitar a entrada armazenada em cache quando o usuário não tem acesso a uma conexão com a Internet.
* **Plug-in do Azure ad WAM**: quando os usuários tentam acessar aplicativos, o plug-in do Azure ad WAM usa o PRT para habilitar o SSO no Windows 10. O plug-in do Azure AD WAM usa o PRT para solicitar tokens de atualização e de acesso para aplicativos que dependem do WAM para solicitações de token. Ele também habilita o SSO em navegadores injetando o PRT nas solicitações do navegador. O SSO do navegador no Windows 10 tem suporte no Microsoft Edge (nativamente) e no Chrome (por meio das contas do Windows 10 ou da extensão do Office Online).

## <a name="how-is-a-prt-renewed"></a>Como um PRT é renovado?

Um PRT é renovado em dois métodos diferentes:

* **Plug-in do Azure ad CloudAP a cada 4 horas**: o plug-in CloudAP renova o PRT a cada 4 horas durante o logon do Windows. Se o usuário não tiver conexão com a Internet durante esse tempo, o plug-in CloudAP renovará o PRT depois que o dispositivo estiver conectado à Internet.
* **Plug-in do Azure ad WAM durante solicitações de token de aplicativo**: o plugin WAM HABILITA o SSO em dispositivos Windows 10 habilitando solicitações de token silencioso para aplicativos. O plug-in WAM pode renovar o PRT durante essas solicitações de token de duas maneiras diferentes:
   * Um aplicativo solicita o WAM para um token de acesso silenciosamente, mas não há um token de atualização disponível para esse aplicativo. Nesse caso, o WAM usa o PRT para solicitar um token para o aplicativo e retorna um novo PRT na resposta.
   * Um aplicativo solicita o WAM para um token de acesso, mas o PRT é inválido ou o Azure AD requer autorização adicional (por exemplo, autenticação multifator do Azure). Nesse cenário, o WAM inicia um logon interativo que exige que o usuário seja autenticado novamente ou forneça verificação adicional e um novo PRT seja emitido na autenticação bem-sucedida.

### <a name="key-considerations"></a>Considerações-chave

* Um PRT só é emitido e renovado durante a autenticação de aplicativo nativo. Um PRT não é renovado ou emitido durante uma sessão de navegador.
* No Azure AD ingressado e dispositivos ingressados no Azure AD híbrido, o plug-in CloudAP é a autoridade principal para um PRT. Se um PRT for renovado durante uma solicitação de token baseada em WAM, o PRT será enviado de volta para o plug-in CloudAP, que verifica a validade do PRT com o Azure AD antes de aceitá-lo.

## <a name="how-is-the-prt-protected"></a>Como o PRT é protegido?

Um PRT é protegido ao associá-lo ao dispositivo ao qual o usuário entrou. O Azure AD e o Windows 10 habilitam a proteção de PRT por meio dos seguintes métodos:

* **Durante a primeira entrada**: durante a primeira entrada, um PRT é emitido por meio de solicitações de assinatura usando a chave do dispositivo gerada criptograficamente durante o registro do dispositivo. Em um dispositivo com um TPM válido e em funcionamento, a chave do dispositivo é protegida pelo TPM, impedindo qualquer acesso mal-intencionado. Um PRT não será emitido se a assinatura de chave de dispositivo correspondente não puder ser validada.
* **Durante solicitações de token e renovação**: quando um PRT é emitido, o Azure ad também emite uma chave de sessão criptografada para o dispositivo. Ele é criptografado com a tkpub (chave de transporte pública) gerada e enviada ao Azure AD como parte do registro do dispositivo. Essa chave de sessão só pode ser descriptografada pela tkpriv (chave de transporte privada) protegida pelo TPM. A chave de sessão é a chave de prova de posse (POP) para todas as solicitações enviadas ao Azure AD.  A chave de sessão também é protegida pelo TPM e nenhum outro componente do sistema operacional pode acessá-la. Solicitações de token ou solicitações de renovação de PRT são assinadas com segurança por essa chave de sessão por meio do TPM e, portanto, não podem ser adulteradas. O Azure AD invalidará todas as solicitações do dispositivo que não estão assinadas pela chave de sessão correspondente.

Ao proteger essas chaves com o TPM, os atores mal-intencionados não podem roubar as chaves nem repetir o PRT em outro lugar, pois o TPM está inacessível mesmo que um invasor tenha posse física do dispositivo.  Portanto, o uso de um TPM aprimora muito a segurança do Azure ad ingressado, o Azure AD híbrido e os dispositivos registrados no Azure AD contra roubo de credenciais. Para desempenho e confiabilidade, o TPM 2,0 é a versão recomendada para todos os cenários de registro de dispositivo do Azure AD no Windows 10.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Como os tokens de aplicativo e cookies de navegador são protegidos?

**Tokens de aplicativo**: quando um aplicativo solicita um token por meio do WAM, o Azure ad emite um token de atualização e um token de acesso. No entanto, o WAM retorna apenas o token de acesso para o aplicativo e protege o token de atualização em seu cache criptografando-o com a chave da interface de programação de aplicativo de proteção de dados (DPAPI) do usuário. O WAM usa com segurança o token de atualização assinando solicitações com a chave de sessão para emitir mais tokens de acesso. A chave DPAPI é protegida por uma chave simétrica baseada no Azure AD no próprio Azure AD. Quando o dispositivo precisa descriptografar o perfil do usuário com a chave DPAPI, o AD do Azure fornece a chave DPAPI criptografada pela chave de sessão, que o plug-in CloudAP solicita TPM para descriptografar. Essa funcionalidade garante a consistência na proteção de tokens de atualização e evita que aplicativos que implementam seus próprios mecanismos de proteção.  

**Cookies do navegador**: no Windows 10, o Azure ad dá suporte ao SSO do navegador no Internet Explorer e no Microsoft Edge nativamente ou no Google Chrome por meio da extensão de contas do Windows 10. A segurança é criada não apenas para proteger os cookies, mas também os pontos de extremidade para os quais os cookies são enviados. Os cookies do navegador são protegidos da mesma maneira que um PRT, utilizando a chave de sessão para assinar e proteger os cookies.

Quando um usuário inicia uma interação COM o navegador, o navegador (ou a extensão) invoca um host cliente nativo do COM. O host do cliente nativo garante que a página seja de um dos domínios permitidos. O navegador pode enviar outros parâmetros para o host do cliente nativo, incluindo um nonce, no entanto, o host do cliente nativo garante a validação do nome do host. O host de cliente nativo solicita um controlador de chave de PRT do plug-in CloudAP, que cria e o assina com a tecla de sessão protegida pelo TPM. Como o PRT-cookie é assinado pela chave de sessão, ele não pode ser adulterado. Esse o PRT-cookie é incluído no cabeçalho de solicitação do Azure AD para validar o dispositivo do qual ele se origina. Se você estiver usando o navegador Chrome, apenas a extensão explicitamente definida no manifesto do host do cliente nativo poderá chamá-lo impedindo que extensões arbitrárias façam essas solicitações. Depois que o Azure AD valida o cookie PRT, ele emite um cookie de sessão para o navegador. Esse cookie de sessão também contém a mesma chave de sessão emitida com um PRT. Durante as solicitações subsequentes, a chave de sessão é validada efetivamente ligando o cookie ao dispositivo e impedindo repetições de outro lugar.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Quando um PRT Obtém uma declaração de MFA?

Um PRT pode obter uma declaração de MFA (autenticação multifator) em cenários específicos. Quando um PRT baseado em MFA é usado para solicitar tokens para aplicativos, a declaração MFA é transferida para esses tokens de aplicativo. Essa funcionalidade fornece uma experiência direta aos usuários, evitando o desafio de MFA para cada aplicativo que o exige. Um PRT pode obter uma declaração de MFA das seguintes maneiras:

* **Entrar com o Windows Hello para empresas**: o Windows Hello para empresas substitui senhas e usa chaves criptográficas para fornecer autenticação forte de dois fatores. O Windows Hello para empresas é específico de um usuário em um dispositivo e, por si só, requer MFA para provisionar. Quando um usuário faz logon com o Windows Hello para empresas, o PRT do usuário Obtém uma declaração de MFA. Esse cenário também se aplica a usuários que fazem logon com cartões inteligentes se a autenticação de cartão inteligente produz uma declaração MFA do ADFS.
   * Como o Windows Hello for Business é considerado autenticação multifator, a declaração MFA é atualizada quando o próprio PRT é atualizado, portanto, a duração da MFA se estenderá continuamente quando os usuários entrarem com o WIndows Hello for Business
* **MFA durante a entrada interativa do WAM**: durante uma solicitação de token por meio do WAM, se um usuário for solicitado a fazer a MFA para acessar o aplicativo, o PRT renovado durante essa interação será impresso com uma declaração de MFA.
   * Nesse caso, a declaração MFA não é atualizada continuamente, portanto, a duração da MFA é baseada no tempo de vida definido no diretório.
   * Quando um PRT e RT existentes anteriores são usados para acesso a um aplicativo, o PRT e o RT serão considerados como a primeira prova de autenticação. Um novo AT será necessário com uma segunda prova e uma declaração de MFA impressa. Isso também emitirá um novo PRT e RT.
* **MFA durante o registro do dispositivo**: se um administrador tiver configurado suas configurações de dispositivo no Azure ad para [exigir que o MFA Registre dispositivos](device-management-azure-portal.md#configure-device-settings), o usuário precisará fazer MFA para concluir o registro. Durante esse processo, o PRT emitido para o usuário tem a declaração MFA obtida durante o registro. Essa funcionalidade só se aplica ao usuário que fez a operação de ingresso, e não a outros usuários que entram nesse dispositivo.
   * Semelhante ao logon interativo do WAM, a declaração de MFA não é atualizada continuamente, portanto, a duração da MFA é baseada no tempo de vida definido no diretório.

O Windows 10 mantém uma lista particionada de PRTs para cada credencial. Portanto, há um PRT para cada um dos Windows Hello para empresas, senha ou cartão inteligente. Esse particionamento garante que as declarações MFA sejam isoladas com base na credencial usada e não sejam misturadas durante solicitações de token.

## <a name="how-is-a-prt-invalidated"></a>Como um PRT é invalidado?

Um PRT é invalidado nos seguintes cenários:

* **Usuário inválido**: se um usuário for excluído ou desabilitado no Azure AD, seu PRT será invalidado e não poderá ser usado para obter tokens para aplicativos. Se um usuário excluído ou desabilitado já tiver entrado em um dispositivo antes, a entrada armazenada em cache o registraria, até que CloudAP esteja ciente de seu estado inválido. Depois que CloudAP determinar que o usuário é inválido, ele bloqueará logons subsequentes. Um usuário inválido é bloqueado automaticamente de entrar em novos dispositivos que não têm suas credenciais armazenadas em cache.
* **Dispositivo inválido**: se um dispositivo for excluído ou desabilitado no Azure AD, o PRT obtido nesse dispositivo será invalidado e não poderá ser usado para obter tokens para outros aplicativos. Se um usuário já tiver entrado em um dispositivo inválido, ele poderá continuar a fazê-lo. Mas todos os tokens no dispositivo são invalidados e o usuário não tem o SSO para nenhum recurso desse dispositivo.
* **Alteração de senha**: depois que um usuário altera sua senha, o PRT obtido com a senha anterior é invalidado pelo Azure AD. A alteração de senha faz com que o usuário receba um novo PRT. Essa invalidação pode ocorrer de duas maneiras diferentes:
   * Se o usuário entrar no Windows com sua nova senha, o CloudAP descartará o PRT antigo e solicitará que o Azure AD emita um novo PRT com a nova senha. Se o usuário não tiver uma conexão com a Internet, a nova senha não poderá ser validada, o Windows poderá exigir que o usuário insira sua senha antiga.
   * Se um usuário tiver feito logon com sua senha antiga ou alterado sua senha depois de entrar no Windows, o PRT antigo será usado para qualquer solicitação de token baseada em WAM. Nesse cenário, o usuário é solicitado a autenticar novamente durante a solicitação de token WAM e um novo PRT é emitido.
* **Problemas de TPM**: às vezes, o TPM de um dispositivo pode falhas ou falhar, levando à inacessibilidade de chaves protegidas pelo TPM. Nesse caso, o dispositivo é incapaz de obter um PRT ou solicitar tokens usando um PRT existente, pois ele não pode provar a posse das chaves de criptografia. Como resultado, qualquer PRT existente é invalidado pelo Azure AD. Quando o Windows 10 detecta uma falha, ele inicia um fluxo de recuperação para registrar novamente o dispositivo com novas chaves de criptografia. Com o ingresso do Azure ad híbrido, assim como o registro inicial, a recuperação ocorre silenciosamente sem a entrada do usuário. Para dispositivos registrados no Azure AD ou no Azure AD, a recuperação precisa ser executada por um usuário que tenha privilégios de administrador no dispositivo. Nesse cenário, o fluxo de recuperação é iniciado por um prompt do Windows que orienta o usuário a recuperar o dispositivo com êxito.

## <a name="detailed-flows"></a>Fluxos detalhados

Os diagramas a seguir ilustram os detalhes subjacentes na emissão, renovação e uso de um PRT para solicitar um token de acesso para um aplicativo. Além disso, essas etapas também descrevem como os mecanismos de segurança mencionados acima são aplicados durante essas interações.

### <a name="prt-issuance-during-first-sign-in"></a>Emissão de PRT durante a primeira entrada

![Emissão de PRT durante o primeiro logon detalhado fluxo](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> Em dispositivos adicionados ao Azure AD, essa troca ocorre de forma síncrona para emitir um PRT antes que o usuário possa fazer logon no Windows. Em dispositivos ingressados no Azure AD híbridos, Active Directory local é a autoridade principal. Portanto, o usuário está aguardando até que possa adquirir um TGT para fazer logon, enquanto a emissão de PRT ocorre de forma assíncrona. Esse cenário não se aplica aos dispositivos registrados do Azure AD, pois o logon não usa as credenciais do Azure AD.

| Etapa | DESCRIÇÃO |
| :---: | --- |
| Um | O usuário insere sua senha na interface do usuário de entrada. O LogonUI passa as credenciais em um buffer de autenticação para o LSA, que, por sua só, passa internamente para CloudAP. O CloudAP encaminha essa solicitação para o plug-in CloudAP. |
| B | O plug-in CloudAP inicia uma solicitação de descoberta de realm para identificar o provedor de identidade para o usuário. Se o locatário do usuário tiver uma configuração de provedor de Federação, o Azure AD retornará o ponto de extremidade MEX (Exchange Metadata Endpoint) do provedor de Federação. Caso contrário, o AD do Azure retorna que o usuário é gerenciado indicando que o usuário pode se autenticar com o Azure AD. |
| C | Se o usuário for gerenciado, o CloudAP receberá o nonce do Azure AD. Se o usuário for federado, o plug-in CloudAP solicitará um token SAML do provedor de Federação com as credenciais do usuário. Depois de receber, o token SAML solicita um nonce do Azure AD. |
| D | O plug-in CloudAP constrói a solicitação de autenticação com as credenciais do usuário, nonce e um escopo do agente, assina a solicitação com a chave do dispositivo (dkpriv) e a envia ao Azure AD. Em um ambiente federado, o plug-in CloudAP usa o token SAML retornado pelo provedor de Federação em vez das credenciais do usuário. |
| E | O Azure AD valida as credenciais do usuário, o nonce e a assinatura do dispositivo, verifica se o dispositivo é válido no locatário e emite o PRT criptografado. Juntamente com o PRT, o Azure AD também emite uma chave simétrica, chamada de chave de sessão criptografada pelo AD do Azure usando a chave de transporte (tkpub). Além disso, a chave de sessão também é inserida no PRT. Essa chave de sessão atua como a chave de prova de posse (PoP) para solicitações subsequentes com o PRT. |
| F | O plug-in CloudAP passa o PRT criptografado e a chave de sessão para CloudAP. CloudAP solicite o TPM para descriptografar a chave de sessão usando a chave de transporte (tkpriv) e criptografá-la novamente usando a própria chave do TPM. O CloudAP armazena a chave de sessão criptografada em seu cache junto com o PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>Renovação de PRT em logons subsequentes

![Renovação de PRT em logons subsequentes](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Etapa | DESCRIÇÃO |
| :---: | --- |
| Um | O usuário insere sua senha na interface do usuário de entrada. O LogonUI passa as credenciais em um buffer de autenticação para o LSA, que, por sua só, passa internamente para CloudAP. O CloudAP encaminha essa solicitação para o plug-in CloudAP. |
| B | Se o usuário tiver feito logon anteriormente no usuário, o Windows iniciará a entrada em cache e validará as credenciais para fazer o logon do usuário. A cada 4 horas, o plug-in CloudAP inicia a renovação de PRT de forma assíncrona. |
| C | O plug-in CloudAP inicia uma solicitação de descoberta de realm para identificar o provedor de identidade para o usuário. Se o locatário do usuário tiver uma configuração de provedor de Federação, o Azure AD retornará o ponto de extremidade MEX (Exchange Metadata Endpoint) do provedor de Federação. Caso contrário, o AD do Azure retorna que o usuário é gerenciado indicando que o usuário pode se autenticar com o Azure AD. |
| D | Se o usuário for federado, o plug-in CloudAP solicitará um token SAML do provedor de Federação com as credenciais do usuário. Depois de receber, o token SAML solicita um nonce do Azure AD. Se o usuário for gerenciado, o CloudAP receberá diretamente o nonce do Azure AD. |
| E | O plug-in CloudAP constrói a solicitação de autenticação com as credenciais do usuário, nonce e o PRT existente, assina a solicitação com a chave de sessão e a envia ao Azure AD. Em um ambiente federado, o plug-in CloudAP usa o token SAML retornado pelo provedor de Federação em vez das credenciais do usuário. |
| F | O Azure AD valida a assinatura de chave de sessão comparando-a com a chave de sessão inserida no PRT, valida o nonce e verifica se o dispositivo é válido no locatário e emite um novo PRT. Como visto anteriormente, o PRT é acompanhado novamente com a chave de sessão criptografada pela chave de transporte (tkpub). |
| G | O plug-in CloudAP passa o PRT criptografado e a chave de sessão para CloudAP. CloudAP solicita que o TPM descriptografe a chave de sessão usando a chave de transporte (tkpriv) e criptografe-a novamente usando a própria chave do TPM. O CloudAP armazena a chave de sessão criptografada em seu cache junto com o PRT. |

### <a name="prt-usage-during-app-token-requests"></a>Uso de PRT durante solicitações de token de aplicativo

![Uso de PRT durante solicitações de token de aplicativo](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Etapa | DESCRIÇÃO |
| :---: | --- |
| Um | Um aplicativo (por exemplo, Outlook, OneNote, etc.) inicia uma solicitação de token para o WAM. O WAM, por sua vez, solicita ao plug-in do Azure AD WAM para atender à solicitação de token. |
| B | Se um token de atualização para o aplicativo já estiver disponível, o plug-in do Azure AD WAM o usará para solicitar um token de acesso. Para fornecer uma prova de ligação de dispositivo, o plugin WAM assina a solicitação com a chave de sessão. O Azure AD valida a chave de sessão e emite um token de acesso e um novo token de atualização para o aplicativo, criptografado pela chave de sessão. O plugin WAM solicita plug-in de AP de nuvem para descriptografar os tokens que, por sua vez, solicita que o TPM descriptografe usando a chave de sessão, resultando no plugin WAM obter ambos os tokens. Em seguida, o plugin WAM fornece apenas o token de acesso para o aplicativo, enquanto criptografa novamente o token de atualização com DPAPI e o armazena em seu próprio cache  |
| C |  Se um token de atualização para o aplicativo não estiver disponível, o plug-in do Azure AD WAM usará o PRT para solicitar um token de acesso. Para fornecer uma prova de posse, o plugin WAM assina a solicitação que contém o PRT com a chave de sessão. O Azure AD valida a assinatura de chave de sessão comparando-a com a chave de sessão inserida no PRT, verifica se o dispositivo é válido e emite um token de acesso e um token de atualização para o aplicativo. Além disso, o Azure AD pode emitir um novo PRT (com base no ciclo de atualização), todos eles criptografados pela chave de sessão. |
| D | O plugin WAM solicita plug-in de AP de nuvem para descriptografar os tokens que, por sua vez, solicita que o TPM descriptografe usando a chave de sessão, resultando no plugin WAM obter ambos os tokens. Em seguida, o plugin WAM fornece apenas o token de acesso para o aplicativo, enquanto criptografa novamente o token de atualização com DPAPI e o armazena em seu próprio cache. O plugin WAM usará o token de atualização no futuro para este aplicativo. O plugin WAM também fornece de volta o novo PRT para o plug-in de AP de nuvem, que valida o PRT com o Azure AD antes de atualizá-lo em seu próprio cache. O plug-in de AP de nuvem usará o novo PRT no futuro. |
| E | O WAM fornece o token de acesso emitido recentemente para o WAM, que, por sua vez, o fornece de volta para o aplicativo de chamada|

### <a name="browser-sso-using-prt"></a>SSO do navegador usando PRT

![SSO do navegador usando PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Etapa | DESCRIÇÃO |
| :---: | --- |
| Um | O usuário faz logon no Windows com suas credenciais para obter um PRT. Quando o usuário abrir o navegador, o navegador (ou extensão) carregará as URLs do registro. |
| B | Quando um usuário abre uma URL de logon do Azure AD, o navegador ou a extensão valida a URL com aquelas obtidas do registro. Se eles corresponderem, o navegador invocará o host do cliente nativo para obter um token. |
| C | O host de cliente nativo valida que as URLs pertencem aos provedores de identidade da Microsoft (conta Microsoft ou Azure AD), extrai um nonce enviado da URL e faz uma chamada para o plug-in CloudAP para obter um cookie de PRT. |
| D | O plug-in CloudAP criará o cookie PRT, entrará com a chave de sessão associada ao TPM e a enviará de volta para o host do cliente nativo. Como o cookie é assinado pela chave de sessão, ele não pode ser adulterado. |
| E | O host do cliente nativo retornará esse cookie de PRT ao navegador, que o incluirá como parte do cabeçalho de solicitação chamado x-MS-RefreshTokenCredential e solicitará tokens do Azure AD. |
| F | O Azure AD valida a assinatura de chave de sessão no cookie PRT, valida o nonce, verifica se o dispositivo é válido no locatário e emite um token de ID para a página da Web e um cookie de sessão criptografado para o navegador. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como solucionar problemas relacionados a PRT, consulte o artigo [solução de problemas de Azure Active Directory híbrida ingressado em dispositivos Windows 10 e Windows Server 2016](troubleshoot-hybrid-join-windows-current.md).
