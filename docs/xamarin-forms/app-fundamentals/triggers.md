---
title: Gatilhos do Xamarin.Forms
description: Este artigo explica como usar gatilhos do Xamarin.Forms para responder a alterações na interface do usuário com XAML. Os gatilhos permitem expressar ações declarativamente em XAML que alteram a aparência dos controles com base em eventos ou alterações de propriedade.
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2020
ms.openlocfilehash: bf9c06dae0df7da1cc69a85d8436376494039959
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77635668"
---
# <a name="xamarinforms-triggers"></a>Gatilhos do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)

Os gatilhos permitem expressar ações declarativamente em XAML que alteram a aparência dos controles com base em eventos ou alterações de propriedade. Além disso, os gatilhos de estado, que são um grupo especializado de gatilhos, definem quando se deve aplicar [`VisualState`](xref:Xamarin.Forms.VisualState).

Você pode atribuir um gatilho diretamente a um controle ou adicioná-lo a um dicionário de recursos na página ou aplicativo a ser aplicado a vários controles.

## <a name="property-triggers"></a>Gatilhos de propriedades

Um gatilho simples pode ser expresso puramente em XAML, adicionando um elemento `Trigger` a uma coleção de gatilhos do controle.
Este exemplo mostra um gatilho que altera uma cor da tela de fundo `Entry` quando recebe o foco:

```xaml
<Entry Placeholder="enter name">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
                 Property="IsFocused" Value="True">
            <Setter Property="BackgroundColor" Value="Yellow" />
            <!-- multiple Setters elements are allowed -->
        </Trigger>
    </Entry.Triggers>
</Entry>
```

As partes importantes da declaração do gatilho são:

- **TargetType** – o tipo de controle ao qual o gatilho se aplica.

- **Propriedade** – a propriedade no controle que é monitorado.

- **Valor** – o valor, quando ele ocorre para a propriedade monitorada, que faz o gatilho ativar.

- **Setter** – uma coleção de elementos `Setter` pode ser adicionada quando a condição do gatilho for atendida. Você deve especificar `Property` e `Value` para definir.

- **EnterActions e ExitActions** (não mostrado) – são escritos em código e podem ser usados em (ou em vez de) elementos `Setter`. Eles são [descritos abaixo](#enteractions-and-exitactions).

### <a name="applying-a-trigger-using-a-style"></a>Aplicação de um gatilho usando um estilo

Gatilhos também podem ser adicionados a uma declaração `Style` em um controle, em uma página ou um aplicativo `ResourceDictionary`. Este exemplo declara um estilo implícito (por exemplo, nenhum `Key` está definido), o que significa que ele se aplicará a todos os controles `Entry` na página.

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style TargetType="Entry">
                        <Style.Triggers>
                <Trigger TargetType="Entry"
                         Property="IsFocused" Value="True">
                    <Setter Property="BackgroundColor" Value="Yellow" />
                    <!-- multiple Setters elements are allowed -->
                </Trigger>
            </Style.Triggers>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>
```

## <a name="data-triggers"></a>Gatilhos de dados

Os gatilhos de dados usam a associação de dados para monitorar outro controle para fazer com que `Setter`s sejam chamados. Em vez do atributo `Property` em um gatilho de propriedade, defina o atributo `Binding` para monitorar o valor especificado.

O exemplo a seguir usa a sintaxe de associação de dados `{Binding Source={x:Reference entry}, Path=Text.Length}`
que é como nos referimos a outras propriedades do controle. Quando o tamanho do `entry` for zero, o gatilho será ativado. Neste exemplo, o gatilho desabilita o botão quando a entrada está vazia.

```xaml
<!-- the x:Name is referenced below in DataTrigger-->
<!-- tip: make sure to set the Text="" (or some other default) -->
<Entry x:Name="entry"
       Text=""
       Placeholder="required field" />

<Button x:Name="button" Text="Save"
        FontSize="Large"
        HorizontalOptions="Center">
    <Button.Triggers>
        <DataTrigger TargetType="Button"
                     Binding="{Binding Source={x:Reference entry},
                                       Path=Text.Length}"
                     Value="0">
            <Setter Property="IsEnabled" Value="False" />
            <!-- multiple Setters elements are allowed -->
        </DataTrigger>
    </Button.Triggers>
