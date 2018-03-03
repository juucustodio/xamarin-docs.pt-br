---
title: "Complicações"
description: "watchOS permite que os desenvolvedores criem complicações personalizadas para inspecionar as faces"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7ACD9A2B-CF69-46EA-B0C8-10E7D81216E8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/03/2017
ms.openlocfilehash: a13de7fbb4b6e1f9fa2853ce599f3a038a5e4040
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="complications"></a>Complicações

_watchOS permite que os desenvolvedores criem complicações personalizadas para inspecionar as faces_

Esta página explica os diferentes tipos de complicações disponíveis e como adicionar um complicativo para seu aplicativo watchOS 3.

Observe que cada aplicativo watchOS pode ter apenas um complicativo.

Comece lendo [documentos da Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ManagingComplications.html) para determinar se seu aplicativo é adequado para um complicativo. Há 5 `CLKComplicationFamily` tipos de exibição para escolher:

[ ![](complications-images/all-complications-sml.png "Os tipos de CLKComplicationFamily 5 disponíveis: Circular pequeno, pequeno Modular, Modular grande, utilitários pequeno, utilitários grande")](complications-images/all-complications.png)

Aplicativos podem implementar apenas um estilo ou todas as cinco, dependendo dos dados que está sendo exibidos.
Você também pode dar suporte a viagem, fornecer valores para horas anteriores e/ou futuras como o usuário ativa o coroa Digital.

<a name="adding" />

## <a name="adding-a-complication"></a>Adicionar um Complicativo

### <a name="configuration"></a>Configuração

Complicações podem ser adicionadas a um aplicativo de inspeção de variáveis durante a criação ou adicionadas manualmente a uma solução existente.

### <a name="add-new-project"></a>Adicione novo projeto...

O **adicionar novo projeto...**  assistente inclui uma caixa de seleção que criará automaticamente uma classe de controlador do complicativo e configurar o **Info. plist** arquivo:

![](complications-images/file-new-project-sml.png "A caixa de seleção incluir Complicativo")

### <a name="existing-projects"></a>Projetos existentes

Para adicionar um complicativo a um projeto existente:

1. Criar um novo **ComplicationController.cs** arquivo de classe e implementar `CLKComplicationDataSource`.
2. Configurar o aplicativo **Info. plist** exponha do complicativo e identidade quais famílias complicativo têm suporte.

Essas etapas são descritas em mais detalhes abaixo.

<a name="clkcomplicationcontroller" />

### <a name="clkcomplicationdatasource-class"></a>Classe CLKComplicationDataSource

O modelo c# a seguir inclui os métodos mínimo necessários para implementar um `CLKComplicationDataSource`.

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

