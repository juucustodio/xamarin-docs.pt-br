---
title: Complicações watchOSs no Xamarin
description: Este documento descreve como trabalhar com complicações de watchOS no Xamarin. Ele aborda como adicionar uma complicação, escrever uma complicação, modelos e fornecer código de exemplo.
ms.prod: xamarin
ms.assetid: 7ACD9A2B-CF69-46EA-B0C8-10E7D81216E8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/03/2017
ms.openlocfilehash: 7e2b3e93baaeac85267c9db2f414793610521f2e
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200031"
---
# <a name="watchos-complications-in-xamarin"></a>Complicações watchOSs no Xamarin

_o watchOS permite aos desenvolvedores escrever complicações personalizadas para rostos de inspeção_

Esta página explica os diferentes tipos de complicações disponíveis e como adicionar uma complicação ao seu aplicativo watchOS 3.

Observe que cada aplicativo watchOS pode ter apenas uma complicação.

Comece lendo os [documentos da Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ManagingComplications.html) para determinar se seu aplicativo é adequado para uma complicação. Há cinco `CLKComplicationFamily` tipos de exibição dos quais escolher:

[![](complications-images/all-complications-sml.png "Os 5 tipos de CLKComplicationFamily disponíveis: Circular pequeno, modular pequeno, modular grande, utilitário pequeno, utilitário grande")](complications-images/all-complications.png#lightbox)

Os aplicativos podem implementar apenas um estilo ou todos os cinco, dependendo dos dados que estão sendo exibidos.
Você também pode dar suporte à viagem de tempo, fornecendo valores para horários passados e/ou futuros, pois o usuário transforma a Digital Crown.

<a name="adding" />

## <a name="adding-a-complication"></a>Adicionando uma complicação

### <a name="configuration"></a>Configuração

As complicações podem ser adicionadas a um aplicativo de inspeção durante a criação ou adicionadas manualmente a uma solução existente.

### <a name="add-new-project"></a>Adicionar novo projeto...

O assistente **Adicionar novo projeto...** inclui uma caixa de seleção que criará automaticamente uma classe de controlador de complicação e configurará o arquivo **info. plist** :

![](complications-images/file-new-project-sml.png "A caixa de seleção incluir complicação")

### <a name="existing-projects"></a>Projetos existentes

Para adicionar uma complicação a um projeto existente:

1. Crie um novo arquivo de classe **ComplicationController.cs** e `CLKComplicationDataSource`implemente.
2. Configure o **info. plist** do aplicativo para expor a complicação e a identidade que as famílias de complicação têm suporte.

Essas etapas são descritas em mais detalhes abaixo.

<a name="clkcomplicationcontroller" />

### <a name="clkcomplicationdatasource-class"></a>Classe CLKComplicationDataSource

O modelo C# a seguir inclui os métodos mínimos necessários para `CLKComplicationDataSource`implementar um.

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
  public ComplicationController ()
  {
  }
  public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
  {
  }
  public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
  {
  }
  public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
  {
  }
}
```

Siga as instruções [escrevendo uma complicação](#writing) para adicionar código a essa classe.

### <a name="infoplist"></a>Info.plist

O arquivo **info. plist** da extensão de inspeção deve especificar o nome do `CLKComplicationDataSource` e quais famílias de complicação você deseja dar suporte:

[![](complications-images/complications-config-sml.png "Os tipos de família de complicação")](complications-images/complications-config.png#lightbox)

A lista de entrada da **classe da fonte de dados** mostrará os `CLKComplicationDataSource` nomes de classe que a subclasse subclasse que inclui sua lógica de complicação.

## <a name="clkcomplicationdatasource"></a>CLKComplicationDataSource

Toda a funcionalidade de complicação é implementada em uma única classe, `CLKComplicationDataSource` substituindo métodos da classe abstrata `ICLKComplicationDataSource` (que implementa a interface).

### <a name="required-methods"></a>Métodos necessários

Você deve implementar os seguintes métodos para a complicação executar:

- `GetPlaceholderTemplate`-Retornar a exibição estática usada durante a configuração ou quando o aplicativo não puder fornecer um valor.
- `GetCurrentTimelineEntry`-Calcular a exibição correta quando a complicação estiver em execução.
- `GetSupportedTimeTravelDirections`-Retorna opções de `CLKComplicationTimeTravelDirections` `None`como, `Forward` `Backward`, ou `Forward | Backward`.

### <a name="privacy"></a>Privacidade

Complicações que exibem dados pessoais

- `GetPrivacyBehavior` - `CLKComplicationPrivacyBehavior.ShowOnLockScreen`or`HideOnLockScreen`

Se esse método retornar `HideOnLockScreen` , a complicação mostrará um ícone ou o nome do aplicativo (e não os dados) quando o relógio estiver bloqueado.

### <a name="updates"></a>Atualizações

- `GetNextRequestedUpdateDate`-Retornar uma hora em que o sistema operacional deve consultar o aplicativo para obter dados de exibição da complicação atualizada.

Você também pode forçar uma atualização de seu aplicativo iOS.

### <a name="supporting-time-travel"></a>Viagem de tempo de suporte

O suporte à viagem de `GetSupportedTimeTravelDirections` tempo é opcional e controlado pelo método. Se ele retornar `Forward`, `Backward`ou `Forward | Backward` , você deverá implementar os métodos a seguir

- `GetTimelineStartDate`
- `GetTimelineEndDate`
- `GetTimelineEntriesBeforeDate`
- `GetTimelineEntriesAfterDate`

<a name="writing" />

## <a name="writing-a-complication"></a>Escrevendo uma complicação

As complicações variam desde a exibição de dados simples até a renderização de dados e de imagem complicadas com o suporte de viagem de tempo. O código a seguir mostra como criar uma complicação simples de modelo único.

<!--
The [sample]() for this article supports more template styles.
-->

## <a name="sample-code"></a>Código de exemplo

Este exemplo só dá suporte `UtilitarianLarge` ao modelo, portanto, só pode ser selecionado em faces específicas de inspeção que dão suporte a esse tipo de complicação. Ao *selecionar* complicações em uma inspeção, ela EXIBE **minha complicação** e, ao *executá* -la, exibe a  **_hora_ de minuto** do texto (com uma parte do tempo).

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
    public ComplicationController ()
    {
    }
    public ComplicationController (IntPtr p) : base (p)
    {
    }
    public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
    {
        CLKComplicationTimelineEntry entry = null;
    var complicationDisplay = "MINUTE " + DateTime.Now.Minute.ToString(); // text to display on watch face
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge)
        {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText(complicationDisplay); // dynamic display
            entry = CLKComplicationTimelineEntry.Create(NSDate.Now, textTemplate);
        } else {
            Console.WriteLine("Complication family timeline not supported (" + complication.Family + ")");
        }
        handler (entry);
    }
    public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
    {
        CLKComplicationTemplate template = null;
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge) {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat ();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText ("MY COMPLICATION"); // static display
            template = textTemplate;
        } else {
            Console.WriteLine ("Complication family placeholder not not supported (" + complication.Family + ")");
        }
        handler (template);
    }
    public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
    {
        handler (CLKComplicationTimeTravelDirections.None);
    }
}
```