</Button>
```

> [!TIP]
> Ao avaliar `Path=Text.Length`, sempre forneça um valor padrão para a propriedade de destino (por exemplo, `Text=""`), pois, caso contrário, ele será `null` e o gatilho não funcionará como esperado.

Além de especificar `Setter`s, você também pode fornecer [`EnterActions` e `ExitActions`](#enteractions-and-exitactions).

## <a name="event-triggers"></a>Gatilhos de evento

O elemento `EventTrigger` requer apenas uma propriedade `Event`, como `"Clicked"` no exemplo a seguir.

```xaml
<EventTrigger Event="Clicked">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Observe que não há elementos `Setter`, mas, em vez disso, uma referência a uma classe definida por `local:NumericValidationTriggerAction`, que requer que o `xmlns:local` seja declarado no XAML da página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

A classe em si implementa `TriggerAction`, o que significa que ela deve fornecer uma substituição para o método `Invoke` que é chamado sempre que ocorre o evento de gatilho.

Uma implementação de ação do gatilho deve:

- Implementar a classe `TriggerAction<T>` genérica com o parâmetro genérico correspondente com o tipo de controle ao qual o gatilho será aplicado. É possível usar superclasses, como `VisualElement`, para gravar ações de gatilho que funcionam com uma variedade de controles ou especificar um tipo de controle, como `Entry`.

- Substituir o método `Invoke` – isso é chamado sempre que os critérios do gatilho forem atendidos.

- Outra opção é expor propriedades que podem ser definidas no XAML quando o gatilho é declarado. Para obter um exemplo disso, confira a classe `VisualElementPopTriggerAction` no respectivo aplicativo de exemplo.

```csharp
public class NumericValidationTriggerAction : TriggerAction<Entry>
{
    protected override void Invoke (Entry entry)
    {
        double result;
        bool isValid = Double.TryParse (entry.Text, out result);
        entry.TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

O gatilho de evento pode ser consumido do XAML:

```xaml
<EventTrigger Event="TextChanged">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Tenha cuidado ao compartilhar gatilhos em um `ResourceDictionary`, pois uma instância será compartilhada entre os controles para que qualquer estado que está configurado uma vez se aplique a todos.

