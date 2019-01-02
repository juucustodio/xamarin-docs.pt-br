---
title: Gatilhos do Xamarin.Forms
description: Este artigo explica como usar gatilhos do Xamarin.Forms para responder a alterações na interface do usuário com XAML. Os gatilhos permitem expressar ações declarativamente em XAML que alteram a aparência dos controles com base em eventos ou alterações de propriedade.
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: 7a273d43ce43ca2946e1ff3177fc4575cd92a534
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53050602"
---
# <a name="xamarinforms-triggers"></a>Gatilhos do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/WorkingWithTriggers)

Os gatilhos permitem expressar ações declarativamente em XAML que alteram a aparência dos controles com base em eventos ou alterações de propriedade.

Você pode atribuir um gatilho diretamente a um controle ou adicioná-lo a um dicionário de recursos na página ou aplicativo a ser aplicado a vários controles.

Há quatro tipos de gatilho:

* [Gatilho de propriedade](#property) – ocorre quando uma propriedade em um controle é definida como um valor específico.

* [Gatilho de dados](#data) – usa associação de dados para disparar com base nas propriedades de outro controle.

* [Gatilho de evento](#event) – ocorre quando acontece um evento no controle.

* [Gatilho múltiplo](#multi) – permite que várias condições de gatilho sejam definidas antes que uma ação ocorra.

<a name="property" />

## <a name="property-triggers"></a>Gatilhos de propriedade

Um gatilho simples pode ser expresso puramente em XAML, adicionando um elemento `Trigger` a uma coleção de gatilhos do controle.
Este exemplo mostra um gatilho que altera uma cor da tela de fundo `Entry` quando recebe o foco:

```xaml
<Entry Placeholder="enter name">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
             Property="IsFocused" Value="True">
            <Setter Property="BackgroundColor" Value="Yellow" />
        </Trigger>
    </Entry.Triggers>
</Entry>
```

As partes importantes da declaração do gatilho são:

* **TargetType** – o tipo de controle ao qual o gatilho se aplica.

* **Propriedade** – a propriedade no controle que é monitorado.

* **Valor** – o valor, quando ele ocorre para a propriedade monitorada, que faz o gatilho ativar.

* **Setter** – uma coleção de elementos `Setter` pode ser adicionada quando a condição do gatilho for atendida. Você deve especificar `Property` e `Value` para definir.

* **EnterActions e ExitActions** (não mostrado) – são escritos em código e podem ser usados em (ou em vez de) elementos `Setter`. Eles são [descritos abaixo](#enterexit).

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
                </Trigger>
            </Style.Triggers>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>
```

<a name="data" />

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
        </DataTrigger>
    </Button.Triggers>
</Button>
```

Dica: ao avaliar `Path=Text.Length`, sempre forneça um valor padrão para a propriedade de destino (por exemplo, `Text=""`), pois, caso contrário, ele será `null` e o gatilho não funcionará como esperado.

Além de especificar `Setter`s, você também pode fornecer [`EnterActions` e `ExitActions`](#enterexit).

<a name="event" />

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

* Implementar a classe `TriggerAction<T>` genérica com o parâmetro genérico correspondente com o tipo de controle ao qual o gatilho será aplicado. É possível usar superclasses, como `VisualElement`, para gravar ações de gatilho que funcionam com uma variedade de controles ou especificar um tipo de controle, como `Entry`.

* Substituir o método `Invoke` – isso é chamado sempre que os critérios do gatilho forem atendidos.

* Outra opção é expor propriedades que podem ser definidas no XAML quando o gatilho é declarado (como `Anchor`, `Scale` e `Length` neste exemplo).

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

As propriedades expostas pela ação de gatilho podem ser definidas na declaração de XAML da seguinte maneira:

```xaml
<EventTrigger Event="TextChanged">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Tenha cuidado ao compartilhar gatilhos em um `ResourceDictionary`, pois uma instância será compartilhada entre os controles para que qualquer estado que está configurado uma vez se aplique a todos.

Observe que os gatilhos de evento não dão suporte a `EnterActions` nem a `ExitActions` [descrito abaixo](#enterexit).

<a name="multi" />

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

* O botão tem `IsEnabled="false"` definido por padrão.
* As condições de gatilho múltiplo usam o conversor para transformar o valor `Text.Length` em um `boolean`.
* Quando todas as condições forem `true`, o setter transformará o `IsEnabled` do botão na propriedade `true`.

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

![](triggers-images/multi-requireall.png "Exemplos de MultiTrigger")

<a name="enterexit" />

## <a name="enteractions-and-exitactions"></a>EnterActions e ExitActions

Outra maneira de implementar alterações quando ocorre um gatilho é adicionando as coleções `EnterActions` e `ExitActions` e especificando as implementações `TriggerAction<T>`.

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
    public FadeTriggerAction() {}

    public int StartsFrom { set; get; }

    protected override void Invoke (VisualElement visual)
    {
            visual.Animate("", new Animation( (d)=>{
                var val = StartsFrom==1 ? d : 1-d;
                visual.BackgroundColor = Color.FromRgb(1, val, 1);

            }),
            length:1000, // milliseconds
            easing: Easing.Linear);
    }
}
```

Observação: `EnterActions` e `ExitActions` são ignorados em **Gatilhos de evento**.



## <a name="related-links"></a>Links relacionados

- [Exemplo de gatilhos](https://developer.xamarin.com/samples/WorkingWithTriggers)
- [Documentação da API do Xamarin.Forms](xref:Xamarin.Forms.TriggerAction`1)
