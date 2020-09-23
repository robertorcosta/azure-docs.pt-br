---
title: Perguntas frequentes sobre os serviços FHIRs no Azure – API do Azure para FHIR
description: Obtenha respostas para perguntas frequentes sobre a API do Azure para FHIR, como o local de armazenamento de dados por trás de APIs do FHIR e suporte de versão.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2020
ms.author: matjazl
ms.openlocfilehash: 36945d998bf00d7b229b5ae3cce1958953ade601
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90978612"
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

### <a name="can-i-create-a-custom-fhir-resource"></a>Posso criar um recurso personalizado do FHIR?

Não permitimos recursos de FHIR personalizados. Se precisar de um recurso FHIR personalizado, você poderá criar um recurso personalizado sobre o [recurso básico](http://www.hl7.org/fhir/basic.html) com extensões. 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>Há suporte para [extensões](https://www.hl7.org/fhir/extensibility.html) na API do Azure para FHIR?

Permitimos que você carregue dados JSON FHIR válidos no servidor. Se você quiser armazenar a definição de estrutura que define a extensão, poderá salvá-la como um recurso de definição de estrutura. No momento, não é possível Pesquisar nas extensões.

### <a name="what-is-the-limit-on-_count"></a>Qual é o limite de _count?

O limite atual da contagem é 100.

### <a name="are-there-any-limitations-on-the-group-export-functionality"></a>Há alguma limitação sobre a funcionalidade de exportação de grupo?

Para a exportação de grupo, só exportamos as referências incluídas do grupo, e não todas as características do [recurso de grupo](https://www.hl7.org/fhir/group.html).


## <a name="azure-iot-connector-for-fhir-preview"></a>Conector IoT do Azure para FHIR (versão prévia)

### <a name="what-is-iomt"></a>O que é o IoMT?
IoMT significa Internet de coisas médicas e é uma categoria de dispositivos IoT que capturam e trocam dados de saúde e bem-estar com outros sistemas de ti de saúde em uma rede. Alguns exemplos de dispositivos IoMT incluem dispositivos fitness e clínicos vestíveis, sensores de monitoramento, rastreadores de atividade, quiosques de atendimento ou até mesmo uma pílula inteligente.

### <a name="how-many-azure-iot-connector-for-fhir-preview-do-i-need"></a>Quantos conectores do Azure IoT para FHIR (versão prévia) preciso?
Um único conector do Azure IoT para FHIR * pode ser usado para ingerir dados de um grande número de tipos de dispositivos diferentes. Você ainda pode optar por usar conectores diferentes pelos seguintes motivos:
- **Escala**: para visualização pública, o conector IOT do Azure para capacidade de recurso FHIR é fixo e esperado para fornecer uma taxa de transferência de cerca de 200 mensagens por segundo. Você pode adicionar mais conector do Azure IoT para FHIR, se for necessário uma taxa de transferência maior.
- **Tipo de dispositivo**: você pode configurar um conector do Azure IOT separado para FHIR para cada tipo de dispositivos IoMT que você tem por motivos de gerenciamento de dispositivo.

### <a name="is-there-a-limit-on-number-of-azure-iot-connector-for-fhir-preview-during-public-preview"></a>Há um limite para o número de conectores do Azure IoT para FHIR (versão prévia) durante a visualização pública?
Sim, você pode criar apenas dois conectores IoT do Azure para FHIR por assinatura, enquanto o recurso está em visualização pública. Esse limite existe para evitar despesas inesperadas, pois o recurso está disponível gratuitamente durante a visualização. Por solicitação, esse limite pode ser elevado até um máximo de cinco conectores do Azure IoT para FHIR.

### <a name="what-azure-regions-azure-iot-connector-for-fhir-preview-feature-is-available-during-public-preview"></a>Qual recurso do conector do Azure IoT para regiões do Azure para FHIR (versão prévia) está disponível durante a visualização pública?
O conector do Azure IoT para FHIR está disponível em todas as regiões do Azure em que a API do Azure para FHIR está disponível.

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>Posso configurar a capacidade de dimensionamento para o conector do Azure IoT para FHIR (versão prévia)?
Como o conector do Azure IoT para FHIR é gratuito durante a visualização pública, sua capacidade de dimensionamento é fixa e limitada. O conector IoT do Azure para a configuração FHIR disponível na visualização pública é esperado para fornecer uma taxa de transferência de cerca de 200 mensagens por segundo. Alguma forma de configuração de capacidade de recursos será disponibilizada em GA (disponibilidade geral).

### <a name="what-fhir-version-does-azure-iot-connector-for-fhir-preview-support"></a>Qual versão do FHIR o conector do Azure IoT para o suporte do FHIR (versão prévia)?
O conector do Azure IoT para FHIR atualmente dá suporte apenas à versão FHIR R4. Portanto, esse recurso só é visível nas instâncias de R4 da API do Azure para FHIR e a Microsoft não planeja oferecer suporte à versão STU3 no momento.

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Por que não consigo instalar o conector do Azure IoT para FHIR (versão prévia) quando o link privado está habilitado na API do Azure para FHIR?
O conector IoT do Azure para FHIR não dá suporte à funcionalidade de link privado no momento. Portanto, se você tiver o link privado habilitado na API do Azure para FHIR, não será possível instalar o conector do Azure IoT para FHIR e vice-versa. Essa limitação deve desaparecer quando o conector do Azure IoT para FHIR estiver disponível para GA (disponibilidade geral).

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-azure-iot-connector-for-fhir-preview-feature-of-azure-api-for-fhir-service"></a>Qual é a diferença entre o conector IoMT FHIR de software livre para o Azure e o Azure IoT Connector para o recurso FHIR (versão prévia) da API do Azure para o serviço FHIR?
O conector IoT do Azure para FHIR é uma versão hospedada e gerenciada do conector IoMT FHIR de software livre para o Azure. No serviço gerenciado, a Microsoft fornece todas as atualizações e manutenção.

Quando estiver executando o conector do IoMT FHIR para o Azure, você terá acesso direto aos recursos subjacentes. Mas você também é responsável por manter e atualizar o servidor e todo o trabalho de conformidade necessário se estiver armazenando PHI dados.

Do ponto de vista do desenvolvimento, cada recurso é implantado no conector IoMT FHIR de código-fonte aberto para o Azure primeiro. Depois de ser validado em código-fonte aberto, ele será liberado para o recurso do conector de IoT do Azure para FHIR da API do Azure para o serviço FHIR. O tempo entre o lançamento em software livre e PaaS depende da complexidade do recurso e de outras prioridades de mapa de estrada.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você leu algumas das perguntas frequentes sobre a API do Azure para FHIR. Leia sobre os recursos com suporte no servidor FHIR para o Azure:
 
>[!div class="nextstepaction"]
>[Recursos de FHIR com suporte](fhir-features-supported.md)

*No portal do Azure, o Conector IoT do Azure para FHIR é chamado de Conector IoT (versão prévia).

FHIR é uma marca registrada da HL7, usada com permissão da HL7.