Observe que os gatilhos de evento não dão suporte a `EnterActions` e `ExitActions`    [descritos abaixo](#enteractions-and-exitactions).

## <a name="multi-triggers"></a>Múltiplos gatilhos

Um `MultiTrigger` é semelhante a um `Trigger` ou `DataTrigger`, exceto que pode haver mais de uma condição. Todas as condições devem ser verdadeiras antes de `Setter`s serem disparados.

Aqui está um exemplo de um gatilho para um botão que é associado a duas entradas diferentes (`email` e `phone`):

```xaml
<MultiTrigger TargetType="Button">
    <MultiTrigger.Conditions>
        <BindingCondition Binding="{Binding Source={x:Reference email},
                                   Path=Text.Length}"
                               Value="0" />
        <BindingCondition Binding="{Binding Source={x:Reference phone},
                                   Path=Text.Length}"
                               Value="0" />
    </MultiTrigger.Conditions>
    <Setter Property="IsEnabled" Value="False" />
    <!-- multiple Setter elements are allowed -->
</MultiTrigger>
```

A coleção `Conditions` também pode conter elementos `PropertyCondition` como este:

```xaml
<PropertyCondition Property="Text" Value="OK" />
```

### <a name="building-a-require-all-multi-trigger"></a>Criar um gatilho múltiplo que “exige tudo”

O gatilho múltiplo apenas atualiza seu controle quando todas as condições são verdadeiras. Fazer o teste para "todos os tamanhos de campo são zero" (como uma página de logon na qual todas as entradas devem ser concluídas) é complicado porque você deseja uma condição "em que Text.Length > 0", mas isso não pode ser expresso em XAML.

Isso pode ser feito com um `IValueConverter`. O código de conversor abaixo transforma o `Text.Length` que se associa a um `bool` que indica se um campo está vazio ou não:

```csharp
public class MultiTriggerConverter : IValueConverter
{
    public object Convert(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        if ((int)value > 0) // length > 0 ?
            return true;            // some data has been entered
        else
            return false;            // input is empty
    }

    public object ConvertBack(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        throw new NotSupportedException ();
    }
}
```

Para usar esse conversor em um gatilho múltiplo, primeiro adicione-o ao dicionário de recursos da página (junto com uma definição de namespace `xmlns:local` personalizada):

```xaml
<ResourceDictionary>
   <local:MultiTriggerConverter x:Key="dataHasBeenEntered" />
</ResourceDictionary>
```

O XAML é mostrado abaixo. Observe as seguintes diferenças do primeiro exemplo de gatilho múltiplo:

- O botão tem `IsEnabled="false"` definido por padrão.
- As condições de gatilho múltiplo usam o conversor para transformar o valor `Text.Length` em um `boolean`.
- Quando todas as condições forem `true`, o setter transformará o `IsEnabled` do botão na propriedade `true`.

```xaml
<Entry x:Name="user" Text="" Placeholder="user name" />

<Entry x:Name="pwd" Text="" Placeholder="password" />

<Button x:Name="loginButton" Text="Login"
        FontSize="Large"
        HorizontalOptions="Center"
        IsEnabled="false">
  <Button.Triggers>
    <MultiTrigger TargetType="Button">
      <MultiTrigger.Conditions>
        <BindingCondition Binding="{Binding Source={x:Reference user},
                              Path=Text.Length,
                              Converter={StaticResource dataHasBeenEntered}}"
                          Value="true" />
        <BindingCondition Binding="{Binding Source={x:Reference pwd},
                              Path=Text.Length,
                              Converter={StaticResource dataHasBeenEntered}}"
                          Value="true" />
      </MultiTrigger.Conditions>
      <Setter Property="IsEnabled" Value="True" />
    </MultiTrigger>
  </Button.Triggers>
</Button>
```

Essas capturas de tela mostram a diferença entre os dois exemplos de gatilho múltiplo acima. Na parte superior das telas, a entrada de texto em apenas um `Entry` é suficiente para habilitar o botão **Salvar**.
Na parte inferior das telas, o botão **Logon** permanece inativo até que ambos os campos contenham dados.

![](triggers-images/multi-requireall.png "MultiTrigger Examples")

## <a name="enteractions-and-exitactions"></a>EnterActions e ExitActions

Outra maneira de implementar alterações quando ocorre um gatilho é adicionando as coleções `EnterActions` e `ExitActions` e especificando as implementações `TriggerAction<T>`.

A coleção [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) é usada para definir um `IList` de [`TriggerAction`](xref:Xamarin.Forms.TriggerAction) objetos que serão invocados quando a condição do gatilho for atendida. A coleção [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) é usada para definir um `IList` de `TriggerAction` objetos que serão invocados depois que a condição do gatilho não for mais atendida.

> [!NOTE]
> Os objetos [`TriggerAction`](xref:Xamarin.Forms.TriggerAction) definidos nas coleções `EnterActions` e `ExitActions` são ignorados pela classe [`EventTrigger`](xref:Xamarin.Forms.EventTrigger).    

Você pode fornecer *ambos* `EnterActions` e `ExitActions`, bem como `Setter`s em um gatilho, mas lembre-se de que `Setter`s são chamados imediatamente (eles não aguardam o `EnterAction` ou o `ExitAction` ser concluído). Como alternativa, você pode executar tudo no código e não usar `Setter`s.

```xaml
<Entry Placeholder="enter job title">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
                 Property="Entry.IsFocused" Value="True">
            <Trigger.EnterActions>
                <local:FadeTriggerAction StartsFrom="0"" />
            </Trigger.EnterActions>

            <Trigger.ExitActions>
                <local:FadeTriggerAction StartsFrom="1" />
            </Trigger.ExitActions>
            <!-- You can use both Enter/Exit and Setter together if required -->
        </Trigger>
    </Entry.Triggers>
</Entry>
```

Como sempre, quando uma classe é referenciada no XAML você deve declarar um namespace como `xmlns:local`, conforme mostrado aqui:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

O código `FadeTriggerAction` é mostrado abaixo:

```csharp
public class FadeTriggerAction : TriggerAction<VisualElement>
{
    public int StartsFrom { set; get; }

    protected override void Invoke(VisualElement sender)
    {
        sender.Animate("FadeTriggerAction", new Animation((d) =>
        {
            var val = StartsFrom == 1 ? d : 1 - d;
            // so i was aiming for a different color, but then i liked the pink :)
            sender.BackgroundColor = Color.FromRgb(1, val, 1);
        }),
        length: 1000, // milliseconds
        easing: Easing.Linear);
    }
}
```

## <a name="state-triggers"></a>Gatilhos de estado

Os gatilhos de estado foram introduzidos no Xamarin.Forms 4.5 e são um grupo especializado de gatilhos que definem as condições sob as quais um [`VisualState`](xref:Xamarin.Forms.VisualState) deve ser aplicado. Mas, no momento, eles são experimentais e só podem ser usados adicionando a seguinte linha de código ao arquivo *App.xaml.cs*:

```csharp
Device.SetFlags(new string[]{ "StateTriggers_Experimental" });
```

Os gatilhos de estado são adicionados à coleção de [`StateTriggers`](xref:Xamarin.Forms.VisualState.StateTriggers) de um [`VisualState`](xref:Xamarin.Forms.VisualState). Essa coleção pode conter um ou vários gatilhos de estado. Um [`VisualState`](xref:Xamarin.Forms.VisualState) será aplicado quando qualquer gatilho de estado na coleção estiver ativo.

Ao usar gatilhos de estado para controlar os estados visuais, o Xamarin.Forms usa as seguintes regras de precedência para determinar qual gatilho (e [`VisualState`](xref:Xamarin.Forms.VisualState) correspondente) estará ativo:

1. Um gatilho que deriva de [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase).
1. Um [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) ativado porque a condição [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowWidth) foi atendida.
1. Um [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) ativado porque a condição [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) foi atendida.

Se múltiplos gatinhos estiverem simultaneamente ativos (por exemplo, dois gatilhos personalizados), o primeiro gatilho declarado na marcação terá precedência.

> [!NOTE]
> Os gatilhos de estado podem ser definidos em [`Style`](xref:Xamarin.Forms.Style) ou diretamente nos elementos.

Confira mais informações sobre os estados visuais em [Gerenciador de estado visual do Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

### <a name="state-trigger"></a>Gatilho de estado

A classe [`StateTrigger`](xref:Xamarin.Forms.StateTrigger), que deriva da classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase), tem uma propriedade associável [`IsActive`](xref:Xamarin.Forms.StateTrigger.IsActive). Um `StateTrigger` aciona o gatilho de uma alteração [`VisualState`](xref:Xamarin.Forms.VisualState) quando a propriedade `IsActive` muda de valor.

A classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase), que é a classe base de todos os gatilhos de estado, tem uma propriedade [`IsActive`](xref:Xamarin.Forms.StateTriggerBase.IsActive) e um evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged). Esse evento é disparado sempre que ocorre uma alteração em [`VisualState`](xref:Xamarin.Forms.VisualState).

