---
title: Métodos e recursos de autenticação-Azure Active Directory
description: Saiba mais sobre os diferentes métodos e recursos de autenticação disponíveis no Azure Active Directory para ajudar a melhorar e proteger eventos de entrada
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 3947bf0dcad598bf52a742c790a2f99538d6facb
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116351"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Quais métodos de autenticação e verificação estão disponíveis no Azure Active Directory?

Como parte da experiência de entrada para contas no Azure Active Directory (AD do Azure), há diferentes maneiras pelas quais um usuário pode se autenticar. Um nome de usuário e senha é a maneira mais comum com que um usuários forneceria historicamente as credenciais. Com os recursos de autenticação e segurança modernos no Azure AD, essa senha básica pode ser complementada ou substituída por métodos de autenticação adicionais.

Um usuário no Azure AD pode optar por autenticar usando um dos seguintes métodos de autenticação:

* Nome de usuário e senha tradicionais
* Entrada sem senha do aplicativo Microsoft Authenticator
* Token de hardware OATH ou chave de segurança FIDO2
* Entrada sem senha baseada em SMS

Muitas contas no Azure AD são habilitadas para redefinição de senha de autoatendimento (SSPR) ou autenticação multifator do Azure. Esses recursos incluem métodos de verificação adicionais, como uma chamada telefônica ou perguntas de segurança. É recomendável que você exija que os usuários registrem vários métodos de verificação. Quando um método não está disponível para um usuário, ele pode optar por autenticar com outro método.

A tabela a seguir descreve quais métodos de autenticação ou verificação estão disponíveis para os diferentes cenários:

