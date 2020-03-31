---
title: Modo de dispositivo compartilhado para dispositivos Android | Azure
description: Conheça o modo dispositivo compartilhado, que permite que os trabalhadores de primeira linha compartilhem um dispositivo Android
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: marsma
ms.reviwer: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 9928b64d286cc5072f28f7cc17e4af3e95662cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085624"
---
# <a name="shared-device-mode-for-android-devices"></a>Modo de dispositivo compartilhado para dispositivos Android

> [!NOTE]
> Esse recurso está em uma versão prévia.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Trabalhadores de primeira linha, como associados de varejo, membros da tripulação de voo e trabalhadores de serviço de campo, muitas vezes usam um dispositivo móvel compartilhado para fazer seu trabalho. Isso se torna problemático quando eles começam a compartilhar senhas ou números de pinos para acessar dados de clientes e negócios no dispositivo compartilhado.

O modo dispositivo compartilhado permite configurar um dispositivo Android para que ele possa ser facilmente compartilhado por vários funcionários. Os funcionários podem fazer login e acessar as informações dos clientes rapidamente. Quando terminarem seu turno ou tarefa, eles podem sair do dispositivo e ele estará imediatamente pronto para o próximo funcionário usar.

O modo dispositivo compartilhado também fornece o gerenciamento lastreado em identidade da Microsoft do dispositivo.

Para criar um aplicativo de modo de dispositivo compartilhado, desenvolvedores e admins de dispositivos em nuvem trabalham juntos:

- Os desenvolvedores escrevem um aplicativo de conta única (aplicativos de várias `"shared_device_mode_supported": true` contas não são suportados no modo de dispositivo compartilhado), adicionam à configuração do aplicativo e escrevem código para lidar com coisas como a saída compartilhada do dispositivo.
- Os admins do dispositivo preparam o dispositivo para ser compartilhado instalando o aplicativo autenticador e configurando o dispositivo para o modo compartilhado usando o aplicativo autenticador. Apenas os usuários que estão na função [administrador de dispositivos](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#cloud-device-administrator) em nuvem podem colocar um dispositivo no modo compartilhado usando o aplicativo [Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview). Você pode configurar a adesão de suas funções organizacionais no portal Azure através de: **Azure Active Directory** > **Roles and Administrators** > **Cloud Device Administrator**.

 Este artigo foca principalmente no que os desenvolvedores devem pensar.

## <a name="single-vs-multiple-account-applications"></a>Aplicativos de conta única versus várias conta

Os aplicativos escritos usando o Microsoft Authentication Library SDK (MSAL) podem gerenciar uma única conta ou várias contas. Para obter detalhes, consulte [o modo de conta única ou o modo de várias contas.](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account) Os recursos da plataforma de identidade da Microsoft disponíveis para o aplicativo variam dependendo se o aplicativo está sendo executado no modo de conta única ou no modo de várias contas.

**Aplicativos de modo de dispositivo compartilhado só funcionam no modo de conta única**.

> [!IMPORTANT]
> Aplicativos que só suportam o modo de várias contas não podem ser executados em um dispositivo compartilhado. Se um funcionário carregar um aplicativo que não suporta o modo de conta única, ele não será executado no dispositivo compartilhado.
>
> Os aplicativos escritos antes do MSAL SDK ser lançado são executados no modo de várias contas e devem ser atualizados para suportar o modo de conta única antes que eles possam ser executados em um dispositivo de modo compartilhado.

**Suporte a contas únicas e várias contas**

Seu aplicativo pode ser construído para suportar a execução em dispositivos pessoais e dispositivos compartilhados. Se o aplicativo atualmente suportar várias contas e você quiser suportar o modo de dispositivo compartilhado, adicione suporte para o modo de conta única.

Você também pode querer que seu aplicativo mude seu comportamento dependendo do tipo de dispositivo em que está sendo executado. Use `ISingleAccountPublicClientApplication.isSharedDevice()` para determinar quando executar no modo de conta única.

Existem duas interfaces diferentes que representam o tipo de dispositivo em que seu aplicativo está. Quando você solicita uma instância de aplicação na fábrica de aplicativos da MSAL, o objeto de aplicação correto é fornecido automaticamente.

O modelo de objeto a seguir ilustra o tipo de objeto que você pode receber e o que isso significa no contexto de um dispositivo compartilhado:

![modelo de herança de aplicação de cliente público](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

Você precisará fazer uma verificação de tipo e lançar `PublicClientApplication` para a interface apropriada quando você receber o seu objeto. O código a seguir verifica o modo de conta múltipla ou o modo de conta única e lança o objeto do aplicativo apropriadamente:

```java
private IPublicClientApplication mApplication;

        // Running in personal-device mode?
        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        // Running in shared-device mode?
        } else if (mApplication instanceOf ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

As seguintes diferenças se aplicam dependendo se seu aplicativo está sendo executado em um dispositivo compartilhado ou pessoal:

|  | Dispositivo de modo compartilhado  | Dispositivo pessoal |
|---------|---------|---------|
| **Contas**     | Conta única | Várias contas |
| **Entrada** | Global | Global |
| **Sign-out** | Global | Cada aplicativo pode controlar se a saída é local para o aplicativo ou para a família de aplicativos. |
| **Tipos de conta com suporte** | Apenas contas de trabalho | Contas pessoais e de trabalho suportadas  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>Por que você pode querer apenas suportar o modo de conta única

Se você estiver escrevendo um aplicativo que só será usado para trabalhadores de primeira linha usando um dispositivo compartilhado, recomendamos que você escreva seu aplicativo para suportar apenas o modo de conta única. Isso inclui a maioria dos aplicativos focados em tarefas, como os aplicativos de registros médicos, aplicativos de fatura e a maioria dos aplicativos de linha de negócios. Apenas o suporte ao modo de conta única simplifica o desenvolvimento porque você não precisará implementar os recursos adicionais que fazem parte de aplicativos de várias contas.

## <a name="what-happens-when-the-device-mode-changes"></a>O que acontece quando o modo do dispositivo muda

Se o aplicativo estiver em execução no modo de várias contas e um administrador colocar o dispositivo no modo de dispositivo compartilhado, todas as contas do dispositivo serão liberadas do aplicativo e as transições do aplicativo para o modo de conta única.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>A saída compartilhada do dispositivo e o ciclo de vida geral do aplicativo

Quando um usuário sai, você precisará tomar medidas para proteger a privacidade e os dados do usuário. Por exemplo, se você estiver construindo um aplicativo de registros médicos, você vai querer ter certeza de que quando o usuário sair os registros de pacientes exibidos anteriormente serão liberados. Sua aplicação deve ser preparada para isso e verificar cada vez que entra em primeiro plano.

Quando seu aplicativo usa o MSAL para fazer login com o usuário em um aplicativo em execução no dispositivo que está no modo compartilhado, a conta de login e os tokens armazenados em cache são removidos do aplicativo e do dispositivo.

O diagrama a seguir mostra o ciclo de vida geral do aplicativo e eventos comuns que podem ocorrer enquanto o aplicativo é executado. O diagrama abrange a partir do momento em que uma atividade é lançada, fazendo login e assinando uma conta, e como eventos como pausar, retomar e parar a atividade se encaixam.

![Ciclo de vida do aplicativo de dispositivo compartilhado](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>Próximas etapas

Experimente o [modo Usar dispositivo compartilhado no tutorial do aplicativo para Android](tutorial-v2-shared-device-mode.md) que mostra como executar um aplicativo de primeira linha em um dispositivo Android de modo compartilhado.