> [!IMPORTANT]
> A propriedade associável [`StateTrigger.IsActive`](xref:Xamarin.Forms.StateTrigger.IsActive) oculta a propriedade herdada [`StateTriggerBase.IsActive`](xref:Xamarin.Forms.StateTriggerBase.IsActive).

O exemplo de XAML a seguir mostra um [`Style`](xref:Xamarin.Forms.Style) que inclui os objetos [`StateTrigger`](xref:Xamarin.Forms.StateTrigger):

```xaml
<Style TargetType="Grid">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Checked">
                    <VisualState.StateTriggers>
                        <StateTrigger IsActive="{Binding IsToggled}"
                                      IsActiveChanged="OnCheckedStateIsActiveChanged" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Black" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Unchecked">
                    <VisualState.StateTriggers>
                        <StateTrigger IsActive="{Binding IsToggled, Converter={StaticResource inverseBooleanConverter}}"
                                      IsActiveChanged="OnUncheckedStateIsActiveChanged" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="White" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

Nesse exemplo, o [`Style`](xref:Xamarin.Forms.Style) implícito tem como destino os objetos [`Grid`](xref:Xamarin.Forms.Grid). Quando a propriedade `IsToggled` do objeto associado é `true`, a cor da tela de fundo de `Grid` é definida como preto. Quando a propriedade `IsToggled` do objeto associado se torna `false`, uma alteração de [`VisualState`](xref:Xamarin.Forms.VisualState) é disparada, e a cor da tela de fundo de `Grid` se torna branca.

Além disso, a cada vez que a alteração [`VisualState`](xref:Xamarin.Forms.VisualState) ocorre, o evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) de `VisualState` é disparado. Cada `VisualState` registra um manipulador de eventos para esse evento:

```csharp
void OnCheckedStateIsActiveChanged(object sender, EventArgs e)
{
    StateTriggerBase stateTrigger = sender as StateTriggerBase;
    Console.WriteLine($"Checked state active: {stateTrigger.IsActive}");
}

