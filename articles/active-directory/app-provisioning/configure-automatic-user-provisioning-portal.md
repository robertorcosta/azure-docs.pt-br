---
title: Gerenciamento de provisionamento de usuários para aplicativos corporativos no Azure AD
description: Saiba como gerenciar o provisionamento de contas de usuário para aplicativos empresariais usando o Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f9bd1afeebedf4cbf37d75d9c57f3d8be0f288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264122"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Gerenciamento de provisionamento de conta de usuário para aplicativos empresariais no Portal do Azure

Este artigo descreve as etapas gerais para o gerenciamento do provisionamento automático de contas de usuário e o desprovisionamento de aplicativos que o suportam. *Provisionamento de contas de usuário* é o ato de criar, atualizar e/ou desabilitar os registros de conta de usuário no armazenamento de perfil do usuário local do aplicativo. A maioria dos aplicativos cloud e SaaS armazena mandatários e permissões na própria loja local de perfil do usuário, e a presença de tal registro de usuário na loja local do usuário é *necessária* para logon único e acesso ao trabalho. Para saber mais sobre o provisionamento automático da conta de usuário, consulte [Automatificar provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](user-provisioning.md).

> [!IMPORTANT]
> O Azure Active Directory (Azure AD) possui uma galeria que contém milhares de aplicativos pré-integrados que estão habilitados para provisionamento automático com o Azure AD. Você deve começar encontrando o tutorial de configuração de provisionamento específico para o seu aplicativo na [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). Você provavelmente encontrará orientação passo-a-passo para configurar o aplicativo e o Azure AD para criar a conexão de provisionamento.

## <a name="finding-your-apps-in-the-portal"></a>Localizar seus aplicativos no portal

Use o portal Diretório Ativo do Azure para visualizar e gerenciar todos os aplicativos configurados para o login único em um diretório. Aplicativos empresariais são aplicativos que são implantados e usados dentro da sua organização. Siga estas etapas para visualizar e gerenciar seus aplicativos corporativos:

