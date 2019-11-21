---
title: Hospedar aplicativos da web do Azure com carga balanceada no apex da zona
description: Use um registro de alias do DNS do Azure para hospedar aplicativos da Web com balanceamento de carga no ápice da zona
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 08/10/2019
ms.author: allensu
ms.openlocfilehash: a673a74f8f6f919e7ebb7fc3b065ee0742ab3a10
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212360"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Hospedar aplicativos da web do Azure com carga balanceada no apex da zona

O protocolo DNS impede a atribuição de algo diferente de um registro A ou AAAA no ápice da zona. Um vértice da zona de exemplo é contoso.com. Essa restrição apresenta um problema para os proprietários de aplicativos que têm aplicativos com balanceamento de carga por trás do Gerenciador de Tráfego. Não é possível apontar para o perfil do Gerenciador de Tráfego no registro do ápice da zona. Como resultado, os proprietários do aplicativo devem usar uma solução alternativa. Um redirecionamento na camada de aplicativo deve redirecionar do ápice da zona para outro domínio. An example is a redirect from contoso.com to www\.contoso.com. Esse arranjo apresenta um único ponto de falha para a função de redirecionamento.

Com registros de alias, esse problema não existe mais. Agora, os proprietários do aplicativo podem apontar seu registro do ápice da zona para um perfil do Gerenciador de Tráfego que tenha nós de extremidade externos. Os proprietários de aplicativos podem apontar para o mesmo perfil do Gerenciador de tráfego que é usado para qualquer outro domínio em sua zona DNS.

For example, contoso.com and www\.contoso.com can point to the same Traffic Manager profile. Esse é o caso, desde que o perfil do Gerenciador de Tráfego tenha apenas endpoints externos configurados.

Neste artigo, você aprende a criar um registro de alias para o seu ápice de domínio e a configurar seu perfil de Gerenciador de Tráfego do Microsoft Azure para seus aplicativos da web.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter um nome de domínio disponível para hospedar no DNS do Azure para testar. Você deve ter controle total sobre esse domínio. Controle total inclui a capacidade de definir os registros NS (nomes de servidor) para o domínio.

Para obter instruções para hospedar seu domínio no DNS do Azure, consulte [Tutorial: hospede seu domínio no DNS do Azure](dns-delegate-domain-azure-dns.md).

O domínio de exemplo usado neste tutorial é contoso.com, mas use seu próprio nome de domínio.

## <a name="create-a-resource-group"></a>Criar um grupos de recursos

Crie um grupo de recursos para armazenar todos os recursos usados neste artigo.

## <a name="create-app-service-plans"></a>Criar planos de serviço de aplicativo

Crie dois planos do Serviço de Aplicativo da Web em seu grupo de recursos usando a tabela a seguir para obter informações de configuração. Para obter mais informações sobre como criar um plano do Serviço de Aplicativo, consulte [Gerenciar um plano do Serviço de Aplicativo no Azure](../app-service/app-service-plan-manage.md).


|name  |Sistema operacional  |Location  |Camada de preços  |
|---------|---------|---------|---------|
|ASP-01     |Windows|Leste dos EUA|Dev / Teste D1-Compartilhado|
|ASP-02     |Windows|EUA Central|Dev / Teste D1-Compartilhado|

## <a name="create-app-services"></a>Criar Serviços de Aplicativos

Crie dois aplicativos da web, um em cada plano do Serviço de Aplicativo.

1. On upper left corner of the Azure portal page, select **Create a resource**.
2. Digite **Web app** na barra de pesquisa e pressione Enter.
3. Select **Web App**.
4. Clique em **Criar**.
5. Aceite os padrões e use a tabela a seguir para configurar os dois aplicativos da Web:

   |name<br>(deve ser exclusivo dentro do. azurewebsites.net)|Grupo de recursos |Pilha de runtime|Região|Plano do serviço de aplicativo/localização
   |---------|---------|-|-|-------|
   |App-01|Uso existente<br>Selecione o grupo de recursos|.NET Core 2.2|Leste dos EUA|ASP-01(D1)|
   |App-02|Uso existente<br>Selecione o grupo de recursos|.NET Core 2.2|EUA Central|ASP-02(D1)|

### <a name="gather-some-details"></a>Reúna alguns detalhes

Now you need to note the IP address and host name for the web apps.

1. Open your resource group and select your first web app (**App-01** in this example).
2. In the left column, select **Properties**.
3. Anote o endereço em **URL** e, em **Endereços IP de saída**, observe o primeiro endereço IP na lista. You'll use this information later when you configure your Traffic Manager end points.
4. Repita a operação em **aplicativo-02**.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de Tráfego

