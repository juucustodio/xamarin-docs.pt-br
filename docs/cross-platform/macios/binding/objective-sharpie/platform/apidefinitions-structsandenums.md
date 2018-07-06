---
title: ApiDefinitions e StructsAndEnums arquivos
description: Este documento descreve os arquivos ApiDefinitions.cs e StructsAndEnums.cs que gera Sharpie objetivo. Esses arquivos, em seguida, são usados para acessar o código de Objective-C do c#.
ms.prod: xamarin
ms.assetid: AC2087C0-BA54-46D8-B70C-6972941C8F73
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: df8d4508db14116a5b36e893f161ac891d58dc46
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855176"
---
# <a name="apidefinitions--structsandenums-files"></a>ApiDefinitions e StructsAndEnums arquivos

Quando o objetivo Sharpie foi executada com êxito, ele gera `Binding/ApiDefinitions.cs` e `Binding/StructsAndEnums.cs` arquivos.
Esses dois arquivos são adicionados a um projeto de associação no Visual Studio para Mac ou passados diretamente para o `btouch` ou `bmac` ferramentas para produzir a ligação final.

Na *alguns* casos esses arquivos gerados podem ser tudo o que você precisa, no entanto, mais frequentemente o desenvolvedor precisará modificá-los manualmente arquivos para corrigir quaisquer problemas que não pôde ser tratados automaticamente pela ferramenta (por exemplo, esses sinalizadores de gerados com uma [ `Verify` atributo](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)).

Algumas das próximas etapas incluem:

- **Ajustando nomes**: às vezes, você desejará ajustar os nomes dos métodos e classes para coincidir com as diretrizes de Design do .NET Framework.
- **Métodos ou propriedades**: A heurística usada por objetivo Sharpie, às vezes, selecionará um método a ser transformado em uma propriedade. Neste ponto, você pode decidir se este é o comportamento desejado ou não.
- **Conectar eventos**: você pode vincular suas classes com suas classes de delegado e gerar automaticamente os eventos para aqueles.
- **Enganchar notificações**: não é possível extrair o contrato de API de notificações dos arquivos de cabeçalho pura, isso exigirá uma viagem para a documentação da API. Se você quiser com rigidez de tipos de notificações, você precisará atualizar o resultado.
- **Curadoria da API**: neste ponto, você pode optar por fornecer construtores adicionais, adicione métodos (para permitir a sintaxe de inicialização na construção do c#), a sobrecarga de operador e implementar suas próprias interfaces no arquivo de definições extras.

Consulte a [associação de uma API](~/cross-platform/macios/binding/objective-c-libraries.md) descrição para ver como esses arquivos se encaixam no processo de associação, conforme mostrado no diagrama a seguir:

![](apidefinitions-structsandenums-images/binding-flowchart.png "O processo de associação é mostrado neste diagrama")

Consulte a [referência de tipos de associação](~/cross-platform/macios/binding/binding-types-reference.md) para obter mais informações sobre o conteúdo desses arquivos.

## <a name="related-links"></a>Links relacionados

- [Xamarin University curso: Criação de uma biblioteca de associações do Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University curso: Criar uma biblioteca de associações do Objective-C com objetivo Sharpie](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
