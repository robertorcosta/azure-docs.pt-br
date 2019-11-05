---
title: Migrar um nome DNS ativo – Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como migrar um nome de domínio DNS personalizado que já está atribuído a um site ativo ao Serviço de Aplicativo do Azure sem nenhum tempo de inatividade.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/21/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 172003b13807720df2431a3610947b36d8303fed
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470354"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrar um nome DNS ativo para o Serviço de Aplicativo do Azure

Este artigo mostra como migrar um nome DNS ativo para o [Serviço de Aplicativo do Azure](../app-service/overview.md) sem nenhum tempo de inatividade.

Quando você migra um site ativo e seu nome de domínio DNS para o Serviço de Aplicativo, o nome DNS já está atendendo ao tráfego em tempo real. Evite o tempo de inatividade na resolução do DNS durante a migração associando o nome DNS ativo preventivamente ao aplicativo Serviço de Aplicativo.

Caso não esteja preocupado com o tempo de inatividade na resolução do DNS, consulte [Mapear um nome DNS personalizado existente para o Serviço de Aplicativo do Azure](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir estas instruções:

- [Verifique se o aplicativo Serviço de Aplicativo não está na camada GRATUITA](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Vincular o nome de domínio preventivamente

Ao vincular um domínio personalizado preventivamente, você obtém o seguinte antes mesmo de fazer quaisquer alterações nos registros DNS:

- Verificar a propriedade de domínio
- Habilitar o nome de domínio para seu aplicativo

Quando você finalmente migrar o nome DNS personalizado do site antigo para o aplicativo Serviço de Aplicativo, não haverá nenhum tempo de inatividade na resolução do DNS.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Criar registro de verificação de domínio

Para verificar a propriedade do domínio, adicione um registro TXT. O registro TXT mapeia de _awverify.&lt;subdomain>_ para _&lt;appname>.azurewebsites.net_. 

O registro TXT necessário depende do registro DNS que você deseja migrar. Para obter exemplos, consulte a seguinte tabela (`@` normalmente representa o domínio raiz):

| Exemplo de registro DNS | Host TXT | Valor TXT |
| - | - | - |
| \@ (raiz) | _awverify_ | _&lt;appname>.azurewebsites.net_ |
| www (sub) | _awverify.www_ | _&lt;appname>.azurewebsites.net_ |
| \* (curinga) | _awverify.\*_ | _&lt;appname>.azurewebsites.net_ |

Na página de registros DNS, anote o tipo de registro do nome DNS que você deseja migrar. O Serviço de Aplicativo dá suporte a mapeamentos de CNAME e registros A.

> [!NOTE]
> Para certos provedores, como CloudFlare, `awverify.*` não é um registro válido. Use `*` apenas em vez disso.

> [!NOTE]
> Curinga `*` registros não será validado subdomínios com o registro do CNAME existente da lista. Talvez você precise criar explicitamente um registro TXT para cada subdomínio.


### <a name="enable-the-domain-for-your-app"></a>Habilitar o domínio para o aplicativo

No [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda da página do aplicativo, selecione **Domínios personalizados**. 

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na página **Domínios personalizados**, selecione o ícone **+** ao lado de **Adicionar nome do host**.

![Adicionar nome do host](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Digite o nome de domínio totalmente qualificado ao qual você adicionou o registro TXT, como `www.contoso.com`. Para um domínio de curinga (como \*.contoso.com), você pode usar qualquer nome DNS que corresponde ao domínio de curinga. 

Selecione **Validar**.

O botão **Adicionar nome do host** é ativado. 

Verifique se **Tipo de registro do nome do host** está definido como o tipo de registro DNS que você deseja migrar.

Selecione **Adicionar nome do host**.

![Adicionar nome DNS para o aplicativo](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Pode levar algum tempo para que o novo nome do host seja refletido na página **Domínios personalizados** do aplicativo. Tente atualizar o navegador para atualizar os dados.

![Registro CNAME adicionado](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Agora, o nome DNS personalizado está habilitado no Azure App. 

## <a name="remap-the-active-dns-name"></a>Remapear o nome DNS ativo

Agora só resta remapear o registro DNS ativo para que ele aponte para o Serviço de Aplicativo. No momento, ele ainda aponta para o site antigo.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Copiar o endereço IP do aplicativo (somente o registro A)

Se estiver fazendo o remapeamento de um registro CNAME, ignore esta seção. 

Para remapear um registro A, você precisa do endereço IP externo do aplicativo Serviço de Aplicativo, que é mostrado na página **Domínios personalizados**.

Feche a página **Adicionar nome do host** selecionando **X** no canto superior direito. 

Na página **domínios personalizados**, copie o endereço IP do aplicativo.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Atualizar o registro DNS

Novamente na página de registros DNS do provedor de domínio, selecione o registro DNS a ser remapeado.

Para o exemplo de domínio raiz `contoso.com`, remapeie o registro A ou CNAME como os exemplos na seguinte tabela: 

| Exemplo de FQDN | Tipo de registro | Host | Valor |
| - | - | - | - |
| contoso.com (raiz) | Uma | `@` | Endereço IP de [Copiar o endereço IP do aplicativo](#info) |
| www\.contoso.com (sub) | CNAME | `www` | _&lt;appname>.azurewebsites.net_ |
| \*.contoso.com (curinga) | CNAME | _\*_ | _&lt;appname>.azurewebsites.net_ |

Salve suas configurações.

As consultas DNS devem começar a serem resolvidas para o aplicativo Serviço de Aplicativo imediatamente após a propagação do DNS.

## <a name="active-domain-in-azure"></a>Domínio ativo no Azure

Você pode migrar um domínio personalizado ativo no Azure, entre assinaturas ou dentro da mesma assinatura. No entanto, essa migração sem tempo de inatividade requer que o aplicativo de origem e o aplicativo de destino recebam o mesmo domínio personalizado em um determinado momento. Portanto, você precisa certificar-se de que os dois aplicativos não sejam implantados na mesma unidade de implantação (internamente conhecida como espaço Web). Um nome de domínio pode ser atribuído a apenas um aplicativo em cada unidade de implantação.

Você pode encontrar a unidade de implantação para seu aplicativo examinando o nome de domínio da URL do FTP/S `<deployment-unit>.ftp.azurewebsites.windows.net`. Verifique e certifique-se de que a unidade de implantação seja diferente entre o aplicativo de origem e o aplicativo de destino. A unidade de implantação de um aplicativo é determinada pelo [plano do serviço de aplicativo](overview-hosting-plans.md) em que ele está. Ele é selecionado aleatoriamente pelo Azure quando você cria o plano e não pode ser alterado. O Azure só garante que dois planos estejam na mesma unidade de implantação quando você [os cria no mesmo grupo de recursos *e* na mesma região](app-service-plan-manage.md#create-an-app-service-plan), mas não tem nenhuma lógica para garantir que os planos estejam em unidades de implantação diferentes. A única maneira de criar um plano em uma unidade de implantação diferente é continuar criando um plano em um novo grupo de recursos ou região até obter uma unidade de implantação diferente.

## <a name="next-steps"></a>Próximas etapas

Saiba como associar um certificado SSL personalizado ao Serviço de Aplicativo.

> [!div class="nextstepaction"]
> [Associar um certificado SSL ao serviço Azure App](configure-ssl-bindings.md)