| Método | Usar ao entrar | Usar durante a verificação |
| --- | --- | --- |
| [Senha](#password) | Sim | MFA e o SSPR |
| [Aplicativo Microsoft Authenticator](#microsoft-authenticator-app) | Sim (versão prévia) | MFA e o SSPR |
| [Chaves de segurança do FIDO2 (versão prévia)](#fido2-security-keys) | Sim | Somente MFA |
| [Tokens de hardware OATH (visualização)](#oath-hardware-tokens) | Sim | SSPR e MFA |
| [sms](#phone-options) | Sim (versão prévia) | MFA e o SSPR |
| [Chamada de voz](#phone-options) | Não | MFA e o SSPR |
| [Perguntas de segurança](#security-questions) | Não | Somente SSPR |
| [Endereço de email](#email-address) | Não | Somente SSPR |
| [Senhas de aplicativo](#app-passwords) | Não | MFA somente em determinados casos |

Este artigo descreve esses diferentes métodos de autenticação e verificação disponíveis no Azure AD e quaisquer limitações ou restrições específicas.

![Métodos de autenticação em uso na tela de login](media/concept-authentication-methods/overview-login.png)

## <a name="password"></a>Senha

Uma senha do Azure AD geralmente é um dos métodos de autenticação primária. Não é possível desabilitar o método de autenticação de senha.

Mesmo que você use um método de autenticação, como o [logon baseado em SMS](howto-authentication-sms-signin.md) , quando o usuário não usar sua senha para assinar, uma senha permanecerá como um método de autenticação disponível.

## <a name="microsoft-authenticator-app"></a>Aplicativo Microsoft Authenticator

O aplicativo autenticador fornece um nível adicional de segurança à sua conta corporativa ou de estudante do Azure AD ou à sua conta Microsoft e está disponível para [Android](https://go.microsoft.com/fwlink/?linkid=866594), [Ios](https://go.microsoft.com/fwlink/?linkid=866594)e [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Com o aplicativo Microsoft Authenticator, os usuários podem autenticar de forma sem senha durante a entrada ou como uma opção de verificação adicional durante os eventos de redefinição de senha de autoatendimento (SSPR) ou autenticação multifator do Azure.

Os usuários podem receber uma notificação por meio do aplicativo móvel para que eles aprovem ou neguem ou usem o aplicativo autenticador para gerar um código de verificação OATH que pode ser inserido em uma interface de entrada. Se você habilitar um código de notificação e verificação, os usuários que registrarem o aplicativo autenticador poderão usar qualquer um dos métodos para verificar sua identidade.

Para usar o aplicativo autenticador em um prompt de entrada em vez de uma combinação de nome de usuário e senha, consulte [habilitar a entrada sem senha com o aplicativo Microsoft Authenticator (versão prévia)](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Os usuários não têm a opção de registrar seu aplicativo móvel quando eles habilitam o SSPR. Em vez disso, os usuários podem registrar seu aplicativo móvel em [https://aka.ms/mfasetup](https://aka.ms/mfasetup) ou como parte do registro de informações de segurança combinadas em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

### <a name="notification-through-mobile-app"></a>Notificação pelo aplicativo móvel

O aplicativo autenticador pode ajudar a impedir o acesso não autorizado a contas e interromper transações fraudulentas enviando uma notificação para o smartphone ou Tablet. Os usuários exibem a notificação e, se for legítima, selecione **verificar**. Caso contrário, eles poderão selecionar **negar**.

![Captura de tela de exemplo de prompt do navegador da Web para notificação do aplicativo autenticador para concluir o processo de entrada](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Se sua organização tiver funcionários trabalhando ou viajando para a China, a *notificação por meio* do método de aplicativo móvel em dispositivos Android não funcionará nesse país. Os métodos de autenticação alternativos devem ser disponibilizados para esses usuários.

### <a name="verification-code-from-mobile-app"></a>Código de verificação de aplicativo móvel

O aplicativo autenticador pode ser usado como um token de software para gerar um código de verificação OATH. Depois de inserir seu nome de usuário e senha, insira o código fornecido pelo aplicativo autenticador na interface de entrada. O código de verificação oferece uma segunda forma de autenticação.

Os usuários podem ter uma combinação de até cinco tokens de hardware OATH ou aplicativos autenticadores, como o aplicativo Microsoft Authenticator, configurado para uso a qualquer momento.

> [!WARNING]
> Para garantir o nível mais alto de segurança para redefinição de senha de autoatendimento quando apenas um método é necessário para redefinição, um código de verificação é a única opção disponível para os usuários.
>
> Quando dois métodos são necessários, os usuários podem redefinir usando um código de notificação ou de verificação Além de quaisquer outros métodos habilitados.

## <a name="fido2-security-keys"></a>Chaves de segurança do FIDO2

A Aliança do FIDO (Fast IDentity online) ajuda a promover os padrões de autenticação aberta e a reduzir o usuário de senhas como uma forma de autenticação. FIDO2 é o padrão mais recente que incorpora o padrão de autenticação na Web (webauthn).

Para usar as chaves de segurança do FIDO2 em um prompt de entrada, em vez de uma combinação de nome de usuário e senha, consulte [Habilitar entrada de chave de segurança FIDO2 sem senha (versão prévia)](howto-authentication-passwordless-security-key.md).

Os usuários podem registrar e, em seguida, selecionar uma chave de segurança FIDO2 na interface de entrada como seus principais meios de autenticação. Essas chaves de segurança FIDO2 normalmente são dispositivos USB, mas também podem usar Bluetooth ou NFC. Com um dispositivo de hardware que manipula a autenticação, a segurança de uma conta é aumentada, pois não há senha que possa ser exposta ou adivinhada.

As chaves de segurança do FIDO2 no Azure AD estão atualmente em visualização. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="oath-hardware-tokens"></a>Tokens de hardware OATH

O OATH é um padrão livre que especifica os códigos de OTP (senha única) são gerados. O Azure AD dá suporte ao uso de tokens OATH-TOTP SHA-1 da variedade de 30 segundos ou 60 segundos. Os clientes podem comprar esses tokens do fornecedor de sua escolha.

As chaves secretas são limitadas a 128 caracteres, que podem não ser compatíveis com todos os tokens. A chave secreta pode conter apenas os caracteres *de a-z* ou *a-z* e os dígitos *1-7*e deve ser codificada em *Base32*.

Tokens de hardware OATH no Azure AD estão atualmente em visualização. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![Carregando tokens OATH para a janela de tokens OATH do MFA](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Depois que os tokens são adquiridos, eles devem ser carregados em um formato de arquivo CSV (valores separados por vírgula), incluindo o UPN, o número de série, a chave secreta, o intervalo de tempo, o fabricante e o modelo, conforme mostrado no exemplo a seguir:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Certifique-se de incluir a linha de cabeçalho no arquivo CSV.

Uma vez formatado corretamente como um arquivo CSV, um administrador pode entrar no portal do Azure, navegar até **Azure Active Directory**  >  **Security**  >  **MFA**  >  **tokens OATH**do MFA de segurança e carregar o arquivo CSV resultante.

Dependendo do tamanho do arquivo CSV, ele poderá levar alguns minutos para ser processado. Selecione o botão **Atualizar** para obter o status atual. Se houver erros no arquivo, você poderá baixar um arquivo CSV que listará os erros a serem resolvidos. Os nomes de campo no arquivo CSV baixado são diferentes da versão carregada.

Depois que os erros forem resolvidos, o administrador poderá ativar cada chave selecionando **Ativar** para o token e inserindo a OTP exibida no token.

Os usuários podem ter uma combinação de até cinco tokens de hardware OATH ou aplicativos autenticadores, como o aplicativo Microsoft Authenticator, configurado para uso a qualquer momento.

## <a name="phone-options"></a>Opções de telefone

Para autenticação direta usando mensagem de texto, você pode [configurar e habilitar usuários para autenticação baseada em SMS (versão prévia)](howto-authentication-sms-signin.md). A entrada baseada em SMS é excelente para os funcionários de front-line. Com a entrada baseada em SMS, os usuários não precisam saber um nome de usuário e uma senha para acessar aplicativos e serviços. Em vez disso, o usuário insere seu número de telefone celular registrado, recebe uma mensagem de texto com um código de verificação e insere isso na interface de entrada.

Os usuários também podem verificar usando um telefone celular ou telefone comercial como forma secundária de autenticação usada durante a autenticação multifator do Azure ou a SSPR (redefinição de senha de autoatendimento).

Para funcionar corretamente, os números de telefone devem estar no formato *+ CountryCode PhoneNumber*, por exemplo, *+ 1 4251234567*.

> [!NOTE]
> Precisa haver um espaço entre o código do país e o número de telefone.
>
> A redefinição de senha não dá suporte a extensões de telefone. Mesmo no formato *+ 1 4251234567X12345* , as extensões são removidas antes de a chamada ser colocada.

### <a name="mobile-phone-verification"></a>Verificação de telefone celular

Para a autenticação multifator do Azure ou SSPR, os usuários podem optar por receber uma mensagem de texto com um código de verificação para entrar na interface de entrada ou receber uma chamada telefônica com um prompt para inserir seu código PIN definido.

Se os usuários não desejarem que seu número de telefone celular fique visível no diretório, mas quiser usá-lo para redefinição de senha, os administradores não devem preencher o número de telefone no diretório. Em vez disso, os usuários devem preencher seu atributo de **telefone de autenticação** por meio do registro de informações de segurança combinadas em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) . Os administradores poderão ver essas informações no perfil do usuário, mas elas não serão publicadas em nenhum outro lugar.

![Captura de tela da portal do Azure que mostra os métodos de autenticação com um número de telefone preenchido](media/concept-authentication-methods/user-authentication-methods.png)

A Microsoft não garante a entrega de prompt de autenticação multifator do Azure baseada em voz ou SMS consistente pelo mesmo número. Ao interesse de nossos usuários, podemos adicionar ou remover códigos curtos a qualquer momento à medida que fizermos ajustes de rota para melhorar a entrega do SMS. A Microsoft não dá suporte a códigos curtos para países/regiões além do Estados Unidos e do Canadá.

#### <a name="text-message-verification"></a>Verificação de mensagem de texto

Com a verificação de mensagem de texto durante a SSPR ou a autenticação multifator do Azure, um SMS é enviado para o número de telefone celular que contém um código de verificação. Para concluir o processo de entrada, o código de verificação fornecido é inserido na interface de entrada.

#### <a name="phone-call-verification"></a>Verificação de chamada telefônica

Com a verificação de chamada telefônica durante a SSPR ou a autenticação multifator do Azure, uma chamada de voz automatizada é feita para o número de telefone registrado pelo usuário. Para concluir o processo de entrada, é solicitado que o usuário insira o número do PIN seguido por # no respectivo teclado.

### <a name="office-phone-verification"></a>Verificação do telefone comercial

O atributo de telefone comercial é gerenciado pelo administrador do Azure AD e não pode ser registrado por um usuário.

Com a verificação de chamada telefônica durante a SSPR ou a autenticação multifator do Azure, uma chamada de voz automatizada é feita para o número de telefone registrado pelo usuário. Para concluir o processo de entrada, é solicitado que o usuário insira o número do PIN seguido por # no respectivo teclado.

### <a name="troubleshooting-phone-options"></a>Solução de problemas de opções de telefone

Se você tiver problemas com a autenticação por telefone para o Azure AD, examine as seguintes etapas de solução de problemas:

* ID de chamador bloqueada em um único dispositivo.
   * Examine os números bloqueados configurados no dispositivo.
* Número de telefone errado ou código de país incorreto ou confusão entre o número de telefone pessoal versus o número de telefone comercial.
   * Solucionar problemas do objeto de usuário e dos métodos de autenticação configurados. Verifique se os números de telefone corretos estão registrados.
* PIN digitado incorreto.
   * Confirme se o usuário usou o PIN correto como registrado para sua conta.
* Chamada encaminhada para a caixa postal.
   * Verifique se o usuário tem o telefone ativado e se o serviço está disponível em sua área ou use um método alternativo.
* Usuário está bloqueado
   * Fazer com que um administrador do Azure AD desbloqueie o usuário no portal do Azure.
* O SMS não está inscrito no dispositivo.
   * Faça com que o usuário altere os métodos ou ative o SMS no dispositivo.
* Provedores de telecomunicações com falha, como nenhuma entrada de telefone detectada, problemas de tons DTMF ausentes, ID de chamador bloqueada em vários dispositivos ou bloqueio de SMS em vários dispositivos.
   * A Microsoft usa vários provedores de telecomunicações para rotear chamadas telefônicas e mensagens SMS para autenticação. Se você vir qualquer um dos problemas acima, faça com que um usuário tente usar o método pelo menos cinco vezes em 5 minutos e tenha as informações do usuário disponíveis ao entrar em contato com o suporte da Microsoft.

## <a name="security-questions"></a>Perguntas de segurança

As perguntas de segurança não são usadas como um método de autenticação durante um evento de entrada. Em vez disso, as perguntas de segurança podem ser usadas durante o processo de redefinição de senha de autoatendimento (SSPR) para confirmar quem você é. As contas de administrador não podem usar perguntas de segurança como método de verificação com SSPR.

Quando os usuários se registram no SSPR, eles são solicitados a escolher os métodos de autenticação a serem usados. Se eles optarem por usar perguntas de segurança, eles escolherão de um conjunto de perguntas a serem solicitadas e, em seguida, fornecerão suas próprias respostas.

![Captura de tela da portal do Azure que mostra métodos de autenticação e opções para perguntas de segurança](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> As perguntas de segurança são armazenadas de forma privada e protegida em um objeto de usuário no diretório e somente podem ser respondidas pelos usuários durante o registro. Não há como um administrador ler ou modificar perguntas ou respostas de um usuário.

As perguntas de segurança podem ser menos seguras do que outros métodos porque algumas pessoas podem conhecer as respostas às perguntas de outros usuários. Se você usar perguntas de segurança com o SSPR, é recomendável usá-las em conjunto com outro método. Um usuário pode ser solicitado a usar o Microsoft Authenticator aplicativo ou a autenticação por telefone para verificar sua identidade durante o processo SSPR e escolher perguntas de segurança somente se eles não tiverem seu telefone ou dispositivo registrado com eles.

### <a name="predefined-questions"></a>Perguntas predefinidas

As seguintes perguntas de segurança predefinidas estão disponíveis para uso como um método de verificação com SSPR. Todas essas perguntas de segurança são traduzidas e localizadas no conjunto completo de idiomas do Office 365 com base na localidade do navegador do usuário:

* Em qual cidade você conheceu seu primeiro cônjuge/parceiro?
* Em qual cidade seus pais se conheceram?
* Em qual cidade seu irmão mais próximo mora?
* Em qual cidade seu pai nasceu?
* Em qual cidade você teve seu primeiro emprego?
* Em qual cidade sua mãe nasceu?
* Em qual cidade você estava no ano de 2000?
* Qual era o sobrenome de seu professor favorito no ensino médio?
* Qual é o nome de uma faculdade que você tentou entrar, mas que não frequentou?
* Qual é o nome do lugar em que você realizou sua primeira festa de casamento?
* Qual é o segundo nome de seu pai?
* Qual é sua comida favorita?
* Qual é o nome e sobrenome de sua avó materna?
* Qual é o segundo nome de sua mãe?
* Qual é o mês e ano de aniversário de seu irmão mais velho? (por exemplo, novembro de 1985)
* Qual é o segundo nome de seu irmão mais velho?
* Qual é o nome e sobrenome de seu avô paterno?
* Qual é o segundo nome de seu irmão mais novo?
* Em qual escola você concluiu a sexta série?
* Qual era o nome e sobrenome de seu melhor amigo de infância?
* Qual era o nome e sobrenome de seu primeiro parceiro?
* Qual era o sobrenome de seu professor favorito no ensino médio?
* Qual era a marca e o modelo de seu primeiro carro ou sua primeira moto?
* Qual era o nome da primeira escola em que você estudou?
* Qual é o nome do hospital em que você nasceu?
* Qual é o nome da rua da primeira casa em que morou na infância?
* Qual é o nome de seu herói de infância?
* Qual é o nome de seu animal de pelúcia preferido?
* Qual era o nome de seu primeiro animal de estimação?
* Qual era seu apelido de infância?
* Qual era seu esporte favorito no ensino médio?
* Qual foi seu primeiro emprego?
* Quais eram os últimos quatro dígitos de seu primeiro número de telefone?
* Quando criança, o que você queria ser quando crescesse?
* Quem é a pessoa mais famosa que você já conheceu?

### <a name="custom-security-questions"></a>Perguntas de segurança personalizadas

Para obter flexibilidade adicional, você pode definir suas próprias perguntas de segurança personalizadas. O tamanho máximo de uma pergunta de segurança personalizada é de 200 caracteres.

As perguntas de segurança personalizadas não são localizadas automaticamente, como as perguntas de segurança padrão. Todas as perguntas personalizadas são exibidas no mesmo idioma que são inseridas na interface do usuário administrativo, mesmo que a localidade do navegador do usuário seja diferente. Se precisar de perguntas localizadas, você deverá usar as perguntas predefinidas.

### <a name="security-question-requirements"></a>Requisitos das perguntas de segurança

Para perguntas de segurança padrão e personalizadas, os seguintes requisitos e limitações se aplicam:

* O limite mínimo para a resposta é de três caracteres.
* O limite máximo para a resposta é de 40 caracteres.
* Os usuários não podem responder à mesma pergunta mais de uma vez.
* Os usuários não podem fornecer a mesma resposta a mais de uma pergunta.
* Qualquer conjunto de caracteres pode ser usado para definir as perguntas e as respostas, incluindo caracteres Unicode.
* O número de perguntas definidas deve ser maior ou igual ao número de perguntas que foram necessárias para se registrar.

## <a name="email-address"></a>Endereço de email

Um endereço de email não pode ser usado como um método de autenticação direta. O endereço de email está disponível apenas como uma opção de verificação para redefinição de senha de autoatendimento (SSPR). Quando o endereço de email é selecionado durante a SSPR, um email é enviado para o usuário para concluir o processo de autenticação/verificação.

Durante o registro para SSPR, um usuário fornece o endereço de email a ser usado. É recomendável que eles usem uma conta de email diferente da conta corporativa para garantir que eles possam acessá-los durante a SSPR.

## <a name="app-passwords"></a>Senhas de aplicativo

Alguns aplicativos mais antigos, sem navegador, não entendem pausas ou interrupções no processo de autenticação. Se um usuário estiver habilitado para a autenticação multifator e tentar usar um desses aplicativos mais antigos e sem navegador, eles normalmente não poderão ser autenticados com êxito. Uma senha de aplicativo permite que os usuários continuem a autenticar com êxito com aplicativos antigos e sem navegador sem interrupção.

Por padrão, os usuários não podem criar senhas de aplicativo. Se você precisar permitir que os usuários criem senhas de aplicativo, selecione **permitir que os usuários criem senhas de aplicativo para entrar em aplicativos sem navegador** em *configurações de serviço* para as propriedades de autenticação multifator do Azure do usuário.

![Captura de tela da portal do Azure que mostra as configurações de serviço para autenticação multifator para permitir o usuário de senhas de aplicativo](media/concept-authentication-methods/app-password-authentication-method.png)

Se você aplicar a autenticação multifator do Azure usando políticas de acesso condicional e não por meio de MFA por usuário, não será possível criar senhas de aplicativo. Os aplicativos modernos que usam políticas de acesso condicional para controlar o acesso não precisam de senhas de aplicativo.

Se sua organização for federada para logon único (SSO) com o Azure AD e você usar a autenticação multifator do Azure, as seguintes considerações se aplicarão:

* A senha do aplicativo é verificada pelo AD do Azure; portanto, ignora a Federação. A federação é usada apenas durante a configuração de senhas de aplicativo. Para usuários federados (SSO), as senhas são armazenadas no ID organizacional. Se o usuário sair da empresa, essa informação deve fluir para o ID da organização usando o DirSync. Os eventos de desabilitação ou exclusão de conta podem levar até três horas para sincronizar, o que atrasa a desabilitação/exclusão de senhas de aplicativo no Azure AD.
* As configurações de controle de acesso do cliente local não são respeitadas por senhas de aplicativo.
* Nenhum recurso de log de autenticação local ou de auditoria está disponível para senhas de aplicativo.
* Determinados designs arquitetônicos avançados podem exigir o uso de uma combinação de nome de usuário e senhas organizacionais e senhas de aplicativo ao usar a autenticação multifator, dependendo de onde eles se autenticam.
    * Para clientes que fazem a autenticação em uma infraestrutura local, você usaria um nome de usuário e senha organizacional.
    * Para clientes que se autenticam no Azure AD, você usaria a senha de aplicativo.

## <a name="next-steps"></a>Próximas etapas

Para começar, confira o [tutorial da SSPR (redefinição de senha por autoatendimento)][tutorial-sspr] e a [Autenticação Multifator do Azure][tutorial-azure-mfa].

Para saber mais sobre os conceitos de SSPR, confira [como funciona a redefinição de senha de autoatendimento do Azure ad][concept-sspr].

Para saber mais sobre os conceitos de MFA, confira [como funciona a autenticação multifator do Azure][concept-mfa].

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md