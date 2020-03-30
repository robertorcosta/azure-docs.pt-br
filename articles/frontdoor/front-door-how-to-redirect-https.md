---
title: Crie uma porta frontal com http para redirecionamento HTTPS usando o portal Azure
description: Aprenda a criar uma Porta frontal com tráfego redirecionado de HTTP para HTTPS usando o portal Azure.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: b7385ef27cd17705f2c86b6f57d4780511b6935c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246850"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Crie uma porta frontal com http para redirecionamento HTTPS usando o portal Azure

Você pode usar o portal Azure para criar uma [Porta da Frente](front-door-overview.md) com um certificado de rescisão SSL. Uma regra de roteamento é usada para redirecionar o tráfego HTTP para HTTPS.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Crie uma porta frontal com um recurso de Aplicativo web existente
> * Adicionar um domínio personalizado com certificado SSL 
> * Configurar HTTPS redirecionar no domínio personalizado

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Crie uma porta frontal com um recurso de Aplicativo web existente

1. Faça login no portal Azure em [https://portal.azure.com](https://portal.azure.com).
2. Clique em **Criar um recurso** encontrado na parte superior esquerda do portal do Azure.
3. Procure a **Porta da Frente** usando a barra de pesquisa e, uma vez que você encontre o tipo de recurso, clique em **Criar**.
4. Escolha uma assinatura e, em seguida, use um grupo de recursos existente ou crie uma nova. Note, a localização solicitada na ui é apenas para o grupo de recursos. A configuração da Porta frontal será implantada em todos os [locais POP do Azure Front Door](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door).

    ![Configure o básico para a nova porta da frente](./media/front-door-url-redirect/front-door-create-basics.png)

5. Clique **em Next** para inserir a guia configuração. A configuração para front door acontece em três etapas - adicionando um host frontend padrão, adicionando backends em um pool de backend e, em seguida, criando regras de roteamento para mapear o comportamento de roteamento para host frontend. 

     ![Designer de configuração da Porta da Frente](./media/front-door-url-redirect/front-door-designer.png)

6. Clique no**+** ícone ' ' nos _hosts frontend_ para criar um host frontend, digite um`\<**name**\>.azurefd.net`nome globalmente único para o seu host frontend padrão para a porta frontal (). Clique **em Adicionar** para prosseguir com a próxima etapa.

     ![Adicione um host frontend](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Clique no**+** ícone ' nas piscinas do _Backend_ para criar um pool de back-end. Forneça um nome para o pool de backend e clique em '**Adicionar um backend**'.
8. Selecione o tipo de host backend como _serviço de aplicativo_. Selecione a assinatura onde o aplicativo da Web está hospedado e selecione o aplicativo web específico da lista suspensa para **nome do host backend**.
9. Clique **em Adicionar** para salvar o backend e clique em **Adicionar** novamente para salvar a configuração de pool backend.   ![Adicione um backend em uma piscina de backend](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Clique no**+** ícone ' nas _regras de roteamento_ para criar uma rota. Forneça um nome para a rota, diga 'HttpToHttpsRedirect', e, em seguida, defina o campo _Protocolos Aceitos_ como **'somente HTTP'.** Certifique-se de que o _host frontend_ apropriado esteja selecionado.  
11. Na seção Detalhes da _rota,_ defina o _tipo de rota_ para **redirecionar,** certifique-se de que o _tipo Redirecionar_ está definido como Found **(302)** e _o protocolo Redirecionar_ está definido apenas como **HTTPS**. 
12. Clique em Adicionar para salvar a regra de roteamento para HTTP para https redirecionar.
     ![Adicionar uma rota de redirecionamento HTTP para HTTPS](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Adicione outra regra de roteamento para lidar com o tráfego HTTPS. Clique no**+**' sinal ' nas _regras de roteamento_ e forneça um nome para a rota, diga 'DefaultForwardingRoute' e, em seguida, defina o campo _'Protocolos aceitos'_ como **'somente HTTPS'.** Certifique-se de que o _host frontend_ apropriado esteja selecionado.
14. Na seção Detalhes da rota, defina o _Tipo de Rota_ para **Frente,** certifique-se de que o pool de back-end direito está selecionado e que o _Protocolo de encaminhamento_ está definido apenas para **HTTPS**. 
15. Clique em Adicionar para salvar a regra de roteamento para encaminhamento de solicitação.
     ![Adicione uma rota de encaminhamento para tráfego HTTPS](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Clique **em 'Revisar + criar** e **criar'** para criar seu perfil na Porta da Frente. Vá para o recurso uma vez criado.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Adicione um domínio personalizado à sua Porta da Frente e habilite https nele
As etapas a seguir mostram como você pode adicionar um domínio personalizado em um recurso da Porta frontal existente e, em seguida, habilitar http para https redirecionamento nele. 

### <a name="add-a-custom-domain"></a>Adicionar um domínio personalizado

Neste exemplo, você adiciona um registro `www` CNAME para `www.contosonews.com`o subdomínio (por exemplo, ).

#### <a name="create-the-cname-record"></a>Criar um registro CNAME

Adicione um registro CNAME para mapear um subdomínio no host`<name>.azurefd.net`frontend padrão da porta frontal (, onde `<name>` está o nome do perfil da porta frontal).

Para `www.contoso.com` o domínio, como exemplo, adicione um registro `www` `<name>.azurefd.net`CNAME que mapeia o nome para .

Depois de adicionar o CNAME, a página de registros DNS será parecida com o seguinte exemplo:

![Domínio personalizado CNAME para Porta da Frente](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>A bordo do domínio personalizado em sua porta da frente

1. Na guia de designer front door, clique no ícone '+' na seção Hosts do Frontend para adicionar um novo domínio personalizado. 
2. Digite o nome DNS personalizado totalmente qualificado `www.contosonews.com`no campo de nome do host personalizado, exemplo . 
3. Uma vez que o mapeamento CNAME do domínio para a porta da frente seja validado, clique em **Adicionar** para adicionar o domínio personalizado.
4. Clique **em Salvar** para enviar as alterações.

![Menu de domínio personalizado](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Habilite https em seu domínio personalizado

1. Clique no domínio personalizado que foi adicionado e na seção **Domínio personalizado HTTPS,** altere o status para **Ativado**.
2. Você pode deixar o **tipo de gerenciamento de certificado** definido para o Front Door _gerenciado_ pelo certificado gratuito mantido, gerenciado e rodado automaticamente pela Porta da Frente. Você também pode optar por usar seu próprio certificado SSL personalizado armazenado com o Azure Key Vault. Este tutorial pressupõe que o uso do certificado gerenciado do Front Door.
![Habilitando HTTPS para domínio personalizado](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Clique em **Atualizar** para salvar a seleção e, em seguida, clique **em Salvar**.
4. Clique **em Atualizar** após alguns minutos e clique novamente no domínio personalizado para ver o progresso do provisionamento de certificados. 

> [!WARNING]
> A ativação do HTTPS para um domínio personalizado pode levar vários minutos e também depende da `<name>.azurefd.net`validação da propriedade do domínio se o CNAME não estiver diretamente mapeado para o host Front Door . Saiba mais sobre [como ativar https para um domínio personalizado](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Configure as regras de roteamento para o domínio personalizado

1. Clique na regra de redirecionamento criada anteriormente.
2. Clique no menu de entrada para hosts frontend e selecione seu domínio personalizado para aplicar essa rota para o seu domínio também.
3. Clique em **Atualizar**.
4. Faça a mesma operação para a outra regra de roteamento, ou seja, para sua rota de encaminhamento para adicionar o domínio personalizado.
5. Clique **em Salvar** para enviar suas alterações.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
- Saiba mais sobre [o redirecionamento de URL na Porta da Frente](front-door-url-redirect.md).