Siga o [escrevendo um complicativo](#writing) instruções para adicionar código para essa classe.

### <a name="infoplist"></a>Info.plist

A extensão de inspeção **Info. plist** arquivo deve especificar o nome do `CLKComplicationDataSource` e quais famílias complicativo que você deseja dar suporte:

[ ![](complications-images/complications-config-sml.png "Os tipos de família do complicativo")](complications-images/complications-config.png)

O **classe da fonte de dados** lista de entrada Mostrar nomes de classe que subclasse `CLKComplicationDataSource` subclasse que inclui a lógica do complicativo.

## <a name="clkcomplicationdatasource"></a>CLKComplicationDataSource

Todas as funcionalidades do complicativo é implementada em uma única classe, substituindo métodos o `CLKComplicationDataSource` classe abstrata (que implementa o `ICLKComplicationDataSource` interface).

### <a name="required-methods"></a>Métodos necessários

Você deve implementar os seguintes métodos para o complicativo executar:

- `GetPlaceholderTemplate` -A exibição estática usada durante a configuração ou quando o aplicativo não pode fornecer um valor de retorno.
- `GetCurrentTimelineEntry` -Calcule a exibição correta quando o complicativo está em execução.
- `GetSupportedTimeTravelDirections` -Retorna opções de `CLKComplicationTimeTravelDirections` como `None`, `Forward`, `Backward`, ou `Forward | Backward`.

### <a name="privacy"></a>Privacidade

Complicações que exibem dados pessoais

* `GetPrivacyBehavior` - `CLKComplicationPrivacyBehavior.ShowOnLockScreen` Ou `HideOnLockScreen`

Se esse método retornar `HideOnLockScreen` complicativo mostrará a um ícone ou o nome do aplicativo (e não os dados) quando o relógio está bloqueado.

### <a name="updates"></a>Atualizações

- `GetNextRequestedUpdateDate` -Retorna um tempo quando o sistema operacional lado deve consultar o aplicativo para exibir dados de complicativo atualizado.

Você também pode forçar uma atualização de seu aplicativo iOS.

### <a name="supporting-time-travel"></a>Suporte a viagem

Suporte de viagem de tempo é opcional e é controlado pelo `GetSupportedTimeTravelDirections` método. Se ele retorna `Forward`, `Backward`, ou `Forward | Backward` , em seguida, você deve implementar os métodos a seguir

- `GetTimelineStartDate`
- `GetTimelineEndDate`
- `GetTimelineEntriesBeforeDate`
- `GetTimelineEntriesAfterDate`

<a name="writing" />

## <a name="writing-a-complication"></a>Escrevendo um Complicativo

Intervalo de complicações de dados simples exibir imagem complicada e renderização de dados com suporte de viagem. O código a seguir mostra como criar um complicativo simple, o único modelo.

<!--
The [sample]() for this article supports more template styles.
-->

## <a name="sample-code"></a>Código de exemplo

Este exemplo só oferece suporte a `UtilitarianLarge` modelo, para que só pode ser selecionado em faces inspecionar específicas que oferecem suporte a esse tipo de complicativo. Quando *selecionando* complicações em um relógio, ele exibe **COMPLICATIVO Meus** e quando *executando* exibe o texto **minuto _hora_**   (com uma parte do tempo).

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

## <a name="complication-templates"></a>Modelos do complicativo

Há vários modelos diferentes para cada estilo complicativo.
O **anel** modelos permitem que você exibir um anel de progresso estilo ao redor do complicativo, que pode ser usado para exibir graficamente o andamento ou algum outro valor.

[Documentos de CLKComplicationTemplate da Apple](https://developer.apple.com/reference/clockkit/clkcomplicationtemplate)

### <a name="circular-small"></a>Pequeno circular

Esses nomes de classe de modelo são prefixados com `CLKComplicationTemplateCircularSmall`:

- **RingImage** -exibir uma única imagem, com um anel de progresso ao redor dele.
- **RingText** -exibir uma única linha de texto, com um anel de progresso ao redor dele.
- **SimpleImage** -exibem apenas uma única imagem pequena.
- **SimpleText** -exibem apenas um pequeno trecho de texto.
- **StackImage** -exibir uma imagem e uma linha de texto, um acima do outro
- **StackText** -exibir duas linhas de texto.

### <a name="modular-small"></a>Modular pequeno

Esses nomes de classe de modelo são prefixados com `CLKComplicationTemplateModularSmall`:

- **ColumnsText** -exibir uma grade pequena de valores de texto (2 linhas e 2 colunas).
- **RingImage** -exibir uma única imagem, com um anel de progresso ao redor dele.
- **RingText** -exibir uma única linha de texto, com um anel de progresso ao redor dele.
- **SimpleImage** -exibem apenas uma única imagem pequena.
- **SimpleText** -exibem apenas um pequeno trecho de texto.
- **StackImage** -exibir uma imagem e uma linha de texto, um acima do outro
- **StackText** -exibir duas linhas de texto.

### <a name="modular-large"></a>Modular grande

Esses nomes de classe de modelo são prefixados com `CLKComplicationTemplateModularLarge`:

- **Colunas** -exibir uma grade de 3 linhas com 2 colunas, incluindo, opcionalmente, uma imagem à esquerda de cada linha.
- **StandardBody** -exibir uma cadeia de caracteres de cabeçalho em negrito, com duas linhas de texto sem formatação. O cabeçalho, opcionalmente, pode exibir uma imagem à esquerda.
- **Tabela** -exibir uma cadeia de caracteres de cabeçalho em negrito, com uma grade de 2 x 2 de texto abaixo dela. O cabeçalho, opcionalmente, pode exibir uma imagem à esquerda.
- **TallBody** -exibir uma cadeia de caracteres de cabeçalho em negrito, com uma maior fonte única linha de texto abaixo.

### <a name="utilitarian-small"></a>Utilitários pequeno

Esses nomes de classe de modelo são prefixados com `CLKComplicationTemplateUtilitarianSmall`:

- **Simples** -exibe uma imagem e texto em uma única linha (o texto deve ser curto).
- **RingImage** -exibir uma única imagem, com um anel de progresso ao redor dele.
- **RingText** -exibir uma única linha de texto, com um anel de progresso ao redor dele.
- **Quadrado** -exibir uma imagem de quadrados (40px ou 44px quadrado para a 38 mm ou mm 42 Apple Watch respectivamente).

### <a name="utilitarian-large"></a>Utilitários grande

Há apenas um modelo para esse estilo complicativo: `CLKComplicationTemplateUtilitarianLargeFlat`.
Ele exibe uma única imagem e texto, tudo em uma única linha.



## <a name="related-links"></a>Links relacionados

- [Documentos da Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ComplicationEssentials.html)
- [Vídeo WWDC](https://developer.apple.com/videos/play/wwdc2015-209/)
