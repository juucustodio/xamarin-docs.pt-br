---
title: ApiDefinitions & StructsAndEnums arquivos
description: Este documento descreve os arquivos ApiDefinitions.cs e StructsAndEnums.cs que gera Sharpie objetivo. Esses arquivos, em seguida, são usados para acessar o código Objective-C do c#.
ms.prod: xamarin
ms.assetid: AC2087C0-BA54-46D8-B70C-6972941C8F73
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 3b991f6105c6053f473b049d195aaef63cbcdd57
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780887"
---
# <a name="apidefinitions--structsandenums-files"></a>ApiDefinitions & StructsAndEnums arquivos

Quando o objetivo Sharpie foi executada com êxito, ele gera `Binding/ApiDefinitions.cs` e `Binding/StructsAndEnums.cs` arquivos.
Esses dois arquivos são adicionados a um projeto de vinculação no Visual Studio para Mac ou passados diretamente para o `btouch` ou `bmac` ferramentas para produzir a associação final.

Em *alguns* casos esses arquivos gerados podem ser tudo o que você precisa, no entanto, mais frequentemente o desenvolvedor precisa modificá-los manualmente gerou arquivos para corrigir quaisquer problemas que não podem ser tratados automaticamente pela ferramenta (como esses sinalizadores com um [ `Verify` atributo](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)).

Algumas das próximas etapas incluem:

- **Ajustando nomes**: às vezes você deseja ajustar os nomes de métodos e classes para coincidir com as diretrizes de Design do .NET Framework.
- **Métodos ou propriedades**: A heurística usada por objetivo Sharpie às vezes escolherá um método a ser transformado em uma propriedade. Neste ponto, você pode decidir se este é o comportamento desejado ou não.
- **Conectar eventos**: você pode vincular suas classes com suas classes delegate e gerar eventos automaticamente para aqueles.
- **Conectar-se com as notificações**: não é possível extrair o contrato de API de notificações dos arquivos de cabeçalho puro, isso exigirá uma viagem para a documentação da API. Se você quiser notificações com rigidez de tipos, você precisará atualizar o resultado.
- **API curadoria**: neste ponto, você pode optar por fornecer construtores extras, adicionar métodos (para permitir a sintaxe do c# inicializar em construção), a sobrecarga de operador e implementar suas próprias interfaces no arquivo de definições adicionais.

Consulte o [associando uma API](~/cross-platform/macios/binding/objective-c-libraries.md) descrição para ver como esses arquivos se encaixam no processo de associação, conforme mostrado no diagrama a seguir:

![](apidefinitions-structsandenums-images/binding-flowchart.png "O processo de ligação é mostrado no diagrama")

Consulte o [referência de tipos de associação](~/cross-platform/macios/binding/binding-types-reference.md) para obter mais informações sobre o conteúdo desses arquivos.

