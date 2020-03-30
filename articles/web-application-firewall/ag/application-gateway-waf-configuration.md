---
title: Limites de tamanho de solicitação do Web application firewall e listas de exclusão no Gateway de aplicativo do Azure - portal do Azure
description: Este artigo fornece informações sobre os limites de tamanho da solicitação do Aplicativo Web E lista a configuração das listas de exclusão no Portal do Aplicativo com o portal Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 7244788bbc7431c7f26363b2852babb72d5697e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526783"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Limites de tamanho e exclusão do Aplicativo Web

O WAF (Azure Application Gateway Web Application Firewall, firewall de aplicativos web) do Azure Application oferece proteção para aplicativos web. Este artigo descreve a configuração dos limites de tamanho de solicitação e listas de exclusões de WAF. Essas configurações estão localizadas na Diretiva WAF associada ao gateway de aplicativo. Para saber mais sobre as políticas do WAF, consulte [o Firewall de aplicativos da Web do Azure no Gateway de aplicativos do Azure](ag-overview.md) e crie políticas de firewall de aplicativos da Web para o Gateway de [aplicativos](create-waf-policy-ag.md)

## <a name="waf-exclusion-lists"></a>Listas de exclusão de WAF

![Limites de tamanho de solicitação](../media/application-gateway-waf-configuration/waf-policy.png)

As listas de exclusões do WAF permitem a você omitir certos atributos de solicitação de uma avaliação do WAF. Um exemplo comum são os tokens inseridos do Active Directory que são usados para autenticação ou campos de senha. Esses atributos são propensos a conter os caracteres especiais que podem disparar um falso positivo das regras de WAF. Depois que um atributo é adicionado à lista de exclusões do WAF, ele não é levado em consideração por nenhuma regra do WAF configurada e ativa. As listas de exclusão são globais em escopo.

Os seguintes atributos podem ser adicionados às listas de exclusão por nome. Os valores do campo escolhido não são avaliados de acordo com as regras do WAF, mas seus nomes ainda são (veja Exemplo 1 abaixo, o valor do cabeçalho Usuário-Agente é excluído da avaliação waf). As listas de exclusão removem a inspeção do valor do campo.

* Cabeçalhos de solicitação
* Cookies de solicitação
* O nome do atributo de solicitação (args) pode ser adicionado como um elemento de exclusão, como:

   * Nome do campo de formulário
   * Entidade XML
   * Entidade JSON
   * Args de seqüência de consulta de URL

Você pode especificar um cabeçalho de solicitação exato, corpo, cookie ou uma correspondência de atributo de cadeia de consulta.  Outra opção é especificar correspondências parciais. Regras de exclusão são de escopo global e se aplicam a todas as páginas e todas as regras.

A seguir estão os operadores de critérios de correspondência com suporte:

- **Equals**: esse operador é usado para uma correspondência exata. Como exemplo, para a seleção de um cabeçalho chamado **bearerToken**, use o operador equals com seletor definido como **bearerToken**.
- **Começa com**: esse operador corresponde com todos os campos que começam com o valor do seletor especificado.
- **Termina com**: esse operador corresponde com todos os campos de solicitação que terminam com o valor do seletor especificado.
- **Contém**: esse operador corresponde com todos os campos de solicitação que contenham o valor do seletor especificado.
- **Igual a qualquer**: Este operador corresponde a todos os campos de solicitação. * será o valor do seletor.

Em todos os casos, a correspondência diferencia maiúsculas de minúsculas e a expressão regular não é permitida como seletor.

> [!NOTE]
> Para obter mais informações e ajuda para solucionar problemas, consulte [waf solução de problemas](web-application-firewall-troubleshoot.md).

### <a name="examples"></a>Exemplos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Os exemplos a seguir demonstram o uso de exclusões.

#### <a name="example-1"></a>Exemplo 1

Neste exemplo, você deseja excluir o cabeçalho do agente de usuário. O cabeçalho de solicitação do usuário-agente contém uma seqüência característica que permite que os pares de protocolo de rede identifiquem o tipo de aplicativo, sistema operacional, fornecedor de software ou versão de software do agente usuário do software solicitante. Para obter mais informações, consulte [Usuário-Agente](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

Pode haver várias razões para desativar a avaliação deste cabeçalho. Pode haver uma seqüência que o WAF vê e assume que é malicioso. Por exemplo, o clássico ataque SQL "x=x" em uma seqüência. Em alguns casos, isso pode ser um tráfego legítimo. Então você pode precisar excluir este cabeçalho da avaliação waf.

O cmdlet Azure PowerShell a seguir exclui o cabeçalho do agente de usuário da avaliação:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>Exemplo 2

Este exemplo exclui o valor no parâmetro do *usuário* que é passado na solicitação através da URL. Por exemplo, digamos que é comum no seu ambiente que o campo do usuário contenha uma string que o WAF vê como conteúdo malicioso, por isso bloqueia-o.  Você pode excluir o parâmetro do usuário neste caso para que o WAF não avalie nada no campo.

O cmdlet Azure PowerShell a seguir exclui o parâmetro do usuário da avaliação:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
Assim, se `http://www.contoso.com/?user%281%29=fdafdasfda` a URL for passada para o WAF, ela não avaliará a string **fdafdasfda,** mas ainda avaliará o nome do parâmetro **user%281%29**. 

## <a name="waf-request-size-limits"></a>Limites de tamanho de solicitação de WAF



O firewall do aplicativo Web permite a você configurar limites de tamanho de solicitação dentro de limites inferior e superior. As duas configurações de limites de tamanho a seguir estão disponíveis:

- O campo de tamanho máximo do corpo de solicitação é especificado em kilobytes e controla o limite de tamanho total da solicitação, excluindo quaisquer uploads de arquivo. Este campo pode variar de mínimo de 1 KB para o valor máximo de 128 KB. O valor padrão para o tamanho do corpo da solicitação é 128 KB.
- O campo de limite de carregamento de arquivo é especificado em MB e controla o tamanho máximo de carregamento de arquivos permitido. Este campo pode ter um valor mínimo de 1 MB e os seguintes máximos:

   - 100 MB para gateways WAF médios v1
   - 500 MB para gateways WAF grandes v1
   - 750 MB para gateways v2 WAF 

 O valor padrão para o limite de carregamento de arquivos é 100 MB.

O WAF também oferece um botão configurável para ativar ou desativar a inspeção de corpo da solicitação. Por padrão, a inspeção de corpo da solicitação está habilitada. Se a inspeção do corpo solicitado for desligada, a WAF não avaliará o conteúdo do corpo de mensagem HTTP. Nesses casos, o WAF continua a impor regras de WAF no URI, cookies e cabeçalhos. Se a inspeção do corpo da solicitação estiver desativada, o campo de tamanho máximo do corpo da solicitação não será aplicável e não poderá ser definido. Desativar a inspeção de corpo da solicitação permite que mensagens maiores que 128 KB sejam enviadas ao WAF, mas o corpo da mensagem não é inspecionado quanto a vulnerabilidades.

## <a name="next-steps"></a>Próximas etapas

Depois de configurar as configurações de WAF, você pode aprender como exibir os logs de WAF. Para obter mais informações, consulte [os diagnósticos do Application Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).
