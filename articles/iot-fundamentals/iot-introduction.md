---
title: Introdução ao Azure IoT (Internet das Coisas)
description: Introdução explicando os conceitos básicos do Azure IoT e os serviços de IoT, incluindo exemplos que ajudam a ilustrar o uso de IoT.
author: robinsh
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 10/11/2018
ms.author: robinsh
ms.openlocfilehash: ce82a2cc4cc936d2e0a7a8b82cbc0ed7e5c6eb52
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048643"
---
# <a name="what-is-azure-internet-of-things-iot"></a>O que é o Azure IoT (Internet das Coisas)?

O Azure IoT (Internet das Coisas) é uma coleção de serviços de nuvem gerenciados pela Microsoft que conectam, monitoram e controlam bilhões de ativos de IoT. Simplificando, uma solução de IoT é composta por um ou mais dispositivos IoT, e por um ou mais serviços de back-end em execução na nuvem, os quais se comunicam entre si. 

Este artigo aborda os conceitos básicos de IoT, fala sobre casos de uso e explica resumidamente os oito serviços disponíveis. Se você entender o que está disponível, poderá escolher que deseja examinar mais detalhadamente a fim de ajudar a projetar seu cenário.

## <a name="introduction"></a>Introdução

As principais partes de uma solução de IoT são estas: dispositivos, serviços de back-end e as comunicações entre os dois. 

### <a name="iot-devices"></a>Dispositivos IoT

Em geral, os dispositivos são compostos por uma placa de circuitos com sensores anexados que se conectam à Internet. Muitos dispositivos se comunicam por meio de um chip de Wi-Fi. Confira alguns exemplos de dispositivos IoT:

* sensores de pressão em uma bomba de petróleo remota
* sensores de temperatura e umidade em uma unidade de ar-condicionado
* acelerômetros em um elevador
* sensores de presença em uma sala

Dois dispositivos usados com frequência na criação de protótipos são o Kit de desenvolvimento MX Chip IoT básico da Microsoft e dispositivos Raspberry PI. O Kit de desenvolvimento MX Chip tem sensores internos de temperatura, pressão e umidade, além de um giroscópio e um acelerômetro, um magnetômetro e um chip de Wi-Fi. O Raspberry PI é um dispositivo IoT ao qual você pode anexar vários tipos de sensores diferentes, para que você possa selecionar exatamente o necessário para o seu cenário. 

