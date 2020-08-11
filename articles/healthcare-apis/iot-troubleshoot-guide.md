---
title: Conector do Azure IoT para FHIR (visualização)-guia de solução de problemas e instruções
description: Como solucionar problemas de mensagens e condições de erro comuns do conector do Azure IoT para FHIR (versão prévia) e copiar arquivos de mapeamento
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: jasteppe
ms.openlocfilehash: 088d1e409f14fdba02311d1ff17eb655f6e41ad3
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053449"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Guia de solução de problemas do conector do Azure IoT para FHIR (versão prévia)

Este artigo fornece etapas para solucionar problemas de mensagens e condições de erro comuns do conector do Azure IoT para FHIR *.  

Você também aprenderá a criar cópias do conector do Azure IoT para mapeamentos de conversão do FHIR JSON (por exemplo: Device e FHIR).  

Você pode usar as cópias JSON de mapeamento de conversão para edição e arquivamento fora do portal do Azure.  

> [!TIP]
> Se você estiver abrindo um tíquete de [suporte técnico do Azure](https://azure.microsoft.com/support/create-ticket/) para o conector do Azure IOT para FHIR, certifique-se de incluir cópias do JSON de mapeamento de conversão para ajudar com o processo de solução de problemas.

## <a name="error-messages-and-fixes-for-azure-iot-connector-for-fhir-preview"></a>Mensagens de erro e correções para o conector do Azure IoT para FHIR (versão prévia)

|Mensagem|Visível|Condição|Correção| 
|-------|---------|---------|---|
|Nome de mapeamento inválido, o nome de mapeamento deve ser Device ou FHIR.|API|O tipo de mapeamento fornecido não é dispositivo ou FHIR.|Use um dos dois tipos de mapeamento com suporte (por exemplo: Device ou FHIR).|
|Falha na validação. As informações necessárias estão ausentes ou não são válidas.|API e portal do Azure|Tentativa de salvar um mapeamento de conversão que não tem informações ou elemento necessário.|Adicione informações ou elemento de mapeamento de conversão ausente e tente salvar o mapeamento de conversão novamente.|
|Regenerar parâmetros de chave não definidos.|API|Regenerar solicitação de chave.|Inclua os parâmetros na solicitação de chave de regeneração.|
|Foi atingido o número máximo de instâncias do conector IoT que podem ser provisionadas nesta assinatura.|API e portal do Azure|Conector de IoT do Azure para cota de assinatura do FHIR atingido (o padrão é (2) por assinatura).|Exclua uma das instâncias existentes do conector IoT do Azure para FHIR.  Use uma assinatura diferente que não tenha atingido a cota de assinatura.  Solicite um aumento de cota de assinatura.|
|O recurso de movimentação não tem suporte para o conector IoT com a API do Azure habilitada para o recurso FHIR.|API e portal do Azure|Tentativa de fazer uma operação de movimentação em uma API do Azure para o recurso FHIR que tem uma ou mais instâncias do conector IoT do Azure para FHIR.|Exclua as instâncias existentes do conector IoT do Azure para FHIR para fazer a operação de movimentação.|
|Conector IoT não provisionado.|API|Tentar usar serviços filho (conexões & mapeamentos) quando o pai (conector do Azure IoT para FHIR) não tiver sido provisionado.|Provisionar um conector do Azure IoT para FHIR.|
|A solicitação não terá suporte.|API|Não há suporte para a solicitação de API específica.|Use a solicitação de API correta.|
|A conta não existe.|API|A tentativa de adicionar um conector do Azure IoT para FHIR e a API do Azure para o recurso FHIR não existe.|Crie a API do Azure para o recurso FHIR e tente a operação novamente.|
|A API do Azure para a versão FHIR do recurso FHIR não tem suporte para o conector IoT.|API|Tentando usar um conector do Azure IoT para FHIR com uma versão incompatível da API do Azure para o recurso FHIR.|Crie uma nova API do Azure para o recurso FHIR (versão R4) ou use uma API do Azure existente para o recurso FHIR (versão R4).

##  <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>Por que o meu conector do Azure IoT para dados FHIR (versão prévia) não aparece na API do Azure para FHIR?

|Possíveis problemas  |Correções            |
|------------------|-----------------|
|Os dados ainda estão sendo processados.|Os dados são redados à API do Azure para FHIR em lotes (a cada ~ 15 minutos).  É possível que os dados ainda estejam sendo processados e o tempo adicional seja necessário para que os dados sejam persistidos na API do Azure para FHIR.|
|O mapeamento de conversão de dispositivo JSON não foi configurado.|Configure e salve o JSON de mapeamento de conversão de dispositivo em conformidade.|
|O mapeamento de conversão de FHIR JSON não foi configurado.|Configure e salve o JSON de mapeamento de conversão de FHIR em conformidade.|
|A mensagem do dispositivo não contém uma expressão esperada definida no mapeamento do dispositivo.|Verifique se as expressões JsonPath definidas no mapeamento de dispositivos correspondem aos tokens definidos na mensagem do dispositivo.|
|Um recurso de dispositivo não foi criado na API do Azure para FHIR (tipo de resolução: somente pesquisa) *.|Crie um recurso de dispositivo válido na API do Azure para FHIR. Verifique se o recurso de dispositivo contém um identificador que corresponde ao identificador de dispositivo fornecido na mensagem de entrada.|
|Um recurso de paciente não foi criado na API do Azure para FHIR (tipo de resolução: somente pesquisa) *.|Crie um recurso de paciente válido na API do Azure para FHIR.|
|A referência do dispositivo. paciente não está definida ou a referência é inválida (tipo de resolução: somente pesquisa) *.|Verifique se o recurso do dispositivo contém uma [referência](https://www.hl7.org/fhir/device-definitions.html#Device.patient) válida a um recurso do paciente.| 

* Guia de [início rápido: implantar o conector do Azure IOT (versão prévia) usando portal do Azure](iot-fhir-portal-quickstart.md#create-new-azure-iot-connector-for-fhir-preview) para obter uma descrição funcional do conector IOT do Azure para tipos de resolução FHIR (por exemplo: Lookup ou CREATE).

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>Criando cópias do Azure IoT Connector para FHIR (visualização) mapeamento de conversão JSON
A cópia do conector do Azure IoT para arquivos de mapeamento FHIR pode ser útil para edição e arquivamento fora do site portal do Azure.

As cópias de arquivos de mapeamento devem ser fornecidas ao suporte técnico do Azure ao abrir um tíquete de suporte para auxiliar na solução de problemas.

> [!NOTE]
> JSON é o único formato com suporte para arquivos de mapeamento de dispositivo e FHIR no momento.

> [!TIP]
> Saiba mais sobre o conector do Azure IoT para o [dispositivo FHIR e o mapeamento de conversão FHIR JSON](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. Selecione **"conector IOT (versão prévia)"** no canto inferior esquerdo da API do Azure para o painel de recursos FHIR na seção **"suplementos"** .

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="Conector IoT" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Selecione o **"conector"** do qual você copiará o mapeamento de conversão JSON.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="Conector IoT" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Esse processo também pode ser usado para copiar e salvar o conteúdo do JSON **"Configurar mapeamento de FHIR"** .

3. Selecione **"Configurar mapeamento de dispositivo"**.

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="Conector IoT" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Selecione o conteúdo do JSON e faça uma operação de cópia (por exemplo: selecione CTRL + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="Conector IoT" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Faça uma operação de colagem (por exemplo: selecione CTRL + v) em um novo arquivo dentro de um editor (por exemplo: Visual Studio Code, bloco de notas) e salve o arquivo com uma extensão *. JSON.

> [!TIP]
> Se você estiver abrindo um tíquete de [suporte técnico do Azure](https://azure.microsoft.com/support/create-ticket/) para o conector do Azure IOT para FHIR, certifique-se de incluir cópias do JSON de mapeamento de conversão para ajudar com o processo de solução de problemas.

## <a name="next-steps"></a>Próximas etapas

Confira as perguntas frequentes sobre o conector do Azure IoT para FHIR.

>[!div class="nextstepaction"]
>[Azure IoT Connector para FHIR FAQs](fhir-faq.md#azure-iot-connector-for-fhir-preview)

*No portal do Azure, o Conector IoT do Azure para FHIR é chamado de Conector IoT (versão prévia).

FHIR é uma marca registrada da HL7, usada com permissão da HL7.
