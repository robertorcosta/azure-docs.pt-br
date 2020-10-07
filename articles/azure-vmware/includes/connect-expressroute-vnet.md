---
title: Conectar o ExpressRoute ao gateway de rede virtual
description: Etapas para conectar o ExpressRoute ao gateway de rede virtual.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 241919e3a69b8d1c3c24e6c894bcbf20aea62d5f
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578335"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. Navegue até a nuvem privada criada no tutorial anterior, selecione **Conectividade** em **Gerenciar** e selecione a guia **ExpressRoute**.

1. Copie a chave de autorização. Se não houver uma chave de autorização, você precisará criar uma: selecione **+ Solicitar uma chave de autorização**.

   :::image type="content" source="../media/expressroute-global-reach/start-request-auth-key.png" alt-text="Copie a chave de autorização. Se não houver uma chave de autorização, você precisará criar uma: selecione + Solicitar uma chave de autorização" border="true":::.

1. Navegue até o Gateway de Rede Virtual criado na etapa anterior e, em **Configurações**, selecione **Conexões**. Na página **Conexões**, selecione **+ Adicionar**.

1. Na página **Adicionar conexão**, forneça valores para os campos e selecione **OK**. 

   | Campo | Valor |
   | --- | --- |
   | **Nome**  | Insira um nome para a conexão.  |
   | **Tipo de conexão**  | Selecione **ExpressRoute**.  |
   | **Resgatar autorização**  | Esta caixa deve estar selecionada.  |
   | **Gateway de rede virtual** | O gateway de Rede Virtual criado anteriormente.  |
   | **Chave de autorização**  | Copie e cole a chave de autorização da guia ExpressRoute do seu Grupo de recursos. |
   | **URI de circuito de par**  | Copie e cole a ID do ExpressRoute da guia ExpressRoute do seu Grupo de recursos.  |

   :::image type="content" source="../media/expressroute-global-reach/open-cloud-shell.png" alt-text="Copie a chave de autorização. Se não houver uma chave de autorização, você precisará criar uma: selecione + Solicitar uma chave de autorização" border="true":::

A conexão entre o circuito do ExpressRoute e sua Rede Virtual é criada.