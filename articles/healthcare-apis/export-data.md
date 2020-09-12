---
title: Executando a exportação invocando $export comando na API do Azure para FHIR
description: Este artigo descreve como configurar e usar a exportação de-identificada
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 83509b5f452ab7cf88774561c12d7aa2cf3b46cf
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482310"
---
# <a name="how-to-export-fhir-data"></a>Como exportar dados do FHIR

Para definir as configurações de exportação e criar a conta de armazenamento do Azure, consulte [aqui](configure-export-data.md).

## <a name="exporting-fhir-resources-using-export-command"></a>Exportando recursos FHIR usando o comando $export

Depois de configurar a API do Azure para FHIR para exportação, agora podemos usar o comando $export para exportar os dados do serviço para a conta de armazenamento que especificamos ao configurar a exportação. Para saber como invocar $export comando no FHIR Server, leia a documentação sobre a especificação $export em [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . 

O comando $export na API do Azure para FHIR usa um parâmetro _ \_ conatiner_ opcional que pode ser usado para especificar o contêiner dentro da conta de armazenamento configurada para a qual os dados devem ser exportados.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

> [!IMPORTANT]
> Observe que atualmente a API do Azure para FHIR dá suporte apenas à exportação no nível do sistema, conforme definido na especificação de $export em [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . Além disso, somente _ \_ uma vez que_ o parâmetro de consulta tem suporte no momento.

## <a name="exporting-de-identified-data-preview"></a>Exportando dados de identificação (versão prévia)

O comando de $export também pode ser usado para exportar dados desidentificados do servidor FHIR. Ele usa o mecanismo de anonimato de [ferramentas FHIR para anonimato](https://github.com/microsoft/FHIR-Tools-for-Anonymization)e obtém detalhes de configuração de anonimato em parâmetros de consulta. Você pode criar seu próprio arquivo de configuração de anonimato ou usar o [arquivo de configuração de exemplo](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) para o método Safe Harbor da HIPAA como um ponto de partida. 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

|Parâmetro de consulta            | Exemplo |Optionality| Descrição|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.jsem|Necessário para exportação de-identificado |Nome do arquivo de configuração. Consulte o formato do arquivo de configuração [aqui](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format). Esse arquivo deve ser **mantido dentro de um contêiner chamado** anonimion dentro da mesma conta de armazenamento do Azure configurada como o local de exportação. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|Opcional para exportação de-identificado|Essa é a eTag do arquivo de configuração. Você pode obter a ETag usando Gerenciador de Armazenamento do Azure da propriedade blob|

> [!IMPORTANT]
> Observe que a exportação bruta, bem como as gravações de exportação desidentificadas, para a mesma conta de armazenamento do Azure especificada como parte da configuração de exportação. É recomendável que você use contêineres diferentes correspondentes a uma configuração diferente de identificada e gerencie o acesso do usuário no nível do contêiner.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a exportar recursos do FHIR usando o comando $export, incluindo dados desidentificadoss. Em seguida, você pode configurar os dados de exportação:
 
>[!div class="nextstepaction"]
>[configurar dados de exportação](configure-export-data.md)
