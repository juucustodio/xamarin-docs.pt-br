---
title: Desempenho do Xamarin.Android
description: Há várias técnicas para aumentar o desempenho dos aplicativos criados com o Xamarin.Android. Coletivamente, essas técnicas podem reduzir de forma considerável a quantidade de trabalho que está sendo executado por uma CPU e a quantidade de memória consumida por um aplicativo. Esse artigo descreve e aborda essas técnicas.
ms.prod: xamarin
ms.assetid: dc2e27f2-7f71-4d57-9cf9-165528276613
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 63365ebc12089ced7de621b3a510996fa66119ce
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571955"
---
# <a name="xamarinandroid-performance"></a>Desempenho do Xamarin.Android

_Há muitas técnicas para aumentar o desempenho dos aplicativos criados com o Xamarin. Android. Coletivamente, essas técnicas podem reduzir muito a quantidade de trabalho que está sendo executado por uma CPU e a quantidade de memória consumida por um aplicativo. Este artigo descreve e discute essas técnicas._

## <a name="performance-overview"></a>Visão geral do desempenho

O baixo desempenho de aplicativo se apresenta de várias maneiras. Ele pode fazer com que o aplicativo pareça não responder, deixar a rolagem lenta ou reduzir a vida útil da bateria. No entanto, a otimização do desempenho engloba mais do que apenas a implementação de um código eficiente. A experiência do usuário quanto ao desempenho do aplicativo também deve ser considerada. Por exemplo, garantir que as operações sejam executadas sem impedir o usuário de realizar outras atividades pode ajudar a melhorar a experiência do usuário.

Há várias técnicas para aumentar o desempenho, bem como o desempenho observado, dos aplicativos criados com o Xamarin.Android. Entre elas estão:

