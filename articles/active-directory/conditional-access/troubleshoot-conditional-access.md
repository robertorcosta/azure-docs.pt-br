---
title: Problemas de solucionar problemas de login com o Conditional Access - Azure Active Directory
description: Este artigo descreve o que fazer quando suas políticas de acesso condicional resultam em resultados inesperados
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 03/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf1daca79f3ed20d9b7a89af20d74ff5f3148b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337435"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Solução de problemas de login com acesso condicional

As informações deste artigo podem ser usadas para solucionar resultados inesperados de login relacionados ao Acesso Condicional usando mensagens de erro e logins do Azure AD.

## <a name="conditional-access-sign-in-interrupt"></a>Interrupção do login do Acesso Condicional

A primeira maneira é rever a mensagem de erro que aparece. Para problemas de login ao usar um navegador da Web, a própria página de erro tem informações detalhadas. Essas informações por si só podem descrever qual é o problema e isso pode sugerir uma solução.

![Assinar erro - dispositivo compatível necessário](./media/troubleshoot-conditional-access/image1.png)

No erro acima, a mensagem afirma que o aplicativo só pode ser acessado a partir de dispositivos ou aplicativos clientes que atendam à política de gerenciamento de dispositivos móveis da empresa. Neste caso, o aplicativo e o dispositivo não cumprem essa política.

## <a name="azure-ad-sign-in-events"></a>Eventos de login do Azure AD

O segundo método para obter informações detalhadas sobre a interrupção do login é revisar os eventos de login do Azure AD para ver quais políticas ou políticas de Acesso Condicional foram aplicadas e por quê.

Mais informações podem ser encontradas sobre o problema clicando em **Mais detalhes** na página inicial de erro. Clicar em **mais detalhes** revelará informações de solução de problemas que são úteis ao pesquisar os eventos de login do Azure AD para o evento de falha específico que o usuário viu ou ao abrir um incidente de suporte com a Microsoft.

![Mais detalhes de um acesso condicional interromperam o login do navegador da Web.](./media/troubleshoot-conditional-access/image2.png)

Para descobrir qual política ou políticas de Acesso Condicional foram aplicadas e por que fazer o seguinte.

1. Faça login no **portal Azure** como administrador global, administrador de segurança ou leitor global.
1. Navegue até os > **logins**do **Diretório Ativo do Azure**.
1. Encontre o evento para o login para revisar. Adicione ou remova filtros e colunas para filtrar informações desnecessárias.
   1. Adicionar filtros para estreitar o escopo:
      1. **ID de correlação** quando você tem um evento específico para investigar.
      1. **Acesso condicional** para ver falha e sucesso da política. Escopo seu filtro para mostrar apenas falhas para limitar resultados.
      1. **Nome de usuário** para ver informações relacionadas a usuários específicos.
      1. **Data** escopo do prazo em questão.

   ![Selecionando o filtro de acesso condicional no registro de logins](./media/troubleshoot-conditional-access/image3.png)

1. Uma vez que o evento de login correspondente à falha de login do usuário tenha sido encontrado, selecione a guia **Acesso Condicional.** A guia Acesso Condicional mostrará a política ou as políticas específicas que resultaram na interrupção do login.
   1. As informações na guia **Solução de Problemas e suporte** podem fornecer uma razão clara para a falha de um login, como um dispositivo que não atendia aos requisitos de conformidade.
   1. Para investigar mais, aprofunde a configuração das políticas clicando no nome da **diretiva**. Clicar no Nome da **diretiva** mostrará a interface de usuário de configuração de diretiva para a diretiva selecionada para revisão e edição.
   1. Os **detalhes** **do usuário cliente** e do dispositivo que foram usados para a avaliação da política de acesso condicional também estão disponíveis nas guias **Informações Básicas,** **Localização,** **Informações do Dispositivo,** **Detalhes de Autenticação**e **Detalhes Adicionais** do evento de login.

   ![Inscreva-se no evento Guia de Acesso Condicional](./media/troubleshoot-conditional-access/image5.png)

Se as informações no evento não forem suficientes para entender os resultados do login ou ajustar a política para obter os resultados desejados, então um incidente de suporte pode ser aberto. Navegue até a guia solução de **problemas e suporte** do evento de login e selecione **Criar uma nova solicitação de suporte**.

![A guia Solução de problemas e suporte do evento De inscrição](./media/troubleshoot-conditional-access/image6.png)

Ao enviar o incidente, forneça o ID de solicitação e a hora e a data do evento de login nos detalhes de apresentação do incidente. Essas informações permitirão que o suporte da Microsoft encontre o evento com o qual você está preocupado.

### <a name="conditional-access-error-codes"></a>Códigos de erro de acesso condicional

| Código de erro de login | Cadeia de Caracteres de Erro |
| --- | --- |
| 53000 | DispositivoNãoCompatível |
| 53001 | DispositivoNotDomainJoined |
| 53002 | AplicativoUsedIsnotanApprovedApp |
| 53003 | BloqueadoByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="next-steps"></a>Próximas etapas

- [Relatórios de atividades de entrada no portal do Azure Active Directory](../reports-monitoring/concept-sign-ins.md)
- [Solução de problemas Acesso Condicional usando a ferramenta E se](troubleshoot-conditional-access-what-if.md)
- Melhores práticas para [acesso condicional no Diretório Ativo do Azure](best-practices.md)
