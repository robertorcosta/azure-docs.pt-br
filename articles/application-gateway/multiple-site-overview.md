---
title: Hospedagem de vários sites no Gateway de Aplicativo do Azure
description: Este artigo fornece uma visão geral do suporte a vários sites do Gateway de Aplicativo do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 07/20/2020
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: b3e6bc6d2dd5568dcc11a37c6ab44bd3b4089c66
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87067942"
---
# <a name="application-gateway-multiple-site-hosting"></a>Hospedagem de vários sites do Gateway de Aplicativo

A hospedagem de vários sites permite que você configure mais de um aplicativo Web na mesma porta de um gateway de aplicativo. Ele permite que você configure uma topologia mais eficiente para suas implantações adicionando até mais de 100 sites a um gateway de aplicativo. Cada site pode ser direcionado para seu próprio pool de back-end. Por exemplo, três domínios, contoso.com, fabrikam.com e adatum.com, apontam para o endereço IP do gateway de aplicativo. Você criaria três ouvintes multissite e configuraria cada ouvinte para as respectivas configurações de porta e protocolo. 

Você também pode definir nomes de host curinga em um ouvinte multissite e até 5 nomes de host por ouvinte. Para saber mais, confira [nomes de host curinga no ouvinte](#wildcard-host-names-in-listener-preview).

:::image type="content" source="./media/multiple-site-overview/multisite.png" alt-text="Gateway de aplicativo multissite":::

> [!IMPORTANT]
> As regras são processadas na ordem em que estão listadas no portal para a SKU v1. Para a SKU v2, as correspondências exatas têm precedência mais alta. É altamente recomendável configurar primeiro os ouvintes de vários locais para configurar um ouvinte básico.  Isso irá garantir que o tráfego seja roteado para o back-end correto. Se um ouvinte básico for listado primeiro e corresponder a uma solicitação de entrada, ele é processado por esse ouvinte.

As solicitações de `http://contoso.com` são encaminhadas para ContosoServerPool, e as de `http://fabrikam.com` são encaminhadas para FabrikamServerPool.

Da mesma forma, você pode hospedar vários subdomínios do mesmo domínio pai na mesma implantação do gateway de aplicativo. Por exemplo, você pode hospedar `http://blog.contoso.com` e `http://app.contoso.com` em uma implantação de gateway de aplicativo único.

## <a name="wildcard-host-names-in-listener-preview"></a>Nomes de host curinga no ouvinte (visualização)

O gateway de aplicativo permite o roteamento baseado em host usando o ouvinte de HTTP (S) de vários sites. Agora, você tem a capacidade de usar caracteres curinga, como asterisco (*) e ponto de interrogação (?) no nome do host, e até 5 nomes de host por ouvinte HTTP (S) de vários sites. Por exemplo, `*.contoso.com`.

Usando um caractere curinga no nome do host, você pode corresponder a vários nomes de host em um único ouvinte. Por exemplo, `*.contoso.com` pode corresponder a `ecom.contoso.com` , `b2b.contoso.com` bem como `customer1.b2b.contoso.com` e assim por diante. Usando uma matriz de nomes de host, você pode configurar mais de um nome de host para um ouvinte para rotear solicitações para um pool de back-end. Por exemplo, um ouvinte pode conter `contoso.com, fabrikam.com` , que aceitará solicitações para ambos os nomes de host.

:::image type="content" source="./media/multiple-site-overview/wildcard-listener-diag.png" alt-text="Ouvinte curinga":::

>[!NOTE]
> Este recurso está em visualização e está disponível somente para Standard_v2 e WAF_v2 SKU do gateway de aplicativo. Para saber mais sobre visualizações, confira [termos de uso aqui](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

No [portal do Azure](create-multiple-sites-portal.md), você pode defini-las em caixas de texto separadas, conforme mostrado na captura de tela abaixo.

:::image type="content" source="./media/multiple-site-overview/wildcard-listener-example.png" alt-text="Configuração de exemplo de ouvinte curinga":::

>[!NOTE]
>Se você estiver criando um novo ouvinte multissite ou adicionando mais de um nome de host ao ouvinte multissite existente no portal do Azure, ele será adicionado por padrão ao `HostNames` parâmetro da configuração do ouvinte, o que adiciona mais funcionalidade ao `HostName` parâmetro existente na configuração.

No [Azure PowerShell](tutorial-multiple-sites-powershell.md), você deve usar `-HostNames` em vez de `-HostName` . Com os nomes de host, você pode mencionar até 5 nome de host como valores separados por vírgulas e usar caracteres curinga. Por exemplo, `-HostNames "*.contoso.com,*.fabrikam.com"`

No [CLI do Azure](tutorial-multiple-sites-cli.md), você deve usar `--host-names` em vez de `--host-name` . Com os nomes de host, você pode mencionar até 5 nomes de host como valores separados por vírgulas e usar caracteres curinga. Por exemplo, `--host-names "*.contoso.com,*.fabrikam.com"`

### <a name="allowed-characters-in-the-host-names-field"></a>Caracteres permitidos no campo nomes de host:

* `(A-Z,a-z,0-9)`-caracteres alfanuméricos
* `-`-hífen ou menos
* `.`-período como um delimitador
*   `*`-pode corresponder com vários caracteres no intervalo permitido
*   `?`-pode corresponder a um único caractere no intervalo permitido

### <a name="conditions-for-using-wildcard-characters-and-multiple-host-names-in-a-listener"></a>Condições para usar caracteres curinga e vários nomes de host em um ouvinte:

*   Você só pode mencionar até 5 nomes de host em um único ouvinte
*   O asterisco `*` pode ser mencionado apenas uma vez em um componente de nome de um estilo de domínio ou nome de host. Por exemplo, Component1 *. component2*. component3. `(*.contoso-*.com)`é válido.
*   Só pode haver até dois asteriscos `*` em um nome de host. Por exemplo, `*.contoso.*` é válido e `*.contoso.*.*.com` é inválido.
*   Só pode haver um máximo de 4 caracteres curinga em um nome de host. Por exemplo, `????.contoso.com` , `w??.contoso*.edu.*` são válidos, mas `????.contoso.*` são inválidos.
*   O uso de asterisco `*` e ponto de interrogação `?` juntos em um componente de um nome de host ( `*?` ou `?*` ou `**` ) é inválido. Por exemplo, `*?.contoso.com` e `**.contoso.com` são inválidos.

### <a name="considerations-and-limitations-of-using-wildcard-or-multiple-host-names-in-a-listener"></a>Considerações e limitações do uso de caracteres curinga ou vários nomes de host em um ouvinte:

*   A [terminação SSL e o SSL de ponta a ponta](ssl-overview.md) exigem que você configure o protocolo como https e carregue um certificado a ser usado na configuração do ouvinte. Se for um ouvinte multissite, você também poderá inserir o nome do host, geralmente esse é o CN do certificado SSL. Ao especificar vários nomes de host no ouvinte ou usar caracteres curinga, você deve considerar o seguinte:
    *   Se for um nome de host curinga como *. contoso.com, você deverá carregar um certificado curinga com CN como *. contoso.com
    *   Se vários nomes de host forem mencionados no mesmo ouvinte, você deverá carregar um certificado SAN (nomes alternativos da entidade) com o CNs correspondente aos nomes de host mencionados.
*   Você não pode usar uma expressão regular para mencionar o nome do host. Você só pode usar caracteres curinga, como asterisco (*) e ponto de interrogação (?) para formar o padrão de nome de host.
*   Para a verificação de integridade de back-end, não é possível associar várias [investigações personalizadas](application-gateway-probe-overview.md) por configurações de http. Em vez disso, você pode investigar um dos sites no back-end ou usar "127.0.0.1" para investigar o localhost do servidor de back-end. No entanto, quando você estiver usando caracteres curinga ou vários nomes de host em um ouvinte, as solicitações para todos os padrões de domínio especificados serão roteadas para o pool de back-end dependendo do tipo de regra (básica ou baseada em caminho).
*   As propriedades "hostname" aceitam uma cadeia de caracteres como entrada, em que você pode mencionar que apenas um nome de domínio não curinga e "hostnames" usa uma matriz de cadeias de caracteres como entrada, em que você pode mencionar até 5 nomes de domínio curinga. Mas as duas propriedades não podem ser usadas de uma vez.
*   Não é possível criar uma regra de [redirecionamento](redirect-overview.md) com um ouvinte de destino que usa caracteres curinga ou vários nomes de host.

Consulte [criar vários sites usando portal do Azure](create-multiple-sites-portal.md) ou [usando o Azure PowerShell](tutorial-multiple-sites-powershell.md) ou [usando CLI do Azure](tutorial-multiple-sites-cli.md) para obter o guia passo a passo sobre como configurar nomes de host curinga em um ouvinte multissite.

## <a name="host-headers-and-server-name-indication-sni"></a>Cabeçalhos de host e SNI (Indicação de Nome de Servidor)

Existem três mecanismos comuns para habilitar a hospedagem de vários sites na mesma infraestrutura.

1. Hospede vários aplicativos Web, cada um em um endereço IP exclusivo.
2. Use o nome de host para hospedar vários aplicativos Web no mesmo endereço IP.
3. Use portas diferentes para hospedar vários aplicativos Web no mesmo endereço IP.

Atualmente, o gateway de aplicativo dá suporte a um único endereço IP público em que ele escuta o tráfego. Portanto, vários aplicativos, cada um com seu próprio endereço IP, não têm suporte no momento. 

O gateway de aplicativo dá suporte a vários aplicativos que escutam em portas diferentes, mas esse cenário requer que os aplicativos aceitem o tráfego em portas não padrão. Geralmente, essa não é uma configuração que você deseja.

O Gateway de Aplicativo depende de cabeçalhos de host HTTP 1.1 para hospedar mais de um site na mesma porta e endereço IP público. Os sites hospedados no gateway de aplicativo também podem oferecer suporte ao descarregamento de TLS com a extensão TLS de Indicação de Nome de Servidor (SNI). Esse cenário significa que o farm da Web de back-end e o navegador cliente devem dar suporte à extensão TLS e HTTP/1.1 conforme definido no RFC 6066.

## <a name="next-steps"></a>Próximas etapas

Depois de aprender sobre a hospedagem de vários sites, vá para [criar vários sites usando portal do Azure](create-multiple-sites-portal.md) ou [usando Azure PowerShell](tutorial-multiple-sites-powershell.md) ou [usando CLI do Azure](tutorial-multiple-sites-cli.md) para obter o guia passo a passo sobre como criar um gateway de aplicativo para hospedar vários sites.

Você pode visitar [Modelo do Resource Manager usando a hospedagem de vários sites](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) para encontrar uma implantação baseada em modelo de ponta a ponta.