Crie um perfil do Gerenciador de Tráfego no seu grupo de recursos. Use os padrões e digite um nome exclusivo dentro do namespace trafficmanager.net.

Para obter informações sobre como criar um perfil do Gerenciador de Tráfego do Microsoft Azure, consulte [Guia de início rápido: crie um perfil do Gerenciador de Tráfego para um aplicativo da Web altamente disponível](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Criar pontos de extremidade

Agora você pode criar os pontos de extremidade para os dois aplicativos web.

1. Open your resource group and select your Traffic Manager profile.
2. In the left column, select **Endpoints**.
3. Selecione **Adicionar**.
4. Use a tabela a seguir para configurar os terminais:

   |Type  |name  |Escolha o destino  |Location  |Configurações personalizadas de cabeçalho|
   |---------|---------|---------|---------|---------|
   |Ponto de extremidade externo     |Final-01|Endereço IP que você registrou para o App-01|Leste dos EUA|host:\<a URL que você registrou para o aplicativo 01\><br>Exemplo: **: o aplicativo host-01.azurewebsites.net**|
   |Ponto de extremidade externo     |Final-02|Endereço IP que você registrou para o aplicativo-02|EUA Central|host:\<a URL que você registrou para o aplicativo-02\><br>Exemplo: **: o aplicativo host-02.azurewebsites.net**

## <a name="create-dns-zone"></a>Criar zona DNS

Você pode usar uma zona DNS existente para teste ou criar uma nova zona. Para criar e delegar uma nova zona DNS no Azure, consulte [Tutorial: hospede seu domínio no DNS do Azure](dns-delegate-domain-azure-dns.md).

## <a name="add-a-txt-record-for-custom-domain-validation"></a>Add a TXT record for custom domain validation

When you add a custom hostname to your web apps, it will look for a specific TXT record to validate your domain.

1. Open your resource group and select the DNS zone.
2. Escolha **Conjunto de registros**.
3. Add the record set using the following table. For the value, use the actual web app URL that you previously recorded:

   |name  |Type  |Value|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>Adicionar um domínio personalizado

Add a custom domain for both web apps.

1. Open your resource group and select your first web app.
2. In the left column, select **Custom domains**.
3. Under **Custom Domains**, select **Add custom domain**.
4. Under **Custom domain**, type your custom domain name. Por exemplo, contoso.com.
5. Selecione **Validar**.

   Your domain should pass validation and show green check marks next to **Hostname availability** and **Domain ownership**.
5. Selecione **Adicionar domínio personalizado**.
6. Para ver o novo nome do host em **Hostnames atribuídos ao site**, atualize seu navegador. The refresh on the page doesn't always show changes right away.
7. Repita este procedimento para seu segundo aplicativo web.

## <a name="add-the-alias-record-set"></a>Adicione o conjunto de registros de alias

Now add an alias record for the zone apex.

1. Open your resource group and select the DNS zone.
2. Escolha **Conjunto de registros**.
3. Adicione o registro definido usando a tabela a seguir:

   |name  |Type  |Conjunto de registros de alias  |Tipo de alias  |Recursos do Azure|
   |---------|---------|---------|---------|-----|
   |@     |A|SIM|Recursos do Azure|Gerenciador de tráfego - seu perfil|


## <a name="test-your-web-apps"></a>Teste seus aplicativos web

Now you can test to make sure you can reach your web app and that it's being load balanced.

1. Abra um navegador da web e navegue até seu domínio. Por exemplo, contoso.com. Você deve ver a página de aplicativo web padrão.
2. Interrompa seu primeiro aplicativo web.
3. Feche seu navegador da web e aguarde alguns minutos.
4. Inicie seu navegador da Web e navegue até seu domínio. Você ainda deve ver a página do aplicativo da web padrão.
5. Interrompa seu segundo aplicativo web.
6. Feche seu navegador da web e aguarde alguns minutos.
7. Inicie seu navegador da Web e navegue até seu domínio. Você deve ver o erro 403, indicando que o aplicativo da Web está parado.
8. Inicie seu segundo aplicativo da web.
9. Feche seu navegador da web e aguarde alguns minutos.
10. Inicie seu navegador da Web e navegue até seu domínio. Novamente, você verá a página de aplicativo web padrão.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre registros de alias, consulte os seguintes artigos:

- [Tutorial: configure um registro de alias para se referir a um endereço IP público do Azure](tutorial-alias-pip.md)
- [Tutorial: Configurar um registro de alias para dar suporte a nomes de domínio com o Gerenciador de tráfego do apex](tutorial-alias-tm.md)
- [PERGUNTAS FREQUENTES SOBRE O DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

To learn how to migrate an active DNS name, see [Migrate an active DNS name to Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).
