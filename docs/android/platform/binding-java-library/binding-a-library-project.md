---
title: Associação de um projeto de biblioteca do Eclipse
description: Este passo a passo explica como usar os modelos de projeto Xamarin.Android para vincular um projeto de biblioteca eclipse Android.
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 2ac402bf423c9f3fe136d1ba31622d915d2e2eef
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027735"
---
# <a name="binding-an-eclipse-library-project"></a>Associação de um projeto de biblioteca do Eclipse

_Este passo a passo explica como usar os modelos de projeto Xamarin.Android para vincular um projeto de biblioteca eclipse Android._

## <a name="overview"></a>Visão geral

Embora. Os arquivos AAR estão cada vez mais se tornando a norma para a distribuição de bibliotecas Android, em alguns casos é necessário criar uma vinculação para um *projeto de biblioteca Android*. Projetos de biblioteca android são projetos especiais de Android que contêm código compartilhável e recursos que podem ser referenciados por projetos de aplicativos Android. Normalmente, você se vincula a um projeto de biblioteca Android quando a biblioteca é criada no Eclipse IDE.
Este passo a passo fornece exemplos de como criar um projeto de biblioteca Android . ZIP da estrutura do diretório de um projeto Eclipse.

Os projetos de biblioteca android são diferentes dos projetos regulares do Android, pois não são compilados em um APK e não são, por conta própria, implantáveis em um dispositivo. Em vez disso, um projeto de biblioteca android deve ser referenciado por um projeto de aplicativo Android. Quando um projeto de aplicativo Android é construído, o projeto da biblioteca do Android é compilado primeiro. O projeto do aplicativo para Android será então absorvido no projeto de biblioteca Android compilado e incluirá o código e os recursos no APK para distribuição. Por causa dessa diferença, criar uma vinculação para um projeto de biblioteca Android é ligeiramente diferente de criar uma vinculação para um Java . JAR ou . Arquivo AAR.

## <a name="walkthrough"></a>Passo a passo

Para usar um projeto de biblioteca Android em um projeto Xamarin.Android Java Binding é necessário primeiro construir o projeto de biblioteca Android no Eclipse. A captura de tela a seguir mostra um exemplo de um projeto de biblioteca android após a compilação: 

[![Exemplo de projeto de biblioteca no Eclipse](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png#lightbox)

Observe que o código-fonte do projeto da biblioteca android foi compilado para um temporário . Arquivo JAR chamado **android-mapviewballoons.jar**e que os recursos foram copiados para a pasta **bin/res/crunch.** 

Uma vez que o projeto da biblioteca Android tenha sido compilado no Eclipse, ele pode ser vinculado usando um projeto Xamarin.Android Java Binding. Primeiro um. O arquivo ZIP deve ser criado que contenha as pastas **bin** e **res** do projeto da biblioteca android. É importante que você remova o subdiretório de **crunch** interveniente para que os recursos residam em **bin/res**. A captura de tela a seguir mostra o conteúdo de uma dessas . Arquivo ZIP: 

[![Conteúdo do projeto da biblioteca Android .zip](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png#lightbox)

Este. O arquivo ZIP é então adicionado ao projeto Xamarin.Android Java Binding, como mostrado na captura de tela a seguir:

[![Zip adicionado ao projeto Java Binding](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png#lightbox)

Observe que a Ação de Construção do . O arquivo ZIP foi configurado automaticamente como **LibraryProjectZip**.

Se houver algum. Arquivos JAR que são exigidos pelo projeto da biblioteca Android, eles devem ser adicionados à pasta **Jars** do projeto Java Binding Library e ao **Conjunto Build Action** para **ReferenceJar**. Um exemplo disso pode ser visto na captura de tela abaixo: 

[![Conjunto de ação de construção para ReferenceJar](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png#lightbox)

Uma vez que essas etapas estejam concluídas, o projeto Xamarin.Android Java Binding pode ser usado como descrito anteriormente neste documento.

> [!NOTE]
> A compilação dos projetos da biblioteca Android em outros IDEs não é suportada no momento. Outros IDEs podem não criar a mesma estrutura de diretório ou arquivos na pasta **bin** como Eclipse. 

## <a name="summary"></a>Resumo

Neste artigo, nós percorremos o processo de vinculação de um projeto de biblioteca Android. Construímos o projeto de biblioteca Android no Eclipse, então criamos um arquivo zip da **lixeira** e pastas **de res** do projeto da biblioteca Android. Em seguida, usamos este zip para criar um projeto Xamarin.Android Java Binding. 
