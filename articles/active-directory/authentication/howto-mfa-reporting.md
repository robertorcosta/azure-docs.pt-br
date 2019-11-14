---
title: Relatórios de acesso e uso para o Azure MFA-Azure Active Directory
description: Descreve como usar o recurso de relatórios da Autenticação Multifator do Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb90fafb48be1d3389597c6188b0200743f90c3e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74065967"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Relatórios na Autenticação Multifator do Azure

A Autenticação Multifator do Azure fornece vários relatórios que podem ser usados por você e sua organização, acessíveis através do portal do Azure. A tabela a seguir lista os relatórios possíveis:

| Relatório | Local padrão | DESCRIÇÃO |
|:--- |:--- |:--- |
| Histórico de usuário bloqueado | O Azure AD > Security > MFA > bloquear/desbloquear usuários | Mostra o histórico de solicitações para bloquear ou desbloquear usuários. |
| Alertas de fraudes e uso | Microsoft Azure AD > Entradas | Fornece informações sobre o uso geral, resumo do usuário e detalhes do usuário, assim como um histórico de alertas de fraude enviados durante o intervalo de datas especificado. |
| Uso para componentes locais | Relatório de atividade de > do Azure AD > Security > MFA | Fornece informações sobre o uso geral do MFA por meio da extensão do NPS, ADFS e do servidor MFA. |
| Histórico de usuário desviado | O Azure AD > Security > MFA > bypass único | Fornece um histórico de solicitações para ignorar a Autenticação Multifator para um usuário. |
| Status do servidor | Status do servidor > MFA > do Azure AD > Security | Exibe o status dos Servidores de Autenticação Multifator associado à sua conta. |

## <a name="view-mfa-reports"></a>Exibir relatórios de MFA

