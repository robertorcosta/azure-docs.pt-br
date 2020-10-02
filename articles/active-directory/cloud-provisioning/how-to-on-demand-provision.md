---
title: Provisionamento de provisionamento por demanda do Azure AD Connect Cloud
description: Este artigo descreve o recurso de provisionamento sob demanda.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72623d69f9fb0cbc68758a362bb977e5e02d2c00
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91637032"
---
# <a name="azure-ad-connect-cloud-provisioning-on-demand-provisioning"></a>Provisionamento de provisionamento por demanda do Azure AD Connect Cloud

Azure AD Connect o provisionamento em nuvem introduziu um novo recurso, que permitirá que você teste as alterações de configuração, aplicando essas alterações a um único usuário.  Você pode usar isso para validar e verificar se as alterações feitas na configuração foram aplicadas corretamente e estão sendo sincronizadas corretamente com o Azure AD.  

> [!IMPORTANT] 
> Quando você usa o provisionamento sob demanda, os filtros de escopo não são aplicados ao usuário selecionado.  Isso significa que você pode usar o provisionamento sob demanda em usuários que estão fora das UOs que você especificou.


## <a name="using-on-demand-provisioning"></a>Usando o provisionamento sob demanda
Para usar o novo recurso, siga as etapas abaixo.


1.  Na portal do Azure, selecione **Azure Active Directory**.
2.  Selecione **Azure AD Connect**.
3.  Selecione **gerenciar provisionamento**.

    ![Gerenciar provisionamento](media/how-to-configure/manage1.png)
4. Em **configuração**, selecione sua configuração.
5. Em **validar** , clique no botão **provisionar um usuário** . 

 ![Provisionar um usuário](media/how-to-on-demand-provision/on-demand2.png)

6. Na tela de provisionamento sob demanda.  Insira o **nome distinto** de um usuário e clique no botão **provisionar** .  
 
 ![Provisionamento sob demanda](media/how-to-on-demand-provision/on-demand3.png)
7. Após a conclusão, você deverá ver uma tela de êxito e quatro caixas de seleção verdes indicando que ela foi provisionada com êxito.  Todos os erros serão exibidos à esquerda.

  ![Sucesso](media/how-to-on-demand-provision/on-demand4.png)

Agora você pode examinar o usuário e determinar se as alterações feitas na configuração foram aplicadas.  O restante deste documento descreverá as seções individuais que são exibidas nos detalhes de um usuário sincronizado com êxito.

## <a name="import-user-details"></a>Importar detalhes do usuário
Esta seção fornece informações sobre o usuário que foi importado do Active Directory.  Esse é o aspecto do usuário antes de ser provisionado no Azure AD.  Clique no link **Exibir detalhes** para exibir essas informações.

![Importar usuário](media/how-to-on-demand-provision/on-demand5.png)

Usando essas informações, você pode ver os vários atributos e seus valores que foram importados.  Se você tiver criado um mapeamento de atributo personalizado, poderá ver o valor aqui.
![Importar detalhes do usuário](media/how-to-on-demand-provision/on-demand6.png)

## <a name="determine-if-user-is-in-scope-details"></a>Determinar se o usuário está nos detalhes do escopo
Esta seção fornece informações sobre se o usuário que foi importado para o Azure AD está no escopo.  Clique no link **Exibir detalhes** para exibir essas informações.

![Escopo do usuário](media/how-to-on-demand-provision/on-demand7.png)

Usando essas informações, você pode ver informações adicionais sobre o escopo de seus usuários.

![Detalhes do escopo do usuário](media/how-to-on-demand-provision/on-demand10a.png)

## <a name="match-user-between-source-and-target-system-details"></a>Corresponder o usuário entre os detalhes do sistema de origem e de destino
Esta seção fornece informações sobre se o usuário já existe no Azure AD e se uma junção ocorre em vez de provisionar um novo usuário.  Clique no link **Exibir detalhes** para exibir essas informações.
![Exibir detalhes](media/how-to-on-demand-provision/on-demand8.png)

Usando essas informações, você pode ver se uma correspondência foi encontrada ou se um novo usuário vai ser criado.

![Informações do usuário](media/how-to-on-demand-provision/on-demand11.png)

Os detalhes de correspondência mostrarão uma mensagem com uma das três operações a seguir.  Elas são:
- Criar-um usuário é criado no Azure AD
- Atualização – um usuário é atualizado com base em uma alteração feita na configuração
- Excluir-um usuário é removido do Azure AD.

Dependendo do tipo de operação que você executou, a mensagem irá variar.

## <a name="perform-action-details"></a>Executar detalhes da ação
Esta seção fornece informações sobre o usuário que foi provisionado ou exportado para o Azure AD depois que a configuração é aplicada.  Esse é o aspecto do usuário quando ele é provisionado no Azure AD.  Clique no link **Exibir detalhes** para exibir essas informações.
![Executar detalhes da ação](media/how-to-on-demand-provision/on-demand9.png)

Usando essas informações, você pode ver os valores dos atributos depois que a configuração é aplicada.  Eles parecem semelhantes ao que foi importado ou são os diferentes?  A configuração se aplica com êxito?  

Esse processo permitirá que você rastreie a transformação do atributo à medida que ele se mover pela nuvem e para seu locatário do Azure AD.

![atributo de rastreamento](media/how-to-on-demand-provision/on-demand12.png)

## <a name="next-steps"></a>Próximas etapas 

- [O que é o provisionamento em nuvem do Azure AD Connect?](what-is-cloud-provisioning.md)
- [Como instalar Azure AD Connect provisionamento de nuvem](how-to-install.md)
 