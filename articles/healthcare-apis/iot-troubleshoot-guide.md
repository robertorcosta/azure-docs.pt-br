---
title: Conector do Azure IoT para FHIR (conector IoT)-guia de solução de problemas e instruções
description: Como solucionar problemas de mensagens de erro e condições comuns do conector IoT e copiar arquivos de mapeamento
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jasteppe
ms.openlocfilehash: eff1272318413da7855134b0a8a44dd0a0711a6c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285570"
---
# <a name="iot-connector-preview-troubleshooting-guide"></a>Guia de solução de problemas do conector IoT (versão prévia)

Este artigo fornece etapas para solucionar problemas de mensagens e condições de erro do conector IoT comum (versão prévia).  

Você também aprenderá a criar cópias dos arquivos de mapeamento de JSON do conector IoT (visualização) para edição e arquivamento fora do portal do Azure.

## <a name="error-messages-and-fixes"></a>Mensagens de erro e correções

|Mensagem   |Condição  |Fix         |
|----------|-----------|------------|
|Nome de mapeamento inválido, o nome de mapeamento deve ser dispositivo ou FHIR|O tipo de mapeamento fornecido não é dispositivo ou FHIR|Use um dos dois tipos de mapeamento com suporte (por exemplo: Device ou FHIR)|
|Regenerar parâmetros de chave não definidos|Regenerar solicitação de chave|Incluir os parâmetros na solicitação de chave de regeneração|
|Atingido o número máximo de instâncias do conector IoT que podem ser provisionadas nesta assinatura|Cota de assinatura do conector IoT atingida (o padrão é 2 por assinatura)|Exclua um dos conectores existentes, use uma assinatura diferente que não atingiu o (2) conectores por cota de assinatura ou solicite um aumento de cota de assinatura|
|O recurso de movimentação não tem suporte para o conector IoT com a API do Azure habilitada para o recurso FHIR|Tentando fazer uma operação de movimentação em uma API do Azure para o recurso FHIR que tem um ou mais conectores IoT|Excluir conectores existentes para executar/concluir a (s) operação (ões) de movimentação|
|A API do Azure para o recurso FHIR tem o link privado habilitado.  Não há suporte para o link privado com o conector IoT|Tentando adicionar um conector IoT a uma API do Azure para o recurso FHIR que tem o link privado habilitado|Selecione ou crie uma API do Azure para o recurso FHIR (versão R4) que não tem o link privado habilitado|
|Conector IoT não provisionado|Tentar usar serviços filho (conexões & mapeamentos) quando o pai (conector IoT) não tiver sido provisionado|Provisionar um conector IoT|
|Não há suporte para a solicitação|Não há suporte para a solicitação de API específica|Usar a solicitação de API correta|
|A conta não existe|A tentativa de adicionar um conector IoT e a API do Azure para o recurso FHIR não existe|Crie a API do Azure para o recurso FHIR e tente a operação novamente|
|A API do Azure para a versão FHIR do recurso FHIR não tem suporte para o conector IoT|Tentando usar um conector IoT com uma versão incompatível da API do Azure para o recurso FHIR|Criar uma nova API do Azure para o recurso FHIR (versão R4) ou usar uma API do Azure existente para o recurso FHIR (versão R4)

## <a name="creating-copies-of-the-iot-connector-preview-mapping-files"></a>Criando cópias dos arquivos de mapeamento do conector IoT (versão prévia)
> [!NOTE]
> JSON é o único formato com suporte para arquivos de mapeamento de dispositivo e FHIR no momento.

> [!TIP]
> A cópia dos arquivos de mapeamento do conector IoT pode ser útil para edição e arquivamento fora do site do portal do Azure e para fornecer ao suporte técnico do Azure ao abrir um tíquete de suporte.
> 
> Saiba mais sobre o dispositivo do conector IoT [e os arquivos JSON de mapeamento FHIR](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. Selecione **"conector IOT (versão prévia)"** no canto inferior esquerdo da API do Azure para o painel de recursos FHIR na seção **"suplementos"** .

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="Conector IoT" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Selecione o **"conector"** do qual você copiará os arquivos de mapeamento.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="Conector IoT" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

3. Selecione **"Configurar mapeamento de dispositivo"**.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="Conector IoT" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

> [!NOTE]
> Esse processo também pode ser usado para copiar/salvar o conteúdo do JSON **"Configurar mapeamento de FHIR"** .

4. Selecione o conteúdo do JSON e faça uma operação de cópia (por exemplo: selecione CTRL + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="Conector IoT" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Faça uma operação de colagem (por exemplo: selecione CTRL + v) em um novo arquivo dentro de um editor (por exemplo: Visual Studio Code, bloco de notas) e salve o arquivo com uma extensão *. JSON.

> [!TIP]
> Se você estiver abrindo um tíquete de [suporte técnico do Azure](https://azure.microsoft.com/support/create-ticket/) para o conector IOT, certifique-se de incluir cópias dos arquivos de mapeamento para ajudar com o processo de solução de problemas.

## <a name="next-steps"></a>Próximas etapas

Confira as perguntas frequentes sobre o conector IoT

>[!div class="nextstepaction"]
>[Perguntas frequentes do conector IoT](fhir-faq.md#iot-connector-preview)


FHIR é uma marca registrada da HL7, usada com permissão da HL7.