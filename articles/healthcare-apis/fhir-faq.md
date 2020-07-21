---
title: Perguntas frequentes sobre os serviços FHIRs no Azure – API do Azure para FHIR
description: Obtenha respostas para perguntas frequentes sobre a API do Azure para FHIR, como o local de armazenamento de dados por trás de APIs do FHIR e suporte de versão.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: af891935fd474e6f1f83ff1c2ce56ef71cd065c6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536718"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Perguntas frequentes sobre a API do Azure para FHIR

## <a name="azure-api-for-fhir"></a>API do Azure para FHIR

### <a name="what-is-fhir"></a>O que é o FHIR?
Os recursos de interoperabilidade do Fast Healthcare (FHIR-pronunciado "incêndio") são um padrão de interoperabilidade destinado a habilitar a troca de dados de saúde entre diferentes sistemas de integridade. Esse padrão foi desenvolvido pela organização HL7 e está sendo adotado pelas organizações de saúde em todo o mundo. A versão mais atual do FHIR disponível é R4 (versão 4). A API do Azure para FHIR dá suporte a R4 e também dá suporte à versão anterior STU3 (Standard para avaliação, use 3). Para obter mais informações sobre o FHIR, visite [HL7.org](http://hl7.org/fhir/summary.html).

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Os dados estão por trás das APIs do FHIR armazenadas no Azure?

Sim, os dados são armazenados em bancos de dados gerenciados no Azure. A API do Azure para FHIR não fornece acesso direto ao armazenamento de dados subjacente.

### <a name="what-identity-provider-do-you-support"></a>A qual provedor de identidade você dá suporte?

Atualmente, damos suporte a Microsoft Azure Active Directory como o provedor de identidade.

### <a name="what-fhir-version-do-you-support"></a>A qual versão do FHIR você dá suporte?

Damos suporte às versões 4.0.0 e 3.0.1 tanto na API do Azure para FHIR (PaaS) quanto no servidor FHIR para o Azure (código aberto).

Para obter detalhes, consulte [recursos com suporte](fhir-features-supported.md). Leia sobre o que mudou entre as versões no [histórico de versão para HL7 FHIR](https://hl7.org/fhir/R4/history.html).

### <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Qual é a diferença entre o Microsoft FHIR Server de software livre para o Azure e a API do Azure para FHIR?

A API do Azure para FHIR é uma versão hospedada e gerenciada do Microsoft FHIR Server de software livre para o Azure. No serviço gerenciado, a Microsoft fornece todas as atualizações e manutenção. 

Quando estiver executando o FHIR Server para Azure, você terá acesso direto aos serviços subjacentes. Mas você também é responsável por manter e atualizar o servidor e todo o trabalho de conformidade necessário se estiver armazenando PHI dados.

Do ponto de vista do desenvolvimento, todos os recursos são implantados no Microsoft FHIR Server de software livre para o Azure primeiro. Depois de ser validado em código-fonte aberto, ele será liberado para a solução de PaaS API do Azure para FHIR. O tempo entre a versão em software livre e PaaS depende da complexidade do recurso e de outras prioridades de roteiro. 

### <a name="what-is-smart-on-fhir"></a>O que é inteligente no FHIR?

SMART (aplicativos médicos substituíveis e tecnologia reutilizável) em FHIR é um conjunto de especificações abertas para integrar aplicativos de parceiros com servidores FHIR e outros sistemas de ti de saúde, como registros eletrônicos de integridade e trocas de informações de integridade. Ao criar um aplicativo inteligente no FHIR, você pode garantir que seu aplicativo possa ser acessado e utilizado por uma infinidade de sistemas diferentes.
Autenticação e API do Azure para FHIR. Para saber mais sobre o SMART, visite o [Smart Health IT](https://smarthealthit.org/).


## <a name="iot-connector-preview"></a>Conector IoT (visualização)

### <a name="what-is-iomt"></a>O que é o IoMT?
IoMT significa Internet de coisas médicas e é uma categoria de dispositivos IoT que capturam e trocam dados de saúde e bem-estar com outros sistemas de ti de saúde em uma rede. Alguns exemplos de dispositivos IoMT incluem adequação e wearables clínica, sensores de monitoramento, rastreadores de atividade, quiosques de ponto de atendimento ou até mesmo um Smart Pill.

### <a name="how-many-iot-connectors-do-i-need"></a>De quantos conectores IoT eu preciso?
Um único conector IoT pode ser usado para ingerir dados de um grande número de tipos diferentes de dispositivos. Você ainda pode optar por usar conectores diferentes pelos seguintes motivos:
- **Escala**: para visualização pública, a capacidade do recurso do conector IOT é fixa e esperada para fornecer uma taxa de transferência de cerca de 200 mensagens por segundo. Você pode adicionar mais conectores IoT, se uma taxa de transferência maior for necessária.
- **Tipo de dispositivo**: você pode configurar um conector IOT separado para cada tipo de dispositivos IoMT que você tem por motivos de gerenciamento de dispositivo.

### <a name="is-there-a-limit-on-number-of-iot-connectors-during-public-preview"></a>Há um limite para o número de conectores IoT durante a visualização pública?
Sim, você pode criar apenas dois conectores IoT por assinatura, enquanto o recurso está em visualização pública. Esse limite existe para evitar despesas inesperadas, pois o recurso está disponível gratuitamente durante a visualização. Por solicitação, esse limite pode ser elevado até um máximo de cinco conectores IoT.

### <a name="what-azure-regions-iot-connector-feature-is-available-during-public-preview"></a>Qual recurso do conector IoT das regiões do Azure está disponível durante a visualização pública?
O conector IoT está disponível em todas as regiões do Azure em que a API do Azure para FHIR está disponível.

### <a name="can-i-configure-scaling-capacity-for-iot-connector"></a>Posso configurar a capacidade de dimensionamento para o conector IoT?
Como o conector IoT é gratuito durante a visualização pública, sua capacidade de dimensionamento é fixa e limitada. A configuração do conector IoT disponível na visualização pública é esperada para fornecer uma taxa de transferência de cerca de 200 mensagens por segundo. Alguma forma de configuração de capacidade de recursos será disponibilizada em GA (disponibilidade geral).

### <a name="what-fhir-version-does-iot-connector-support"></a>A qual versão FHIR o conector IoT dá suporte?
O conector IoT atualmente dá suporte apenas à versão R4 do FHIR. Portanto, esse recurso só é visível nas instâncias de R4 da API do Azure para FHIR e a Microsoft não planeja oferecer suporte à versão STU3 no momento.

### <a name="why-cant-i-install-iot-connector-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Por que não consigo instalar o conector IoT quando o link privado está habilitado na API do Azure para FHIR?
O conector IoT não dá suporte à funcionalidade de link privado no momento. Portanto, se você tiver o link privado habilitado na API do Azure para FHIR, não será possível instalar o conector IoT e vice-versa. Essa limitação deve desaparecer quando o conector IoT está disponível para GA (disponibilidade geral).

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-iot-connector-feature-of-azure-api-for-fhir-service"></a>Qual é a diferença entre o conector IoMT FHIR do software livre para o Azure e o recurso conector do IoT da API do Azure para o serviço FHIR?
O conector IoT é uma versão hospedada e gerenciada do conector de FHIR do IoMT de software livre para o Azure. No serviço gerenciado, a Microsoft fornece todas as atualizações e manutenção.

Quando estiver executando o conector do IoMT FHIR para o Azure, você terá acesso direto aos recursos subjacentes. Mas você também é responsável por manter e atualizar o servidor e todo o trabalho de conformidade necessário se estiver armazenando PHI dados.

Do ponto de vista do desenvolvimento, cada recurso é implantado no conector IoMT FHIR de código-fonte aberto para o Azure primeiro. Depois de ser validado em código-fonte aberto, ele será liberado para o recurso de conector de IoT PaaS da API do Azure para o serviço FHIR. O tempo entre o lançamento em software livre e PaaS depende da complexidade do recurso e de outras prioridades de mapa de estrada.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você leu algumas das perguntas frequentes sobre a API do Azure para FHIR. Leia sobre os recursos com suporte no servidor FHIR para o Azure:
 
>[!div class="nextstepaction"]
>[Recursos de FHIR com suporte](fhir-features-supported.md)