1. Entre no [Portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **segurança** > **MFA**.
3. Selecione o relatório que você deseja exibir.

   ![Relatório de status do servidor do MFA no portal do Azure](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Relatório de entradas do AD do Azure

Com o **relatório de atividades de logins** no [Portal do Azure](https://portal.azure.com), você pode obter as informações necessárias para determinar o desempenho do seu ambiente.

O relatório de logins pode fornecer informações sobre o uso de aplicativos gerenciados e atividades de login do usuário, o que inclui informações sobre o uso da autenticação multifator (MFA). Os dados de MFA oferecem ideias sobre como a MFA está funcionando em sua organização. Isso permite que você responda perguntas como:

- A entrada foi desafiada com a MFA?
- Como o usuário concluiu o MFA?
- Por que o usuário não conseguiu concluir a MFA?
- Quantos usuários são desafiados para MFA?
- Quantos usuários não conseguiram concluir o desafio da MFA?
- Quais são os problemas comuns de MFA que os usuários finais estão encontrando?

Esses dados estão disponíveis por meio do [Portal do Azure](https://portal.azure.com) e da [API de relatórios](../reports-monitoring/concept-reporting-api.md).

![Relatório de entradas do Azure AD no portal do Azure](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Estrutura de relatório de entradas

Os relatórios de atividade de entrada para MFA fornecem acesso às seguintes informações:

**MFA necessária:** se a MFA será necessário para a entrada ou não. A MFA pode ser necessária devido a MFA por usuário, acesso condicional ou outros motivos. Os valores possíveis são **Sim** ou **Não**.

**Resultado da MFA:** para saber mais se a MFA foi atendida ou negada:

- Se a MFA tiver sido atendida, esta coluna fornecerá mais informações sobre como a MFA foi atendida.
   - Autenticação Multifator do Azure
      - concluído na nuvem
      - expirou devido às políticas configuradas no locatário
      - registro solicitado
      - satisfeito por declaração no token
      - satisfeita por declaração fornecida pelo provedor externo
      - satisfeita por autenticação forte
      - ignorado porque o fluxo exercitado era o fluxo de logon de agente do Windows
      - ignorado devido a senha de aplicativo
      - ignorado devido ao local
      - ignorado devido a dispositivo registrado
      - ignorado devido a dispositivo lembrado
      - concluído com êxito
   - Redirecionado para um provedor externo para autenticação multifator

- Se a MFA tiver sido negada, esta coluna fornecerá o motivo da negação.
   - Autenticação Multifator do Azure negada;
      - autenticação em andamento
      - tentativa de autenticação duplicada
      - foi inserido código incorreto muitas vezes
      - autenticação inválida
      - Código de verificação do aplicativo móvel inválido
      - configuração incorreta
      - chamada telefônica caiu na caixa postal
      - o número de telefone tem um formato inválido
      - erro de serviço
      - não é possível acessar o telefone do usuário
      - não é possível enviar a notificação de aplicativo móvel no dispositivo
      - não é possível enviar a notificação de aplicativo móvel
      - usuário recusou a autenticação
      - o usuário não respondeu à notificação do aplicativo móvel
      - o usuário não tem nenhum método de verificação registrado
      - código incorreto inserido por usuário
      - PIN incorreto inserido pelo usuário
      - o usuário desligou a chamada telefônica sem obter êxito na autenticação
      - usuário está bloqueado
      - o usuário nunca inseriu o código de verificação
      - usuário não encontrado
      - código de verificação já usado uma vez

**Método de autenticação de MFA:** o método de autenticação de usuário usado para concluir a MFA. Os valores possíveis incluem:

- mensagem de texto
- Notificação de aplicativo móvel
- Chamada telefônica (telefone de autenticação)
- Código de verificação do aplicativo móvel
- Chamada telefônica (telefone comercial)
- Chamada telefônica (telefone de autenticação alternativo)

**Detalhe de autenticação MFA:** versão limpa do número de telefone, por exemplo: + X XXXXXXXX64.

**Acesso condicional** Encontre informações sobre políticas de acesso condicional que afetaram a tentativa de entrada, incluindo:

- Nome da política
- Controles de concessão
- Controles de sessão
- Resultado

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>Relatórios do PowerShell sobre usuários registrados para MFA

Primeiro, verifique se você tem o [módulo PowerShell do MSOnline v1](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) instalado.

Identifique os usuários que se registraram para MFA usando o PowerShell a seguir.

```Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifique os usuários que não se registraram para MFA usando o PowerShell a seguir.

```Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="possible-results-in-activity-reports"></a>Resultados possíveis em relatórios de atividades

A tabela a seguir pode ser usada para solucionar problemas de autenticação multifator usando a versão baixada do relatório de atividade de autenticação multifator. Eles não aparecerão diretamente no portal do Azure.

| Resultado da chamada | DESCRIÇÃO | Descrição ampla |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN inserido | O usuário inseriu um PIN.  Se a autenticação for bem-sucedida, elas entraram no PIN correto.  Se a autenticação for negada, elas inseriram um PIN incorreto ou o usuário será definido como modo padrão. |
| SUCCESS_NO_PIN | Somente # inserido | Se o usuário estiver definido como modo PIN e a autenticação for negada, isso significa que o usuário não inseriu seu PIN e apenas inseriu #.  Se o usuário estiver definido como modo padrão e a autenticação for bem sucedido, isso significa que o usuário inseriu apenas #, que é a coisa correta a fazer no modo padrão. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Não pressionado após a entrada | O usuário não enviou nenhum dígito DTMF, pois # não foi inserido.  Outros dígitos inseridos não são enviados, a menos que # seja inserido indicando a conclusão da entrada. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Nenhuma entrada de telefone-tempo limite excedido | A chamada foi respondida, mas não havia resposta.  Isso normalmente indica que a chamada foi selecionada pela caixa postal. |
| SUCCESS_PIN_EXPIRED | PIN expirado e não alterado | O PIN do usuário expirou e foi solicitado a alterá-lo, mas a alteração do PIN não foi concluída com êxito. |
| SUCCESS_USED_CACHE | Cache usado | A autenticação foi bem-sucedida sem uma chamada de autenticação multifator desde que uma autenticação bem-sucedida anterior para o mesmo nome de usuário ocorreu dentro do período de tempo de cache configurado. |
| SUCCESS_BYPASSED_AUTH | Autenticação ignorada | Êxito na autenticação usando um bypass único iniciado para o usuário.  Consulte o relatório histórico de usuário desviado para obter mais detalhes sobre o bypass. |
| SUCCESS_USED_IP_BASED_CACHE | Cache baseado em IP usado | A autenticação foi bem-sucedida sem uma chamada de autenticação multifator desde uma autenticação bem-sucedida anterior para o mesmo nome de usuário, tipo de autenticação, nome do aplicativo e IP ocorrido dentro do período de tempo de cache configurado. |
| SUCCESS_USED_APP_BASED_CACHE | Cache baseado em aplicativo usado | A autenticação foi bem-sucedida sem uma chamada de autenticação multifator desde uma autenticação bem-sucedida anterior para o mesmo nome de usuário, tipo de autenticação e nome do aplicativo dentro do período de tempo de cache configurado. |
| SUCCESS_INVALID_INPUT | Entrada de telefone inválida | A resposta enviada do telefone não é válida.  Isso pode ser de uma máquina de fax ou modem, ou o usuário pode ter inserido * como parte do PIN. |
| SUCCESS_USER_BLOCKED | O usuário está bloqueado | O número de telefone do usuário está bloqueado.  Um número bloqueado pode ser iniciado pelo usuário durante uma chamada de autenticação ou por um administrador usando o portal do Azure. <br> Observação: um número bloqueado também é um subproduto de um alerta de fraude. |
| SUCCESS_SMS_AUTHENTICATED | Mensagem de texto autenticada | Para a mensagem de teste de duas vias, o usuário respondeu corretamente com sua OTP (senha de uso único) ou OTP + PIN. |
| SUCCESS_SMS_SENT | Mensagem de texto enviada | Para mensagem de texto, a mensagem de texto que contém a senha de uso único (OTP) foi enviada com êxito.  O usuário vai inserir a OTP ou OTP + PIN no aplicativo para concluir a autenticação. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Aplicativo móvel autenticado | O usuário foi autenticado com êxito por meio do aplicativo móvel. |
| SUCCESS_OATH_CODE_PENDING | Código OATH pendente | O usuário foi solicitado a fornecer seu código OATH, mas não respondeu. |
| SUCCESS_OATH_CODE_VERIFIED | Código OATH verificado | O usuário inseriu um código OATH válido quando solicitado. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Código OATH de fallback verificado | O usuário teve a autenticação negada usando o método de autenticação multifator primário e forneceu um código OATH válido para fallback. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Perguntas de segurança de fallback respondidas | O usuário teve a autenticação negada usando seu método de autenticação multifator primário e respondeu suas perguntas de segurança corretamente para fallback. |
| FAILED_PHONE_BUSY | A autenticação já está em andamento | A autenticação multifator já está processando uma autenticação para esse usuário.  Isso geralmente é causado por clientes RADIUS que enviam várias solicitações de autenticação durante o mesmo logon. |
| CONFIG_ISSUE | Telefone inacessível | A chamada foi tentada, mas não pôde ser colocada ou não foi respondida.  Isso inclui sinal de ocupado, sinal de fast Busy (desconectado), três tons (número não mais em serviço), tempo limite atingido durante o toque, etc. |
| FAILED_INVALID_PHONENUMBER | Formato de número de telefone inválido | O número de telefone tem um formato inválido.  Os números de telefone devem ser numéricos e devem ter 10 dígitos para o código do país + 1 (Estados Unidos & Canadá). |
| FAILED_USER_HUNGUP_ON_US | O usuário desligou o telefone | O usuário respondeu ao telefone, mas, em seguida, travou sem pressionar nenhum botão. |
| FAILED_INVALID_EXTENSION | Extensão inválida | A extensão contém caracteres inválidos.  Somente dígitos, vírgulas, * e # são permitidos.  Um prefixo @ também pode ser usado. |
| FAILED_FRAUD_CODE_ENTERED | Código de fraude inserido | O usuário optou por relatar fraude durante a chamada, resultando em uma autenticação negada e um número de telefone bloqueado.| 
| FAILED_SERVER_ERROR | Não é possível fazer a chamada | O serviço de autenticação multifator não pôde fazer a chamada. |
| FAILED_SMS_NOT_SENT | Não foi possível enviar a mensagem de texto | Não foi possível enviar a mensagem de texto.  A autenticação foi negada. |
| FAILED_SMS_OTP_INCORRECT | OTP de mensagem de texto incorreto | O usuário inseriu uma senha de uso único (OTP) incorreta da mensagem de texto recebida.  A autenticação foi negada. |
| FAILED_SMS_OTP_PIN_INCORRECT | OTP de mensagem de texto + PIN incorreto | O usuário inseriu uma senha de uso único (OTP) incorreta e/ou um PIN de usuário incorreto.  A autenticação foi negada. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Máximo de tentativas de OTP de mensagem de texto excedido | O usuário excedeu o número máximo de tentativas de OTP (senha de uso único). |
| FAILED_PHONE_APP_DENIED | Aplicativo móvel negado | O usuário negou a autenticação no aplicativo móvel pressionando o botão negar. |
| FAILED_PHONE_APP_INVALID_PIN | PIN inválido do aplicativo móvel | O usuário inseriu um PIN inválido ao autenticar no aplicativo móvel. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | PIN do aplicativo móvel não alterado | O usuário não concluiu com êxito uma alteração de PIN necessária no aplicativo móvel. |
| FAILED_FRAUD_REPORTED | Fraude relatada | O usuário relatou fraude no aplicativo móvel. |
| FAILED_PHONE_APP_NO_RESPONSE | Sem resposta do aplicativo móvel | O usuário não respondeu à solicitação de autenticação do aplicativo móvel. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Aplicativo móvel todos os dispositivos bloqueados | Os dispositivos de aplicativo móvel para este usuário não estão mais respondendo a notificações e foram bloqueados. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Falha na notificação do aplicativo móvel | Ocorreu uma falha ao tentar enviar uma notificação para o aplicativo móvel no dispositivo do usuário. |
| FAILED_PHONE_APP_INVALID_RESULT | Resultado inválido do aplicativo móvel | O aplicativo móvel retornou um resultado inválido. |
| FAILED_OATH_CODE_INCORRECT | Código OATH incorreto | O usuário inseriu um código OATH incorreto.  A autenticação foi negada. |
| FAILED_OATH_CODE_PIN_INCORRECT | Código OATH + PIN incorreto | O usuário inseriu um código OATH incorreto e/ou um PIN de usuário incorreto.  A autenticação foi negada. |
| FAILED_OATH_CODE_DUPLICATE | Código OATH duplicado | O usuário inseriu um código OATH que foi usado anteriormente.  A autenticação foi negada. |
| FAILED_OATH_CODE_OLD | Código OATH desatualizado | O usuário inseriu um código OATH que precede um código OATH que foi usado anteriormente.  A autenticação foi negada. |
| FAILED_OATH_TOKEN_TIMEOUT | Tempo limite do resultado do código OATH | O usuário demorou muito para inserir o código OATH e a tentativa de autenticação multifator já tinha atingido o tempo limite. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Tempo limite do resultado de perguntas de segurança | O usuário demorou muito para inserir a resposta a perguntas de segurança e a tentativa de autenticação multifator já tinha atingido o tempo limite. |
| FAILED_AUTH_RESULT_TIMEOUT | Tempo limite de resultado de autenticação | O usuário demorou muito para concluir a tentativa de autenticação multifator. |
| FAILED_AUTHENTICATION_THROTTLED | Autenticação limitada | A tentativa de autenticação multifator foi limitada pelo serviço. |

## <a name="next-steps"></a>Próximas etapas

* [Relatórios de uso e informações do SSPR e MFA](howto-authentication-methods-usage-insights.md)
* [Para usuários](../user-help/multi-factor-authentication-end-user.md)
* [Onde implantar](concept-mfa-whichversion.md)
