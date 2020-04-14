---
title: Perguntas frequentes
description: Respostas para perguntas frequentes relacionadas ao serviço Azure Container Instances
author: dkkapur
ms.topic: article
ms.date: 04/10/2020
ms.openlocfilehash: 8730dcb24af61730d7f93ea37a53cf87435eb9f9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261611"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Perguntas frequentes sobre instâncias de contêineres do Azure

Este artigo aborda perguntas frequentes sobre instâncias de contêineres do Azure.

## <a name="deployment"></a>Implantação

### <a name="how-large-can-my-container-image-be"></a>Quão grande pode ser minha imagem de contêiner?

O tamanho máximo para uma imagem de contêiner implantável em Instâncias de Contêiner do Azure é de 15 GB. Você pode ser capaz de implantar imagens maiores dependendo da disponibilidade exata no momento em que você implantar, mas isso não é garantido.

O tamanho da imagem do seu contêiner impacta o tempo que leva para ser implantado, então geralmente você quer manter as imagens do contêiner o menor possível.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Como posso acelerar a implantação do meu contêiner?

Como um dos principais determinantes dos tempos de implantação é o tamanho da imagem, procure maneiras de reduzir o tamanho. Remova camadas que você não precisa ou reduza o tamanho das camadas na imagem (escolhendo uma imagem do sistema operacional base mais leve). Por exemplo, se você estiver executando contêineres Linux, considere usar o Alpine como sua imagem base em vez de um Servidor Ubuntu completo. Da mesma forma, para contêineres Windows, use uma imagem base do Nano Server, se possível. 

Você também deve verificar a lista de imagens pré-armazenadas em cache no Azure Container Images, disponível através da API [List Cached Images.](/rest/api/container-instances/listcachedimages) Você pode ser capaz de trocar uma camada de imagem por uma das imagens pré-cacheadas. 

Veja [orientações](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) mais detalhadas sobre a redução do tempo de inicialização do contêiner.

### <a name="what-windows-base-os-images-are-supported"></a>Quais imagens do Sistema Operacional base do Windows são suportadas?

#### <a name="windows-server-2016-base-images"></a>Imagens base do Windows Server 2016

* [Nano](https://hub.docker.com/_/microsoft-windows-nanoserver)Servidor `10.0.14393.x`:`sac2016`
* [Núcleo do servidor do Windows:](https://hub.docker.com/_/microsoft-windows-servercore) `ltsc2016``10.0.14393.x`

> [!NOTE]
> As imagens do Windows baseadas na versão semi-anual do Canal 1709 ou 1803 não são suportadas.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 e imagens da base do cliente (visualização)

* [Nano](https://hub.docker.com/_/microsoft-windows-nanoserver)Server `1809` `10.0.17763.914` : , ou anterior
* [Núcleo do](https://hub.docker.com/_/microsoft-windows-servercore) `ltsc2019`Servidor do Windows : , `1809`ou `10.0.17763.914` anterior
* [Janelas](https://hub.docker.com/_/microsoft-windows) `1809`: `10.0.17763.914` , ou anterior

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Qual camada de imagem .NET ou .NET Core devo usar no meu recipiente? 

Use a menor imagem que satisfaça suas necessidades. Para Linux, você pode usar uma imagem *runtime-alpine* .NET Core, que tem sido suportada desde o lançamento do .NET Core 2.1. Para o Windows, se você estiver usando o Framework .NET completo, então você precisa usar uma imagem do Windows Server Core (imagem somente em tempo de execução, como *4.7.2-windowsservercore-ltsc2016*). As imagens somente em tempo de execução são menores, mas não suportam cargas de trabalho que requerem o SDK .NET.

## <a name="availability-and-quotas"></a>Disponibilidade e cotas

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Quantos núcleos e memória devo alocar para meus contêineres ou para o grupo de contêineres?

Isso depende da sua carga de trabalho. Inicie o desempenho pequeno e teste para ver como seus contêineres se saem. [Monitore o uso de recursos de CPU e memória](container-instances-monitor.md)e adicione núcleos ou memória com base no tipo de processos que você implanta no contêiner. 

Certifique-se também de verificar a disponibilidade de [recursos](container-instances-region-availability.md#availability---general) para a região que você está implantando para os limites superiores nos núcleos da CPU e memória disponível por grupo de contêineres. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Em que infra-estrutura subjacente a ACI funciona?

O Azure Container Instances pretende ser um serviço sem servidor de contêineres sob demanda, por isso queremos que você esteja focado no desenvolvimento de seus contêineres e não se preocupe com a infra-estrutura! Para aqueles que estão curiosos ou querem fazer comparações sobre desempenho, a ACI roda em conjuntos de VMs Azure de várias SKUs, principalmente das séries F e D. Esperamos que isso mude no futuro à medida que continuamos a desenvolver e otimizar o serviço. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Eu quero implantar milhares de núcleos na ACI - posso aumentar minha cota?
 
Sim (às vezes). Veja as [cotas e limites](container-instances-quotas.md) do artigo para cotas atuais e quais limites podem ser aumentados por solicitação.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Posso implantar com mais de 4 núcleos e 16 GB de RAM?

Ainda não. Atualmente, estes são os máximos para um grupo de contêineres. Entre em contato com o Suporte do Azure com requisitos ou solicitações específicas. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Quando a ACI estará em uma região específica?

A disponibilidade atual da região é publicada [aqui.](container-instances-region-availability.md#availability---general) Se você tiver um requisito para uma região específica, entre em contato com o Suporte Azure.

## <a name="features-and-scenarios"></a>Recursos e cenários

### <a name="how-do-i-scale-a-container-group"></a>Como dimensionar um grupo de contêineres?

Atualmente, o dimensionamento não está disponível para contêineres ou grupos de contêineres. Se você precisar executar mais instâncias, use nossa API para automatizar e criar mais solicitações para criação de grupos de contêineres para o serviço. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Quais recursos estão disponíveis para instâncias em execução em um VNet personalizado?

Você pode [implantar grupos de contêineres em uma rede virtual azure](container-instances-vnet.md) de sua escolha e delegar IPs privados para os grupos de contêineres para direcionar o tráfego dentro do VNet através de seus recursos do Azure. A implantação de um grupo de contêineres em uma rede virtual está atualmente disponível para cargas de trabalho de produção em um subconjunto de regiões do Azure.

## <a name="pricing"></a>Preços

### <a name="when-does-the-meter-start-running"></a>Quando o taxímetro começa a funcionar?

A duração do grupo de contêineres é calculada a partir do momento em que começamos a puxar a imagem do primeiro contêiner (para uma nova implantação) ou o grupo de contêineres é reiniciado (se já implantado), até que o grupo de contêineres seja interrompido. Veja detalhes em [Container Instances preços](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Eu paro de ser carregado quando meus contêineres são parados?

Os medidores param de funcionar assim que todo o grupo de contêineres for interrompido. Enquanto um contêiner em seu grupo de contêineres estiver funcionando, mantemos os recursos no caso de você querer ligar os contêineres novamente. 

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais](container-instances-overview.md) sobre o Azure Container Instances.
* [Solucionando problemas comuns](container-instances-troubleshooting.md) em Instâncias de Contêiner do Azure.