- [Otimizar hierarquias de layout](#optimizelayout)
- [Otimizar os modos de exibição de lista](#optimizelistviews)
- [Remover manipuladores de eventos nas atividades](#removeeventhandlers)
- [Limitar o tempo de vida dos serviços](#limitservices)
- [Liberar recursos ao receber notificação](#releaseresources)
- [Liberar recursos quando a interface do usuário estiver oculta](#releaseresourcesuihidden)
- [Otimizar recursos de imagem](#optimizeimages)
- [Descartar os Recursos de Imagem não Utilizados](#disposeimages)
- [Evitar aritmético de ponto flutuante](#avoidfloats)
- [Ignorar caixas de diálogo](#dismissdialogs)

> [!NOTE]
>  Antes de ler esse artigo, você deve primeiro ler [Desempenho de plataforma cruzada](~/cross-platform/deploy-test/memory-perf-best-practices.md), que discute técnicas específicas sem plataforma para melhorar o uso de memória e o desempenho de aplicativos criados usando a plataforma Xamarin.

<a name="optimizelayout"></a>

## <a name="optimize-layout-hierarchies"></a>Otimizar hierarquias de layout

Cada layout adicionado a um aplicativo exige inicialização, layout e desenho. A passagem de layout pode ser cara ao aninhar [`LinearLayout`](xref:Android.Widget.LinearLayout) instâncias que usam o `weight` parâmetro, pois cada filho será medido duas vezes. O uso de instâncias aninhadas do `LinearLayout` pode levar a uma hierarquia de exibição profunda, o que pode resultar em baixo desempenho para layouts que são informados várias vezes, como em um [`ListView`](xref:Android.Widget.ListView) . Portanto, é importante que esses layouts sejam otimizados, uma vez que os benefícios de desempenho serão multiplicados.

Por exemplo, considere o [`LinearLayout`](xref:Android.Widget.LinearLayout) para uma linha de exibição de lista que tem um ícone, um título e uma descrição. O `LinearLayout` conterá um [`ImageView`](xref:Android.Widget.ImageView) e um vertical `LinearLayout` que contém duas [`TextView`](xref:Android.Widget.TextView) instâncias:

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="?android:attr/listPreferredItemHeight"
    android:padding="5dip">
    <ImageView
        android:id="@+id/icon"
        android:layout_width="wrap_content"
        android:layout_height="fill_parent"
        android:layout_marginRight="5dip"
        android:src="@drawable/icon" />
    <LinearLayout
        android:orientation="vertical"
        android:layout_width="0dip"
        android:layout_weight="1"
        android:layout_height="fill_parent">
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="0dip"
            android:layout_weight="1"
            android:gravity="center_vertical"
            android:text="Mei tempor iuvaret ad." />
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="0dip"
            android:layout_weight="1"
            android:singleLine="true"
            android:ellipsize="marquee"
            android:text="Lorem ipsum dolor sit amet." />
    </LinearLayout>
</LinearLayout>
```

Esse layout tem 3 níveis de profundidade e é um desperdício quando é desinflado para cada [`ListView`](xref:Android.Widget.ListView) linha. No entanto, ele pode ser melhorado ao nivelar o layout, conforme mostrado no exemplo de código a seguir:

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="?android:attr/listPreferredItemHeight"
    android:padding="5dip">
    <ImageView
        android:id="@+id/icon"
        android:layout_width="wrap_content"
        android:layout_height="fill_parent"
        android:layout_alignParentTop="true"
        android:layout_alignParentBottom="true"
        android:layout_marginRight="5dip"
        android:src="@drawable/icon" />
    <TextView
        android:id="@+id/secondLine"
        android:layout_width="fill_parent"
        android:layout_height="25dip"
        android:layout_toRightOf="@id/icon"
        android:layout_alignParentBottom="true"
        android:layout_alignParentRight="true"
        android:singleLine="true"
        android:ellipsize="marquee"
        android:text="Lorem ipsum dolor sit amet." />
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/icon"
        android:layout_alignParentRight="true"
        android:layout_alignParentTop="true"
        android:layout_above="@id/secondLine"
        android:layout_alignWithParentIfMissing="true"
        android:gravity="center_vertical"
        android:text="Mei tempor iuvaret ad." />
</RelativeLayout>
```

A hierarquia anterior de três níveis foi reduzida para uma hierarquia de dois níveis e uma única [`RelativeLayout`](xref:Android.Widget.RelativeLayout) substituiu duas [`LinearLayout`](xref:Android.Widget.LinearLayout) instâncias. Um aumento de desempenho significativo será obtido ao recriar o layout de cada [`ListView`](xref:Android.Widget.ListView) linha.

<a name="optimizelistviews"></a>

## <a name="optimize-list-views"></a>Otimizar os modos de exibição de lista

Os usuários esperam uma rolagem suave e tempos de carregamento rápidos para [`ListView`](xref:Android.Widget.ListView) instâncias. No entanto, o desempenho de rolagem pode ser prejudicado quando cada linha do modo de exibição de lista tiver hierarquias de exibição profundamente aninhadas ou quando as linhas do modo de exibição da lista tiverem layouts complexos. Contudo, existem técnicas que podem ser usadas para evitar o baixo desempenho de `ListView`:

- Para obter mais informações sobre como reutilizar os modos de exibição de linha, confira [Reutilizar modos de exibição de linha](#reuserowviews).
- Nivele os layouts, sempre que possível.
- Armazene em cache o conteúdo da linha que for recuperado de um serviço Web.
- Evite colocar a imagem em escala.

Coletivamente, essas técnicas podem ajudar a manter as [`ListView`](xref:Android.Widget.ListView) instâncias rolando suavemente.

<a name="reuserowviews"></a>

### <a name="reuse-row-views"></a>Reutilizar modos de exibição de linha

Ao exibir centenas de linhas em um [`ListView`](xref:Android.Widget.ListView) , seria um desperdício de memória para criar centenas de [`View`](xref:Android.Views.View) objetos quando apenas um pequeno número deles é exibido na tela ao mesmo tempo. Em vez disso, apenas os objetos `View` visíveis nas linhas da tela podem ser carregados na memória, com o **conteúdo** sendo carregado nesses objetos reutilizados. Isso impede a instanciação de centenas de objetos adicionais, economizando tempo e memória.

Portanto, quando uma linha desaparecer da tela, seu modo de exibição poderá ser colocado em uma fila para reutilização, conforme mostrado no exemplo de código a seguir:

```csharp
public override View GetView(int position, View convertView, ViewGroup parent)
{
   View view = convertView; // re-use an existing view, if one is supplied
   if (view == null) // otherwise create a new one
       view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
   // set view properties to reflect data for the given row
   view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
   // return the view, populated with data, for display
   return view;
}
```

À medida que o usuário rola, o [`ListView`](xref:Android.Widget.ListView) chama a `GetView` substituição para solicitar que novas exibições sejam exibidas – se disponível, ela passa uma exibição não usada no `convertView` parâmetro. Se esse valor for `null` , o código criará uma nova [`View`](xref:Android.Views.View) instância, caso contrário, as `convertView` Propriedades poderão ser redefinidas e reutilizadas.

Para obter mais informações, confira [Reutilização do modo de exibição da linha](~/android/user-interface/layouts/list-view/populating.md#row-view-re-use) em [Preenchendo um ListView usando dados](~/android/user-interface/layouts/list-view/populating.md).

<a name="removeeventhandlers"></a>

## <a name="remove-event-handlers-in-activities"></a>Remover manipuladores de eventos nas atividades

Quando uma atividade é destruída no runtime do Android, ela ainda poderá estar ativa no runtime Mono. Portanto, remova os manipuladores de eventos de objetos externos no `Activity.OnPause` para impedir que o runtime mantenha uma referência a uma atividade que foi destruída.

Em uma atividade, declare os manipuladores de eventos no nível de classe:

```csharp
EventHandler<UpdatingEventArgs> service1UpdateHandler;
```

Em seguida, implemente os manipuladores de atividade, como em `OnResume`:

```csharp
service1UpdateHandler = (object s, UpdatingEventArgs args) => {
    this.RunOnUiThread (() => {
        this.updateStatusText1.Text = args.Message;
    });
};
App.Current.Service1.Updated += service1UpdateHandler;
```

Quando a atividade encerra o estado de execução, o `OnPause` é chamado. Na implementação `OnPause`, remova os manipuladores da seguinte maneira:

```csharp
App.Current.Service1.Updated -= service1UpdateHandler;
```

<a name="limitservices"></a>

## <a name="limit-the-lifespan-of-services"></a>Limitar o tempo de vida dos serviços

Quando um serviço é iniciado, o Android mantém o processo do serviço em execução. Isso torna o processo caro, uma vez que sua memória não pode ser paginada ou usada em outro lugar. Portanto, deixar um serviço em execução quando não é necessário aumenta o risco de um aplicativo apresentar um desempenho baixo devido às restrições de memória. A troca de aplicativos também pode se tornar menos eficiente, pois o número de processos que o Android pode armazenar em cache é reduzido.

O tempo de vida de um serviço pode ser limitado ao usar um `IntentService`, que se encerra uma vez que é controlado pela tentativa que o iniciou.

<a name="releaseresources"></a>

## <a name="release-resources-when-notified"></a>Liberar recursos ao receber notificação

Durante o ciclo de vida do aplicativo, o [`OnTrimMemory`](xref:Android.App.Activity.OnTrimMemory*) retorno de chamada fornece uma notificação quando a memória do dispositivo está baixa. Esse retorno de chamada deve ser implementado para detectar as seguintes notificações de nível de memória:

- [`TrimMemoryRunningModerate`](xref:Android.Content.ComponentCallbacks2.TrimMemoryRunningModerate)– o aplicativo *pode* desejar liberar alguns recursos desnecessários.
- [`TrimMemoryRunningLow`](xref:Android.Content.ComponentCallbacks2.TrimMemoryRunningLow)– o aplicativo *deve* liberar recursos desnecessários.
- [`TrimMemoryRunningCritical`](xref:Android.Content.ComponentCallbacks2.TrimMemoryRunningCritical)– o aplicativo *deve* liberar o máximo possível de processos não críticos.

Além disso, quando o processo do aplicativo é armazenado em cache, as seguintes notificações de nível de memória podem ser recebidas pelo [`OnTrimMemory`](xref:Android.App.Activity.OnTrimMemory*) retorno de chamada:

- [`TrimMemoryBackground`](xref:Android.Content.ComponentCallbacks2.TrimMemoryBackground)– recursos de liberação que podem ser recriados de forma rápida e eficiente se o usuário retornar ao aplicativo.
- [`TrimMemoryModerate`](xref:Android.Content.ComponentCallbacks2.TrimMemoryModerate)– a liberação de recursos pode ajudar o sistema a manter os outros processos em cache para melhorar o desempenho geral.
- [`TrimMemoryComplete`](xref:Android.Content.ComponentCallbacks2.TrimMemoryComplete)– o processo do aplicativo será encerrado em breve se mais memória não for recuperada em breve.

As notificações devem ser respondidas liberando recursos com base no nível recebido.

<a name="releaseresourcesuihidden"></a>

## <a name="release-resources-when-the-user-interface-is-hidden"></a>Liberar recursos quando a interface do usuário estiver oculta

Libere quaisquer recursos usados pela interface do usuário do aplicativo quando o usuário navegar para outro aplicativo, visto que eles podem aumentar significativamente a capacidade do Android para processos em cache, o que pode causar um impacto sobre a qualidade da experiência do usuário.

Para receber uma notificação quando o usuário sai da interface de usuário, implemente o [`OnTrimMemory`](xref:Android.App.Activity.OnTrimMemory*) retorno de chamada em `Activity` classes e escute o [`TrimMemoryUiHidden`](xref:Android.Content.ComponentCallbacks2.TrimMemoryUiHidden) nível, o que indica que a interface do usuário está oculta da exibição. Essa notificação será recebida apenas quando *todos* os componentes da interface do usuário do aplicativo ficarem ocultos para o usuário. A liberação de recursos da interface do usuário quando esta notificação é recebida garante que, se o usuário navegar de volta de outra atividade no aplicativo, os recursos da interface do usuário ainda estarão disponíveis para retomar a atividade rapidamente.

<a name="optimizeimages"></a>

## <a name="optimize-image-resources"></a>Otimizar os recursos de imagem

As imagens são alguns dos recursos mais caros que os aplicativos usam e, geralmente, são capturadas em alta resolução. Portanto, ao exibir uma imagem, mostre-a com a resolução necessária para a tela do dispositivo. Se a imagem tiver uma resolução mais alta que a tela, ela deverá ser reduzida.

Para obter mais informações, confira [Otimizar os recursos de imagem](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages) na guia [Desempenho de plataforma cruzada](~/cross-platform/deploy-test/memory-perf-best-practices.md).

<a name="disposeimages"></a>

## <a name="dispose-of-unused-image-resources"></a>Descartar os Recursos de Imagem não Utilizados

Para salvar no uso de memória, é uma boa ideia descartar recursos de imagem grandes, que não são mais utilizados. No entanto, é importante garantir que as imagens sejam descartadas corretamente. Em vez de usar uma invocação `.Dispose()` explícita, é possível aproveitar as instruções [using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) para garantir o uso correto dos objetos `IDisposable`. 

Por exemplo, a classe [Bitmap](xref:Android.Graphics.Bitmap) implementa `IDisposable`. O encapsulamento da instanciação de um objeto `BitMap` em um bloco `using` garante que ele será descartado corretamente na saída do bloco:

```csharp
using (Bitmap smallPic = BitmapFactory.DecodeByteArray(smallImageByte, 0, smallImageByte.Length))
{
    // Use the smallPic bit map here
}
```

Para saber mais sobre a liberação de recursos descartáveis, consulte [Liberar Recursos IDisposable](~/cross-platform/deploy-test/memory-perf-best-practices.md#idisposable).  

<a name="avoidfloats"></a>

## <a name="avoid-floating-point-arithmetic"></a>Evitar aritmético de ponto flutuante

Em dispositivos Android, a aritmética de ponto flutuante é aproximadamente duas vezes mais lenta do que a aritmética de inteiros. Portanto, substitua a aritmética de ponto flutuante pela aritmética de inteiros, se possível. No entanto, não há nenhuma diferença de tempo de execução entre a aritmética `float` e a `double` em hardwares recentes.

> [!NOTE]
> Mesmo para a aritmética de inteiros, algumas CPUs não têm recursos de divisão de hardware. Portanto, as divisões de inteiro e as operações de módulo geralmente são executadas no software.

<a name="dismissdialogs"></a>

## <a name="dismiss-dialogs"></a>Descartar as caixas de diálogo

Ao usar a [`ProgressDialog`](xref:Android.App.ProgressDialog) classe (ou qualquer caixa de diálogo ou alerta), em vez de chamar o [`Hide`](xref:Android.App.Dialog.Hide*) método quando a finalidade da caixa de diálogo for concluída, chame o [`Dismiss`](xref:Android.App.Dialog.Dismiss*) método. Caso contrário, a caixa de diálogo ainda estará ativa e vazará a atividade ao manter uma referência a ela.

## <a name="summary"></a>Resumo

Esse artigo descreve e discute técnicas para aumentar o desempenho dos aplicativos criados com o Xamarin.Android. Coletivamente, essas técnicas podem reduzir de forma considerável a quantidade de trabalho que está sendo executado por uma CPU e a quantidade de memória consumida por um aplicativo.

## <a name="related-links"></a>Links relacionados

- [Desempenho de plataforma cruzada](~/cross-platform/deploy-test/memory-perf-best-practices.md)
