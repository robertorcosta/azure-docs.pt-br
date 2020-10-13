---
title: Configuração de ouvinte de gateway Aplicativo Azure
description: Este artigo descreve como configurar ouvintes de gateway Aplicativo Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: ef2ff8924cd8a92c5d2d2e5dd9da6bb74fad1a14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89652802"
---
# <a name="application-gateway-listener-configuration"></a>Configuração do ouvinte do gateway de aplicativo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Um ouvinte é uma entidade lógica que verifica as solicitações de conexão de entrada usando a porta, o protocolo, o host e o endereço IP. Ao configurar o ouvinte, você deve inserir valores para eles que correspondam aos valores correspondentes na solicitação de entrada no gateway.

Ao criar um gateway de aplicativo usando o portal do Azure, você também cria um ouvinte padrão escolhendo o protocolo e a porta para o ouvinte. Você pode escolher se deseja habilitar o suporte do HTTP2 no ouvinte. Depois de criar o gateway de aplicativo, você pode editar as configurações desse ouvinte padrão (*appGatewayHttpListener*) ou criar novos ouvintes.

## <a name="listener-type"></a>Tipo de ouvinte

Ao criar um novo ouvinte, você escolhe entre [ *básico* e *multissite*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Se você quiser que todas as suas solicitações (para qualquer domínio) sejam aceitas e encaminhadas para pools de back-end, escolha básico. Saiba [como criar um gateway de aplicativo com um ouvinte básico](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Se você quiser encaminhar solicitações para diferentes pools de back-end com base no cabeçalho do *host* ou nomes de host, escolha ouvinte multissite, em que você também deve especificar um nome de host que corresponda à solicitação de entrada. Isso ocorre porque o gateway de aplicativo depende de cabeçalhos de host HTTP 1,1 para hospedar mais de um site na mesma porta e endereço IP público. Para saber mais, confira [Hospedagem de vários sites usando o gateway de aplicativo](multiple-site-overview.md).

### <a name="order-of-processing-listeners"></a>Ordem de ouvintes de processamento

Para a SKU v1, as solicitações são correspondidas de acordo com a ordem das regras e o tipo de ouvinte. Se uma regra com um ouvinte básico vier primeiro na ordem, ela será processada primeiro e aceitará qualquer solicitação para essa porta e combinação de IP. Para evitar isso, configure as regras com os ouvintes de vários sites primeiro e envie por push a regra com o ouvinte básico para o último na lista.

Para a SKU v2, os ouvintes multissite são processados antes dos ouvintes básicos.

## <a name="front-end-ip-address"></a>Endereço IP de front-end

Escolha o endereço IP de front-end que você planeja associar a este ouvinte. O ouvinte ouvirá as solicitações de entrada neste IP.

## <a name="front-end-port"></a>Porta de front-end

Escolha a porta de front-end. Selecione uma porta existente ou crie uma nova. Escolha qualquer valor do [intervalo permitido de portas](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Você pode usar não apenas portas bem conhecidas, como 80 e 443, mas qualquer porta personalizada que seja adequada. Uma porta pode ser usada para ouvintes voltados para o público ou para ouvintes de face privada.

## <a name="protocol"></a>Protocolo

Escolha HTTP ou HTTPS:

- Se você escolher HTTP, o tráfego entre o cliente e o gateway de aplicativo será descriptografado.

- Escolha HTTPS se desejar a [rescisão](features.md#secure-sockets-layer-ssltls-termination) de TLS ou [criptografia TLS de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/ssl-overview). O tráfego entre o cliente e o gateway de aplicativo é criptografado. E a conexão TLS é encerrada no gateway de aplicativo. Se desejar a criptografia TLS de ponta a ponta, você deverá escolher HTTPS e definir a configuração de **http de back-end** . Isso garante que o tráfego seja criptografado novamente quando ele viajar do gateway de aplicativo para o back-end.


Para configurar a rescisão de TLS e criptografia TLS de ponta a ponta, você deve adicionar um certificado ao ouvinte para permitir que o gateway de aplicativo derive uma chave simétrica. Isso é ditado pela especificação do protocolo TLS. A chave simétrica é usada para criptografar e descriptografar o tráfego que é enviado para o gateway. O certificado de gateway deve estar no formato PFX (troca de informações pessoais). Esse formato permite exportar a chave privada que o gateway usa para criptografar e descriptografar o tráfego.

## <a name="supported-certificates"></a>Certificados com suporte

Consulte [visão geral do término do TLS e TLS de ponta a ponta com o gateway de aplicativo](ssl-overview.md#certificates-supported-for-tls-termination)

## <a name="additional-protocol-support"></a>Suporte a protocolo adicional

### <a name="http2-support"></a>Suporte do HTTP2

O suporte ao protocolo HTTP/2 está disponível para clientes que se conectam somente a ouvintes do gateway de aplicativo. A comunicação com os pools de servidores back-end é por HTTP/1.1. Por padrão, o suporte HTTP/2 está desabilitado. O trecho de código Azure PowerShell a seguir mostra como habilitar isso:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

### <a name="websocket-support"></a>Suporte para WebSocket

O suporte ao WebSocket está habilitado por padrão. Não há nenhuma configuração configurável pelo usuário para habilitá-la ou desabilitá-la. Você pode usar Websockets com ouvintes HTTP e HTTPS.

## <a name="custom-error-pages"></a>Páginas de erro personalizadas

Você pode definir um erro personalizado no nível global ou no nível do ouvinte. No momento, não há suporte para a criação de páginas de erro personalizadas de nível global do portal do Azure. Você pode configurar uma página de erro personalizada para um erro de firewall do aplicativo Web 403 ou uma página de manutenção de 502 no nível do ouvinte. Você também deve especificar uma URL de blob publicamente acessível para o código de status de erro fornecido. Para obter mais informações, confira [Criar páginas de erro personalizadas do Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Códigos de erro do Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Para configurar uma página de erro personalizada global, consulte [configuração de Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

## <a name="tls-policy"></a>Política TLS

Você pode centralizar o gerenciamento de certificados TLS/SSL e reduzir a sobrecarga de descriptografia de criptografia para um farm de servidores back-end. A manipulação de TLS centralizado também permite que você especifique uma política TLS central adequada aos seus requisitos de segurança. Você pode escolher a política TLS *padrão*, *predefinida*ou *personalizada* .

Você configura a política TLS para controlar as versões do protocolo TLS. Você pode configurar um gateway de aplicativo para usar uma versão mínima de protocolo para Handshakes de TLS do TLS 1.0, TLS 1.1 e TLS 1.2. Por padrão, o SSL 2,0 e o 3,0 estão desabilitados e não são configuráveis. Para obter mais informações, consulte [visão geral da política de TLS do gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Depois de criar um ouvinte, você o associa a uma regra de roteamento de solicitação. Essa regra determina como as solicitações recebidas no ouvinte são roteadas para o back-end.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre as regras de roteamento de solicitações](configuration-request-routing-rules.md).