void OnUncheckedStateIsActiveChanged(object sender, EventArgs e)
{
    StateTriggerBase stateTrigger = sender as StateTriggerBase;
    Console.WriteLine($"Unchecked state active: {stateTrigger.IsActive}");
}
```

Nesse exemplo, quando um manipulador do evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) é disparado, o manipulador informa se [`VisualState`](xref:Xamarin.Forms.VisualState) está ativo ou não. Por exemplo, as mensagens a seguir são enviadas para a janela do console ao alterar do estado visual `Checked` para o estado visual `Unchecked`:

```
Checked state active: False
Unchecked state active: True
```

> [!NOTE]
> É possível criar gatilhos de estado personalizado derivados da classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase).

### <a name="adaptive-trigger"></a>Gatilho adaptável

Um [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) dispara uma alteração [`VisualState`](xref:Xamarin.Forms.VisualState) quando a janela tem a altura ou largura especificada. Esse gatilho tem duas propriedades associáveis:

- [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight), do tipo `double`, que indica a altura mínima da janela na qual [`VisualState`](xref:Xamarin.Forms.VisualState) deve ser aplicado.
- [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight), do tipo `double`, que indica a largura mínima da janela na qual [`VisualState`](xref:Xamarin.Forms.VisualState) deve ser aplicado.

> [!NOTE]
> [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) deriva da classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) e, portanto, pode anexar um manipulador de eventos ao evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged).

O exemplo de XAML a seguir mostra um [`Style`](xref:Xamarin.Forms.Style) que inclui os objetos [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger):

```xaml
<Style TargetType="StackLayout">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Vertical">
                    <VisualState.StateTriggers>
                        <AdaptiveTrigger MinWindowWidth="0" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="Orientation"
                                Value="Vertical" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Horizontal">
                    <VisualState.StateTriggers>
                        <AdaptiveTrigger MinWindowWidth="800" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="Orientation"
                                Value="Horizontal" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

