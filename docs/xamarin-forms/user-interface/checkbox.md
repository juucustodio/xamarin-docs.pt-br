---
Título: " Xamarin.Forms caixa de seleção" Descrição: "a Xamarin.Forms caixa de seleção é um tipo de botão que pode ser marcado ou vazio. Quando uma caixa de seleção é marcada, ela é considerada como ativada. Quando uma caixa de seleção está vazia, ela é considerada como off. "
MS. Prod: xamarin MS. AssetID: B8B9268B-BCB8-42B9-B08C-C0F22C137238 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 06/11/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-checkbox"></a>Xamarin.FormsVerificação

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)

O Xamarin.Forms `CheckBox` é um tipo de botão que pode ser marcado ou vazio. Quando uma caixa de seleção é marcada, ela é considerada como ativada. Quando uma caixa de seleção está vazia, ela é considerada como off.

`CheckBox`define uma `bool` propriedade chamada `IsChecked` , que indica se o `CheckBox` está marcado. Essa propriedade também é apoiada por um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, o que significa que ele pode ser estilizado e ser o destino de associações de dados.

> [!NOTE]
> A `IsChecked` propriedade vinculável tem um modo de associação padrão de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) .

`CheckBox`define um `CheckedChanged` evento que é disparado quando a `IsChecked` propriedade é alterada, seja por meio da manipulação do usuário ou quando um aplicativo define a `IsChecked` propriedade. O `CheckedChangedEventArgs` objeto que acompanha o `CheckedChanged` evento tem uma única propriedade chamada `Value` , do tipo `bool` . Quando o evento é acionado, o valor da `Value` propriedade é definido como o novo valor da `IsChecked` propriedade.

## <a name="create-a-checkbox"></a>Criar uma caixa de seleção

O exemplo a seguir mostra como criar uma instância de `CheckBox` em XAML:

```xaml
<CheckBox />
```

Esse XAML resulta na aparência mostrada nas seguintes capturas de tela:

![Captura de tela de uma caixa de seleção vazia no iOS e no Android](checkbox-images/checkbox-empty.png "Caixa de seleção vazia")

Por padrão, o `CheckBox` está vazio. O `CheckBox` pode ser verificado pela manipulação do usuário ou definindo a `IsChecked` propriedade como `true` :

```xaml
<CheckBox IsChecked="true" />
```

Esse XAML resulta na aparência mostrada nas seguintes capturas de tela:

![Captura de tela de uma caixa de seleção marcada no iOS e no Android](checkbox-images/checkbox-checked.png "Caixa de seleção marcada")

Como alternativa, um `CheckBox` pode ser criado no código:

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>Responder a um estado de alteração de caixa de seleção

Quando a `IsChecked` propriedade é alterada, seja por meio da manipulação do usuário ou quando um aplicativo define a `IsChecked` propriedade, o `CheckedChanged` evento é acionado. Um manipulador de eventos para esse evento pode ser registrado para responder à alteração:

```xaml
<CheckBox CheckedChanged="OnCheckBoxCheckedChanged" />
```

O arquivo code-behind contém o manipulador para o `CheckedChanged` evento:

```csharp
void OnCheckBoxCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation after examining e.Value
}
```

O `sender` argumento é o `CheckBox` responsável por esse evento. Você pode usar isso para acessar o `CheckBox` objeto ou para distinguir entre vários `CheckBox` objetos que compartilham o mesmo `CheckedChanged` manipulador de eventos.

Como alternativa, um manipulador de eventos para o `CheckedChanged` evento pode ser registrado no código:

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>Associação de dados a uma caixa de seleção

O `CheckedChanged` manipulador de eventos pode ser eliminado usando a vinculação de dados e os gatilhos para responder a um `CheckBox` sendo marcado ou vazio:

```xaml
<CheckBox x:Name="checkBox" />
<Label Text="Lorem ipsum dolor sit amet, elit rutrum, enim hendrerit augue vitae praesent sed non, lorem aenean quis praesent pede.">
    <Label.Triggers>
        <DataTrigger TargetType="Label"
                     Binding="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                     Value="true">
            <Setter Property="FontAttributes"
                    Value="Italic, Bold" />
            <Setter Property="FontSize"
                    Value="Large" />
        </DataTrigger>
    </Label.Triggers>
</Label>
```

Neste exemplo, o [`Label`](xref:Xamarin.Forms.Label) usa uma expressão de associação em um gatilho de dados para monitorar a `IsChecked` Propriedade do `CheckBox` . Quando essa propriedade se torna `true` , `FontAttributes` as `FontSize` Propriedades e da `Label` alteração. Quando a `IsChecked` propriedade retorna para `false` , as `FontAttributes` `FontSize` Propriedades e de `Label` são redefinidas para o estado inicial.

Nas capturas de tela a seguir, a captura de tela do iOS mostra a [`Label`](xref:Xamarin.Forms.Label) formatação quando o `CheckBox` está vazio, enquanto a captura de tela do Android mostra a `Label` formatação quando o `CheckBox` está marcado:

[![Captura de tela de uma caixa de seleção de associação de dados, no iOS e no Android](checkbox-images/checkbox-databinding.png "Caixa de seleção Associação de dados")](checkbox-images/checkbox-databinding-large.png#lightbox "Caixa de seleção Associação de dados")

Para obter mais informações sobre gatilhos, consulte [ Xamarin.Forms gatilhos](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-checkbox"></a>Desabilitar uma caixa de seleção

Às vezes, um aplicativo entra em um estado em que uma está `CheckBox` sendo verificada não é uma operação válida. Nesses casos, o `CheckBox` pode ser desabilitado definindo sua `IsEnabled` propriedade como `false` .

## <a name="checkbox-appearance"></a>Aparência da CheckBox

Além das propriedades que `CheckBox` herdam da [`View`](xref:Xamarin.Forms.View) classe, `CheckBox` também definem uma `Color` propriedade que define sua cor como [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<CheckBox Color="Red" />
```

As capturas de tela a seguir mostram uma série de objetos verificados `CheckBox` , onde cada objeto tem sua `Color` propriedade definida como um diferente [`Color`](xref:Xamarin.Forms.Color) :

![Captura de tela de caixas de seleção coloridas em iOS e Android](checkbox-images/checkbox-colors.png "Caixa de seleção colorida")

## <a name="checkbox-visual-states"></a>Estados visuais da caixa de seleção

`CheckBox`tem um `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) que pode ser usado para iniciar uma alteração visual no `CheckBox` quando ele se torna marcado.

O exemplo de XAML a seguir mostra como definir um estado visual para o `IsChecked` estado:

```xaml
<CheckBox ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Color"
                            Value="Red" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="IsChecked">
                <VisualState.Setters>
                    <Setter Property="Color"
                            Value="Green" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</CheckBox>
```

Neste exemplo, o `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) especifica que quando o `CheckBox` estiver marcado, sua `Color` propriedade será definida como verde. O `Normal` `VisualState` especifica que quando o `CheckBox` estiver em um estado normal, sua `Color` propriedade será definida como vermelho. Portanto, o efeito geral é que o `CheckBox` é vermelho quando está vazio e verde quando é marcado.

Para obter mais informações sobre os Estados visuais, consulte [ Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Links relacionados

- [Demonstrações da caixa de seleção (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)
- [Xamarin.FormsGatilhos](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.FormsGerenciador de estado visual](~/xamarin-forms/user-interface/visual-state-manager.md)