1. Abra o [portal do Diretório Ativo do Azure](https://aad.portal.azure.com).
1. Selecione **aplicações Enterprise** no painel esquerdo. Uma lista de todos os aplicativos configurados é mostrada, incluindo aplicativos que foram adicionados da galeria.
1. Selecione qualquer aplicativo para carregar seu painel de recursos, onde você pode visualizar relatórios e gerenciar as configurações do aplicativo.
1. Selecione **Provisionamento** para gerenciar as configurações de provisionamento de contas de usuário para o aplicativo selecionado.

   ![Tela de provisionamento para gerenciar as configurações de provisionamento de contas de usuário](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Modos de provisionamento

O painel **provisionamento** começa com um menu **Mode,** que mostra os modos de provisionamento suportados para um aplicativo corporativo e permite configurá-los. As opções disponíveis incluem:

* **Automático** - Esta opção é mostrada se o Azure AD suportar provisionamento automático baseado em API ou desprovisionamento de contas de usuário para este aplicativo. Selecione este modo para exibir uma interface que ajude os administradores:

  * Configure o Ad do Azure para se conectar à API de gerenciamento de usuários do aplicativo
  * Crie mapeamentos de contas e fluxos de trabalho que definem como os dados da conta de usuário devem fluir entre o Azure AD e o aplicativo
  * Gerencie o serviço de provisionamento AD do Azure

* **Manual** - Esta opção é mostrada se o Azure AD não suportar o provisionamento automático de contas de usuário para este aplicativo. Neste caso, os registros de contas de usuário armazenados no aplicativo devem ser gerenciados usando um processo externo, com base nos recursos de gerenciamento e provisionamento do usuário fornecidos por esse aplicativo (que pode incluir o provisionamento Just-In-Time do SAML).

## <a name="configuring-automatic-user-account-provisioning"></a>Configurar o provisionamento de contas de usuário automático

Selecione a opção **Automático** para especificar configurações para credenciais de admin, mapeamentos, partida e parada e sincronização.

### <a name="admin-credentials"></a>Credenciais de administrador

Expanda **as credenciais de administração** para inserir as credenciais necessárias para que o Azure AD se conecte à API de gerenciamento de usuários do aplicativo. A entrada necessária varia dependendo do aplicativo. Para saber mais sobre os tipos de credencial e os requisitos para aplicativos específicos, confira o [tutorial de configuração para o aplicativo específico](user-provisioning.md).

Selecione **Conexão de teste** para testar as credenciais, fazendo com que o Azure AD tente se conectar ao aplicativo de provisionamento do aplicativo usando as credenciais fornecidas.

### <a name="mappings"></a>Mapeamentos

Expandir **mapeamentos** para visualizar e editar os atributos de usuário que fluem entre o Azure AD e o aplicativo-alvo quando as contas de usuário são provisionadas ou atualizadas.

Há um conjunto pré-configurado de mapeamentos entre objetos de usuário do Azure AD e os objetos de usuário de cada aplicativo SaaS. Alguns aplicativos gerenciam outros tipos de objetos, como grupos ou contatos. Selecione um mapeamento na tabela para abrir o editor de mapeamento à direita, onde você pode visualizá-los e personalizá-los.

![Mostra a tela de mapeamento de atributos](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

As personalizações com suporte incluem:

* Habilitar e desabilitar mapeamentos para objetos específicos, como o objeto de usuário do Azure AD para o objeto de usuário do aplicativo SaaS.
* Editar os atributos que fluem do objeto de usuário do Azure AD para o objeto de usuário do aplicativo. Para obter mais informações sobre mapeamento de atributos, confira [Noções básicas sobre tipos de mapeamento de atributos](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtrando as ações de provisionamento que o Azure AD executa no aplicativo direcionado. Em vez de ter o Azure AD sincronizando totalmente objetos, você pode limitar as ações executadas.

  Por exemplo, apenas selecionar **Atualização** e Azure AD apenas atualiza contas de usuário existentes em um aplicativo, mas não cria novas. Apenas selecione **Criar** e O Azure só cria novas contas de usuário, mas não atualiza as existentes. Esse recurso permite que os admins criem diferentes mapeamentos para criação de contas e atualização de fluxos de trabalho.

* Adicionando um novo mapeamento de atributos. Selecione **Adicionar novo mapeamento** na parte inferior do painel mapeamento de **atributos.** Preencha o formulário **Editar atributo** e selecione **Ok** para adicionar o novo mapeamento à lista.

### <a name="settings"></a>Configurações

Você pode iniciar e interromper o serviço de provisionamento Azure AD para o aplicativo selecionado na área **Configurações** da tela **Provisionamento.** Você também pode optar por limpar o cache de provisionamento e reiniciar o serviço.

Se o provisionamento estiver sendo habilitado pela primeira vez para um aplicativo, ative o serviço alterando o **Status de Provisionamento** para **Ativado**. Essa alteração faz com que o serviço de provisionamento Azure AD execute um ciclo inicial. Ele lê os usuários atribuídos na seção **Usuários e grupos,** consulta o aplicativo de destino para eles e, em seguida, executa as ações de provisionamento definidas na seção **Mapeamentos** Azure AD. Durante esse processo, o serviço de provisionamento armazena dados armazenados em cache sobre quais contas de usuário está gerenciando, de modo que contas não gerenciadas dentro dos aplicativos-alvo que nunca estiveram no escopo de atribuição não são afetadas por operações de desprovisionamento. Após o ciclo inicial, o serviço de provisionamento sincroniza automaticamente objetos de usuário e grupo em um intervalo de quarenta minutos.

Alterar o **Status de Provisionamento** para **Desligado** para pausar o serviço de provisionamento. Neste estado, o Azure não cria, atualiza ou remove objetos de usuário ou grupo no aplicativo. Mude o estado de volta para **On** e o serviço continua de onde parou.

**O estado de corrente claro e a sincronização de reinicialização** acionam um ciclo inicial. Em seguida, o serviço avaliará todos os usuários do sistema de origem novamente e determinará se eles estão em escopo para o provisionamento. Isso pode ser útil quando seu aplicativo está atualmente em quarentena ou você precisa fazer uma alteração em seus mapeamentos de atributos. Observe que o ciclo inicial leva mais tempo para ser concluído do que o ciclo incremental típico devido ao número de objetos que precisam ser avaliados. Você pode aprender mais sobre o desempenho dos ciclos iniciais e incrementais [aqui.](application-provisioning-when-will-provisioning-finish-specific-user.md) 
