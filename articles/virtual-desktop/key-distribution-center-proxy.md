---
title: Configurar o Kerberos centro de distribuição de chaves proxy da área de trabalho virtual do Windows-Azure
description: Como configurar um pool de hosts de área de trabalho virtual do Windows para usar um proxy de centro de distribuição de chaves Kerberos.
author: Heidilohr
ms.topic: how-to
ms.date: 01/26/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: f123659941eaeb3b8ceeb6a999abf836eb9cf5ea
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798352"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy"></a>Configurar um proxy de centro de distribuição de chaves Kerberos

Este artigo mostrará como configurar um proxy KDC (Kerberos Key Distribiution Center) para o pool de hosts. Esse proxy permite que as organizações se autentiquem com o Kerberos fora de seus limites corporativos. Por exemplo, você pode usar o proxy KDC para habilitar a autenticação de cartão inteligente para clientes externos.

## <a name="how-to-configure-the-kdc-proxy"></a>Como configurar o proxy KDC

Para configurar o proxy KDC:

1. Entre no portal do Azure como administrador.

2. Vá para a página da área de trabalho virtual do Windows.

3. Selecione o pool de hosts para o qual você deseja habilitar o proxy KDC e, em seguida, selecione **Propriedades de RDP**.

    > [!div class="mx-imgBorder"]
    > ![Uma captura de tela da página portal do Azure mostrando um usuário selecionando pools de hosts, depois o nome do pool de hosts de exemplo e as propriedades de RDP.](media/rdp-properties.png)

4. Selecione a guia **avançado** e insira um valor no seguinte formato sem espaços:

    > kdcproxyname: s:\<fqdn\>

    > [!div class="mx-imgBorder"]
    > ![Uma captura de tela mostrando a guia Avançado selecionada, com o valor inserido conforme descrito na etapa 4.](media/advanced-tab-selected.png)

5. Selecione **Salvar**.

6. O pool de hosts selecionado agora deve começar a emitir arquivos de conexão RDP com o campo kdcproxyname que você inseriu incluído.

## <a name="next-steps"></a>Próximas etapas

A função RDGateway no Serviços de Área de Trabalho Remota inclui um serviço de proxy KDC. Consulte [implantar a função de gateway RD na área de trabalho virtual do Windows](rd-gateway-role.md) para saber como configurar uma para ser um destino para a área de trabalho virtual do Windows.
