---
title: Reescreva cabeçalhos HTTP com gateway de aplicativo do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da reescrita de cabeçalhos HTTP no Gateway de aplicativos do Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/08/2019
ms.author: absha
ms.openlocfilehash: d0b28770940f0e1adeec16aa89cd087299bd4abc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132992"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Reescreva cabeçalhos HTTP com o Gateway de aplicativo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os cabeçalhos HTTP permitem que um cliente e um servidor passem informações adicionais com uma solicitação ou resposta. Ao reescrever esses cabeçalhos, você pode realizar tarefas importantes, como adicionar campos de cabeçalho relacionados à segurança, como HSTS/ X-XSS-Protection, remover campos de cabeçalho de resposta que podem revelar informações confidenciais e remover informações da porta de cabeçalhos X-Forwarded-For.

O Gateway de Aplicativo permite adicionar, remover ou atualizar solicitações HTTP e cabeçalhos de resposta, enquanto os pacotes de solicitação e resposta são transferidos entre os pools de cliente e de back-end. Ele também permite adicionar condições para garantir que os cabeçalhos especificados sejam reescritos somente quando determinadas condições forem atendidas.

O Application Gateway também suporta várias [variáveis de servidor](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) que ajudam a armazenar informações adicionais sobre solicitações e respostas. Isso torna mais fácil para você criar regras poderosas de reescrita.

> [!NOTE]
>
> O suporte de regravação de cabeçalho HTTP está disponível apenas para o [SKU Standard_V2 e WAF_v2](application-gateway-autoscaling-zone-redundant.md).

