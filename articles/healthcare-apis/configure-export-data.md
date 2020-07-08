---
title: Definir configurações de exportação na API do Azure para FHIR
description: Este artigo descreve como definir configurações de exportação na API do Azure para FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: 46a55b83b38593a514d40a9f75d99739a1efb8a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871016"
---
# <a name="configure-export-setting-and-export-the-data-to-a-storage-account"></a>Configurar a configuração de exportação e exportar os dados para uma conta de armazenamento

A API do Azure para FHIR dá suporte ao comando $export que permite exportar os dados da API do Azure para a conta FHIR para uma conta de armazenamento.

Há quatro etapas envolvidas na execução da exportação na API do Azure para FHIR:

1. Habilitar identidade gerenciada na API do Azure para o serviço FHIR
2. Criar uma conta de armazenamento do Azure (se não for feito antes) e atribuir permissão à API do Azure para FHIR à conta de armazenamento
3. Selecionando a conta de armazenamento na API do Azure para FHIR como exportar conta de armazenamento
4. Executando a exportação invocando $export comando na API do Azure para FHIR

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Habilitando identidade gerenciada na API do Azure para FHIR

A primeira etapa na configuração da API do Azure para FHIR para exportação é habilitar a identidade gerenciada em todo o sistema no serviço. Você pode ler tudo sobre identidades gerenciadas no Azure [aqui](../active-directory/managed-identities-azure-resources/overview.md).

Para fazer isso, navegue até a API do Azure para o serviço FHIR e selecione folha de identidade. Alterar o status para ativado permitirá a identidade gerenciada na API do Azure para o serviço FHIR.

![Habilitar identidade gerenciada](media/export-data/fhir-mi-enabled.png)

Agora, podemos passar para a próxima etapa e criar uma conta de armazenamento e atribuir a permissão ao nosso serviço.

## <a name="adding-permission-to-storage-account"></a>Adicionando permissão à conta de armazenamento

A próxima etapa na exportação é atribuir permissão para a API do Azure para o serviço FHIR gravar na conta de armazenamento.

Depois de criarmos uma conta de armazenamento, navegue até a folha controle de acesso (IAM) na conta de armazenamento e selecione Adicionar atribuições de função

![Habilitar identidade gerenciada](media/export-data/fhir-export-role-assignment.png)

Aqui, adicionamos o colaborador de dados de blob de armazenamento de função ao nosso nome de serviço.

![Habilitar identidade gerenciada](media/export-data/fhir-export-role-add.png)

Agora estamos prontos para a próxima etapa, na qual podemos selecionar a conta de armazenamento na API do Azure para FHIR como uma conta de armazenamento padrão para $export.

## <a name="selecting-the-storage-account-for-export"></a>Selecionando a conta de armazenamento para $export

Etapa final antes de invocar $export comando é atribuir a conta de armazenamento do Azure que a API do Azure para FHIR usará para exportar os dados para o. Para fazer isso, navegue até a folha de integração na API do Azure para serviço FHIR em portal do Azure e selecione a conta de armazenamento 

![Habilitar identidade gerenciada](media/export-data/fhir-export-storage.png)

Depois disso, estamos prontos para exportar os dados usando $export comando.

## <a name="exporting-the-data-using-export-command"></a>Exportando os dados usando o comando $export

Depois de configurar a API do Azure para FHIR para exportação, agora podemos usar o comando $export para exportar os dados do serviço para a conta de armazenamento que especificamos. Para saber como invocar $export comando no FHIR Server, leia a documentação sobre especificação de $export em[https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html)

> [!IMPORTANT]
> Observe que atualmente a API do Azure para FHIR dá suporte apenas à exportação no nível do sistema, conforme definido na especificação de exportação em [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . No momento, não há suporte para parâmetros de consulta com o $export.

>[!div class="nextstepaction"]
>[Configurações adicionais](azure-api-for-fhir-additional-settings.md)