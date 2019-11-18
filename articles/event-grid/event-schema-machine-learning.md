---
title: Grade de eventos do Azure Machine Learning esquema de evento
description: Descreve as propriedades que são fornecidas para eventos de Workspace do Machine Learning com a grade de eventos do Azure
services: event-grid
author: jenns
ms.service: event-grid
ms.topic: reference
ms.date: 10/18/2019
ms.author: jenns
ms.openlocfilehash: 5f2d23b3fe33691d37dc00b2d4e79036293252d9
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132879"
---
# <a name="azure-event-grid-event-schema-for-azure-machine-learning"></a>Esquema de evento da grade de eventos do Azure para Azure Machine Learning

Este artigo fornece as propriedades e o esquema para eventos de espaço de trabalho do Machine Learning. Para obter uma introdução a esquemas de evento, consulte [esquema de evento da Grade de Eventos do Azure](event-schema.md).

Para obter uma lista de exemplos de scripts e tutoriais, consulte [origem do evento do AzureML](event-sources.md#azure-machine-learning).

## <a name="available-event-types"></a>Tipos de evento disponíveis

Azure Machine Learning emite os seguintes tipos de evento:

| Tipo de evento | DESCRIÇÃO |
| ---------- | ----------- |
| Microsoft. MachineLearningServices. ModelRegistered | Gerado quando uma nova versão de modelo ou modelo foi registrada com êxito. |
| Microsoft. MachineLearningServices. ModelDeployed | Gerado quando modelo (s) foram implantados com êxito em um ponto de extremidade. |
| Microsoft. MachineLearningServices. RunCompleted | Gerado quando uma execução é concluída com êxito. |
| Microsoft. MachineLearningServices. DatasetDriftDetected | Gerado quando um monitor de descompasso de conjunto de uma detecta descompasso. |

## <a name="the-contents-of-an-event-response"></a>O conteúdo de uma resposta de evento

Quando um evento é disparado, o serviço de grade de eventos envia dados sobre esse evento para o ponto de extremidade de assinatura.

Esta seção contém um exemplo de como os dados seriam para cada evento.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Evento Microsoft. MachineLearningServices. ModelRegistered

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

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Evento Microsoft. MachineLearningServices. ModelDeployed

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

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Evento Microsoft. MachineLearningServices. RunCompleted

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

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Evento Microsoft. MachineLearningServices. DatasetDriftDetected

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

## <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Digite | DESCRIÇÃO |
| -------- | ---- | ----------- |
| topic | cadeia de caracteres | Caminho de recurso completo para a origem do evento. Esse campo não é gravável. Grade de Eventos fornece esse valor. |
| subject | cadeia de caracteres | Caminho definido pelo fornecedor para o assunto do evento. |
| eventType | cadeia de caracteres | Um dos tipos de evento registrados para a origem do evento. |
| eventTime | cadeia de caracteres | A hora em que o evento é gerado com base na hora UTC do provedor. |
| ID | cadeia de caracteres | Identificador exclusivo do evento. |
| data | objeto | Dados de eventos do armazenamento de blob. |
| dataVersion | cadeia de caracteres | A versão do esquema do objeto de dados. O fornecedor define a versão do esquema. |
| metadataVersion | cadeia de caracteres | A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades para cada tipo de evento:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft. MachineLearningServices. ModelRegistered

| Propriedade | Digite | DESCRIÇÃO |
| -------- | ---- | ----------- |
| ModelName | cadeia de caracteres | O nome do modelo que foi registrado. |
| ModelVersion | int | A versão do modelo que foi registrado. |
| ModelTags | objeto | As marcas do modelo que foi registrado. |
| Modelproperties | objeto | As propriedades do modelo que foi registrado. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft. MachineLearningServices. ModelDeployed

| Propriedade | Digite | DESCRIÇÃO |
| -------- | ---- | ----------- |
| ServiceName | cadeia de caracteres | O nome do serviço implantado. |
| Filecomputetype | cadeia de caracteres | O tipo de computação (por exemplo, ACI, AKS) do serviço implantado. |
  | ModelIds | cadeia de caracteres | Uma lista separada por vírgulas de IDs de modelo. As IDs dos modelos implantados no serviço. |
| Crachás | objeto | As marcas do serviço implantado. |
| ServiceProperties | objeto | As propriedades do serviço implantado. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft. MachineLearningServices. RunCompleted

| Propriedade | Digite | DESCRIÇÃO |
| -------- | ---- | ----------- |
| Experimentoid | cadeia de caracteres | A ID do experimento ao qual a execução pertence. |
| Experimentoname | cadeia de caracteres | O nome do experimento ao qual a execução pertence. |
| RunId | cadeia de caracteres | A ID da execução que foi concluída. |
| RunType | cadeia de caracteres | O tipo de execução da execução concluída. |
| RunTags | objeto | As marcas da execução concluída. |
| RunProperties | objeto | As propriedades da execução concluída. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft. MachineLearningServices. DatasetDriftDetected

| Propriedade | Digite | DESCRIÇÃO |
| -------- | ---- | ----------- |
| Onficaid | cadeia de caracteres | A ID do monitor de descompasso de dados que disparou o evento. |
| Datalogoffname | cadeia de caracteres | O nome do monitor de descompasso de dados que disparou o evento. |
| RunId | cadeia de caracteres | A ID da execução que detectou descompasso de dados. |
| BaseDatasetId | cadeia de caracteres | A ID do conjunto de dados base usado para detectar descompasso. |
| TargetDatasetId | cadeia de caracteres | A ID do conjunto de código de destino usado para detectar descompasso. |
| DriftCoefficient | Duplo | O resultado do coeficiente que disparou o evento. |
| StartTime | datetime | A hora de início da série temporal do conjunto de tempo de destino que resultou na detecção de descompasso.  |
| EndTime | datetime | A hora de término da série temporal do conjunto de tempo de destino que resultou na detecção de descompasso. |


## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da grade de eventos do Azure, consulte [esquema de assinatura da grade de eventos](subscription-creation-schema.md)
* Para obter uma introdução ao uso da grade de eventos do Azure com o Azure Machine Learning, consulte [consumir Azure Machine Learning eventos](/azure/machine-learning/service/concept-event-grid-integration)
* Para obter um exemplo de como usar a grade de eventos do Azure com Azure Machine Learning, consulte [criar fluxos de trabalho de aprendizado de máquina direcionados a eventos](/azure/machine-learning/service/how-to-use-event-grid)
