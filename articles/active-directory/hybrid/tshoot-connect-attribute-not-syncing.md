---
title: Solucionar problemas de um atributo que não está sincronizando no Azure AD Connect | Microsoft Docs
description: Este tópico fornece etapas para solucionar problemas com a sincronização de atributos usando a tarefa de solução de problemas.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a639b14c9313179816f6376aa0c5642a645ea344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "60455873"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Solucionar problemas de um atributo que não está sincronizando no Azure AD Connect

## <a name="recommended-steps"></a>**Etapas Recomendadas**

Antes de investigar problemas de sincronização de atributo, vamos entender o processo de sincronização do **Azure AD Connect**:

  ![Processo de Sincronização do Azure AD Connect](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** Espaço conector, uma tabela no banco de dados.
* **MV:** Metaverso, uma tabela no banco de dados.
* **Anúncio:** Diretório Ativo
* **AAD:** Diretório Ativo do Azure

### <a name="synchronization-steps"></a>**Etapas de sincronização**

* Importação de AD: Objetos de diretório ativo são trazidos para OD CS.

* Importação de AAD: Objetos do Azure Active Directory são trazidos para OAD CS.

* Sincronização: **As regras de sincronização de entrada** e as regras de **sincronização de saída** são executadas na ordem do número de precedência de menor para maior. Para exibir as Regras de Sincronização, você pode ir para **Editor de Regras de Sincronização** dos aplicativos da área de trabalho. As **Regras de Sincronização de Entrada** trazem dados de CS para MV. As **Regras de Sincronização de Saída** movem dados de MV para CS.

* Exportar para AD: Após executar a sincronização, os objetos são exportados de CS AD para **Active Directory**.

* Exportar para AAD: Após executar a sincronização, os objetos são exportados do AAD CS para **o Azure Active Directory**.

### <a name="step-by-step-investigation"></a>**Investigação passo a passo**

* Vamos começar nossa pesquisa do **Metaverso** e examinar o mapeamento de atributo de origem para destino.

* Inicie **Synchronization Service Manager** em aplicativos da área de trabalho, como mostrado abaixo:

  ![Inicializar o Synchronization Service Manager](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* No **Synchronization Service Manager**, selecione **Pesquisa de Metaverso**, selecione **Escopo por Tipo de Objeto**, selecione o objeto usando um atributo e clique no botão **Pesquisa**.

  ![Pesquisa de Metaverso](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Clique duas vezes no objeto encontrado na pesquisar **Metaverso** para exibir todos os seus atributos. Você pode clicar na guia **Conectores** para examinar o objeto correspondente em todos os **Espaços do Conector**.

  ![Conectores de Objeto do Metaverso](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Clique duas vezes no **Active Directory Connector** para exibir os atributos do **Espaço do Conector**. Clique no botão **Visualização**, na caixa de diálogo seguinte, clique no botão a **Gerar Visualização**.

  ![Atributos de Espaço do Conector](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Agora, clique no **Fluxo de Atributo de Importação**, isso mostra o fluxo de atributos do **Espaço do Active Directory Connector** para o **Metaverso**. A coluna **Regra de Sincronização** mostra quais **regras de sincronização** contribuem para esse atributo. A coluna **Fonte de Dados** mostra os atributos do **Espaço do Conector**. A coluna **Atributo de Metaverso** mostra os atributos no **Metaverso**. Você pode procurar o atributo que não está sincronizando aqui. Se você não encontrar o atributo aqui, ele não será mapeado e você precisará criar uma nova **Regra de Sincronização** personalizada para mapear o atributo.

  ![Atributos de Espaço do Conector](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Clique em **Exportar Fluxo de Atributos** no painel esquerdo para exibir o fluxo de atributos do **Metaverso** de volta no **Espaço do Active Directory Connector** usando **Regras de Sincronização de Saída**.

  ![Atributos de Espaço do Conector](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Da mesma forma, você pode exibir o objeto **Espaço de Conector do Azure Active Directory** e gerar a **Visualização** para exibir o fluxo de atributos de **Metaverso** para o **Espaço de Conector** e vice-versa, dessa forma, você poderá investigar por que um atributo não está sincronizando.

## <a name="recommended-documents"></a>**Documentos recomendados**
* [Sincronização do Azure AD Connect: conceitos técnicos](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Sincronização do Azure AD Connect: noções básicas sobre a arquitetura](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Sincronização do Azure AD Connect: noções básicas sobre expressões de provisionamento declarativo](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Sincronização do Azure AD Connect: noções básicas sobre expressões de provisionamento declarativo](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Sincronização do Azure AD Connect: Noções básicas sobre a configuração padrão](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Azure Active Directory Connect Sync: noções básicas sobre usuários, grupos e contatos](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Sincronização Azure AD Connect: Atributos de sombra](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Próximas etapas

- [Sincronização do Azure AD Connect](how-to-connect-sync-whatis.md).
- [O que é a identidade híbrida?](whatis-hybrid-identity.md).