<a name="templates" />

## <a name="complication-templates"></a>Modelos de complicação

Há vários modelos diferentes disponíveis para cada estilo de complicação.
Os modelos de **anel** permitem exibir um anel em estilo de progresso em volta da complicação, que pode ser usada para exibir o progresso ou outro valor graficamente.

[Documentos do CLKComplicationTemplate da Apple](https://developer.apple.com/reference/clockkit/clkcomplicationtemplate)

### <a name="circular-small"></a>Pequeno circular

Esses nomes de classe de modelo são todos prefixados com `CLKComplicationTemplateCircularSmall`:

- **RingImage** -exibe uma única imagem, com um anel de progresso ao seu respeito.
- **RingText** -exibe uma única linha de texto, com um anel de progresso em torno dela.
- **SimpleImage** – basta exibir uma pequena imagem simples.
- **SimpleText** – apenas exibir um pequeno trecho de texto.
- **StackImage** -exibir uma imagem e uma linha de texto, uma acima da outra
- **StackText** -exibe duas linhas de texto.

### <a name="modular-small"></a>Modular pequeno

Esses nomes de classe de modelo são todos prefixados com `CLKComplicationTemplateModularSmall`:

- **ColumnsText** -exibe uma pequena grade de valores de texto (2 linhas e 2 colunas).
- **RingImage** -exibe uma única imagem, com um anel de progresso ao seu respeito.
- **RingText** -exibe uma única linha de texto, com um anel de progresso em torno dela.
- **SimpleImage** – basta exibir uma pequena imagem simples.
- **SimpleText** – apenas exibir um pequeno trecho de texto.
- **StackImage** -exibir uma imagem e uma linha de texto, uma acima da outra
- **StackText** -exibe duas linhas de texto.

### <a name="modular-large"></a>Modular grande

Esses nomes de classe de modelo são todos prefixados com `CLKComplicationTemplateModularLarge`:

- **Colunas** – exiba uma grade de três linhas com 2 colunas, incluindo, opcionalmente, uma imagem à esquerda de cada linha.
- **StandardBody** -exibe uma cadeia de caracteres de cabeçalho em negrito, com duas linhas de texto sem formatação. O cabeçalho pode, opcionalmente, exibir uma imagem à esquerda.
- **Tabela** -exibe uma cadeia de caracteres de cabeçalho em negrito, com uma grade 2x2 de texto abaixo dela. O cabeçalho pode, opcionalmente, exibir uma imagem à esquerda.
- **TallBody** -exibe uma cadeia de caracteres de cabeçalho em negrito, com uma linha de texto de fonte maior abaixo.

### <a name="utilitarian-small"></a>Utilitário pequeno

Esses nomes de classe de modelo são todos prefixados com `CLKComplicationTemplateUtilitarianSmall`:

- **Flat** -exibe uma imagem e algum texto em uma única linha (o texto deve ser curto).
- **RingImage** -exibe uma única imagem, com um anel de progresso ao seu respeito.
- **RingText** -exibe uma única linha de texto, com um anel de progresso em torno dela.
- **Quadrado** -exibe uma imagem quadrada (40px ou 44px quadrado para 38 mm ou 42 mm Apple Watch respectivamente).

### <a name="utilitarian-large"></a>Utilitário grande

Há apenas um modelo para esse estilo de complicação `CLKComplicationTemplateUtilitarianLargeFlat`:.
Ele exibe uma única imagem e algum texto, tudo em uma única linha.



## <a name="related-links"></a>Links relacionados

- [Documentos da Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ComplicationEssentials.html)
- [Vídeo do WWDC](https://developer.apple.com/videos/play/wwdc2015-209/)