Nesse exemplo, o [`Style`](xref:Xamarin.Forms.Style) implícito tem como destino os objetos [`StackLayout`](xref:Xamarin.Forms.StackLayout). Quando a largura da janela está entre 0 e 800 unidades independentes de dispositivo, os objetos de `StackLayout` aos quais `Style` é aplicado terão uma orientação vertical. Quando a largura da janela for >= 800 unidades independentes de dispositivo, a alteração de [`VisualState`](xref:Xamarin.Forms.VisualState) será disparada, e a orientação de `StackLayout` mudará para horizontal:

![VisualState de StackLayout Vertical](triggers-images/adaptivetrigger-vertical.png "Exemplo de AdaptiveTrigger")
![VisualState de StackLayout Horizontal](triggers-images/adaptivetrigger-horizontal.png "Exemplo de AdaptiveTrigger")

As propriedades [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) e [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) podem ser usadas de forma independente ou em conjunto uma com a outra. O XAML a seguir mostra um exemplo da definição das duas propriedades:

```xaml
<AdaptiveTrigger MinWindowWidth="800"
                 MinWindowHeight="1200"/>
```

Nesse exemplo, [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) indica que o [`VisualState`](xref:Xamarin.Forms.VisualState) correspondente será aplicado quando a largura atual da janela for >= 800 unidades independentes de dispositivo e a altura atual da janela for >= 1200 unidades independentes de dispositivo.

### <a name="compare-state-trigger"></a>Comparar gatilho de estado

O [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger) dispara uma alteração de [`VisualState`](xref:Xamarin.Forms.VisualState) quando a propriedade é igual a um valor específico. Esse gatilho tem duas propriedades associáveis:

- [`Property`](xref:Xamarin.Forms.CompareStateTrigger.Property), do tipo `object`, que indica a propriedade comparada pelo gatilho.
- [`Value`](xref:Xamarin.Forms.CompareStateTrigger.Value), do tipo `object`, que indica o valor no qual [`VisualState`](xref:Xamarin.Forms.VisualState) deve ser aplicado.

> [!NOTE]
> [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger) deriva da classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) e, portanto, pode anexar um manipulador de eventos ao evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged).

O exemplo de XAML a seguir mostra um [`Style`](xref:Xamarin.Forms.Style) que inclui os objetos [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger):

```xaml
<Style TargetType="Grid">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Checked">
                    <VisualState.StateTriggers>
                        <CompareStateTrigger Property="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                                             Value="True" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Black" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Unchecked">
                    <VisualState.StateTriggers>
                        <CompareStateTrigger Property="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                                             Value="False" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="White" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
...
<Grid>
    <Frame BackgroundColor="White"
           CornerRadius="12"
           Margin="24"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <StackLayout Orientation="Horizontal">
            <CheckBox x:Name="checkBox"
                      VerticalOptions="Center" />
            <Label Text="Check the CheckBox to modify the Grid background color."
                   VerticalOptions="Center" />
        </StackLayout>
    </Frame>
</Grid>
```

