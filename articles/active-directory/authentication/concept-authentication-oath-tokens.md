---
title: Método de autenticação de tokens OATH-Azure Active Directory
description: Saiba mais sobre como usar tokens OATH no Azure Active Directory para ajudar a melhorar e proteger eventos de entrada
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: c62b531b0d189ad698a930d65506fff0ebee3a69
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532580"
---
# <a name="authentication-methods-in-azure-active-directory---oath-tokens"></a>Métodos de autenticação em tokens Azure Active Directory OATH

O OATH TOTP (senha de uso único por tempo limitado) é um padrão aberto que especifica os códigos de OTP (senha de uso único) são gerados. O OATH TOTP pode ser implementado usando software ou hardware para gerar os códigos. O Azure AD não dá suporte a HOTP OATH, um padrão diferente de geração de código.

## <a name="oath-software-tokens"></a>Tokens de software OATH

Os tokens de software OATH normalmente são aplicativos como o Microsoft Authenticator e outros aplicativos de autenticação. O Azure AD gera a chave secreta ou semente, que é inserida no aplicativo e usada para gerar cada OTP.

O aplicativo Authenticator gera códigos automaticamente quando configurado para fazer notificações por push para que um usuário tenha um backup, mesmo que seu dispositivo não tenha conectividade. Aplicativos de terceiros que usam OATH TOTP para gerar códigos também podem ser usados.

Alguns tokens de hardware OATH TOTP são programáveis, o que significa que eles não vêm com uma chave secreta ou semente previamente programada. Esses tokens de hardware programáveis podem ser configurados usando a chave secreta ou semente obtida do fluxo de configuração do token de software. Os clientes podem comprar esses tokens do fornecedor que preferirem e usar a chave secreta ou semente no processo de configuração do fornecedor.

## <a name="oath-hardware-tokens-preview"></a>Tokens de hardware OATH (versão prévia)

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

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como configurar métodos de autenticação usando a [API REST do Microsoft Graph versão beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).
