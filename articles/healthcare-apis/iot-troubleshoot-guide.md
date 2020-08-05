---
title: Conector do Azure IoT para FHIR (visualização)-guia de solução de problemas e instruções
description: Como solucionar problemas de mensagens e condições de erro comuns do conector do Azure IoT para FHIR (versão prévia) e arquivos de mapeamento de cópia
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: jasteppe
ms.openlocfilehash: b404fa5322d3afa8cbf71741382d44dd0433d49c
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553675"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Guia de solução de problemas do conector do Azure IoT para FHIR (versão prévia)

Este artigo fornece etapas para solucionar problemas de mensagens e condições de erro comuns do conector do Azure IoT para FHIR *.  

Você também aprenderá como criar cópias do conector do Azure IoT para arquivos de mapeamento JSON FHIR para edição e arquivamento fora do portal do Azure ou para fornecer ao suporte técnico do Azure ao abrir um tíquete de suporte. 

## <a name="error-messages-and-fixes"></a>Mensagens de erro e correções

|Mensagem   |Condição  |Fix         |
|----------|-----------|------------|
|Nome de mapeamento inválido, o nome de mapeamento deve ser dispositivo ou FHIR|O tipo de mapeamento fornecido não é dispositivo ou FHIR|Use um dos dois tipos de mapeamento com suporte (por exemplo: Device ou FHIR)|
|Regenerar parâmetros de chave não definidos|Regenerar solicitação de chave|Incluir os parâmetros na solicitação de chave de regeneração|
|Atingido o número máximo de instâncias do conector IoT * que podem ser provisionadas nesta assinatura|Conector do Azure IoT para cota de assinatura do FHIR atingido (o padrão é (2) por assinatura)|Excluir uma das instâncias existentes do conector do Azure IoT para FHIR, usar uma assinatura diferente que não atingiu a cota de assinatura ou solicitar um aumento de cota de assinatura|
|O recurso de movimentação não tem suporte para o conector IoT com a API do Azure habilitada para o recurso FHIR|Tentando fazer uma operação de movimentação em uma API do Azure para o recurso FHIR que tem uma ou mais instâncias do conector IoT do Azure para FHIR|Excluir instâncias existentes do conector do Azure IoT para FHIR para executar e concluir a operação de movimentação|
|Conector IoT não provisionado|Tentando usar serviços filho (conexões & mapeamentos) quando o pai (conector do Azure IoT para FHIR) não tiver sido provisionado|Provisionar um conector do Azure IoT para FHIR|
|Não há suporte para a solicitação|Não há suporte para a solicitação de API específica|Usar a solicitação de API correta|
|A conta não existe|A tentativa de adicionar um conector do Azure IoT para FHIR e a API do Azure para o recurso FHIR não existe|Crie a API do Azure para o recurso FHIR e tente a operação novamente|
|A API do Azure para a versão FHIR do recurso FHIR não tem suporte para o conector IoT|Tentando usar um conector do Azure IoT para FHIR com uma versão incompatível da API do Azure para o recurso FHIR|Criar uma nova API do Azure para o recurso FHIR (versão R4) ou usar uma API do Azure existente para o recurso FHIR (versão R4)

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-mapping-files"></a>Criando cópias do conector do Azure IoT para arquivos de mapeamento FHIR (versão prévia)
A cópia do conector do Azure IoT para arquivos de mapeamento FHIR pode ser útil para edição e arquivamento fora do site portal do Azure e para fornecer ao suporte técnico do Azure ao abrir um tíquete de suporte.

> [!NOTE]
> JSON é o único formato com suporte para arquivos de mapeamento de dispositivo e FHIR no momento.

> [!TIP]
> Saiba mais sobre o conector do Azure IoT para [dispositivos FHIR e FHIR mapeando arquivos JSON](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. Selecione **"conector IOT (versão prévia)"** no canto inferior esquerdo da API do Azure para o painel de recursos FHIR na seção **"suplementos"** .

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="Conector IoT" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Selecione o **"conector"** do qual você copiará os arquivos de mapeamento.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="Conector IoT" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Esse processo também pode ser usado para copiar e salvar o conteúdo do JSON **"Configurar mapeamento de FHIR"** .

3. Selecione **"Configurar mapeamento de dispositivo"**.

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="Conector IoT" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Selecione o conteúdo do JSON e faça uma operação de cópia (por exemplo: selecione CTRL + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="Conector IoT" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Faça uma operação de colagem (por exemplo: selecione CTRL + v) em um novo arquivo dentro de um editor (por exemplo: Visual Studio Code, bloco de notas) e salve o arquivo com uma extensão *. JSON.

> [!TIP]
> Se você estiver abrindo um tíquete de [suporte técnico do Azure](https://azure.microsoft.com/support/create-ticket/) para o conector do Azure IOT para FHIR, certifique-se de incluir cópias de seus arquivos de mapeamento para ajudar com o processo de solução de problemas.

## <a name="next-steps"></a>Próximas etapas

Confira as perguntas frequentes sobre o conector do Azure IoT para FHIR.

>[!div class="nextstepaction"]
>[Azure IoT Connector para FHIR FAQs](fhir-faq.md#azure-iot-connector-for-fhir-preview)

* No portal do Azure, o conector do IoT do Azure para FHIR é conhecido como conector IoT (versão prévia).

FHIR é uma marca registrada da HL7, usada com permissão da HL7.