---
title: Usando pacotes de DAC do Banco de Dados SQL – SQL do Azure no Edge (versão prévia)
description: Saiba mais sobre como usar dacpacs no SQL do Azure no Edge (versão prévia)
keywords: SQL no Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 0ddd1544c6a51ff1e2f98a28e40d9eb2ee0b47c7
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233275"
---
# <a name="sql-database-dac-packages-in-sql-edge"></a>Pacotes de DAC do Banco de Dados SQL no SQL do Azure no Edge

O SQL do Azure no Edge (versão prévia) é um mecanismo de banco de dados relacional otimizado projetado para implantações de IoT e de borda. Ele foi criado com base nas versões mais recentes do Mecanismo de Banco de Dados do Microsoft SQL Server, que fornece funcionalidades de desempenho, segurança e processamento de consulta líderes do setor. Juntamente com as funcionalidades de gerenciamento de banco de dados relacional líderes do setor do SQL Server, o SQL do Azure no Edge fornece a funcionalidade de streaming interno para análise em tempo real e processamento de eventos complexos.

O SQL do Azure no Edge também fornece uma implementação nativa do SqlPackage.exe, que permite que você implante um [DAC do Banco de Dados SQL](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) durante a implantação do SQL no Edge. Os dacpacs do Banco de Dados SQL podem ser implantados no SQL no Edge usando o parâmetro SqlPackage exposto por meio da opção `module twin's desired properties` do módulo do SQL no Edge:

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Campo | Descrição |
|------|-------------|
| SqlPackage | O URI de armazenamento de blobs do Azure para o arquivo *.zip que contém o pacote de DAC do Banco de Dados SQL.
| ASAJobInfo | URI de armazenamento de blobs do Azure para o trabalho do ASA no Edge.

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>Usar um pacote de DAC do Banco de Dados SQL com o SQL no Edge

Para usar um pacote de DAC do Banco de Dados SQL (*.dacpac) com o SQL no Edge, siga estas etapas:

1. Crie ou extraia um pacote de DAC do Banco de Dados SQL. Consulte [Extrair um DAC de um banco de dados](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) para obter informações sobre como gerar um pacote de DAC para um banco de dados do SQL Server existente.

2. Compacte o *.dacpac e carregue-o em uma conta de armazenamento de blobs do Azure. Para obter mais informações sobre como carregar arquivos no armazenamento de blobs do Azure, consulte [Carregar, baixar e listar blobs com o portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Gere uma assinatura de acesso compartilhado para o arquivo zip usando o portal do Azure. Para saber mais, consulte [Delegar acesso com SAS (Assinaturas de Acesso Compartilhado)](../storage/common/storage-sas-overview.md).

4. Atualize a configuração do módulo do SQL no Edge para incluir o URI de acesso compartilhado para o pacote de DAC. Para atualizar o módulo do SQL no Edge, siga estas etapas:

    1. No portal do Azure, acesse sua implantação do Hub IoT.

    2. No painel esquerdo, selecione **IoT Edge**.

    3. Na página do **IoT Edge**, localize e selecione o IoT Edge em que o módulo SQL no Edge é implantado.

    4. Na página de dispositivos **Dispositivo do IoT Edge**, selecione **Definir Módulo**.

    5. Na página **Definir módulos**, selecione **Configurar** no módulo do SQL no Edge.

    6. No painel **Módulos Personalizados do IoT Edge**, selecione **Definir propriedades desejadas do módulo gêmeo**. Atualize as propriedades desejadas para incluir o URI da opção `SQLPackage`, conforme mostrado no exemplo a seguir.

        > [!NOTE]
        > O URI do SAS no JSON a seguir é apenas um exemplo. Substitua o URI pelo URI real da sua implantação.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "<<<SAS URL for the *.zip file containing the dacpac",
                }
            }
        ```

    7. Clique em **Salvar**.

    8. Na página **Definir módulos**, selecione **Avançar**.

    9. Na página **Definir módulos**, selecione **Avançar** e **Enviar**.

5. Após a atualização do módulo, o arquivo do pacote de DAC será baixado, descompactado e implantado na instância do SQL no Edge.

Em cada reinicialização do contêiner do SQL do Azure no Edge, o pacote de arquivo *.dacpac é baixado e avaliado quanto a alterações. Se uma nova versão do arquivo dacpac for encontrada, as alterações serão implantadas no banco de dados no SQL no Edge.

## <a name="next-steps"></a>Próximas etapas

- [Implantar o SQL no Edge por meio do portal do Azure](deploy-portal.md).
- [Transmitir dados](stream-data.md)
- [Machine learning e IA com o ONNX no SQL no Edge (versão prévia)](onnx-overview.md)
