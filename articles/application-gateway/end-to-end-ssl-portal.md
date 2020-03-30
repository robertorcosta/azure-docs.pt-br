---
title: Configure criptografia TLS de ponta a ponta usando o portal
titleSuffix: Azure Application Gateway
description: Aprenda a usar o portal Azure para criar um gateway de aplicativo com criptografia TLS de ponta a ponta.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6f86f32e64bbbe79ea5a403d04f7d6c29ee6b980
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133005"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>Configure TLS de ponta a ponta usando o Gateway de aplicativo com o portal

Este artigo descreve como usar o portal Azure para configurar a criptografia TLS (Transport Layer Security, segurança de camada de transporte de ponta a ponta), anteriormente conhecida como criptografia SSL (Secure Sockets Layer, camada de soquetes seguros), através do Azure Application Gateway v1 SKU.

> [!NOTE]
> O Application Gateway v2 SKU requer certificados raiz confiáveis para habilitar a configuração de ponta a ponta.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Para configurar tLS de ponta a ponta com um gateway de aplicativo, você precisa de um certificado para o gateway. Os certificados também são necessários para os servidores back-end. O certificado gateway é usado para derivar uma chave simétrica em conformidade com a especificação do protocolo TLS. A chave simétrica é então usada para criptografar e descriptografar o tráfego enviado para o gateway. 

Para criptografia TLS de ponta a ponta, os servidores back-end direito devem ser permitidos no gateway do aplicativo. Para permitir esse acesso, carregue o certificado público dos servidores back-end, também conhecidos como Certificados de Autenticação (v1) ou Certificados raiz confiáveis (v2), para o gateway do aplicativo. A adição do certificado garante que o gateway de aplicativo se comunique apenas com instâncias back-end conhecidas. Essa configuração protege ainda mais a comunicação de ponta a ponta.

