---
title: Configuração de novo agente de provisionamento de Azure AD Connect nuvem
description: Este artigo descreve como instalar o provisionamento de nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b18629a776dd98950f49b1f607cbc876abcd9c
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628870"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Criar uma nova configuração para Azure AD Connect provisionamento baseado em nuvem

Depois de instalar o agente, você precisa entrar no portal do Azure e configurar o provisionamento de nuvem do Azure Active Directory (Azure AD) Connect. Siga estas etapas para habilitar o agente.

## <a name="configure-provisioning"></a>Configurar provisionamento
Para configurar o provisionamento, siga estas etapas.

 1. Na portal do Azure, selecione **Azure Active Directory**
 2. Selecione **Azure AD Connect**.
 3. Selecione **gerenciar provisionamento**.

 ![Gerenciar provisionamento](media/how-to-configure/manage1.png)
 
 4. Selecione **nova configuração**.
 5. Na tela configuração, selecione seu domínio e se deseja habilitar a sincronização de hash de senha.  Clique em **criar**.  
 
 ![Criar nova configuração](media/how-to-configure/configure1.png)


 6.  A tela Editar configuração de provisionamento será aberta.

   ![Editar configuração](media/how-to-configure/configure2.png)

 7. Insira um **email de notificação**. Este email será notificado quando o provisionamento não estiver íntegro.
 8. Mova o seletor para habilitar e selecione salvar.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Provisionamento de escopo para usuários e grupos específicos
Você pode fazer o escopo do agente para sincronizar usuários e grupos específicos usando grupos de Active Directory locais ou unidades organizacionais. Você não pode configurar grupos e unidades organizacionais em uma configuração. 

 1.  Na portal do Azure, selecione **Azure Active Directory**.
 2. Selecione **Azure AD Connect**.
 3. Selecione **gerenciar provisionamento (versão prévia)**.
 4. Em **configuração**, selecione sua configuração.

 ![Seção de configuração](media/how-to-configure/scope1.png)
 
 5. Em **Configurar**, selecione **Editar filtros de escopo** para alterar o escopo da regra de configuração.
 ![Editar escopo](media/how-to-configure/scope3.png)
 7. À direita, você pode alterar o escopo.  Clique em **concluído**  e em **salvar** quando terminar.
 8. Depois de alterar o escopo, você deve [reiniciar o provisionamento](#restart-provisioning) para iniciar uma sincronização imediata das alterações.

## <a name="attribute-mapping"></a>Mapeamento de atributos
Azure AD Connect o provisionamento de nuvem permite que você mapeie facilmente atributos entre seus objetos de usuário/grupo local e os objetos no Azure AD.  Você pode personalizar mapeamentos de atributo padrão de acordo com suas necessidades comerciais. Sendo assim, você pode alterar ou excluir mapeamentos de atributos existentes ou criar mapeamentos.  Para obter mais informações, consulte [mapeamento de atributo](how-to-attribute-mapping.md).

## <a name="on-demand-provisioning"></a>Provisionamento sob demanda
Azure AD Connect o provisionamento de nuvem permite que você teste as alterações de configuração, aplicando essas alterações a um único usuário ou grupo.  Você pode usar isso para validar e verificar se as alterações feitas na configuração foram aplicadas corretamente e estão sendo sincronizadas corretamente com o Azure AD.  Para obter mais informações, consulte [provisionamento sob demanda](how-to-on-demand-provision.md).

## <a name="restart-provisioning"></a>Reiniciar o provisionamento 
Se você não quiser aguardar a próxima execução agendada, acione o provisionamento executado usando o botão **reiniciar provisionamento** . 
 1.  Na portal do Azure, selecione **Azure Active Directory**.
 2. Selecione **Azure AD Connect**.
 3.  Selecione **gerenciar provisionamento (versão prévia)**.
 4. Em **configuração**, selecione sua configuração.

   ![Seleção de configuração para reiniciar o provisionamento](media/how-to-configure/scope1.png)

 5. Na parte superior, selecione **reiniciar provisionamento**.

## <a name="remove-a-configuration"></a>Remover uma configuração
Para excluir uma configuração, siga estas etapas.

 1.  Na portal do Azure, selecione **Azure Active Directory**.
 2. Selecione **Azure AD Connect**.
 3. Selecione **gerenciar provisionamento (versão prévia)**.
 4. Em **configuração**, selecione sua configuração.
   
   ![Seleção de configuração para remover a configuração](media/how-to-configure/scope1.png)

 5. Na parte superior da tela de configuração, selecione **excluir**.

>[!IMPORTANT]
>Não há nenhuma confirmação antes de excluir uma configuração. Certifique-se de que essa é a ação que você deseja executar antes de selecionar **excluir**.


## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é o provisionamento em nuvem do Azure AD Connect?](what-is-cloud-provisioning.md)
