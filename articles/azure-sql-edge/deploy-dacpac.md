---
title: Usando o banco de dados SQL DACPAC e pacotes BACPAC-Azure SQL Edge (versão prévia)
description: Saiba mais sobre como usar o dacpacs e o bacpacs no Azure SQL Edge (versão prévia)
keywords: SQL no Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 52c8e9586d8ee53cdaac28cb1c48d2927d82c2ed
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462751"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>Pacotes DACPAC e BACPAC do banco de dados SQL no SQL Edge

O SQL do Azure no Edge (versão prévia) é um mecanismo de banco de dados relacional otimizado projetado para implantações de IoT e de borda. Ele foi criado com base nas versões mais recentes do Mecanismo de Banco de Dados do Microsoft SQL Server, que fornece funcionalidades de desempenho, segurança e processamento de consulta líderes do setor. Juntamente com as funcionalidades de gerenciamento de banco de dados relacional líderes do setor do SQL Server, o SQL do Azure no Edge fornece a funcionalidade de streaming interno para análise em tempo real e processamento de eventos complexos.

O Azure SQL Edge também fornece uma implementação nativa de SqlPackage.exe que permite que você implante um [banco de dados SQL DACPAC e](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) um pacote BACPAC durante a implantação do SQL Edge. Os dacpacs do Banco de Dados SQL podem ser implantados no SQL no Edge usando o parâmetro SqlPackage exposto por meio da opção `module twin's desired properties` do módulo do SQL no Edge:

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
| SqlPackage | URI do armazenamento de BLOBs do Azure para o arquivo *. zip* que contém o DAC ou o pacote BACPAC do banco de dados SQL. O arquivo zip pode conter vários pacotes de DAC ou arquivos bacpac.
| ASAJobInfo | URI de armazenamento de blobs do Azure para o trabalho do ASA no Edge.

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>Usar um pacote de DAC do Banco de Dados SQL com o SQL no Edge

Para usar um pacote de DAC do banco de dados SQL `(*.dacpac)` ou um arquivo BACPAC `(*.bacpac)` com o SQL Edge, siga estas etapas:

1. Crie/Extraia um pacote de DAC ou exporte um arquivo Bacpac usando o mecanismo mencionado abaixo. 
    - Crie ou extraia um pacote de DAC do Banco de Dados SQL. Consulte [Extrair um DAC de um banco de dados](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) para obter informações sobre como gerar um pacote de DAC para um banco de dados do SQL Server existente.
    - Exportando um pacote de DAC implantado ou um banco de dados. Consulte [exportar um aplicativo da camada de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application/) para obter informações sobre como gerar um arquivo bacpac para um banco de dado SQL Server existente.

2. Compacte o `*.dacpac` `*.bacpac` arquivo ou e carregue-o em uma conta de armazenamento de BLOBs do Azure. Para obter mais informações sobre como carregar arquivos no armazenamento de blobs do Azure, consulte [Carregar, baixar e listar blobs com o portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

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
                    "SqlPackage": "<<<SAS URL for the *.zip file containing the dacpac and/or the bacpac files",
                }
            }
        ```

    7. Clique em **Salvar**.

    8. Na página **Definir módulos**, selecione **Avançar**.

    9. Na página **Definir módulos**, selecione **Avançar** e **Enviar**.

5. Após a atualização do módulo, o arquivo de pacote é baixado, descompactado e implantado na instância do SQL Edge.

Em cada reinicialização do contêiner do Azure SQL Edge, o `*.dacpac` pacote de arquivos é baixado e avaliado quanto a alterações. Se uma nova versão do arquivo dacpac for encontrada, as alterações serão implantadas no banco de dados no SQL no Edge. Arquivos Bacpac 

## <a name="next-steps"></a>Próximas etapas

- [Implantar o SQL no Edge por meio do portal do Azure](deploy-portal.md).
- [Transmitir dados](stream-data.md)
- [Machine learning e IA com o ONNX no SQL no Edge (versão prévia)](onnx-overview.md)