Para saber mais, consulte [Visão geral do término do TLS e TLS de ponta a ponta com o Gateway de aplicativos](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>Crie um novo gateway de aplicativo com TLS de ponta a ponta

Para criar um novo gateway de aplicativo com criptografia TLS de ponta a ponta, você precisará primeiro ativar a terminação do TLS ao criar um novo gateway de aplicativo. Essa ação permite a criptografia TLS para comunicação entre o cliente e o gateway de aplicativo. Em seguida, você precisará colocar na lista Destinatários Seguros os certificados para os servidores back-end nas configurações HTTP. Essa configuração permite a criptografia TLS para comunicação entre o gateway de aplicativo e os servidores back-end. Isso realiza criptografia TLS de ponta a ponta.

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>Habilite a terminação do TLS ao criar um novo gateway de aplicativo

Para saber mais, consulte [ativar o término do TLS enquanto cria um novo gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Adicionar certificados de autenticação/raiz de servidores back-end

1. Selecione **Todos os recursos**e selecione **myAppGateway**.

2. Selecione **configurações HTTP** no menu do lado esquerdo. O Azure criou automaticamente uma configuração HTTP padrão, **appGatewayBackendHttpSettings**, quando você criou o gateway do aplicativo. 

3. Selecione **appGatewayBackendHttpSettings**.

4. Em **Protocolo,** selecione **HTTPS**. Aparece um painel para **certificados de autenticação Backend ou certificados de raiz confiáveis.**

5. Selecione **Criar novo**.

6. No **campo Nome,** digite um nome adequado.

7. Selecione o arquivo de certificado na caixa **de certificado Upload CER.**

   Para gateways de aplicativo Padrão e WAF (v1), você deve carregar a chave pública do seu certificado de servidor back-end no formato .cer.

   ![Adicionar certificado](./media/end-to-end-ssl-portal/addcert.png)

   Para Standard_v2 e WAF_v2 gateways de aplicativo, você deve carregar o certificado raiz do certificado de servidor back-end no formato .cer. Se o certificado de back-end for emitido por uma autoridade de certificado (CA) bem conhecida, você pode selecionar a caixa **de seleção de certificado de CA bem conhecida** e, em seguida, não precisa carregar um certificado.

   ![Adicionar certificado raiz confiável](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Certificado raiz](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Selecione **Salvar**.

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>Habilite o TLS de ponta a ponta para um gateway de aplicativo existente

Para configurar um gateway de aplicativo existente com criptografia TLS de ponta a ponta, você deve primeiro ativar a terminação TLS no ouvinte. Essa ação permite a criptografia TLS para comunicação entre o cliente e o gateway de aplicativo. Em seguida, coloque esses certificados para servidores back-end nas configurações HTTP na lista Destinatários seguros. Essa configuração permite a criptografia TLS para comunicação entre o gateway de aplicativo e os servidores back-end. Isso realiza criptografia TLS de ponta a ponta.

Você precisará usar um ouvinte com o protocolo HTTPS e um certificado para habilitar a rescisão do TLS. Você pode usar um ouvinte existente que atenda a essas condições ou criar um novo ouvinte. Se você escolher a opção anterior, você pode ignorar a seguinte seção "Ativar terminação TLS em um gateway de aplicativo existente" e mover-se diretamente para a seção "Adicionar autenticação/certificados raiz confiáveis para servidores backend".

Se você escolher a última opção, aplique as etapas no procedimento a seguir.
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>Habilitar o término do TLS em um gateway de aplicativo existente

1. Selecione **Todos os recursos**e selecione **myAppGateway**.

2. Selecione **Ouvintes** no menu do lado esquerdo.

3. Selecione o ouvinte **básico** ou **multi-site,** dependendo dos seus requisitos.

4. Em **Protocolo,** selecione **HTTPS**. Aparece um painel para **Certificado.**

5. Carregue o certificado PFX que você pretende usar para a rescisão do TLS entre o cliente e o gateway do aplicativo.

   > [!NOTE]
   > Para fins de teste, você pode usar um certificado auto-assinado. No entanto, isso não é aconselhável para cargas de trabalho de produção, porque eles são mais difíceis de gerenciar e não são completamente seguros. Para obter mais informações, consulte [criar um certificado auto-assinado](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Adicione outras configurações necessárias para o **Ouvinte,** dependendo dos seus requisitos.

7. Selecione **OK** para salvar.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Adicionar certificados de autenticação/raiz confiável de servidores back-end

1. Selecione **Todos os recursos**e selecione **myAppGateway**.

2. Selecione **configurações HTTP** no menu do lado esquerdo. Você pode colocar certificados em uma configuração HTTP back-end existente na lista Destinatários seguros ou criar uma nova configuração HTTP. (Na etapa seguinte, o certificado para a configuração HTTP padrão, **appGatewayBackendHttpSettings**, é adicionado à lista Destinatários seguros.)

3. Selecione **appGatewayBackendHttpSettings**.

4. Em **Protocolo,** selecione **HTTPS**. Aparece um painel para **certificados de autenticação Backend ou certificados de raiz confiáveis.** 

5. Selecione **Criar novo**.

6. No **campo Nome,** digite um nome adequado.

7. Selecione o arquivo de certificado na caixa **de certificado Upload CER.**

   Para gateways de aplicativo Padrão e WAF (v1), você deve carregar a chave pública do seu certificado de servidor back-end no formato .cer.

   ![Adicionar certificado](./media/end-to-end-ssl-portal/addcert.png)

   Para Standard_v2 e WAF_v2 gateways de aplicativo, você deve carregar o certificado raiz do certificado de servidor back-end no formato .cer. Se o certificado back-end for emitido por um CA bem conhecido, você pode selecionar a caixa de seleção **de certificado de CA bem conhecida** e, em seguida, não precisa carregar um certificado.

   ![Adicionar certificado raiz confiável](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Gerenciar o tráfego da Web com um gateway de aplicativo usando a CLI do Azure](./tutorial-manage-web-traffic-cli.md)
