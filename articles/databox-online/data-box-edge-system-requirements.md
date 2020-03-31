---
title: Requisitos de sistema do Microsoft Azure Data Box Edge | Microsoft Docs
description: Aprender sobre os requisitos de software e de rede para seu Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: 458c062eef011363724cb894ce67ba75181ba8ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260222"
---
# <a name="azure-data-box-edge-system-requirements"></a>Requisitos do sistema Azure Data Box Edge

Este artigo descreve os requisitos de sistema importantes para a solução do Microsoft Azure Data Box Edge e para os clientes que se conectam ao Azure Data Box Edge. É recomendável que você examine as informações com atenção antes de implantar o Data Box Edge. Você pode consultar novamente a essas informações conforme necessário durante a implantação e operação subsequente.

São requisitos de sistema do Data Box Edge:

- **Requisitos de software para hosts** – descreve as plataformas com suporte, os navegadores da interface do usuário de configuração local, os clientes SMB e requisitos adicionais para os clientes que acessam o dispositivo.
- **Requisitos de rede para o dispositivo** – fornece informações sobre os requisitos de rede da operação do dispositivo físico.

## <a name="supported-os-for-clients-connected-to-device"></a>Sistema operacional com suporte para clientes conectados ao dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocolos com suporte para clientes que acessam o dispositivo

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Contas de armazenamento com suporte

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Tipos de armazenamento com suporte

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Navegadores com suporte para a interface do usuário da Web local

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Requisitos de porta de rede

### <a name="port-requirements-for-data-box-edge"></a>Requisitos de porta para Data Box Edge

A tabela a seguir lista as portas que devem ser abertas no firewall para permitir o tráfego SMB, de nuvem ou de gerenciamento. Nesta tabela, *entrada* ou *de entrada* refere-se à direção da qual as solicitações do cliente acessam o dispositivo. *Saída* ou *de saída* referem-se à direção para a qual seu dispositivo do Data Box Edge envia dados externamente, além da implantação, por exemplo, saída para a internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Requisitos de porta para IoT Edge

O Azure IoT Edge permite a comunicação de saíde de um dispositivo do Edge local com a nuvem do Azure usando os protocolos do Hub IoT com suporte. A comunicação de entrada só é necessária para cenários específicos em que o Hub IoT do Azure precisa enviar mensagens por push para o dispositivo do Azure IoT Edge (por exemplo, mensagens de nuvem a dispositivo).

Use a tabela a seguir para a configuração de porta dos servidores que hospedam o runtime do Azure IoT Edge:

| Nº da porta | Entrada ou saída | Escopo da porta | Obrigatório | Orientação |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Saída       | WAN        | Sim      | Saída aberta para o provisionamento do IoT Edge. Essa configuração é necessária ao usar scripts manuais ou o DPS (serviço de provisionamento de dispositivos) do Azure IoT.|

Para todas as informações, acesse [Regras de configuração de firewall e de porta para implantação do IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall

Os administradores de rede geralmente podem configurar regras avançadas de firewall com base nos padrões de URL para filtrar o tráfego de entrada e de saída. Seu dispositivo do Data Box Edge e o serviço dependem de outros aplicativos da Microsoft, como o Barramento de Serviço do Azure, o Controle de Acesso do Azure Active Directory, contas de armazenamento e servidores do Microsoft Update. Os padrões de URL associados a esses aplicativos podem ser usados para configurar regras de firewall. É importante entender que os padrões de URL associados a esses aplicativos podem ser alterados. Essas mudanças exigem que o administrador de rede monitore e atualize as regras de firewall do Data Box Edge conforme e quando necessário.

É recomendado que você defina suas regras de firewall para tráfego de saída, com base nos endereços IP fixos do Data Box Edge e, na maioria dos casos, de modo flexível. No entanto, você pode usar as informações a seguir para definir regras avançadas de firewall que são necessárias para criar ambientes seguros.

> [!NOTE]
> - Os IPs do dispositivo (de origem) sempre devem estar configurados para todas as interfaces de rede habilitadas para a nuvem.
> - Os IPs de destino devem ser configurados como [Intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Padrões de URL para o recurso de gateway

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Padrões de URL para o recurso de computação

| Padrão de URL                      | Componente ou funcionalidade                     |   
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Registro de Contêiner da Microsoft (obrigatório)               |
| https://\*.azurecr.io                     | Registros de contêiner pessoal e de terceiros (opcional) | 
| https://\*.azure-devices.net              | Acesso do Hub IoT (obrigatório)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Padrões de URL para gateway para o Governo Azure

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Padrões de URL para computação para o Governo Azure

| Padrão de URL                      | Componente ou funcionalidade                     |  
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://.cdn.mscr.com.\* | Registro de Contêiner da Microsoft (obrigatório)               |
| https://\*(azure-devices.us).              | Acesso do Hub IoT (obrigatório)           |
| https://\*azurecr.us                    | Registros de contêiner pessoal e de terceiros (opcional) | 

## <a name="internet-bandwidth"></a>Largura de banda de Internet

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Computação de considerações de dimensionamento

Use sua experiência enquanto desenvolve e testa sua solução para garantir que haja capacidade suficiente em seu dispositivo Data Box Edge e obtenha o desempenho ideal do seu dispositivo.

Os fatores que você deve considerar incluem:

- **Especificidades do contêiner** - Pense no seguinte.

    - Quantos contêineres estão na sua carga de trabalho? Você poderia ter um monte de recipientes leves contra alguns com recursos intensivos.
    - Quais são os recursos alocados nesses contêineres versus quais são os recursos que eles estão consumindo?
    - Quantas camadas seus contêineres compartilham?
    - Há recipientes não utilizados? Um recipiente parado ainda ocupa espaço em disco.
    - Em que língua seus recipientes estão escritos?
- **Tamanho dos dados processados** - Quantos dados seus contêineres estarão processando? Esses dados consumirão espaço em disco ou os dados serão processados na memória?
- **Desempenho esperado** - Quais são as características de desempenho desejadas da sua solução? 

Para entender e refinar o desempenho da sua solução, você poderia usar:

- As métricas de computação disponíveis no portal Azure. Vá para o recurso Data Box Edge e, em seguida, vá para **Monitoramento > Métricas**. Olhe para o **edge compute - Uso de memória** e edge **compute - CPU percentual** para entender os recursos disponíveis e como os recursos estão sendo consumidos.
- Os comandos de monitoramento disponíveis através da interface PowerShell do dispositivo, tais como:

    - `dkrdbe stats`para obter uma transmissão ao vivo de estatísticas de uso de recursos de contêineres. O comando suporta métricas de CPU, uso de memória, limite de memória e IO de rede.
    - `dkrdbe system df`para obter informações sobre a quantidade de espaço em disco utilizado. 
    - `dkrdbe image prune`para limpar imagens não utilizadas e liberar espaço.
    - `dkrdbe ps --size`para ver o tamanho aproximado de um recipiente em execução. 

    Para obter mais informações sobre os comandos disponíveis, vá para [Monitorar e solucionar módulos de computação](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules).

Finalmente, certifique-se de validar sua solução no conjunto de dados e quantificar o desempenho no Data Box Edge antes de implantar na produção.


## <a name="next-step"></a>Próxima etapa

- [Implante seu Azure Data Box Edge](data-box-edge-deploy-prep.md)
