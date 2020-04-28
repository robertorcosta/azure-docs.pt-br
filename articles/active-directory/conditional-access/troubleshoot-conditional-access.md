---
title: Solucionando problemas de conexão com acesso condicional-Azure Active Directory
description: Este artigo descreve o que fazer quando as políticas de acesso condicional resultam em resultados inesperados
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337435"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Solucionando problemas de conexão com acesso condicional

As informações neste artigo podem ser usadas para solucionar os resultados de entrada inesperados relacionados ao acesso condicional usando mensagens de erro e o log de entradas do Azure AD.

## <a name="conditional-access-sign-in-interrupt"></a>Interrupção de entrada de acesso condicional

A primeira maneira é examinar a mensagem de erro que aparece. Para problemas de entrada ao usar um navegador da Web, a página de erro em si tem informações detalhadas. Essas informações sozinhas podem descrever qual é o problema e que podem sugerir uma solução.

![Dispositivo em conformidade com erro de conexão necessário](./media/troubleshoot-conditional-access/image1.png)

No erro acima, a mensagem informa que o aplicativo só pode ser acessado por dispositivos ou aplicativos cliente que atendam à política de gerenciamento de dispositivo móvel da empresa. Nesse caso, o aplicativo e o dispositivo não atendem a essa política.

## <a name="azure-ad-sign-in-events"></a>Eventos de entrada do Azure AD

O segundo método para obter informações detalhadas sobre a interrupção de conexão é examinar os eventos de entrada do Azure AD para ver quais políticas de acesso condicional ou diretivas foram aplicadas e por quê.

Mais informações podem ser encontradas sobre o problema clicando em **mais detalhes** na página de erro inicial. Clicar em **mais detalhes** revelará informações de solução de problemas que são úteis ao pesquisar os eventos de entrada do Azure ad para o evento de falha específico que o usuário viu ou ao abrir um incidente de suporte com a Microsoft.

![Mais detalhes de uma entrada do navegador da Web interrompida por acesso condicional.](./media/troubleshoot-conditional-access/image2.png)

Para descobrir qual política de acesso condicional ou políticas são aplicadas e por que fazer o seguinte.

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou leitor global.
1. Navegue até **Azure Active Directory** > **entradas**.
1. Localize o evento para a entrada a ser revisada. Adicionar ou remover filtros e colunas para filtrar informações desnecessárias.
   1. Adicione filtros para restringir o escopo:
      1. **ID de correlação** quando você tiver um evento específico para investigar.
      1. **Acesso condicional** para ver a falha de política e o êxito. Escopo seu filtro para mostrar apenas as falhas para limitar os resultados.
      1. **Nome de usuário** para ver informações relacionadas a usuários específicos.
      1. **Data** do escopo para o intervalo de tempo em questão.

   ![Selecionando o filtro de acesso condicional no log de entradas](./media/troubleshoot-conditional-access/image3.png)

1. Depois que o evento de entrada que corresponde à falha de entrada do usuário for encontrado, selecione a guia **acesso condicional** . A guia acesso condicional mostrará a política ou políticas específicas que resultaram na interrupção de entrada.
   1. As informações na guia **solução de problemas e suporte** podem fornecer um motivo claro sobre por que uma falha de entrada, como um dispositivo que não atende aos requisitos de conformidade.
   1. Para investigar ainda mais, faça uma busca detalhada na configuração das políticas clicando no **nome da política**. Clicar no **nome da política** mostrará a interface do usuário de configuração de política para a política selecionada para revisão e edição.
   1. Os **detalhes do dispositivo** e do **usuário cliente** que foram usados para a avaliação da política de acesso condicional também estão disponíveis nas guias **informações básicas**, **localização**, **informações do dispositivo**, **detalhes de autenticação**e **detalhes adicionais** do evento de entrada.

   ![Guia acesso condicional de evento de entrada](./media/troubleshoot-conditional-access/image5.png)

Se as informações no evento não forem suficientes para entender os resultados de entrada ou ajustar a política para obter os resultados desejados, um incidente de suporte poderá ser aberto. Navegue até a guia **solução de problemas e suporte** do evento de entrada e selecione **criar uma nova solicitação de suporte**.

![A guia solução de problemas e suporte do evento de entrada](./media/troubleshoot-conditional-access/image6.png)

Ao enviar o incidente, forneça a ID da solicitação e a hora e a data do evento de entrada nos detalhes de envio do incidente. Essas informações permitirão que o suporte da Microsoft encontre o evento sobre o qual você está preocupado.

### <a name="conditional-access-error-codes"></a>Códigos de erro de acesso condicional

| Código de erro de entrada | Cadeia de Caracteres de Erro |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="next-steps"></a>Próximas etapas

- [Relatórios de atividades de entrada no portal do Azure Active Directory](../reports-monitoring/concept-sign-ins.md)
- [Solucionando problemas de acesso condicional usando a ferramenta de What If](troubleshoot-conditional-access-what-if.md)
- Práticas recomendadas para [acesso condicional no Azure Active Directory](best-practices.md)