Nesse exemplo, o [`Style`](xref:Xamarin.Forms.Style) implícito tem como destino os objetos [`Grid`](xref:Xamarin.Forms.Grid). Quando a propriedade [`IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) do objeto [`CheckBox`](xref:Xamarin.Forms.CheckBox) é `false`, a cor da tela de fundo de `Grid` é definida como branca. Quando a propriedade `CheckBox.IsChecked` se torna `true`, uma alteração de [`VisualState`](xref:Xamarin.Forms.VisualState) é disparada, e a cor da tela de fundo de `Grid` se torna preta:

[![Captura de tela de alteração de estado visual disparado, em iOS e Android](triggers-images/comparestatetrigger-unchecked.png "Exemplo de CompareStateTrigger")](triggers-images/comparestatetrigger-unchecked-large.png#lightbox "Exemplo de CompareStateTrigger")
[![Captura de tela de alteração de estado visual disparado, em iOS e Android](triggers-images/comparestatetrigger-checked.png "Exemplo de CompareStateTrigger")](triggers-images/comparestatetrigger-unchecked-large.png#lightbox "Exemplo de CompareStateTrigger")

### <a name="device-state-trigger"></a>Gatilho de estado de dispositivo

O [`DeviceStateTrigger`](xref:Xamarin.Forms.DeviceStateTrigger) dispara uma alteração de [`VisualState`](xref:Xamarin.Forms.VisualState) com base na plataforma do dispositivo na qual o aplicativo está em execução. Esse gatilho tem uma única propriedade associável:

- [`Device`](xref:Xamarin.Forms.DeviceStateTrigger.Device), do tipo `string`, que indica a plataforma do dispositivo na qual [`VisualState`](xref:Xamarin.Forms.VisualState) deve ser aplicado.

> [!NOTE]
> [`DeviceStateTrigger`](xref:Xamarin.Forms.DeviceStateTrigger) deriva da classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) e, portanto, pode anexar um manipulador de eventos ao evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged).

O exemplo de XAML a seguir mostra um [`Style`](xref:Xamarin.Forms.Style) que inclui os objetos `DeviceStateTrigger`:

```xaml
<Style x:Key="DeviceStateTriggerPageStyle"
       TargetType="ContentPage">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="iOS">
                    <VisualState.StateTriggers>
                        <DeviceStateTrigger Device="iOS" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Silver" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Android">
                    <VisualState.StateTriggers>
                        <DeviceStateTrigger Device="Android" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="#2196F3" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="UWP">
                    <VisualState.StateTriggers>
                        <DeviceStateTrigger Device="UWP" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Aquamarine" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

Nesse exemplo, o [`Style`](xref:Xamarin.Forms.Style) explícito tem como destino os objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage). Os objetos `ContentPage` que consomem o estilo definem a cor de plano de fundo como prata no iOS, azul claro no Android e água-marinha no UWP. As capturas de tela a seguir mostram as páginas resultantes no iOS e Android:

[![Captura de tela de uma alteração de estado visual disparada, no iOS e no Android](triggers-images/devicestatetrigger.png "Exemplo de DeviceStateTrigger")](triggers-images/devicestatetrigger-large.png#lightbox "Exemplo de DeviceStateTrigger")

### <a name="orientation-state-trigger"></a>Gatilho de estado de orientação

O [`OrientationStateTrigger`](xref:Xamarin.Forms.OrientationStateTrigger) dispara uma alteração [`VisualState`](xref:Xamarin.Forms.VisualState) quando a orientação do dispositivo é alterada. Esse gatilho tem uma única propriedade associável:

- [`Orientation`](xref:Xamarin.Forms.OrientationStateTrigger.Orientation), do tipo [`DeviceOrientation`](xref:Xamarin.Forms.Internals.DeviceOrientation), que indica a orientação que deve ser aplicada a [`VisualState`](xref:Xamarin.Forms.VisualState).

> [!NOTE]
> [`OrientationStateTrigger`](xref:Xamarin.Forms.OrientationStateTrigger) deriva da classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) e, portanto, pode anexar um manipulador de eventos ao evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged).

O exemplo de XAML a seguir mostra um [`Style`](xref:Xamarin.Forms.Style) que inclui os objetos `OrientationStateTrigger`:

```xaml
<Style x:Key="OrientationStateTriggerPageStyle"
       TargetType="ContentPage">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Portrait">
                    <VisualState.StateTriggers>
                        <OrientationStateTrigger Orientation="Portrait" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Silver" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Landscape">
                    <VisualState.StateTriggers>
                        <OrientationStateTrigger Orientation="Landscape" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="White" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

Nesse exemplo, o [`Style`](xref:Xamarin.Forms.Style) explícito tem como destino os objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage). Os objetos `ContentPage` que consomem o estilo definem sua cor de plano de fundo como prata quando a orientação é retrato e como branco quando a orientação é paisagem.

## <a name="related-links"></a>Links relacionados

- [Exemplo de gatilhos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)
- [Gerenciador de Estado Visual do Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
- [API de Gatilho do Xamarin.Forms](xref:Xamarin.Forms.TriggerAction`1)