Para obter mais informações sobre os dispositivos IoT disponíveis, confira o maior [catálogo de dispositivos certificados para IoT](https://catalog.azureiotsolutions.com/alldevices) do setor.

Os [SDKs de dispositivos IoT](../iot-hub/iot-hub-devguide-sdks.md) permitem que você crie aplicativos que são executados em seus dispositivos, para que possam executar as tarefas necessárias. Com os SDKs, você pode enviar telemetria ao seu hub IoT, receber mensagens e atualizações do Hub IoT e assim por diante.

### <a name="communication"></a>Comunicação

Seu dispositivo pode se comunicar com serviços de back-end em ambas as direções. Confira alguns exemplos de como o dispositivo pode se comunicar com a solução de back-end.

#### <a name="examples"></a>Exemplos 

* Seu dispositivo pode enviar a temperatura de um caminhão refrigerado a cada cinco minutos para um Hub IoT. 

* O serviço de back-end pode pedir ao dispositivo para enviar a telemetria com maior frequência para ajudar a diagnosticar um problema. 

* O dispositivo pode enviar alertas com base nos valores obtidos nos sensores. Por exemplo, ao monitorar um reator de lotes em uma usina química, convém enviar um alerta quando a temperatura exceder determinado valor.

* Seu dispositivo pode enviar informações a um painel para conferência de operadores humanos. Por exemplo, uma sala de controle em uma refinaria pode mostrar a temperatura e a pressão de cada tubulação, além do volume que passa pela tubulação, permitindo que os operadores assistam ao processo. 

Essas tarefas, e muitas outras, podem ser implementadas usando os [SDKs de dispositivos IoT](../iot-hub/iot-hub-devguide-sdks.md).

#### <a name="connection-considerations"></a>Considerações sobre conexão

Conectar os dispositivos de modo seguro e confiável geralmente é o maior desafio em soluções de IoT. Isso porque os dispositivos IoT têm características diferentes em comparação com outros clientes, como navegadores e aplicativos móveis. Especificamente, os dispositivos IoT:

* Frequentemente são sistemas internos sem operadores humanos (diferente de um telefone).

* Eles podem ser implantados em locais remotos, nos quais o acesso físico é caro.

* Só podem ser acessados por meio do back-end da solução. Não há outra maneira de interagir com o dispositivo.

* Podem ter recursos de energia e de processamento limitados.

* Podem ter conectividade de rede intermitente, lenta ou cara.

* Talvez precisem de protocolos de aplicativo proprietários, personalizados ou específicos do setor.

### <a name="back-end-services"></a>Serviços de back-end 

Veja algumas das funções que um serviço de back-end pode fornecer.

* Receber telemetria em escala de seus dispositivos e determinar como processar e armazenar os dados.

* Analisar a telemetria para fornecer insights, seja em tempo real ou após o ocorrido.

* Enviar comandos da nuvem para um dispositivo específico. 

* Provisionar dispositivos e controlar quais dispositivos podem se conectar à sua infraestrutura.

* Gerenciar o estado dos dispositivos e monitorar suas atividades.

Por exemplo, em um cenário de manutenção preditiva, o back-end da nuvem armazena o histórico de telemetria. A solução usa esses dados para identificar comportamentos anormais potenciais em bombas específicas antes que causem um problema real. Usando a análise de dados, é possível identificar que a solução preventiva é enviar um comando de volta para o dispositivo para realizar uma ação corretiva. Esse processo gera um loop de comentários automatizado entre o dispositivo e a nuvem, o que aumenta a eficiência da solução.

## <a name="an-iot-example"></a>Um exemplo de IoT

Aqui está um exemplo de como uma empresa usou IoT para economizar milhões de dólares. 

Há uma fazenda de gado enorme com centenas de milhares de vacas. É muito complicado controlar todas essas vacas, saber como elas estão, e isso requer uma grande circulação. Eles anexaram sensores a cada vaca, os quais enviam informações como as coordenadas GPS e a temperatura para um serviço de back-end que as grava em um banco de dados.

Em seguida, eles usam um serviço de análise que verifica os dados recebidos e analisa os dados de cada vaca para verificar o seguinte:

* A vaca está com febre? Há quanto tempo a vaca está com febre? Se isso está ocorrendo há mais de um dia, obtenha as coordenadas GPS, encontre a vaca e, se necessário, trate-a com antibióticos. 

* A vaca está no mesmo lugar há mais de um dia? Nesse caso, obtenha as coordenadas GPS e encontre a vaca. A vaca caiu de um precipício? A vaca está ferida? A vaca precisa de ajuda? 

A implementação dessa solução de IoT permitiu que a empresa verificasse e tratasse as vacas rapidamente, reduzindo o tempo gasto pelos funcionários dirigindo pela área para verificar os animais e economizando muito dinheiro. Para obter mais exemplos reais de como as empresas usam IoT, confira [Estudos de casos técnicos da Microsoft sobre IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

## <a name="iot-services"></a>Serviços IoT

Há vários serviços relacionados à IoT no Azure, e pode ser complexo saber qual deles é o ideal. Alguns deles, como o IoT Central e os aceleradores de solução de IoT, fornecem modelos para ajudar você a criar sua própria solução e começar a trabalhar rapidamente. Você também pode desenvolver totalmente suas próprias soluções usando outros serviços disponíveis; tudo depende do grau de ajuda e de controle desejado. Confira uma lista dos serviços disponíveis e para que eles servem.

1. [**IoT Central**](../iot-central/core/overview-iot-central.md): essa é uma plataforma de aplicativo IoT que simplifica a criação de soluções IoT, bem como ajuda a reduzir a carga e o custo das operações de gerenciamento e desenvolvimento da IoT. Para começar, selecione um modelo para o tipo de dispositivo, crie e teste um aplicativo básico do IoT Central que será usado pelos operadores do dispositivo. O aplicativo do IoT Central também permitirá que você monitore os dispositivos e provisione novos dispositivos. Esse serviço destina-se a soluções simples que não exigem personalização avançada do serviço.

2. [**Aceleradores de solução de IoT**](/azure/iot-suite): É uma coleção das soluções de PaaS que você pode usar para acelerar o desenvolvimento de uma solução de IoT. Comece com uma solução IoT fornecida e a personalize totalmente de acordo com suas necessidades. Você precisa ter conhecimento de Java ou .NET para personalizar o back-end, e ter conhecimento de JavaScript para personalizar a visualização. 

3. [**Hub IoT**](/azure/iot-hub/): Esse serviço permite que você conecte seus dispositivos a um hub IoT e monitore e controle bilhões de dispositivos IoT. Isso é especialmente útil se você precisa de comunicação bidirecional entre os dispositivos IoT e o back-end. Esse é o serviço subjacente do IoT Central e dos aceleradores de solução IoT. 

4. [**Serviço de Provisionamento de Dispositivos no Hub IoT**](/azure/iot-dps/): É um serviço auxiliar do Hub IoT que você pode usar para provisionar dispositivos no hub IoT com segurança. Com esse serviço, você pode provisionar milhões de dispositivos rapidamente e com facilidade, em vez de provisioná-los individualmente. 

5. [**IoT Edge**](/azure/iot-edge/): Esse serviço se baseia no Hub IoT. Ele pode ser usado para analisar dados em dispositivos IoT em vez de na nuvem. Ao mover partes da sua carga de trabalho para a borda, menos mensagens precisam ser enviadas para a nuvem. 

6. [**Gêmeos Digitais do Azure**](../digital-twins/index.yml): Esse serviço permite que você crie modelos abrangentes do ambiente físico. Você pode modelar os relacionamentos e as interações entre pessoas, espaços e dispositivos. Por exemplo, você pode prever as necessidades de manutenção de uma fábrica, analisar os requisitos de energia em tempo real para uma rede elétrica ou otimizar o uso de espaço disponível em um escritório.

7. [**Time Series Insights**](/azure/time-series-insights): Esse serviço permite que você armazene, visualize e consulte grandes quantidades de dados de série temporal gerados por dispositivos IoT. Você pode usar esse serviço com o Hub IoT. 

8. [**Azure Mapas**](/azure/azure-maps): Esse serviço fornece informações geográficas para aplicativos Web e móveis. Há um conjunto completo de APIs REST e um controle JavaScript baseado na Web que pode ser usado para criar aplicativos flexíveis que funcionam em aplicativos da área de trabalho ou móveis para dispositivos da Apple e do Windows.

## <a name="next-steps"></a>Próximas etapas

Para ver alguns casos de negócios reais e a arquitetura usada, confira os [Estudos de casos técnicos do Microsoft Azure IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured).

Para alguns projetos de exemplo que você pode experimentar com um Kit de desenvolvimento de IoT, confira o [Catálogo de projetos do Kit de desenvolvimento de IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/). 

Para obter uma explicação abrangente sobre os diferentes serviços e como eles são usados, confira [Tecnologias e serviços do Azure IoT](iot-services-and-technologies.md).

Para ver uma análise detalhada da arquitetura IoT, confira a [Arquitetura de Referência do IoT do Microsoft Azure](https://aka.ms/iotrefarchitecture).