![Reescrevendo cabeçalhos](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Cabeçalhos suportados

Você pode reescrever todos os cabeçalhos em solicitações e respostas, exceto os cabeçalhos Host, Connection e Upgrade. Você também pode usar o gateway de aplicativo para criar cabeçalhos personalizados e adicioná-los às solicitações e respostas que estão sendo roteadas através dele.

## <a name="rewrite-conditions"></a>Condições de reescrita

Você pode usar condições de reescrita para avaliar o conteúdo de solicitações e respostas HTTP(S) e executar uma reescrita de cabeçalho somente quando uma ou mais condições forem atendidas. O gateway de aplicativo usa esses tipos de variáveis para avaliar o conteúdo das solicitações e respostas HTTP(S):

- Cabeçalhos HTTP na solicitação.
- Cabeçalhos HTTP na resposta.
- Variáveis do servidor Application Gateway.

Você pode usar uma condição para avaliar se uma variável especificada está presente, se uma variável especificada corresponde a um valor específico ou se uma variável especificada corresponde a um padrão específico. Você usa a [biblioteca Perl Compatible Regular Expressions (PCRE)](https://www.pcre.org/) para configurar a correspondência regular do padrão de expressão nas condições. Para saber mais sobre a sintaxe de expressão regular, consulte a [página principal de expressões regulares perl](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Reescrever ações

Você usa ações de reescrita para especificar os cabeçalhos de solicitação e resposta que deseja reescrever e o novo valor para os cabeçalhos. Você pode criar um novo cabeçalho, modificar o valor de um cabeçalho existente ou excluir um cabeçalho existente. O valor de um novo cabeçalho ou de um cabeçalho existente pode ser definido para esses tipos de valores:

- Text.
- Cabeçalho da solicitação. Para especificar um cabeçalho de solicitação, você precisa usar a sintaxe {http_req_*cabeçalhoNome*}.
- Cabeçalho de resposta. Para especificar um cabeçalho de resposta, você precisa usar a sintaxe {http_resp_*cabeçalhoNome*}.
- Variável servidor. Para especificar uma variável de servidor, você precisa usar a sintaxe {var_*serverVariable*}.
- Uma combinação de texto, um cabeçalho de solicitação, um cabeçalho de resposta e uma variável de servidor.

## <a name="server-variables"></a>Variáveis de servidor

O Application Gateway usa variáveis de servidor para armazenar informações úteis sobre o servidor, a conexão com o cliente e a solicitação atual na conexão. Exemplos de informações armazenadas incluem o endereço IP do cliente e o tipo de navegador da Web. As variáveis do servidor mudam dinamicamente, por exemplo, quando uma nova página é carregada ou quando um formulário é postado. Você pode usar essas variáveis para avaliar condições de reescrita e reescrever cabeçalhos.

O gateway de aplicativo suporta essas variáveis do servidor:

| Nome da variável | Descrição                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | O campo de cabeçalho de solicitação `client_ip` do cliente X-Forwarded-For com a variável (veja explicação mais tarde nesta tabela) anexado a ele no formato IP1, IP2, IP3 e assim por diante. Se o campo X-Forwarded-For não estiver no `add_x_forwarded_for_proxy` cabeçalho de `$client_ip` solicitação do cliente, a variável será igual à variável. Essa variável é particularmente útil quando você deseja reescrever o cabeçalho X-Forwarded-For definido pelo Application Gateway para que o cabeçalho contenha apenas o endereço IP sem as informações da porta. |
| ciphers_supported          | Uma lista das cifras apoiadas pelo cliente.          |
| ciphers_used               | A seqüência de cifras utilizadas para uma conexão TLS estabelecida. |
| client_ip                  | O endereço IP do cliente do qual o gateway de aplicativo recebeu a solicitação. Se houver um proxy reverso antes do gateway de aplicativo e do cliente de origem, *client_ip* retornarão o endereço IP do proxy reverso. |
| client_port                | A porta do cliente.                                                  |
| client_tcp_rtt             | Informações sobre a conexão TCP do cliente. Disponível em sistemas que suportam a opção de soquete TCP_INFO. |
| client_user                | Quando a autenticação HTTP é usada, o nome de usuário fornecido para autenticação. |
| host                       | Nesta ordem de precedência: o nome do host da linha de solicitação, o nome do host do campo de cabeçalho de solicitação host ou o nome do servidor que corresponde a uma solicitação. |
| cookie_*nome*              | O *biscoito de nome.*                                            |
| http_method                | O método usado para fazer a solicitação de URL. Por exemplo, GET ou POST. |
| http_status                | O status da sessão. Por exemplo, 200, 400 ou 403.                       |
| http_version               | O protocolo de solicitação. Normalmente HTTP/1.0, HTTP/1.1 ou HTTP/2.0. |
| query_string               | A lista de pares de variáveis/valorque segue o "?" na URL solicitada. |
| received_bytes             | A duração da solicitação (incluindo a linha de solicitação, o cabeçalho e o corpo de solicitação). |
| request_query              | Os argumentos na linha de solicitação.                                |
| request_scheme             | O esquema de solicitação: http ou https.                            |
| request_uri                | A solicitação original completa URI (com argumentos).                   |
| sent_bytes                 | O número de bytes enviados a um cliente.                             |
| server_port                | A porta do servidor que aceitou uma solicitação.                 |
| ssl_connection_protocol    | O protocolo de uma conexão TLS estabelecida.        |
| ssl_enabled                | "Ligado" se a conexão funcionar no modo TLS. Caso contrário, uma corda vazia. |

## <a name="rewrite-configuration"></a>Configuração de regravação

Para configurar a reescrita do cabeçalho HTTP, você precisa concluir essas etapas.

1. Crie os objetos necessários para reescrever o cabeçalho HTTP:

   - **Reescrever ação**: Usado para especificar os campos de solicitação e solicitação de cabeçalho que você deseja reescrever e o novo valor para os cabeçalhos. Você pode associar uma ou mais condições de reescrita com uma ação de reescrita.

   - **Condição de reescrita:** Uma configuração opcional. As condições de regravação avaliam o conteúdo das solicitações e respostas HTTP(S). A ação de reescrita ocorrerá se a solicitação ou resposta HTTP(S) corresponder à condição de reescrita.

     Se você associar mais de uma condição a uma ação, a ação só ocorre quando todas as condições são atendidas. Em outras palavras, a operação é uma operação lógica e.

   - **Regra de reescrita**: Contém combinações de várias combinações de ação de reescrita/ reescrita.

   - **Seqüência de**regras : Ajuda a determinar a ordem em que as regras de reescrita são executadas. Essa configuração é útil quando você tem várias regras de reescrita em um conjunto de reescrita. Uma regra de reescrita que tem um valor de seqüência de regras mais baixo é executada primeiro. Se você atribuir a mesma seqüência de regras a duas regras de reescrita, a ordem de execução não é determinista.

   - **Conjunto de regravação**: Contém várias regras de reescrita que serão associadas a uma regra de roteamento de solicitação.

2. Anexar o conjunto de regravação *(reescreverRuleSet)* a uma regra de roteamento. A configuração de reescrita é anexada ao ouvinte de origem através da regra de roteamento. Quando você usa uma regra básica de roteamento, a configuração de regravação de cabeçalho está associada a um ouvinte de origem e é uma reescrita global de cabeçalho. Quando você usa uma regra de roteamento baseada em caminho, a configuração de regravação de cabeçalho é definida no mapa de caminho da URL. Nesse caso, aplica-se apenas à área de caminho específica de um site.
   > [!NOTE]
   > Url Regravar alterar os cabeçalhos; ele não altera a URL para o caminho.

Você pode criar vários conjuntos de reescrita de cabeçalho HTTP e aplicar cada conjunto de regravação a vários ouvintes. Mas você pode aplicar apenas uma reescrita definida a um ouvinte específico.

## <a name="common-scenarios"></a>Cenários comuns

Aqui estão alguns cenários comuns para usar a reescrita de cabeçalho.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Remover informações da porta do cabeçalho X-Forwarded-For

O Gateway de aplicativo insere um cabeçalho X-Forwarded-For em todas as solicitações antes de encaminhar as solicitações para o backend. Este cabeçalho é uma lista separada por comuma de portas IP. Pode haver cenários em que os servidores back-end só precisam dos cabeçalhos para conter endereços IP. Você pode usar a reescrita de cabeçalho para remover as informações da porta do cabeçalho X-Forwarded-For. Uma maneira de fazer isso é definir o cabeçalho para a variável servidor add_x_forwarded_for_proxy:

![Remover porta](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Modifique uma URL de redirecionamento

Quando um aplicativo back-end envia uma resposta de redirecionamento, você pode querer redirecionar o cliente para uma URL diferente da especificada pelo aplicativo back-end. Por exemplo, você pode querer fazer isso quando um serviço de aplicativo está hospedado atrás de um gateway de aplicativo e exige que o cliente faça um redirecionamento para seu caminho relativo. (Por exemplo, um redirecionamento de contoso.azurewebsites.net/path1 para contoso.azurewebsites.net/path2.)

Como o App Service é um serviço multilocatário, ele usa o cabeçalho host na solicitação para encaminhar a solicitação para o ponto final correto. Os serviços de aplicativos têm um nome de domínio padrão de *.azurewebsites.net (digamos contoso.azurewebsites.net) que é diferente do nome de domínio do gateway do aplicativo (digamos contoso.com). Como a solicitação original do cliente tem o nome de domínio (contoso.com) do gateway do aplicativo como nome de host, o gateway do aplicativo altera o nome do host para contoso.azurewebsites.net. Ele faz essa alteração para que o serviço do aplicativo possa encaminhar a solicitação para o ponto final correto.

Quando o serviço de aplicativo envia uma resposta de redirecionamento, ele usa o mesmo nome de host no cabeçalho de localização de sua resposta como o da solicitação que recebe do gateway do aplicativo. Assim, o cliente fará a solicitação diretamente para contoso.azurewebsites.net/path2 em vez de passar pelo gateway de aplicativo (contoso.com/path2). Ignorar o gateway do aplicativo não é desejável.

Você pode resolver esse problema definindo o nome do host no cabeçalho de localização para o nome de domínio do gateway do aplicativo.

Aqui estão as etapas para substituir o nome de host:

1. Crie uma regra de reescrita com uma condição que avalia se o cabeçalho de localização na resposta contém azurewebsites.net. Digite `(https?):\/\/.*azurewebsites\.net(.*)$`o padrão .
1. Execute uma ação para reescrever o cabeçalho de localização para que ele tenha o nome de host do gateway do aplicativo. Faça isso `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` entrando como o valor do cabeçalho.

![Modificar cabeçalho de localização](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementar cabeçalhos HTTP de segurança para evitar vulnerabilidades

Você pode corrigir várias vulnerabilidades de segurança implementando cabeçalhos necessários na resposta do aplicativo. Esses cabeçalhos de segurança incluem X-XSS-Protection, Strict-Transport-Security e Content-Security-Policy. Você pode usar o Application Gateway para definir esses cabeçalhos para todas as respostas.

![Cabeçalho de segurança](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Exclua cabeçalhos indesejados

Você pode querer remover cabeçalhos que revelam informações confidenciais de uma resposta HTTP. Por exemplo, você pode querer remover informações como o nome do servidor back-end, o sistema operacional ou os detalhes da biblioteca. Você pode usar o gateway de aplicativo para remover esses cabeçalhos:

![Cabeçalho de exclusão](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Verifique a presença de um cabeçalho

Você pode avaliar uma solicitação HTTP ou cabeçalho de resposta para a presença de um cabeçalho ou variável servidor. Esta avaliação é útil quando você deseja executar uma reescrita de cabeçalho somente quando um certo cabeçalho está presente.

![Verificando a presença de um cabeçalho](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Limitações

- Se uma resposta tiver mais de um cabeçalho com o mesmo nome, então reescrever o valor de um desses cabeçalhos resultará em soltar os outros cabeçalhos na resposta. Isso geralmente pode acontecer com o cabeçalho Set-Cookie, já que você pode ter mais de um cabeçalho set-cookie em uma resposta. Um desses cenários é quando você está usando um serviço de aplicativo com um gateway de aplicativo e configurou afinidade de sessão baseada em cookies no gateway do aplicativo. Neste caso, a resposta conterá dois cabeçalhos set-cookie: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` um usado pelo serviço do `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`aplicativo, por exemplo: e outro para afinidade de gateway de aplicativo, por exemplo, . Reescrever um dos cabeçalhos set-cookie neste cenário pode resultar na remoção do outro cabeçalho Set-Cookie da resposta.

- A reescrita dos cabeçalhos Conexão, Upgrade e Host não é suportada no momento.

- Os nomes de cabeçalho podem conter quaisquer caracteres alfanuméricos e símbolos específicos definidos no [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). No momento, não suportamos\_o personagem especial underscore ( ) em nomes de Cabeçalho.

## <a name="next-steps"></a>Próximas etapas

Para saber como reescrever cabeçalhos HTTP, consulte:

- [Reescreva cabeçalhos HTTP usando o portal Azure](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Reescreva cabeçalhos HTTP usando o Azure PowerShell](add-http-header-rewrite-rule-powershell.md)
