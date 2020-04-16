---
title: Azure Machine Learning como fonte da Grade de Eventos
description: Descreve as propriedades fornecidas para eventos de espaço de trabalho de aprendizado de máquina com a Grade de Eventos do Azure
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: 7d9af420c7e2b47d2aeb4a8bf42ee138a605b305
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393270"
---
# <a name="azure-machine-learning-as-an-event-grid-source"></a>Azure Machine Learning como fonte de grade de eventos

Este artigo fornece as propriedades e o esquema para eventos de espaço de trabalho de aprendizagem de máquina. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md).

## <a name="event-grid-event-schema"></a>Esquema de eventos da Grade de Eventos

### <a name="available-event-types"></a>Tipos de evento disponíveis

O Azure Machine Learning emite os seguintes tipos de eventos:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.MachineLearningServices.ModelRegistrado | Criado quando uma nova versão de Modelo ou Modelo foi registrada com sucesso. |
| Microsoft.MachineLearningServices.ModelImplantado | Criado quando os modelos foram implantados com sucesso em um ponto final. |
| Microsoft.MachineLearningServices.runCompleted | Levantado quando uma corrida foi concluída com sucesso. |
| Microsoft.MachineLearningServices.DatasetDriftDetectado | Levantado quando um monitor de deriva Dataset detecta deriva. |
| Microsoft.MachineLearningServices.runStatusChanged | Elevado quando um status de execução muda para 'falhou'. |

### <a name="the-contents-of-an-event-response"></a>O conteúdo de uma resposta de evento

Quando um evento é acionado, o serviço Event Grid envia dados sobre esse evento para assinar o ponto final.

Esta seção contém um exemplo de como esses dados seriam para cada evento.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Microsoft.MachineLearningServices.ModelEvento registrado

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "models/sklearn_regression_model:20",
  "eventType": "Microsoft.MachineLearningServices.ModelRegistered",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ModelName": "sklearn_regression_model",
    "ModelVersion": 20,
    "ModelTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ModelProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Microsoft.MachineLearningServices.ModelEvento implantado

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "endpoints/my-sklearn-service",
  "eventType": "Microsoft.MachineLearningServices.ModelDeployed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ServiceName": "my-sklearn-service",
    "ServiceComputeType": "ACI",
    "ModelIds": "sklearn_regression_model:1,sklearn_regression_model:2",
    "ServiceTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ServiceProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Microsoft.MachineLearningServices.RunCompleted event

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Microsoft.MachineLearningServices.DatasetDriftEvento detectado

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "datadrifts/{}/runs/{}",
  "eventType": "Microsoft.MachineLearningServices.DatasetDriftDetected",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "DataDriftId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef",
    "DataDriftName": "myDriftMonitor",
    "RunId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef_1571590300380",
    "BaseDatasetId": "3c56d136-0f64-4657-a0e8-5162089a88a3",
    "TargetDatasetId": "d7e74d2e-c972-4266-b5fb-6c9c182d2a74",
    "DriftCoefficient": 0.83503490684792081,
    "StartTime": "2019-07-04T00:00:00+00:00",
    "EndTime": "2019-07-05T00:00:00+00:00"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesrunstatuschanged-event"></a>Microsoft.MachineLearningServices.RunStatusEvento alterado

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    },
   "RunStatus": "failed"
   },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| topic | string | Caminho de recurso completo para a origem do evento. Esse campo não é gravável. Grade de Eventos fornece esse valor. |
| subject | string | Caminho definido pelo fornecedor para o assunto do evento. |
| eventType | string | Um dos tipos de evento registrados para a origem do evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| id | string | Identificador exclusivo do evento. |
| data | objeto | Dados de eventos do armazenamento de blob. |
| dataVersion | string | A versão do esquema do objeto de dados. O fornecedor define a versão do esquema. |
| metadataVersion | string | A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades para cada tipo de evento:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft.MachineLearningServices.ModelRegistrado

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| ModelName | string | O nome do modelo que foi registrado. |
| Versão de modelo | string | A versão do modelo que foi registrada. |
| ModelTags | objeto | As etiquetas do modelo que foi registrada. |
| Propriedades do modelo | objeto | As propriedades do modelo que foi registrada. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft.MachineLearningServices.ModelImplantado

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| ServiceName | string | O nome do serviço implantado. |
| ServiceComputeType | string | O tipo de computação (por exemplo, ACI, AKS) do serviço implantado. |
  | ModelIds | string | Uma composição separou a lista de IDs do modelo. Os IDs dos modelos implantados no serviço. |
| Etiquetas de Serviço | objeto | As etiquetas do serviço implantado. |
| Propriedades de serviço | objeto | As propriedades do serviço implantado. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft.MachineLearningServices.runCompleted

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| ExperimentId | string | A id do experimento a que a corrida pertence. |
| ExperimentName | string | O nome da experiência a que a corrida pertence. |
| RunId | string | O ID da Corrida que foi concluído. |
| Tipo de execução | string | O Tipo de Execução da Execução concluída. |
| RunTags | objeto | As etiquetas do Run concluído. |
| Executarpropriedades | objeto | As propriedades do Run concluído. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft.MachineLearningServices.DatasetDriftDetectado

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| DataDriftId | string | A id do monitor de deriva de dados que desencadeou o evento. |
| DataDriftName | string | O nome do monitor de deriva de dados que desencadeou o evento. |
| RunId | string | O ID da Run que detectou a deriva de dados. |
| BaseDatasetId | string | O ID do conjunto de dados base usado para detectar deriva. |
| TargetDatasetId | string | O ID do conjunto de dados de destino usado para detectar drift. |
| DriftCoeficiente | double | O resultado do coeficiente que desencadeou o evento. |
| StartTime | DATETIME | O tempo de início da série de tempo do conjunto de dados de destino que resultou na detecção de deriva.  |
| EndTime | DATETIME | O tempo final da série de tempo do conjunto de dados de destino que resultou na detecção de deriva. |

### <a name="microsoftmachinelearningservicesrunstatuschanged"></a>Microsoft.MachineLearningServices.runStatusChanged

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| ExperimentId | string | A id do experimento a que a corrida pertence. |
| ExperimentName | string | O nome da experiência a que a corrida pertence. |
| RunId | string | O ID da Corrida que foi concluído. |
| Tipo de execução | string | O Tipo de Execução da Execução concluída. |
| RunTags | objeto | As etiquetas do Run concluído. |
| Executarpropriedades | objeto | As propriedades do Run concluído. |
| RunStatus | string | O status da Corrida. |

## <a name="tutorials-and-how-tos"></a>Tutoriais e como fazer
| Title | Descrição |
| ----- | ----- |
| [Consumir eventos de Machine Learning do Azure](../machine-learning/concept-event-grid-integration.md) | Visão geral da integração do Azure Machine Learning com a Event Grid. |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura do Azure Event Grid, consulte [o esquema de assinatura do Event Grid](subscription-creation-schema.md)
* Para obter uma introdução ao uso da Grade de Eventos do Azure com o Azure Machine Learning, consulte [Consumir eventos de Machine Learning do Azure](/azure/machine-learning/service/concept-event-grid-integration)
* Para um exemplo de usar o Azure Event Grid com o Azure Machine Learning, consulte [Criar fluxos de trabalho de aprendizado de máquina impulsionados por eventos](/azure/machine-learning/service/how-to-use-event-grid)
