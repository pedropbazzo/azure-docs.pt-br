---
title: Aprenda a usar uma área restrita Apache Hadoop, emulador-Azure HDInsight
description: 'Para começar a aprender sobre o uso do ecossistema do Apache Hadoop, você pode configurar um sandbox do Hadoop a partir do Hortonworks em uma máquina virtual do Azure. '
keywords: emulador do hadoop,área restrita do hadoop
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: how-to
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: de2a8e6377ababadc7d42f873a816a01a8f3b77e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86207468"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Introdução a uma área restrita do Apache Hadoop, um emulador em uma máquina virtual

Aprenda a instalar o sandbox Apache Hadoop do Hortonworks em uma máquina virtual para aprender sobre o ecossistema do Hadoop. A área restrita fornece um ambiente de desenvolvimento local para saber mais sobre o Hadoop, o HDFS (Sistema de Arquivos Distribuído Hadoop) e o envio de trabalhos. Quando estiver familiarizado com o Hadoop, você poderá começar a usar o Hadoop no Azure, criando um cluster do HDInsight. Para saber mais sobre como começar, confira [Introdução ao Hadoop no HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Oracle VirtualBox](https://www.virtualbox.org/). Baixe-o e instale-o [aqui](https://www.virtualbox.org/wiki/Downloads).

## <a name="download-and-install-the-virtual-machine"></a>Baixar e instalar a máquina virtual

1. Navegue até os [downloads do Cloudera](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html).

1. Clique em **VIRTUALBOX** em **escolher tipo de instalação** para baixar a área restrita do Hortonworks mais recente em uma VM. Entre ou preencha o formulário de interesse do produto.

1. Clique no botão **HDP sandbox (mais recente)** para iniciar o download.

Para obter instruções sobre como configurar a área restrita, consulte [Guia de implantação e instalação do Sandbox](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/).

Para baixar uma área restrita da versão mais antiga do HDP, consulte os links em **versões mais antigas**.

## <a name="start-the-virtual-machine"></a>Iniciar a máquina virtual

1. Abra o Oracle VM VirtualBox.
1. No menu **Arquivo**, clique em **Importar Dispositivo** e especifique a imagem da Área Restrita da Hortonworks.
1. Selecione a Área Restrita da Hortonworks, clique em **Iniciar** e em **Início Normal**. Quando a máquina virtual tiver terminado o processo de inicialização, ela exibirá instruções de logon.

    ![início normal do VirtualBox Manager](./media/apache-hadoop-emulator-get-started/virtualbox-normal-start.png)

1. Abra um navegador da Web e navegue até a URL exibida (geralmente `http://127.0.0.1:8888` ).

## <a name="set-sandbox-passwords"></a>Definir senhas da Área Restrita

1. Na etapa de **introdução** da página da Hortonworks Sandbox, selecione **Exibir Opções Avançadas**. Use as informações desta página para fazer logon na área restrita usando SSH. Use o nome e a senha fornecidos.

   > [!NOTE]
   > Se você não tiver um cliente SSH instalado, poderá usar o SSH baseado na Web fornecido pelo pela máquina virtual em **http://localhost:4200/** .

    Na primeira vez que você se conectar usando SSH, você receberá uma solicitação para alterar a senha da conta raiz. Insira uma nova senha, que você usa quando faz logon usando SSH.

2. Depois de conectado, digite o seguinte comando:

    ```bash
    ambari-admin-password-reset
    ```

    Quando receber uma solicitação, forneça uma senha para a conta de administrador do Ambari. Isso é usado quando você acessa a interface do usuário da Web do Ambari.

## <a name="use-hive-commands"></a>Usar comandos do Hive

1. De uma conexão SSH com a área restrita, use o seguinte comando para iniciar o shell do Hive:

    ```bash
    hive
    ```

2. Quando o shell for iniciado, use o seguinte para exibir as tabelas que são fornecidas com a área restrita:

    ```hiveql
    show tables;
    ```

3. Use o seguinte para recuperar 10 linhas da tabela `sample_07` :

    ```hiveql
    select * from sample_07 limit 10;
    ```

## <a name="next-steps"></a>Próximas etapas

* [Aprenda a usar o Visual Studio com a Hortonworks Sandbox](../hdinsight-hadoop-emulator-visual-studio.md)

* [Learning the ropes of the Hortonworks Sandbox](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)

* [Hadoop tutorial - Getting started with HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
