---
title: Métodos e recursos de autenticação – Azure Active Directory
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
ms.openlocfilehash: 642f2705f54fe8f84cfde7ff039c9a723be59595
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770952"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Quais métodos de autenticação e verificação estão disponíveis no Azure Active Directory?

Como parte da experiência de entrada para contas no Azure Active Directory (Azure AD), há diferentes maneiras pelas quais um usuário pode se autenticar. Um nome de usuário e uma senha é a maneira mais comum, historicamente, de um usuário fornecer credenciais. Com os recursos modernos de autenticação e segurança no Azure AD, essa senha básica pode ser complementada ou substituída por métodos de autenticação adicionais.

Um usuário no Azure AD pode optar por autenticar usando um dos seguintes métodos de autenticação:

* Nome de usuário e senha tradicionais
* Entrada sem senha do aplicativo Microsoft Authenticator
* Token de hardware OATH ou chave de segurança FIDO2
* Entrada sem senha baseada em SMS

Muitas contas no Azure AD são habilitadas para SSPR (redefinição de senha self-service) ou Autenticação Multifator do Azure. Esses recursos incluem métodos de verificação adicional, como uma chamada telefônica ou perguntas de segurança. É recomendável exigir que os usuários registrem vários métodos de verificação. Quando um método não está disponível para um usuário, ele pode optar por autenticar com outro método.

A seguinte tabela descreve quais métodos estão disponíveis para autenticação primária ou secundária:

| Método | Autenticação primária | Autenticação secundária |
| --- | --- | --- |
| [Senha](#password) | Sim | |
| [Aplicativo Microsoft Authenticator](#microsoft-authenticator-app) | Sim (versão prévia) | MFA e o SSPR |
| [Chaves de segurança FIDO2 (versão prévia)](#fido2-security-keys) | Sim | Somente MFA |
| [Tokens de software OATH](#oath-software-tokens) | Não | MFA |
| [Tokens de hardware OATH (versão prévia)](#oath-hardware-tokens-preview) | Sim | MFA |
| [SMS](#phone-options) | Sim (versão prévia) | MFA e o SSPR |
| [Chamada de voz](#phone-options) | Não | MFA e o SSPR |
| [Perguntas de segurança](#security-questions) | Não | Somente SSPR |
| [Endereço de email](#email-address) | Não | Somente SSPR |
| [Senhas de aplicativo](#app-passwords) | Não | MFA somente em determinados casos |

Este artigo descreve esses diferentes métodos de autenticação e verificação disponíveis no Azure AD e as limitações ou restrições específicas.

![Métodos de autenticação em uso na tela de login](media/concept-authentication-methods/overview-login.png)

## <a name="password"></a>Senha

Uma senha do Azure AD geralmente é um dos métodos de autenticação primária. Não é possível desabilitar o método de autenticação de senha.

Mesmo que você use um método de autenticação como [entrada baseada em SMS](howto-authentication-sms-signin.md) quando o usuário não usa senha para entrar, a senha permanece como um método de autenticação disponível.

## <a name="microsoft-authenticator-app"></a>Aplicativo Microsoft Authenticator

O aplicativo Authenticator fornece um nível adicional de segurança para sua conta corporativa ou de estudante do Azure AD ou sua conta Microsoft e está disponível para [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) e [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Com o aplicativo Microsoft Authenticator, os usuários podem autenticar sem senha durante a entrada ou como uma opção de verificação adicional durante os eventos de SSPR (redefinição de senha self-service) ou Autenticação Multifator do Azure.

Os usuários podem receber uma notificação por meio do aplicativo móvel para que eles aprovem ou neguem a entrada ou podem usar o aplicativo Authenticator para gerar um código de verificação OATH que pode ser inserido em uma interface de entrada. Se você habilitar um código de notificação e verificação, os usuários que registrarem o aplicativo Authenticator poderão usar qualquer um dos métodos para confirmar a identidade.

Para usar o aplicativo Authenticator em um prompt de entrada em vez de uma combinação de nome de usuário e senha, confira [Habilitar a entrada sem senha com o aplicativo Microsoft Authenticator (versão prévia)](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Os usuários não têm a opção de registrar o aplicativo móvel quando habilitam a SSPR. Em vez disso, eles podem fazer esse registro em [https://aka.ms/mfasetup](https://aka.ms/mfasetup) ou como parte do registro combinado de informações de segurança em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

### <a name="notification-through-mobile-app"></a>Notificação pelo aplicativo móvel

O aplicativo Authenticator pode ajudar a impedir o acesso não autorizado a contas e interromper transações fraudulentas enviando uma notificação para seu smartphone ou tablet. Os usuários visualizam a notificação e, se for legítima, selecionam **Confirmar**. Caso contrário, eles podem selecionar **Negar**.

![Captura de tela com um exemplo de prompt do navegador da Web para notificação do aplicativo Authenticator para concluir o processo de entrada](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Caso sua organização tenha funcionários trabalhando ou viajando para a China, a *Notificação por aplicativo móvel* em dispositivos Android não funciona nesse país/região. Métodos alternativos de autenticação devem ser disponibilizados para esses usuários.

### <a name="verification-code-from-mobile-app"></a>Código de verificação de aplicativo móvel

O aplicativo Authenticator pode ser usado como um token de software para gerar um código de verificação OATH. Depois de inserir seu nome de usuário e sua senha, insira o código fornecido pelo aplicativo Authenticator na interface de login. O código de verificação oferece uma segunda forma de autenticação.

Os usuários podem ter uma combinação de até cinco tokens de hardware OATH ou aplicativos de autenticação, como o Microsoft Authenticator, configurados para uso a qualquer momento.

> [!WARNING]
> A fim de garantir o mais alto nível de segurança para a redefinição de senha self-service quando apenas um método é necessário para redefinir, o código de verificação é a única opção disponível para os usuários.
>
> Quando dois métodos forem necessários, os usuários poderão redefinir usando uma notificação ou um código de verificação, além de qualquer outro método habilitado.

## <a name="fido2-security-keys"></a>Chaves de segurança FIDO2

A FIDO (Fast IDentity online) Alliance ajuda a promover padrões de autenticação aberta e a reduzir o uso de senhas como forma de autenticação. FIDO2 é o padrão mais recente que incorpora o padrão de autenticação na Web (WebAuthn).

Para usar as chaves de segurança do FIDO2 em um prompt de entrada em vez de uma combinação de nome de usuário e senha, confira [Habilitar entrada de chave de segurança de FIDO2 sem senha (versão prévia)](howto-authentication-passwordless-security-key.md).

Os usuários podem registrar e, em seguida, selecionar uma chave de segurança FIDO2 na interface de entrada como o principal meio de autenticação. Essas chaves de segurança FIDO2 normalmente são dispositivos USB, mas também podem usar Bluetooth ou NFC. Com um dispositivo de hardware que manipula a autenticação, a segurança de uma conta é aumentada, pois não há senha que possa ser exposta ou adivinhada.

As chaves de segurança do FIDO2 no Azure AD estão atualmente em versão prévia. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="oath-tokens"></a>Tokens OATH

O OATH TOTP (senha de uso único por tempo limitado) é um padrão aberto que especifica os códigos de OTP (senha de uso único) são gerados. O OATH TOTP pode ser implementado usando software ou hardware para gerar os códigos. O Azure AD não dá suporte a HOTP OATH, um padrão diferente de geração de código.

### <a name="oath-software-tokens"></a>Tokens de software OATH

Os tokens de software OATH normalmente são aplicativos como o Microsoft Authenticator e outros aplicativos de autenticação. O Azure AD gera a chave secreta ou semente, que é inserida no aplicativo e usada para gerar cada OTP.

O aplicativo Authenticator gera códigos automaticamente quando configurado para fazer notificações por push para que um usuário tenha um backup, mesmo que seu dispositivo não tenha conectividade. Aplicativos de terceiros que usam OATH TOTP para gerar códigos também podem ser usados.

Alguns tokens de hardware OATH TOTP são programáveis, o que significa que eles não vêm com uma chave secreta ou semente previamente programada. Esses tokens de hardware programáveis podem ser configurados usando a chave secreta ou semente obtida do fluxo de configuração do token de software. Os clientes podem comprar esses tokens do fornecedor que preferirem e usar a chave secreta ou semente no processo de configuração do fornecedor.

### <a name="oath-hardware-tokens-preview"></a>Tokens de hardware OATH (versão prévia)

O Azure AD dá suporte ao uso de tokens OATH-TOTP SHA-1 que atualizam códigos a cada 30 ou 60 segundos. Os clientes podem adquirir esses tokens do fornecedor que preferirem.

Os tokens de hardware OATH TOTP normalmente vêm com uma chave secreta ou semente previamente programada no token. Essas chaves devem ser inseridas no Azure AD, conforme descrito nas etapas a seguir. As chaves secretas estão limitadas a 128 caracteres, que podem não ser compatíveis com todos os tokens. A chave secreta só pode conter os caracteres *a-z* ou *A-Z* e dígitos *1-7* e deve ser codificada em *Base32*.

Os tokens de hardware OATH TOTP programáveis que podem ser propagados também podem ser configurados com o Azure AD no fluxo de instalação do token de software.

Os tokens de hardware OATH agora são compatíveis como parte de uma versão prévia pública. Para obter mais informações sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

![Carregar tokens OATH para a folha de tokens OATH da MFA](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Depois que os tokens são adquiridos, eles precisam ser carregados em um arquivo com formato CSV (valores separados por vírgulas) que inclua UPN, número de série, chave secreta, intervalo de tempo, fabricante e modelo, como mostrado no seguinte exemplo:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Verifique se você incluiu a linha de cabeçalho no arquivo CSV.

Após a formatação correta com um arquivo CSV, um administrador pode entrar no portal do Azure, navegar para **Azure Active Directory > Segurança > MFA > Tokens OATH** e carregar o arquivo CSV resultante.

Dependendo do tamanho do arquivo CSV, ele poderá levar alguns minutos para ser processado. Selecione o botão **Atualizar** para obter o status atual. Se houver erros no arquivo, você poderá baixar um arquivo CSV que lista os erros para que você possa resolvê-los. Os nomes de campo no arquivo CSV baixado são diferentes da versão carregada.

Depois que os erros forem resolvidos, o administrador poderá ativar cada chave selecionando **Ativar** para o token e inserindo a OTP exibido no token.

Os usuários podem ter uma combinação de até cinco tokens de hardware OATH ou aplicativos de autenticação, como o Microsoft Authenticator, configurados para uso a qualquer momento.

## <a name="phone-options"></a>Opções por telefone

Para autenticação direta usando mensagem de texto, você pode [Configurar e habilitar usuários para a autenticação baseada em SMS (versão prévia)](howto-authentication-sms-signin.md). A entrada baseada em SMS é ótima para os trabalhadores de linha de frente. Com a entrada baseada em SMS, os usuários não precisam saber um nome de usuário e uma senha para acessar aplicativos e serviços. Em vez disso, o usuário insere seu número de telefone celular registrado, recebe uma mensagem de texto com um código de verificação e insere isso na interface de entrada.

Os usuários também podem verificar usando um celular ou telefone comercial como forma secundária de autenticação usada durante a Autenticação Multifator do Azure ou a SSPR (redefinição de senha self-service).

Para funcionarem adequadamente, os números de telefone devem estar no formato *+CountryCode PhoneNumber*, por exemplo: *+1 4251234567*.

> [!NOTE]
> Precisa haver um espaço entre o código do país/região e o número de telefone.
>
> A redefinição de senha não dá suporte a ramais telefônicos. Mesmo no formato *+1 4251234567X12345*, as extensões são removidas antes que a chamada seja completada.

### <a name="mobile-phone-verification"></a>Verificação por celular

Para a Autenticação Multifator do Azure ou SSPR, os usuários podem optar por receber uma mensagem de texto com um código de verificação para entrar na interface de entrada ou receber uma chamada telefônica com um prompt para inserir o código PIN definido.

Se os usuários não quiserem que o número do telefone celular seja visível no diretório, mas ainda quiserem usá-lo para redefinição de senha, os administradores não deverão preencher o número de telefone no diretório. Em vez disso, os usuários devem preencher o atributo **Telefone de Autenticação** por meio do registro combinado das informações de segurança em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo). Os administradores poderão ver essas informações no perfil do usuário, mas elas não serão publicadas em nenhum outro lugar.

![Captura de tela do portal do Azure que mostra os métodos de autenticação com um número de telefone preenchido](media/concept-authentication-methods/user-authentication-methods.png)

A Microsoft não garante a entrega de prompt consistente da Autenticação Multifator do Azure com base em voz ou SMS pelo mesmo número. Pensando no melhor para nossos usuários, nós podemos adicionar ou remover códigos curtos a qualquer momento, pois fazemos ajustes de rota para melhorar a capacidade de entrega de SMS. A Microsoft não dá suporte a códigos curtos para países/regiões além dos Estados Unidos e do Canadá.

#### <a name="text-message-verification"></a>Verificação por mensagem de texto

Com a verificação por mensagem de texto durante a SSPR ou Autenticação Multifator do Azure, um SMS com um código de verificação é enviado para o número do telefone celular. Para concluir o processo de entrada, o código de verificação fornecido é inserido na interface de entrada.

#### <a name="phone-call-verification"></a>Verificação por chamada telefônica

Com a verificação por chamada telefônica durante a SSPR ou a Autenticação Multifator do Azure, uma chamada de voz automatizada é feita para o número de telefone registrado pelo usuário. Para concluir o processo de entrada, o usuário deve inserir o número do PIN seguido por # no teclado.

### <a name="office-phone-verification"></a>Verificação por telefone comercial

O atributo de telefone comercial é gerenciado pelo administrador do Azure AD e não pode ser registrado pelo próprio usuário.

Com a verificação por chamada telefônica durante a SSPR ou a Autenticação Multifator do Azure, uma chamada de voz automatizada é feita para o número de telefone registrado pelo usuário. Para concluir o processo de entrada, o usuário deve inserir o número do PIN seguido por # no teclado.

### <a name="troubleshooting-phone-options"></a>Solucionar problemas das opções por telefone

Se você tiver problemas com a autenticação por telefone do Azure AD, examine as seguintes etapas de solução de problemas:

* ID de chamador bloqueada em um único dispositivo.
   * Examine os números bloqueados configurados no dispositivo.
* Número de telefone errado, código de país/região incorreto ou confusão entre número de telefone pessoal e o número de telefone comercial.
   * Solucione os problemas com o objeto do usuário e os métodos de autenticação configurados. Verifique se os números de telefone corretos estão registrados.
* PIN incorreto inserido.
   * Confirme se o usuário usou o PIN correto registrado para a conta.
* Chamada encaminhada para a caixa postal.
   * Verifique se o telefone do usuário está ativado e se o serviço está disponível na área dele ou use um método alternativo.
* Usuário está bloqueado
   * Peça para o administrador do Azure AD desbloquear o usuário no portal do Azure.
* Não há assinatura para SMS no dispositivo.
   * Peça para o usuário alterar os métodos ou ativar o SMS no dispositivo.
* Provedores de telecomunicações com falha, por exemplo, nenhuma entrada de telefone detectada, problemas de tons DTMF ausentes, ID de chamador bloqueada em vários dispositivos ou bloqueio de SMS em vários dispositivos.
   * A Microsoft usa vários provedores de telecomunicações a fim de rotear chamadas telefônicas e mensagens SMS para autenticação. Se você se deparar com qualquer um dos problemas acima, peça para um usuário tentar usar o método pelo menos cinco vezes em cinco minutos e tenha as informações do usuário disponíveis ao entrar em contato com o suporte da Microsoft.

## <a name="security-questions"></a>Perguntas de segurança

Perguntas de segurança não são usadas como um método de autenticação durante um evento de entrada. Em vez disso, elas podem ser usadas durante o processo de SSPR (redefinição de senha self-service) para confirmar quem você é. As contas de administrador não podem usar perguntas de segurança como método de verificação com SSPR.

Quando os usuários se registram na SSPR, eles devem escolher os métodos de autenticação a serem usados. Se eles optarem por perguntas de segurança, escolherão um conjunto de perguntas a serem feitas e, em seguida, fornecerão as respostas.

![Captura de tela do portal do Azure que mostra os métodos de autenticação e as opções para perguntas de segurança](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> As perguntas de segurança são armazenadas de forma privada e protegida em um objeto de usuário no diretório e somente podem ser respondidas pelos usuários durante o registro. Não há nenhuma maneira de um administrador ler ou modificar as perguntas ou respostas de um usuário.

As perguntas de segurança podem ser menos seguras do que outros métodos porque algumas pessoas podem conhecer as respostas às perguntas de outros usuários. Caso você use perguntas de segurança com SSPR, é recomendável fazer isso em conjunto com outro método. Um usuário pode ser solicitado a usar o aplicativo Microsoft Authenticator ou a autenticação por telefone para confirmar a identidade durante o processo de SSPR e escolher perguntas de segurança somente se não tiver acesso ao telefone ou dispositivo registrado.

### <a name="predefined-questions"></a>Perguntas predefinidas

As perguntas de segurança predefinidas a seguir estão disponíveis para uso como método de verificação com SSPR. Todas estas perguntas de segurança são traduzidas e localizadas para o conjunto completo de idiomas do Office 365 com base na localidade do navegador do usuário:

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

Para obter mais flexibilidade, você poderá definir perguntas de segurança personalizadas. O tamanho máximo de uma pergunta de segurança personalizada é de 200 caracteres.

As perguntas de segurança personalizadas não são localizadas automaticamente, como as perguntas de segurança padrão. Todas as perguntas personalizadas são exibidas no mesmo idioma em que são inseridas na interface do usuário administrativa, mesmo se a localidade do navegador do usuário for diferente. Se precisar de perguntas localizadas, você deverá usar as perguntas predefinidas.

### <a name="security-question-requirements"></a>Requisitos das perguntas de segurança

Para perguntas de segurança padrão e personalizadas, os seguintes requisitos e limitações se aplicam:

* O limite mínimo para a resposta é de três caracteres.
* O limite máximo para a resposta é de 40 caracteres.
* Os usuários não podem responder à mesma pergunta mais de uma vez.
* Os usuários não podem fornecer a mesma resposta a mais de uma pergunta.
* Qualquer conjunto de caracteres pode ser usado para definir as perguntas e as respostas, incluindo caracteres Unicode.
* O número de perguntas definidas deve ser maior ou igual ao número de perguntas que foram necessárias para se registrar.

## <a name="email-address"></a>Endereço de email

Um endereço de email não pode ser usado como um método de autenticação direta. O endereço de email está disponível apenas como uma opção de verificação para SSPR (redefinição de senha self-service). Quando o endereço de email é selecionado durante a SSPR, um email é enviado ao usuário para concluir o processo de autenticação/verificação.

Durante o registro na SSPR, um usuário fornece o endereço de email a ser usado. É recomendável que eles usem uma conta de email diferente da conta corporativa para poderem acessá-la durante a SSPR.

## <a name="app-passwords"></a>Senhas de aplicativo

Alguns aplicativos mais antigos e sem navegador não entendem pausas ou interrupções no processo de autenticação. Se um usuário estiver habilitado para a autenticação multifator e tentar usar um desses aplicativos, normalmente não poderá ser autenticado com êxito. Uma senha de aplicativo possibilita que os usuários continuem a ter uma autenticação bem-sucedida com aplicativos antigos e sem navegador sem interrupção.

Por padrão, os usuários não podem criar senhas de aplicativo. Se você precisar permitir que os usuários criem senhas de aplicativo, selecione a opção **Permitir que os usuários criem senhas de aplicativo para entrar em aplicativos sem navegador** em *Configurações do serviço* nas propriedades da Autenticação Multifator do Azure do usuário.

![Captura de tela do portal do Azure que mostra as configurações do serviço da autenticação multifator para permitir o usuário de senhas de aplicativo](media/concept-authentication-methods/app-password-authentication-method.png)

Caso você imponha a Autenticação Multifator do Microsoft Azure usando políticas de Acesso condicional e não por meio de MFA por usuário, não será possível criar senhas de aplicativo. Aplicativos modernos que usam políticas de Acesso condicional para controlar o acesso não precisam de senhas de aplicativo.

Caso sua organização seja federada para SSO (logon único) com o Azure AD e você use a Autenticação Multifator do Azure, o seguinte se aplica:

* A senha de aplicativo é verificada pelo Azure AD e, portanto, ignora a federação. A federação é usada apenas durante a configuração de senhas de aplicativo. Para usuários federados (SSO), as senhas são armazenadas no ID organizacional. Se o usuário sair da empresa, essa informação deve fluir para o ID da organização usando o DirSync. A desabilitação ou exclusão da conta pode levar até três horas para ser sincronizada, atrasando a desabilitação/exclusão de senhas de aplicativo no Azure AD.
* As configurações do Controle de Acesso do Cliente local não são consideradas pelas senhas de aplicativo.
* Nenhum recurso de registro ou auditoria de autenticação local está disponível para senhas de aplicativo.
* Alguns designs arquitetônicos avançados podem exigir o uso de uma combinação de nome de usuário e senhas da organização e senhas de aplicativo ao usar a autenticação multifator, dependendo de onde seja feita a autenticação.
    * Para clientes que fazem a autenticação em uma infraestrutura local, você usaria um nome de usuário e senha organizacional.
    * Para clientes que se autenticam no Azure AD, você usaria a senha de aplicativo.

## <a name="next-steps"></a>Próximas etapas

Para começar, confira o [tutorial da SSPR (redefinição de senha por autoatendimento)][tutorial-sspr] e a [Autenticação Multifator do Azure][tutorial-azure-mfa].

Para saber mais sobre os conceitos de SSPR, confira [Como funciona a redefinição de senha self-service do Azure AD][concept-sspr].

Para saber mais sobre conceitos de MFA, confira [Como funciona como a Autenticação Multifator do Azure][concept-mfa].

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md